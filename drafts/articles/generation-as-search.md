# Journal seed — Generation as search

The unifying frame: AI-assisted development is search.
The question that separates methodologies is not the
algorithm — it is **where the search state lives**.

## The two objects

Every search has two objects, and conflating them is the
defining pathology of standard AI-assisted development:

- **The region** — the space of programs that satisfy the
  intent. Defined by the spec. Authored, versioned,
  legible.
- **The point** — the concrete artifact currently on
  disk. One sample from the region, every degree of
  freedom resolved one particular way.

From the point you cannot recover the region: the
artifact does not distinguish intentional constraint from
sampling accident. From the region you cannot recover the
point: the spec defines the space, not the sample.
(= the pair from *Collapsing the wave function*, restated.)

## The two movements

- **Move/shrink the region** = refine the spec. Each
  added constraint narrows the space around the optimum.
- **Transport the point** = generate/regenerate. First
  generation is the landing; diff-driven evolution is
  local search in the neighborhood where the point
  landed.

Standard AI dev and Code from Spec run the *same* loop —
prompt → code, iterate with signal (compiler, tests,
human eye). Same algorithm. Different state persistence:

- Standard: region knowledge lives in the conversation
  (dies with the session), in heads (leaves with people),
  and *in the code itself* — where point and region
  become one object. Intent and accident become
  indistinguishable. Anchoring is this conflation named:
  the artifact doubling as region-definition without
  authority. Fear of refactoring is its symptom.
- CFS: region persists in the spec; point persists in
  the artifact; the manifest line (chain hash, checksum)
  is literally the pair (region, point) — **the complete
  state of a search**, one line per artifact. The tooling
  knew before the theory, again.

## Regeneration = transport + projection − drift

The non-surgical diff decomposes into three components:

1. **Transport** — the requested delta.
2. **Projection** — the agent, holding the full predicate
   in context, pulls an out-of-region point back inside
   (fixes a bug it noticed while applying the delta).
   Projected-gradient steps. Desirable; only possible
   because the whole spec is in the chain.
3. **Lateral drift** — unrequested movement *within* the
   valid region: re-rolled resolutions the spec never
   pinned. The *Many worlds* risk.

The framework, seen this way, is a calibration: the spec
enables (2), the disposition suppresses (3), (1) is the
work. Standard dev takes **unprojected steps** — (1) and
(3) only, never (2): drift has no restoring force and
accumulates.

## Why keep the spec? The oracle, not the re-run

Weakest justification: repeating the search (rare;
*Many worlds* argues against). Real justification: the
spec is the **membership oracle**. Every future
maintenance step is a membership query — "which
perturbations of this point remain valid?" — and it is
unanswerable from the point alone. Two oracles:

- Spec: **complete but soft** (agent reads and judges,
  fallibly).
- Tests: **partial but hard** (mechanical, but only over
  pinned behavior).

### Tests are spec — the question is which kind

Prose spec and test spec are **two descriptions of the
same region**, differing in nature and binding, not in
category. The prose spec is an *intensional* definition —
the region by rules — and binds at **generation**: it is
what the sampler reads to aim inside the region (soft
oracle). The test spec is an *extensional* definition —
boundary points with verdicts — and binds at
**verification**: it executes against the point and
accepts or rejects (hard oracle, partial by nature).
"Sanity check" names the *moment* tests act, not their
nature.

