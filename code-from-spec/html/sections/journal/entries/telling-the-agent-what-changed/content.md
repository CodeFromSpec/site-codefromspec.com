---
title: Telling the agent what changed
description: "The fix for anchoring: a cache, a hash comparison, and a one-word disposition per spec position. What worked, and the elegant hypothesis that didn't."
date: 2026-07-01
---

# Telling the agent what changed

July 1, 2026

## Three open problems

![Three open problems](/images/journal/20260701_image_1.png)

In [Anchoring on old code](/journal/anchoring-on-old-code) I
described a failure in regeneration. When the spec changes but the
previously generated artifact is in the agent's context, the agent
sometimes preserves the old code and never processes the change.
The manifest updates, the build passes, staleness reports clean —
and the behavior never moved. The spec moved; the code did not.

That article closed with three problems worth solving: how to make
the agent reliably prioritize the spec over the existing code;
whether changing the order of presentation — code first, spec last
— reduces anchoring; and how to give the agent an explicit delta of
what changed in the spec, so it does not have to discover the
difference on its own.

I built against all three. This is a report on what happened —
including the part where my favorite hypothesis produced nothing.

## What I built

The mechanism rests on a cache. Whenever the tooling assembles a
spec chain, it records the content of every position — each
ancestor's contribution, each dependency, the node's own sections —
keyed by content hash. The cache is best-effort infrastructure:
gitignored, reconstructable, optional. Without it, regeneration
works exactly as before.

With it, the tooling can compare the chain that produced the last
generation against the chain that exists now, position by position,
by hash. No interpretation, no rendering, no diff notation — just:
same hash or different hash. Each position in the current spec is
tagged with a **disposition**: `unchanged`, `changed`, `added`, or
`removed`.
Positions that changed or were removed additionally deliver their
old content, in blocks that precede the current spec.

The regeneration context arrives in temporal order: the old spec
content (only what moved), the existing artifact, then the current
spec — complete, authoritative, and last. Here is a real one,
condensed, from a regeneration where a tool's output location
changed:

```xml
<chain>
  <previous_constraints>
    <entry name="SPEC/golang/implementation/mcp_tools/dump_chain"
           disposition="changed">
    ...the old interface: output is "wrote dump_chain.xml"...
    </entry>
  </previous_constraints>

  <previous_instructions disposition="changed">
  ...the old logic: write to dump_chain.xml at the project root...
  </previous_instructions>

  <existing_artifact>
  ...the Go file generated from that old spec...
  </existing_artifact>

  <constraints>
    <entry name="SPEC/golang" disposition="unchanged">...</entry>
    <entry name="SPEC/golang/implementation" disposition="unchanged">...</entry>
    <entry name="SPEC/golang/implementation/mcp_tools/dump_chain"
           disposition="changed">
    ...the new interface: output path derived from the logical name...
    </entry>
  </constraints>

  <instructions disposition="changed">
  ...the new logic: write to code-from-spec/.dump/&lt;name&gt;.xml...
  </instructions>
</chain>
```

The agent does not have to hunt for the change across several pages
of spec. Two positions say `changed` and carry their before and
after. Everything marked `unchanged` can be read as settled.

## The accidental experiment

The first real test was accidental, and it gave me both sides of a
comparison I could not have designed better.

While building the cache itself, I changed a test spec. The spec
said the tool's output should start with `chain_hash:` followed by
27 characters; I changed it to say the output should start with
`<chain>`. Two words different, in a spec that runs several pages
when delivered in the chain.

The first regeneration ran before the cache was operational. The
agent received the existing test file (which checked for
`chain_hash:`), the full current spec (which now said `<chain>`),
and no disposition. It compared them, concluded "no changes
required," and wrote the file unchanged. This is the anchoring
failure from the earlier article, reproduced exactly — and note
that the context was already in the reordered form, existing
artifact before the current spec. The remedy was also the one I
had documented: delete the artifact and regenerate from scratch.

Later in the same session, the cache came online. I reversed the
spec change, ran a generation to populate the cache, then reversed
it again. Same two-word change, same model, same subagent
configuration, same existing artifact.

This time the `<instructions>` element carried
`disposition="changed"`, and `<previous_instructions>` held the old
text. The agent identified the change and applied it. One attempt,
correct result.

## The deliberate one

