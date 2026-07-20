---
title: Code from Spec
description: A software development methodology where specifications drive the code — developed in public on real projects, failures included.
updated: 2026-07-20
---

![Code from Spec](/images/banner.png)

# Code from Spec

A software engineering methodology for the age of AI —
developed on real projects, documented in public as it
is discovered and consolidated.

**Specifications drive the code.**

This is how I build software now. I write
specifications; an agent generates the code from them.
When a specification changes, the tooling detects which
code is stale, and that code is regenerated. The
specification is the thing I author, review, and
version; the code follows from it — generated when new,
minimally updated when the spec moves. I still read that
output — in diffs that minimal regeneration keeps
small — and the more a project matures, the less of it
I read. What I no longer do is write it.

The methodology is on its fifth version, developed by
using it to build its own tooling and in professional
work on real projects, under real deadlines. It has no
users other than me (well, perhaps a few unwilling
coworkers), and no comparison against a disciplined
baseline. What it has is a working
mechanism, a growing set of ideas that survived contact
with practice, and a public record of both, failures
included. If parts of it are useful to you, take them.

Staleness is detected
by hash. Generation is confined by the tooling. A hand
edit to a generated file is flagged the moment it
appears. Trust goes to the structure, not to anyone's
good behavior — and where the enforcement has a known
gap, the gap is documented, not passed off as a
guarantee. The sections below describe how that
enforcement works.

## The theory

Behind the methodology sits a
[theory of spec-driven development](/theory) — a
vocabulary for naming its parts: intent, description,
generator, oracle, artifact. It was written to ground
design decisions this project had been making by
instinct, and it is probably the most insightful thing
on this site. The sections below describe the mechanism;
the theory says why it is built this way — why a
specification defines a region of programs rather than
one program, why the artifact is not disposable, why
regeneration must show the agent what changed.

## The specification tree

Specifications are organized as a tree of nodes. Each
node is a directory with a `_node.md` file, and its
position in the filesystem is its position in the
hierarchy. High-level intent sits at the root;
implementation detail sits at the leaves. Only leaf
nodes generate artifacts.

A leaf node, in its entirety, can be this small:

```
---
output: internal/fees/calculation.go
---
# SPEC/payments/fees/calculation

# Public
## Interface
`func CalculateFee(amount int64, isPremium bool) (int64, error)`

# Agent
Fees are computed in integer cents — no floating point.
The standard fee is 2% of the amount, rounded down;
premium accounts pay half.
Reject non-positive amounts with a validation error.
```

A node inherits the public content of every ancestor,
automatically. A constraint written once at an ancestor
node — an error-handling rule, a convention, a type —
governs every leaf beneath it, including leaves added
months later. What a node needs from elsewhere in the
tree, it declares. The context for any generation is
the path from the root to the node plus what the node
declares — not searched for, not guessed. The structure
decides what the agent sees.

Specifications are structured natural language —
readable and reviewable without leaving the domain's
vocabulary. Specs live in git, reviewed in pull
requests, diffed, and blamed, with the same machinery a
team already uses for code. The aim, still untested, is
that someone who knows the domain but not programming
could sit on that review — a reviewer on the pull
request, saying "this is wrong" in one artifact
they can actually evaluate.

## Confinement

The agent that generates a file sees only that node's
chain — the inherited constraints, the declared
dependencies, the node's own specification — and writes
one declared output file. It does not browse the
repository, read neighboring code, or fetch anything
else. If the chain is not enough to generate the
artifact, the correct output is not code. It is a
report of exactly what is missing.

Confinement is enforced by the tooling, not requested
of the agent: the generation agent's tools are
restricted to reading its chain and writing its output.
An unconfined agent facing an ambiguous spec
compensates: it reads other files, infers patterns, and
builds output that satisfies a model it assembled from
context nobody chose. The result looks diligent but is
subtly wrong in ways that are hard to trace. A confined
agent has two options instead — generate from what it
has, or report the gap. The third option, inventing
context that looks like research, is the one
confinement removes.

## Staleness, by hash

Each generated artifact records the hash of the chain
that produced it. Change a specification, and the hash
of every artifact that inherited or depended on it no
longer matches. Those artifacts are stale, and stale is
the signal to regenerate. The bookkeeping lives in a
single manifest; generated files carry no framework
metadata and no comments, so a regeneration that
produces identical code produces no diff at all.

Divergence is caught in both directions: a spec that
moved ahead of its artifact marks the artifact stale,
and a generated file edited by hand is flagged as
modified. Either way, the pair fell out of agreement,
and the tooling says so.

The hash answers one question: was this artifact
produced from this specification. Whether the code is
correct is a separate question, for a separate layer.

## Verification

The methodology assumes an imperfect agent, because
that is the agent that exists. An agent can
hallucinate, omit a required step, misread an
unambiguous spec, or preserve old behavior the spec has
changed — and the code will compile and the manifest
will read clean. Tests catch these. Tests are
specifications too: authored, reviewed, and versioned
alongside the rest. The hash checks where the code came
from; the tests check what it does.

A green suite is evidence only if the tests are an
independent opinion — a generated implementation and a
generated test could otherwise share the same
misreading and agree on it. The structure works against
that: a test spec is its own account of expected
behavior, and the agent that generates a test receives
the component's public contract, not its
implementation — every node's internals stay out of the
rest of the tree's chains. Implementation and test
become two independent readings of the same intent.

In practice, the tests are where the confidence comes
from. The hash and the confinement bound where an error
can originate; the tests are what say the code does
what the spec meant — they are the layer I actually
trust when I ship. And they are the least codified part
of the methodology: the structure of a test spec is
settled, but deciding what to test, and how much, is
still judgment exercised case by case — instinct, not
yet rules. Extracting it into rules a tree can enforce
is open work.

## Where things stand

The spec tree format — how specifications are
structured, how context is assembled, how staleness is
detected — is approaching a definitive form. The newest
piece is the regeneration mechanism: telling the agent
exactly which parts of a spec changed, so it updates
the code without re-deciding everything that did not.
It is built, and early results are promising. The
[journal](/journal) reports this work as it happens,
including the hypotheses that failed.

Open problems remain. Natural language is ambiguous;
the working answer is convergence, with test specs
anchoring meaning mechanically — whether it converges
fast enough for complex domains is unproven. Generation
is non-deterministic; the guarantees are about
behavior, verified by tests, not about the generated
code itself. Authorship by someone with no software
background is the north star, not a current
capability — the [rationale](/rationale) describes that
destination as originally argued, and the
[theory](/theory) now gives the vocabulary to analyze
how far it is. The
tooling targets Claude Code; the spec format is
client-agnostic, the orchestration is not.

[Explore the framework on GitHub](https://github.com/CodeFromSpec/framework)

*Last updated July 20, 2026.*
