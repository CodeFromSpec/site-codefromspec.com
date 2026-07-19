---
title: "Part III — The description"
description: "What the description carries, what it leaves to the generator, and what it costs to keep."
date: 2026-07-18
---

# Part III — The description

In Part II we learned that a perfect description does not eliminate the need for the oracle, and a perfect
oracle does not eliminate the need for the description. Here we discuss what the description should be.

## What the generator supplies for free

![Part III — The description](/images/theory/20260718_image_part_III.png)

No description teaches its generator to program.

And that is the whole point of using LLMs for generating source code. An enormous share of what a
program has to get right arrives already resolved, for free, before the description says a single word.
This works because the prior is concentrated: silences do not resolve at random; they resolve toward whatever
training made common. The routine part of a program comes from the prior; the description only has to carry
what no training data could contain, the part that belongs to this project alone.

The dividing line inside the spec is conventional versus unconventional. Conventional intent can be left silent —
the prior already carries it. Unconventional intent has to be declared, explicitly and forcefully, because
silence invites the default. The clause that breaks a convention fights the prior on every draw; it is the
most expensive kind of clause to keep working, and the most valuable text in the spec.

So the description is no ordinary specification; it is a delta: what remains after subtracting, from
everything the program must get right, everything code in general already gets right. A system assembled
from conventions fits in a page; one dense with decisions no training data contains does not. The length tracks
how much real information the software carries beyond the average of all software.

A silence in the spec is not an omission; it is compression. What the prior already carries need not be
stated, for the same reason a compressed file omits what the algorithm can reconstruct. Most silences are
successful compression: the prior resolves them exactly as the author expected, and stating them would be
redundancy. An underspecification bug is a compression failure: the author assumed the prior carried
something it did not, and the draw resolved the silence against intent. A generator swap reprices
the silences where the two priors differ: what was safe under the old one becomes a bet on the new one.

## Source code is also a spec

The delta has an upper bound, and it is source code itself. Source code is a description in the limiting case:
it decides every dimension and leaves the generator nothing to resolve. No spec ever needs to carry more
information than the source it would produce.

The bound cuts both ways. A piece of software whose intent fights the prior everywhere probably belongs at the bound,
written directly as source code, because prose that determines everything still pays a model's error rate to
become code. And a spec that keeps growing toward the bound without that justification is turning into source code
written in markdown. Natural-language description earns its keep strictly below the bound: wide enough to take
the prior's free resolutions, narrow enough to state what must not vary.

Which makes the bound an escape hatch, not a rival. A project that works at the level of the description does
not choose between spec and source code. Where a piece demands full determination, the spec the workflow
needs is the source code itself, kept as such. The two levels combine within a single program, and a spec-driven
workflow that cannot say "this part is decided literally" is missing the top of its own range.

## Diminishing returns

A second reason not to specify everything arrives before the bound does: each clause buys less than the one
before it. A clause is worth how often the generator would break it if left unstated, times what the break
would cost. A clause against a mistake the generator never makes is inert, however sensible it reads. Clauses
overlap, and any competent spec states the common deviations first, so every later clause guards a rarer,
thinner slice.

Meanwhile the cost per clause stays flat or rises. The rarer the deviation a clause guards, the harder it is
to think of in advance. Additionally, every clause needs revision if intent moves. Falling value against cost
gives the stopping rule: add clauses while the next one's value exceeds its cost of writing and carrying.
Where intent has settled, that point arrives later; where intent is still being discovered, it arrives early.

The stopping point also moves with what an undetected failure costs. Where a failure costs an afternoon of
cleanup, most rare deviations are not worth a clause; where it costs a plane or a misdosed patient, the same
arithmetic justifies specifying far into the rare cases.

## Smaller is not cheaper

It is tempting to assume that the goal is to minimize the spec's size, but the decision between spec and
source is not about which one is shorter. A spec shorter than its source can still lose, if nothing catches
what its silences resolve wrong. A spec longer than its source can still win, because the edits that matter
later happen at the right level. And keeping the source directly means it must be actually written, by an
author who may lack skills the generator has.

The cost is not settled at writing time: a description is written once and worked with for the life of what
it describes. Intent changes arrive as edits to it, verification runs against its silences, every handover
and every generator swap reads it fresh. What decides is the cost of authoring plus verifying plus evolving,
not characters saved.

## The cost of authoring

Authoring has entered every account so far as a single price. It bundles three costs. Deciding what the system is:
the abstractions, the trade-offs, the resolutions intent does not hand over ready-made. Knowing the prior: a
spec is compressed against a dictionary, and judging which silences are safe is knowledge of the dictionary —
earned by use, repriced whenever the generator changes. And wording it. Cheap generation collapsed the third
cost and left the other two standing. A generator drafts prose fluently, so it can draft a spec — and what it
drafts resolves from its prior, which is the conventional part, the part that barely needed stating. The
delta is by definition what no training data contains, so it is the one part of the description the generator cannot
supply. The spec worth writing is the part that cannot be generated.

