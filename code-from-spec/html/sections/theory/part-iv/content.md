---
title: "Part IV — The oracle"
description: "Where verdicts come from, what each costs, and how far each can be trusted."
date: 2026-07-18
---

# Part IV — The oracle

Part III asked what the description should be. This part asks the same of the oracle: where a project's
verdicts come from, what each one costs, and how far each can be trusted, starting from the verdicts farthest
from intent, and climbing.

## The type system as a pre-paid oracle

![Part IV — The oracle](/images/theory/20260718_image_part_IV.png)

The compiler is the project's first oracle, running before the first test is written. It decides one slice
of intent (shape, not behavior), and the slice is genuine intent because the language was a deliberate
choice, a clause in the description that selects the space every draw is sampled from and brings that
space's checker with it.

What sets this oracle apart is how it was paid for: a test is authored by this project, at this project's
cost, while a type system is authored once, by the language's designers, and every adopter inherits its coverage
free, on every build, for as long as the project lives. And type annotations are a slice of the written
description that cannot go stale, because the build consults them on every change.

Most of the case against types was a cost of writing, and writing is what collapsed: a generator supplies
the annotations unasked. What types buy is what cheap generation is short of: mechanical confirmation of
every draw, and a verdict safe to hand straight back to the generator. Choosing an untyped language declines
free coverage in the regime that needs it most, to save a cost that has mostly stopped existing. Even so, no
type system confirms a property nobody thought to encode. The frontier between prepaid shape and tested
behavior is a design choice, and something always stays on the tested side.

## Closed oracles and open oracles

When the compiler rejects a program, nobody suspects the compiler. When a test fails, suspecting the test
is sometimes the right answer. The difference is not one of kind (a compiler bug is structurally the same
event as a test disagreeing with the spec) but one of trust. Years of heavy use have driven the chance of
the compiler being wrong down to a probability most people treat as zero.

Call an oracle **closed** when that kind of trust has been earned, and **open** when it has not. The
compiler is closed; a freshly written test is open, because a red verdict could mean the code is wrong or
the test is wrong, and nobody has checked which. The flaky test that everyone learns to rerun is the
clearest example: an oracle nobody examined, vetoing code that would have been fine.

The compiler has siblings that share its economics without sharing its closure: the linter, the static
analyzer, the security scanner. Each is authored once, outside the project, and inherited as free coverage
on every build, pre-paid the way the type system is. What differs is the slice each one watches, and how far
its verdict can be trusted. Under cheap generation the family earns more than it ever did: every dimension
closed mechanically is reviewer attention released for what only a human can judge.

The linter sits near the closed end. Its slice is the cost-relevant dimensions (naming, structure,
convention), the ones that never change what the program does and always change what it costs to read. No
test asserts them, so before the linter they were priced as review. And a project's lint configuration is
ratification at its cheapest: the convention nobody wrote down, enforced in review at review's price,
becomes a rule that fires on every draw.

The formatter sits past the closed end: it does not check its dimensions, it deletes them. Tools like `gofmt`
issue no verdict on layout; they rewrite every candidate into one canonical form, and a dimension that can no
longer vary needs neither aiming nor confirming.

The security scanner is pre-paid like the rest of the family, watching the intent every project shares without stating it
(no secret in the source, no query assembled from raw input), and it sits further toward the open end: its
'no' is often a suspicion rather than a violation. What may be safely fed back to the generator depends on
closure. The compiler's verdict is never argued with, the linter's rarely; a scanner's suspicion fed back as
if it were a clause is a promotion nobody examined, and a generator pressed to silence a suspicion will find
the cheapest silence, which is not always a fix.

## When intent moves

When a requirement changes on purpose, a description-facing oracle resists, because it
still polices yesterday's requirements. At the right strength that resistance is useful: the failing test
forces the team to acknowledge the change, and updating the test records that the change was intentional.

