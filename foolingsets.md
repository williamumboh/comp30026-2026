(sec-nonreg-fooling)=

# Technique 1: Fooling Set

::: {seealso}

The lecture notes are based[^1] on the exposition in:

- Sections 1.1 and 1.2 of [A Note on Proving Non-Regularity via Fooling
  Sets](https://chekuri.cs.illinois.edu/teaching/fooling-sets.pdf) by
  Chandra Chekuri (UIUC). This is a beautifully written note that
  provides a lot of intuition behind the approach.[^2]
- [Stanford CS103
  slides](https://web.stanford.edu/class/archive/cs/cs103/cs103.1246/lectures/18/)
- Section 3.8 of [Lecture Notes on Finite-State
  Machines](http://jeffe.cs.illinois.edu/teaching/algorithms/models/03-automata.pdf)
  by Jeff Erickson (UIUC)

:::

At an intuitive level, to prove a language $L$ is not regular, we need
to exploit the fact that a finite automata has a fixed amount of memory,
independent of the length of the input string. The fooling set technique
allows us to prove lower bounds on the amount of memory that is needed
to recognize $L$.

In more detail, the technique lets us prove **lower bounds on the number
of states a DFA needs to recognize** $L$, i.e. it allows us to prove
statements of the form "no DFA with fewer than 5 states can recognize
$L$". Since a DFA has a fixed number of states, to show that no DFA can
recognize $L$, we will prove that **for every non-negative integer $k$,
no DFA with fewer than $k$ states can recognize $L$.**

(sec-nonreg-meaning)=

## What does it mean to prove nonregularity?

Recall that a language $L$ is regular if and only if there is a DFA $M$
that recognizes it.

Recall also that a DFA $M$ recognizes a language $L$ if and only if for
every input $x$:

- if $x$ is in $L$, then $M$ accepts $x$, and
- if $x$ is not in $L$, then $M$ rejects $x$.

Thus, we get the following definition of nonrecognition.

::: {prf:definition} Finite automata nonrecognition

We say that $M$ *does not recognize* $L$ iff there exists an input
string $x$ on which $M$ makes a mistake, i.e.

- $x$ is in $L$ and $M$ rejects $x$, or
- $x$ is not in $L$ and $M$ accepts $x$.

Such an input string $x$ is called a *counterexample* to $M$, as it
proves that $M$ does not recognize $L$.

:::

This then leads to the following definition of nonregularity.

::: {prf:definition} Nonregular languages

A language $L$ is *nonregular* iff there is a counterexample to every
DFA $M$, i.e. for every DFA $M$, there exists an input string $x$ on
which $M$ makes a mistake:

- $x$ is in $L$ and $M$ rejects $x$, or
- $x$ is not in $L$ and $M$ accepts $x$.

:::

## Extended Transition Function

To show that a DFA $M$ makes a mistake on a language, a key idea is to
consider the computation paths of $M$, i.e. the sequence of states that
$M$ transitions through as it processes an input string, and the final
state of $M$ after processing the entire string. To this end, we need
the concept of the extended transition function.

Let $M$ be a DFA with state transition function $\delta$ and initial
state $q_0$. Recall the definition of the state transition function
$\delta$: given current state $q$ and after processing the next input
symbol $a$, the DFA $M$ moves to state $\delta(q,a)$.

We now define the extended transition function $\delta^*$ which tells us
the resulting state after processing a given string starting from a
given state. See the following for a formal definition.

::: {prf:definition} Extended transition function $\delta^*$

Let $M$ be a deterministic finite automaton with

1.  Finite input alphabet $Σ$
2.  Set of states $Q$
3.  Initial state $q₀$
4.  State transition function $\delta : Q × \Sigma → Q$
5.  Subset of accepting states $F$

The *extended transition function*
$\delta^* : Q \times \Sigma^* \rightarrow Q$ is defined recursively as
follows[^3]:
$$δ^*(q,w) =
\begin{cases}
q  & \text{if $w = \epsilon$}\\
\delta(r,a) & \text{if $w = xa$ for some $a \in \Sigma$ and $r = \delta^*(q,x)$}
\end{cases}$$

:::

::: {note} Iterative definition of $\delta^*$

We remark that $\delta^*(q,w)$ can be viewed as an iterated application
of $\delta$. To see this, let $n$ be the length of $w$ and $w_i$ be the
$i$-th symbol of $w$. Unfolding the recurrence in the recursive
definition of $\delta^*$ gives
$$\delta^*(q,w) = \delta(\delta(\cdots \delta(\delta(q, w_1), w_2)\cdots), w_{n-1}), w_n)$$
For example, $\delta^*(q,01) = \delta(\delta(q,0),1)$.

:::

::: {note} Diagrammatic representation of $\delta^*$

As can be seen from the above note, the notation for iterated
application of $\delta^*$ can get unwieldy. It will be more convenient
to represent the expression $\delta^*(q,w) = r$ using the following
diagram:
$$q \xrightarrow{w} r$$

:::

Observe that $\delta^*(q_0,w)$ is the state that $M$ ends up in after
processing input $w$. We call this state the *resulting state* of $M$ on
$w$, and denote it by $q(w)$. By definition, $M$ accepts $w$ if and only
if $q(w)$ is an accepting state.

## Warmup: 1-State DFAs

Before we proceed with the fooling set technique, let's see how
analyzing resulting states is useful. In particular, we will prove the
following characterization of the languages recognized by DFAs with a
single state. The characterization allows us to rule out single-state
DFAs for some languages $L$: no DFA with 1 state can recongize $L$ if
$L$ is not the empty language or the language of all strings.

::: {prf:lemma label=lem-nonreg-single} Characterization of languages of
single-state DFA

A single-state DFA either accepts every string or rejects every string.
Consequently, if $L$ is neither the empty language nor the language of
all strings, then $L$ cannot be recognized by a single-state DFA.

:::

::: {prf:proof}

Let $M$ be a single-state DFA. Since $M$ has only one state, the
resulting state of every string is the initial state. Thus, if the
initial state is accepting, then $M$ accepts all strings; otherwise, it
accepts none. Therefore, the language recognized by $M$ is either the
language of all strings, or the empty language.

:::

Consequently, every language that is not empty or all strings cannot be
recognized by a 1-state DFA.

## Prefix-Suffix Decomposition

Our next goal is to develop tools that let us show that a language can
only be recognized by DFAs with more than $k$ states for $k > 1$. It no
longer suffices to simply consider the resulting state of a string.

::: {important} Key takeaway from the definition of $\delta^*$

Observe that $\delta^*(q,z)$ depends only on the state $q$, the string
$z$ and the state transition function $\delta$. Intuitively, this means
that after the finite automata processes a prefix of the input string,
the remaining state transitions, and more importantly, whether the input
string is accepted or not, depends **only** on the **current state** and
the **remaining input string, i.e. the suffix**.[^4]

:::

The below observation also immediately follows from the definition but
will be important later on.

::: {prf:observation label=obs-nonreg-decomp} Prefix-Suffix
Decomposition

Let $w$ be an input string, and suppose we decompose $w$ into a prefix
$x$ and a suffix $y$, i.e. $w = xy$, and let $r = \delta^*(q_0,x)$, i.e.
$r$ is the resulting state of the prefix $x$. Then,
$\delta^*(q_0, w) = \delta^*(r,y)$.[^5]

In other words, the computation path of $M$ on $w$ is the computation
path on the prefix $x$ starting from the initial state $q_0$ followed by
the computation path on the suffix $y$ starting from where the first
path ended, i.e. the resulting state of the prefix. We represent this
using the following diagram:

$$q_0 \xrightarrow{x} r \xrightarrow{y} q(w)$$

:::

:::::: {prf:example} Examples of @obs-nonreg-decomp

Consider the following finite automata.

::: {image width=300px} ./path-automaton.png

:::

Here are some examples of @obs-nonreg-decomp:

1.  Input string $01011$ with prefix $010$ and suffix $11$, we get
    $$     \delta^*(q_0, 010) = q_2 \text{ and } \delta^*(q_2, 11) = q_1.
         $$ Equivalently,
    $$     q_0 \xrightarrow{010} q_2 \xrightarrow{11} q_1
         $$
2.  Input string 0011 with prefix $00$ and suffix $11$, we get
    $$     \delta^*(q_0, 00) = q_2 \text{ and } \delta^*(q_2,11) = q_1.
         $$ Equivalently,
    $$     q_0 \xrightarrow{00} q_2 \xrightarrow{11} q_1
         $$
3.  Input string $111$ with prefix $1$ and suffix $11$, we get
    $$     \delta^*(q_0, 1) = q_2 \text{ and } \delta^*(q_2,11) = q_1.
         $$ Equivalently,
    $$     q_0 \xrightarrow{1} q_2 \xrightarrow{11} q_1
         $$

::::::

Looking at the above examples more closely, we see a pattern: if two
strings $x$ and $y$ have the same resulting state, then appending the
same string $z$ to both $x$ and $y$ yield strings $xz$ and $yz$ with the
same resulting state. Moreover, it does not matter what $z$ is, i.e. the
statement holds true for every $z$.

In fact, this is not a coincidence and holds for all DFA. We state it
more formally in the following lemma.[^6]

:::: {prf:lemma label=lem-dist1} Memorylessness Lemma

Let $M$ be a DFA, and let $x$ and $y$ be strings.

If $x$ and $y$ have the same resulting state, then, for every string
$z$, the strings $xz$ and $yz$ also have the same resulting state (i.e.
$q(xz) = q(yz)$), and as a consequence: $M$ either accepts both $xz$ and
$yz$, or rejects both of them. See @fig-memorylessness for an
illustration.

::: {figure label=fig-memorylessness width=300px} ./memorylessness.png

Illustration of memorylessness.

:::

Equivalently, if there exists a string $z$ such that $M$ accepts exactly
one of $xz$ and $yz$ and rejects the other, then $x$ and $y$ do not have
the same resulting state (i.e. $q(x) \neq q(y)$).

::::

## Distinguishable pairs and distinguishing suffixes

@lem-dist1 captures the essence of the memorylessness of finite
automata. To use it to show that certain DFA cannot recognize a language
$L$, we will need the following definition.

::: {prf:definition label=def-nonreg-suffix} Distinguishable pairs and
distinguishing suffixes

Let $L$ be a language and $x,y,z$ be strings. The string $z$ is said to
be a *distinguishing suffix* of the pair $x,y$ if exactly one of $xz$
and $yz$ is in $L$ and the other is not. The pair $x$ and $y$ is a
*distinguishable pair* with respect to $L$ iff it has a distinguishing
suffix.

:::

We also sometimes say that $x$ and $y$ are distinguishable with respect
to $L$, and when the language $L$ is clear from context, we also say $x$
and $y$ are distinguishable.

Let us take a look at some examples and exercises to consolidate our
understanding of the definition.

### Examples of distinguishable pairs and distinguishing suffixes

::: {prf:example}

Let $L$ be the language of even-length bit strings:
$$\epsilon, 00, 01, 10, 11, 0000, 0001, 0010, 0011$$

The strings $\epsilon$ and 0 are distinguishable with distinguishing
suffix $\epsilon$:

- $\epsilon\epsilon = \epsilon$ which is in $L$
- $0\epsilon = 0$ which is not in $L$.

:::

::: {exercise}

Let $L$ be the language of bit strings ending in 10. Give a pair of
distinguishable strings for $L$ along with their distinguishing suffix.

:::

::: {exercise}

Let $L$ be the language of bit strings whose length is divisible by 5.
Give a pair of distinguishable strings for $L$ along with their
distinguishing suffix.

:::

## Distinguishable strings have different resulting states

Next, we connect the notion of distinguishable strings and @lem-dist1 to
show that every DFA that recognizes $L$ must satisfy a certain
condition.

::: {prf:lemma label=lem-dist2}

Let $L$ be a language and $M$ be a DFA. If $x$ and $y$ are
distinguishable strings and $M$ is such that $q(x) = q(y)$, then $M$
does not recognize $L$.

:::

::: {prf:proof}

Let $z$ be a distinguishing suffix of $x,y$. @lem-dist1 implies that $M$
either accepts both $xz$ and $yz$, or rejects them both. On the other
hand, since $z$ is a distinguishing suffix of $x,y$, exactly one of $xz$
and $yz$ is in $L$. Thus, $M$ makes a mistake on one of them and thus
does not recognize $L$.

:::

An immediate consequence of @lem-dist2 is that any language with at
least one distinguishable pair (such as the ones in the example and
exercises above) cannot be recognized by a single-state DFA. Indeed,
there are only 2 languages without any distinguishable pair: the empty
language and the language of all strings. Observe that this gives a
different proof of the characterization of the languages accepted by
single-state DFA (@lem-nonreg-single).

## Fooling sets

Next, we show how to use @lem-dist2 to rule out DFAs with more than 1
state.

::: {prf:definition label=def-nonreg-fooling} Fooling Set

A set of strings $F$ is a *fooling set* for a language $L$ iff every
pair of distinct strings $x \neq y$ in $F$ is distinguishable with
respect to $L$.

:::

::: {prf:lemma label=lem-nonreg-fool} Fooling Set Lemma

Let $L$ be a language. The following are true:

1.  If $L$ has a fooling set with $k$ strings, then $L$ cannot be
    recognized by a DFA with less than $k$ states.
2.  If for every $k \geq 1$, there is a fooling set $F_k$ for $L$ with
    at least $k$ strings, then $L$ is not regular.

:::

::: {prf:proof}

We begin by proving the first part of the lemma. Let $M$ be a DFA with
fewer than $k$ states and $F$ be a fooling set for $L$ of size $k$.
Since there are more strings than there are states, there exist two
strings $x$ and $y$ in $F$ with the same resulting state, i.e.,
$q(x) = q(y)$. Since every pair of strings in $F$ are distinguishable,
there exists a distinguishing suffix $z$ for $x,y$. Thus, @lem-dist2
implies that $M$ does not recognize $L$. This proves the first part of
the lemma.

Next, we prove the second part of the lemma using the first part. Let
$M$ be a DFA. We now show that it does not recognize $L$. Suppose it has
$k$ states. Since $F_{k+1}$ has at least $k+1$ strings, the first part
of the lemma implies that $M$ does not recognize $L$.

:::

::: {tip}

Since the proof outlines a method to find a counterexample for a given
DFA, you may find it helpful to run through the argument in the proof on
a specific nonregular language $L$ and a specific DFA $M$. You should be
able to get a counterexample for that specific DFA. For example, you can
take any of the example DFAs, and the language
$L = \{0^n1^n \mid n \geq 0\}$ and its fooling set given below.

:::

### Examples of fooling sets

::: {prf:example}

Let $L$ be the language of strings ending in 10. Then,
$F = \{\epsilon, 1, 10\}$ is a fooling set of size 3. Here are the
distinguishing suffixes for each pair of string in $F$:

- $\epsilon$ and $1$ has distinguishing suffix 0.
- $\epsilon$ and $10$ has distinguishing suffix $\epsilon$.
- $1$ and $10$ has distinguishing suffix 0.

We conclude that $L$ requires at least 3 states.

:::

::: {exercise label=ex-equal01}

Let $L = \{0^n1^n \mid n \geq 0\}$. Show that for every $k \geq 1$,
there exists a fooling set $F_k$ of size at least $k$.

:::

::: {exercise}

Let $L$ be the language consisting of bit strings of the form $ww$ for
some bit string $w$. In other words, every string in $L$ is some string
$w$ concatenated with itself. So, $L$ contains
$$\epsilon, 00, 11, 1010, 0101, 1111, 101101, \ldots$$

Show that for every $k \geq 1$, there exists a fooling set $F_k$ of size
at least $k$.

:::

### Strategies for constructing fooling sets

There is no sure-fire way of constructing fooling sets for a language
$L$. Here are some general heuristics to try. Each of these were used
for the examples and exercises above.

1.  Construct fooling set using prefixes of strings in $L$.
2.  To construct $F_k$, consider strings for which it seems a counter
    that can count up to at least $k$ is needed to distinguish between
    them.
3.  Often, it is possible to construct a fooling set $F_k$ such that for
    every string $x \in F_k$, there is a string $z$ such that $z$
    distinguishes $x$ from the other strings in $F_k$, i.e. either $xz$
    is in $L$ but $yz$ is not in $L$ for every other $y$ in $F_k$, or
    vice versa.

::: {seealso}

- Section 1.2 of [A Note on Proving Non-Regularity via Fooling
  Sets](https://chekuri.cs.illinois.edu/teaching/fooling-sets.pdf)
- The boxed text at the top of page 17, just before Section 3.9, of
  [Lecture Notes on Finite-State
  Machines](http://jeffe.cs.illinois.edu/teaching/algorithms/models/03-automata.pdf)

:::

::: {tip}

We only need to show that for every $k$, there exists a fooling set of
size **at least** $k$. We do **not** need to show that there exists a
fooling set of size **exactly** $k$ for every $k$.

:::

::: {caution} **Every pair** must be distinguishable

A common pitfall is constructing a set of strings in which not every
pair of strings is distinguishable. The proof needs every pair to be
distinguishable as the pigeonhole principle only tells us that there is
*some* pair $x$ and $y$ with the same resulting states[^7], and our
proof needs to work no matter what pair it is.

For example, it is easy to design a 2-state finite automata that
recognizes the language of even-length bit strings. On the other hand,
consider the set of strings $S$ consisting of the empty string
$\epsilon$ as well as all odd-length strings. Pairing $\epsilon$ with
any odd-length string $x$ gives a distinguishing pair with
distinguishing suffix $0$. Thus, $S$ contains infinitely many
distinguishable pairs but does not imply that the language is not
regular.

:::

### More examples of fooling sets

::: {exercise}

Let $L$ be the language consisting of bit strings where 0 and 1 occur
the same number of times:
$$\epsilon, 01, 10, 1001, 0110, 000111, 010101, 101010, \ldots$$

Show that for every $k \geq 1$, there exists a fooling set $F_k$ of size
at least $k$.

:::

::: {exercise}

A *palindrome* is a string that is the same as its reverse. Let $L$ be
the language of palindromes over the usual Latin alphabet consisting of
letters from a to z. Examples of palindromes in $L$ include: abba,
civic, deed, kayak, level, radar, tacocat[^8]

Show that for every $k \geq 1$, there exists a fooling set $F(k)$ of
size at least $k$.

:::

[^1]: One main difference between these and my lecture notes is that I
    have tried to avoid proof by contradiction as much as possible as
    students who have not encountered them before find them very
    confusing, and I have tried to minimize use of mathematical notation
    and jargon.

[^2]: Chandra also discusses the Pumping Lemma approach and the
    differences between the two approaches.

[^3]: For the mathematically inclined, we can express the second case
    more succinctly as $\delta^*(q,w) = \delta(\delta^*(q,x),a)$.

[^4]: If you are familiar with Markov chains, this should remind you of
    a similar [property of Markov
    chains](https://en.wikipedia.org/wiki/Markov_property).

[^5]: For the mathematically inclined, we can express this more
    succinctly as $\delta^*(q_0,w) = \delta^*(\delta^*(q,x),y)$.

[^6]: The name of the lemma is something I came up with, not a standard
    name used by others.

[^7]: The pair can depend on the exact specification of $M$.

[^8]: What is a tacocat? It's a 🐈 in a 🌮, of course! Here's
    [mine](./tacocat.jpg).
