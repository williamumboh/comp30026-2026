(sec-tm-closure)=

# Closure Properties

A class of languages is **closed under** an operation if applying the
operation to languages in the class always gives a language in the
class. Why do we care about closure properties? Because they let us
build new machines from machines we already have. In programming terms,
it is like reusing functions.

In this lecture we do the same with Turing machines: given machines for
some languages, we reuse them as subroutines in a machine for a new
language. When a class is closed under an operation, this always works,
since the new language is in the class whenever the given ones are. But
not every class is closed under every operation.

::: {note}

For the sake of making these concepts more relatable and concrete, we
will also write Turing machines as Python functions, and also refer to
them as algorithms. A machine $M$ becomes a function `M()` where the
function call `M(w)` returns `True` if $M$ accepts $w$, `False` if $M$
rejects $w$, and never returns if $M$ loops on $w$. This is
mathematically valid as the Church-Turing Thesis implies that Python
programs are equivalent to Turing machines.[^1]

:::

## Decidable Languages

Closure properties are relatively straightforward for decidable
languages since deciders always halt.

::: {note}

A decider $M$ for $L$ always halts, so the call `M(w)` always returns:
`True` if $w \in L$ and `False` if $w \notin L$.

:::

### Union and Intersection

::: {prf:theorem}

If $L_1$ and $L_2$ are decidable, then $L_1 \cup L_2$ and $L_1 \cap L_2$
are decidable.

:::

::: {prf:proof enumerated=false}

Let $M_1$ and $M_2$ be deciders for $L_1$ and $L_2$, respectively.

The high level descriptions are:

- (Union) Given $w$, run $M_1$ on $w$ then $M_2$ on $w$. If at least one
  accepts, accept; otherwise, reject.
- (Intersection) Given $w$, run $M_1$ on $w$ then $M_2$ on $w$. If both
  accepts, accept; otherwise, reject.

``` python
def M_union(w):
    return M_1(w) or M_2(w)

def M_intersection(w):
    return M_1(w) and M_2(w)
```

We now justify that these decide the union and intersection. Every call
to $M_1$ and $M_2$ returns, so both functions always return, and thus
are deciders. The correctness of the functions follow from the
definition of union and intersection. `M_union` accepts $w$ if and only
if $w \in L_1$ or $w \in L_2$, that is, $w \in L_1 \cup L_2$. Similarly,
`M_intersection` accepts $w$ if and only if $w \in L_1$ and $w \in L_2$,
that is, $w \in L_1 \cap L_2$.

:::

### Concatenation

::: {tip}

As you will see below, you can get a lot of mileage simply by being
clear about the problem definition and thinking about how to
algorithmically check the definition. In particular, pay attention to
how we break down the definition of the closure operation and turn it
into an algorithm.

:::

::: {prf:theorem}

If $L_1$ and $L_2$ are decidable, then $L_1 \circ L_2$ is decidable.

:::

::: {prf:proof enumerated=false label=prf-concat-decidable}

Let $M_1$ and $M_2$ be deciders for $L_1$ and $L_2$, respectively.

Recall that $w \in L_1 \circ L_2$ if and only if it can be split into a
prefix $x$ in $L_1$ and a suffix $y$ in $L_2$, i.e., $w = xy$ for some
$x \in L_1$ and $y \in L_2$. For example, the string $0110$ has 5
splits:

- $x = \epsilon$ and $y = 0110$
- $x = 0$ and $y = 110$
- $x = 01$ and $y = 10$
- $x = 011$ and $y = 0$
- $x = 0110$ and $y = \epsilon$

Equivalently, in Python, we say that $w \in L_1 \circ L_2$ if and only
if there exists an integer $i$ in `range(len(w))+1` such that `w[:i]` is
in $L_1$ and `w[i:]` is in $L_2$.[^2]

