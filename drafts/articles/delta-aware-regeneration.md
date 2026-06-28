# Delta-Aware Regeneration

## The problem with regeneration today

When an artifact becomes stale, the generation subagent
receives two things: the current spec chain and the
existing artifact. The chain is the source of truth. The
existing artifact is a reference — a starting point for
minimal changes.

The subagent must figure out what changed. It has the
full chain and the full artifact, and somewhere between
them is a difference that matters. But the subagent does
not know where to look. It must read the entire chain,
read the entire artifact, and infer which parts of the
spec are new, which changed, and which stayed the same.

This is the setup that produces anchoring. The existing
artifact is concrete, tested, familiar-looking code. The
spec is natural language — potentially ambiguous,
definitely longer to parse. When the two compete for the
subagent's attention, the artifact often wins. The
subagent checks whether the code contradicts the spec
rather than regenerating from the spec. A subtle change
in wording, a new constraint added three ancestors up, a
field renamed in a dependency's interface — these are
easy to miss when the code in front of you already looks
right.

We documented this problem in "Anchoring on old code."
The failure mode is silent: the hash updates, the file
is written, the staleness system reports clean. But the
change never happened. The spec moved; the code did not.

## What the subagent is missing

The subagent is missing a directional signal. It knows
where it needs to end up (the current chain) and where
it started (the existing artifact), but it does not know
what changed between the last generation and now. It
must reconstruct that difference by reading — the same
task a human reviewer performs when reading a PR without
the diff.

Nobody reviews code without a diff. The diff is what
makes review tractable: it focuses attention on what
changed and lets everything else fade into background.
Without it, the reviewer must hold the entire before and
after in mind and compare them point by point. This is
exactly what we ask the subagent to do.

## The delta

Delta-aware regeneration gives the subagent the diff.
When an artifact is stale, the tooling computes exactly
which chain positions changed since the last generation,
and delivers the changes alongside the full chain.

The subagent receives three things instead of two:

1. **The current chain** — the full spec, as before.
2. **The delta** — which positions changed, what was
   added, what was removed, and for changed positions,
   the before and after content.
3. **The existing artifact** — the starting point, as
   before.

The delta is the directional signal. It tells the
subagent: "the spec changed here, here, and here.
Everything else is the same." The subagent no longer
needs to discover the difference — the framework
computes it and delivers it.

## How it works

The mechanism is a cache that stores the content of each
chain position at the time of generation. When the
tooling writes an artifact, it records not just the chain
hash but the individual content hashes of every position
in the chain. The actual content behind each hash is
stored in a content-addressed cache.

When the artifact later becomes stale, the tooling
compares the old positions against the current ones:

- **Changed** — same position, different content. The old
  content is in the cache; the new content is the current
  file. The tooling can present the diff.
- **Added** — a new dependency, a new ancestor section.
  The position did not exist in the previous generation.
- **Removed** — a dependency was dropped, a section was
  deleted. The position existed before but is absent now.
- **Unchanged** — same position, same content hash.
  Nothing to report.

The cache is best-effort infrastructure. It is gitignored
and can be lost without breaking anything. Without the
cache, the subagent receives the full chain and the
existing artifact — exactly the same behavior as today.
The delta is an enhancement, not a requirement.

## What this changes about anchoring

Anchoring happens when the subagent has no clear signal
about what changed and falls back to preserving what
exists. The delta changes the economics of that decision.

Without a delta, the subagent faces a diffuse task:
"here is the spec, here is the code, make them match."
The existing code is a strong attractor because it is
concrete and internally consistent. The spec change is
a weak signal buried in a long document.

With a delta, the task becomes focused: "these specific
parts of the spec changed. Here is what they said before,
here is what they say now. Update the code accordingly."
The spec change is no longer a weak signal — it is the
primary input. The existing code is still present as a
reference, but it no longer needs to be the subagent's
map for understanding what happened.

This does not eliminate anchoring. The subagent may still
preserve old patterns when the delta implies they should
change. But the failure mode shifts from "the subagent
didn't notice the change" to "the subagent noticed the
change and didn't apply it correctly" — a narrower, more
diagnosable problem.

## The connection to cascade regeneration

When a root node changes, every descendant becomes stale.
Most of these are cascade staleness — the target node's
own spec did not change, only an ancestor's did. The
delta makes this visible: the subagent sees that only an
ancestor position changed, and the target's own Public
and Agent sections are unchanged.

This is useful information. A change to a shared
convention may or may not affect a given artifact. The
delta lets the subagent — and the human reviewing the
generation — understand the scope of the change without
reading the entire chain.

In the future, this could enable smarter regeneration
strategies: cascade-only changes with small deltas might
be handled differently from changes where the target
node's own spec was rewritten. But even without strategy
optimization, the delta makes every regeneration more
informed.

## Not a replacement for confinement

The delta does not weaken confinement. The subagent still
sees only the chain — it does not explore the filesystem
or read unrelated files. The delta is derived entirely
from the chain: it is the difference between the chain
that produced the last generation and the chain that
exists now. It adds information the framework already
had but was not delivering.

The chain remains the source of truth. The delta is a
lens that highlights what moved.
