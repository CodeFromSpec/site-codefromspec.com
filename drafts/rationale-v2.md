# Rationale (v2 — restructured)

> The change is not in how code is written. It is in whose
> knowledge reaches the software, and by what path. AI is the
> enabler, not the point.

## The knowledge that never reaches the software

Every organization that builds software is full of people who know
what the software should do. The compliance officer knows the
regulations better than any engineer will. The accountant knows the
reconciliation rules. The product manager knows the users. None of
them can put that knowledge into the software directly. The only
path runs through the engineering team: explain what you need,
wait, and hope the translation was faithful.

It rarely is — not because engineers are careless, but because
translation loses information by nature. The expert explains; the
engineer interprets; the interpretation becomes code the expert
cannot read. The gaps stay invisible until they surface months
later as a failed audit, a reconciliation that does not balance, or
an edge case nobody handled because nobody knew it existed outside
one person's head.

This is not a vision or a prediction. It is the daily condition of
every company that builds software, and the cost of it is paid
continuously, mostly without being noticed.

## What AI changed, and what it did not

AI was supposed to close this gap. Code generation is within
everyone's reach: describe what you want, and the agent writes the
code. For small tasks this works. For anything an organization
depends on, it does not — because a domain expert who asks an agent
to build something receives output they cannot evaluate. The code
appears to work, and they accept it, because the alternative
requires expertise they do not have. The agent makes mistakes,
invents behavior, and faithfully implements ambiguous requests in
ways the requester never intended.

So the gap is not closed. It moved. The expert no longer depends on
an engineer to write the code, but still depends on someone to tell
them whether the code is right. Participation without the ability
to evaluate is not participation. It is delegation with extra
steps.

What AI did change — genuinely, measurably — is the economics of
one specific activity: deriving code from a written specification.
That change is narrower than the hype and more important than it
looks, and the rest of this page is about what it makes possible.

## Why specifications failed before

Capturing knowledge in structured artifacts before writing code is
not a new idea — it has failed at least twice.

Formal methods in the 1970s and 1980s — structured analysis, Z
notation, VDM — pursued precision. The research was rigorous.
Adoption never followed, and part of the reason was that the
notations solved precision but not participation: the compliance
officer was no closer to verifying a Z schema than to verifying
the code. The knowledge still had to pass through a translator.

Then CASE tools in the 1990s and Model-Driven Architecture in the
2000s pursued generation: draw the model, generate the code.
Anyone who lived through that wave will recognize this site's
premise — and reach for the same objection. The generated code was
edited by hand, the model and the code drifted apart immediately,
and it was the model that got abandoned. The round-trip problem
was never solved; "the model is the source of truth" became a
slogan describing nothing.

Both waves died of the same disease, and it was economic, not
conceptual. Keeping a specification — or a model — in sync with
evolving code required constant manual effort. The artifact
drifted from the implementation, the team stopped trusting it, and
the cost exceeded the benefit. Specifications survived only where
regulators forced the issue — aviation, medical devices — because
there an external force pays the cost of rigor. Everywhere else,
the cheapest process wins, and the cheapest process is
code-and-fix.

## Why the economics changed

AI changes both constraints at once, and this part is not a
forecast — it is how the methodology already operates today.

Generating code from a specification is cheap. And when the code is
derived from the spec — with tooling that detects, by hash, exactly
which artifacts a spec change makes stale — the spec cannot drift:
it is the source, not a parallel document to maintain. The
round-trip problem that killed CASE and MDA is closed by
construction, not by discipline: generated code is never edited by
hand, divergence in either direction is detected mechanically, and
regeneration is cheap enough that the spec never loses the contest
with the code. What was a slogan in 2005 is an enforced invariant
now.

And because the agent works from structured natural language, the
specification no longer needs a notation that excludes the people
whose knowledge it captures. The artifact that drives the software
becomes an artifact the organization can read.

This is the rationale, in the strict sense: the economic obstacle
was the reason specs failed for fifty years, the obstacle is gone,
and the machinery that replaces it — spec trees, confined
generation, staleness by hash, behavioral tests — exists and is in
use. What it is being used for today is engineering teams building
software from specs. What it points toward is the next section —
and the difference between the two matters.

## The north star

