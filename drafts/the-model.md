# A (peculiar) theory of software engineering

## A stronger claim

The previous two essays described properties of generating code
with a stochastic model. [Collapsing the wave function](/articles/collapsing-the-wave-function)
argued that a spec run through a non-deterministic executor defines
a space of programs, not a program, and that the artifact is the
record of which point in that space you landed on. [Many worlds](/articles/many-worlds)
argued that the record is worth keeping, because the defects of one
program are a bounded, shrinking set, and the defects of a
distribution of programs are not.

Read on their own, both essays sound like observations about AI.
This page makes a stronger claim: **none of it is about AI.**
Software engineering was always the activity of sampling programs
from a space defined by an intent nobody fully wrote down. AI did
not introduce the sampling. It changed the price of a sample, and
three prices next to it — and every doctrine the field has ever
held about specs, documentation, rewrites, and technical debt turns
out to be the correct answer to a cost structure that has now
changed underneath it.

## The primitives

Strip the argument to its parts, and none of them require a model.

There is a **space of programs**, every artifact expressible in
some language. There is an **intent** — what the system is actually
supposed to do, held by the people who need it, known incompletely
even to them, revised on contact with reality. There is a
**description** — any artifact that narrows the space to a region
of acceptable programs: a spec, a test suite, a type system, a
ticket, a convention nobody wrote down but everybody enforces in
review. A description does not have to be versioned to count; it
only has to constrain. There is a **generator** — any process that,
given a description and a context, produces a program: a
distribution over the space, sampled once per invocation. There is
an **oracle** — the partial procedure that decides membership in
the region, on whichever dimensions it happens to cover. And there
is the **artifact** — the point you actually have, carrying
whatever evidence of survival it has accumulated: tests it has
passed, traffic it has served, incidents it has not caused.

Two of these roles are easy to conflate, and the conflation causes
real damage downstream, so it is worth separating them here. The
description's job is **aiming**: it conditions the distribution the
generator samples from, shifting probability mass toward the region
it describes. Aiming is statistical, not geometric — a good
description makes landing outside the region unlikely, not
impossible, and the generator remains free to miss on any single
draw. The oracle's job is **confirming**, and confirming is bounded
rather than statistical: it decides membership by checking specific
properties, on whichever dimensions someone built it to check.
Passing every check an oracle can run is not the same proposition as
belonging to the region the description actually defines — it is
the narrower claim of belonging to the intersection the oracle
happens to observe. Outside that intersection there is no
confirmation available, only the aim, and whatever confidence the
aim earned.

Four operations act on these parts. **Sampling** produces a fresh
point from the generator. A **local move** — a diff — nudges the
existing point without resampling the rest. **Resampling** discards
the point and draws a new one, independent of the last. And
**ratifying** moves a discovery from the environment into the
description or the oracle, so that future samples are constrained
by it rather than merely lucky with respect to it. Underneath all
four sit three costs that determine which one is rational at any
moment: the cost of generating a candidate, the cost of checking it
against the oracle, and the cost of the next local move once you
are standing on a given point — call it the cost of the local
gradient.

That is the whole apparatus. Nothing in it names a model, a
programming language, or a decade.

## What a bug is

Given these primitives, a bug has a precise shape, and it comes in
two kinds worth telling apart, because the remedy differs.

The first kind is **nonconformance**: the description spoke on the
dimension in question, and the sample still landed outside the
region it defined. This is a plain generation error — the aim was
adequate, the draw missed anyway. A faithful oracle catches it by
construction, provided the oracle happens to check that dimension.
When it slips through regardless, the gap is in the oracle's
coverage, not in the description.

The second kind is the more interesting one, and it is what most
bugs discovered in production actually are: **underspecification**.
The description never spoke on the dimension at all — a legitimate
silence, since no description of useful size closes every degree of
freedom — and the generator resolved it however its own priors
favored. Most such resolutions are harmless: the behaviors
compatible with the silence mostly overlap with what the
stakeholders would have accepted, had anyone thought to ask. A bug,
on this reading, is the case where the coin landed on the side
nobody wanted: the sample is fully conforming to everything the
description said, and still wrong, because the description never
said enough to rule the outcome out. This is the same mechanism
[Many worlds](/articles/many-worlds) describes as luck that can run
out on a re-roll — a bug is what that luck running out looks like
from the inside, the first time it happens.

The distinction changes the fix. Nonconformance is repaired by
extending the oracle to catch the violation mechanically, or simply
by generating again against the same description, since the
description was never the problem. Underspecification is repaired
by narrowing the description along the dimension the incident just
revealed — and, in the same motion, teaching the oracle to check it,
so the fix holds even after the code that currently embodies it is
gone. This is the operational meaning behind "the fix goes into the
spec and a test pins it": one clause narrows the aim, the other
closes the oracle's blind spot on that one dimension, and only doing
both makes the correction durable across a resample instead of true
only of the current point.

This also sharpens what **ratifying** means as an operation. It is
not merely recording a decision after the fact. It is retroactively
narrowing the region a description defines, to exclude a point a
sample just proved was reachable and unwanted — intent, discovered
in arrears, folded back into the description that was supposed to
have contained it from the start.

## The battlefield assumption

Push the model to a limit case, exactly to see what it assumes
without saying so: grant a perfect oracle — no blind spot, deciding
any sample against true intent, for free. The space of programs
does not shrink because the oracle got better. Even restricted to
strings over a finite alphabet it stays infinite, so the set of
points that fail intent stays infinite too. No finite description
can pre-empt an infinite defect surface — enumerating every way a
program could go wrong would make the description as long as the
space it describes, which is the same wall
[Collapsing the wave function](/articles/collapsing-the-wave-function)
already names as the reason a description leaves room for silence
in the first place.

So "eliminate every bug in the spec" is not a high bar nobody has
cleared yet. It is not a coherent goal, oracle or no oracle. What
happens instead, in every real project, is narrower and more
honest: pick a point — the artifact currently deployed — and the
neighborhood reachable from it by local moves, and treat that
neighborhood as the battlefield. Fix what surfaces there, one
defect at a time, and stop asking whether the description covers
the rest of the space. This is Herbert Simon's satisficing, fought
geographically: not "find the best program the space permits," but
"find a program in this basin good enough to stop looking for a
better one," treating each local correction as a step toward that
point rather than toward a global correctness nobody can reach.

Fighting this way rests on an assumption nobody states, because
most basins honor it well enough that stating it never seemed
necessary: *every basin entered contains a point good enough,
reachable by successive local corrections, at a cost that stays
acceptable.* Technical debt — the local gradient's cost rising over time — is
what it looks like when this assumption starts to fail. As long as
it holds, staying and fighting is the right call.

That confidence deserves a caveat before it hardens into proof. The
strongest evidence usually offered for it is exactly the wrong kind:
most software anyone can point to was built this way, therefore the
assumption must generally hold. This is the same selection effect
the rest of this page names for a different population — a live,
maintained codebase is observable precisely because it satisficed; a
project that never found its basin's good-enough point does not
stick around to be cited alongside it. It gets rewritten, which the
old doctrines already treat as the rare, expensive exception rather
than a disproof; or abandoned, which leaves no artifact to point to
at all; or left running in a permanent, expensive, never-quite-
acceptable state that nobody offers as a positive example even
though it is evidence the assumption failed there. The population of
systems available as examples is already conditioned on having
satisficed — the identical structure "Many worlds" uses to explain
why a stable artifact's untested dimensions look safe: they look
safe, or here the assumption looks true, only because the
counter-examples are the ones nobody gets to see. The honest
position is narrower than "the assumption is probably true." It is
that failure is visible after the fact, in technical debt and drift,
and there is no way to know in advance, for a specific basin,
whether it holds.

## The region is never a point

Push the same limit case one step further, in the other direction.
The battlefield assumption argued that a finite description, however
detailed, cannot pre-empt an infinite defect surface: the points
that fail intent stay unbounded no matter how much the description
says. The same argument runs in reverse, and it is worth stating
plainly, because it answers a question this page has left implicit
since [Collapsing the wave function](/articles/collapsing-the-wave-function)
opened with it: does a spec ever define a single program?

Not even a perfect one does. Some of the infinitude inside a region
is trivial: renaming a variable, reordering two independent
statements, swapping a standard-library call for an equivalent one —
differences that do not correlate with behavior at all, and there
are infinitely many ways to make changes like that to any single
program. This part of the multiplicity is free, in every sense: it
costs nothing and buys nothing, because nothing that matters rides
on it.

But there is a second, non-trivial multiplicity underneath the
trivial one, and it exists for the identical reason the defect
surface stays infinite: intent is a finite object, stated by finite
people in finite time, and a finite set of constraints on a space
with effectively unbounded dimensions cannot close all of them — not
because anyone was careless, but because there is always another
dimension nobody has reached yet. Error handling for an input nobody
has tried. Behavior under concurrent access nobody described. The
order two independent side effects fire in. None of this is a
defect, if intent never had an opinion on it — and none of it is
fully resolved either. A program that logs verbosely and one that
logs nothing can both satisfy the identical, fully-articulated
intent, and there is no sense in which the spec, however complete,
prefers one.

So the two questions collapse into one answer. Yes: a finite
description, run against a space this large, is always satisfied by
infinitely many programs — part of that infinitude free and
meaningless, the rest genuine and unresolved, for exactly the reason
an infinite defect surface survives even a perfect oracle. A region
is not a fallback for specs written carelessly. It is what any spec
is, always, because the space has more room than any finite intent
can fill. Collapsing to one point is not the spec settling a
disagreement it failed to see. It is sampling doing the one thing a
finite description structurally cannot: choosing.

## Not every free dimension is free

Naming sits inside the trivial multiplicity described under "The
region is never a point," and it is the place that multiplicity is
hardest to accept as harmless. Phil Karlton's list of the two hard
problems in computer science — cache invalidation and naming things
— puts naming next to a genuinely hard correctness problem, and the
model built so far has no room for that: renaming a variable does
not move the artifact to a different point in the behaviorally
relevant sense, no region gets narrower or wider because of it. If
naming only mattered for behavior, it should be free in every sense
that matters, and it plainly is not.

The gap is real, and closing it needs a distinction the model has
not made yet. A dimension can be free with respect to the region —
it never changes which programs count as conforming — while
remaining expensive with respect to everything else the model
already counts as a cost: the price a human pays to confirm a
candidate, the price a future editor pays to aim correctly at what
still needs deciding, the price of a local move accidentally landing
on a dimension that does matter. Naming is exactly this kind of
dimension. It never touches the region a description defines. It
constantly touches how cheaply anyone — human reviewer, future
engineer, generating agent reading its own prior output — can tell
a load-bearing dimension from an inert one before touching it.

This gives naming a precise role instead of a vague virtue. A good
name is a nearly free signal that narrows, locally, the exact
uncertainty already named as fundamental under "Why refactors hurt":
nobody can compute this space's metric cleanly enough to know in
advance which moves are free. A name that says what a value is for,
or why a branch exists, is a tiny fragment of aim embedded directly
at the point future confirmation and future movement will need it
most — cheaper to write than a spec clause, read exactly when it
matters, by whoever is about to make the next local move. A bad name
spends the same free dimension for nothing, or worse: it misdirects
the very judgment call — free or load-bearing? — that this page has
already shown carries real cost when it fails.

The model's account of dimensions needed a second axis all along,
and the maxim is what surfaced the gap: **behaviorally free** and
**cost-relevant** are independent properties, not the same property
under two names. Most dimensions are low on both. A few — the traps
that carry outsized, catastrophic sensitivity — are high on the
first. Naming turns out to be high on the second while staying at
zero on the first, and nothing before this section had room to say
so.

## Diminishing returns on aim

There is a mechanism worth making precise, because the intuition
behind it is right and the usual way to state it — counting how much
of the space a rule "removes" — breaks down the moment the space is
infinite. Subtracting infinite sets from infinite sets does not say
which rule mattered more; infinity minus infinity is not an answer.
The quantity that actually behaves the way intuition wants is not a
count of excluded programs. It is probability mass under the
generator's own distribution — the same notion of aim already
established: a rule's power is the probability that the generator,
left unconstrained, would have violated it.

Stated this way, the overlap argument holds rigorously, by a fact
from probability rather than a leap of intuition: the chance of
violating at least one of several rules is at most the sum of the
chances of violating each on its own — Boole's inequality, the same
union bound the intuition was already reaching for. Two rules whose
likely violations overlap — a null check that would also have caught
a concurrency bug, a type constraint that would also have ruled out
a malformed input — buy less together than the sum of what they buy
apart, and a spec that keeps adding rules in the same well-trodden
neighborhood of the space runs into this quickly.

But overlap alone does not yet explain why the *first* rule written
tends to buy the most — that needs the failure modes a spec could
name to be unevenly sized, and they generically are. A handful of
ways a program goes wrong are common enough that almost any
unconstrained sample trips one of them; the rest are individually
rare, tripped by a small fraction of samples each, in a long tail
with no end. State the common failure modes first — which is what a
competent spec, or even an inattentive one, tends to do, since those
are the failures anyone would think to mention — and every rule
after the first has to work with what the earlier ones already
excluded: the largest, easiest-to-name chunks of probability mass
are already spoken for, and what is left to a later rule is
disproportionately the long tail, one thin slice at a time.

