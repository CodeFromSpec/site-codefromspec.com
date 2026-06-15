# On Feeding Generated Code Back to the AI

## The question

In spec-driven code generation, the source of truth is a
specification tree. Code is a derived artifact — generated,
not authored. When a spec changes, the code is regenerated
by an AI agent that receives the spec chain as context.

When regenerating, should the agent also see the previously
generated code?

This is not an obvious decision. The previously generated
code — the existing artifact — is a natural candidate for
context: it shows the agent what was generated last time,
so it can produce a minimal diff. Across multiple projects
using [Code from Spec](https://codefromspec.com), we have
explored both approaches and found that the answer is less
straightforward than it seems.

## What goes well

There are real benefits to showing the agent its previous
output.

The agent compares the spec against the existing code and
changes only what the spec requires. Helper function names,
variable naming style, code organization — all remain
stable between generations. Diffs are small and reflect
only the spec change. When only the artifact tag hash needs
updating (an upstream change cascaded but the spec for this
node did not change), the agent recognizes there is nothing
to change and produces the same file with a new hash. This
is the common case in cascade-only regenerations, and it
works well.

When it does not work — when the agent anchors on the
existing code and ignores a spec change — a natural
reaction is to augment the prompt with a directional
signal: a compilation error, a failing test result, or an
explicit description of what changed in the spec. This
mirrors traditional AI-assisted development, where the
agent receives the code plus a signal that says "this is
wrong, fix it here." And it works — the agent, given a
clear direction, applies the change.

But in spec-driven generation, this creates a problem.
The directional signal is not part of the spec chain. It
is not versioned, does not participate in the hash, and
will not be present on the next regeneration. It is an
ad-hoc intervention that fixes this generation but does not
prevent the next one from producing the same error. If the
fix depends on information outside the spec, the spec is no
longer the sole source of truth — and the methodology's
core premise is undermined.

## What goes wrong

The benefits of including the existing artifact come with a
cost that is harder to see.

When the spec changes but the existing code reflects the
old spec, the agent tends to preserve the existing code. We
have observed this repeatedly:

- A prefix was renamed from `ROOT/` to `SPEC/` across the
  entire spec tree. The existing Go code used `ROOT/` in
  usage examples. The agent preserved `ROOT/` from the
  existing code despite the spec saying `SPEC/`.

- A hash placeholder in test fixtures needed to be exactly
  27 characters. The spec provided a 27-character example.
  The existing artifact had a 25-character hash. The agent
  kept the 25-character version.

- A struct field was renamed from `LogicalName` to
  `UnqualifiedLogicalName`. The existing code used the old
  field name and worked around it with a utility function.
  The agent preserved the workaround instead of using the
  new field directly.

In each case, deleting the existing artifact and
regenerating from scratch produced correct code.

The pattern is consistent: existing artifact present, spec
change ignored. Existing artifact absent, spec change
applied. We do not know the exact mechanism — it may be the
statistical weight of code tokens, the instruction wording,
or something else. But the result is reproducible.

## Why it is hard to detect

The generation prompt says: "use the existing artifact as a
starting point, make only the changes needed." The
intention is stability. In practice, "only the changes
needed" becomes "change as little as possible." The spec
change is not applied, but the artifact tag hash is
updated. The staleness detection system reports "up to
date." The code compiles. Many tests pass. The change is
simply absent.

This matters because one of the ambitious goals of
spec-driven generation is to not have to read the generated
code. The spec defines behavior, the agent generates code,
tests validate the result. This model depends on failures
being detectable — and silent non-changes are the hardest
failures to detect.

Compare the two failure modes. Generation from scratch may
produce code that diverges from the spec — the agent
misunderstands something and generates incorrect code. But
it tried. The error manifests as a failing test: "expected
X, got Y." You trace back to the spec, find the ambiguity,
fix it.

Generation with an existing artifact may produce code that
silently preserves old behavior. The agent does not
misunderstand the spec — it never processes the change.
Debugging "implemented wrong" means following the logic
until it diverges. Debugging "did not implement" means
first discovering that the change is absent — which
requires the exact right test. And if the test was also
generated from a spec that anchored on old patterns, even
the test may not catch it.

## On why this happens

We can only speculate about why the agent anchors on the
existing artifact. We do not have visibility into the
model's internal reasoning. But we can observe the
context it receives and note what seems likely to
contribute.

The agent receives the spec chain and the existing
artifact in the same stream. The chain contains Go
interfaces from other packages (authoritative — must
follow exactly), pseudocode from the functional spec
(authoritative — must implement), and library
documentation (authoritative — must use those APIs). The
existing artifact is not authoritative — it is the thing
being replaced. But nothing in the context marks it as
different. For the agent, it is all equally trustworthy.
And the existing artifact is typically the largest, most
concrete, most code-like block.

In traditional AI-assisted development, the agent
receives a directional signal alongside the existing
code: a failing test, an error message, a specific change
request. The signal says "this is wrong, fix it here." In
spec-driven regeneration, there is no such signal. The
agent must discover for itself what changed in the spec —
while the existing code sits in the context looking
correct.

These are plausible explanations, not proven causes. What
we can say with certainty is simpler: if the existing
artifact is not in the context, anchoring on it is
impossible by definition.

## The tradeoff

Removing the existing artifact from the context eliminates
anchoring but introduces variability. Without an anchor,
the agent may choose different names for helper functions,
organize code differently, or structure error handling in a
new pattern. The code is correct but looks different every
time. Diffs between consecutive generations are larger and
noisier.

For teams that review generated code, this adds friction.
For version control history, it is noise. And if the
existing artifact captured a decision that the spec does
not describe — a performance optimization, a platform
workaround — generating from scratch loses it.

On the other side: removing the existing artifact reduces
input tokens (it is typically the largest block in the
context), while output tokens stay the same (the agent
writes the complete file either way). And it eliminates an
entire class of silent failures.

The question is which cost is higher: noisy diffs that are
honest, or clean diffs that may be hiding unchanged code.

## Where we are

We have not made a final decision. There are scenarios
where the existing artifact is clearly valuable —
cascade-only regenerations where the spec for the node did
not change, or projects early in their spec-driven journey
where specs are still being refined and stability matters.

But our experience across projects — hundreds of generated
artifacts, multiple rounds of regeneration, repeated
encounters with silent anchoring — suggests that the
default should be generation from scratch. The anchoring
problem is silent, invisible to automated checks, and
consistently biases the agent toward preserving the past
instead of following the spec.

If code is truly a derived artifact, perhaps we should
treat it that way: generate it from the spec, validate it
with tests, and not feed it back into the process that
created it.
