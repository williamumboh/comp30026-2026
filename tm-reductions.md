(sec-tm-reductions)=

# Reductions

In this lecture, we will learn about reductions, a powerful idea in
computer science.

::: {note}

While languages, Turing machines, recognizers, and deciders, may seem
too abstract and removed from the rest of computer science, recall that
languages model Yes/No computational problems, Turing machines model
algorithms, recognizers corresponds to algorithms that may not terminate
and deciders correspond to algorithms that always terminates.

In this lecture, we will use these terms interchangeably to reinforce
the fact that these are real-world problems and algorithms.

For the sake of concreteness, we will also provide Python
implementations of reductions. We interpret Turing machines $M$ as
Python functions `M`. The function call `M(w)` returns `True` if $M$
accepts $w$, `False` if $M$ rejects $w$, and never returns if $M$ loops
on $w$. We interpret a decider $M$ for a language $L$ as a Python
function `M` such that the function call `M(w)` on a string $w$ returns
`True` if $w \in L$ and `False` if $w \notin L$.

:::

::: {prf:definition} Reduction

A language $A$ *reduces to* a language $B$ (also written as $A \leq B$)
if the existence of a decider for $B$ implies that $A$ is decidable. A
*reduction* is a decider $M_A$ for $A$ that can make calls to a decider
$M_B$ for $B$.

:::

Informally, a computational problem $A$ reduces to a computational
problem $B$ means that the existence of an algorithm for $B$ implies the
existence of an algorithm for $A$. Typically, the reduction itself is
yet another algorithm that decides $A$ using a subroutine that solves
$B$. The reduction itself does not show that $A$ is decidable, rather,
it shows that **if** there is a decider $M$ for $B$, then there is a way
to use $M$ to decide $A$. Thus, the reduction shows that $A$ is
computationally no harder than $B$, and this is why we use the notation
$A \leq B$.

In Python, a reduction from $A$ to $B$ is a function `M_A` that is able
to make calls to a function `M_B` that decides $B$.

::: {attention}

It is important that the reduction treats the decider $M_B$ for $B$ as a
**black box**. In other words, the reduction can only make calls to
$M_B$ by running $M_B$ on some string $w$, and see whether $M$ accepts
or rejects $w$. The reduction cannot assume that $M_B$ has any property
beyond that it decides $B$.

:::

::: {prf:example} $L^c \leq L$

The proof that decidable languages are closed under complement actually
shows that $L^c \leq L$ for every language $L$, i.e., if $L$ is
decidable, then so is its complement. Here is its Python implementation,
where `M` is a function that decides $L$.

``` python
def M_complement(w):
    return not M(w)
```

:::

