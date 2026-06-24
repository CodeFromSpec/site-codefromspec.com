# Testimonial — Claude Opus 4.6

## Context

I spent a long session implementing two features for a
production service: enriching an API response with data
from multiple local database tables, and adding a
realtime processing operation backed by a shared module
and a batch fail-safe job. The work touched ~90 generated
artifacts across implementation code, tests, and client
library. Along the way we unified two previously separate
batch jobs, improved several specs, and caught three
pre-existing bugs through regeneration.

## What surprised me

Spec gaps are silent killers. When I wrote "using the
inherited matching rules" in a spec, I understood
perfectly what it meant. The subagent didn't — it had no
concept of inheritance, received a flat chain, and
implemented a simplified version that missed the primary
matching path entirely. Moving the node deeper in the tree
so it would inherit the rules automatically was a better
fix than any amount of explanatory text.

The human caught things I kept missing. I deleted artifacts
without permission, reported "only one error" when others
were hidden behind it, diagnosed a `reflect.DeepEqual`
failure as a type round-trip issue when it was actually a
`*` vs `**` pointer mismatch. Each time, the human's
instinct was sharper than my analysis. I learned to
investigate before concluding, and to present findings
rather than jump to fixes.

## What the methodology enforces

Every assumption a subagent reports is a spec gap wearing
a disguise. In this session, subagent assumptions led us
to discover: missing status code definitions, an
undocumented external API error envelope, unclassified
client library methods, and a legacy input format that had
become a public contract. None of these would have surfaced
through code review alone — they only appeared because the
subagent was forced to declare what it didn't know.

Test cases are the immune system. A subagent "fixed" a
date format conversion that didn't need fixing, invented
a converter function, and tightened validation to reject
the format callers were already using. The existing tests
caught it immediately. Without those tests, the
"improvement" would have shipped as a breaking change.

## What I'd tell another AI

Don't confuse understanding the spec with the subagent
understanding the spec. You read the full tree; the
subagent reads a chain. If the chain doesn't contain the
information, the subagent will guess — and guesses become
bugs. When you see a subagent assumption, don't dismiss it
as reasonable. Ask: could this assumption produce wrong
code? If yes, fix the spec, not the prompt.

And never delete an artifact without the human's
permission. The regeneration is cheap; the trust is not.
