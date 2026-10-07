(sec-tm-accept)=

# Turing Machine Acceptance Problem

The TM Acceptance problem is as follows: given an encoding
$\langle M \rangle$ of a Turing machine $M$ and an input string $w$,
does $M$ accept $w$?

Equivalently, we define the language $A_{TM}$ to consist of strings
$\langle M, w \rangle$ such that $M$ is a Turing machine and $M$ accepts
$w$.

::::: {caution}

It is tempting to claim that the following TM decides $A_{TM}$.

On input $\langle M, w\rangle$:

1.  Simulate $M$ on input $w$
2.  Accept if $M$ halts and accepts
3.  Reject if $M$ halts and rejects
4.  Reject if $M$ runs forever

The issue is that "Reject if M runs forever" is not a valid TM action.

::: {figure width=300px} ./titanic-meme.jpg

An actual run of the TM.

:::

:::::

::: {prf:theorem label=thm-tm-accept-recognizable}

$A_{TM}$ is recognizable.

:::

::: {prf:proof}

The following Turing machine $U$ recognizes $A_{TM}$:

On input $\langle M, w \rangle$:

1.  Let $C$ be initial [configuration](#def-tm-config) of $M$ on $w$
2.  While state of $C$ is neither the accept nor the reject state:
    - Update $C$ to next configuration by applying the transition
      function of $M$
3.  If state of $C$ is accept, accept; else, reject

To prove that it recognizes $A_{TM}$, we need to show that $U$ accepts
$\langle M, w \rangle$ if and only if $M$ accepts $w$. There are three
possible cases when $M$ is run on $w$:

1.  $M$ halts and accepts
2.  $M$ halts and rejects
3.  $M$ runs forever

By definition, if $M$ accepts, then $U$ accepts. In the other cases, $U$
either rejects or runs forever. Thus, $U$ accepts $\langle M, w \rangle$
if and only if $M$ accepts $w$, as desired.

:::

(sec-universal)=

## Universal Turing machines

The fact that we can design a Turing machine that takes as input an
encoding of another Turing machine and simulate it is referred to as the
"universal" nature of Turing machines. In contrast, finite automata and
pushdown automata do not have this feature: there is no universal finite
automaton (pushdown automaton, resp.) that can take an encoding of a
finite automaton (pushdown automaton, resp.) and simulate it.

Universality also has huge practical importance. For example, your
mobile phone is not a single-purpose device: by loading different apps,
it can take on other functionality. Without universality, [you need one
device to browse the Internet, a separate one to play music, and a
separate one to make
calls](https://www.youtube.com/watch?v=OLenSrOsWLc&t=61s).
