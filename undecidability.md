(sec-undecidability)=

# Undecidabilty

We now show that not only are there undecidable problems but also
problems that are not even recognizable!

We begin by [constructing a language $L_D$](#sec-LD) that is not
recognizable using a technique called *diagonalization* and exploiting
the [universality of Turing machines](#sec-universal). Then, we show
that [if $A_{TM}$ is decidable, then $L_D$ is
decidable](#sec-tm-accept-reduction) as well. Since $L_D$ is not
recognizable, it is also not decidable, and thus we conclude that
[$A_{TM}$](#sec-tm-accept) is undecidable.

(sec-LD)=

## Constructing $L_D$ via Diagonalization

Fix an encoding of Turing machines $M$ into finite-length binary strings
$\langle M \rangle$.[^1] It is easy to see that we can list all possible
strings in string order. Since every Turing machine $M$ has a binary
string encoding, we can also list all possible Turing machines in string
order of their encodings. Let $w_i$ be the $i$-th binary string in
string order,[^2] and $M_i$ be the $i$-th Turing machine in string order
of its encoding.

We can now define a table that enumerates all Turing machines and
consequently, all recognizable languages: the $i$-th row corresponds to
the $i$-th Turing machine $M_i$, and the $j$-th entry in the row is 1 if
$M_i$ accepts $w_j$ and 0 otherwise. In other words, the entries in row
$i$ specifies $L(M_i)$, exactly the set of strings accepted by $M_i$.
See figure below for an illustration of the table.

Next, we define the language $L_D$ by flipping the entries along the
diagonal of the table: the string $w_i$ is in $L_D$ if and only if $M_i$
does not accept $w_i$, or equivalently,
$L_D = \{w_i \mid w_i \notin L(M_i)\}$. See figure below for an
illustration.

::: {figure width=400px} ./diagonalization.png

Illustration of construction of $L_D$. The bottom table is the
enumeration of all Turing machines. Entry $(i,j)$ is 1 if $M_i$ accepts
$w_j$ and 0 otherwise.

:::

::: {prf:theorem label=thm-LD}

The language $L_D$ is not recognizable.

:::

::::: {prf:proof enumerated=false}

To show that no Turing machine recognizes $L_D$, it suffices to show
that for every Turing machine $M$, there is a string $w$ which it makes
a mistake on, i.e., it accepts $w$ when $w \notin L_D$ or it rejects $w$
when $w \in L_D$. Suppose $M$ is the $i$-th Turing machine, i.e.,
$M = M_i$. Then, by definition of $L_D$, we have that $w_i \in L_D$ if
and only if $M_i$ does not accept $w_i$. Therefore, we conclude that $M$
makes a mistake on $w_i$ and thus does not recognize $L_D$.

:::::

(sec-tm-accept-reduction)=

## Undecidability of TM Acceptance

We can now show that $A_{TM}$ is undecidable by showing that a decider
for $A_{TM}$ gives a decider for $L_D$ which is impossible since $L_D$
is not even recognizable.

::: {prf:theorem label=thm-tm-accept-reduction}

If $A_{TM}$ is decidable, then $L_D$ is decidable.

:::

::: {prf:proof enumerated=false}

Suppose there exists a Turing machine $M$ that decides $A_{TM}$. The
idea is to use $M$ to look up the diagonal entries of the table used to
define $L_D$. Consider the following Turing machine $D$.

On input $w$:

1.  Compute $i$ such that $w = w_i$
2.  Simulate $M$ on $\langle M_i, w_i \rangle$
3.  If $M$ rejects, accept; else, reject.

Since $M$ is a decider, it always accepts or rejects, it never runs
forever. Thus, $D$ also always halts. By definition of $M$ being a
decider for $A_{TM}$ and by definition of $D$, we get that for every
string $w_i$, $D$ accepts it if and only if $M_i$ does not accept $w_i$.
Therefore, $D$ decides $L_D$.

:::

@thm-LD and @thm-tm-accept-reduction imply that $A_TM$ is undecidable.

::: {prf:theorem}

$A_{TM}$ is undecidable.

:::

(sec-self-reference)=

## Undecidabilty via Self-Reference

The proof of the undecidability of $A_{TM}$ given above is different
from the typical proof, which uses self-reference to obtain a
contradiction, and can be hard to understand at first. In this section,
we give proofs using self-reference as self-reference is a key concept
in computation and is essential for proofs of undecidability using
diagonalization.

Intuitively, the idea is similar to how self-reference can lead to
logical paradoxes such as the [liar
paradox](https://en.wikipedia.org/wiki/Liar_paradox) and the [barber
paradox](https://en.wikipedia.org/wiki/Barber_paradox) (also in Homework
problem P6.1). In one version of the liar paradox, there are two types
of people: truth-tellers who always tell true statements, and liars who
always tell false statements. Suppose you meet a person who declares "I
am a liar". If the statement is true, then the person is lying and so
the statement is false. On the other hand, if the statement is false,
then the person is telling the truth and so the statement is true. Since
the statement can either be true or false, and both possibilities lead
to contradictions.

As before, fix an encoding of Turing machines $M$ into strings
$\langle M \rangle$ and let $M_i$ be the $i$-th Turing machine in string
order of its encoding. We now define a similar table as before where row
$i$ corresponds to $M_i$ but we will only focus on columns that also
correspond to encodings of Turing machines. See figure below for an
illustration.

The language $L_S$ is defined as follows:

- if $w$ is not a valid encoding of a Turing machine, $w$ is not in
  $L_S$
- if $w$ is a valid encoding of some Turing machine $M_i$ (i.e.
  $w = \langle M_i \rangle$), then $w$ is in $L_S$ if and only if $w$ is
  not accepted by $M_i$ (i.e. $M_i$ either rejects or runs forever on
  $w = \langle M_i \rangle$)

Equivalently, $L_S$ consists of the encodings of Turing machines $M$
that do not accept their own encodings:
$$L_S = \{ \langle M \rangle \mid \langle M \rangle \notin L(M)\}.$$

::: {figure width=400px} ./diagonalization-LS.png

Illustration of the construction of $L_S$. Entry $(i,j)$ is 1 if $M_i$
accepts $w_j$ and 0 otherwise.

:::

::: {prf:theorem}

The language $L_S$ is not recognizable.

:::

:::::: {prf:proof enumerated=false}

We now use proof by contradiction to show that there is no recognizer
for $L_S$.

Suppose, towards a contradiction, that there is a TM $R$ that recognizes
$L_S$. Thus, $R$ accepts $\langle M \rangle$ if and only if $M$ does not
accept its own encoding $\langle M \rangle$.

What happens if we run $R$ on its own encoding $\langle R \rangle$?
There are two possibilities:

1.  either $R$ accepts its own encoding $\langle R \rangle$
2.  or $R$ does not accept $\langle R \rangle$.

We now show that both of these cases lead to contradictions, just like
in the paradox example above.

1.  Suppose $R$ accepts $\langle R \rangle$. Since $R$ recognizes $L_S$,
    we get that $\langle R \rangle \in L_S$. But the definition of $L_S$
    implies that $\langle R \rangle \in L_S$ if and only if $R$ does not
    accept $\langle R \rangle$. Thus, we get a contradiction.
2.  Suppose $R$ does not accept $\langle R \rangle$. Since $R$
    recognizes $L_S$, we get that $\langle R \rangle \notin L_S$. But
    the definition of $L_S$ implies that $\langle R \rangle \notin L_S$
    if and only if $R$ accepts $\langle R \rangle$. Thus, we get a
    contradiction.

Since both possibilities lead to contradictions, and all reasoning steps
are valid except for the assumption that $L_S$ is recognizable, we
conclude that $L_S$ is in fact not recognizable.

::::::

## Undecidabilty of $A_{TM}$ via Self-Reference

We now give a direct proof that $A_{TM}$ is undecidable using
self-reference, instead of relying on some other language being
unrecognizable.

::: {prf:theorem}

$A_{TM}$ is undecidable.

:::

::: {prf:proof enumerated=false}

Suppose, towards a contradiction, that there is a TM $H$ that decides
$A_{TM}$. Thus, $H$ accepts $\langle M, w \rangle$ if and only if $M$
accepts $w$.

We now construct a TM $D$ using $H$ that takes as input encodings of
Turing machines and behaves as follows:

On input $\langle M \rangle$:

1.  Run $H$ on $\langle M, \langle M \rangle \rangle$
2.  Accept if $H$ rejects
3.  Reject if $H$ accepts

Since $H$ is a decider, we can indeed run $H$ on
$\langle M, \langle M \rangle \rangle$ so $D$ is also a decider.

What happens if we run $D$ on its encoding $\langle D \rangle$? Observe
that for every Turing machine $M$, $D$ accepts $\langle M \rangle$ if
and only if $M$ does not accept $\langle M \rangle$. So, if we run $D$
on its own encoding $\langle D \rangle$, we get that $D$ accepts
$\langle D \rangle$ if and only if $D$ does not accept
$\langle D \rangle$, a contradiction!

Since assuming that $A_{TM}$ is decidable leads to a contradiction, we
get that $A_{TM}$ is in fact undecidable.

:::

## Optional: Fun with Self-Reference

There are lots of fun things you can do with self-reference:

- [quines](https://en.wikipedia.org/wiki/Quine_(computing)) are programs
  that print their own source code. This is how viruses replicate.
- creating a backdoored login program with no trace in its source code.
  The origin is [Ken
  Thompson's](https://en.wikipedia.org/wiki/Ken_Thompson)[^3] Turing
  Award lecture [Reflection On Trusting
  Trust](https://dl.acm.org/doi/10.1145/358198.358210), now considered a
  seminal work in computer security. You may find this [blog
  post](https://www.cesarsotovalero.net/blog/revisiting-ken-thompson-reflection-on-trusting-trust.html)
  easier to read.

Check out Bernard Chazelle's excellent essay [Algorithm as an Idiom of
Modern
Science](https://www.cs.princeton.edu/~chazelle/pubs/algorithm.html) for
more examples.

[^1]: For example, Python source code in binary.

[^2]: $w_0 = \epsilon$, $w_1 = 0$, $w_2 = 01$, $w_3 = 11$, …

[^3]: One of the creators of UNIX and the Go programming language.
