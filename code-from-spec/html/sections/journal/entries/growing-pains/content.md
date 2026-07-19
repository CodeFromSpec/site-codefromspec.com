---
title: Growing pains
description: "The theory essay gave the project a vocabulary for analyzing its own promises — and the first thing it analyzed was how incomplete they were."
date: 2026-07-19
---

# Growing pains

July 19, 2026

## Three weeks on one document

![Growing pains](/images/journal/20260719_image_1.png)

The last three weeks went into a single document: a multi-part
essay building a vocabulary for spec-driven development. What
follows once a stochastic generator exists, where each part
reaches its limits, and grounds for design decisions. It is
published now as the [theory](/theory) section of this site.

The essay was written as an instrument, a way to name the parts
precisely enough that design decisions could be reasoned about
rather than felt. Six primitives (intent, description, generator,
oracle, artifact, and the space they live in), four operations,
and a set of consequences that follow once you hold the words
clearly. Almost nothing in it is proved; the point was never
proof but precision.

What I did not expect was what the instrument would do to the
rest of the project the moment it existed.

## What broke

This site opened with a sentence: *specifications are the source
of truth; code materializes them.* The essay's Part III arrives
at a different conclusion: the unit of truth is the pair — the
spec, which says what must be true, and the artifact, which
records how everything the spec delegated was resolved. Neither
half can reconstruct the other. Discard the artifact and you keep
your boundaries but lose your position — every resolution nobody
ratified is re-decided by the next draw.

The [rationale](/rationale) promises a destination: domain experts
contributing specifications within engineering guardrails, the way
product developers ship on a platform team's golden paths. It is
a compelling argument, and I still believe in the destination. But
the essay exposes machinery the rationale does not account for.
The spec drifts toward a prompt under the pressure of the
generation loop (Part III), a document increasingly tuned for
the model and decreasingly legible to its human readers. The
oracle that would let a domain expert trust the output cannot be
built (Part IV): to build is to author, and whatever is authored
encodes its author's understanding, not intent itself. The
verdicts that answer to intent directly are the reactions nobody
can schedule.

I had articles prepared for publication that are now partially
obsolete — their arguments absorbed by the essay or superseded by
its vocabulary. The essay did not invalidate the earlier thinking.
It reframed it in terms precise enough to see where it was
incomplete.

## What the vocabulary gives back

The domain expert is a generator with a different prior than an
engineer's. The spec they would write has different safe silences,
probably far fewer. The guardrails the rationale promises are
decomposition and encapsulation (Part VI): the expert operates
within boundaries an engineer designed, the same way a product
developer's deploy is safe because of a pipeline someone else
hardened. The verdicts the expert needs are intent-facing, the
most expensive kind. But a structured spec reviewed by the
expert, even if not authored by them, is already one hop closer
to intent than code they cannot read.

None of this was articulable before the essay. The vocabulary
does not answer whether the destination is reachable. It gives
the tools to ask the question precisely (which conditions must
hold, which are expensive, which may be impossible) instead of
promising and hoping.

## What is already done

The rationale is frozen with a note: *April 2026 — the argument
that launched the project, preserved as published.* The landing
page carries a last-updated date. The theory is published. These
are small acknowledgments that the project's thinking has moved
and the earlier artifacts are honest about when they were written.

## What comes next

A companion page of academic references and related work is being
prepared: sources, lineage, and the disagreements the essay
engages with. Whether it will be published is not decided yet;
the work is worth doing regardless, as a study and learning
instrument for myself.

Beyond that, the essay produced more than I can absorb at once.
The theory brings lessons that will impact the next steps of the
framework and the methodology: generator versioning, the
test-suite's role repriced, how decomposition interacts with spec
tree structure, what resists the drift toward prompt. Digesting
those lessons into concrete plans is work that has not been done
yet. The instrument exists. Using it on the project's
own roadmap is what follows.

And the most interesting use may be turning it back on the
rationale's destination. The vocabulary now exists to ask — not
promise, ask — how far that destination is, what stands in the
way, and what "within guardrails" actually requires of the
guardrails. That analysis is what the next phase of this project
is for.
