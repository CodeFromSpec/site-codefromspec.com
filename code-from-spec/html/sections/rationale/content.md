---
title: Rationale
description: "Why Code from Spec exists — what killed specifications twice, what changed, and where this leads: a platform team for the whole company"
---

![Rationale](/images/banner_rationale.png)

# Rationale

> The change is not in how code is written. It is in
> whose knowledge reaches the software, and by what
> path. AI is the enabler, not the point.

Building software with an AI agent at my side is not
the problem this project exists to solve — plenty of
engineers, myself included, already do that well
enough. Code from Spec is a
test tube for a different question: whether the gap
between the people who know what the software should
do and the people who can make it real can be
narrowed — and by how much. The disruptive force of AI
is already being felt in how organizations produce
software; the CEO vibe-coding a prototype is its
crudest early form. This methodology is my attempt to
bring structure and engineering to that
transformation, rather than watch it arrive without
them.

## The knowledge that never reaches the software

Every organization that builds software is full of
people who know what the software should do. The
compliance officer knows the regulations better than
any engineer will. The accountant knows the
reconciliation rules. The product manager knows the
users. None of them can put that knowledge into the
software directly. The only path runs through the
engineering team: explain what you need, wait, and
hope the translation was faithful.

It rarely is — not because engineers are careless, but
because translation loses information by nature. The
expert explains; the engineer interprets; the
interpretation becomes code the expert cannot read.
The gaps stay invisible until they surface months
later as a failed audit, a reconciliation that does
not balance, or an edge case nobody handled because
nobody knew it existed outside one person's head.

This is not a vision or a prediction. It is the daily
condition of every company that builds software, and
the cost of it is paid continuously, mostly without
being noticed.

## What AI changed, and what it did not

AI was supposed to close this gap. Code generation is
within everyone's reach: describe what you want, and
the agent writes the code. For small tasks this works.
For anything an organization depends on, it does not —
because a domain expert who asks an agent to build
something receives output they cannot evaluate. The
code appears to work, and they accept it, because the
alternative requires expertise they do not have. The
agent makes mistakes, invents behavior, and faithfully
implements ambiguous requests in ways the requester
never intended.

So the gap is not closed. It moved. The expert no
longer depends on an engineer to write the code, but
still depends on someone to tell them whether the code
is right. Participation without the ability to
evaluate is not participation: it is delegation with
extra steps.

What AI did change — genuinely, measurably — is the
economics of one specific activity: deriving code from
a written specification. That change is narrower than
the hype and more important than it looks, and the
rest of this page is about what it makes possible.

## Why specifications failed before

Capturing knowledge in structured artifacts before
writing code is not a new idea — it has failed at
least twice.

Formal methods in the 1970s and 1980s — structured
analysis, Z notation, VDM — pursued precision. The
research was rigorous. Adoption never followed, and
part of the reason was that the notations solved
precision but not participation: the compliance
officer was no closer to verifying a Z schema than to
verifying the code. The knowledge still had to pass
through a translator.

Then CASE tools in the 1990s and Model-Driven
Architecture in the 2000s pursued generation: draw the
model, generate the code. Anyone who lived through
that wave will recognize this site's premise — and
reach for the same objection. The generated code was
edited by hand, the model and the code drifted apart
immediately, and it was the model that got abandoned.
The round-trip problem was never solved; "the model is
the source of truth" became a slogan describing
nothing.

Both waves died of the same disease, and it was
economic, not conceptual. Keeping a specification — or
a model — in sync with evolving code required constant
manual effort. The artifact drifted from the
implementation, the team stopped trusting it, and the
cost exceeded the benefit. Specifications survived
only where regulators forced the issue — aviation,
medical devices — because there an external force pays
the cost of rigor. Everywhere else, the cheapest
process wins, and the cheapest process is
code-and-fix.

## Why the economics changed

Generating code from a specification is cheap. And
when the code is derived from the spec, the spec
cannot drift: it is the source, not a parallel
document to maintain. The
round-trip problem that killed CASE and MDA is closed
by construction, not by discipline: generated code is
never edited by hand, divergence in either direction
is detected mechanically, and regeneration is cheap
enough that the spec never loses the contest with the
code. What was a slogan in 2005 is a reality now.

And because the agent works from structured natural
language, the specification no longer needs a notation
that excludes the people whose knowledge it captures.
The artifact that drives the software becomes an
artifact the organization can read.

## The destination

Every large software organization already contains a
working example of where this is going. A platform
team — a small, senior group — builds the
infrastructure that the much larger population of
product developers ships on: the golden paths, the
deploy pipelines, the guardrails, the runbooks.
Product developers do not know how the cluster is
configured and do not need to. They work inside rails
built by people who do, and the rails are what make
their autonomy safe. Nobody calls this a dream. It is
standard practice, with well-understood economics.

The destination is that relationship, one level up: a
lean tech team becomes the platform team for the whole
company. The engineers build the scaffolding, and the
domain experts contribute within it, to the part they
actually know: that a duplicate cancellation returns
success, that an overdue invoice moves to a specific
status, which fee applies to which kind of account.

The change is that the expert contributes to the
artifact itself, instead of to an intermediary's
understanding of what she meant. The compliance
officer still does not touch the constraint that makes
her rule safe under concurrent execution; that is
enforced around her rather than expected of her — the
same way a product developer's deploy is safe because
of a pipeline someone else hardened.

## What happens to the engineer

The engineer does more engineering, not less. When
domain experts contribute content directly, the
engineering team's work shifts from absorbing and
translating other people's knowledge to designing the
system that makes their knowledge reliably become
software: architecture, constraints, failure modes,
the guardrails that hold everything else up. That is
the work the title always implied — and it is,
precisely, platform work.

This mirrors what happened to hardware. In the 1960s,
building anything electronic meant designing your own
circuits. Today you take a microcontroller from a
catalog and a sensor off the shelf. The components
that once demanded teams of engineers became
commodities — and the specialized expertise did not
lose its value. It moved upstream, into the companies
that design the components everyone else builds with.
Software is at the same point. AI commoditizes code
generation, the equivalent of buying parts off the
shelf. Building from commodity parts still requires
knowing what to build and how the pieces fit, and that
judgment about system behavior, security, and failure
modes is not something AI replaces or domain experts
acquire. The point was never to remove it. It was to
stop using it as a gate that everyone else's knowledge
must pass through.

## What remains unsolved

Natural language is ambiguous. This is the historical
reason specifications used formal notation, and
choosing natural language for the sake of
participation reopens it. The working answer is
convergence: specs become more precise through
iteration, each cycle narrowing the room for
interpretation, with test specs anchoring meaning
mechanically. Whether this converges fast enough for
domains complex enough is being learned in practice,
not assumed.

Generation is not deterministic. The same spec can
produce different code across runs. Verification
against test specs bounds the variation in behavior,
but the guarantees are about what the software does,
not about the code that does it — which couples
reliability to the discipline of behavioral test
coverage. Where that coverage is thin, the assurance
is thin, and nothing in the framework hides it.

And the destination is exactly that — a
destination, not a position. If this were a mission
to colonize Mars, the honest status report
would read: we have the rocket engine almost figured
out; we have absolutely no idea how to build the
habitats the colonists will live in; but we will
probably manage to get off Earth. The engine is the
generation machinery — spec trees, confinement,
staleness by hash, behavioral tests — built and in
daily use. The habitat is the accountant contributing
a spec and trusting what happens next, and no one
should read this page as claiming that distance has
been crossed. What remains is the work of making
participation reliable, and that is what Code from
Spec is for.
