# On Test Cases as Immune System

## The last line

In spec-driven code generation, the spec defines what
the software should do. The agent generates code from
the spec. Confinement ensures the agent sees only the
chain. Staleness detection tells you when to
regenerate. Context management ensures the right
constraints reach the right generation.

All of this can be perfect, and the generated code
can still be wrong.

The spec may be ambiguous in a way nobody noticed. The
agent may interpret an unambiguous spec incorrectly.
The agent may implement nine out of ten steps and
silently omit the tenth. The agent may "fix" something
that was correct, because a general rule in the chain
contradicted a specific exception it didn't
understand.

Test cases are what catch these failures. They are the
immune system of the methodology — not the first line
of defense, but the last one, and the one that matters
most when everything else fails silently.

## What tests protect against

In traditional development, tests verify that the
developer's code does what the developer intended.
Developer and author are the same person. The test
catches mistakes in execution.

In spec-driven generation, the author is human and
the executor is an AI agent. The test catches a
different class of failure: the gap between what the
spec says and what the agent produced. This gap has
several shapes.

**Hallucination.** The agent invents behavior not in
the spec. A function gains a parameter conversion the
spec never asked for. A validation rule appears that
was never specified. A "helpful" optimization changes
semantics. Without a test that exercises the expected
behavior, the hallucination ships as a feature.

**Omission.** The agent skips something the spec
explicitly requires. A status transition is missing. A
field is not populated. An error path is not handled.
The code compiles, the happy path works, and the
omitted behavior is invisible until a specific test
case exercises it.

**Misinterpretation.** The spec says one thing; the
agent understands another. A date format is validated
as YYYYMMDD when the contract is DD-MM-YYYY. A
cooldown parameter is passed as a string concatenation
when the SQL expects integer arithmetic. The agent's
interpretation is internally consistent — it just
doesn't match what the spec meant.

**Anchoring.** The agent sees a previous generation
and preserves its behavior instead of implementing
the spec change. The spec was updated; the code was
not. The artifact tag hash is current; the behavior is
stale. Only a test that verifies the new behavior
catches the silent non-change.

Each of these failures is silent. The code compiles.
The artifact tag says "up to date." The staleness
system reports clean. The failure only surfaces when a
test exercises the specific behavior that went wrong.

## Test specs are specs

In Code from Spec, test cases are not afterthoughts
written by developers after the code works. They are
specs — authored before or alongside the
implementation spec, reviewed in PRs, versioned in
git. A test spec defines what the implementation must
satisfy, in the same structured natural language as
every other spec.

This means test cases carry the same authority as
implementation specs. When a test fails, the question
is not "is the test wrong?" — it is "does the spec
describe the correct behavior?" If yes, the
implementation must change. If no, the spec must
change. The test is never silently deleted or
commented out to make the build green.

This also means test cases are a form of
documentation that domain experts can review. A
compliance officer may not be able to evaluate
generated code, but they can review a test spec
that says "a request with amount zero returns an
invalid request error" and confirm whether that is
the correct business rule.

## What we have observed

Across sessions working with Code from Spec on
production services, test cases have consistently
caught generation errors that no other mechanism
would have surfaced:

- A subagent added input validation for a date field,
  requiring a format that contradicted the existing
  API contract. The existing tests used the correct
  format and failed immediately, revealing that the
  subagent had "fixed" something that was already
  correct — turning a valid contract into a breaking
  change.

- A subagent generated stub functions in a test file
  with incorrect type signatures — using a generic
  interface type where a concrete type was required,
  and adding parameters that the real function did
  not accept. The test build failed before any test
  ran.

- A subagent classified void operations as operations
  that return data, generating code that referenced
  response fields that didn't exist on the response
  struct. The build caught it, but only because the
  test file exercised the types.

- A subagent used a pointer-to-pointer where the test
  expected a plain pointer for round-trip comparison.
  The `reflect.DeepEqual` failure pointed directly at
  the type mismatch — a subtle error that would have
  been invisible without the comparison test.