Feathers ("legacy code is code without tests") and
Finster ("with good acceptance tests I can rewrite your
system in another stack, no drama; code is implementation
detail") assert: a sufficiently dense extensional
definition suffices to accept any conforming point. True
— and it is literally CFS regeneration — with the *Many
worlds* clause: "no drama" holds exactly to the extent
the tests pinned what matters; everything unenumerated is
re-rolled in the rewrite. Characterization tests are the
inverse move: reconstructing an extensional definition
*from the point* when the intensional one was lost.

Why CFS keeps both descriptions instead of tests-only
(Finster) or spec-only (full trust in the LLM):
1. **Generation** — extensional definitions are a poor
   generative medium: a sampler guided only by boundary
   points searches by trial, expensive and
   underdetermined between pins. The sampler needs the
   rule, not the list.
2. **Coverage** — part of the region is not pinnable by
   execution (guard-node content: security policy,
   conventions, architecture). Intensional content with
   no viable extensional counterpart.
3. **Legibility** — the rule in prose is what the domain
   expert reads.
   And spec-only means 100% trust in the soft oracle — the
   fallible reader. **The two-description architecture is
   forced by the untrusted sampler: one description to aim,
   one to check.**

Crucial emendation to "tests refine the spec": if the
test spec were *derived* from the prose spec, agreement
between implementation and test would be one opinion
rendered twice (The immune system). The two descriptions
must be **independent articulations of the same intent**
for their agreement to constitute evidence. So: tests are
spec (same category, different binding) *and* the second
independent line (by requirement, not accident); "sanity
check" only names when their binding fires.

Boundary note: property-based testing is the hard oracle
reaching toward intensional content — rules checked
mechanically — which is why it is the natural mitigation
of the gap between the two descriptions.

### Aim vs. hit

The plain-language version: the spec is taking aim; tests
are checking whether the shot hit. Tests-only is dumb
search — the aim is what reduces the space from infinite
to tractable; without it, almost every sampled point
misses and the hard oracle becomes an expensive filter
over noise. Spec-only is trusting a shooter with
imperfect aim. Both descriptions, because the shooter
(the LLM) is cheap and unreliable.

Consequence for feedback (revises the compiler-feedback
seed's original position): a test failure IS a verdict
from the region — same category as a compiler error, so
the "tests are a different kind of thing" objection
falls. What survives the category argument is the triage
gate, because a test failure is an **ambiguous verdict
between two authored descriptions**: either the point is
outside the region (agent erred → feedback is legitimate
projection) or the two descriptions disagree at that
point (spec gap or test bug → the disagreement is the
convergence loop's food, and auto-repair would silently
consume it by moving the point into the intersection).
Plus Goodhart: pins-as-repair-targets invite hardcoding,
special-casing, amputation — repair must be projection
onto the intersection (failure + full prose chain), never
pin-chasing. Accounting: a post-feedback green is
conformity at that pin, not independent concordance;
first-pass green remains the metric.

Staleness, reread: the chain hash does not detect an
invalid point; it detects that **the region moved under
the point** and conservatively assumes the point may have
been left outside. Drift sources: human hand (modified),
prior hallucination (point born outside), spec change
(region moved).

**Legacy code = a point whose region was lost.** All
legacy maintenance is the permanent payment for the
irreversibility point→region: membership queries answered
by archaeology (reading code, interviewing veterans,
breaking production to find the boundary). Keeping the
spec is refusing to enter that regime.

## Spec fix vs. code fix: reclassification vs. relocation

- A code fix **moves the point**. The defective point
  remains *inside* the valid region — it was never
  invalid — so nothing prevents a future sample from
  returning to it. This is the exact reason "the next
  regeneration will overwrite the fix": regeneration
  samples/projects onto the region, and the patched point
  was never a distinguished element of it.
- A spec fix **moves the boundary**: the defective point
  is reclassified as outside — unsamplable, for every
  future generation, forever. Code fix removes an
  instance; spec fix removes a **class**.
- Precision for *Many worlds*' claim: "one bug leaves the
  set and cannot return" holds **iff the fix moved the
  region**. Point-only fixes can return on any re-roll.
- Why spec AND test: the spec moves the soft oracle's
  boundary; the test records the exclusion in the hard
  oracle. Without the test, the new boundary exists but
  nothing checks it mechanically.
- Honesty ("blind — well, not quite"): a human code fix
  is not blind; the human has a region *in their head*
  and navigates it. The failure is not blindness — it is
  that the head-region does not persist or propagate.
  Back to the master theme: same search, different state
  location.

### The no-manual-fix rule, restated

"The next regeneration will overwrite the fix" was the
from-scratch-era justification, and minimal regeneration
weakens it: a manual fix in the artifact can *survive*
the next diff — by anchoring, i.e. by luck (the spec is
silent there, so the agent preserves it). The honest
justification migrates from **loss** to **unaccounted
divergence**: a manual fix moves the point with no region
bookkeeping. Its survival is unstable (any regeneration
that re-rolls that neighborhood may silently drop it —
and the drop is *projection working*, the agent pulling
the point back to what the region dictates); the bug's
class stays samplable (nothing stops it from being reborn
in a sibling node or a future generation); and membership
was never checked (the fix may have moved the point
outside the region, and the region does not know). It is
unratified luck stored in the point, invisible to both
oracles. The tooling partially knew: a hand edit yields
`modified`, and `load_chain` refuses to regenerate a
modified artifact — the framework never implemented
"overwrite"; it implemented **quarantine of the diverged
pair**. Only the doctrine's phrasing was behind the
mechanism.

## Falsifiable prediction (the baseline experiment)

If both processes are the same search with different
memory architectures, then on **one-shot tasks CFS has no
advantage** — it should lose, paying the cost of writing
the region a throwaway conversation would have resolved.
Divergence appears only where search memory amortizes:
the same system modified over months, by more than one
person, across turnover. This dictates the design of the
comparison against disciplined agentic coding — and
predicts the false negative of the wrong design (short,
solo project: tie or CFS loss).

Honest corollary: the point/region conflation is *cheap*,
and for most software (prototypes, scripts, anything that
dies in three months) it is the rational choice. The map
is only worth its cost when there will be future
expeditions. This *derives* the anti-use-cases instead of
listing them.

## Translations of existing concepts

- Disposition = local move instead of random restart.
- Convergence loop = every meaning-level signal must
  become region movement, not just point movement.
- First-pass success rate (see compiler-feedback seed) =
  region concentration metric: how shrunk the region is
  when *any* sample of it is acceptable.
- Participation = widening who can see and push the
  region's boundary — the region is the only one of the
  two objects legible to a domain expert; the expert
  never touches the point.

## Connections

- Collapsing the wave function: the pair, formalized as
  (region, point).
- Many worlds: drift, defect sets, the corrected
  monotonicity claim.
- Anchoring / disposition: the conflation and its fence.
- The immune system: the two oracles.
- Compiler-feedback seed: verdicts as in-region search
  signal; first-pass rate.

## Candidate titles

- Generation as search
- The region and the point
- Where the search state lives