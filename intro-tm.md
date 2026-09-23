(sec-intro-tm)=

# Introduction to Turing Machines

## Turing Machines, Informally

We can view finite automata as a finite-state machine whose input is on
a tape and controls a tape head.

:::::: {figure width=400px} ./fa-schematic.png

Schematic diagram of a finite automata. The finite control box
represents the various states and state transitions of the finite
automata.

::::::

The finite automata can:

- move its tape head to the right by one cell
- read the symbol under the tape head
- the tape contains only its input
- accepts or rejects once it has reached the end of the tape

A Turing machine (TM) on the other hand can in addition:

- move the tape head to the left by one cell
- write a symbol to the tape cell under the tape head (overwriting
  whatever is in the tape cell)
- has an infinite tape (the input appears first and is followed by an
  infinite number of blanks, denoted by the blank symbol "˽")
- can accept or reject at any time (not only at the end of the input)

::: {prf:example} Informal example

Consider the canonical noncontext-free language
$\{0^n1^n2^n \mid n \geq 0\}$.

Let us first assume that the TM has the ability to "cross off" tape
cells. Here's a high-level description of a TM that recognizes the
language:

1.  Scan right until ˽ (this is the end of the input) while checking
    that the input is of the form $0^*1^*2^*$. If not, reject.
2.  Return tape head to left end
3.  Scan right, "crossing off" the first $0$ that is not crossed off
    yet, the first $1$ that is not crossed off yet, and the first $2$
    that is not crossed off yet
4.  At the end of the scan:
    - If we successfully cross off one $0$, one $1$, one $2$, repeat
      line 3
    - Else if every input symbol is crossed off, accept
    - Else, reject

The crossing off operation can be implemented by using the symbols {strike}`0`, {strike}`1`, {strike}`2` to mean the crossed off versions of 0, 1, and 2.

:::

## Formal Definition

::: {prf:definition} Turing Machine (TM)

A Turing machine $M$ consists of:

1.  Finite input alphabet $Σ$
2.  Finite tape alphabet $\Gamma$ (contains $\Sigma$)
3.  Set of states $Q$
4.  Initial state $q₀$
5.  State transition function $\delta$ which takes as input the current
    state and the symbol under the tape head, and outputs the next
    state, the symbol to write under the tape head and whether to move
    the tape head to the left or right. Mathematically,
    $\delta : Q × \Gamma → Q \times \Gamma \times \{L,R\}$.
6.  Accepting state $q_{acc}$ and rejecting state $q_{rej}$

:::

The transition function can also be represented using a state transition
diagram as was done for finite and pushdown automata. The transition
$\delta(q,a) = (r,b,d)$ is represented by
$q \xrightarrow{a \rightarrow b, d} r$. For transitions that do not
overwrite the symbol under the tape head with a different symbol, i.e.
$\delta(q,a) = (r,a,d)$, we use $q \xrightarrow{a \rightarrow d} r$.

Let $M$ be a Turing machine. Consider an input string
$w = v_1 v_2 \cdots v_n$ where each $v_i$ is a symbol of the alphabet.
The TM processes $w$ as follows:

1.  Initially, the first $n$ tape cells of the tape contain the symbols
    of $w$, with the remaining cells containing the blank symbol ˽
2.  Start at the start state and with the tape head at the start (i.e.
    left end) of the tape
3.  Repeat the following:
    - Let $q$ be the current state and $x$ be the symbol under the tape
      head
    - If $q = q_{acc}$, $M$ halts and accepts
    - If $q = q_{rej}$, $M$ halts and rejects
    - Else:
      - let $(r,y,d) = \delta(q,x)$
      - write $x$ to the cell under the tape head, move tape head in
        direction $d$, and move to state $r$

::: {note} Moving tape head to the left

If the tape head is already at the start (i.e. left end of the tape),
moving it to the left keeps it at the start of the tape.

:::

(sec-tm-config)=

### Turing Machine Configuration and Computation Path

