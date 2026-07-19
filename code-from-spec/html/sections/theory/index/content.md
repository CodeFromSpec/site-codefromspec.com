---
title: "A (peculiar) theory of spec-driven development"
description: "A vocabulary for naming the parts of spec-driven development — what follows once a stochastic generator exists, where each part reaches its limits, and grounds for design decisions."
date: 2026-07-18
---

![A (peculiar) theory of spec-driven development](/images/banner_theory.png)

# A (peculiar) theory of spec-driven development

July 18, 2026

Start with two observations about generating code with a stochastic model:

- A spec run through a non-deterministic generator defines a space of programs, not one single program; and

- the generated artifact is the point in that space you landed on.

This essay is about what follows once such a generator exists. It was written in the course of designing
this spec-driven development methodology, and it serves this work as an instrument: a vocabulary for naming the
parts, a map of where each part reaches its limits, and grounds for design decisions.

- [Part I — The primitives](/theory/part-i) — The six primitives and the four operations that act on them.
- [Part II — The apparatus at its limits](/theory/part-ii) — What happens when each primitive is pushed to its limit, and the corner where current practice already sits.
- [Part III — The description](/theory/part-iii) — What the description carries, what it leaves to the generator, and what it costs to keep.
- [Part IV — The oracle](/theory/part-iv) — Where verdicts come from, what each costs, and how far each can be trusted.
- [Part V — The test suite](/theory/part-v) — What a test suite buys when generation is cheap.
- [Part VI — The case for decomposition](/theory/part-vi) — Why decomposition matters more, not less, under spec-driven development.
- [Conclusion](/theory/conclusion) — The payoffs, and the irreducible roles no apparatus replaces.