- A subagent's test used random identifiers in webhook
  payloads instead of the deterministic format the
  implementation expected. The implementation correctly
  rejected them, and the test failure revealed that the
  test spec didn't specify the identifier format
  explicitly.

- A subagent treated a terminal state as blocking all
  updates, but the spec defined one operation type that
  should always apply regardless of terminal state. The
  test expected the state to change; the implementation
  refused. The contradiction between test spec and
  implementation spec was surfaced by the failure.

In every case, the test was the signal. Not the build
(most of these compiled). Not the spec review (the
specs looked correct at first read). Not the
subagent's assumptions report (it reported the
ambiguity but resolved it incorrectly). The test was
what turned a silent failure into a loud one.

## Tests discover spec gaps

When a test fails after regeneration, the instinct is
to fix the code. In spec-driven generation, the
discipline is different: trace the failure back to
the spec.

Every test failure has one of three root causes:

1. **The spec is ambiguous.** The agent interpreted it
   differently than intended. Fix the spec to be
   unambiguous.

2. **The spec is incomplete.** The agent didn't have
   enough information. Add the missing context to the
   spec or its dependencies.

3. **The agent made an error despite a clear spec.**
   Regenerate. If the error recurs, add a more
   explicit instruction to the spec's Agent section.

In all three cases, the spec improves. The test
failure is not a bug to fix — it is a spec gap to
close. Each closure makes the spec more precise, and
more precise specs produce more reliable generations.

This is the convergence loop: generate, test, fail,
investigate, fix spec, regenerate, pass. Each
iteration leaves the spec tree better than it found
it. The tests are the engine that drives convergence.

## The cost of a missing test

When a test case exists and catches a generation
error, the cost is small: investigate, fix the spec,
regenerate.

When a test case doesn't exist, the generation error
ships. It may surface in production as a bug, in an
audit as a compliance gap, or never at all — lurking
as a latent defect until conditions align.

The cost of a missing test case in spec-driven
generation is higher than in traditional development.
In traditional development, the same developer who
wrote the code also wrote the tests, and they share
an understanding of intent. If a test is missing, the
developer's judgment is still embedded in the code.

In spec-driven generation, there is no developer
judgment in the code. The code is whatever the agent
produced from the chain. If the test doesn't verify
a behavior, nothing does. The spec says what should
happen; the test verifies that it did. Without the
test, the spec is an aspiration.

## Preserving test cases

When refactoring specs — moving nodes, merging
modules, restructuring the tree — test cases must
migrate with the logic they verify. A test case that
existed in the old structure and doesn't exist in the
new structure is a regression waiting to happen.

This is not hypothetical. In a session where two
processing jobs were unified into a shared module,
the test cases from both jobs needed to migrate to
the new module's test spec. If any had been dropped,
the unified module would have had less coverage than
the two it replaced — and any generation error in the
uncovered area would have gone undetected.

The rule is simple: when logic moves, its test cases
move with it. When modules merge, their test cases
merge. The number of test cases after a refactor must
be at least the number before. Test cases are not
implementation details — they are behavioral
contracts that survive structural changes.

## Not a proof

Tests are not a proof of correctness. They verify
specific behaviors under specific conditions. A
passing test suite means every tested behavior is
correct. It says nothing about untested behavior.

In spec-driven generation, this limitation is sharper
than usual. Each regeneration may produce code that
handles untested edge cases differently. A test suite
that covers the happy path and three error cases
leaves everything else to the agent's judgment — and
the agent's judgment is not guaranteed to be the same
across generations.

The practical response is coverage discipline: every
behavior that matters must have a test case in a test
spec. Not code coverage — behavioral coverage. The
question is not "does every line execute?" but "does
every behavior the spec prescribes have a test that
verifies it?"

This is the same standard applied to safety-critical
software — and for the same reason. When the author
of the code cannot be trusted to catch their own
mistakes, verification must be external and
systematic. The difference is that in safety-critical
software, the distrust is institutional. In
spec-driven generation, it is structural: the agent
has no memory, no judgment, and no accountability.
The tests have all three.