We now explain in more detail exactly what a single step of computation
in a Turing machine looks like.

::: {prf:definition} TM Configuration

The *configuration* of a Turing machine $M$ on an input string $w$ is a
snapshot of its execution at a point in time. In particular, the
configuration consists of:

- the current state
- the current state of the tape (i.e. the contents of the tape)
- the current location of the tape head

We use the following notation for a configuration: we write $xqy$ to
mean that the current state is $q$, the contents of the tape is $xy$ and
the tape head is on the first symbol of $y$.

:::

Let $C$ and $C'$ be two configurations. We say that $C$ *yields* $C'$
iff applying $\delta$ to $C$ results in $C'$ and write
$C \Rightarrow C'$.

::: {prf:example}

Let $C$ be the configuration in which the tape has the string $ababb$
followed by an infinite sequence of blanks, the current state is $q$ and
the tape head is on the third symbol $a$. Then, we write $C = abqabb$.

If $\delta(q,a) = (r,b,R)$, then $C$ yields the configuration
$C' = abbrbb$.

:::

The initial configuration of $M$ on $w$ is $C_1 = q_0w$: the state is
the initial state, the tape contains the input $w$ and the tape head is
at the start of the tape. Let $C_k$ be the configuration obtained after
applying the transition function $k-1$ times.

::: {prf:definition} Halting

A TM $M$ is said to *halt* on input $w$ if after a finite number of
transitions from $C_1$, we obtain a configuration whose state is
$q_{acc}$ (in which case, $M$ *accepts* $w$) or $q_{rej}$ (in which
case, $M$ *rejects* $w$. or $q_{rej}$. A TM $M$ is said to be a
*decider* if it halts on every input.

:::

::: {important} Outcomes of Turing machine computation

A subtle but important feature of Turing machines that finite automata
do not possess is that a Turing machine can potentially loop forever on
an input, never entering $q_{acc}$ or $q_{rej}$.

:::

::: {prf:definition} Turing machine language

The language of a TM $M$ is the set of strings accepted by $M$, and is
denoted by $L(M)$.

:::

::: {prf:definition} Recognizers and deciders

A language $A$ is *Turing-recognizable*[^1] if there exists a TM $M$
such that $A = L(M)$, and *Turing-decidable* if there exists a TM $M$
such that $A = L(M)$ **and** $M$ is a decider. We say that $A$ is
*recognized* by $M$ and *decided* by $M$, respectively.

:::

:::::: {prf:example} while loop

Here is an example of a TM that never halts on any input. The TM simply
moves the tape head to the left forever. Note that moving the tape head
to the left when its already at the start of the tape means it stays at
the start of the tape.

::: {figure width=100px} ./tm-non-halting.png

A TM that loops on every input.

:::

::::::

## Church-Turing Thesis

In the early 1900s, mathematicians wanted to formalize what it means for
a computational problem to "effectively computable". One of the
computational problems they were interested in is determining whether a
formula in first-order logic is satisfiable. Besides the Turing machine,
other models include $λ$-calculus, rewriting systems, and others.
Although the models look very different, they all turn out to be
equivalent to one another in terms of computational power.

This led to the formulation of the Church-Turing Thesis.

::: {note} Church-Turing Thesis

Every reasonable model of unrestricted computation is equivalent to the
Turing machine.

:::

In particular, it means that a problem can be solved using \<insert
favorite programming language\> if and only if it can be solved by a
Turing machine.

::: {note} Thesis vs Theorem

The Church-Turing Thesis is not a formal statement due to the phrase
"every reasonable model of unrestricted computation" and thus cannot be
proved or disproved. Therefore, it is not a theorem. However, there has
been a mountain evidence agreeing with the Thesis.

:::

In the next two sections, we will see that while we can define Turing
machines with extra capabilities, they are no more powerful than the
standard Turing machine. This helps justify that Turing machines is a
model of general-purpose computation.

[^1]: Also called *semi-decidable*.
