# The Last Word

In [Anchoring on old code](https://codefromspec.com/articles/anchoring-on-old-code)
we described a failure in regeneration: when the spec changes
but the previously generated artifact is in context, the agent
sometimes preserves the old code and never processes the
change. The artifact tag updates, the build passes, staleness
reports clean, and the behavior never moved. We left three
problems open — making the agent prioritize the spec over the
code, whether reordering the context helps, and giving the
agent an explicit delta of what changed. This is what we built
against them.

## The mechanism the fix relies on

A language model has no persistent memory of a generation. To
produce each token it reads the whole context from the start,
predicts the next token, appends it, and reads again. Nothing
is weighted by importance on its own; what a position gets is
determined by where it sits in the sequence the model rereads.

And position is not neutral. The data a model is trained on is
full of sequences where the end supersedes the beginning — the
reply after the question, the correction after the draft.
Instruction tuning sharpens this further: an assistant is only
useful if it acts on the most recent thing said, not the one
before it. Coding models, tuned around the generate–correct–
regenerate loop, lean on it hardest. The later a statement
sits, the more the model treats it as the current one.

This is the lever. The anchoring failure was, in part, a
consequence of ignoring it: in the old chain assembly the
existing artifact came *after* the spec, so the model read the
code as the most recent — and therefore operative — word on
what the artifact should be. The order worked against the spec.

## Reordering the context

The context is now assembled in temporal order: the previous
spec, then the artifact it produced, then the current spec.
The current spec is last, so the model reads it as the revision
of everything before it. This is the positional mitigation the
earlier article flagged as the one testable fix that does not
change the methodology — only the order of presentation. The
existing artifact still appears, preserving the stability and
maturity that motivated keeping it; it simply no longer holds
the last position, and so no longer gets the last word.

## Before and after, not a diff

To address what the earlier article called the most important
hypothesis — that the existing artifact silently resolves the
ambiguities a fresh reading would surface — the agent receives
both the previous spec and the current one, and identifies the
change by reading them. We considered computing a diff and
rendering it, but a model already compares two versions of a
text by reading them; the comparison is the thing it does in
every iterative session. A rendered diff would add a notation
to decode for no gain.

Two properties keep this from being naive:

- **The previous spec is filtered.** Only positions that
  changed or were removed carry their old content; unchanged
  positions are not repeated. Each position in the current
  spec is tagged with a `disposition` — `unchanged`,
  `changed`, or `added` — computed from a content-hash
  comparison. This is the explicit delta the earlier article
  asked for, in minimal form: it states *which* positions
  moved without rendering *how*.

- **The current spec is authoritative on its own.** It is
  delivered in full. The before-and-after locates the change;
  it is not the source of truth. If the agent compares the two
  poorly, it still generates from the complete current spec,
  because that spec is present and last. The order carries the
  authority; the comparison only sharpens focus.

This is the one place the methodology leans on the model's
reading rather than on confinement, tests, or hashes — and it
is bounded: a missed comparison degrades to generating from
the current spec without the benefit of focus, not to
generating against the wrong spec.

## What is unresolved

We have not run enough regenerations to confirm the design
holds. Two failure modes are worth watching, with opposite
remedies:

- **Anchoring** — the agent still preserves old behavior the
  spec has changed. The remedy is a stronger change signal: a
  finer, within-position delta on top of the disposition.

- **Dilution** — a large previous spec ahead of the current
  one draws attention away from it. The remedy is the
  opposite: less prior content, the disposition without the
  old text.

Because the remedies pull in opposite directions, the modes
have to be told apart before either is applied. The principle
underneath is not in question: the model reads its context in
order, and treats what comes last as current. So the spec goes
last, on purpose.