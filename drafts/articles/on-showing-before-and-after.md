# On Showing Before and After

## The problem regeneration creates

When an artifact becomes stale, the agent regenerates it from
the current spec chain. On regeneration it also receives the
existing artifact — the file it produced last time — as a
reference, so it can make minimal changes and keep the
unchanged parts stable. This is the one deliberate exception to
generating from the chain alone, and it earns its place: it
keeps diffs small, keeps review tractable, and lets a codebase
accumulate the confidence that comes from code that has not
changed.

But it also creates a task we have written about before. The
agent now has the full chain and the full artifact, and
somewhere between them is a difference that matters. It does
not know where. It must read the entire chain, read the entire
artifact, and infer which parts of the spec are new, which
changed, and which stayed the same.

This is the setup that produces anchoring. The existing
artifact is concrete, tested, familiar-looking code. The spec
is natural language — longer to parse, sometimes ambiguous.
When the two compete for attention, the code often wins. The
agent checks whether the code contradicts the spec rather than
regenerating from the spec, and a subtle change — a reworded
constraint, a new rule three ancestors up, a renamed field in a
dependency — is easy to miss when the code in front of you
already looks right. The failure is silent: the hash updates,
the file is written, the staleness system reports clean. The
spec moved; the code did not.

## The diff we almost built

The agent is missing a directional signal. It knows where it
needs to end up and where it started, but not what changed
between them. Nobody reviews code this way. The diff is what
makes review tractable — it focuses attention on what moved and
lets everything else fade. Without it, a reviewer has to hold
the entire before and after in mind and compare them point by
point. That is exactly what we were asking the agent to do.

So the natural fix is to give the agent the diff. The mechanism
is a cache that stores the content of each chain position at
generation time. When the artifact later goes stale, the
tooling compares the old positions against the current ones:

- **Changed** — same position, different content. The old
  content is in the cache, the new content is current; the
  tooling can present both.
- **Added** — a position that did not exist last time.
- **Removed** — a position that existed before and is now gone.
- **Unchanged** — same content hash, nothing to report.

The cache is best-effort and gitignored; without it,
regeneration falls back to the chain and the existing artifact,
exactly as before. The delta is an enhancement, not a
requirement. We described this design in full, and it is sound.
We were close to building it.

## Where it broke down

It broke down on a question we could not answer well: what
format should the diff take?

Unified diff reads cleanly for code and lies about prose. A
one-word change in a paragraph shows up as the whole line
leaving and re-entering; reflowing a paragraph with no semantic
change can look like a change. Word-level diff fixes prose and
reads strangely in a function signature. Every representation
we considered was good for one kind of content and bad for
another — and our specs are a mixture of prose and structure,
sometimes within a single section. Worse, every option assumed
the agent is good at decoding that particular notation, and we
had no evidence for that. Reading prose and noticing a
contradiction is something models do constantly. Correctly
interpreting a unified diff is a narrow, much less exercised
skill. We were about to commit the framework to computing a
diff in a format we weren't sure the agent could read, to solve
a problem the diff might not be the best tool for.

## What a model is actually good at

The way out came from looking at what the agent does well,
rather than what we could compute for it.

A language model reads a sequence and interprets each part in
light of what came before. As the sequence advances, later
content revises, refines, or supersedes earlier content —
because that is how the text it learned from behaves.
Conversations work this way. Essays work this way. A revision
to a document comes after the thing it revises. The model was
trained on a world where position in the sequence carries
meaning, and where the most recent statement on a subject is
usually the operative one.

This means the agent is already good at a specific task: read
one version of something, then read another, and notice where
they disagree. It does not need the difference pre-computed and
marked up. It needs the two versions, in the right order, and
it will find the difference by reading — the same faculty it
uses to notice when a later turn in a conversation contradicts
an earlier one.