With the cache operational, I ran a test designed in advance, on
the same tool — the regeneration whose chain appears condensed
above. The change: `dump_chain` used to write to a single file at
the project root, overwriting it on every call. The spec now says
it writes to `code-from-spec/.dump/<name>.xml`, deriving the file
name from the logical name, so that dumps of different nodes
coexist. Two spec files were edited: the node's own public
interface and its generation instructions, and the corresponding
test spec.

Staleness reported three artifacts: the implementation and its
tests, where the spec genuinely changed, and the MCP server — pure
cascade. The server depends on the `dump_chain` interface, which
changed, but it only forwards the string the tool returns.

Three artifacts, three different situations, three correct
behaviors.

The **implementation** agent applied exactly the two changes the
spec made — the path derivation, the dynamic return value — added
the one import the new code needed, and touched nothing else. The
error handling, the structure, the naming of the previous
generation survived intact.

The **server** agent received a chain where exactly one position
was marked `changed`: the `dump_chain` interface it depends on. It
traced the implication — the server merely passes the returned
string through — concluded that no change was required, and
regenerated the file identically. This is worth pausing on. The
disposition told it precisely where to look; the conclusion that
nothing was affected was the agent's verification, not the
orchestrator's assumption. In the earlier failure, "no changes
required" was the anchoring symptom. Here it was the correct
answer, reached by reviewing the one thing that had moved.

The **test** agent applied every update the test spec made — paths,
expected return values — and then went one step further than the
spec: the new output path is nested, so the overwrite test's setup
now needs the directory created before writing the old file, which
the spec never stated. It added that. A traced consequence of the
change, not a transcription of it.

After generation: staleness clean, build passing, tests passing.

## The hypothesis that produced nothing

The earlier article offered three hypotheses for why anchoring
happens, and flagged one as especially attractive: position.
Language models weight recent context more heavily, and in the old
assembly the existing artifact came after the spec — so perhaps the
code was winning simply by having the last word. Reverse the order,
the reasoning went, and the spec becomes the operative signal.

It was an elegant theory, cheap to test. And it produced nothing.

The reordered context — code first, current spec last — was already
in place when the experiment above ran its first arm, and the agent
anchored exactly as before. Whatever weight recency carries, it was
not enough to make the agent notice two words in several pages. The
problem is evidently not that simple.

I keep the temporal order anyway, for a different reason: it is
the arrangement under which the rest of the design makes sense. The
current spec is delivered complete and last, so it is authoritative
on its own — if the agent reads the comparison poorly, it still
generates from the full current spec. The failure mode of a missed
comparison is "generated without the benefit of focus," not
"generated against the wrong spec." But the order is scaffolding,
not the fix. The fix was the signal.

## The cheap half of the delta

The explicit delta was the third open problem, and I almost built
it the expensive way: compute a diff of each changed position and
render it for the agent. That design broke down on two questions.
The first was format: unified diff reads cleanly for code and lies
about prose — a one-word change shows up as a whole line leaving
and re-entering; word-level diff does the opposite; the specs are
both, sometimes in a single section. The second was fluency, and
there I had worse than no evidence. I had once wanted
`write_file` to accept a diff instead of the whole file, and the
model could not produce unified diffs reliably. Generating is not
reading, but it was a poor omen for building the framework around
a notation the model handles awkwardly from either side.

What the model does handle — constantly, unprompted, in every
working session — is something anyone who talks to one has
watched: it finds contradictions. Say one thing early in a
conversation and something incompatible later, and it points at
the pair. That is comparison by reading: no notation, just two
statements and the noticing. The bet was that this skill might be
sufficient.

So I split the delta in half. Saying *which* positions changed is
a hash comparison — mechanical, exhaustive, impossible to get
wrong. Showing *how* a position changed is rendering — expensive,
fragile, format-dependent. I built the first half and let reading
do the second: the disposition says where to look, the old content
of what moved says what was there before, and the agent finds the
difference the way it finds a contradiction in a conversation.

## What I do not know yet

Since the experiments above, disposition-guided regeneration has
run several times, across different real-world codebases, and it
has behaved correctly every time.
That is a consistent early record — however, it is still n of 1:
all of this is empirical, not rigorous.

The disposition tells the agent where to look, but noticing what
changed within a position, and tracing what it implies for the
code, is still the model's work. How well this holds depends on
the capability of the model in use, and will move — in either
direction — as models change. This is something to watch over
time, with caution.
