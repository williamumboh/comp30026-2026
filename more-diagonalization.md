# More diagonalization (OPTIONAL, unassessed)

In this section, we give more examples of diagonalization and an
alternate proof that there is an unrecognizable language. This is
completely optional and unassessed.

The idea is to show that there are more languages than there are Turing
machines and thus there is a language that is not recognized by any
Turing machine. But the set of languages is infinite and so is the set
of Turing machines, so first we need a way to compare the sizes of
infinite sets.

To compare the sizes of finite sets, we can simply count the number of
elements in both sets and then compare the counts. We cannot do this for
infinite sets. However, observe that another way to show that two finite
sets $A$ and $B$ have the same size is by showing that we can match each
element of $A$ to a unique element of $B$. If the sets have different
sizes, there is no such matching.

::: {prf:example}

Consider the set $A = \{1,2,3\}$ and $B = \{a,b,c\}$. Then, we can match
them as follows:

- 1 $\rightarrow$ a
- 2 $\rightarrow$ b
- 3 $\rightarrow$ c

:::

Fortunately, this idea of matching extends to infinite sets. Formally,
the matching is given by a function $f : A \rightarrow B$ that maps
elements in $A$ to elements in $B$. The function $f$ is a *1-1
correspondence* if it satisfies:

1.  (Injectivity) No two elements in $A$ get mapped to the same element
    in $B$. Formally, for every $x,y \in A$, we have $f(x) \neq f(y)$.
2.  (Surjectivity) Every element in $B$ is mapped to by some element in
    $A$. Formally, for every $z \in B$, there is a $x \in A$ such that
    $f(x) = z$.

We say that two sets $A$ and $B$ have the same size iff there is a 1-1
correspondence $f: A \rightarrow B$. Moreover, if set $A$ has the same
size as the set of natural numbers $\mathcal{N} = \{1, 2, 3, \ldots\}$,
then we say that $A$ is *countable*.

## Countable sets

::: {prf:theorem}

The set of Boolean strings $\{0,1\}^*$ is countable.

:::

::: {prf:proof}

Sort the set of Boolean strings in string order: $\epsilon$, 0, 1, 00,
01, 10, 11, 000, … Define the 1-1 correspondence
$f : \mathcal{N} \rightarrow \{0,1\}^*$ as follows: $f(i)$ is the $i$-th
string in string order.

:::

::: {prf:theorem}

The set of Turing machines is countable.

:::

::: {prf:proof}

Fix an encoding of Turing machines into strings; for example, we can
encode any Turing machine as source code in some programming language.
We can then sort the Turing machines according to string order of their
encodings and define the 1-1 correspondence $f$ as follows: $f(i)$ is
the $i$-th Turing machine in the above order.

:::

## Diagonalization for Reals

To show that there are more languages than Turing machines, we will use
a technique called *diagonalization*. First, we present the technique in
a simpler setting and use it to show that the set of real numbers
$\mathcal{R}$ is uncountable. Thus the set of reals is larger than the
set of natural numbers.

::: {prf:theorem}

$\mathcal{R}$ is uncountable.

:::

::::: {prf:proof}

We will show that every function
$f : \mathcal{N} \rightarrow \mathcal{R}$ misses some real number, i.e.
is not surjective: there exists $x \in \mathcal{R}$ such that for every
$n \in \mathcal{N}$, $f(n) \neq x$.

Consider a function $f : \mathcal{N} \rightarrow \mathcal{R}$. Define
the real number $x$ as follows: for every $i \geq 1$, $x$ differs from
$f(i)$ in the $i$-th decimal place.

::: {figure} ./diagonalization-reals.png

Illustration of construction of $x$ given $f$. It is called
"diagonalization" because $x$ is defined by the diagonal of the table.

:::

This is a valid definition of a real number. By definition,
$f(n) \neq x$ for every $n \geq 1$ and so $f$ is not a 1-1
correspondence.

Since this argument holds for every function
$f : \mathcal{N} \rightarrow \mathcal{R}$, we get that there is no 1-1
correspondence between $\mathcal{N}$ and $\mathcal{R}$ and so
$\mathcal{R}$ is uncountable.

:::::