Reductions have two purposes. First, much like [closure
properties](#sec-tm-closure), reductions let us create new algorithms by
using other algorithms as a subroutine. For example, we obtained a
decider for NFA Acceptance by reducing it to DFA Acceptance, and a
decider for DFA Equivalence by reducing it to DFA Emptiness.

Second, reductions let us to prove other problems are undecidable. The
statement "$B$ is decidable implies that $A$ is decidable" is logically
equivalent to the statement "$A$ is undecidable implies that $B$ is
undecidable". We have seen such a reduction in the first proof of the
undecidability of $\ATM$ in @sec-tm-accept-reduction where we used a
decider for $\ATM$ to construct a decider for $L_D$, and used the
unrecognizability of $L_D$ to conclude that $\ATM$.

In summary, by showing that $A \leq B$, we get that:

1.  if $B$ is decidable, then $A$ is decidable (we say that $A$
    **reduces to** $B$)
2.  if $A$ is undecidable, then $B$ is undecidable (we say that $B$ is
    undecidable by **reducing from** $A$)

In this lecture, we will focus on the second.

## Halting Problem

The Halting Problem is defined as follows: given an encoding of a Turing
machine $M$ and a string $w$, decide if $M$ halts on $w$. The
corresponding language $\HALT$ consists of strings
$\langle M, w \rangle$ such that $M$ halts on $w$.

In Python terms, the Halting Problem is: given the source code of a
Python function `M` and a string `w`, decide if the function call `M(w)`
returns.[^1]

Recall that the TM Acceptance problem (aka $\ATM$) asks: given an
enconding of a Turing machine $M$ and a string $w$, does $M$ accept $w$?

In Python terms, the TM Acceptance problem asks: given the source code
of a Python function `M` and a string `w`, decide if the function call
`M(w)` returns `True`.

::: {prf:theorem}

$\ATM \leq \HALT$.

:::

::: {prf:proof enumerated=false}

To show that $\ATM \leq \HALT$, we need to construct a decider $S$ for
$\ATM$ given a decider $H$ for $\HALT$.

Here's the high level idea for $S$. Recall that we built a recognizer
for $\ATM$ by simulating $M$ on $w$. The only reason that this is not a
decider is that the simulation never halts if M does not halt on $w$.
Thus, we can use the decider $H$ for $\HALT$ to first check if $M$ halts
on $w$. If it does, then we can safely simulate $M$ on $w$; otherwise,
then we know that $M$ does not accept $w$.

Here is the pseudocode for the decider $S$ for $\ATM$, given a decider
$H$ for $\HALT$.

On input $\langle M,w \rangle$:

1.  Run $H$ on $\langle M,w \rangle$
2.  If $H$ accepts,
    - Run $M$ on $w$
    - Accept if $M$ accepts
    - Reject if $M$ rejects
3.  Else: reject

In Python:

``` python
def S(M,w):
    if H(M,w):
        return M(w)           #in this case, we know M(w) will return so we can safely make the function call
    else:
        return False          #in this case, M(w) does not return so it does not return True
```

We now justify that $S$ decides $\ATM$. First, we argue that $S$ is a
decider. Since $H$ is a decider for $\HALT$, the first step always
terminates. Moreover, we only run $M$ on $w$ if $H$ accepts
$\langle M, w\rangle$, i.e. if $M$ halts on $w$. Thus, the simulation of
$M$ on $w$ always halts as well. Therefore, $S$ is indeed a decider.

Next, we argue that $S$ is correct. Observe that if $M$ does not halt on
$w$, then $M$ does not accept $w$. In this case, $S$ correctly rejects.
If $M$ halts on $w$, $S$ accepts if and only if $M$ accepts $w$.
Therefore, $S$ is correct.

We conclude that $S$ is indeed a decider for $\ATM$ and so
$\ATM \leq \HALT$.

:::

Since $\ATM$ is undecidable, $\ATM \leq \HALT$ implies that $\HALT$ is
undecidable.

::: {prf:theorem}

$\HALT$ is undecidable.

:::

## TM Emptiness

The TM Emptiness Problem is defined as follows: given an encoding of a
Turing machine $M$, decide if $L(M) = \emptyset$, i.e. if $M$ does not
accept any string. The corresponding language $\EMPTYTM$ consists of
strings $\langle M \rangle$ such that $M$ does not accept any string.

In Python terms, given a function `M`, decide if for every input `w`,
the function call `M(w)` either returns `False` or never returns.

::: {note} Application

Why do we care about TM Emptiness? Suppose we want to know if an LLM `M`
can never be prompted into outputting a biological weapon. We can define
a new function `T` that on input `w`, feeds it to `M`, and if the output
`M(w)` is a biological weapon, returns `True`. If `T(w)` does not return
`True` for every string `w`, then we know that there is no prompt that
will cause `M` to output a biological weapon.

:::

::: {prf:theorem}

$\ATM \leq \EMPTYTM$.

:::

::: {prf:proof enumerated=false}

To show that $\ATM \leq \EMPTYTM$, we need to construct a decider for
$\ATM$ given a decider for $\EMPTYTM$.

The reduction is trickier than the one for $\HALT$ and involves
constructing a TM that we then run the decider for $\EMPTYTM$ on. In
particular, we will create a new TM $T$ that accepts all strings if $M$
accepts $w$, and accepts no string otherwise. Thus, $L(T) = \emptyset$
if and only if $M$ accepts $w$.

Let $H$ be a decider for $\EMPTYTM$. Consider the following TM $S$ for
$\ATM$.

On input $\langle M,w \rangle$:

1.  Construct TM $T$ that behaves as follows:
    - On input $x$:
      - Run $M$ on $w$
      - Accept if $M$ accepts
      - Reject if $M$ rejects
      - Run forever if $M$ runs forever
2.  Run $H$ on $\langle T \rangle$
3.  Accept if $H$ rejects
4.  Reject if $H$ accepts

In Python:

``` python
def S(M,w):
    def T(x):
        return M(w)
    return not H(T)
```

We now justify that $S$ decides $\ATM$. First, observe that $T$ does the
same thing on every input and thus, $T$ accepts every string if $M$
accepts $w$ and $T$ accepts no string otherwise. Therefore,
$L(T) = \emptyset$ if and only if $M$ does not accept $w$. So, $S$ is
correct for $\ATM$. The fact that it is a decider follows easily from
the fact that $H$ is a decider and thus running $H$ on
$\langle T \rangle$ always halts.

We conclude that $S$ is indeed a decider for $\ATM$ and so
$\ATM \leq \EMPTYTM$.

:::

Since $\ATM$ is undecidable, $\ATM \leq \EMPTYTM$ implies that
$\EMPTYTM$ is undecidable.

::: {prf:theorem}

$\EMPTYTM$ is undecidable.

:::

## TM Equivalence

The TM Equivalence Problem is defined as follows: given an encoding of
two Turing machines $M_1$ and $M_2$, decide if $L(M_1) = L(M_2)$, i.e.
for every string $w$, $M_1$ accepts $w$ if and only if $M_2$ accepts
$w$. The corresponding language $\EQTM$ consists of
$\langle M_1, M_2 \rangle$ such that $L(M_1) = L(M_2)$.

In Python terms, given two functions `M_1` and `M_2`, decide whether for
every string `w`, we have that `M_1(w)` returns `True` if and only if
`M_2(w)` returns `True`.

::: {note} Application

Why do we care about TM Equivalence? Suppose you use an LLM to optimize
your code. Then, you want to verify that execution speed is the only
difference between the LLM output and your code is that the LLM output,
and that the LLM did not introduce bugs, security vulnerabities, or
worse, backdoors. For example, if you are writing code for a
cryptographic protocol, then a backdoor would be disastrous…

:::

::: {prf:theorem}

$\EMPTYTM \leq \EQTM$.

:::

::: {prf:proof enumerated=false}

To show that $\EMPTYTM \leq \EQTM$, we need to construct a decider for
$\EMPTYTM$ given a decider for $\EQTM$.

The idea here is pretty simple: we construct a TM $N$ that rejects every
string, and so $M$ is equivalent to $N$ if and only if
$L(M) = \emptyset$.

Let $H$ be a decider for $\EQTM$. Consider the following TM $S$ for
$\EMPTYTM$.

On input $\langle M \rangle$:

1.  Construct TM $N$ that rejects every string
2.  Run $H$ on $\langle M, N\rangle$
3.  Accept if $H$ accepts
4.  Reject if $H$ rejects

In Python:

``` python
def S(M):
    def N(x):
        return False
    return H(M,N)
```

We now argue that $S$ decides $\EMPTYTM$. Since $N$ rejects every
string, we get that $L(N) = \emptyset$ and so $S$ accepts
$\langle M \rangle$ if and only if $L(M) = L(N) = \emptyset$. So, $S$ is
correct for $\EMPTYTM$. The fact that it is a decider follows easily
from the fact that $H$ is a decider and thus running $H$ on
$\langle M, N \rangle$ always halts.

We conclude that $S$ is indeed a decider for $\EMPTYTM$ and so
$\EMPTYTM \leq \EQTM$.

:::

Since $\EMPTYTM$ is undecidable, $\EMPTYTM \leq \EQTM$ implies that
$\EQTM$ is undecidable.

::: {prf:theorem}

$\EQTM$ is undecidable.

:::

## The Power of Halting Problem

Another way to see why we cannot have a decider for the Halting problem
is that it is extremely powerful: we can use it to turn recognizers into
deciders, using the same construction as for $\ATM \leq \HALT$.

::: {prf:theorem}

Suppose there is a decider $H$ for $\HALT$. Then for every language $L$,
the existence of a recognizer $M$ implies the existence of a decider
$S$.

:::

::: {prf:proof}

Consider the following TM $S$ for $L$.

On input $w$:

1.  Run $H$ on $\langle M,w \rangle$
2.  If $H$ accepts:
    - Run $M$ on $w$
    - Accept if $M$ accepts
    - Reject if $M$ rejects
3.  Else: reject

In Python:

``` python
def S(w):
    if H(M,w):
        return M(w)           #in this case, we know M(w) will return so we can safely make the function call
    else:
        return False          #in this case, M(w) does not return so it does not return True
```

We now justify that $S$ decides $L$. First, we argue that $S$ is a
decider. Since $H$ is a decider for $\HALT$, the first step always
terminates. Moreover, we only run $M$ on $w$ if $H$ accepts
$\langle M, w\rangle$, i.e. if $M$ halts on $w$. Thus, the simulation of
$M$ on $w$ always halts as well. Therefore, $S$ is indeed a decider.

Next, we argue that $S$ is correct: it accepts $w$ if $w\in L$ and
rejects otherwise. Suppose $w \in L$. Since $M$ recognizes $L$, we get
that $M$ halts and accepts $w$. Thus, since $H$ decides $\HALT$, $H$
accepts $\langle M, w \rangle$, and the simulation will eventually
accept, and so does $S$.

On the other hand, if $w \notin L$, then either $M$ rejects $w$ or $M$
does not halt on $w$. If $M$ rejects $w$, then $H$ accepts
$\langle M, w \rangle$, and the simulation eventually rejects, and so
does $S$. Otherwise, $H$ rejects $\langle M, w \rangle$ and $S$ rejects
as well.

We conclude that $S$ is indeed a decider for $L$.

:::

[^1]: Recall that $M$ does not halt on $w$ corresponds to the function
    call `M(w)` never returning.
