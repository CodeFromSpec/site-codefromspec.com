# Journal seed — Is the compiler cheating?

Question: during regeneration, is it legitimate to feed the
generation subagent compiler errors or test failures from its own
output? Or does that violate "generate from the chain only"?

## Position, restated through the model

The confusion dissolves once the compiler is named for what it
already is elsewhere on this site: an oracle, not a description.
Confinement exists to control **aim** — which sources of intent are
allowed to condition what the generator produces. A compiler error
carries zero intent. It is a **confirm** signal: a verdict, on one
narrow dimension (shape), about a sample that was already generated
from the chain. Feeding it back does not add a competing
description. It adds information about where the existing
description was already violated.

- **Compiler errors: always safe to feed back.** A language's
  grammar and type rules are a closed, wholesale, pre-paid oracle,
  argued at length under "The type system as a pre-paid oracle" —
  authored once by the language's designers, not by this project,
  and never independently re-authored by anyone this project could
  disagree with. That closure has a sharp consequence: a compiler
  verdict can never be ambiguous. There is no second, independently
  authored account of the language's grammar for the compiler's
  verdict to conflict with — the compiler *is* the grammar,
  executable. Every compiler failure is therefore unambiguous
  nonconformance in the sense "What a bug is" already gave the term:
  the shape dimension was already fully spoken for by the choice of
  language, and the sample missed it. Feeding the verdict back and
  retrying is not consulting a new source of intent. It is
  re-sampling with the cheapest possible directional signal against
  a dimension that was never in question. (Gaming mode to guard
  against even here: satisfying the verdict by amputation — deleting
  the offending functionality instead of fixing it.)
