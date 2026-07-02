# Collapsing the wave function

## A spec does not define a program

A specification read by a non-deterministic executor does not
define a program. It defines a space of programs — every
implementation that satisfies what the spec says, differing in
everything the spec leaves open. Variable names, decomposition into
helpers, the choice between two standard-library functions, the
handling of an edge case the spec never mentions: each silence is a
degree of freedom, and a spec of any useful size has many.

This is not a defect to be engineered away. A spec that closed
every degree of freedom would be as long as the code and readable
by no one; leaving room for the executor's competence is what makes
a spec shorter than the thing it specifies. But it has a
consequence that the discourse around spec-driven development
consistently misses: **run the same spec through the same model
twice, and you get two different programs.** Both conforming. Both
correct, as far as the spec can say. Not the same.

## Generation is a measurement

Borrow the physics image for a moment: before generation, the spec
is a superposition of all its conforming implementations. Running
the generator collapses it — one concrete artifact comes out, with
every degree of freedom resolved one particular way. This function
got this name. That ambiguity got that reading. The edge case the
spec never mentioned got handled, or did not.

The artifact, in other words, is not just output. It is the
**record of the collapse** — the only record of which point in the
space of possible programs you are actually standing on. That
information exists nowhere else. It is not in the spec, which
defines the space, not the point. It is not in the model, which
holds no memory of what it generated. It is not reconstructible,
because the generator is stochastic: run it again and you do not
get your branch back, you get a new one.

This reframes what regenerating from scratch does. It is not a
clean build. It is a fresh sample from the space — every resolved
ambiguity re-rolled, every silent choice re-decided, to fix
whatever single thing prompted the regeneration. Tests bound the
damage, but only for tested behavior; everything the tests do not
pin is re-decided by a process with no memory of what worked last
time. Discarding the artifact does not discard stale output. It
discards the knowledge of which program you had.

## The pair

Follow the logic one step further and it arrives somewhere we did
not expect when we started.

We opened this site with the sentence: *specifications are the
source of truth, and code is a generated artifact.* We wrote it,
and it is how we began. It is also incomplete, and the
incompleteness is exactly the collapse. The spec is the source of **authority** —
where it speaks, it wins, every time, and nothing in the artifact
can override it. But the full truth about the system is not held by
the spec alone. It is held by the pair: the spec, which says what
must be true, and the artifact, which records how everything the
spec delegated was resolved.

Neither half can reconstruct the other. From the artifact you
cannot recover the spec — the intent, the constraints, the reasons
are not in the code, which is why code is a poor context source.
And from the spec you cannot recover the artifact — only the space
it came from. The spec without the artifact has authority over no
program in particular. The artifact without the spec is a program
with no account of what it is supposed to do. The unit that must be
versioned, preserved, and kept in agreement is the pair.

Where the spec speaks, the spec wins. Where the spec is silent, the
artifact is the memory of the resolution. Those are different kinds
of truth, and a methodology needs both.

And this division is not only philosophy — we had already met its
failure mode and given it a name. [Anchoring](/articles/anchoring-on-old-code)
is what happens when the artifact's memory oversteps: the spec
changes, but the code keeps answering for decisions the spec now
speaks to. The most important of the hypotheses we offered there —
that the existing artifact silently resolves the ambiguities the
agent should re-judge — is this exact mechanism, read as pathology.
The property that makes the artifact worth keeping is the same
property that makes it dangerous, which is why the boundary of its
authority cannot be left to the agent's judgment. The
[disposition](/articles/telling-the-agent-what-changed) is that
boundary, drawn mechanically: `unchanged` means the spec is still
silent here and the artifact's memory stands; `changed` means the
spec has spoken and the memory loses its vote. Anchoring was never
a mysterious misbehavior. It was the pair's two kinds of truth left
without a fence between them.

## The alternative that does not exist

There is an apparent way out, and it deserves to be named so it can
be dismissed. If the generator were deterministic, the artifact
really would be disposable — regenerating would reproduce it
bit-for-bit, the way a compiler reproduces a binary. So the
tempting move: pin the model version, fix the configuration, treat
model-plus-settings as part of the versioned toolchain, and
disposability comes back.

The move fails before it starts, because pinning does not produce a
function. A pinned model is still a sampler. On average it behaves
predictably; in the detail — which reading of an ambiguous
sentence, which of two reasonable decompositions, which handling of
the case the spec never mentioned — it varies between runs, same
model, same settings, same spec. The variation is not a
configuration error to be tuned away. It is what the thing is.
Determinism was never on the table, and a toolchain that cannot
reproduce its own output cannot carry the branch information for
you.

And even if it could, it is not yours to pin. What serves under a
model name can change on the provider's schedule, not yours, and
the names themselves retire. A compiler you can vendor and build
from source in ten years; a hosted model you cannot vendor at all.

The accounting is simple: the information of which branch you are
on has to live somewhere, and the generator cannot hold it — not
because of who owns it, but because of what it is. The artifact is
the only place the branch can live. Versioning it is not a
preference. It is the only option that exists.

## What the tooling already knew

Here is what convinced us the framing is right rather than merely
poetic: the tooling had already arrived at it, mechanically, before
we had the words.

The manifest — the file that tracks every generated artifact —
records exactly two fingerprints per artifact: the chain hash, the
state of the spec that produced it, and the checksum, the state of
the file that came out. One line per artifact, and the line is the
pair.

And the staleness report detects divergence in both directions.
When the spec moves and the artifact stays, the artifact is
**stale**. When the artifact moves and the spec stays — someone
edited generated code by hand — it is **modified**. Two names, one
disease: the pair fell out of agreement. The framework never
assumed that drift only flows one way, because at the level of
hashes there is no privileged half — there is a recorded pairing,
and it either still holds or it does not.

Regeneration, in this frame, is the operation that restores
agreement while destroying as little of the collapse as possible.
The [disposition mechanism](/articles/telling-the-agent-what-changed)
tells the agent which parts of the spec moved; the existing
artifact preserves every resolution the change did not touch; the
result is a new pair, minimally different from the old one. A spec
diff becomes an artifact diff. The branch survives the correction.

## Derived, not disposable

None of this demotes the spec or promotes the code to
independence. The artifact remains downstream: generated, never
hand-edited, corrected only through the spec that governs it. What
changes is the reason you keep it. You do not keep generated code
because regenerating is expensive, or because diffs are convenient
for review — those are real but secondary. You keep it because it
knows something the spec does not: which of the many programs your
spec permits is the one that has been running, passing tests, and
surviving production.

The spec defines the wave function. The artifact is the collapse.
You need the first to know what may be true, and the second to
know what is — and a methodology built on a stochastic generator
has to hold on to both, because no one else is holding either.
