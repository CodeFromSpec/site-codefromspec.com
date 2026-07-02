# The immune system

## The net that was always there

In spec-driven generation, the spec defines what the software
should do, the agent generates code from it, confinement keeps
the agent on the chain, staleness detection says when to
regenerate. All of this can be correct, and the generated code
can still be wrong. Tests are what catch that.

That much is the usual claim, and it is true. But stated this
way it makes tests sound like a new requirement the methodology
introduces — a last line of defense bolted on because the agent
is untrustworthy. That framing is backwards, and getting it
right is the whole point of this article.

Tests were never the *last* line. They were always the
*second*. In ordinary development there are two nets under every
behavior, not one. The first is the author's judgment, set into
the code itself: the defensive check added after a 2 a.m.
incident, the deliberate ordering that respects a lock nobody
documented, the edge case handled because the author had been
burned by it before. None of that is in any spec. It lives in
the code because the person who wrote the code put it there, and
it protects every behavior it touches whether or not a test
exercises that behavior. The second net is the test suite, which
catches what the author got wrong.

Spec-driven generation does not add a net. It removes the first
one. The author is human; the executor is the model; and the
model deposits no judgment into the code, because the code is
only whatever the chain produced this time. Every behavior that
the author's instinct used to protect for free is now protected
by exactly one thing, or by nothing. The test was the second net
yesterday. Today it is the only net, and so it has to hold the
entire weight that two nets used to share.

This is why "the cost of a missing test is higher here" is too
weak. The honest statement is harder: **you were always this
dependent on tests. The author's judgment was quietly paying
part of the bill, and the methodology has stopped the subsidy.**
A team with mediocre tests and good developers shipped reliable
software for decades and credited the tests, or the luck, or the
process. They were being subsidized by judgment embodied in
code they never had to write down. Generation removes the payer
and presents the invoice. Nothing about the bill is new. Only
the payer is gone.

## Two kinds of immunity

Calling tests an immune system is the right metaphor, but the
usual telling uses only half of it. A real immune system has two
arms, and the methodology has both.

**Innate immunity** is the part you are born with. Barriers and
generic responders, always on, present by construction, costing
no per-threat effort. They do not learn and do not need to: they
catch broad classes of harm the moment it appears. In a generated
codebase this is the type system, the schema constraints, the
compiler — and the linters and static analyzers beside them,
catching unchecked errors and suspicious patterns with no
per-behavior effort. A `CHECK(amount > 0)` rejects a zero-value transfer in
the database, deterministically, on every generation, with no
test authored and no model consulted. A `UNIQUE` index enforces
idempotency whether or not the agent remembered it. A strong type
makes an entire category of mistake unexpressible before any test
runs. Money stored as `int64` cents cannot silently become a
float. None of this depends on the model. It is fast, generic,
and free, and it is the same on every regeneration because it is
not generated — it is the ground the generation stands on.

**Adaptive immunity** is the part that must be trained. It learns
a specific pathogen, builds a specific response, and remembers
it. It is slow to acquire and has to be paid for one antigen at a
time, but it reaches what the innate system cannot. This is the
test spec. It is authored for a specific behavior — "a request
with amount zero returns an invalid request error," "a paid
boleto's timestamp carries the Brasil offset" — and it catches
the specific thing the type system and the schema let through.

The two arms divide the surface of the system between them, and
the division is precise: **adaptive immunity is mandatory exactly
where innate immunity is silent.** Where the truth lives in an
executable type, the innate system holds it and no test is
strictly required to keep it true. Where the truth lives in a
string with a convention in a comment — a `TEXT` column annotated
`-- DD-MM-YYYY`, a date format named in prose three nodes up the
chain — the innate system sees nothing, because to the database
it is just text and to the compiler it is just a string. There,
the test spec is the *only* antibody. If it is not written,
nothing stands.

