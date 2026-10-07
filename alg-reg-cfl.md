(sec-alg-reg-cfl)=

# Algorithms for Regular and Context-Free Languages

In the following, we use the notation $\langle O \rangle$ to denote the
encoding of the object $O$ into a string. Given a list of objects
$O_1, \ldots, O_k$, we use $\langle O_1, \ldots, O_k \rangle$ to be an
encoding of the objects into a single string.

For example, if $x_1q_1y_1$ and $x_2q_2y_2$ are the configurations of a
NTM at some point in time, we can encode them into a single string
$x_1q_1y_1\#x_2q_2y_2$.

Another example is that we can encode Turing machines into their
corresponding Python (or your favorite programming language) source
code.

The following is a brief summary of the results for problems about
regular and context-free languages. See lectures for details.

## Regular languages

- DFA Acceptance is decidable.
  - The idea is that we can simulate a DFA on any input string $w$. The
    simulation only requires applying the DFA's transition function $k$
    times where $k$ is the length of $w$.
- NFA Acceptance is decidable.
  - The idea is to first determinize the NFA and then apply the
    algorithm for DFA Acceptance.
- DFA Emptiness is decidable.
  - The idea is that the sequence of transitions a DFA takes on an input
    string is a path in the state transition diagram starting from the
    initial state; and the string is accepted if the path ends in an
    accepting state.
  - Thus, there is a string that the DFA accepts if and only if there is
    a path from the initial state to an accepting state, which we can
    determine by running breadth-first search
- DFA Equivalence is decidable
  - The idea is that two languages are equivalent if and only if their
    symmetric difference is the empty set
  - Using closure properties of regular languages, we can transform the
    DFAs for the two languages into a DFA for the symmetric difference
    on which we then run DFA Emptiness

## Context-Free

For these, you only need to know the statements, not the proofs. See
Sipser for proofs.

- CFG Acceptance is decidable[^1]
- CFG Emptiness is decidable
- CFG Equivalence is undecidable
- CFG Ambiguity is undecidable

[^1]: This is not as easy as DFA Acceptance.
