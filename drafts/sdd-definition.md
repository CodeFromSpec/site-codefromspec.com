# Spec-driven development, as we define it

> The term "spec-driven development" is circulating with as many
> meanings as there are tools selling it. This page states what we
> mean by it — as premises you must accept and obligations that
> follow from them. It is a working definition: we expect to refine
> it as practice teaches us more, and this page will change when it
> does.

## The premises

Three facts, not choices. They are properties of the executor that
exists, and a definition that ignores any of them is describing a
different technology.

**The executor is a sampler, not a function.** Run the same
specification through the same model twice and you get two
different programs — both conforming, neither the same. This
cannot be configured away: there is no seed to fix, the provider
can change what serves under a model name at any moment, and even
a pinned model varies in the detail — which reading of an
ambiguous sentence, which of two reasonable decompositions.
Every generation of programming language before this one raised
the level of abstraction while keeping the translator
deterministic. This is the first whose translator is not, and most
of what follows, follows from that.

**A specification in natural language defines a space of programs,
not a program.** Every silence is a degree of freedom, and a spec
of useful size has many — deliberately, because closing every
degree of freedom would make the spec as long as the code and
readable by no one. Generation collapses the space into one
concrete program, with every open choice resolved some particular
way.

**The model is an unversionable input.** Half of what determines
the output lives in the model's weights: everything the spec did
not say, resolved by the executor's judgment. That half cannot be
hashed, cannot be diffed, and changes on the provider's schedule —
a model upgrade is a silent edit to every specification at once.

## The obligations

What any system honestly claiming spec-driven development with AI
is forced to do, given the premises. These are not features. They
are consequences.

**1. Version the pair.** The generated artifact records which of
the many permitted programs you are actually running — information
that exists nowhere else and cannot be reconstructed, only
resampled. The source of truth is the pair: the spec, which says
what must be true, and the artifact, which records how everything
the spec delegated was resolved. Discarding generated code is not
hygiene. It is amnesia.

**2. Authority flows one way.** Where the spec speaks, it wins —
every time, and divergence is corrected by regenerating from the
spec, never by editing the artifact. This is a rule about
location, not authorship: code a human must write goes *into* the
specification, where it is versioned, inherited, and survives
regeneration — not into the generated file, where it is drift.

**3. Concordance is sustained, and detected mechanically.** The
pair falls out of agreement in both directions: the spec moves
ahead of the code, or a hand edit moves the code away from the
spec. Both must be detected by machinery — hashes, not promises —
because agreement maintained by discipline is agreement that
decays. This obligation is the definition's boundary line:
spec-driven development *is* the activity of keeping the pair in
agreement over time. Stop paying that cost and you are not doing
a riskier variant of it. You have stopped doing it.

**4. Evolution proceeds by minimal delta.** A spec change becomes
an artifact change that preserves every resolution the change does
not touch. Regenerating from scratch re-rolls every silent choice
in the file to fix one — discarding the accumulated record of what
worked, re-deciding everything the tests do not pin. The loop is
the one that has always worked, for human developers and for
AI-assisted ones: current code plus a signal produces the next
code. What the definition adds is where the signal is recorded —
in the spec, durably — so the change has an address, not just an
effect. Same loop, different ledger.

**5. Verification is behavioral, and independent by construction.**
The hash proves the code came from the spec; nothing but tests
proves it does what the spec means — and under generation, tests
are the only sensor pointed at the half of the specification that
lives in the model. A generated test only counts as a second
opinion if it was generated blind to the implementation: same
intent, separate reading, no channel between them. Independence
requested of an agent is independence that erodes; it has to be
imposed by what the agent is given.

**6. Context is curated, not gathered.** The generating agent sees
context someone chose — the constraints that govern this artifact,
assembled deliberately — not whatever it could scavenge from the
repository. An executor that assembles its own context builds the
output on evidence nobody reviewed. What the agent sees must be an
input you author, version, and audit, like everything else that
determines the artifact.

## What this definition dissolves

**Spec-first is not spec-driven.** Writing a spec, generating
code, and abandoning the spec is a well-crafted prompt — nothing
more. The authority migrates to the code the moment the
implementation starts evolving without the spec, and a document
that no longer governs anything does not drive anything. "Driven"
means sustained direction. Spec-first is spec-started.

**Spec-anchored versus spec-as-source is a distinction that does
not distinguish.** In both, what is on disk is the same: the spec
and the code, versioned together, kept in agreement, both
indispensable. The difference is a rule about which half a human
may type into — governance, not architecture. The only version of
"the code is disposable" that would make spec-as-source a separate
category requires a deterministic translator, and premise one says
there is none. Under this definition both are simply spec-driven
development, practiced with different hand positions.

**"It will drift" is not a critique.** Drift is the name of the
state where the concordance cost stopped being paid — obligation
three abandoned. A spec-driven system that drifted permanently did
not reveal a flaw in spec-driven development. It stopped doing
spec-driven development, and kept the folder of markdown files.

## What stays free

The definition is deliberately silent about implementation. How
specifications are structured, how context is assembled for the
executor, how the delta is communicated on regeneration, how much
literal code the specs carry and where, which model runs the
generation, what the tooling looks like — these are design choices
where implementations can legitimately differ, compete, and
improve. Two systems that satisfy the obligations are both doing
spec-driven development, however differently they do it.

## Where Code from Spec stands

Code from Spec is one implementation of this definition: the spec
tree is its answer to context curation, the manifest and chain
hash its answer to mechanical concordance, disposition-guided
regeneration its answer to minimal delta, confined blind
generation its answer to independence. Those answers are described
across this site and are still being refined — the definition
above is the part we expect to survive their refinement.

We arrived at this definition backwards: not by deciding what
spec-driven development should mean, but by building it, watching
what broke, and keeping what the failures forced on us. Every
obligation on this page has a scar behind it. That is why we trust
it more than a definition written first.
