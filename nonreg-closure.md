(sec-nonreg-closure)=

# Technique 2: Closure Properties

Now that we have several languages that we have shown to be nonregular,
we switch to a different technique that can often result in a shorter
proof but can be trickier to apply.

Recall that regular languages are closed under several operations such
as:

1.  complement
2.  intersection

These operations let us take regular languages and create new regular
languages:

1.  if $L$ is regular, then $L^c$ is also regular.
2.  if $A$ and $B$ are regular, then so is $A \cap B$.

We can also use them to prove nonregularity as follows:

1.  if $L^c$ is not regular, then $L$ cannot be regular.
2.  if $A$ is regular and $A \cap B$ is not regular, then $B$ cannot be
    regular.

## Example applications

::: {prf:example}

Let $L$ be the language of bit strings where 0 and 1 do not occur the
same number of times. Since $L^c$ is the language of bit strings where 0
and 1 occur the same number of times, and we have already shown $L^c$ is
not regular in @ex-equal01, we get that $L$ is not regular.

:::

::: {prf:example}

Let $B$ be the language of bit strings where 0 and 1 occur the same
number of times.

We have already shown using that $B$ is not regular in @ex-equal01 via
fooling sets. We now give a shorter proof using the fact that the
language $L = \{0^n1^n \mid n \geq 0\}$ is not regular. Observe that
$L = A \cap B$ where $A = 0^*1^*$, i.e. the language of strings in which
0s can only appear to the right of 1, and vice versa. Since $A$ is
regular but $A \cap B$ is not, then $B$ cannot be regular.

:::

## Other operations

In general, one can use any operation that the regular languages are
closed under. Other operations include:

1.  union
2.  concatenation
3.  Kleene star (aka Kleene closure)

## Fooling sets vs closure properties

Here are the benefits and drawbacks to the two techniques:

1.  If a language $L$ is not regular, one can always prove its
    nonregularity using fooling sets.[^1] This is not always the case
    for the closure property technique.[^2]
2.  Even if a language can be proved nonregular using closure
    properties, it can be quite tricky to find the right languages and
    operations.
3.  Proofs via closure properties tend to be much shorter.

One can also combine the two. For example, suppose you want to show $B$
is nonregular but are having difficulty finding fooling sets for $B$.
Then, you can try to find a regular language $A$ such that it is easier
to find fooling sets for $A \cap B$. In other words, closure of
intersection lets you *reduce* the task of finding fooling sets for $B$
to finding fooling sets for $A \cap L$. More generality, it lets you
reduce the task of proving nonregularity of $B$ to proving nonregularity
of $A \cap B$.

[^1]: In fact, the Myhill-Nerode Theorem says that $L$ is not regular if
    and only if for every $k$, there is a fooling set of size at least
    $k$.

[^2]: One might say the fooling set technique is *fool-proof*.