So we stopped computing the diff. On regeneration, we show the
agent the specification as it was when the existing artifact was
generated, then the existing artifact, then the specification as
it is now. Three blocks, in time order. The agent reads the old
spec, reads the code that spec produced, then reads the new spec
— and the new spec, arriving last, reads as the correction. We
are not asking the agent to apply a diff. We are asking it to
read a short story whose ending revises its beginning, which is a
thing it does without being asked. (What we actually put in the
"before" is narrower than the whole previous spec — more on that
below — but the shape of the idea is this.)

## Why this is safe to lean on

There is an obvious objection, and we have raised it against
ourselves before. Our methodology is built on not trusting the
agent. Confinement exists because the agent hallucinates,
misreads, and invents context that looks like diligence. We
trust the chain, the tests, and the hash — not the model's
judgment. So why is it acceptable to lean on the model's
reading to find what changed?

The answer is in what happens when the reading fails. The
current spec is delivered in full, and it is authoritative on
its own. If the agent compares the two versions perfectly, it
generates the right code. If it compares them poorly — misses a
change, misreads the difference — it still generates from the
current spec, because the current spec is right there,
complete, and last in the sequence. The comparison is not
load-bearing. It is a lens that sharpens attention on what
moved; it is not the source of truth about where to end up.

This is the line that makes leaning on the model defensible. We
do not let the model's reading determine correctness — the
current spec determines that. We let it determine *where to
look*, and a failure to look in the right place degrades to
"generated correctly from the full spec without the benefit of
focus," not to "generated wrong." It is the same instinct as
the rest of the framework: arrange things so the failure mode
is survivable. A computed delta, had we made it the agent's
primary input, would have been load-bearing — if the agent
misread the diff, it would have had nothing else to reason
from. Showing both full versions keeps the floor underneath the
agent at all times.

## The order is the argument

Once the decision is to show both versions, the order stops
being a layout detail and becomes the whole mechanism.

The blocks arrive in the sequence in which they became true:
the previous constraints, the previous implementation guidance,
the existing artifact those produced, then the current
constraints, the current guidance, and finally the material to
transform. Each block has authority over the ones before it,
because it comes after them — the way a later message in a
conversation has the last word. The existing artifact sits in
the middle: valid up to the point it appears, then redefined by
the current spec that arrives after it.

This ordering is the concrete form of a fix we had previously
only hypothesized. In writing about anchoring, we noticed that
the existing artifact used to come *after* the spec, and that
recency might be part of why the code won the competition for
attention. We suggested, without proof, that reversing the
order — spec last — might help. The regeneration envelope is
where that hypothesis becomes the design. The current spec is
now the most recent strong signal the agent sees before it
generates. The old code no longer gets the last word.

## Only what moved

There is a version of this that would reintroduce the problem it
solves. If we showed the entire previous spec — every position,
changed or not — alongside the entire current spec, the agent
would face a wall of old material, most of it identical to what
sits below it. That is the dilution risk in its purest form: the
unchanged majority drowning the changed few, and a second full
copy of the spec competing for attention with the authoritative
one.

So we show only what moved. Every position in the current spec
is tagged with what happened to it since the last generation —
unchanged, changed, or added — a label the tooling computes by
comparing content hashes, without reading or interpreting
anything. That tag travels on the current spec, where the agent
generates from, so the signal sits in the block it already has
to read. The previous block then carries nothing but the old
content of the positions that actually moved: the ones that
changed, and the ones that were removed. Positions that did not
change leave no trace in the previous block at all — their
"unchanged" tag on the current spec already says everything
there is to say about them. The before is not a second copy of
the spec; it is only the old text of what is no longer current.

That tag is the directional signal we set out to build, reached
from the other side. The delta-aware design wanted to tell the
agent "the spec changed here, here, and here." The disposition
says exactly that — but as a one-word label per position, derived
from a hash comparison, rather than a rendered diff the agent has
to decode. The expensive, fragile half of the delta — showing
*how* a position changed, in some notation — is the half we did
not build. The cheap, robust half — saying *which* positions
changed — turned out to cost almost nothing, and it is in.

## What we kept