This is not a law forced by the structure of the space. It is what
happens when rules are written in the order people actually write
them, and it gives the battlefield assumption its economic twin.
That section argued a spec cannot eliminate every bug, no matter how
much is written, because the defect surface stays infinite. This
adds the reason not to try: the marginal rule costs about as much
effort to write and review as the one before it, and buys steadily
less. Satisficing is not only what a finite description is forced
into by an infinite space. It is also, separately, the point where
writing more spec stops being worth what it costs — and the two
reasons happen to point at the same place.

## Where the crossover sits

Diminishing returns on the spec side pairs with a cost that does
not fall nearly as fast: writing, reviewing, and carrying a rule
costs about the same whether it guards against a common failure or
a rare one — if anything, cost tends to rise on the way into the
tail, because rarer failure modes are also harder to think of in the
first place. Put a shrinking marginal value against a flat or rising
marginal cost and there is a crossover point, in principle even if
never as a clean number: keep adding rules while the next one's
probability-weighted value exceeds what it costs to write and
carry, and stop once it does not.

Nothing about that crossover point is fixed. It moves with the cost
of what a surviving failure would do. A rule's value is not just the
probability mass it removes — it is that mass multiplied by what
happens if the excluded failure occurs anyway, undetected. Where a
failure costs an afternoon of cleanup, most of the long tail is not
worth a rule; the crossover arrives early, and the residual,
unaddressed infinity this page has already conceded is unavoidable
gets left standing, cheaply. Where a failure costs a plane, or a
misdosed patient, or a regulator's attention, the identical
probability-weighted calculation justifies chasing the tail much
further, because the cost term on the other side of the
multiplication is enormous. This is not a new observation for this
site: the [rationale](/rationale) already names aviation and medical
devices as the two domains where specification-heavy methods
survived, for the reason it states in different words — an external
force pays the cost of rigor. What this page adds is the mechanism.
Those domains do not run on a different mathematics of diminishing
returns. They carry a different multiplier on the value side of the
same crossover, which moves where stopping becomes rational without
changing the shape of the curve that produces it.

## Why resampling forever does not scale

This crossover has a direct consequence for a strategy that looks
attractive precisely because generation got cheap: discard the
artifact and regenerate from the spec on every change, rather than
patch. [Many worlds](/articles/many-worlds) already priced this in
terms of the defect set — each regeneration is a fresh sample,
exposed to a fresh set of bugs, and the maturity a stable artifact
accumulates never gets the chance to compound. The crossover point
adds the reason the obvious fix — write a spec large enough that any
sample from it is safe — does not rescue the strategy.

Making repeated, independent resampling safe requires the spec to
pre-empt the failure modes a fresh sample could land on, and that is
exactly the job diminishing returns makes expensive precisely where
it matters most: not the common failures, which any reasonable spec
already covers, but the long tail a stable artifact would otherwise
leave dormant for free. Resampling on every change asks the spec to
do, on every single cycle, the job a stable artifact only has to do
once, gradually, at the pace incidents actually arrive — it pays the
full price of comprehensive specification repeatedly instead of
amortizing it the way a battlefield does, one bug ratified once and
closed forever.

The oracle can substitute for some of this, and it is generally the
cheaper lever: a test confirms mechanically where a spec clause can
only shift probability, so a unit of writing effort buys more
certain protection as a test than as prose, which is exactly the
asymmetry between aiming and confirming established earlier. But the
oracle is not exempt from the same underlying scarcity — it too
meets common failures first and a long tail after, for the identical
reason a spec does. Resampling forever is maladaptive not because
resampling went back to being expensive — it never did — but because
the safety net a spec or an oracle can economically provide keeps
the same shape it always had: dense where failures are common, thin
in the tail. Cheap generation does not change that shape. It only
changes how often the strategy asks you to jump without one.

## Why pinning a defect pays off locally, and mostly locally

Every fix made this way, per the earlier taxonomy, gets ratified
into the oracle: a test is added that would have caught it. That
test extends what the oracle can *confirm*; it does not necessarily
narrow what the description *aims* at.

The value of that addition is not uniform across the space. It is
highest inside the battlefield actually being fought on, and close
to zero outside it — the defect a test catches is, by construction,
a property of the region you are in, not of the whole space the
description permits. Resample into an unrelated implementation of
the same intent, or redesign away the mechanism that produced the
defect, and the pinned test may simply never fire again — not
because it is wrong, but because the trap it was built to catch no
longer exists where you have gone. Spent effort, collected value
zero, with no way to know this in advance for any single test.

The practice pays off anyway, on average, because of locality in
the specific sense already established: most dimensions of the
space do not correlate with behavior, but a few carry outsized,
catastrophic sensitivity, and a local move is far likelier to
perturb one of those few than an independent resample is to land on
the same one by chance. A diff operates on the same representation, often near the very
lines that caused the defect the first time. The description could
carry a warning there too — narrowing it is literally the other
half of ratification, from the section above — but a warning
written into the description only shifts probability; it does not
bind. The same asymmetry between aiming and confirming noted
earlier holds without exception here: a constraint stated plainly
in the chain still only conditions what the generator is likely to
do, and the site's own record already shows probability falling
short of compliance — the
[disposition mechanism](/journal/telling-the-agent-what-changed)
exists precisely because a spec stating a change in full, present
in the chain, was once not enough on its own to stop an agent from
anchoring on the two words it should have overridden. And even
where the description's warning lands, it only acts at the moment
the whole chain is walked again; it says nothing to an edit that
touches the artifact without regenerating from the tree at all. The
oracle's warning fires on any edit, whether the chain was consulted
or not, generated in full or patched by hand. A pinned test is what
is left standing in the gap the description cannot close alone —
not because prose is forbidden there the way comments are forbidden
in the code, but because prose, however present, only ever aims. It
stands for exactly as long as the fighting stays on the same
battlefield — which is to say, for exactly as long as it is likely
to be needed again.

This also names the failure symmetric to underspecification, and it
deserves the same honesty. A test can pin more than intent. Written
against the current point's specific shape rather than the contract
it is meant to protect, it freezes an accident as if it were a
requirement — dead weight the moment the battlefield changes, and
worse while it lasts, because it can veto a future, better
resolution of an ambiguity a next generation would otherwise be
free to answer on its own terms. A test written from the contract a
description makes, not from what the current implementation happens
to do, is a test about intent, and a test about intent keeps its
value on every battlefield that intent still governs. A test about
the accident only ever had value on the one battlefield where the
accident happened to occur.

## The environment as the slowest oracle

There is a layer of the oracle worth naming on its own, because it
explains something the earlier sections left implicit: production.
Every input the software actually receives, over its whole
operating life, is a draw from a distribution nobody wrote down and
no test suite enumerated in advance — and each one is, functionally,
a query to the oracle: does this sample belong to the region intent
actually wants? Most queries return an unremarkable yes and change
nothing. The ones that return no are incidents, and each incident
triggers a fix that, per the taxonomy above, narrows the description
and extends what a cheaper, faster layer of the oracle — a test —
can now catch without waiting for production to ask again.

Read this way, a program's maturity is not a metaphor and not
merely age. It is the accumulated record of one specific, very
expensive, very slow oracle — the world, running the software —
having fired against the artifact repeatedly, with every firing
ratified back into a cheaper layer so it need not fire the same way
twice. This is what [Many worlds](/articles/many-worlds)'s claim,
that a codebase's defect set only shrinks, reduces to under this
model: a report of how many times the most expensive oracle has
already been consulted, and paid for. Production sits above types,
property checks, and example-based tests in both coverage and cost
per verdict, and it is the only layer that checks against intent
directly rather than against somebody's earlier, necessarily
incomplete encoding of intent into a cheaper check. That is also its
danger: by the time it returns a no, the cost was already paid in
production, not in a build.

## When the target itself moves

Everything so far has quietly assumed that intent holds still while
the artifact catches up to it — that underspecification is a gap
waiting to be closed, and each ratification permanently narrows the
distance to a fixed point. That assumption is convenient and false.
Intent is not filed away complete somewhere, waiting to be read
correctly; it is held, partially, by people who revise what they
think they want as they watch the software behave, as the market
around them changes, as a regulator publishes a new rule. The
target most software chases is not stationary. It drifts, for
reasons that have nothing to do with anyone's mistake.

This has to be told apart from underspecification, because the two
look identical from inside a single incident and demand different
remedies. An underspecification bug is intent that was already
fixed at the time the description fell silent — closing the gap
resolves it for good, because the target had not moved. Drift is
intent that has genuinely changed since the description last spoke
— closing today's gap resolves nothing, because tomorrow's incident
will report the same code failing against a target that is, by
then, somewhere else. Treating drift as underspecification produces
a description that keeps narrowing, with great discipline, toward
where the target used to be.

Drift is a second way the battlefield assumption can fail, distinct
from technical debt. Technical debt is the destination standing
still while the road there gets more expensive with every step.
Drift is the road staying cheap while the destination keeps moving —
every fix lands, and the software is still wrong, not because the
fixes failed, but because they were aimed at where intent stood two
fixes ago. No amount of local-gradient efficiency compensates for
chasing a target that outpaces the chase; the signal here is not
cost rising, it is convergence never arriving despite cost staying
flat.

None of this is new to people who build software — it is the
oldest complaint in the discipline, usually filed under
"requirements churn" or "the client doesn't know what they want."
What the model adds is the reason that complaint is not a failure
of discipline: intent is discovered by contact with a working
system as much as it is specified ahead of one, and a description,
however carefully authored, is a snapshot of intent at the moment
someone was willing to commit it to writing — not a claim that
intent will hold still afterward. Naur's theory-building has the
same shape once more: the theory a team holds is not static either,
and the reason it degrades on turnover is the same reason a
description degrades under drift — the holder of the current, truer
version of intent is a person, not an artifact, and the artifact is
only ever as current as the last time someone updated it from them.

There is a natural instinct worth stating so it can be qualified:
survivor evidence only grows more valuable with age, so the case for
resampling only weakens over time. That holds when intent stands
still. Under
drift, an old artifact's survivor evidence is evidence about a
target that no longer fully exists — still valuable, but
depreciating on a schedule nobody here has found how to write down.
This is left as an open question rather than resolved: how to tell,
in practice, whether a given incident is asking for a local
correction or announcing that the target has moved far enough to
make the whole battlefield the wrong one.

## Outrunning drift

The open question above has at least a partial answer, and it comes
from the same place as the rest of this page's reversals: cheap
generation. Chasing drift through successive local diffs means
walking the terrain between the point currently held and wherever
the new intent's optimum now sits, and that walk inherits every cost
already named twice over. The local gradient gets steeper the
farther the walk has to go, because a diff is built to make a small,
bounded change, not to reshape an artifact's structure wholesale.
And the walk runs straight through the regime where anchoring does
the most damage: the more intent has moved, the more the existing
artifact — read alongside the spec by the very mechanism
[Anchoring on old code](/journal/anchoring-on-old-code) documents —
resists exactly the range of change now required. Drift large enough
to demand reshaping architecture, not adjusting a rule, is drift
large enough that local correction may cost more than starting over.

Starting over, though, is no longer the operation it used to be.
Re-specifying is cheap — rewriting a description in natural language
to match a new understanding of intent costs a fraction of what
re-architecting the same amount of behavior by hand would. And once
the description is rewritten, generation is cheap too: a fresh
sample from the newly defined region, not a walk toward it. The
comparison that decides whether to patch through drift or resample
past it is therefore no longer patch-cost against the sunk survivor
evidence alone, which is the calculus given earlier for the ordinary
case. It is patch-cost — inflated by how far intent has moved and by
the anchoring risk of dragging old structure along — against the
cost of re-specifying plus a fresh sample. That comparison used to
favor patching almost by default, because generation was the
expensive side of it. Whether it still does, in a given case, is no
longer something the old default can answer for you.

This gives the earlier open question a criterion, if not a number:
drift is a signal to resample rather than merely patch exactly when
the distance it has moved exceeds what a local diff can absorb
without inheriting anchoring's failure mode. That criterion should
be read for what it is — a sharper restatement of the tradeoff, not
a resolution of it, because both sides of the comparison are exactly
the unmeasurable quantities this page has conceded throughout:
nobody has a number for how far intent has moved or how much a local
diff can absorb before anchoring takes over. What changed is not
that the comparison became computable. It is that the collapse in
generation cost moved the break-even point somewhere a team now has
to actually go look for, instead of a point so far away that the old
default could stand in for the answer without anyone checking.

One assumption in this comparison deserves the same scrutiny "The
battlefield assumption" already turned on itself: "generation is
cheap" has been quietly carrying "and generates something adequate"
alongside it, and those are different claims. A cheap resample that
lands somewhere worse than the patch it replaced is not a
counterexample to anything argued here, but nothing in this section
establishes that cheap generation is competent generation — only
that it is cheap. Where that gap is wide, the comparison this
section makes should be read as conditional on it closing, not as
evidence that it already has.

## Why refactors hurt

A refactor is meant to be free: change structure, preserve behavior,
move along the dimensions established earlier as inert — rename,
reorder, redecompose — without touching the few that carry real
weight. In principle this is the trivial multiplicity described earlier,
under "The region is never a point": motion within a single
behavioral equivalence class, at zero cost, because nothing
observable is at stake.