The cost hides well, because it does not scale the way checking does. A verdict is owed on every draw; a
decision, once made, aims every draw that follows. So as volume grows the whole bill reads as a checking
bill, and the cost of deciding amortizes out of sight. It is still paid — on every clause that fights the prior,
every trade-off no training data resolves, every re-decision when intent moves — and nothing about cheap drawing has
made deciding any cheaper.

## The generator is not part of the description

The generator shapes every draw so strongly that it is natural to think of it as part of the description.

A description can be violated; a generator cannot. "This sample violates the spec" names something an oracle
can check. "This sample violates the generator" names nothing. The description is a predicate; membership is
checkable without asking who produced the sample. The generator is a tendency, where the draw was likely to
land. Likelihood and conformance are therefore independent: a sample can conform and be unlikely, or be likely
and nonconforming, and no amount of likelihood adds up to acceptability. Correlation between the two is
manufactured, and the manufacturing is what aiming is.

Both description and generator narrow the space, but toward different targets. The generator narrows toward
what code in general has been, the description toward what this project means.

## The description is the delta between intent and the generator

A spec is written for an audience. What order the clauses come in, how forcefully each is worded, what
needs no mention at all: every one of those choices is made against a reader's prior. So the subtraction that
defines the delta is not against code in general; it is against the prior of the generator that will actually
read it. The generator is still not part of the description's content, but it is the addressee of the
description's form. The form is most of the text — portable in principle, the way source code is, yet
still written against a platform.

The oldest proof is the handover: the spec a new team inherits reads incomplete. For the prior of the team
that wrote it, it was complete; the silences were free for readers who already carried what was left unsaid.
The intent did not change; the audience did. A team swap is a generator swap, and the handover is where the
delta gets measured.

That dependence invites an objection: if swapping the generator changes the spec, are they really two
separate things? They are. A spec is compressed against the prior the way a file is compressed against a
dictionary. What the dictionary already carries is what the encoding may leave unsaid. Swap dictionaries and
every byte of the compressed form changes; the document it decompresses to does not. Two specs tuned to two
different priors can share no sentences and still define the same region, because the identity lives at the
level of what is meant, not of what is written. A generator swap reprices which decisions need stating and
which silences are safe, while the decisions themselves hold still.

Ratification, accordingly, has two modes. Some fixes narrow the region, recording a decision the project had
not made yet. Others only re-aim the encoding, rewording a clause the generator keeps missing. The same file
receives two different kinds of edit: one changes what the project means, the other changes how it is said
to this generator.

## Versioning the generator

The handover from team to team also happens between models, every time the generator in use changes. Teams
never versioned the engineer, but a model's identity is trivial to record. Declare it in the project's
versioned configuration, perhaps even control it per generated artifact, and treat any change to the
declaration as a real event, not an emergency. A generator's influence is exerted entirely at the draw,
so nothing already shipped can break. What reprices is everything ahead: a swap touches no decision and no test,
but it reprices the description's form, and rewriting that form is real work that scales with how far the
priors differ.

From inside one incident, a swap's misses look like ordinary underspecification. What distinguishes the swap
is correlation: the silences where the priors differ all reprice at once, and the misses concentrate where
no ratified check waits, because the checks were fitted to the old prior's habits. The declaration makes that
signal fire in a diff someone reviews, instead of overnight in nobody's changelog. And it is not only for
regenerating workflows: every edit made with the description in hand is made against its declared generator.

The declaration buys less than a lockfile teaches you to expect, in three ways. A pinned compiler makes the
binary reproducible; a pinned model still samples, so the pin stabilizes the distribution, not the output.
The name is not the model: a provider can revise what serves behind an unchanged identifier (a bugfix, an
optimization, a security improvement) announcing nothing and renaming nothing. Against that the declaration
is blind, and only the oracles can notice. And the pin is a lease: providers retire versions on their own
schedule, so the swap is postponed, not prevented.

## The spec converges toward a prompt

A description in use sits under a pressure: the generation loop only polices content that changes what gets
generated. A clause that fights the prior rots visibly (the next generation contradicts it), while a
rationale that is behaviorally inert rots in silence. The incentive follows the policing. A spec is written
until the generator gets it right, and stops there, because "good enough for the model" is where the loop
stops hurting, and "good for the team" has no oracle. Left alone, the description converges toward an
excellent prompt, precise where the generator needs precision and increasingly illegible as a theory of the
system for its human readers.

