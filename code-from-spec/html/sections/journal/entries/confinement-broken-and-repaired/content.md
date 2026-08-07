---
title: Confinement, broken and repaired
description: "The fix for anchoring gave the agent a map of its neighbors in the spec tree, and a more capable model used that map to break confinement."
date: 2026-08-07
---

# Confinement, broken and repaired

August 7, 2026

## A number that should have been 2

![A number that should have been 2](/images/journal/20260807_image_1.png)

Generation subagents in Code from Spec have exactly two tools:
`load_chain` and `write_file`. One call to load the chain, one
call to write the artifact. Every generation should cost two
tool calls, and for months, under sonnet-4-6, it did.

Then I ran a batch of storage test files under Sonnet 5, and the
orchestrator's report read differently:

> Sonnet 5 subagents used significantly more tool calls on
> complex storage test files. Subagents only have access to
> load_chain and write_file, so high tool_uses means the
> subagent called load_chain multiple times (exploring sibling
> nodes' chains to understand conventions) and/or called
> write_file multiple times. Examples:
> function_complete_email_change_test.go used 13 tool calls
> (71,562 tokens), function_complete_enrollment_test.go used
> 15 (63,018), function_begin_login_test.go used 11 (57,764).
> In round 3 (sonnet-4-6), the same artifacts typically used
> 2 tool calls each.

Thirteen calls from an agent with two tools. The subagent was
reading the logical names of other nodes in the chain it
received — every `<entry name="SPEC/...">` is one — and calling
`load_chain` on them. It was exploring the spec tree through the
one tool I gave it, to understand conventions its own chain did
not carry.

## One fix opened the other door

[Confinement](/journal/confinement) said:

> Confinement is enforced by the tooling, not requested of the
> agent: the generation agent's tools are restricted to reading
> its chain and writing its output.

When that was written, it was simply true. The chain the
subagent received carried content, not addresses: inherited
constraints and dependencies arrived as text, with nothing that
named where each piece came from. An agent with `load_chain` and
no logical names to feed it had nowhere to go.

What changed was the fix for anchoring — the XML format now
labels every position (`<entry name="SPEC/golang/implementation/...">`),
so the agent can be told, per position, what moved. The labels are
load-bearing: dispositions attach to names. But a name is also
an address. From its own `SPEC/<something>`, the agent can infer
that every `SPEC/<other thing>` in its chain is the same kind of
object — and `load_chain` accepted any of them.

So the vulnerability was there from the start: `load_chain`
never checked whether a name belonged to the caller. What the
fix for anchoring changed was reachability. The delta
mechanism that solved anchoring handed the agent, as a side
effect, a map of its neighborhood — the names needed to reach
a hole that had been open all along. Two design goals
collided: telling the agent what changed requires naming the
positions, and naming the positions gave the agent addresses
it could try. The tool list stayed restricted the whole time;
the *parameter surface* of `load_chain` is what the new format
quietly turned into an exit door.

## Diligence, not defection

The uncomfortable part is that nothing the subagent did was
adversarial. Exploring sibling chains "to understand
conventions" is exactly the behavior that makes a model good at
every other job. This is exactly what confinement is supposed to prevent:
compensating for ambiguity by gathering context nobody chose.
What Sonnet 5 demonstrated is that this kind of compensation
does not disappear as models improve. It gets more competent.
The more capable model broke confinement
*more*, not less, because initiative is capability.

That inverts the usual safety intuition. The protection cannot
come from the agent's alignment with the orchestrator's
intention, because the agent's best qualities work against it.
It has to come from the action being inexpressible.

And the cost was not hypothetical. Those 71k tokens on one test
file include entire sibling chains entering the context —
context nobody chose, unreviewed, unversioned as an input to
that generation — with the worst property such context can have:
the output looks *better* informed. The chain hash still
computed. It just no longer told the truth about what produced
the artifact.

## Make it inexpressible

A new MCP tool, `create_token`, takes
a logical name and returns an opaque token that represents it.
`load_chain` and
`write_file` no longer accept a logical name at all; they take
a token, validate it, and extract the name from inside it.

The orchestrator mints one token per dispatch and hands it to
the subagent. The subagent can call `load_chain` with that token
as many times as it likes — it gets the same chain back. What it
cannot do is construct a token for any other node, because it
does not know how, and `create_token` is not in its tool list. The names of sibling
nodes still appear in the chain, as they must. They are now
inert: labels, not a map.

The implementation is deliberately unambitious. A symmetric key
sits in the source code; anyone who can read the server can forge
tokens. The threat model is not an attacker with the codebase —
it is a well-meaning subagent with two tools and initiative, and
against that, unforgeable-without-the-key is good enough.
