# The Last Word

In a debate, the speaker who goes last has an advantage that
has nothing to do with being right. They get to frame what
came before — to cast the earlier arguments as the things
their own argument now answers. Order is leverage. Whoever
speaks last speaks over everyone else.

A language model reads its context the same way. It does not
weigh a prompt as a flat set of facts to be reconciled; it
reads a sequence, and in a sequence, later revises earlier.
This is not a quirk to be corrected — it is what the model
learned from. The text it was trained on is full of
sequences where the end supersedes the beginning:
conversations where a later turn overrides an earlier one, a
document whose final draft replaces its draft, an argument
that builds to the claim that governs the rest. The model
absorbed the convention that the most recent statement on a
subject is usually the operative one. When you place
something last, you are not just putting it at the bottom.
You are giving it the last word.

This means the order in which you present things to a model
is not layout. It is an argument about authority. Two blocks
of context that contradict each other are not equal just
because they are both present; the one that comes later wins
the room, the way the closing speaker does. Most of the time
we arrange context without thinking about this, and most of
the time it does not bite us. But when two parts of the
context genuinely disagree — when one is meant to override
the other — the order stops being cosmetic and becomes the
thing that decides which one the model follows.

We ran into this directly, in the part of Code from Spec
where it matters most.

## Where it bit us

In this methodology, code is generated from a specification.
When the spec changes, the code is regenerated. To keep the
regenerated code from drifting needlessly — different names,
reshuffled structure, churn with no meaning — we show the
agent the code it produced last time, as a reference to stay
close to.

That reference is also a trap. The old code is concrete,
plausible, already-working. The new spec is prose — longer to
read, and describing a change that may be small and easy to
miss. When the two sit in front of the agent and disagree,
the old code tends to win. The agent checks whether the code
still roughly satisfies the spec instead of regenerating from
the spec, and a real change slips through. The spec moved;
the code did not; nothing flags it.

We had been giving the old code the last word. It came after
the spec in the context, and so — by the same convention that
makes a closing speaker persuasive — it read as the most
recent, most authoritative statement about what the code
should be. We were arranging the context against ourselves.

## What we built

The fix follows directly from the insight: if the last thing
the model reads is what it treats as authoritative, then the
authoritative thing has to come last.

So we order the context as a story in time. First the spec as
it was at the last generation. Then the code that spec
produced. Then the spec as it is now. The agent reads the old
rules, reads the code that embodied them, and then reads the
new rules — which arrive last, and therefore read as the
correction to everything above. We are not handing the agent
a computed diff to apply. We are letting it do the thing it
already does well: read a sequence and notice that the ending
revises the beginning. Showing it both the before and the
after, in that order, lets it see what changed without anyone
having to compute the change for it — and it leaves the
current spec where the model's own habits will treat it as
the last word.

The mechanics underneath are modest: the framework keeps what
the spec said last time, shows only the parts that actually
moved, and labels each part with whether it changed, was
added, or was removed. But the mechanics are not the point.
The point is that we stopped fighting how the model reads and
started arranging the context to match it. The spec is
authoritative, so the spec goes last.

## Why it is safe to lean on

There is a tension here worth being honest about. Elsewhere
this methodology is built on *not* trusting the agent's
judgment — on confinement, on tests, on hashes, precisely
because the model misreads and invents. Leaning on the
model's reading to notice what changed seems to cut against
that.

It does not, because of where the weight rests. The current
spec is delivered in full and is authoritative on its own. If
the agent reads the before-and-after well, it sees exactly
what moved. If it reads them poorly, it still generates from
the current spec, because the current spec is right there,
complete, and last. The comparison is a lens, not a
foundation. We are not trusting the model's reading to be
correct; we are arranging things so that the spec wins even
when the reading is imperfect. The order does the work, and
the order cannot misread.

## The smaller principle

There is a habit underneath this that is worth naming. The
reflex of an engineer faced with "the agent should notice
what changed" is to build something: compute a diff, render
it in some notation, hand it over. We almost did. What we did
instead was notice that the model already compares two
versions by reading them, the way it already keeps the latest
statement as the operative one — and that the job was not to
build a mechanism but to stop working against one that was
already there.

Order is the cheapest tool in the box and the easiest to
overlook, because it feels like formatting. It is not. For a
reader who treats sequence as meaning — which is what a
language model is — the order of the context is a claim about
what matters most. Put the thing that should govern where the
model already looks for the thing that governs: last.

We do not yet know how far this carries. We have not run
enough regenerations to say whether the order alone keeps the
agent anchored on the spec rather than the old code, or
whether a large body of prior context placed ahead of the
spec dilutes it instead. Those are opposite failures with
opposite fixes, and watching which one appears, if either
does, is how the idea earns its place. But the principle
under it is not in doubt, because it is not really about this
framework. It is about how the thing reads. Whatever you put
last, you have given the last word — so choose it on purpose.