In practice a refactor of any size rarely stays that clean, for the
same union-bound reason that explained diminishing returns, run
in the opposite direction. That argument showed the chance of
tripping at least one of several failure modes grows, not shrinks,
the more of them a single move puts at risk. A small diff touches
one or two lines and has correspondingly little chance of brushing a
load-bearing dimension nearby. A refactor spans the whole structure,
and even when every individual step aims to move along an inert
dimension, the sheer number of steps raises the cumulative chance
that at least one of them nicks a dimension that was never inert to
begin with — the boundary between the two kinds of dimension is not
always visible in advance, and no one can compute this space's
metric cleanly enough to know for certain, before moving, which
moves are free. A refactor large enough to be worth doing is, by the
same arithmetic that makes a long spec chase a shrinking tail, large
enough to be likely to cross a dimension that mattered.

There is a second cost, independent of the first, and it is
arguably the one that actually hurts: refactors are typically
verified once, at the end, rather than checked after each of the
many moves that make them up. When something breaks, the
bounded-versus-unbounded distinction
[Confinement](/journal/confinement) already draws applies at a scale
this page has not needed until now — the failure is not localized to
one small, reviewable change, but to some point inside a large
bundle of simultaneous ones, and finding which one requires exactly
the archaeology a confined, incremental process exists to avoid.
This is not an argument against refactoring. It is the reason a
refactor checked only at the end costs more, in expectation, than
the same total distance covered as a sequence of smaller moves, each
confirmed before the next begins — the same principle the
[disposition mechanism](/journal/telling-the-agent-what-changed)
already applies to regeneration, holding just as well for a change
nobody thinks to call a regeneration at all.

None of this is the whole account, and leaving it there would make
the same mistake in reverse. Refactoring is usually undertaken
specifically to lower the local-gradient cost of every move that
comes after it — the entire point of paying the risk described above
is to buy a cheaper future battlefield, the technical-debt payoff
"Old doctrines" already named. A fair expected-value comparison
weighs the union-bound risk taken on today against the
local-gradient cost avoided on every future move this artifact will
need, discounted by how many such moves are actually coming — for a
component near the end of its useful life that future term is small
and the refactor is a bad trade even before its immediate risk is
counted; for one with years of active change ahead of it, the same
future term can dwarf the risk and make the refactor the right call
by a wide margin. The case made above is a case about how to pay a
cost that is frequently worth paying, not a case against paying it.

## Decomposition as a lever

Reuse, componentization, and object-oriented encapsulation are
usually taught as hygiene: don't repeat code, group related things,
hide what callers should not see. Read under this model they are
something sharper — a decision about where to draw the boundaries of
a battlefield, made because the primitives introduced early on apply
recursively. A component is not a metaphor for a smaller version of
the system; it is a smaller instance of the exact same structure —
its own intent, its own description, its own generator, its own
oracle, its own artifact accumulating its own survivor evidence —
nested inside a larger one.

Once decomposition is seen this way, **reuse** stops being only a
convenience for the author and becomes a decision about how many
independent samples a subproblem gets drawn as. Solve a subproblem
once and reference the single result from every call site, and there
is one artifact, one description, one oracle to ratify fixes into,
and one accumulating record of survival. Solve it independently at
each site instead — copy the logic, or re-derive it from scratch at
every point that needs it — and each copy is its own draw from the
space, with its own uncorrelated defect set, exactly the situation
[Many worlds](/articles/many-worlds) already described as the cost
of resampling, triggered here by duplication instead of by time.
Reuse also accelerates maturity in a way plain deduplication does
not get credit for: every call site's traffic funnels through the
same point, so the shared component meets the environment acting as
oracle far more often, per unit of calendar time, than any one of
the duplicates would alone. Solved once and shared, a subproblem
matures at the rate of the whole system's use of it. Solved N times,
it matures N times more slowly, N times over, with N different
defect sets to eventually discover.