The above discussion about the definition of $L_1 \circ L_2$ suggests
the following algorithm: Try all splits $w = xy$; for each split, run
$M_1$ on $x$ and $M_2$ on $y$, and accept if both accepts; reject after
all splits have been tried.

``` python
def M_concatenation(w):
    for i in range(len(w)+1):  # try each split w = xy
        x, y = w[:i], w[i:]
        if M_1(x) and M_2(y):
            return True
    return False
```

We now justify that this decides the concatenation. There are $|w| + 1$
splits and every call returns, so `M_concatenation` always returns. It
accepts $w$ if and only if some split $w=xy$ has $x \in L_1$ and
$y \in L_2$, that is, $w \in L_1 \circ L_2$.

:::

### Kleene star

::: {prf:theorem}

If $L$ is decidable, then $L^*$ is decidable.

:::

::: {prf:proof}

Let $M$ be a decider for $L$.

Recall that $w \in L^*$ if and only if there exists $k \ge 0$ such that
$w = x_1 x_2 \cdots x_k$ with each $x_i \in L$. Thus, there are two
possibilities for $w$ to be in $L^*$:

- ($k = 0$) $w = \epsilon$;
- ($1 \leq k \leq |w|$) $w$ can be split into $k$ nonempty substrings
  $x_1, x_2, \ldots, x_k$ where each part $x_i \in L$.

This suggests the following algorithm:

1.  if $w = \epsilon$, accept
2.  for $1 \leq k \leq |w|$,
    - for every split of $w$ into $k$ nonempty substrings
      $x_1,\ldots,x_k$:
      - accept if $M$ accepts each of $x_1, \ldots, x_k$
3.  reject

We assume we are given a function `splits(w,k)` that returns an array of
all splits of $w$ into $k$ nonempty substrings.

::::: {note class=dropdown dropdown=true} (Optional) `splits(w,k)`

For those who are curious, here is one implementation of `splits(w,k)`

``` python
from itertools import combinations

def splits(w, k):
    # all splits of w into k non-empty pieces: cut k - 1 of the |w| - 1 gaps
    n = len(w)
    result = []
    for cuts in combinations(range(1, n), k - 1):
        starts = (0, *cuts)
        ends = (*cuts, n)
        result.append([w[i:j] for i, j in zip(starts, ends)])
    return result
```

:::::

We try all splits by brute force:

``` python
def M_star(w):
    if w == "":
        return True
    for k in range(1, len(w) + 1):
        for pieces in splits(w, k):  # try each split w = x_1 x_2 ... x_k
            if all(M(x) for x in pieces):
                return True
    return False
```

We now justify that this decides the Kleene star. There are finitely
many splits (see the note below) and every call returns, so `M_star`
always returns. It accepts $w$ if and only if $w = \varepsilon$ or some
split of $w$ into pieces has every piece in $L$, that is, $w \in L^*$.

::::: {note}

How many splits does `M_star` try for a non-empty $w$? Counting in two
ways, with $n = |w|$, both give $2^{n-1}$:

- Each of the $n - 1$ gaps between consecutive symbols of $w$ is either
  cut or not, and each choice gives a different split, so there are
  $2^{n-1}$ splits.