- **Test failures: the same signal category, minus the closure.** A
  test is not a closed, wholesale oracle — it is authored retail, by
  a person or an agent, independently of the implementation,
  precisely so it can catch what the implementation gets wrong. That
  same independence is what makes a test failure genuinely ambiguous
  in a way a compiler failure structurally cannot be: either the
  sample violated something the spec and the test agree on
  (nonconformance — safe to feed back, same as a compiler error), or
  the test and the spec disagree with each other at this point
  (underspecification, or a wrong test — the exact fork "Test, spec,
  or both" already names). Auto-repairing the second case without a
  human in the loop does not fix a bug. It moves the sample into
  whatever intersection of the two authored descriptions happens to
  satisfy both, and silently consumes a disagreement that should
  have become a spec fix. That is not "cheating" in the sense the
  question originally worried about — it is a **ratification
  failure**: a discovery that should have been written into the
  description or the test, discarded instead into an artifact nobody
  will re-derive.

## The line, derived rather than borrowed

The earlier framing reached for an immune-system metaphor — innate
versus adaptive — to justify treating compiler feedback differently
from test feedback. The model already gives a sharper,
non-metaphorical reason: a compiler is a **closed oracle**, authored
once, wholesale, with no independent second author who could
disagree with it. A test is an **open oracle**, authored retail,
independently, precisely so it *can* disagree — with the
implementation, and sometimes, when something has gone wrong
upstream, with the spec itself. Closed oracles cannot produce an
ambiguous verdict, because nothing else claims to speak for the
region they check. Open oracles can, because two independently
authored accounts of the same intent are exactly the material a real
disagreement is made of.

- **Closed oracles (compiler, linter, exhaustiveness checker):**
  feedback automatic and legitimate. Deterministic, intent-free,
  unambiguous. Bounded retries, always counted.
- **Open oracles (behavioral tests):** failure escalates to triage
  first. Only after the fork resolves to "spec and test agree, the
  agent erred anyway" does feeding the failure back become a
  same-category retry rather than a silent ratification failure.
  (Open question: is even this too permissive? Stricter position:
  test results never reach the agent directly; only spec fixes
  reach it, and the agent never sees its own prior failure.)

## The case against, and what it is actually afraid of

The strongest version of the objection is not about the compiler at
all — it is about **visibility**. An agent that silently
self-corrects against a closed oracle is fine; an agent that
silently self-corrects against an *open* one can hide the spec's
insufficiency forever. The ambiguity that caused the failure never
reaches a human, never becomes a ratified fix, and the artifact
converges on something that merely survived the retry loop rather
than something the description actually specifies. This is the same
failure "Many worlds" already named from a different angle:
everything ratified survives a resample; everything that only ever
passed by the luck of an unlogged retry is exactly the kind of luck
that does not.

## The case for, in the model's own terms

1. **Verdict versus aim.** Confinement excludes unauthored sources
   of *aim* — neighboring files, unrelated context, anything that
   could compete with the spec over what to build. A compiler's
   output is a mechanical function of (artifact, toolchain). It
   never says what to build. It says only what happened, which is
   confirm, not aim, and confinement was never a rule about confirm.
2. **The category already exists in the chain.** The existing
   artifact and the `previous_*` blocks documented under "Telling
   the agent what changed" are already machine-produced, unhashed
   context delivered with explicit, subordinate authority — history
   to inform, not to govern. A build verdict is the same category of
   thing: informative, never authoritative.
3. **Filter, not redefinition — aiming and confirming again.** The
   description defines a region; feeding back a compiler verdict
   does not move that region, because the compiler was never part of
   aiming it in the first place — it only ever confirms membership.
   Reading an unauthorized neighboring file would redefine the
   region with constraints nobody wrote, which is exactly the
   aim-contamination confinement exists to prevent. A verdict cannot
   contaminate a channel it never had access to.
4. **A cheap directional signal beats a coin re-flip.** "Many
   worlds" already named the alternative for what it is: resampling
   without the failure in context is asking a memoryless executor to
   "do it again but better," which is not even a long shot — the
   same distribution, sampled again, as likely to fail the same way
   as it ever was. A compiler error is the cheapest directional
   signal available, closer in kind to the disposition mechanism's
   cheap, mechanical delta than to a blind re-roll.

## The thesis that dissolves "cheating"

Feedback is not cheating. **Unmetered feedback is** — and only where
the oracle is open enough to disagree with the spec in the first
place. The claim genuinely at risk is "the description was
sufficient to produce this artifact." A retry against a closed
oracle never puts that claim at risk, because the closed oracle was
never part of what the description had to get right — the shape
dimension was already fully specified by the choice of language,
argued under "The type system as a pre-paid oracle." A retry against
an open oracle does put the claim at risk, which is exactly why it
needs the fork triage before it is allowed to happen silently.

Metering turns this into the mechanical measurement "Where the
crossover sits" and "Diminishing returns on aim" already wanted a
number for and never had one: first-pass compile rate is a direct,
cheap estimate of how much of the shape dimension's probability mass
the generator is missing on the first sample, before any retry loop
absorbs it. A rising retry count on a node is not noise to be
smoothed away. It is the alarm this page has been arguing all along
should exist — that some dimension's aim is in debt, and debt on a
dimension a closed oracle cannot fix by itself is debt only a spec
change can pay down.

## Design notes, reframed

- Feedback arrives as a **new, bounded dispatch** with the verdict
  as an explicit, declared input — not as conversation accumulating
  in one agent's context. This is confinement's own requirement,
  restated for a retry: every generation is a function of declared
  inputs, and a verdict fed back has to be one of them, not a side
  effect of letting an agent keep talking to itself. The alternative
  is not "cheating" either, but it stops being a guided sampler and
  becomes ordinary agentic debugging — a different, unconfined
  activity this methodology deliberately does not do.
- This retry loop is worth naming as its own operation, distinct
  from the four already given under "The primitives": not a fresh,
  independent sample, not a permanent local move against a persisted
  artifact, and not a ratification into the description or the
  oracle. It is a **bounded, ephemeral resample** — scoped to one
  generation event, guided by a closed oracle's verdict, that leaves
  no trace in the versioned half of anything once the event
  concludes — cheap precisely because it never touches aim.
- What the loop makes visible was always true and previously
  invisible: the toolchain was always an implicit dependency of
  every generation, "the artifact must compile under this compiler,"
  present in every chain whether or not anyone wrote it down.
  Surfacing it is a gain, not a leak.
- Retry counts recorded per node and reported per session — the raw
  material for the first-pass-rate metric above.

## Connections

- "The type system as a pre-paid oracle": why a compiler's verdict
  can never be ambiguous.
- "What a bug is": nonconformance versus underspecification, the
  same fork this page's triage gate depends on.
- "Test, spec, or both": what a test failure's ambiguity actually
  forks into, and why only one branch is safe to auto-repair.
- "Many worlds": the memoryless re-flip; why a directional signal is
  not the same risk as a blind resample.
- "The primitives": the four operations this page adds a fifth,
  bounded one to.
- "Telling the agent what changed": precedent for cheap, mechanical,
  unhashed signals narrowing a search without contaminating aim.

## Candidate titles

- Is the compiler cheating?
- Closed oracles, open oracles
- Feedback is not cheating; unmetered feedback is