**Encapsulation** — hiding a component's internals behind a public
contract — is a defense against Hyrum's Law, drawn structurally
rather than promised by convention. Hyrum's Law bites on whatever is
observable; a caller cannot ratify a dependency on a resolution it
cannot see. This is the identical mechanism the site's own
[Verification](/#verification) already relies on for a different
reason — a test spec generated from the public contract, not the
implementation, cannot inherit the implementation's accidental
choices — and [Confinement](/journal/confinement) applies the same
restriction to what a generation agent is allowed to see of the rest
of the tree. Encapsulation, confinement, and contract-only test
generation are the same move, applied to three different observers —
callers, generation agents, verification agents — all kept from
ratifying, accidentally or otherwise, a resolution nobody meant to
promise.

**Single Responsibility** is the version of this that narrows the
battlefield itself rather than just its visibility. A component that
owns one concern has fewer behaviorally load-bearing dimensions
bundled into one region, which moves three things in its favor at
once. Diminishing returns on its spec arrive sooner and cheaper,
because there is less to cover before the easy, high-value rules run
out — a small, focused description can approach its own crossover
point at a fraction of the size a tangled one would need. Drift is
contained: when the stakeholder concern behind one responsibility
moves, only the component that owns it needs to be re-fought or
re-specified, and the survivor evidence accumulated by every
unrelated component stays untouched, instead of an entire artifact's
maturity being put back into question because one dimension, buried
in a much larger bundle, had to change. And the union-bound risk
described under "Why refactors hurt" drops with it: a local move
inside a single-responsibility component has fewer nearby
load-bearing dimensions it could possibly nick, because the ones
belonging to unrelated concerns were never in the same region to
begin with.

None of this is a new mechanism. It is the same battlefield, drift,
and diminishing-returns arguments, applied at the scale of where a
system's boundaries are drawn instead of the scale of a single
artifact — which is exactly what the recursion at the start of this
section promised.

## Partitioning dimensions, not shrinking them

One correction is worth making to "Decomposition as a lever" before
it is mistaken for something stronger than it is. Componentization
does not shrink the total dimensionality of a problem — the
decisions a system has to make do not disappear by being split
across files, and drawing a boundary between two components
typically adds a few new ones at the seam: what the contract says,
how a failure crosses it, what either side may assume about the
other. The union of dimensions across every component is not
smaller than the monolith's, and is sometimes slightly larger.

What shrinks is not the count. It is the **interaction surface** —
how many other dimensions any given dimension can combinatorially
entangle with. A monolith holds every dimension in one region, where
a local move can, in principle, reach any of them; the union-bound
argument under "Why refactors hurt" grows with exactly that
reachable set. Partition the same dimensions into components with a
real boundary between them, and a local move inside one component
can only ever reach the dimensions inside it — the reachable set per
move drops from the whole system to one component's share of it,
even though nothing about the system's total complexity changed.
This is "Single Responsibility"'s argument stated at the level of
the primitives rather than the level of a coding convention: fewer
dimensions to entangle with per box, not fewer dimensions overall.

Reuse is a related but separate lever, worth keeping apart from
partitioning. Partitioning decides how dimensions are grouped; reuse
decides how many independent samples get drawn for a given group.
Solving the same subproblem once and referencing it, instead of
resolving it again at every site that needs it, is not shrinking
dimensionality either — it is refusing to pay "Many worlds"'
union-of-defect-sets cost for dimensions that were never going to be
different across sites to begin with. The two strategies compose
well precisely because they act on different things: partitioning
narrows what a single move can touch; reuse narrows how many times
the same thing gets independently drawn.

## Humans were always samplers

Here is the fact the apparatus makes visible once you look for it:
two teams handed the same requirement produce different codebases.
The same developer, implementing the same ticket on two different
Mondays, produces different modules. There has never been a
deterministic executor turning intent into code. Human engineering
was sampling from the first line of code anyone ever wrote — it
simply never looked like it, for two reasons that turn out to be
parameters of the process, not properties of the executor.

The first is that sampling was expensive enough to happen once per
system, across a career. Nobody notices they are standing in a
distribution when they have only ever seen one draw from it. The
second, and more important: the human generator remembers its own
collapse. This is where Peter Naur's 1985 argument about programming
as theory-building lands with precision. Naur's point was that a
program is not its text — it is the theory the team holds about how
the text maps onto the world, and that theory is not recoverable
from the artifacts once the team disperses. In this model, that
theory is exactly the tacit half of the pair described in
[Collapsing the wave function](/articles/collapsing-the-wave-function):
the record of which ambiguities were resolved, and how, and why.
The pair — description plus resolution — always existed. The
description-half was written down, sometimes. The resolution-half
lived in the heads of the people who wrote the code, and moved when
they did.

## What expertise actually is

Naur's theory-building names what an engineer carries in their head
about one system. Extend the same idea across a career and it names
something else worth stating precisely: an engineer who has spent
years working in one region of the space of programs — a domain, a
class of system, a language's idioms — is not merely faster than a
novice. They are better calibrated in three distinct places this
model already separates, and each is worth naming on its own.

They are a **better generator**. Enough excursions into a domain
condition an engineer's own sampling the way a description
conditions a model's: the first solution that comes to mind already
carries the weight of every past excursion that worked and every one
that did not, and the distribution it is drawn from puts more mass
near workable points than a novice's would, for the same problem.

They write **better descriptions**. Locality established earlier
that most dimensions of a space do not correlate with behavior while
a few carry outsized, catastrophic sensitivity — the traps. An
engineer who has been burned by those specific traps in that
specific domain knows where they are without discovering them again,
and a spec they write states precisely the dimensions that matter
while staying silent, correctly and cheaply, on the ones that do
not. A novice's silence is ignorance. An expert's silence is
judgment.

And they are a **good oracle in their own right**. A senior engineer
reading a candidate solution can often sense something is wrong
before any test catches it — not intuition in any mystical sense,
but compressed pattern recognition, built from having personally
paid for enough failures in that region to recognize their shape on
sight. This is the same mechanism described earlier as the
environment acting as the slowest oracle, run at the scale of a
career instead of a single artifact: many past incidents, across
many past systems, ratified not into a shared spec or test suite but
into one person's private, portable judgment.

This is worth stating plainly because of what it implies for the
transition the rest of this page describes. A cheap generator is a
fast sampler. It is not, by itself, a well-aimed one, and it is not,
by itself, a competent oracle — the three compressions an expert
carries in one head are exactly the tacit half of the pair this page
has already argued does not survive in a memoryless executor. What
an experienced engineer used to supply silently, for free, bundled
into personal judgment, now has to be supplied on purpose, written
down, by someone — in the description that aims, and in the oracle
that confirms. This is not a smaller job than the one experienced
engineers already did. It is the same job, made explicit.

## Old doctrines, read as theorems

Once engineering is sampling with a rememberer, the field's oldest
instincts stop being folklore and start being consequences of a
specific, nameable cost structure.

**"Never rewrite from scratch,"** Joel Spolsky's famous warning,
is the correct policy when resampling is expensive, the accumulated
survivor evidence is destroyed by it, and the generator — a team
that lived through the bugs — at least remembers the ones it made.
Even that memory does not fully compensate, which is why the advice
holds as strongly as it does.

**"Documentation always goes stale"** is the correct prediction when
a description that is not consumed by generation or verification has
no force holding it to the artifact, and the tacit half of the pair
was cheap enough, relative to maintaining a written half, that teams
rationally let the written half rot. This is the same economic
diagnosis the [rationale](/rationale) already gives for why formal
methods and model-driven architecture failed to displace code — it
turns out to generalize past that specific history.

**"Turnover kills projects"** is what happens when the team that
leaves takes the tacit half of the pair with it. The code does not
change the day they leave, but it becomes an orphaned point with no
attached region — and whoever inherits it must re-infer the region
from the point, which is the same operation, and the same hazard,
as an agent regenerating code by reading only the existing file.

**Technical debt** is the cost of the local gradient rising over
time — the same code, the same team, and each subsequent move costs
more than the last, until a local move stops being viable at all.
**Wrapping legacy systems in adapters instead of rewriting them** is
what a team does when moving the point is no longer affordable and
the only remaining lever is deforming the landscape around it —
building new capability adjacent to the old point rather than
through it.

A historical correction belongs here, because the term itself has
drifted from what coined it. Ward Cunningham's original 1992 use of
"technical debt" was not about sloppy code — he said explicitly it
was not — it was about the gap between the current code and a
better understanding of the problem, discovered only by building the
first version: sometimes you do not know enough to get the design
right the first time. That is drift, in this page's vocabulary, not
the rising local-gradient cost this entry has been using the term
for. The popular meaning drifted from Cunningham's own coinage over
three decades of independent, uncoordinated use — itself an instance
of the phenomenon this page keeps describing, a shared description
eroding under enough use nobody centrally enforced, close to Hyrum's
Law applied to terminology instead of to an API.

Each of these is folk wisdom precisely because it was learned by
contact with a cost structure that held for seventy years without
anyone needing to name it. The model does not contradict any of it.
It explains why it was true, and — more usefully — under what
conditions it stops being true.

## More maxims, under the same lens

The doctrines above are theorems of the old cost regime — they stop
holding, or hold differently, once generation gets cheap. The
maxims below are a different kind: consequences of the primitives
themselves, not of any particular price of sampling, which is why
they hold before, during, and after the transition this page
describes.

**Hyrum's Law** — with enough users, every observable behavior
becomes something somebody depends on, whatever the contract says —
is ratification performed by someone other than the spec's author,
into a region they never agreed to and do not control. Every
accidental resolution of a silent dimension is, to an external
caller, not visible as accidental; it is just what the software
does, and long enough exposure turns it into part of their intent
even though it was never part of the description's. A fix that
closes an underspecification gap, per the taxonomy given earlier,
can be a regression for whoever had already ratified the accident on
their own.

**Chesterton's Fence** — do not remove a fence until you know why it
is there — is an instruction to recover, before moving, whether a
detail is free or load-bearing when the description that would have
said so plainly was never written. The fence's justification lives
in the tacit half of the pair; removing it is a local move made
without the resolution that once ratified it, and it may be standing
between the artifact and an underspecification bug that has been
dormant, per [Many worlds](/articles/many-worlds), for exactly as
long as the fence has.

**Broken windows** — visible neglect invites more of it — is
anchoring, in humans rather than in an agent. A person editing a
messy file, no differently from the model in
[Anchoring on old code](/journal/anchoring-on-old-code), treats the
artifact in front of them as the strongest available signal of the
local aim, outweighing a written standard nobody is actively
enforcing at that spot. Disorder in the current point conditions the
next sample toward more disorder, whether the sampler is silicon or
not.

**Shift left** — catch defects as early and cheaply as possible,
types before review, review before tests, tests before production —
is the direct policy that falls out of treating the oracle as
layered by cost, argued under "The environment as the slowest
oracle." The maxim only makes sense once the layers are seen as
ordered by price per verdict, not as redundant copies of the same
check.

**YAGNI** — you aren't gonna need it — is "Diminishing returns on
aim" applied before the fact instead of after it: paying to write a
rule for a dimension no incident has yet shown to matter spends
against probability mass nobody has demonstrated clears the
crossover.

**"It's not a bug, it's a feature"** is the mirror image of the
taxonomy given under "What a bug is": an underspecification
resolution that turns out
to be liked, rather than unwanted, gets ratified in the same motion
as a bug fix, except the verdict on the coin flip runs the other
way — the accident becomes the intended answer, retroactively,
instead of the excluded one.

**Postel's Law** — be conservative in what you send, liberal in what
you accept — sits in open tension with Hyrum's Law once both are
read this way. Liberal acceptance deliberately enlarges a system's
own region beyond what the written description claims, to reduce
friction at an integration boundary; Hyrum's Law guarantees that
every one of those silently accepted extra cases eventually becomes
something a caller depends on. Being liberal in what you accept is
writing a second, larger, de facto specification, one caller and one
exception at a time, without ever agreeing to any single clause of
it.

**DRY** — don't repeat yourself — is a durability requirement on
ratification, stated as a style rule. A description that exists in
two places has to be corrected in both whenever either is ratified,
and nothing about duplication forces that to happen; a rule fixed in
one copy and silently wrong in the other is the same failure "What a
bug is" already named for a fix that never made it into a test — a
correction true of one point instead of the whole region — arrived
at by a different route.

**Not Invented Here syndrome** is the same bet as skipping a
building code, made voluntarily and usually without noticing it is
a bet. Reimplementing something that already works discards the
accumulated survivor evidence "Many worlds" already priced — the
existing solution's defect set has been found and closed, one
incident at a time, and a fresh implementation restarts at the
bottom of the bathtub curve with an unknown defect set of its own.
It also pays, a second time, a discovery cost someone else already
paid once — the exact waste "Decomposition as a lever" already
named for solving a subproblem independently at every site that
needs it, except here the sites are separated by company rather
than by call graph. Rewriting a mature system "in a more modern
language" can be the rare case that clears the bar "Outrunning
drift" sets — worth it when intent has genuinely moved far enough
that patching cannot follow — but that bar requires an actual
distance to cross, and most instances of the syndrome have none: no
drift, only preference, spending real accumulated evidence to buy an
aesthetic. None of this makes reuse costless, either: concentrating
a solved problem into one shared point, per "Decomposition as a
lever," concentrates its undiscovered defects the same way — a flaw
nobody has found yet fails every caller at once instead of failing
independently, the one honest cost on the other side of the ledger.

## The cost of generality

Reuse and building it specific are not opposites in the sense
folklore treats them — they are two points on the same crossover
argument "Where the crossover sits" already gave for spec-writing,
applied here to a build-versus-reuse decision instead of a
rule-versus-no-rule one.

A generic, shared solution earns everything "Decomposition as a
lever" and Not Invented Here already credited it with: amortized
discovery, a defect set shrunk by traffic from every caller at once,
a description someone else already paid to write. But it earns that
by being built to satisfy more than one intent simultaneously — its
region has to contain every call site's needs, not just this one's —
and that breadth is not free. The extra configuration, the extra
abstraction layer, the code paths that exist only because some other
caller needs them: all of it is additional dimensionality this
specific use case did not ask for and now has to carry, per "Why
refactors hurt," as more surface a future local move can nick
without meaning to. A bespoke solution, built for exactly one shape
of the problem, can specialize in ways the generic one structurally
cannot — assume the input is always sorted, always positive, always
one size — and specialization of this kind routinely buys simplicity
or performance a general-purpose tool has to forgo to remain
general.

There is a second cost to reuse worth naming here. A shared
component answers to every caller's intent, not only this one's, and
when another caller's intent drifts, the shared component may move
for reasons that have nothing to do with this use case at all —
drift imported from someone else's stakeholders, arriving through a
dependency instead of through this project's own discovery. A
bespoke solution answers only to the intent that built it; whatever
moves it, moves it for a reason this project actually has.

None of this reverses the earlier argument. It sharpens it into the
shape every crossover in this page eventually takes: reuse wins when
amortized discovery and shared maturity are worth more than the
unneeded generality and the imported drift; bespoke wins when the
problem's specific shape has enough optimization or simplicity on
the table that carrying someone else's generality would cost more
than deriving the answer alone. Which side wins is not a question
this page can answer in general. It is exactly the question "Where
the crossover sits" already put a name to.

## When the boundary is real

The instinct that a change confined to one part of a system should
only affect related things in that part is correct, and it is worth
stating exactly what it depends on, because when it fails — a change
to interest calculation somehow breaks authentication — the failure
always traces to the same thing: the boundary drawn in the file
structure was not the boundary that actually governs the dimensions.

"Partitioning dimensions, not shrinking them" already gave the
mechanism: a local move can only reach the dimensions inside the
region it is made in, provided that region's boundary is real. A
boundary is real to the extent that four things hold. No dimension
crosses it through shared mutable state — a global variable, a
shared table, an ambient config object read by both sides is a
dimension that belongs to neither component alone, and a move that
looks confined to one file can still reach it. The interface
crossing the boundary is narrow — few dimensions actually pass
through it, the way a pure function's arguments and return value
pass through cleanly where a shared record carrying fields for both
concerns does not. The failure domains are isolated — one side
crashing, leaking memory, or exhausting a shared resource should not
be able to take the other down, which is a property of the runtime
and deployment, not of the source tree. And the oracle covers both
sides regardless of which one changed — a test suite that only
re-runs "affected" tests, guessed from an incomplete dependency
graph, can let a leak through the seam and report green, which is
not evidence the boundary held, only evidence nobody looked.

Every one of the first three is a claim about where dimensions
actually live, which the file structure asserts but does not
enforce. Shared mutable state is the most common failure: two
components that each look properly separated in the source tree can
still share a dimension neither owns, and a move confined to one
file's text is not confined to the region that dimension actually
occupies. This is the practical shape the Chesterton's Fence entry
already warned about, run through this page's specific vocabulary:
the "unrelated" field one component quietly depends on is not a
coincidence to discover through an incident — it is a dimension
whose true boundary was never where the directory structure implied
it was.

None of this is a reason to distrust decomposition. It is the reason
decomposition is a design decision with a real answer, not a free
assumption: draw the interface narrow, keep state unshared, isolate
the runtime, and verify both sides on every change that touches the
seam, and the instinct holds exactly as strongly as those four
things do — no more, and no less.

## What actually changed

AI changes three of the parameters above. It changes none of the
structure.

**The cost of generating collapses.** Sampling, which used to happen
once per system across a career, becomes a routine operation,
cheap enough to run on every change. This is also the first time
the distribution becomes observable: the non-determinism was always
there — two engineers, two Mondays — the model just made it fast and
frequent enough to see.

**The generator loses its memory** — or, more precisely, whatever
memory it has is not versioned, which turns out to be the
load-bearing half of the claim. A hosted model retains nothing
between invocations by default, and even where retrieval, long
context, or persistent agent memory patch some of that back, what
gets retrieved is usually the same lossy artifacts this page has
already argued do not carry the tacit half — old text, not old
understanding. Real or simulated, that memory is not hashed, not
reviewed, and not portable across a model swap the way a spec is.
The tacit half of the pair — the record of which ambiguities were
resolved and why — does not live anywhere a project can point to and
version, unless something outside the generator holds it on purpose.
Naur's theory, which used to survive precariously in the heads of a
stable team, now has no home built for it by default, memory
features or not.

**The sampling rate explodes**, which puts the oracle under a kind
of pressure it never faced from slow, well-intentioned human
executors: a cheap generator iterating against a test suite is
something closer to an adversarial search for the suite's blind
spots than to an engineer trying in good faith to make it pass.

Nothing about the space of programs, the existence of intent, or
the need for an oracle changed. What changed is that the thing
that used to be free — the generator's memory — now has a price,
and the thing that used to be nearly free — a sample — now is.

## What an adversarial generator is

Call a generator adversarial not because it intends harm, but
because of what cheap, repeated sampling against a fixed, partial
oracle does structurally, regardless of intent. When generating is
expensive — the historical case — a generator that fails the oracle
simply fails, and the cost of trying again discourages searching
for the edge cases the oracle cannot see. When generating is nearly
free, failing the oracle costs almost nothing, and an unbounded
number of attempts is economically available. Under those
economics, an oracle with any blind spot at all becomes more likely
to be found the more attempts get thrown at it — not because the
generator is malicious, but because search converges on whatever the
fitness function rewards, and the fitness function here is "passed
the oracle," not "satisfies the intent the oracle was built to
protect." How much this matters in practice depends on how many
attempts actually get thrown, and at the retry counts typical of
most generation loops today — a handful, not an exhaustive search —
this is a real, directional pressure rather than the fully realized
adversarial search the rest of this section describes at its
strongest. The mechanism does not require malice or scale to exist.
It requires scale to dominate, and that is a claim about a trend,
not yet a claim about where things already stand.

This is the same phenomenon documented under different names in
adjacent fields: reward hacking in reinforcement learning,
specification gaming in the broader literature on optimizing
against a proxy, Goodhart's law read as a law of engineering rather
than of economics. The generator needs no strategy and no goal to
produce it. A model asked to make a failing test pass may find it
cheaper to special-case the exact input the test checks than to fix
the underlying logic; asked to raise coverage, it may add assertions
that exercise a line without constraining its behavior. Both satisfy
the oracle. Neither moves the artifact closer to the region the spec
actually defines — closer to the intent — and either can look like
progress while doing it.

The consequence for oracle design is direct, and the site's own
methodology already applies it without naming the reason this way:
an oracle that shares its blind spots with the generator that has to
satisfy it will be found and exploited faster than an oracle that
does not. [Verification](/#verification) requires that a test spec
be generated from a component's public contract, not its
implementation, precisely so the two readings of the same intent do
not inherit the same gap. Independence does not close the blind spot
— nothing does, by the argument above — but it makes the blind spot
of the generator and the blind spot of the oracle two different
sets, which is the only defense a partial oracle has against a
generator cheap enough to search exhaustively for their
intersection.

## The corollaries

Read against this shift, both earlier essays and the rest of the
site stop being ideas about AI coding tools and become the
predictable response to a changed cost structure.

The tacit half of the pair must be **externalized**, because the
generator no longer carries it. This is the entire argument of
[Collapsing the wave function](/articles/collapsing-the-wave-function):
the spec defines the region, the artifact records the resolution,
and the pair — not either half alone — is the unit that must be
versioned.

The boundary between what is **ratified** and what merely
**survived by luck** becomes the variable that decides whether a
regeneration is safe, argued in [Many worlds](/articles/many-worlds):
everything written into the spec and pinned by a test is preserved
across a re-roll; everything that worked by accident is re-decided,
and may not land the same way twice.

Exploration is cheapest at **birth**, before an artifact has
accumulated survivor evidence worth losing, and gets more expensive
with every day it runs — which is a policy, not just an observation:
resample early, hold steady later. Drift complicates this, as shown
above: intent that keeps moving can force the same choice again,
later in an artifact's life, no matter how much survivor evidence
has piled up.

And regeneration should be **minimal** by default — a local move
against the existing point, not a fresh draw from the space —
exactly because a fresh draw destroys accumulated evidence the spec
never captured. This is not a discipline layered on top of the
generation machinery described on the [home page](/); it is what
the [disposition mechanism](/journal/telling-the-agent-what-changed)
is *for*.

## The type system as a pre-paid oracle

A compiler is an oracle in the strict sense already given: a
mechanical, binary procedure that decides membership against one
real, if narrow, slice of intent — shape, not behavior. When the
language itself was a deliberate choice, as it usually is, failing
to compile is a genuine intent violation, not a technicality outside
intent's reach: whoever chose the language meant the software to be
expressible in it, and a build error says that meaning was not
honored. What compiling does not do is extend that credit to the
rest of intent. A program that compiles has cleared exactly the
shape bar and nothing more — type-correctness is necessary for
almost every purpose and sufficient for almost none, the same
partiality every oracle in this model has already been shown to
carry, easy to miss here only because the bar it clears is so cheap
to check that clearing it feels like clearing all of them.

But the choice of language does something an ordinary description
does not: it does not narrow a region inside a fixed space of
programs, it selects which space is being sampled from in the first
place. This is a decision made once, early, usually without
revisiting — closer to a zeroth-order description than an ordinary
spec clause, made before a single line of the actual spec is
written.

What makes a strongly-typed language's compiler worth naming on its
own is how that oracle got paid for. An ordinary test or spec clause
is authored retail: written by this project, for this project, the
moment an incident or a review demands it, at that project's cost. A
type system is authored wholesale, once, by a language's designers,
and every program in that language inherits its coverage for free at
the moment of compilation — a large, fixed investment in catching a
well-understood class of common failure, amortized across every
project that has ever chosen that language, including this one,
without this project having to write a rule for it. Choosing a
strongly-typed language is buying oracle coverage in bulk, prepaid
by a community, instead of building it retail, one ratified incident
at a time.

This bends the crossover argued under "Where the crossover sits" in
the language's favor before a single project-specific rule is
written. If a whole common class of failure is already excluded by
the compiler, the project's own finite budget for writing spec
clauses and tests does not have to spend its cheapest, highest-value
effort re-deriving that class — it starts further along the curve,
free to spend the budget where the compiler has no opinion: the
actual domain logic, exactly where a description earns its keep, per
"The primitives."

None of this is free on the other side. A stricter type system also
narrows the space it draws from, and narrowing a space before any
spec exists risks excluding valid points along with bad ones — a
correct, intent-satisfying program the type checker cannot prove
safe is rejected all the same, the type system's own version of the
false positive this page has already conceded every partial oracle
can produce. This is also why the choice tracks the birth-versus-
maturity policy already given as a corollary: a system whose intent
is still being discovered benefits from a larger, more permissive
space and a cheap, unconstrained exploration loop, even at the cost
of catching fewer shape errors for free; a system with a long life
ahead of it, high fan-in, or high stakes collects more value from
the prepaid coverage than it loses to the narrower space, for the
same reason "Decomposition as a lever" already gave for investing
more in what many callers share. The choice of language is not a
style preference made once and forgotten. It is the earliest and
longest-lived instance of the exact trade this page has been
describing throughout — how much oracle coverage to buy, and when —
made before there is even a project to make it for.

The boundary this section drew between shape and behavior is not
fixed by nature, and it is worth being precise about that, because
language design has spent decades moving it. Rust's borrow checker
is a closed oracle in exactly the sense already given — authored
once, by the language's designers, with no independent second
account to disagree with it — and it is total authority over a
dimension considerably richer than parsing and type arity: a program
that borrows without violating the checker's rules is memory-safe
and free of data races in the code the checker covers, not merely
well-shaped. This is not a special case. It is what happens when a
type system is engineered to encode a semantic property and not only
a syntactic one, and the same move scales further in
dependently-typed languages, where a passing type check can certify
an arbitrary proposition the type was built to state. The frontier
between what a closed oracle can settle for free and what only an
open oracle, tested at runtime, can settle is not fixed by shape
versus behavior as static categories — it is a design choice a type
system's authors make, and every property moved across it stops
needing a test and starts costing nothing per project, the same
prepayment already priced above, now shown to be adjustable rather
than capped at parsing and arity. What a closed oracle can never do,
no matter how rich the type system, is confirm a property nobody
thought to encode — the frontier moves, but something is always
still on the open side of it.

## Closed oracles and open oracles

"The type system as a pre-paid oracle" left one property of the
compiler unnamed, and it is worth making explicit because it settles
a question this page has not yet had to ask: can an oracle's verdict
itself be ambiguous?

A compiler cannot produce an ambiguous verdict, and the reason is
structural, not a matter of care in its design. The language's
grammar and type rules were authored once, by the language's own
designers, and the compiler simply executes that authoring — there
is no second, independently authored account of the same rules for a
verdict to conflict with. Call this a **closed oracle**: total
authority over the one dimension it covers, because nothing else
claims to speak for that dimension.

A test is different in exactly the property that makes it worth
writing in the first place. It is authored separately from the
implementation — retail, by a person or an agent, deliberately
independent, per the argument already given under "What an
adversarial generator is" for why that independence matters. That
same independence means a test's failure can mean one of two
different things: the implementation violated something the spec and
the test agree on, or the test and the spec disagree with each
other, and the implementation is caught in the middle. Call this an
**open oracle** — capable of disagreeing not just with a sample, but
with the very description it was meant to serve.

The distinction gives a firm answer to something "Confinement" left
as a practical judgment call: feeding an oracle's verdict back into a
bounded retry, without treating it as a new source of aim, is safe
exactly when the oracle is closed. A closed oracle's verdict can only
ever mean nonconformance, per the taxonomy under "What a bug is" —
there is no second reading to reconcile, so retrying with the
verdict in hand is re-sampling with a cheap directional signal, not
consulting an unauthorized description. An open oracle's verdict
needs the same nonconformance-versus-underspecification fork "What a
bug is" already gives, resolved before any retry is safe, because
half of what it might mean is not a defect in the sample at all, but
a disagreement between two authored artifacts that only a human can
resolve without quietly erasing it.

This also names a fifth operation this page's primitives did not yet
have a place for: a **bounded, ephemeral resample** — scoped to a
single generation event, guided by a closed oracle's own verdict,
leaving no trace in anything versioned once the event ends. It is
cheaper than a full resample and safer than silent self-correction
against an open oracle, precisely because it never touches aim at
all.

## The oracle is generated too

There is a recursion sitting inside everything this page has said
about tests, and it is worth pulling out into the open: a test is
not a primitive. It is an artifact, generated the same way any other
artifact is — from a description (a test spec, stating what the
behavior should be), by a generator (a person or an agent
translating that description into a specific language's test code),
producing a sample that itself can suffer everything "What a bug is"
already named. The test-generation step can miss what the test spec
actually said (nonconformance) or resolve something the test spec
left silent in a way nobody intended (underspecification). The
oracle, in other words, is downstream of its own aim, and inherits
every uncertainty aim was ever shown to carry.

This means the open oracle already named under "Closed oracles and
open oracles" is open in a second sense beyond the one already
given. It is not only capable of disagreeing with the spec because
it was authored independently — it is itself a generated translation
of a description, and that translation can be wrong on its own
terms, before any question of agreement with the implementation's
spec even arises. A test that looks like ground truth because it is
code that runs and returns a verdict is still, underneath, a sample
from a generator conditioned on a test spec, with everything that
implies.

The regress this opens does not go on forever, and it is worth being
honest about exactly where it stops instead of pretending it does
not exist. It stops at two places. One is a closed oracle: the
generated test code at least has to compile, and that verdict, per
the argument already given, cannot be ambiguous — a floor under
every open oracle, confirming shape even when it can say nothing
about whether the test checks the right thing. The other is a human,
or an independent second generation, reading the test against the
test spec directly — the same defense "What an adversarial generator
is" already gave for the implementation-versus-test relationship,
applied one level up: a test's trustworthiness is not a property it
has by being a test, it is earned the same way an implementation's
trustworthiness is, by independence of authorship, by mutation
testing standing in for a confirm layer the test does not otherwise
have, and by the same production-oracle mechanism argued under "The
environment as the slowest oracle" ratifying it over time as it
survives contact with real failures it was meant to catch.

Ratifying a fix "into a test," which this page has treated for most
of its length as the durable, mechanical half of a correction, is
therefore not quite the bedrock it was allowed to sound like. It is
durable relative to the artifact it protects, and it is generated,
aimed, and fallible relative to whatever generated it. The chain of
trust bottoms out somewhere. It is worth knowing that it bottoms out
at a closed oracle or a human, and nowhere else.

## The generator is not the spec

A natural temptation, once a description is seen to leave so much
unwritten, is to conclude that whatever fills the silence must
itself be part of the description — that the model, since it
resolves every ambiguity a spec does not, has joined the spec as an
unwritten second half. The primitives already rule this out, and it
is worth saying explicitly why, because the temptation is strong and
the mistake it produces is a real one.

The generator and the description have been separate primitives
since the first section of this page, and for a reason that matters
here specifically: a description conditions a distribution; a
generator is the distribution, and something has to supply it
regardless of what any description says. Every generator resolves
whatever a description leaves silent — that is not a special
property discovered about models, it is the definition of a
generator. A human filling in the same silence, drawing on years in
a domain, is doing the identical job with the identical structure
already given a name: "What expertise actually is" already located
that competence squarely in the generator, as a better-calibrated
sampling distribution, not as a hidden clause smuggled into the
spec. A model resolving an unstated comparison operator is the same
fact wearing different weights.

What is genuinely new, and worth keeping from the instinct that
produced this section, is not where the model sits but what its
variability costs. A human generator's prior changes slowly, across
a career, mostly in the direction "What expertise actually is"
already named as improvement. A model's prior does not change across
a career at all — it changes the moment a provider retrains or swaps
a version, for reasons outside any project's control, and the
artifact that comes out can move without a single line of the
description changing. The chain hash records the state of the
description faithfully; it was never built to, and cannot, record
the state of a generator that is not part of what it hashes. This is
not evidence the generator belongs inside the versioned half. It is
evidence that an unversioned, unaccountable input sits upstream of
every artifact, on the far side of a boundary this page has drawn on
purpose — and the discipline argued under "The type system as a
pre-paid oracle" exists because of exactly that boundary: divert
what must not vary to a deterministic oracle or a hard type, leave
to the generator only what is safe to leave stochastic, and verify
behaviorally, after the fact, because the one input nobody can pin
down is the one input testing was always going to have to cover.

## What actually licenses a short spec

There is a premise sitting underneath every defense of a short spec,
classical and current alike — a competent reader, a blueprint that
assumes someone who can read a blueprint, a recipe that assumes
someone who can cook, a spec node that never explains what a hash
map is. The premise says: leaving something unwritten is safe
because a competent executor resolves it correctly. Read against the
primitives built here, that premise smuggles in a claim it has no
right to. Competence, however real, is a property of the generator,
and a generator only ever aims. "Correctly" is a confirm-shaped
word, and nothing about a generator's prior, however well-trained or
well-earned, can supply it.

What a competent executor actually contributes is a higher hit rate
— a prior concentrated on sensible defaults rather than spread
uniformly over the space, so that most silences resolve into
something workable on the first sample. This is real, and it is
exactly what "What expertise actually is" already named as the
first of three separate contributions competence makes, the
better-generator role. But a higher hit rate is not certainty, and
nothing about it tells you, for any specific resolved silence,
whether this one landed on the right side. That answer was never
available at generation time. It becomes available only through the
mechanism this page has spent most of its length describing: the
incident that surfaces a bad resolution, the fix that gets ratified
into a test or a narrower clause, and the slow accumulation "The
environment as the slowest oracle" already called maturity. A short
spec is not safe because competence resolves its silences correctly.
It is safe because competence keeps the volume of wrong resolutions
low enough that the discovery-and-ratification loop can afford to
catch the rest — a claim about a rate and a cost, not a claim about
correctness at the moment of generation.

This changes what "the executor already knows how to turn intent
into working code" was actually asserting, for every specification
in history, not only the ones this page has been built to explain.
It was never a claim that the executor gets it right. It was always,
whether the field noticed or not, a claim about a hit rate good
enough that the remaining misses are economically absorbable by
whatever discovery loop happens to be in place — code review, a QA
cycle, a customer complaint, a regulator's audit. Slow discovery
loops made the claim viable historically because misses were rare
and generation itself was rare — one executor, one attempt, mistakes
found over years. A model as executor does nothing to this claim
except raise the sampling rate the loop has to keep up with, which
is the same parameter shift "What actually changed" already named as
the whole difference AI makes, arriving here from a different
direction: the reason a short spec is safe was never the executor's
competence alone. It was the loop behind it, and the loop has to run
faster now because the executor got faster, not because it got any
more entitled to the word "correctly."

## When the generators disappear

"The battlefield assumption" named one way the local-gradient cost
can rise — technical debt, the code's own structure degrading move
by move. "When the target itself moves" named a second — intent
drifting out from under a fixed description. There is a third, and
it has nothing to do with the code or the intent: the population of
generators competent to work on it can collapse out from under both.

"What expertise actually is" already located competence in the
generator, not the artifact — a better generator samples more
accurately, writes tighter descriptions, judges its own output more
reliably, all earned by time spent in a specific space. That
competence is not guaranteed to persist at the level of an industry.
A language can have working, well-specified, well-tested code and,
decades later, almost nobody left who can read it fluently enough to
make even a small local move with any confidence. The code has not
moved. The intent has not moved. The supply of generators able to
stand on that point and fight has gone to zero, or close enough that
hiring one costs what migrating away would.

This is a legitimate trigger for the same calculation "Outrunning
drift" already gave, run against a different cause. Patching in
place, once a language's competent-generator pool has collapsed, is
not merely uncomfortable — its local-gradient cost has been driven
up by a factor that has nothing to do with the artifact's own
condition, exactly the way a rising local-gradient cost from
technical debt or drift already licensed a comparison against
re-specifying and resampling. "We cannot find people who can
maintain this system" is not a complaint about the code. It is a
report that one whole term in the crossover has gone to infinity,
and migrating can be entirely rational even when nothing about
behavior needed to change at all.

It is worth naming, tentatively, what a cheap and competent
generator does to this specific trigger, because it may be the one
case AI genuinely changes rather than merely accelerates. A model's
competence, per "The generator is not the spec," is a property of
training, not of a living community — it does not retire, does not
need replacing, and does not care how few humans still read the
language fluently. If a model can generate and verify code in a
scarce language as competently as it can in a common one, the
generator-extinction trigger for migration weakens in a way none of
this page's other arguments do, because the scarcity it responds to
was always about people, and people are the one input a model was
never going to run out of being able to substitute for. This is
speculative, and stated as such: it depends on a model's competence
in a given language actually holding up, which nothing here has
tested.

## What this says about generating tests

Apply the whole apparatus to one concrete practice, because a model
that only produces distinctions and disclaimers is not yet useful
for a Tuesday-morning decision: how tests should get written when a
machine writes them.

**Generate from the contract, never from the implementation.** This
is already argued under "What an adversarial generator is" and
"Decomposition as a lever" — a test that reads the code it is meant
to check inherits that code's blind spots along with its intent, and
a cheap, repeated generator will eventually find whatever gap the
two share. The rule is not a style preference. It is the only
defense a partial oracle has against a generator built, in effect,
to search for its weaknesses.

**Prioritize by expected value, not by coverage.** "Where the
crossover sits" already gives the right unit: a test's worth is the
probability mass of the failure it guards against, multiplied by the
cost of that failure going undetected. Coverage percentage counts
lines exercised, not stakes protected, and a generator asked to
raise it can satisfy the number by exercising a line without
constraining its behavior — a documented case, not a hypothetical
one. A methodology that optimizes coverage optimizes the wrong side
of the multiplication.

**Aim tests at the load-bearing dimensions on purpose.** Most of a
program's surface does not correlate with behavior at all; a few
dimensions carry outsized, catastrophic sensitivity, per the
argument given under "The region is never a point." Boundary values, error paths,
concurrent access, resource exhaustion — the traditional targets of
boundary-value analysis and equivalence partitioning — are exactly
this small set, named by a different vocabulary decades before this
page existed. The folklore already points at the right place. What
the model adds is why spreading effort uniformly across the rest of
the surface is not thoroughness. It is diminishing returns, paid at
full price.

**Write the test at the level of the contract the fix defends, not
the shape of the code that happened to fail.** "Why pinning a defect
pays off locally" already named the failure this avoids: a test
written against an accident freezes it as a requirement, vetoing a
better resolution some future generation would otherwise be free to
reach.

**Weight investment by reuse, not by size.** "Decomposition as a
lever" already showed that a shared component's defect set is
discovered once and closed for everyone who calls it, where a
used-once component's fixes only ever pay off locally. A component
with high fan-in deserves test investment out of proportion to its
line count, because every hour spent there is spent once and
collected by every caller.

**Match rigor to what a miss costs, not to a house style.** "Where
the crossover sits" already showed the crossover moves with the
stakes: a component whose failure costs an afternoon does not owe
the same investment as one whose failure costs a plane, a misdosed
patient, or a regulator's attention. A single test-writing standard
applied uniformly across a codebase either wastes effort on the
cheap components or under-protects the expensive ones, and usually
does both at once.

**Spend the cheap window at birth.** The corollary already given —
exploration is cheapest before survivor evidence accumulates —
applies directly: broad, exploratory testing earns its cost early in
a component's life, while a mature component, its common failure
modes already ratified, gets more from a few precise regression
tests than from continuing to search a region that has already given
up its low-hanging defects.

**Measure the oracle by whether it resists being gamed, not by how
much of the code it touches.** "What an adversarial generator is"
already named the right yardstick: resistance to optimization, not
numeric coverage. Mutation testing — deliberately injecting small
defects and checking whether the suite notices — makes this
yardstick operational: it asks the suite the same question a cheap,
adversarial generator will eventually ask it, before the generator
gets the chance.

## Test, spec, or both

The apparatus already gives a decision procedure for this, not just
a set of concepts, so it is worth writing out as one.

Start with the taxonomy from "What a bug is." If the failure is
nonconformance — the description already spoke on this dimension
and the sample still landed outside it — the spec was never the
problem, and the fix is a test, or simply another sample against the
same description. Writing a spec clause here duplicates what already
exists and does not address why the generator missed it. If the same
nonconformance keeps recurring despite the spec already speaking
plainly, that repetition is itself evidence — not that the rule is
missing, but that its current wording is not registering, which is
an aim problem to solve by rewording or repositioning the existing
clause, not a reason to add a new one.

If the failure is underspecification — the description was genuinely
silent — the default is both, for a reason already argued and worth
restating plainly here: a spec clause is probabilistic and durable, a
test is mechanical and local. The clause conditions every future
sample, including one from a full resample, on every variant of the
mistake its wording actually covers; but this site's own record
already shows prose being present in full and still not followed, so
a clause alone carries no guarantee. The test costs little to add
once the mistake is understood, and it guarantees the specific case
will be caught if it recurs — but its guarantee stops exactly at the
case it names, and its value depends on staying in the same
battlefield unless it is written at the level of the contract rather
than the accident. Together they cover for each other's weak side:
the clause's reach against the test's certainty.

Where budget forces a choice between only one, weigh stakes and
durability the way "Where the crossover sits" and "Decomposition as
a lever" already do. A component with high fan-in, high failure
cost, or a long remaining life in its current battlefield earns the
spec clause even at the cost of writing it carefully — the broad,
resample-surviving protection is worth the effort. A component about
to be resampled anyway, used once, or cheap to fail earns the test
and nothing more; the clause's marginal value has nowhere long-lived
to pay off. And a dimension that is behaviorally free but
cost-relevant — the naming case argued earlier — earns neither a
test, which would have nothing to check, nor a heavy clause, only a
light convention note aimed at the humans and agents who will next
need to tell a load-bearing dimension from an inert one.

None of this replaces judgment. It replaces "should I write a test
or fix the spec" with the question this page has been arguing has an
answer: which side of aim and confirm is actually missing here, and
does this battlefield have enough life left in it to be worth aiming
better, or only enough to be worth confirming once.

One clarification worth stating on its own, since it is easy to
conflate: consulting the description at every fix is necessary, but
it is an act of aim, not of confirmation, and the distinction from
the primitives applies here without exception. Reading the spec
before making a change shifts the odds that the change respects it;
it does not guarantee the change stays inside the region, and this
site's own record already shows why — prose present in full, in the
chain, has still gone unfollowed. The only operation that actually
confirms a fix has not carried the point outside the region is
running the oracle against the result, and even that confirms only
on the dimensions the oracle happens to cover, per "The region is
never a point." Consulting the spec on every fix is the right habit,
worth making structurally unavoidable rather than optional — but it
buys better odds, not a guarantee. The guarantee, bounded as it
always is, comes from the test that runs after.

## The questions this reframes

A few questions recur in every conversation about specs versus
code. The model does not settle them with a number, but it replaces
each with a version that has an answer.

*Does the spec have value?* The spec is the only artifact that
distinguishes a region from a point, a decision from an accident.
Its value is the value of the option it preserves — to regenerate,
to hand the system to a new team, to bring in an executor with no
memory of what came before — and that option was cheap to give up
only while the generator remembered and the big move was rare. Both
premises are gone.

*Is code alone sufficient?* Code alone was never sufficient. It
looked sufficient because it was never alone — it was paired with a
tacit half held by whoever wrote it. The live question is not
whether code suffices, but where the other half of the pair lives
now, and whether it survives the departure of whoever is holding
it.

*When should you regenerate instead of patching?* When the evidence
a resample would destroy is worth less than the cost of staying in
the current basin, discounted by how much of that evidence the
oracle can hand back for free — or, separately, when intent has
drifted far enough that walking a local diff toward it, anchoring
risk included, costs more than re-specifying and drawing fresh. An
oracle with dense coverage makes resampling cheap regardless of age;
a thin oracle makes every day of age expensive to discard.

*What is codebase maturity?* A posterior narrowed by selection — a
defect set that contact with reality has shrunk, held in a point
that has stopped moving. It is a statistical property of a single
sample, not a property of the code's age or its aesthetics.

## What the field already knew

The claim that programming is discovery, that requirements move, and
that deciding what to build is the hard part is not new to this page
or to the AI era. Several of the field's most-cited voices already
argued versions of it, independently, decades before a model could
generate a line of code, and reconciling them sharpens rather than
threatens what this page has built.

**Fred Brooks**, in "No Silver Bullet" (1986), split software's
difficulty into essential complexity — irreducible, inherent in the
problem itself — and accidental complexity — friction imposed by
tools, notation, and process, which better tools can remove. His
prediction that no single technology would deliver an
order-of-magnitude productivity gain rests on essential complexity
dominating: most of what makes software hard was never the mechanics
of writing it down. Read against "What actually changed," this
reconciles cleanly rather than contradicts. AI collapsed the cost of
one specific accidental difficulty — translating an
already-understood intent into code — and did nothing to the
essential one: deciding what the intent actually is, in a space with
far more dimensions than intuition expects. Brooks's other claim
from the same essay, that the hardest part of building a system is
deciding precisely what to build, and that no part is harder to
rectify once done wrong, is essentially "What actually licenses a
short spec" and "Where the crossover sits" stated in 1986, in the
vocabulary available at the time.

Brooks had already drawn the practical conclusion from this a decade
earlier, in "The Mythical Man-Month" (1975): "plan to throw one
away; you will, anyhow" — a direct precedent for exploration being
cheapest at birth, advocated even under the old regime where
generation was maximally expensive, on the theory that the first
system teaches the team what the second one should be. He revised
this view later, favoring incremental growth over wholesale
replacement in the twentieth-anniversary retrospective — moving, in
his own words, toward exactly the tension this page has held
throughout between resampling and patching, without ever fully
resolving it either.

**Ward Cunningham** coined "technical debt" the same decade, and
meant something closer to drift than to the rising local-gradient
cost the term is popularly used for today — a correction already
made under "Old doctrines."

**Jack Reeves**, in "What Is Software Design?" (1992), argued that
source code is the actual design document and compiling is
manufacturing — a claim usually read as provocative, but which
"Collapsing the wave function" already arrives at independently: the
artifact is the record of resolved decisions, closer to design than
to the description that permitted many designs. Reeves and this page
differ only in vocabulary, three decades apart.

**The Agile Manifesto** (2001) and Kent Beck's Extreme Programming
made the discovery problem into an institutional response rather
than an observation: welcome changing requirements even late in
development, and flatten the cost-of-change curve through tests,
refactoring, and continuous integration rather than accept it as an
exponential law of nature. This is "The battlefield assumption"
treated as an achievement rather than a given — the assumption that
a basin's satisficing point stays reachable at acceptable cost does
not hold by default, and the practices agile prescribes are, read
this way, a program for keeping the local gradient flat on purpose.

None of these voices had a generator that samples cheaply, and none
of them needed one to notice that specifying is harder than
building, that the target moves, and that the first attempt is
rarely the last. What changed since is not the discovery. It is the
price of the thing discovery used to be measured against.

## Why reading is harder than writing

Joel Spolsky's observation that reading code is harder than writing
it, offered as the reason programmers reliably underrate code they
did not write, has a precise mechanism in this model rather than
remaining a psychological aside.

Writing happens with the tacit half of the pair live and present —
"Humans were always samplers" already located it there, the record
of which ambiguities were resolved and why, held in whoever is doing
the resolving, in the moment they do it. A writer moving through a
silence and settling it does not have to infer whether the dimension
they just touched was load-bearing or free; they know, because they
are the one deciding. Reading is the same judgment made from the
opposite side of the same gap "Collapsing the wave function" already
named: the artifact in front of a reader records that a decision was
made, not why, and not whether the reason still applies. Every line
is, from the reader's position, an unresolved instance of the
question "Why refactors hurt" already posed and could not answer in
general — free or load-bearing? — and the reader has none of the
writer's advantage in answering it.

This gives Chesterton's Fence, and the rewrite impulse Not Invented
Here already named, a shared root instead of two separate
explanations. A reader who cannot tell which fences are load-bearing
sees only fences, and a codebase made entirely of decisions whose
reasons are invisible reads as arbitrary regardless of how sound
those reasons actually were. The conclusion "I could write this more
clearly" is frequently true of the reader's own understanding and
false of the code — what improved is legibility to one specific
reader who has not yet paid the discovery cost the original
artifact's choices already absorbed, not the artifact's fitness for
the region it satisfies. Writing feels easier than reading because
it is a different operation entirely: one runs forward with full
access to the reasons, the other runs backward trying to reconstruct
reasons a description never wrote down and an artifact was never
going to carry.

## What increased writing volume does to reading

The premise behind "Why reading is harder than writing" has a direct
application to the situation "What actually changed" already
described, and it is worth drawing out on its own, because it points
at a bottleneck this page has otherwise discussed as an oracle
problem, not a cognitive one.

Sampling collapsing in cost means more code gets written, not less —
the volume of artifacts a project produces per unit of time rises
with the same curve that makes resampling and drift-chasing newly
affordable. None of this changes the mechanism "Why reading is
harder than writing" already gave: reading is the reconstruction of
reasons an artifact never wrote down, from the outside, without the
generator's live access to them, and that reconstruction cost is a
property of the artifact and the reader, not of how fast the
artifact was produced. Writing got cheap. Reading did not, and
reading is the operation review, confirmation, and maintenance are
all built from.

This inverts which side of the pair is the bottleneck. Before, a
human wrote the code and therefore already possessed, for free, the
very access that makes reading unnecessary for the author — the
scarce resource was the writing itself. Now the scarce resource is
the reconstruction a reviewer has to perform on code nobody involved
has the tacit half for, produced fast enough that the volume needing
reconstruction grows with generation speed, not with review
capacity, which has not gotten any faster. An organization that
keeps routing confirmation through human reading at the old rate is
asking the one operation this page has shown does not scale with
generation speed to absorb all of the speedup on the other side.

The redirection this suggests is the same one "Test, spec, or both"
already argued for on different grounds: confirmation that does not
require reconstructing the tacit half — a closed oracle, a type
system, a test that runs mechanically — scales with generation
volume the way human reading never will, because it was never doing
the reconstruction in the first place. This does not mean review
disappears; it means the rational response to cheaper writing is not
proportionally more human reading of proportionally more code. It is
shifting as much of the newly enlarged confirmation burden as the
crossover justifies onto oracles that do not pay the reading tax at
all, and reserving the reading itself for exactly the residue — the
underspecification fork, the disagreement between two authored
artifacts — that only a human was ever going to be able to resolve.

## A market for lemons

Akerlof's market for lemons — buyers cannot tell a good used car
from a bad one until after buying it, so the market prices both at
the average, good sellers exit, and quality collapses — has a
precise counterpart in how programmers get evaluated, and the
mechanism is the same oracle latency this page has already named.

"The environment as the slowest oracle" already established that
production is the highest-coverage, but also the slowest and most
expensive, layer of confirmation — a defect from careless
underspecification can sit dormant, looking like a green build and a
shipped feature, for months before an incident reveals it. The
evaluation cycle that decides who gets promoted, staffed on the
interesting project, or let go runs far faster than that oracle
does. A performance review, a sprint retrospective, a resume,
arrives long before the oracle that would actually distinguish a
careful resolution from a lucky one has had time to fire. What the
evaluator can observe now is not quality — it is velocity, story
points closed, features shipped — an accidental, immediately
available proxy standing in for a fidelity-critical property that
has not yet had the chance to reveal itself.

This is the identical structure "What an adversarial generator is"
already described for a cheap generator gaming an oracle, except the
generator being evaluated is a person and the reward is a career
rather than a merge. A programmer who writes carefully, tests what
matters, and ships less in a sprint looks, on the only signal
available at review time, worse than one who resolves every silence
the fast way and ships more — the coin that "What a bug is" already
named landing badly is not yet visible, and will not be for months.
Reward the fast signal long enough and the market does exactly what
Akerlof's does: the careful engineers are underpaid relative to the
defect rate they are actually preventing, the fast ones are overpaid
relative to the defect rate they are actually accumulating, and
either the careful engineers leave for evaluators patient enough to
wait for the real oracle, or they learn to stop paying a cost nobody
is measuring.

The fix is not exhortation. It is the same move already argued for a
different kind of hidden cost under "Where the crossover sits" and
"What this says about generating tests": meter what the slow oracle
would eventually say, sooner. Code review, static analysis, a strong
type system, and a test suite that actually resists gaming are not
only technical defenses against bad code — they are an attempt to
move some of production's verdict into a faster oracle, so that
quality becomes visible before a performance cycle closes instead of
two incidents later. An organization that only measures shipped
output is choosing, whether it knows it or not, to run the lemons
market on its own engineers.

## What this says about code review

Apply the same apparatus to the practice sitting next to testing in
every engineering culture, because the two are usually taught
together and turn out to answer different questions.

**Review is a human oracle, and it covers exactly what mechanical
oracles cannot.** A type system confirms shape; a test confirms
behavior against whatever it was written to check; neither can say
anything about a dimension that carries no behavioral difference at
all. Naming, structure, the shape of an abstraction — the
cost-relevant, behaviorally free dimensions "Not every free
dimension is free" already named — are invisible to every oracle
that only watches what a program does, because nothing about what it
does changed. A human reading the code is the only oracle layer that
can confirm these, which is also why no amount of test investment
ever makes review fully redundant: the two confirm different things,
not the same thing at different costs.

**Independence is what makes it work, and its absence is what makes
it decorative.** "What an adversarial generator is" already argued
that a test sharing the implementation's blind spots is worth less
than one authored independently from the contract. The identical
argument governs who reviews: a reviewer who is the author, or a
model reviewing its own prior output in the same context, is not a
second reading — it is the same generator checking its own
satisfaction, prone to the same blind spots for the same reason. A
reviewer who did not write the code, given the contract and the diff
and nothing else, is a genuinely separate sample of judgment, and
that separation, not seniority or diligence, is what the practice's
value actually depends on.

**A review comment is a discovery, and it needs the same choice
"Test, spec, or both" already gives.** A reviewer catching a bug
before merge is "shift left" at its cheapest layer — an incident
avoided before it ever reaches a test suite, let alone production.
But a comment fixed only in that one diff and never generalized is
exactly the ephemeral, unratified knowledge "Humans were always
samplers" already located in a person's head: real, and gone the
moment that reviewer is not in the room for the next similar diff. A
review culture that never promotes a recurring comment into a linter
rule, a type constraint, or a test is paying an expensive, human
oracle to catch the same thing repeatedly, forever, when a cheap one
could have closed it once. A recurring comment is a signal this page
has already used for a different symptom under "Diminishing returns
on aim": the aim is in debt, and the fix is not another reviewer
saying it again.

**The bottleneck it inherits is reading's, not writing's.** "Why
reading is harder than writing" and "What increased writing volume
does to reading" already gave the mechanism: review is the reading
operation, performed by someone without the generator's live access
to why a choice was made, and that cost does not fall as generation
gets faster. A model producing ten times the diffs does not make
review ten times easier; it asks the one operation that never got
cheaper to absorb ten times the volume. This is the sharpest form of
the crossover this page keeps returning to: as writing approaches
zero cost, review approaches being the entire remaining cost of
shipping anything, and treating it as a fixed ritual rather than a
budgeted resource is how that cost gets paid blindly.

**Rigor should track stakes and reach, not apply uniformly.** "Match
rigor to what a miss costs" and "Weight investment by reuse, not by
size" already argued this for tests; nothing about review is exempt.
A two-reviewer policy applied identically to a one-line copy change
and a payment-authorization path is the same mistake "Where the
crossover sits" already named for a single test-writing standard:
wasted on the cheap component, thin on the expensive one, and
usually both at once.

**Review runs on probability mass too, and diminishing returns
follows the same arithmetic.** A reviewer's catch rate on any class
of defect is not certainty, it is a probability — high for the
common, obvious failure modes almost any competent reader would
flag, falling off into the same long tail "Diminishing returns on
aim" already derived for spec-writing. A second pass over the same
diff, by the same reviewer or a different one, buys less than the
first for the identical reason a second spec clause buys less than
the first: whatever was easy enough to catch already got caught, and
what is left is disproportionately the rare and the subtle. This
gives reviewer selection an actionable lever spec-writing never had:
since the union bound's cost comes from overlap, a second reviewer
with genuinely different background or domain exposure catches more,
on net, than a second reviewer who thinks about the code the same
way the first one does — the same independence argument that already
governed test authorship, applied to choosing who reviews rather
than only whether review happens.

**A reviewer's own attention allocation is the crossover, run by one
person in real time.** "Match rigor to what a miss costs" described
an organizational policy; a competent reviewer applies the identical
calculation privately, inside a single diff, without ever naming it.
Spending three careful passes on the code that touches money and one
skim on a rename is "Where the crossover sits," executed at the
grain of individual lines instead of individual components, and it
depends on the same judgment "What expertise actually is" already
located in a person: knowing which lines are load-bearing before
spending the attention to confirm they are correct.

**Judging whether the code will be legible to a reader who is not
here yet is close to uniquely review's job today.** No mechanical
oracle can confirm this, because the judgment being made is not
about what the code does — it is a prediction about whether a
future, different mind, without the current reader's context, will
be able to reconstruct the reasons "Why reading is harder than
writing" already said are not written down anywhere. A linter can
proxy pieces of it — length, nesting, naming convention — but the
actual question, will this make sense to someone who was not in this
conversation, is a theory-of-mind operation, not a behavioral check,
and behavioral oracles were never built to answer it. This is not a
claim that only a human can ever do this — a capable enough model,
simulating a future reader the way it can simulate other things, may
turn out to do it well. It is a claim about what, today, only review
actually attempts.

**Whether review is necessary is the wrong question, because review
is not one thing.** It bundles at least three functions this page
can now tell apart: confirming the free-but-cost-relevant dimensions
no mechanical oracle sees, catching underspecification before the
next, more expensive oracle layer has to, and supplying independence
against a generator's blind spots. Each has a different crossover,
and none of them justifies a fixed, uniform amount of review applied
everywhere out of habit.

**And a fourth function is not technical at all, and this page's own
mechanisms predict it degrades in a specific way.** An approval
attached to a name is a record that due diligence happened, useful
for reasons that have nothing to do with whether a defect was
actually caught — an accountability signal, not a confirm operation.
This function can survive perfectly intact even after the other
three have collapsed to ritual, because it is cheap to produce and
slow to audit: nobody can tell, from an approval alone, whether the
reviewer actually reconstructed the reasoning "Why reading is harder
than writing" said was expensive, or spent thirty seconds and
clicked approve. Evaluate reviewers on throughput — reviews closed
per day, the fast, visible proxy — and "A market for lemons" runs a
second time, one level up the same stack: the reviewer who reads
carefully and catches less-visible defects looks, on the only signal
available, slower and less productive than one who approves quickly
and rarely blocks anything, and the incentive tilts toward exactly
the rubber-stamping that keeps the paperwork intact while the
technical function it was named for quietly stops happening.

## Where a human reviewer and a model reviewer differ

Naming review as a bundle of functions makes the
human-versus-model question answerable function by function instead
of as one verdict, and the answers are not the same shape.

**Accountability is not a capability gap; it is a structural one.**
The fourth function named above — an approval standing for due
diligence performed — depends on attaching legal and organizational
responsibility to a party who can bear it. No amount of model
competence changes this, because the function is not about judgment
quality, it is about where liability lands, and current
organizations and law do not recognize a model as a party that can
hold it. This is the one function of review no improvement in models
closes.

**Independence is a real question for a model reviewing its own
output, and a subtler one for a model reviewing anything.** Two
different humans typically carry different training, different blind
spots, genuinely decorrelated per "Decomposition as a lever"'s
reuse-risk argument run on people instead of code. The same model
reviewing its own prior generation, even in a fresh session, is not
a second sample in the same sense — it is the same generator
checking its own satisfaction, and "The generator is not the spec"
already located the relevant competence in the model itself, which
does not change between a generation call and a review call using
the same weights. A genuinely different model, trained differently,
is closer to a second, decorrelated reader than the same model
reviewing itself — the same independence this page has argued for
throughout, now cashed out as a concrete recommendation: vary the
model, not only the prompt, if the review is meant to catch what the
generator could not see in itself.

**Cost changes which crossover is even worth running.** A model
review is cheap enough to run on every change, which shifts "Match
rigor to what a miss costs" toward applying it everywhere mechanical
judgment reaches, the same collapse in marginal cost "What actually
changed" already named for generation. This does not remove the
bottleneck "What increased writing volume does to reading" already
identified — it only moves it to whatever residue a model cannot yet
judge well.

**Theory of mind is a capability question, not a structural one, and
it may already be closing.** Predicting whether a future reader will
reconstruct an artifact's reasoning is a simulation a competent
model may perform adequately now or soon — unlike accountability,
nothing about the function is reserved for a biological reader by
definition. Where this lands is an open, testable question this page
does not resolve by assertion.

**Locally earned competence is the harder gap to close.** "What
expertise actually is" already argued that a long-tenured reviewer's
value is partly generic skill and partly a tacit, project-specific
history — this team's past incidents, this codebase's specific
fragile corners, norms nobody wrote down. A model has this only to
the extent it was actually given it, which is exactly this
methodology's own argument for externalizing the tacit half into the
spec tree rather than leaving it in a person's head: the gap between
a tenured human reviewer and a model reviewer is not a permanent
ceiling on the model, it is a measure of how much of that project's
history has been written down anywhere the model can read it.

**Consistency cuts in both directions.** A model given the same diff
twice is close to the same reviewer twice, for better and worse — no
fatigue, no mood, no throughput pressure degrading care the way "A
market for lemons" already described for humans, but also no lucky
day where an unrelated association catches something a systematic
pass would miss, and whatever blind spot the model carries, it
carries on every review, not just the ones made on a bad afternoon.

## A theory of engineering, or a theory of software?

The question is worth asking plainly, because the honest answer
changes what this whole page is a theory of. Try transplanting the
primitives to a discipline with none of software's history: civil or
electrical engineering, load-bearing structures, wiring codes. A
space of possible structures satisfying a requirement. An intent
held by people who are not the ones pouring concrete. A description
— architectural drawings, structural calculations, and a building
code that is, quite literally, a spec, authored once and inherited
by every project under its jurisdiction. A generator — the
engineer's design process. An oracle — structural analysis,
inspection, code review, and, in the end, the building standing
under real load for decades, exactly the environment acting as
oracle already argued for software. Chesterton's Fence is barely a
metaphor in construction: the maxim exists because someone,
somewhere, removed a wall they thought was decorative and found out
otherwise. Building codes get amended after a disaster identifies a
failure mode nobody wrote down — a discovery ratified into the
description, governing every future project the moment it is
codified, the identical mechanism this page has called ratifying a
fix into a shared ancestor. The skeleton does not strain. It
transfers.

What does not transfer is a set of parameter values this page has
been treating as background, and naming them is what actually
answers the instinct that something is different. Generation cost,
in physical engineering, is not one number — it is two, radically
apart. Revising a drawing is cheap, cheaper every year as simulation
improves; pouring the foundation is not, and nothing about better
tooling makes concrete less permanent once it sets. Software
collapsed design and artifact into nearly the same act — compiling
is not pouring concrete — and the whole story this page tells about
AI is the story of that already-cheap act getting cheaper still.
Physical engineering never had that collapse available to it and
never will, because the expensive step is gated by matter, not by
information processing. It lives permanently in the corner the old
doctrines already named for pre-AI software — resampling is not
merely expensive, it is architecturally irreversible — except more
so, and without the exit software just found.

The stakes differ too, not just in degree but in distribution.
Software's crossover, argued under "Where the crossover sits," moves
with what a miss costs, and most software sits somewhere in the
middle of that range — a prototype tolerates a great deal, a
pacemaker's firmware tolerates almost nothing. Structural and
electrical engineering do not get a middle: a building that fails is
rarely a minor incident, so the crossover sits permanently at the
far end, and the codified, mandatory, externally enforced body of
standards this discipline is known for is the direct consequence —
the same mechanism the [rationale](/rationale) already named for
aviation and medical devices, applied by default to an entire field
instead of only to its riskiest corner.

There is a further reason those codes are mandatory rather than
merely available, and it follows directly from "Many worlds" and
"Why resampling forever does not scale": a building code is a
ratified discovery, and the discovery was frequently paid for in the
currency this page has tried hardest to avoid needing — a collapse,
a fire, a death. "Decomposition as a lever" already argued that a
shared component's defect set is discovered once and closed for
everyone who uses it, where re-deriving it independently pays the
discovery cost again for no new benefit. Civil and electrical
engineering do not leave that choice to each engineer's private
economics, the way software leaves the choice to reuse a library or
write one from scratch. They make reuse of the ratified rule
compulsory, because the alternative is not merely inefficient — it
is a live bet that whoever skips the code will rediscover, on their
own project, a failure mode someone already paid to identify. What
software calls Not Invented Here syndrome is the same bet, made
voluntarily and usually without noticing it is a bet at all.

And the dimensionality is not the same order of magnitude. A
structure's behavior is governed by physics that, for a well-posed
problem, converges on a narrow band of adequate solutions — the
genuine, non-trivial residual freedom argued under "The region is
never a point" is real in civil engineering but far smaller, because
geometry and material properties do a great deal of the narrowing
before any engineer writes a word. This is also, read through
"Diminishing returns on aim," why a comprehensive, industry-wide body
of codified rules is affordable for a building and has never once
been affordable for software: a lower-dimensional space reaches the
point where a rule has covered most of what is worth covering at a
cost a profession can actually pay collectively. Software's space is
too large for any rule set to reach that point on behalf of the
whole field at once, so each project pays its own way up its own
crossover instead of inheriting one paid centrally.

One disanalogy is worth naming because the model has no place for it
yet, not because it can be waved away. A structure decays on its
own — corrosion, fatigue, material aging — independent of any edit
and any drift in intent, purely a function of time and physics
acting on the artifact itself. Nothing in this page's account of an
artifact has it losing ground while nobody touches it; a stable,
unmodified piece of software does not become less conforming by
sitting still. Physical engineering's artifacts do, and any version
of this model built to reach that far would need a mechanism this
one does not have: decay as a category of drift with no stakeholder
and no discovery event behind it, just time acting directly on the
point.

The honest conclusion is narrower than either instinct on its own.
The primitives do not appear to be specific to software — they look
like a general account of engineering under an imperfect executor
and a partial specification, and civil and electrical engineering
are not a bad fit for it, they are a different, more extreme, and
more permanent point in the same parameter space this page has been
mapping for software's transition. What is specific to software, and
newly to AI, is not the structure. It is a historically unusual
combination of parameter values — collapsing generation cost,
unusually high dimensionality, and stakes that range from trivial to
extreme within a single field — that no other engineering discipline
has had reason to experience all at once.

## Reproduction is not exploration

The claim that physical engineering never had, and never will have,
cheap generation deserves the same adversarial pressure this page
has applied to its other confident claims, and it does not survive
unmarked.

Mass manufacturing is the counter-example: once a mold or a
production line exists, stamping out another unit is nearly free,
and has been for over a century — cheap, repeated, physical
generation, no AI required. If that counts, "physical engineering
never had this collapse available" is simply false, and the earlier
account of what makes software's transition historically unusual
needs to explain why manufacturing does not qualify.

It does not qualify, but the reason is worth stating precisely,
because it sharpens rather than rescues the earlier claim by
accident. This page's generator was never defined as cheap to run
again. It was defined as cheap to run again into a fresh,
independently resolved point — a distribution, not a stamp.
Manufacturing's cheap repetition is the opposite of that: the entire
purpose of tooling is to suppress variance between units, to
reproduce one already-validated design as identically as the
physical process allows. It is a cheap confirm-and-repeat operation
on a point already chosen, not a cheap resample across a region
still being explored. Software's generator, and this page's, is
cheap at the other operation entirely — cheap exploration, a fresh,
differently resolved point on every draw. Mass production was never
in the market this page has been describing, because it was never
sampling.

Something closer to a real counter-example exists, and it is worth
naming because it does not resolve as cleanly. Generative design —
topology optimization and similar algorithms that produce many
structurally different candidate parts against the same load and
material constraints — paired with additive manufacturing, which can
realize any one of those candidates for close to the same cost as
any other, is cheap physical generation in the sense this page
actually means: many independently resolved points, sampled from a
real region, each realizable at near-marginal cost. Wherever this
pairing takes hold, the prediction this page's apparatus makes is
concrete and checkable, not retrofitted: those workflows should show
many-worlds-style defect churn across printed iterations, a
crossover that moves with the stakes of the part, and rising
pressure on the simulation and testing oracle that has to keep pace
with a sampling rate it did not face when a design took months to
tool up for.

The corrected claim is narrower than the one made first, and more
precise for being narrower. It is not physical engineering as such
that was structurally barred from cheap generation. It is
large-scale, site-specific, one-off construction — a building, a
bridge — where the realized artifact cannot be decoupled from a
physical process bounded by matter regardless of how the design was
produced. Smaller-scale mechanical engineering, wherever design and
realization can be decoupled the way generative design and additive
manufacturing decouple them, was never structurally exempt at all.
It was waiting for the same two things software was: a cheap way to
sample many candidates, and a cheap way to check them.

## What decays without anyone touching it

The civil-engineering section conceded a real gap: a structure
decays on its own, independent of any edit or any drift in intent,
and this page's account of an artifact had no place for that.
Software's analogue exists, and it is not underspecification and not
drift, even though it can look like either from inside a single
incident.

A dependency deprecates. A platform stops receiving security
patches. An API the artifact was built against changes its contract
on a schedule this project does not control. None of this touches
the artifact's own text, and none of it requires intent to have
moved — the stakeholder may want exactly what they always wanted.
What has moved is the ambient space the artifact's continued
region-membership was silently conditioned on, the same zeroth-order
dependency "The type system as a pre-paid oracle" already named for
the choice of language, except now degrading instead of merely
aging. A description that was completely satisfied yesterday can be
violated tomorrow with the artifact, the intent, and the description
all held perfectly still.

This is not drift, because nobody's understanding of what they want
changed. It is not underspecification, because the description was
not silent on anything relevant — the gap opened in the ground the
description was standing on, not in the description. It does not fit
"the battlefield assumption" either: a basin can hold a perfectly
good point today and stop containing one tomorrow for reasons that
have nothing to do with any local move made inside it. Call it
environmental drift, to keep the vocabulary while marking the
difference: intent-drift moves the target; environmental drift moves
the ground the artifact stands on, with no stakeholder and no
discovery event behind it, purely a function of time acting on
everything the artifact depends on but does not itself define. A
codebase that is never touched is not, contrary to an assumption
running quietly through most of this page's economics, a codebase
whose region-membership is stable. It may simply be a codebase whose
decay is not yet visible, for the same reason a dormant bug is not
yet visible: nobody has run the query that would reveal it.

## What would count as being wrong

A lens that never risks being wrong is not yet earning the
confidence its own vocabulary projects, so it is worth stating a
prediction plainly instead of only conceding, in general terms, that
none of this produces a number.

The clearest one concerns defect signature, and it has not been
checked. Teams that resample and regenerate heavily should show more
regressions of previously fixed behavior and fewer long-tenured,
familiar bugs, because only what was ratified into a test survives a
re-roll and everything else is re-decided on every draw. Teams that
patch a stable artifact for years should show close to the opposite
signature: bugs that persist for a long time once introduced, but
few regressions, because the code causing them is not being
re-sampled. If regeneration-heavy teams turned out to have fewer
regressions and more long-lived bugs than patch-heavy teams, that
would be evidence against the account "Many worlds" gives, not a
maxim this page has not gotten around to translating yet. Nothing
here has gone looking for that data. It should be possible to, and
until someone does, every claim built on top of "Many worlds" carries
this untested weight along with it.

## Limits

This is a lens, not a predictor, and it should not be sold as more.
The space of programs has far more dimensions than intuition
expects, and displacement along most of them does not correlate
with behavior at all — a renamed variable, two independent
statements reordered, one standard-library function swapped for an
equivalent one: each a real move in the space and a null move in
what the program does. A small number of other dimensions behave
the opposite way: flip a comparison operator, shift a loop bound by
one, swap two arguments of the same type, and a single-token edit
lands the artifact in a different region of the space entirely.
Textual distance — the only distance cheap enough to compute —
tracks neither kind of dimension: it overstates the move for most
of them and misses it completely for the few that matter.
"Basin" and "landscape" are borrowed from continuous optimization
and used here loosely; nothing in this model computes a gradient in
the sense an optimizer would recognize. Intent is latent, partial,
and moves as the people who hold it learn more about their own
problem — no description ever fully catches up to it, and the model
does not claim otherwise. And the oracle only ever certifies what
someone thought to check; it has no opinion about the region it was
never built to see.

None of that produces a number. What it produces is a reformulation
that separates ill-posed questions from decidable ones — "spec or
code?" is not decidable as stated. "Where does the second half of
the pair live, and does it survive who is holding it?" is.

## Which economics is this?

Every crossover, every diminishing return, every mention of what an
artifact is worth has been economics from the first section, done
without stopping to name which branch. It is worth stopping now,
because naming it exposes both a genuine debt and a genuine risk.

At least four distinct traditions have been borrowed, not always
announced. The plain optimization behind "Where the crossover sits"
and "Diminishing returns on aim" — set marginal cost against
marginal value, stop where they cross — is standard microeconomics,
the same tool that prices anything under a budget. The
probability-weighted stakes multiplying a rule's worth by what a
miss would cost is decision theory under uncertainty, expected value
in its oldest form. "A market for lemons" borrowed a named result
outright: Akerlof's adverse selection, where a buyer's inability to
observe quality before the fact collapses the price paid for it,
applied here to engineers evaluated on velocity instead of a car's
paint job. "What an adversarial generator is" is closer to a
different classical problem — a principal who cannot observe an
agent's true effort and must reward a proxy instead, the same
hidden-action difficulty that gives Goodhart's law its economic
teeth, distinct from Akerlof's hidden-information problem even
though both surface in this page as "the visible signal is not the
real one." The value the spec was given under "The questions this
reframes" — the value of the option to regenerate, to switch
generators, to bring in a new team — is real options reasoning,
pricing the flexibility itself rather than any specific outcome it
might produce. And the monoculture risk "Decomposition as a lever"
conceded as reuse's honest cost is portfolio theory in miniature:
concentrating a bet correlates its failures, diversifying it does
not remove the failures but decorrelates them.

None of this was imported for decoration. Each branch earns its keep
by producing a claim this page would not have reached without it —
that careful engineers get structurally underpaid until a faster
oracle exists, that a rule's worth depends on a cost term with
nothing to do with how likely the failure is, that a shared
component's risk is a different kind of risk than an independent
one's, not just a smaller or larger amount of the same kind.

The risk runs in the same direction "Limits" already named for
physics metaphors, and deserves the same discipline. Real economics
comes with assumptions this page has not checked — rational actors,
competitive markets, prices that actually clear — and borrowing a
result without borrowing the conditions that produce it is how a
genuinely explanatory analogy quietly turns into decoration wearing
a more impressive name. The honest use of each branch here has been
structural, not computational: microeconomics for the shape of a
tradeoff, not a solved optimization; Akerlof for the mechanism of
adverse selection, not a fitted market-clearing price; portfolio
theory for the distinction between correlated and independent risk,
not a variance figure. Where this page has done that, the economics
is load-bearing. Where a future pass finds a section using economic
language to sound rigorous rather than to make a checkable claim,
that is the same test "The battlefield assumption" already had to
survive, run again on a different vocabulary.