That resistance can be wrong in both directions. A test pinned to an accident (the exact wording of an
error message, the order of a list nobody sorted on purpose, the current shape of an internal structure)
fails on a harmless change as loudly as on a real regression. A test suite full of them resists everything.
Updating them costs more than the change is worth, so the change stops being made. The opposite failure is
updating the tests in bulk. Every red assertion gets rewritten to match the new output (regenerate the
snapshots, patch until the build is green, an agent's first instinct) without anyone asking, test by test,
whether the change was intended. A test suite maintained that way resists nothing, and one that never resists
is not checking the software; it is describing whatever the software currently does.

The design rule: match an oracle's rigidity to how settled the thing it watches is. Something that is done
changing belongs behind a rigid check (a type, a schema, a contract check), where the resistance is
structural and costs nothing, because no legitimate change is being resisted. Something still being
discovered belongs behind flexible checks, cheap to update when the next discovery arrives. Misaligned one
way, something still changing sits behind a rigid check and every change pays for formal re-authoring.
Misaligned the other way, something settled sits behind checks that a bulk update can erase, and that is
what the next section's generator learns to exploit.

## What an adversarial generator is

When generating is expensive, a candidate that fails the tests simply fails; retrying costs too much. When
generating is nearly free, retrying is the whole method: the generator keeps producing candidates until one
passes. The trouble is what "passes" means: a generator retrying against a fixed set of tests will converge
on whatever makes the tests green, and making the tests green is not the same thing as satisfying the intent
the tests were written to protect.

At today's retry counts (a handful of attempts, not an exhaustive search), this is a pressure, not a
realized disaster. But raise the retry budget and the share of candidates that pass the tests while
violating intent grows.

The mechanism is the one other fields call reward hacking, and anyone who has run a generation loop has seen
it. A model asked to make a failing test pass may hardcode the test's exact input rather than fix the logic.
A model asked to raise coverage may add assertions that execute a line without actually checking its
behavior. Both make the tests green. Neither moves the software closer to what the description asks for.

One layer of defense is independence: keep the tests' blind spots different from the generator's by deriving
the tests from the public contract, never from the implementation. This does not close any blind spot
(nothing can), but it keeps the generator's gaps and the tests' gaps from overlapping, so a cheap search has
a harder time finding a candidate that slips through both.

## Feeding the verdict back

Most generation workflows run a loop: a candidate fails a test, the failure message goes back to the
generator, and a new candidate comes out. The failure message is not just a signal to retry; it is a
specification. A failing test does not say *no*; it says *withdrawal amounts must be positive, and this one
was not*, and that sentence steers the next attempt exactly the way a clause in the description would.
Whether this is safe depends on how much the test can be trusted.

When the test is closed (well-established, its own correctness not in doubt), the message going back
restates something the project has already ratified. The retry is cheap and safe. When the test is open
(freshly written, its correctness still an open question), the message going back is a claim nobody has
examined. Feed back a test's *balance must be positive* and the generator will comply whether or not the
project's description ever said so. The loop quietly promotes an unexamined claim to governing status. And
if the red verdict actually meant that the test was wrong, compliance erases the disagreement instead of
resolving it. This is also where the adversarial mechanism bites: a generator that cannot find the real fix
can still hardcode the test's expected output.

Test-driven development ran this loop for decades, safely, because the person who wrote the test was
watching: they had just authored the assertion on purpose, they had the authority to say whether it was
correct, and they could tell in real time whether a red verdict meant the code was wrong or the test was
wrong. Automated generation keeps the loop and removes the person. The safety does not transfer.

Either way, the loop is only allowed to touch the candidate. Between one attempt and the next it can change
as little as a comma or start over from scratch, but everything it does stays inside a single generation
event, and discarded candidates leave no trace in anything versioned. That is what makes retrying cheap:
what gets thrown away is a candidate, not a deployed artifact, and nothing the project has learned is at
stake.

## The environment as the ultimate oracle

Production is the most authoritative oracle there is. Every input the software receives is a query: does
this behavior belong to the region intent wants? The two verdicts are not symmetric: a 'no' arrives with a
lesson attached, a dimension named, a silence exposed; a 'yes' carries one bit and no reasons. The 'no'
teaches; the 'yes' only steadies.

Its verdicts face intent directly, and are priced accordingly. Part of the price is the reporter: a test
delivers its 'no' automatically, while production's arrives only through monitoring, a support queue, or a
user who bothers to say so. A verdict nobody files is coverage the oracle only appears to have. The other
part is lateness: by the time production says 'no', the cost was paid there, not in a build.

Both prices can be engineered down, and a whole discipline — site reliability engineering and its
neighbors — does little else. Progressive delivery works on
the lateness, observability on the reporter. A canary, a feature flag, a staged rollout all buy production's
verdict a slice at a time: put the candidate in front of a bounded share of the world's queries, read the
verdict, and only then expose the rest. The verdict arrives before the full exposure instead of after it,
and what any single 'no' can cost is capped by the size of the slice. Monitoring and structured error
reporting raise the rate at which verdicts get filed instead of evaporating with an annoyed user. None of
it makes production a different oracle. It re-prices the same one, and every unit of that engineering also
cheapens the fresh draw, whose new defects get discovered at report prices instead of incident prices.

## Can an intent-facing oracle be built?

Production's verdicts are authoritative, late, and expensive, so the tempting project is a cheaper oracle
with the same authority. It cannot be built. To build is to author, and whatever is authored encodes its
author's understanding of intent, not intent itself. That is at least one lossy step, and it does not
disappear when the intent-holder does the authoring: the same stakeholder who signs the encoding will
reject behavior it permits, the day the encounter teaches them what they actually needed. Intent is
discovered on contact; an encoding is whatever it was on the day it was written.

Look at what actually gets built under this ambition. A demo judges nothing; it puts behavior in front of
the stakeholder, and the stakeholder judges. Monitoring judges nothing; it moves production's judgment to
someone who can act on it. A metric comes closest to a built judge, and it is the weakest of the three. It is an
authored guess that some measurable quantity tracks what the business wants. Where the guess is right, the
metric is an approximation; where it is wrong, it can be gamed — the number can be raised without serving
the intent it stands for. All three are worth building. None of them is the oracle; each carries a verdict somebody else
produced, or approximates one.

The verdicts that answer to intent with nothing authored in between are the reactions: a stakeholder seeing
the behavior and finding it wrong, production meeting an input nobody imagined. That is what makes them
authoritative, and it is also what makes them impossible to schedule. A reaction arrives when the encounter
happens, not when the build needs it.

## The distance from intent

An engineer interviews the stakeholder, misreads one requirement, and writes both the spec and the test
description from the misreading. Everything that follows agrees: the implementation conforms to the spec,
the tests pass, the reviewer approves. The error is invisible to the entire pipeline, because the spec is
where all the other verdicts come from, and an error that precedes the spec is inherited by everything
derived from it.

Cheap generation makes this worse. Tests and builds take minutes; a demo with the stakeholder or a real
user encountering the software takes days or weeks. When implementation was slow, the two kinds of verdict
arrived interleaved, and a misreading could surface halfway through. Now every mechanical check passes
before the first real encounter happens. The wrong product arrives finished, tests green, review approved.
Completeness is evidence of conformance, not evidence that anyone asked for what got built.

There are two remedies. The first removes the distance entirely: the person who needs the software builds
it. Every use is a direct verdict, and no misreading can precede it, because nobody translated anything.
That is part of why tools built by their own users are so good. The second keeps the translation but
insures it: the project needs at least one verdict that does not come from the same understanding the spec
was written from. In the scene above, almost anything would have worked (a demo with the stakeholder, a
second interviewer, the stakeholder reading the test description) as long as it did not depend on the
engineer who misread.

The known ways of buying that insurance differ in honesty. Making the stakeholder write the specs directly
has been tried. Behavior-driven development promised stakeholder-authored tests and mostly produced Gherkin
written by engineers, intermediary work formatted as stakeholder work. That is worse than honest distance,
because the project believes it holds insurance it does not hold. The affordable version is review instead
of authorship: the stakeholder reads the test description, the one encoding written in the language of
observable behavior, and every scenario that survives the reading becomes a mechanical check that fires on
every future draw. The reading only covers what is written (the scenario nobody wrote is not there to
object to) and it goes stale as intent moves, so it has to recur. It is still the cheapest insurance
there is.

## The LLM-as-judge proposition

A model can be asked to judge candidates: give it the candidate and a judging prompt, and it returns a
verdict at the price of a draw. The pitch is that this relieves the regime's bottleneck: generation is
cheap and verification is expensive, so make verification cheap too. Part II already showed why that
promise fails. A cheaply manufactured verdict does not remove the cost of checking; it defers it, and the
deferred cost comes back as defects nobody caught. So the useful question is not whether the judge's
verdicts are cheap. It is where the information in them comes from.

The judge has exactly two real sources of information, and both are already in the apparatus. Tools do not
add a third: a judge that runs the test or consults the checker is, at that moment, relaying the oracle it
invoked — the verdict is the tool's, and carries the tool's trust, not the prior's. The first is
the prior. Training distilled decades of code that survived review and use, so the judge knows what
conventional code looks like — the same territory the linter and the scanner cover, without pre-written
rules and at the price of a draw. The second is the judging prompt: whatever the description declares, the
judge checks against. But notice the modality. A clause in a test is the description in executable form:
it decides. The same clause in a judging prompt stays in stated form while being asked to do executable
work. The judge reads it the way the generator reads the spec, statistically, and can apply it wrong on
any draw. So writing judge prompts instead of tests trades a verdict for an aimed opinion, at the same
authoring cost. That is a losing trade wherever a test could have been written. It is the only option on
the dimensions no test reaches — the diffuse ones, the cost-relevant ones — where the judge competes with
the reviewer rather than with the test suite. And everything neither source paid for — the silences of the
delta, the dimensions nobody wrote down — the judge resolves from the prior and formats as a verdict. That
is not confirming; it is the generator's own move, performed a second time.

This pins down exactly where the judge is blind. Judge and generator are trained on much the same code, so
their competences are heavily correlated: where the prior is strong, both do well; where it is weak, both
fail together; and no weakness of the generator is ever covered by the judge. What is not correlated is the
luck of a single draw. A generator's slip — the off-by-one, the unhandled empty case — is by definition an
improbable outcome under the prior, and the same prior that made it unlikely to write makes it easy to
spot when reading. The reading is a draw too, with luck of its own — but its luck is independent of the
luck that produced the slip, and flagging improbable text asks less of the prior than producing probable
text one token at a time. So the judge recovers sampling accidents.

By the same mechanism, the judge certifies the errors that matter most. A silence resolved by the wrong
convention was a probable draw, and it reads as natural to the very prior that produced it. The judge's
detection rate on an error is proportional to how improbable that error was: it catches exactly the bugs
that were rarest, and misses exactly the underspecification that cheap generation produces most. This
leaves the judge a niche, and a shrinking one. It is the prior consulted in reader mode, sweeping up
whatever the compiler, the linter, and the tests did not already close — a stream of slips that thins with
every generator that slips less.

What each of the two verdicts is worth follows from this. A 'pass' is worth nothing: it means the prior
found nothing unusual in output that the same prior shaped. Treat that 'pass' as a real verdict and you
have manufactured the deferred defect. A 'no' is worth something under one condition:
it must be pointed enough to check in a minute. A diffuse suspicion costs the reading it was supposed to
spare, and noisy 'no's train their readers to ignore the real ones. So the judge is a discard filter,
never an approver. And its unreliability is not the kind that closure repairs: the candidate is text that
speaks to its reader, and confident comments, assertive names, and the style of correctness all sway what
is, in the end, an opinion about plausibility. A test is gamed only by finding its blind spot; a judge can
be persuaded.

This is why the judge fails worst at exactly the scale it was hired for. Part II showed that volume grows
to match the verdict's price. Verification was the brake, and the brake was also the discipline: when a
verdict costs something, a candidate has to be worth judging, and that is what pushed effort into the
description and into every filter that runs before the expensive verdict is spent. Cheapen the verdict and
both effects disappear at once. Generation expands to the new ceiling, and the incentive to aim before
generating expires, because a candidate no longer has to be worth anything to be judged. Worse candidates
arrive in greater numbers at a cheaper gate. And against a cheap, partial oracle the generator becomes
adversarial — with an advantage it never had against tests. Against a test, an adversarial search has to
find the blind spot. Against the judge it does not even have to search: generator and judge share the
prior, so the generator's most natural output is already what the judge finds plausible. The gaming comes
built in. Even triage — the judge ordering the queue for scarce human attention — survives only while the
human verdict remains the destination. At a thousand candidates and fifty readings, the nine hundred and
fifty nobody reaches are certified by passes worth nothing. That is not the judge misused. That is the
equilibrium of the judge used as sold: an instrument that cheapens the verdict finances the volume that
overwhelms it, dissolves the discipline that made candidates worth judging, and delivers the deferred cost
at the scale the cheap verdicts built.

[← Part III](/theory/part-iii) · [Table of contents](/theory) · [Part V →](/theory/part-v)
