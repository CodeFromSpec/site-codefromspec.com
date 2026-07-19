---
title: "Conclusion"
description: "The vocabulary, the bet, and what is left for the person."
date: 2026-07-18
---

# Conclusion

This essay built a vocabulary: a way to name the pieces in play when code comes from a stochastic generator —
the description, the generator, the oracle, the artifact, the draw — so they can be reasoned about, not just
felt.

Almost nothing here is proved, and proof was never the point: hold the words clearly and they force their conclusions.
Once the artifact holds resolutions the description never recorded, "just regenerate it" acquires a price. Once
a spec is a delta against a prior, swapping the generator reprices the text while changing nothing it means.
Once anchoring has three participants, the pull
of the existing code stops being a vague impression and becomes something you can name, locate, and counter. None is a theorem; each is hard to escape once you hold the words, and
each bears on a choice a real project faces.

The whole ambition is not to prescribe how to build software from a generator, but to make the pieces
legible enough that a team can reach its own conclusions — and reach them again when the generator
changes, when the price of a verdict moves, when a piece of the system settles. The framework prescribes none
of it; it gives the choices terms precise enough to answer them.

Two payoffs follow:

The first is the vocabulary itself. Intent, description, generator, oracle, artifact — the words turn out to
describe not just software built with a model, but any software built at all; a team handing a spec to a new hire
runs the same apparatus as a team handing it to a generator. That reach is what makes it worth keeping. The
language a team thinks in is the language it decides in, and reaching for these words is all that was ever
asked.

The second is a bet. Held clearly, the vocabulary can be pushed, its limits tested, its consequences drawn.
This essay is one such pass through spec-driven development — one with a beginning, middle, and end, not an
exhaustive one. Many other passes are possible; this one discarded more insights than it kept. What it kept are
conclusions: that a bug is a silence resolved against intent, splitting into repairs that cannot stand in for
one another; that a spec is the delta between intent and the prior, and drifts toward a prompt when nothing
resists; that a test suite earns its keep by memory more than by coverage. They are strong claims, not proven ones —
offered in the belief that they are true enough to build on, which is the one thing the essay cannot settle
from inside. They also do not all expire on the same schedule. Some are analytic and fall only with the
vocabulary — that a description-facing oracle passes mis-specification through certified is true by
construction, under any generator. Others are aimed at the generator in use. The judge's narrow niche is the
clearest case: it is the resultant of two empirical asymmetries — how much judge and generator share a
prior, and how much cheaper reading is than writing — and it reprices when either moves. That is the essay's
own thesis applied to itself: every encoding is written against a prior, this one included. Where a
conclusion leans on the current generator, a swap rereads it, and the vocabulary is what is built to survive
the rereading. Holding the words puts a team in a position to settle it.

The words answer three questions a newcomer to spec-driven development actually asks: what SDD is, what the
model is doing inside it, and what is left for the person. The first two they answer directly; the third they
answer by subtraction. The apparatus keeps everything in text (the description, the tests, the artifact)
except two things. Intent is held by people, not files, and what the readers know about the code lives in
heads, not in the repository. From those two exclusions the irreducible roles fall out: someone has to hold
the intent and author the delta that encodes it, and someone has to deliver the verdicts that answer to
intent, because no built oracle does. Everything between them — the writing, the routine resolution of
silences, the mechanical checking — can move to the machine, component by component. Remove those two poles as
well, and the result is not the dream. It is a loop optimizing conformance to encodings whose fidelity to
intent no one is left to check.
