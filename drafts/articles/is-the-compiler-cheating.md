# Journal seed — Is the compiler cheating?

Question: during regeneration, is it legitimate to feed
the generation subagent compiler errors or test failures
from its own output? Or does that violate "generate from
the chain only"?

## Position (revised under the search framing)

- **Compiler errors: yes.** A compiler error is the
  deterministic verdict of the toolchain over that exact
  code. It carries no intent — it cannot tell the agent
  *what to build*, only that what it built does not hold
  together. Feeding it back is cleaning sampler noise,
  not importing unauthored context. (Gaming mode to guard
  against even here: satisfying the verdict by
  amputation — deleting the offending functionality.)
- **Test failures: legitimate as a signal category, but
  only after triage.** Original position ("tests are a
  different kind of object") was refuted by the search
  framing: the test spec is a region description too
  (extensional), so a test failure IS a verdict, same
  category as the compiler's. What survives is the triage
  gate, on two grounds the category argument does not
  dissolve:
    1. **The verdict is ambiguous.** With two authored
       descriptions of the region, a failure means either
       "point outside region" (descriptions agree; agent
       erred) or "the two descriptions disagree at this
       point" (spec ambiguous/incomplete, or test wrong).
       Auto-repair in the second case moves the point into
       the *intersection* and silently consumes the
       disagreement — which is the convergence loop's food.
       Compiler verdicts have no such ambiguity: the
       toolchain is not an authored description and cannot
       disagree about intent. A test failure is a collision
       between two authored artifacts, and those must reach
       a human.
    2. **Goodhart.** Pins-as-repair-targets invite passing
       the pins without the rule between them: hardcoded
       expected values, special-cased scenarios,
       amputation. Legitimate repair is projection onto the
       intersection — failure output + the full prose
       chain — never pin-chasing.
- **Accounting corollary:** a green obtained after the
  agent saw the failing assertion is weaker evidence than
  a first-pass green at that pin — conformity, not
  independent concordance. Mark repaired greens;
  first-pass rate remains the spec-quality metric.
- **Policy:** failure → triage (the disambiguation) → if
  "descriptions agree, point is out": retry with failure
  output + full chain, bounded, counted.

## The case against (taken seriously)

- Prompt additions "bypass the chain, are not versioned,
  do not participate in the hash" — a compile error is
  all of those.
- The manifest would subtly lie: the artifact came from
  (chain + N feedback rounds), not from the chain alone.
- Deepest risk: an agent that self-corrects can hide the
  spec's insufficiency. The ambiguity that caused the
  error never reaches a human, never becomes a spec fix.
  Convergence stalls, permanently subsidized by invisible
  iteration. (The "subsidy" argument from The immune
  system, reincarnated: the retry loop as the new silent
  payer.)

## The case for

1. **Verdict vs. intent.** Confinement exists to exclude
   unauthored *sources of intent* (neighboring files,
   CLAUDE.md, the web) — things that compete with the
   spec over *what to build*. Compiler output is a
   mechanical function of (artifact, toolchain). It is a
   verdict about *what happened*, and cannot contaminate
   the what.
2. **The category already exists in the chain.**
   `<existing_artifact>` and the `previous_*` blocks are
   machine-produced, unauthored, unhashed context,
   delivered with explicit authority semantics ("history —
   not so you can preserve it"). Build verdicts fit the
   same category: context that informs but does not
   govern.
3. **Filter vs. redefinition (via Collapsing).** The spec
   defines a space of conforming programs. The compiler
   does not move that space — it filters samples.
   Error-guided retry is search *within* the space.
   Reading a neighboring file *redefines* the space with
   constraints nobody wrote. That is the difference.
4. **Via Many worlds:** retry *without* the error is the
   coin re-flip ("do it again but better," to a memoryless
   executor). The disposition mechanism already
   consecrated the principle: cheap directional signal is
   what makes retry rational. A compiler error is the
   cheapest directional signal there is.

## The line

Form vs. meaning — and it coincides with innate vs.
adaptive immunity:

- **Form (compiler, linter, type checker):** feedback
  automatic and legitimate. Deterministic, intent-free.
  Bounded retries (2–3), always counted.
- **Meaning (behavioral tests):** failure escalates to
  triage first. Only after the verdict "spec is clear,
  agent erred anyway" is a retry with the failure output
  in context legitimate — and superior to a blind
  re-flip. (Open question: is even this too permissive?
  Stricter position: test results never reach the agent;
  only spec fixes do.)

## The thesis that dissolves "cheating"

Feedback is not cheating; **uncounted feedback is.** The
claim at risk is "the spec is sufficient to produce the
artifact." Five feedback rounds mean the spec was not
sufficient in one shot — hiding the rounds hides the
insufficiency. So: do not forbid the signal; **meter
it**. First-pass success rate (compiles and passes with
zero feedback) becomes *the* mechanical metric of spec
quality. Retries are permitted throughput; a rising retry
count on a node is the alarm that its spec precision is
in debt. Free byproduct: the project's first mechanical
KPI of convergence.

## Design notes

- Feedback arrives as a **new subagent dispatch** with
  the verdict as an explicit block (e.g.
  `<build_verdict>`), preserving "every generation is a
  function of declared inputs." Not as conversation
  accumulating in one agent's context — that is the line
  between a guided sampler and an agent debugging (i.e.
  ordinary agentic coding).
- Retry counts recorded per node (manifest or cache) and
  reported per session.
- Admission the feedback makes visible: the toolchain was
  always an implicit dependency of generation ("the
  artifact must compile under *this* compiler"). The
  feedback loop surfaces it; that is gain, not loss.

## Connections

- The immune system: innate/adaptive division; the
  subsidy argument.
- Many worlds: memoryless re-flip; directional signal.
- Collapsing the wave function: space vs. sample; filter
  vs. redefinition.
- Telling the agent what changed: precedent for cheap
  mechanical signals shrinking the haystack.
- BEST_PRACTICES "Diagnose before regenerating": the
  existing rule that test failures go to triage.

## Candidate titles

- Is the compiler cheating?
- The verdict and the intent
- Feedback is not cheating; uncounted feedback is