Here is where we are going. Not where we are — where we are is
engineers using this methodology on real projects, and non-technical
authorship remaining a direction. But a methodology needs a north,
and this one has a precise precedent for its north.

Every large software organization already contains a working
example of the relationship we are aiming for. A platform team — a
small, senior group — builds the infrastructure that the much
larger population of product developers ships on: the golden paths,
the deploy pipelines, the guardrails, the runbooks. Product
developers do not know how the cluster is configured and do not
need to. They work inside rails built by people who do, and the
rails are what make their autonomy safe. Nobody calls this a dream.
It is standard practice, with well-understood economics.

The north star is that relationship, one level up: a lean tech team
becomes the platform team for the whole company. The engineers
build the scaffolding — the spec tree's architecture, the guard
nodes that hold error handling and security policy, the test
conventions, the verification pipeline — and the domain experts
contribute within it, to the part they actually know: that a
duplicate cancellation returns success, that an overdue boleto
moves to a specific status, which fee applies to which kind of
account.

That knowledge used to reach the software through two translations
— to an analyst, then to a programmer — arriving as a third-hand
copy, sometimes revealed wrong only in production. Contributing to
the artifact instead of to someone's memory of what she said is the
change. The compliance officer still does not touch the constraint
that makes her rule safe under concurrent execution; that sits
above her in the tree, enforced around her rather than expected of
her — the same way a product developer's deploy is safe because of
a pipeline someone else hardened.

The analogy also says, honestly, what is different and harder. A
product developer can read the platform's code when the
abstraction leaks; a domain expert cannot read past the spec. That
is why the guardrails here have to do more work than a platform's
— why generation is confined, why constraints propagate down the
tree automatically, why the parts the expert cannot evaluate are
enforced rather than assumed. And it says what is open: how far a
domain contributor goes inside the rails before a need exceeds
what the scaffolding anticipated, and how gracefully the system
gives way when one does. That is being learned in practice, not
assumed.

## What happens to the engineer

The engineer does more engineering, not less. When domain experts
contribute content directly, the engineering team's work shifts
from absorbing and translating other people's knowledge to
designing the system that makes their knowledge reliably become
software: architecture, constraints, failure modes, the guardrails
that hold everything else up. That is the work the title always
implied — and it is, precisely, platform work.

This mirrors what happened to hardware. In the 1960s, building
anything electronic meant designing your own circuits. Today you
take a microcontroller from a catalog and a sensor off the shelf.
The components that once demanded teams of engineers became
commodities — and the specialized expertise did not lose its value.
It moved upstream, into the companies that design the components
everyone else builds with. Software is at the same point. AI
commoditizes code generation, the equivalent of buying parts off
the shelf. Building from commodity parts still requires knowing
what to build and how the pieces fit, and that judgment about
system behavior, security, and failure modes is not something AI
replaces or domain experts acquire. The point was never to remove
it. It was to stop using it as a gate that everyone else's
knowledge must pass through.

## What we have not solved

A rationale should be exact about its open problems.

Natural language is ambiguous. This is the historical reason
specifications used formal notation, and choosing natural language
for the sake of participation reopens it. The working answer is
convergence: specs become more precise through iteration, each
cycle narrowing the room for interpretation, with test specs
anchoring meaning mechanically. Whether this converges fast enough
for domains complex enough is being learned in practice, not
assumed.

Generation is not deterministic. The same spec can produce
different code across runs. Verification against test specs bounds
the variation in behavior, but the guarantees are about what the
software does, not about the code that does it — which couples
reliability to the discipline of behavioral test coverage. Where
that coverage is thin, the assurance is thin, and nothing in the
framework hides it.

And the north star is a north star. If this were a mission to
colonize Mars, the honest status report would read: we have the
rocket engine almost figured out; we have absolutely no idea how
to build the habitats the colonists will live in; but we will
probably manage to get off Earth. The engine is the generation
machinery — spec trees, confinement, staleness by hash, behavioral
tests — built and in daily use. The habitat is the accountant
contributing a spec and trusting what happens next, and no one
should read this page as claiming that distance has been crossed.
What we claim is narrower: the economics that killed every
previous attempt at specification-driven development have changed,
the engine runs, and the destination has a working precedent in
every company that runs a platform team. What remains is the work
of making participation reliable, and that is what Code from Spec
is for.
