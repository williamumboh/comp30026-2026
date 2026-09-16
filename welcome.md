# Welcome!

Welcome to the website for Weeks 8.5 to 12 of COMP30026 Models of
Computation!

This website contains the companion notes to my lectures and other
resources. There is a lot of dense information, putting all of them up
on the slides can be visually overwhelming. Thus, I have designed my
slides to only contain information pertinent to the focus of the
lecture. You are welcome to open up the website on your laptop or phone
(it's actually mobile-friendly) to use as a reference during the
lecture.

::: {tip}

You can hover over a linked definition, lemma or theorem and have it
popup like [this](./hover-link-demo.mov). The search bar in the top
right corner lets you search across all the notes.

:::

::: {caution}

The lecture notes and the lectures are meant to complement each other.
Certain information is best presented in text form and others are best
presented in a live delivery. Thus, there will be some information that
is only presented in one of the two modes. Also note that some live
demonstrations will not be captured on slides.

:::

# Lecture schedule

| Lecture | Topics Covered (Topic Numbers, Slides) |
|----|----|
| Week 8 Lecture 2 | @sec-nonreg, @sec-nonreg-fooling, [Slides](./w8l2-slides.pdf) |

# Resources

My materials and approach to teaching them are inspired by the following
amazing books and courses. They will also be the main resource for this
subject.

- [Introduction to Theoretical Computer
  Science](https://introtcs.org/public/index.html) by Boaz Barak
  (Harvard University)
- [CS 103: Mathematical Foundations of
  Computing](https://web.stanford.edu/class/archive/cs/cs103/cs103.1252/)
  (Stanford University)
- [CS 251: Great Ideas in Theoretical Computer
  Science](https://s23.cs251.com/index.html) (CMU)
- [Notes on Models of
  Computation](http://jeffe.cs.illinois.edu/teaching/algorithms/#models)
  by Jeff Erickson[^1] (UIUC)

Introduction to the Theory of Computation by Michael Sipser is a great
textbook.

# Proofs

One of the biggest differences with the above resources is that writing
mathematical proofs about computation is not an intended learning
outcome for COMP30026. Instead, our goal is to teach the key ideas and
arguments behind these proofs. These can then be turned into
mathematical proofs once one has learned mathematical proofs (e.g.
[MAST20026 Real
Analysis](https://handbook.unimelb.edu.au/subjects/mast20026)). If you
are interested in self-learning how to write proofs, check out the
Resources section and lectures of CS 103 (this subject only assumes US
high school algebra as a
[prerequisite](https://web.stanford.edu/class/archive/cs/cs103/cs103.1246/prereqs).)
and Module 1 of CS 251.

# Tips for success

The
[Preface](https://introtcs.org/public/lec_00_0_preface.html#to-the-student)
of Introduction to Theoretical Computer Science has some great advice on
how to succeed.

There are two pieces of advice that I want to add to the above.

::: {tip}

**Examples** are essential to understanding definitions and theorems.

:::

Most of the time, the definitions and theorems are stated in very
general terms and thus can seem very abstract. I strongly encourage you
to think about some simple concrete examples of the objects in the
definitions and theorems. For example, later on, we will talk about sets
of bit strings; it is helpful to check your understanding by thinking
about whether the set contains the empty string? What is the smallest
string in the set? What is the smallest string that is *not* in the set?
Is the set empty? Does it contain every string?

::: {tip}

When you feel stuck, ask yourself: what is the **simplest** thing I do
not yet understand or understand how to do?

:::

For example, when working on problems, it is useful to try to find
simpler versions that you can tackle. We will provide scaffolding for
problems in Assignment 2 to show you how to break down problems into
simpler subproblems.

[^1]: Jeff also has a wonderful [Algorithms
    textbook](https://jeffe.cs.illinois.edu/teaching/algorithms/) that
    is free and available online.
