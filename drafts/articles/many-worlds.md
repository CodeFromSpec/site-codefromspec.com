# Many worlds

## Don't touch what works

Software developers learn early to respect working code. Code that
has been in production, survived edge cases, and been hardened over
time carries value beyond what it does — and experienced teams are
cautious about rewriting it. "Don't touch what works" is not
laziness. It is risk management: every rewrite is a chance to lose
something that was expensive to learn.

Spec-driven generation seems to make this instinct obsolete. If
code is a derived artifact, regenerating it should be as harmless
as rebuilding a binary. In [Collapsing the wave function](/articles/collapsing-the-wave-function)
we argued that it is not: the generator is stochastic, so a spec
defines a space of conforming programs, and the artifact on disk is
one realized sample from it — a record that regeneration from
scratch does not rebuild but replaces, with a different sample.

This article is about why that replacement is costly. Not because
the new sample is worse — it may well be better. Because of what
happens to bugs when the program you run keeps changing underneath
a spec that has not.

## Every program comes with its own bugs

Here is the property that makes a stable artifact valuable, stated
plainly: **the bugs of one program are a bounded set. The bugs of a
distribution of programs are not.**

Take the artifact you have. It is imperfect — some of the choices
the generator made are wrong in ways nobody has noticed. But its
defects are a specific, fixed collection: the defects of *this*
program. And that collection has two properties worth money.

It only shrinks. Each failure that surfaces — a failing test, a
production incident — identifies one member of the set. The fix
goes into the spec and a test pins it; the artifact is
[regenerated minimally](/articles/telling-the-agent-what-changed),
the correction applied, everything else preserved. One
bug leaves the set and cannot return. Run this loop for months and
the artifact converges: not toward perfection, but toward a program
whose remaining defects are the ones your environment has never
been able to reach.

Which is the second property: the dormant bugs stay dormant. Every
program has defects that its environment never exercises — the
race condition your traffic pattern cannot trigger, the edge case
your data never produces. In a stable artifact these are
operationally nonexistent, and they remain so, indefinitely,
because the code that contains them does not change.

## Regeneration visits the other worlds

Now regenerate from scratch, and watch both properties disappear.

The new artifact is a fresh sample from the space. It does not have
your program's bugs — it has its own: a new set, of unknown size,
with an unknown intersection with your environment. The bugs you
spent months finding and fixing are gone, yes — but so is the
knowledge of where this program's weaknesses are. The dormant
defects that were safely out of your environment's reach have been
re-rolled, and the new ones may not be out of reach.

Do this on every spec change, and something statistically ugly
happens. Each regeneration is a fresh draw, and each draw exposes
you to a fresh defect set. Over enough regenerations, the system
does not converge on stability — it progressively explores the
**union of the defect sets of every program the spec permits**. You
do not live with the bugs of one program. You encounter, over time,
the bugs of all of them.

Reliability engineering has a name for the shape this produces.
Failure rates follow a bathtub curve: high in infancy, low through
maturity, rising again in wear-out. A codebase that is regenerated
from scratch on every change never leaves infancy. Every build is a
newborn. The maturity phase — the long, flat bottom of the curve,
where the code that has been running keeps running — is exactly the
phase that perpetual resampling makes unreachable.

## You don't tell the junior to start over

Every team that employs junior developers already knows how to
handle an executor that introduces bugs. The junior will get things
wrong — it is a given, and it is fine, because the errors are
detected and corrected. What no one does is respond to a bug by
saying: *sit down, write the whole module again, and this time
don't make error #11.*

The instruction is absurd for two reasons, and they are worth
separating. The first is blast radius: rewriting everything to fix
one thing risks the ninety-nine things that were right. The
correction of a bounded error should be bounded — that is what
makes errors from an imperfect executor tolerable at all.

The second reason is specific to our executor, and it makes the
instruction more absurd, not less. Told to start over, a junior at
least learns: there is some chance the rewrite genuinely avoids
error #11, because the junior remembers making it. The model
remembers nothing. It does not know it made error #11, and a fresh
generation is not chastened by past failures — it is the same
distribution, sampled again, as likely to produce the old error as
it ever was, minus nothing. For a memoryless executor, "do it again
but better" is not even a long shot. It is a coin re-flip dressed
up as an instruction.

There is exactly one channel through which "don't make error #11"
reaches an executor with no memory: the spec that governs the
generation and the test that pins the behavior. Written there, the
correction holds for every future generation, by any agent, on any
model. Spoken anywhere else, it holds for nothing. This is not a
discipline the methodology invented — it is the only mechanism
that exists.

## What survives the re-roll, and what does not

The previous section suggests an objection worth taking seriously.
If every fix ends up in the spec and a test, then a from-scratch
regeneration does not lose the fixes — the spec carries them, the
tests enforce them, and the new sample is born already corrected.
Is the stability argument not just sentimentality about code?

Ratified corrections do survive. That is the point of putting them
in the spec. What does not survive is everything that was right
**without ever being ratified** — and in any real system, that is a
lot.

A spec of any size is ambiguous in places nobody has identified.
The previous generation resolved those ambiguities somehow, the
tests passed, the software ran. It worked — partly by luck. The
ambiguity was never found, so it was never fixed, and nobody paid
the price of making the spec precise there. A stable artifact lets
that debt sit harmlessly: the resolution that happened to work
keeps working, because the code that embodies it does not change.
A re-roll calls the debt in. The new sample resolves the same
ambiguity fresh, and this time the coin may land the other way.
What worked yesterday breaks — not because the spec changed, but
because the spec had never actually decided, and the decision was
re-made.

The honest accounting of a from-scratch regeneration is therefore:
everything written down survives; everything that was luck is
re-rolled. And no team knows, at any given moment, how much of its
system's correct behavior is written down and how much is luck.
The stable artifact is the only place the luck is stored.

## Stability is a statistical property

None of this is an argument against regeneration — the methodology
is built on it. It is an argument about *how* code should be
regenerated: minimally, against the existing artifact, changing
what the spec change requires and preserving every resolution it
does not touch. The [disposition mechanism](/articles/telling-the-agent-what-changed)
exists precisely to make that safe — to keep the artifact stable
without letting its stability override the spec.

It is also, finally, a reframing of what code maturity is. The
developer's instinct to respect working code looks like
sentimentality — an attachment to something that could, after all,
be rewritten. Under a stochastic generator it turns out to be a
statistical insight. A mature codebase is one whose defect set has
been enumerated and shrunk by contact with reality, and whose
dormant defects have been left in peace. That property belongs to
*a* program — one draw, held onto, corrected in place. The spec
permits many worlds. Reliability comes from living in one of them.