The pressure is not unopposed — onboarding, debugging, and audit all reward legibility — but it is continuous
where they are episodic, and the document drifts toward whoever reads it most often.

That cuts against the hope pinned on spec-as-source workflows. Making the spec the source closes the gap
between spec and code, and reopens it inside the spec, between the document the generator needs and the
theory the team needs.

## The authored form and the delivered form

Nothing in the apparatus requires the spec to be presented to the generator as written. The description a
human edits and the description the generator receives can be different forms of the same content:
restructured, annotated, assembled with the artifact alongside it in whatever arrangement reduces anchoring
and raises the hit rate. The transformation does not alter what the description says; it adapts how it is
said to the generator that will read it. This is not a convenience; it is a design surface. How the spec is
presented is part of how it aims, and an adapter between the authored form and the delivered form is
infrastructure of the same kind as the compiler that transforms source into the machine's input without
changing what the program means.

## The artifact is not disposable

A description defines a region, never a point. Every draw resolves the silences the description left open,
and those resolutions are recorded nowhere but in the artifact: the description says what may vary; the
artifact says what, this time, was chosen.

That is why the artifact is not a disposable output of the spec. Discard it, and the project keeps its
boundaries but loses its position: every resolution nobody ratified is re-decided by the next draw, and the
verdicts the point accumulated (tests passed, traffic served, incidents survived) are spent with nothing to
show for it. A third loss is paid by people rather than the project: a local move obligates confirmation of what it
changed; a resample obligates confirmation of everything, and where the verdict is human, that is a full
reading instead of a targeted diff. So the strategy of discarding the artifact and regenerating
from the spec on every change rests on a false premise: that the description is the whole record. It never
was. It holds the decisions, while the artifact holds the resolutions and the evidence. Regenerate on every
change and the second half is repurchased, draw after draw.

The evidence is subtler than a list of passed tests, as it attaches only to the dimensions the environment
actually exercised. A program's other defects sit dormant: present in the code, never hit, harmless only
as long as the code does not change. A resample draws those dormant defects fresh, and re-decides every
ambiguity the last draw resolved well without anyone noticing — resolutions that worked partly by luck.
Whatever was written down survives; whatever was luck may not, and no one knows how much of a working
system is which. The artifact is the only place that unwritten luck is kept.

So the discard is not forbidden; it is graded, and the grade is set by the oracles: the more of the region
they close mechanically, the less unwritten luck a resample can lose. At one end sits the settled, mechanically
closed component — an enumerable domain under exhaustive test vectors — where regeneration is a legitimate
strategy, because nothing unwritten is left to bet. Short of that corner, no combination of spec and test
suite earns the discard: its own completeness is unverifiable from inside, so "safe to regenerate" stays a
bet that the unchecked dimensions do not matter, never a fact. There, regenerating from scratch is a forced
exception rather than a strategy, and what forces it is anchoring — the point capturing the draw instead of
informing it — and even then the first response is to re-arm the description, not to discard the artifact.

## What spec-driven development optimizes

Each piece of the software faces one decision: at which level between spec and source code does it live?
The answer is an optimization, not an allegiance. Where the cost of authoring, verifying, and evolving a
spec is lower than writing and maintaining the source directly, the piece lives as spec and the source is
generated. Where it is not, the piece lives as source code, and nothing is lost by saying so. The two
levels combine within a single project, and a workflow that cannot hold both is missing part of its own
range. The balance depends on the generator in use, so a swap reprices it; and it moves as intent settles,
so the question is re-asked over a piece's life, not answered once.

The discipline has one endemic risk: the generation loop polices what the generator needs and neglects what
its human readers need, so the spec drifts toward an excellent prompt and away from a legible theory of the
system. Resisting that drift is not a side concern; it is most of what keeping a spec alive costs.

Spec-driven development (SDD) is the discipline of keeping three things aligned, piece by piece: the description
that carries the decisions, the artifact that carries the resolutions and the evidence, and the declared
generator that names the audience both were written for. Working at the spec level does not license
discarding the artifact; working at the source level does not license discarding the description.

The name invites a misreading: spec-driven development sounds like the old dream finally coming true — teams
at last writing the specifications everyone always said they should, decades of under-documentation solved
by tooling. It is nothing of the sort. What made specification expensive has not changed, and the 'spec' of
spec-driven development is not the document that dream was about: it is addressed to a generator, tuned to
its prior, and pulled by the loop's incentives away from its human readers, not toward them. Spec-driven
development is an engineering technique for extracting reliable software from a stochastic generator; the
spec is the instrument, not the point.

[← Part II](/theory/part-ii) · [Table of contents](/theory) · [Part IV →](/theory/part-iv)
