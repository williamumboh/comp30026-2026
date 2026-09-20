(sec-nonreg)=

# Proving Nonregularity

::: {warning} Challenging material ahead

This section will require several passes and lots of hands-on practice.
Please ensure that you budget extra time.

:::

In this section, we get a first glimpse at proving impossibility
results. In particular, we will discuss methods for proving a given
language $L$ is nonregular, i.e. that there is no possible deterministic
finite automata that can recognise $L$.

::: {exercise} Check your understanding

We have also seen nondeterministic finite automata and regular
expressions. Based on what we have already seen, do we need to prove
separately that there is no nondeterministic finite automata that
recognises $L$?

:::

We begin with @sec-nonreg-fooling, and then we show how to use
@sec-nonreg-closure properties to leverage the fact that some other
language is already known to be nonregular (Week 9).