This gives the coverage question a concrete shape. The surface
that adaptive immunity must cover is not "everything." It is the
weak-type seams: every place where a real type was not available
or not used, and a convention carries meaning the executors
cannot enforce. Count those seams and you have counted where a
test case is load-bearing rather than merely nice. That count is
finite, locatable, and auditable — and it is the honest measure
of how much test discipline a given system actually demands.

## What adaptive immunity is for

The failures the test spec exists to catch all share one
property: under generation, they are silent. The code compiles,
the manifest reads up to date, the staleness system reports
clean. Each is the gap between what the spec meant and what the
agent produced.

**Hallucination** — the agent invents behavior the spec never
asked for. A validation appears, a parameter gains a conversion,
a "helpful" optimization changes semantics. Without a test that
pins the expected behavior, the invention ships as a feature.

**Omission** — the agent skips something the spec requires. A
status transition is missing, a field goes unpopulated, an error
path is absent. The happy path works; the omission is invisible
until a case exercises it.

**Misinterpretation** — the spec says one thing and the agent
understands another. A date validated in the wrong format, an
amount handled as a string where integer arithmetic was meant.
The agent's reading is internally consistent. It simply is not
what the spec meant.

**[Anchoring](/articles/anchoring-on-old-code)** — the agent sees
the previous generation and preserves its behavior instead of
applying the spec change. The spec moved; the code did not; the
hash updated anyway. [Telling the agent what
changed](/articles/telling-the-agent-what-changed) makes this
rarer; only a test that asserts the new behavior proves it did
not happen.

These are adaptive-immunity targets specifically because the
innate system cannot see them. A type cannot tell that the
business rule was supposed to change. A constraint cannot tell
that the agent invented a validation. For these, the antibody has
to be authored, because the threat is defined at the level of
meaning, and meaning is exactly what the deterministic layers do
not check.

## A passing test only means something if the test is independent

Here is the part the comfortable version of this argument skips,
and it is the part that decides whether the immune system works
at all.

In ordinary development, a test means something because a human
wrote it. The developer wrote the code, and a *different* act of
judgment — sometimes a different person — wrote the test, and the
agreement between them is evidence precisely because the two came
from independent sources. The test is a second opinion.

In spec-driven generation, the implementation and the test are
both derived from the same spec tree, by the same model, through
the same confined process. If they agree, that is not
automatically a second opinion. It can be the same opinion
rendered twice. When a spec is silent on some point, the model
resolves the silence — and it may resolve it the same wrong way
in the implementation and in the test, because the same priors
are doing the resolving on both sides. The implementation does
the wrong thing; the test asserts the wrong thing; they match;
the suite is green; the behavior is wrong. The manifest will call
this up to date, and it will be telling the truth — the code did
come from this chain. Currency is not correctness, and a green
suite of correlated tests is not verification.

So the adaptive immune system has a precondition the innate one
does not: **the test spec must be a genuinely independent
articulation of intent, not a restatement of the implementation
spec.** The way to earn that independence is to write acceptance
criteria as their own account of correct behavior — input and
expected output, in the domain's terms — rather than as a
paraphrase of the implementation's mechanism. A test that says
"insert this row, expect this field populated with these values"
is independent of the implementation's three SQL queries and two
maps. A test that merely re-narrates those queries is not; it
will agree with the implementation by sharing its mistakes.

