(sec-background)=

# Background

Refer to this section for a refresher on notation and terminology that
will be heavily used throughout. For more detailed discussion, see the
relevant sections of:

- [Discrete Math
  Background](https://complexityincs.com/discrete-math.pdf) by Thomas
  Watson;
- [Mathematics for Computer
  Science](https://courses.csail.mit.edu/6.042/spring18/mcs.pdf) by Eric
  Lehman, F. Thomson Leighton, and Albert R. Meyer ([lecture
  slides](https://opencw.aprende.org/courses/electrical-engineering-and-computer-science/6-042j-mathematics-for-computer-science-spring-2015/lecture-slides/)).

## Set notation

In the following, let $S$ and $T$ be sets.

- $\emptyset$ is the empty set
- $x \in S$ means $x$ is in $S$; and $x \notin S$ means $x$ is not in
  $S$.
- $S \setminus T$ is the set of elements in $S$ but not in $T$
- $S \cap T$ is the set of elements in $S$ **and** $T$, and is called
  the *intersection* of $S$ and $T$
- $S \cup T$ is the set of elements in $S$ **or** $T$, and is called the
  *union* of $S$ and $T$
- $S \subseteq T$ means $S$ is a subset of $T$, i.e. every element of
  $S$ is an element of $T$; we also say that $S$ is *contained in* $T$.
  Moreover, we say that $S$ is a *proper subset* of $T$ (or $S$ is
  *properly contained in* $T$) if $S$ is contained in $T$ and $T$
  contains some element not in $S$; this is written as $S \subsetneq T$.
- $S \supseteq T$ means $S$ is a superset of $T$, i.e. every element of
  $T$ is an element of $S$; we also say that $S$ *contains* $T$.
  Similarly, we say that $S$ is a *proper superset* of $T$ (or $S$
  *properly contains* $T$) if $S$ contains $T$ and $S$ contains some
  element not in $T$; this is written as $S \supsetneq T$.
- $2^S$ is the set containing every subset of $S$ (including $\emptyset$
  and $S$), and is called the *powerset* of $S$. Sometimes, it is
  denoted by $\mathcal{P}(S)$.
  - For example, when $S$ is the set $\{a,b\}$, then the elements of
    $2^S$ are $\emptyset$, $\{a\}$, $\{b\}$ and $\{a,b\}$.
- $\{x \in S \mid P(x)\}$ means the set of elements in $S$ that
  satisfies the predicate $P$. This is called the *set-builder
  notation*.
  - For example, if $S$ is a set of numbers, then
    $\{x \in S \mid x \text{ is even}\}$ is the set of even numbers in
    $S$.
  - Sometimes, : is used in place of $\mid$. For example,
    $\{x \in S : x \text{ is even}\}$
- If $S$ is a subset of $T$, then the *complement* is $S \setminus T$,
  the elements of $T$ not in $S$.
- For a positive integer $m$, we write $[m]$ as shorthand for the set of
  integers from $1$ to $m$: $[m] = \{1, \ldots, m\}$.

For a more in-depth and beginner-friendly discussion see also [Guide to
Elements and
Subsets](https://web.stanford.edu/class/archive/cs/cs103/cs103.1246/resources/Guide%20to%20Elements%20and%20Subsets.pdf).

(sec-background-thms)=

## Theorems and Lemmas

Usually, a *theorem* is an important result that we want to show, and a
*lemma* is a smaller result that is not necessarily interesting by
itself, but is useful to bigger results. Often, a theorem is proved by
proving a sequence of smaller lemmas. In a way, lemmas are similar to
the role of subroutines in programming.

See also [CS103 Guide to
Proofs](https://web.stanford.edu/class/cs103/guide_to_proofs_on_discrete_structures#writing-longer-proofs)
and Section 1.3.2 of [Introduction to Theoretical Computer
Science](https://introtcs.org/public/lec_00_1_math_background.html).

## Proofs

(sec-iff)=

### Proving if and only if statements

There are 3 ways to prove a statement of the form "$A$ if and only if
$B$":

1.  Prove "If $A$, then $B$" and "If $B$, then $A$";
2.  Prove "If $A$, then $B$" and "If not $A$, then not $B$";
3.  Prove "If $B$, then $A$" and "If not $B$, then not $A$".
