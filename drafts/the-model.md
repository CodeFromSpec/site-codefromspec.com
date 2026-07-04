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
already named under Limits — are high on the first. Naming turns out
to be high on the second while staying at zero on the first, and
nothing before this section had room to say so.

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
always favor patching, because generation was the expensive side of
it. It no longer reliably does.

This gives the earlier open question a criterion, if not a number:
drift is a signal to resample rather than merely patch exactly when
the distance it has moved exceeds what a local diff can absorb
without inheriting anchoring's failure mode — and the same collapse
in generation cost that makes ordinary resampling affordable makes
this specific move, re-specify and then regenerate, cheaper than it
has been for as long as the discipline has existed.

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

## What actually changed

AI changes three of the parameters above. It changes none of the
structure.

**The cost of generating collapses.** Sampling, which used to happen
once per system across a career, becomes a routine operation,
cheap enough to run on every change. This is also the first time
the distribution becomes observable: the non-determinism was always
there — two engineers, two Mondays — the model just made it fast and
frequent enough to see.

**The generator loses its memory.** A hosted model retains nothing
between invocations. The tacit half of the pair — the record of
which ambiguities were resolved and why — does not live anywhere
unless something outside the generator holds it. Naur's theory,
which used to survive precariously in the heads of a stable team,
now has no home at all unless one is built.

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
economics, an oracle with any blind spot at all tends to get found
— not because the generator is malicious, but because search
converges on whatever the fitness function rewards, and the fitness
function here is "passed the oracle," not "satisfies the intent the
oracle was built to protect."

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
argument given under Limits. Boundary values, error paths,
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