We gave up exhaustiveness, but only at the level of fine detail.
The disposition is exhaustive about which positions moved — a
hash comparison cannot miss one. What is not guaranteed is that
the agent, reading a changed position against its current
counterpart, notices every difference within it; on a large
position with a tiny change, it may not. We accept this because
the cost is bounded: the current spec is authoritative
regardless, so an unnoticed difference at worst means the agent
did not get the focus it could have had — not that it generated
against the wrong spec.

What we kept is the cache. The same content store that would have
fed a rendered diff is what lets the tooling reconstruct each
previous position and compute each disposition, comparing the
hashes it recorded at the last generation against the current
ones. Nothing new had to be built for any of it — the machinery
that stores position content at generation time was already
there. And the half of the delta we did defer, showing how a
single position changed down to the word, remains available: the
old content of every changed position is on disk. We can layer
that finer signal on top of the disposition later, if testing
asks for it, without changing the envelope's shape.

This also keeps a smaller benefit the delta promised: visibility
into cascade regeneration. When a root node changes, every
descendant goes stale, but most are cascade staleness — the
target's own spec did not change, only an ancestor's did. The
disposition makes this explicit: the target's own positions are
marked unchanged and only an ancestor's is marked changed. The
agent learns the scope of the change from the labels, without
having to infer it.

## Not a weakening of confinement

Showing the previous spec does not loosen confinement. The
agent still sees only the chain — it does not explore the
filesystem or read unrelated files. The previous spec is
derived entirely from the chain: it is the chain that produced
the last generation, reconstructed from the cache. It adds no
new source of authority. The current chain remains the source
of truth; the previous one is history shown alongside it, and
history has no vote.

## The pattern underneath

There is a habit in this methodology worth naming, because this
is the third time we have followed it. Context management came
from not building a retrieval system — the tree already put the
right constraints in front of each generation, so we let
structure do what machinery would have done badly. The manifest
came from not making the agent write metadata into files — the
tooling already knew the hash, so we moved the bookkeeping out
of the agent's way. And now the difference between two
generations comes from not computing a diff — the model already
reads sequences and notices change, so we let it.

Each time, the move is the same: find the place where we were
about to build a mechanism, and ask whether something already
present — the structure, the tooling, or the model's own
faculties — was going to do the job anyway. The first two times
the answer was structure. This time it is a capability of the
model, which is a more delicate thing to depend on, and we
leaned on it only because we could arrange for its failure to be
harmless. The principle is not "trust the model." It is "don't
build what is already there" — and then, separately and
carefully, "make sure that when the thing you didn't build
fails, nothing breaks."

## What the test has to tell us

This is a design, not a result. We have not yet run enough
regenerations through it to know whether reading-the-difference
holds up. Two failure modes are worth watching, because they
pull in opposite directions and have opposite remedies.

The first is the one we designed against: anchoring, the agent
preserving old behavior the current spec has changed. If it
still happens, the question is whether the order helped at all,
and whether the finer, within-position delta is needed after
all.

The second is the one the design still risks, though filtering
blunts it: dilution. We place the previous block ahead of the
current spec, and although it carries content only for the
positions that moved, a regeneration that changes many positions
still puts a substantial body of old spec ahead of the
authoritative one. The order is meant to protect against this —
the current spec is last, where attention is strongest — but it
is the same positional hypothesis, and it could fail in the other
direction: not the old code winning, but the volume of old
material drowning the new spec. Filtering keeps this small when
little changed; it offers less protection when a lot did.

The two are distinguishable in the failures they produce, and
they have to be told apart, because the fix for one is not the
fix for the other. Anchoring asks for a stronger signal about
what changed — which is where the finer, within-position delta
would return. Dilution asks for less old material still —
carrying the disposition on the current spec without delivering
the old content at all, leaving the agent to work from the
current spec and the change labels alone. The remedies pull in
opposite directions, which is why the failures have to be told
apart before either is applied. Watching which one appears, if
either does, is how this design earns its place or gets revised.