The structure does more than help here — it closes the channel
outright. In the spec tree, the section of a node where the
implementation mechanism lives is not inheritable and not
importable: no other node can see it. A test node depends on the
implementation's public contract and receives exactly that — the
interface, never the how — and the implementation's generated
source is not in the test's chain either. The agent generating a
test could not peek at the implementation if it tried. The
blindness is not requested of it; it is what the chain contains.
Two confined passes, each structurally blind to the other, produce
two independent readings of the same intent. And regeneration
preserves this: each agent is shown its own previous output, never
the other artifact, so the channel stays closed across
regenerations too. The conventions that keep all of this in place
are documented in the framework's
[TESTING.md](https://github.com/CodeFromSpec/framework/blob/main/docs/TESTING.md).

What structure cannot supply is the content half of independence.
Two blind readings are only as independent as the texts they read:
if the test spec is a paraphrase of the implementation spec's
mechanism, the two agents will converge on the same mistake with
no channel between them needed. So a team that wants the immune
system to mean something still has to spend on acceptance criteria
that say what correct *is*, independently of how the code achieves
it. That spending is not a tax the methodology adds. It is the
irreducible cost of a second opinion, which is the only thing that
ever made a test worth trusting.

## Tests discover spec gaps

When a test fails after regeneration, the instinct is to fix the
code. The discipline is the opposite: trace the failure to the
spec. Every failure has one of three roots — the spec is
ambiguous and the agent read it one way, the spec is incomplete
and the agent lacked something, or the spec is clear and the
agent erred anyway. The first two are fixed in the spec; the
third is regenerated, and if it recurs, made explicit in the
node's instructions. In all three the spec ends up more precise,
and a more precise spec moves a delegation out of the model's
weights and into the written half where it can be reviewed. The
test failure is not a bug to patch. It is a question the spec had
left unanswered, now forced into the open.

This is the convergence loop, and it has a direction. Each
closure shrinks the unwritten half of the spec — the part
silently delegated to the model — and grows the written half,
toward the core of shared competence no spec should have to
state. The tests are the engine that drives it, because they are
what turn a silent delegation into a loud failure with an
address.

## Coverage that survives

Because the code does not persist across regenerations and the
author's judgment is not in it, the adaptive immune system is the
institutional memory. That has two consequences teams routinely
get wrong.

A fix must go into the spec and the test spec, never into the
generated code. A patch in the artifact survives until the next
regeneration; a rule in the spec survives every regeneration.
When a production incident reveals an edge case, the impulse to
add a defensive line to the code is the wrong reflex — that line
will be overwritten. The edge case has to be captured where it
will inform every future generation, which means the spec and a
test that pins it.

And when specs are refactored — nodes moved, modules merged — the
test cases must migrate with the logic they verify. A behavior
that had an antibody in the old structure and lacks one in the
new structure is a regression waiting for the next generation to
introduce. The rule is mechanical: when logic moves, its tests
move; when modules merge, their tests merge; the count of
behavioral assertions after a refactor is at least the count
before. Adaptive immunity is acquired memory, and acquired memory
that is dropped in a reorganization is immunity lost.

## Not a proof, and sharper than usual

A passing suite means every tested behavior is correct under the
tested conditions. It says nothing about untested behavior — and
under generation this gap is sharper than in hand-written code.
Untested behavior is held in place by nothing but the
[stability of the artifact](/articles/many-worlds): regenerate
minimally and it persists as unexamined luck; regenerate from
scratch and it is re-decided by a process with no memory of what
worked last time. In hand-written code an untested behavior at
least has one stable implementation that someone reasoned about
once. In generated code it has whatever the last roll chose.

The response is the standard of safety-critical software, applied
for a reason that is structural rather than institutional. In
safety-critical work the distrust of the author is a policy. Here
it is a fact about the executor: the agent has no memory, no
accumulated instinct, and no stake in the outcome. The innate
immune system — types, schema, compiler — covers what it can for
free. Everything past it is the adaptive system's to cover, one
authored antibody per behavior that matters, with the coverage
target set not by lines of code but by the weak-type seams where
nothing else is watching.

This is the same fact seen from two sides. The methodology did
not invent a need for tests. It removed the author's judgment
that used to let teams under-test and not notice, and in doing so
it made visible how much of every system's reliability was
always riding on a second opinion that, in too many codebases,
was never actually written down. The immune system was always the
thing keeping the software alive. Generation is simply the first
executor honest enough to admit it cannot survive without one.