- For each $k$, there are $\binom{n-1}{k-1}$ splits into $k$ pieces, one
  for each choice of $k - 1$ gaps to cut. Summing over $k$ and using the
  [binomial
  theorem](https://en.wikipedia.org/wiki/Binomial_coefficient#Sums_of_the_binomial_coefficients),

  $$   \sum_{k=1}^{n} \binom{n-1}{k-1} = \sum_{j=0}^{n-1} \binom{n-1}{j} = 2^{n-1}.
     $$

:::::

### Complement

::: {prf:theorem}

If $L$ is decidable, then $L^c$ is decidable.

:::

::: {prf:proof enumerated=false}

Let $M$ be a decider for $L$. Recall that $L^c$ is the set of all
strings that are not in $L$. We run $M$ on $w$ and flip its answer:

``` python
def M_complement(w):
    return not M(w)
```

We now justify that this decides the complement. The call `M(w)`
returns, so `M_complement` always returns. It accepts $w$ if and only if
$M$ rejects $w$, that is, $w \notin L$, or equivalently $w \in L^c$.

:::

## Recognizable Languages

A recognizer for $L$ accepts every $w \in L$, but on $w \notin L$ it may
reject or loop. As a Python function, a recognizer `M(w)` returns `True`
if $w \in L$, but if $w \notin L$, it returns `False` or never returns.
This is the main difference from a decider, whose calls always
return.[^3]

### Union and Intersection

::: {prf:theorem}

If $L_1$ and $L_2$ are recognizable, then $L_1 \cap L_2$ is
recognizable.

:::

::::: {prf:proof}

Let $M_1$ and $M_2$ be recognizers for $L_1$ and $L_2$, respectively.

::: {tip} Pattern matching

Another useful strategy for designing algorithms is to first try reusing
algorithms or approaches that work for similar problems. Sometimes it
works, as in the case of intersection, and sometimes it does not, as in
the case of union below, but it gives a good starting point, regardless.

:::

The code from the decidable case still works:

``` python
def M_intersection(w):
    return M_1(w) and M_2(w)
```

We now justify that this recognizes the intersection. `M_intersection`
accepts $w$ if and only if both calls return `True`, that is,
$w \in L_1 \cap L_2$. If $w \notin L_1 \cap L_2$, then there are two
cases:

1.  $M_1$ and $M_2$ reject $w$, in which case `M_intersection` also
    rejects,
2.  at least one of $M_1$ and $M_2$ does not halt, in which case
    `M_intersection` does not halt, but that is allowed: a recognizer
    may loop on strings not in the language.

:::::

For union, the same trick fails. With `M_1(w) or M_2(w)`, if $M_1$ loops
on $w$, then `M_2(w)` is never called, even if $w \in L_2$.

Since $M_1$ or $M_2$ may not halt on $w$, we cannot simply run them on
$w$. Instead, we will simulate them in parallel, one step at a time.
This is analogous to stepping through their source code, one line at a
time.[^4]

::: {prf:theorem}

If $L_1$ and $L_2$ are recognizable, then $L_1 \cup L_2$ is
recognizable.

:::

::: {prf:proof enumerated=false}

Let $M_1$ and $M_2$ be recognizers for $L_1$ and $L_2$, respectively.

We run them **in parallel**, taking turns:

1.  let $C_1$ and $C_2$ be the start configuration of $M_1$ and $M_2$ on
    $w$, respectively
2.  repeat the following:
    - if either $C_1$ or $C_2$ is an accept configuration, accept
    - else if both are reject configurations, reject
    - else, let $C_1$ and $C_2$ be the next configuration of $M_1$ and
      $M_2$, respectively

Recall that a **configuration** of a TM is a snapshot of its
computation: the current state, the tape contents and the head position.
We assume the following helper functions:

- `start_1(w)` and `start_2(w)` returns the start configuration of $M_1$
  and $M_2$ on $w$, respectively;
- `step_1(C)` and `step_2(C)` returns the configuration that
  configuration $C$ yields in one step of $M_1$ and $M_2$, respectively;
  if $C$ is either accept or reject, then `step_1(C)` and `step_2(C)`
  returns $C$
- `accepted(C)`, `rejected(C)` tell whether configuration $C$ is in the
  accept state, the reject state.

``` python
def M_union(w):
    C_1, C_2 = start_1(w), start_2(w)
    while True:
        if accepted(C_1) or accepted(C_2):
            return True
        elif rejected(C_1) or rejected(C_2):
            return False
        else:   #Compute next configuration
            C_1, C_2 = step_1(w), step_2(w)
```

We now justify that this recognizes the union. If $M_1$ or $M_2$ accepts
$w$, say after $t$ steps, then `M_union` returns `True` within $t$
rounds of the loop, since each round simulates every machine that has
not halted for one step. If neither accepts $w$, then `M_union` never
returns `True`: it returns `False` if both reject $w$, and loops if at
least one loops. So `M_union` accepts $w$ if and only if $M_1$ or $M_2$
accepts $w$, that is, $w \in L_1 \cup L_2$.

:::

### Concatenation

::: {prf:theorem}

If $L_1$ and $L_2$ are recognizable, then $L_1 \circ L_2$ is
recognizable.

:::

::: {prf:proof}

Let $M_1$ and $M_2$ be recognizers for $L_1$ and $L_2$, respectively.

Suppose we try to use the [algorithm](#prf-concat-decidable) as for the
case that $M_1$ and $M_2$ are deciders. It tries the splits $w = xy$ one
at a time, and then runs $M_1$ on $w$ and $M_2$ on $w$. Thus, if $M_1$
loops on the $x$ of an early split, the later splits are never tried.
Instead, we step through all splits in parallel. Each split $w = xy$
gives two **runs**, $M_1$ on $x$ and $M_2$ on $y$, so there are
$2(|w| + 1)$ runs. In each round, every run that has not halted takes
one step.

We assume the following helper functions:

- `start_1(w)` and `start_2(w)` returns the start configuration of $M_1$
  and $M_2$ on $w$, respectively;
- `step_1(C)` and `step_2(C)` returns the configuration that
  configuration $C$ yields in one step of $M_1$ and $M_2$, respectively;
  if $C$ is either accept or reject, then `step_1(C)` and `step_2(C)`
  returns $C$
- `accepted(C)`, `rejected(C)` tell whether configuration $C$ is in the
  accept state, the reject state.

``` python
def split_accepted(C_x, C_y):
    # M_1 has accepted x and M_2 has accepted y
    return accepted(C_x) and accepted(C_y)

def split_rejected(C_x, C_y):
    # M_1 has rejected x or M_2 has rejected y: this split can never be accepted
    return rejected(C_x) or rejected(C_y)

def M_concatenation(w):
    # one pair of runs per split w = xy: M_1 on x = w[:i] and M_2 on y = w[i:]
    runs = [(start_1(w[:i]), start_2(w[i:])) for i in range(len(w) + 1)]
    while True:
        # one round: every run that has not halted takes one step
        runs = [(step_1(C_x), step_2(C_y)) for C_x, C_y in runs]
        if any(split_accepted(C_x, C_y) for C_x, C_y in runs):
            return True
        if all(split_rejected(C_x, C_y) for C_x, C_y in runs):
            return False
```

We now justify that this recognizes the concatenation. There are
finitely many runs, so each round finishes. If $w \in L_1 \circ L_2$,
some split $w = xy$ has $M_1$ accepting $x$ and $M_2$ accepting $y$;
once both runs have accepted, `M_concatenation` returns `True`. If
$w \notin L_1 \circ L_2$, no split has both runs accepting, so
`M_concatenation` never returns `True`. So `M_concatenation` accepts $w$
if and only if $w \in L_1 \circ L_2$.

:::

### Complement

Can we reuse the decidable proof for complement? Let $M$ be a recognizer
for $L$ and suppose we try the same code:

``` python
def M_complement(w, M):
    return not M(w)
```

If $M$ loops on some $w \notin L$, then `M(w)` never returns, so
`M_complement` never accepts $w$, even though $w \in L^c$. Running in
parallel does not help either: there is only one machine, so there is
nothing to take turns with. In fact, it is not possible to construct a
recognizer for $L^c$ in general.

::: {prf:theorem label=thm-re-complement}

Recognizable languages are not closed under complement.

:::

Recall that a class of languages are said to be closed under complement
if for **every** language $L$ in the class, its complement $L^c$ is also
in the class. Thus, to show @thm-re-complement, it suffices to show that
there is a recognizable language $L$ whose complement $L^c$ is not
recognizable. We use the TM Acceptance language $A_{\mathrm{TM}}$.

Recall that
$$A_{\mathrm{TM}} = \{\langle M, w \rangle \mid M \text{ is a TM and } M \text{ accepts } w\}.$$
In words, $A_{\mathrm{TM}}$ asks: given an encoding of a TM $M$ and a
string $w$, does $M$ accept $w$?

We have previously shown this language to be
[recognizable](#thm-tm-accept-recognizable) but
[undecidable](#sec-decidability). We now prove @thm-re-complement by
proving that its complement is not recognizable.

::: {prf:theorem label=thm-ATM-not-rec}

$(A_{\mathrm{TM}})^c$ is not recognizable.

:::

In words, $(A_{\mathrm{TM}})^c$ asks: given a TM $M$ and a string $w$,
does $M$ **not** accept $w$, i.e., does $M$ reject $w$ or loop on $w$?

To prove the theorem, we prove something more general.

::: {prf:theorem label=thm-decidable-recognizable}

$L$ is decidable if and only if both $L$ and $L^c$ are recognizable.

:::

First, let us see how @thm-decidable-recognizable implies
@thm-ATM-not-rec.

::: {prf:proof enumerated=false} @thm-ATM-not-rec.

Recall that "$P \Rightarrow Q$" is equivalent to its **contrapositive**
"not $Q \Rightarrow$ not $P$". The ($\Leftarrow$) direction of the
theorem says: if $L$ and $L^c$ are both recognizable, then $L$ is
decidable. Its contrapositive says: if $L$ is not decidable, then $L$ or
$L^c$ is not recognizable. Since $A_{\mathrm{TM}}$ is not decidable,
$A_{\mathrm{TM}}$ or $(A_{\mathrm{TM}})^c$ is not recognizable. But
$A_{\mathrm{TM}}$ is recognizable, so $(A_{\mathrm{TM}})^c$ is not
recognizable.

:::

Now we prove the theorem.

::: {prf:proof}

($\Rightarrow$) If $L$ is decidable, then $L^c$ is decidable too, and
every decider is a recognizer.

($\Leftarrow$) Let $M_1$ recognize $L$ and $M_2$ recognize $L^c$. We run
them **in parallel**, as for union, but now $M_2$ accepting means
reject:

1.  Let $C_1$ and $C_2$ be the start configurations of $M_1$ and $M_2$
    on $w$, respectively.
2.  Repeat the following:
    - if either $C_1$ is accept or $C_2$ is reject, accept
    - else if either $C_1$ is reject or $C_2$ is accept, reject
    - else, let $C_1$ and $C_2$ be the next configuration of $M_1$ and
      $M_2$

``` python
def M_decider(w):
    C_1, C_2 = start_1(w), start_2(w)
    while True:
        if accepted(C_1) or rejected(C_2):
            return True
        if accepted(C_2) or rejected(C_1):
            return False
        C_1, C_2 = step_1(C_1), step_2(C_2)
```

We now justify that this decides $L$. If $w \in L$, then $M_1$ accepts
$w$ after a finite number of steps, so `M_decider` returns `True` after
finitely many rounds. If $w \in L^c$, then $M_2$ accepts $w$ after a
finite number of steps, so `M_decider` returns `False`. Every $w$ is in
$L$ or in $L^c$, so `M_decider` always returns, and it accepts exactly
the strings of $L$: it is a decider for $L$.

:::

[^1]: On the exam, you can choose to use pseudocode.

[^2]: \~range(k)\~ outputs an array of integers starting from 0 up to,
    but not including, $k$; `w[:i]` is the first $i$ symbols of string
    $w$, and `w[i:]` is the suffix from the \$(i+1)\$th symbol onwards.

[^3]: A recognizer may not return your call and leave you hanging.

[^4]: See, for example, [Python Tutor](https://pythontutor.com)
