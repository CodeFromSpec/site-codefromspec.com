# A (peculiar) theory of software engineering

Start with two observations about generating code with a stochastic model:

- A spec run through a non-deterministic executor defines a space of programs, not one single program; and

- the generated artifact is the point in that space you landed on.

Both observations are, in the strict sense, facts about a model rather than metaphors borrowed for one. A
model has an actual probability measure over what it produces, sampled once per invocation from a distribution
conditioned on a prompt — temperature, top-p, and seeds are the vocabulary of sampling because the process actually
is sampling. Everything that follows — probability mass, expected value, a rule's power as an actual likelihood of
violation — depends on that literal sense holding, and it holds for exactly the kind of executor described here:
one context in, one pass, a genuine distribution over outputs, checked afterward. This essay is about what follows once
such an executor exists, and what does not follow from its existence turns out to matter just as much as what does.

## Part I — The primitives

There is a **space of programs**, every artifact expressible in some language. There is an **intent** — what
the system is actually supposed to do, held by the people who need it, known incompletely even to them, revised on
contact with reality. There is a **description** — anything that narrows the space to a region of acceptable
programs: a spec, a test suite, a type system, a ticket, a convention nobody wrote down but everybody enforces in
review. A description does not have to be versioned to count; it only has to constrain. There is a **generator**
— any process that, given a description and a context, produces a program: a distribution over the space,
sampled once per invocation. Even before any description narrows it, that distribution is nowhere near uniform
— training already concentrates almost all of its probability mass on a vanishingly small, highly conventional
corner of an otherwise limitless space, because the training objective rewards predicting what a corpus of
real code already made likely, and that corpus was never uniform over the space to begin with. There is an **oracle** — the
partial procedure that decides membership in the region, on whichever dimensions it happens to cover. And there
is the **artifact** — the point you actually have, carrying whatever evidence of survival it has accumulated:
tests it has passed, traffic it has served, incidents it has not caused.

Two of these roles are easy to conflate, and the conflation causes real damage downstream. The description's job is
**aiming**: it conditions the distribution the generator samples from, shifting probability mass toward the region
it describes. Aiming is statistical, not geometric — a good description makes landing outside the region unlikely,
not impossible, and the generator remains free to miss on any single draw. Aiming narrows an already-narrow thing
further — it does not carve order out of true uniformity, because, as already noted, there was never any
uniformity to carve it out of. The oracle's job is **confirming**, and
confirming is bounded rather than statistical: it decides membership by checking specific properties, on whichever
dimensions someone built it to check. Passing every check an oracle can run is not the same proposition as belonging
to the region the description actually defines — it is the narrower claim of belonging to the intersection the
oracle happens to observe. Outside that intersection there is no confirmation available, only the aim, and whatever
confidence the aim earned. Hold this distinction in mind; almost everything below is a variation on it.

### The generator is not part of the description

A description does not absorb the generator just because the generator resolves what the description leaves
silent — that is the definition of a generator, not a discovery about models. A description conditions a
distribution; a generator is the distribution, and something has to supply it regardless of what any description
says. A human filling in a silence, drawing on years in a domain, and a model filling it from training data are
doing the identical job with the identical structure.

The consequence is worth stating plainly: the generator is not something to version. Nobody proposes
versioning an engineer alongside the spec they implement, and a model earns no different treatment by being
software. Whatever the generator resolves on its own is either safe to leave stochastic or was never safe to
leave silent — and the remedy for the second case is a narrower description or a stronger oracle, not a frozen
generator.

### The operations

Four operations act on these parts. **Sampling** produces a fresh point from the generator. A **local move** —
a diff — nudges the existing point without resampling the rest. **Resampling** discards the point and draws a
new one, independent of the last. And **ratifying** moves a discovery from the environment into the description or
the oracle, so that future samples are constrained by it rather than merely lucky with respect to it. Underneath
all four sit three costs that determine which one is rational at any moment: the cost of generating a candidate,
the cost of checking it against the oracle, and the cost of the next local move once you are standing on a given
point — call it the cost of the local gradient.

That is the whole apparatus. Nothing in it names a model, a programming language, or a decade.

### What a bug is

Given these primitives, a bug has a precise shape, and it comes in two kinds worth telling apart, because the
remedy differs.

The first kind is **nonconformance**: the description spoke on the dimension in question, and the sample still
landed outside the region it defined. This is a plain generation error — the aim was adequate, the draw missed
anyway. A faithful oracle catches it by construction, provided the oracle happens to check that dimension. When
it slips through regardless, the gap is in the oracle's coverage, not in the description.

The second kind is the more interesting one, and a good share of what surfaces in production fits this shape better
than the first: **underspecification**. The description never spoke on the dimension at all — a legitimate silence,
since no description of useful size closes every degree of freedom — and the generator resolved it however its own
priors favored. Most such resolutions are harmless: the behaviors compatible with the silence mostly overlap with
what the stakeholders would have accepted, had anyone thought to ask. A bug, on this reading, is the case where the
coin landed on the side nobody wanted: the sample is fully conforming to everything the description said, and still
wrong, because the description never said enough to rule the outcome out.

The distinction determines the fix. Nonconformance is repaired by extending the oracle to catch the violation
mechanically, or simply by generating again against the same description, since the description was never the
problem. Underspecification is repaired by narrowing the description along the dimension the incident just revealed
— and, in the same motion, teaching the oracle to check it, so the fix holds even after the code that currently
embodies it is gone. This is the operational meaning behind "the fix goes into the spec and a test pins it": one
clause narrows the aim, the other closes the oracle's blind spot on that one dimension, and only doing both makes
the correction durable across a resample instead of true only of the current point.

This also sharpens what **ratifying** means as an operation. It is not merely recording a decision after the
fact. It is retroactively narrowing the region a description defines, to exclude a point a sample just proved was
reachable and unwanted — intent folded back into the description that was supposed to
have contained it from the start.

### Closed oracles and open oracles

Not all oracles fail the same way, and the difference shows up in an experience every engineer recognizes. When
the compiler rejects a program, nobody's first move is to suspect the compiler — the code gets fixed. When a test
fails, suspecting the test is a legitimate question, and sometimes the right answer. Both are oracles delivering a
verdict; only one of those verdicts gets taken at its word. The distinction underneath is whether the verdict itself can
be ambiguous.

It is tempting to answer that a compiler is simply a different kind of thing, but the honest reason is less
categorical. The language standard and the compiler's implementation of it are, strictly, two independently
authored accounts of the same behavior, and a compiler bug is exactly a disagreement between them, which means a
compiler is not different in kind from a test, only in how thoroughly it has been ratified. What actually makes a mature compiler behave as if it could not disagree with anything is that
the standard-versus-implementation gap has been driven, by long exposure and heavy use, to a probability low enough
to treat as zero. Call a **closed oracle** the limit of that process: an oracle so heavily ratified against its
own governing description that disagreement is negligible.

A freshly written test sits at the other end of the same line. It is authored separately from the implementation,
deliberately independent, and that same independence means its failure can mean one of two different things: the
implementation violated something the spec and the test agree on, or the test and the spec disagree with each other,
and the implementation is caught in the middle. Call this an **open oracle** — not a different kind of thing from
a closed one, but one recently or thinly enough ratified that disagreement with its own governing description is
still a live possibility.

## Part II — Finite descriptions, infinite space

### The dimensions that do and do not matter

The space of programs has far more dimensions than intuition expects, and displacement along most of them does not
correlate with behavior at all — a renamed variable, two independent statements reordered, one standard-library
function swapped for an equivalent one: each a real move in the space and a null move in what the program does. A
small number of other dimensions behave the opposite way: flip a comparison operator, shift a loop bound by one,
swap two arguments of the same type, and a single-token edit lands the artifact in a different region of the space
entirely. Textual distance — the only distance cheap enough to compute — tracks neither kind of dimension:
it overstates the move for most of them and misses it completely for the few that matter.

A dimension can be free with respect to the region a description defines — it never changes which programs count
as conforming — while remaining expensive with respect to everything else worth counting as a cost: the price a
human pays to confirm a candidate, the price a future editor pays to aim correctly at what still needs deciding,
the price of a local move accidentally touching a dimension that does matter. Naming is one example of this kind
of dimension. A name never touches the region a description defines. It constantly touches how cheaply anyone —
human reviewer, future engineer, generating agent reading its own prior output — can tell a load-bearing dimension
from an inert one before touching it; a bad name misdirects exactly the judgment whose failure the third cost
above prices.

**Behaviorally free** and **cost-relevant** are independent properties, not the same property under two names.
Most dimensions are low on both. A few carry outsized, catastrophic sensitivity. Naming is high on the second
while usually low, not zero, on the first — reflection, serialization keys, ORM column mapping, and a public
API's own symbols are the exceptions.

### The battlefield assumption

Push the apparatus to a limit case, exactly to see what it assumes without saying so: grant a perfect oracle — no blind
spot, deciding any sample against true intent, for free. The space of programs does not shrink because the oracle
got better. Even restricted to strings over a finite alphabet it stays infinite — so the set of points that fail
intent stays infinite too. No
finite description can pre-empt an infinite defect surface — enumerating every way a program could go wrong would
make the description as long as the space it describes.

So "eliminate every bug in the spec" is not a high bar nobody has cleared yet; it is simply not a coherent goal. 

What happens in practice is more pragmatic: rather than trying to anticipate every
failure before shipping, deploy the artifact at hand and fix only the defects production actually reports — one
at a time, as they arrive — inside the neighborhood reachable from that point by local moves. The battlefield is
not the whole space of possible failures; it is the narrow, specific set an artifact actually meets once it is out
in the world. This is analogous to Herbert Simon's concept of satisficing: not "find the best program the space
permits," but "find a program good enough, against the defects that actually show up, to stop looking for a
better one."

### The evidence for it is survivorship

Satisficing this way rests on an assumption nobody states, because most neighborhoods honor it well enough that stating
it never seemed necessary: *every neighborhood entered contains a point good enough, reachable by successive local
corrections, at a cost that stays acceptable.*

That confidence deserves a caveat before it hardens into proof. The strongest evidence usually offered for it
is exactly the wrong kind: most software anyone can point to was built this way, therefore the assumption must
generally hold. That is a selection effect — a live, maintained codebase is observable precisely because it
satisficed; a project that never found its neighborhood's good-enough point does not stick around to be cited
alongside it. It gets rewritten, abandoned, or left running in a permanent, expensive, never-quite-acceptable
state that nobody offers as a positive example, though each is evidence the assumption failed. The population
of examples is already conditioned on having satisficed — the same reason a stable artifact's untested dimensions
can look safe: the counter-examples are exactly the ones nobody gets to see. The honest position is narrower than
"the assumption is probably true." It is that failure is visible only after the fact, in technical debt and drift,
with no way to know in advance, for a specific neighborhood, whether it holds.

### The region is never a point

Push the same limit case one step further, in the other direction. The battlefield assumption argued that a finite
description, however detailed, cannot pre-empt an infinite defect surface. The same argument runs in reverse:
does a spec ever define a single program?

Not even a perfect one does. Some of the infinitude inside a region is the trivial kind already named — renaming,
reordering, swapping equivalents — free in every sense, costing nothing and buying nothing. But there is a
second, non-trivial multiplicity underneath the trivial one, and it exists for the identical reason the defect
surface stays infinite: intent is a finite object, stated by finite people in finite time, and a finite set of
constraints on a space with effectively unbounded dimensions cannot close all of them — not because anyone
was careless, but because there is always another dimension nobody has reached yet. Error handling for an input
nobody has tried. Behavior under concurrent access nobody described. The order two independent side effects fire
in. None of this is a defect, if intent never had an opinion on it — and none of it is fully resolved either. A
program that logs verbosely and one that logs nothing can both satisfy the identical, fully-articulated intent,
and there is no sense in which the spec, however complete, prefers one.

So the two questions collapse into one answer. A finite description, run against a space this large, is always
satisfied by infinitely many programs — part of that infinitude free and meaningless, the rest genuine and
unresolved, for exactly the reason an infinite defect surface survives even a perfect oracle. A region is not a
fallback for specs written carelessly. It is what any spec is, always, because the space has more room than any
finite intent can fill. Collapsing to one point is not the spec settling a disagreement it failed to see. It is
sampling doing the one thing a finite description structurally cannot: choosing.

### Diminishing returns on aim

There is a mechanism worth making precise, because the intuition behind it is right and the usual way to state it —
counting how much of the space a rule "removes" — breaks down the moment the space is infinite. Subtracting infinite
sets from infinite sets does not say which rule mattered more. The quantity that actually behaves the way intuition
wants is not a count of excluded programs. It is probability mass under the generator's own distribution — the
same notion of aim already established: a rule's power is the probability that the generator, left unconstrained,
would have violated it.

Stated this way, the overlap argument holds rigorously, by a fact from probability rather than a leap of intuition:
the chance of violating at least one of several rules is at most the sum of the chances of violating each on its
own — Boole's inequality, the same union bound the intuition was already reaching for. Two rules whose likely
violations overlap buy less together than the sum of what they buy apart.

But overlap alone does not yet explain why the *first* rule written tends to buy the most — that needs the
failure modes a spec could name to be unevenly sized, and they generically are. A handful of ways a program goes
wrong are common enough that almost any unconstrained sample trips one of them; the rest are individually rare,
in a long tail with no end. State the common failure modes first — which is what a competent spec, or even an
inattentive one, tends to do — and every rule after the first has to work with what the earlier ones already
excluded. This is not a law forced by the structure of the space. It is what happens when rules are written in the
order people actually write them, and it gives the battlefield assumption its economic twin: that section argued
a spec cannot eliminate every bug, because the defect surface stays infinite; this adds the reason not to try —
the marginal rule costs about as much effort as the one before it, and buys steadily less.

### Where the crossover sits

Diminishing returns on the spec side pairs with a cost that does not fall nearly as fast: writing, reviewing,
and carrying a rule costs about the same whether it guards against a common failure or a rare one — if anything,
cost tends to rise on the way into the tail, because rarer failure modes are also harder to think of in the first
place. Put a shrinking marginal value against a flat or rising marginal cost and there is a crossover point,
in principle even if never as a clean number: keep adding rules while the next one's probability-weighted value
exceeds what it costs to write and carry, and stop once it does not.

Nothing about that crossover point is fixed. It moves with the cost of what a surviving failure would do. A rule's
value is not just the probability mass it removes — it is that mass multiplied by what happens if the excluded
failure occurs anyway, undetected. Where a failure costs an afternoon of cleanup, most of the long tail is not
worth a rule; the crossover arrives early. Where a failure costs a plane, or a misdosed patient, or a regulator's
attention, the identical probability-weighted calculation justifies chasing the tail much further, because the
cost term on the other side of the multiplication is enormous. This is not a new observation for this discipline:
aviation and medical devices are the two domains where specification-heavy methods survived, for a reason usually
stated in different words — an external force pays the cost of rigor. The mechanism behind that reason is the
one just given. Those domains do not run on a different mathematics of diminishing returns; they carry a different
multiplier on the value side of the same crossover.

### Why resampling forever does not scale

This crossover has a direct consequence for a strategy that looks attractive precisely because generation got cheap:
discard the artifact and regenerate from the spec on every change, rather than patch — treating the spec as
sufficient on its own, with nothing lost by never keeping what it last produced. That is not so, and the reason is
a fact about defect sets. The defects of one program are a bounded set that tends to shrink as fixes get ratified
and pinned — though not monotonically, since a bad fix can reintroduce an old defect and an untouched artifact
can still lose ground to its environment, discussed later — while the defects of a distribution of programs are
not bounded at all. Each regeneration is a fresh sample from that unbounded distribution, exposed to a fresh set of
bugs, and the maturity a stable artifact accumulates never gets the chance to compound. The crossover point adds the
reason the obvious fix — write a spec large enough that any sample from it is safe — does not rescue the strategy.

Making repeated, independent resampling safe requires the spec to pre-empt the failure modes a fresh sample could
land on, and that is exactly the job diminishing returns makes expensive precisely where it matters most: not the
common failures, which any reasonable spec already covers, but the long tail a stable artifact would otherwise
leave dormant for free. Resampling on every change asks the spec to do, on every single cycle, the job a stable
artifact only has to do once, gradually, at the pace incidents actually arrive.

The oracle can substitute for some of this, and it is generally the cheaper lever: a test confirms mechanically
where a spec clause can only shift probability, so a unit of writing effort buys more certain protection as a test
than as prose. But the oracle is not exempt from the same underlying scarcity — it too meets common failures
first and a long tail after. Resampling forever is maladaptive not because resampling went back to being expensive
— it never did — but because the safety net a spec or an oracle can economically provide keeps the same shape
it always had: dense where failures are common, thin in the tail. Cheap generation does not change that shape. It
only changes how often the strategy asks you to jump without one.

## Part III — Locality, drift, and the shape of change

### Why pinning a defect pays off locally, and mostly locally

Every fix made this way, per the earlier taxonomy, gets ratified into the oracle: a test is added that would have
caught it. That test extends what the oracle can *confirm*; it does not necessarily narrow what the description
*aims* at.

The value of that addition is not uniform across the space. It is highest inside the battlefield actually being
fought on, and close to zero outside it — the defect a test catches is, by construction, a property of the region
you are in, not of the whole space the description permits. Resample into an unrelated implementation of the same
intent, or redesign away the mechanism that produced the defect, and the pinned test may simply never fire again
— not because it is wrong, but because the trap it was built to catch no longer exists where you have gone.

The practice pays off anyway, on average, because of locality: most dimensions of the space do not correlate with
behavior, but a few carry outsized, catastrophic sensitivity, and a local move is far likelier to perturb one of those
few than an independent resample is to land on the same one by chance. A diff operates on the same representation,
often near the very lines that caused the defect the first time. The description could carry a warning there too
— narrowing it is literally the other half of ratification — but a warning written into the description only
shifts probability; it does not bind. A constraint stated plainly in the description still only conditions what the
generator is likely to do. And even where the description's warning lands, it only acts at the moment the
description is consulted again; it says nothing to an
edit that touches the artifact without regenerating from the description at all. The oracle's warning fires on any edit,
whether the description was consulted or not. A pinned test is what is left standing in the gap the description cannot
close alone — not because prose is forbidden there, but because prose, however present, only ever aims.

This also names the failure symmetric to underspecification. A test can pin more than intent. Written against the
current point's specific shape rather than the contract it is meant to protect, it freezes an accident as if it
were a requirement — dead weight the moment the battlefield changes, and worse while it lasts, because it can
veto a future, better resolution of an ambiguity a next generation would otherwise be free to answer on its own
terms. A test written from the contract a description makes is a test about intent, and keeps its value on every
battlefield that intent still governs. A test about the accident only ever had value on the one battlefield where
the accident happened to occur.

### The environment as the slowest oracle

There is a layer of the oracle worth naming on its own: production. Every input the software actually receives,
over its whole operating life, is a draw from a distribution nobody wrote down and no test suite enumerated in
advance — and each one is, functionally, a query to the oracle: does this sample belong to the region intent
actually wants? Most queries return an unremarkable yes and change nothing. The ones that return no are incidents,
and each incident triggers a fix that narrows the description and extends what a cheaper, faster layer of the
oracle — a test — can now catch without waiting for production to ask again.

Read this way, a program's maturity is not a metaphor and not merely age. It is the accumulated record of one specific,
very expensive, very slow oracle — the world, running the software — having fired against the artifact repeatedly,
with every firing ratified back into a cheaper layer so it need not fire the same way twice. Production sits above
types, property checks, and example-based tests in both coverage and cost per verdict, and it is the only layer
that checks against intent directly rather than against somebody's earlier, necessarily incomplete encoding of
intent into a cheaper check. That is also its danger: by the time it returns a no, the cost was already paid in
production, not in a build.

### When the target itself moves

Everything so far has quietly assumed that intent holds still while the artifact catches up to it. That assumption
is convenient and false. Intent is not filed away complete somewhere, waiting to be read correctly; it is held,
partially, by people who revise what they think they want as they watch the software behave, as the market around
them changes, as a regulator publishes a new rule. The target most software chases is not stationary. It drifts,
for reasons that have nothing to do with anyone's mistake.

This has to be told apart from underspecification, because the two look identical from inside a single incident. An
underspecification bug is intent that was already fixed at the time the description fell silent — closing the
gap resolves it for good. Drift is intent that has genuinely changed since the description last spoke — closing
today's gap resolves nothing, because tomorrow's incident will report the same code failing against a target that
is, by then, somewhere else. Treating drift as underspecification produces a description that keeps narrowing,
with great discipline, toward where the target used to be.

Drift is a second way the battlefield assumption can fail, distinct from technical debt. Technical debt is the
destination standing still while the road there gets more expensive with every step. Drift is the road staying cheap
while the destination keeps moving. None of this is new to people who build software — it is the oldest complaint
in the discipline, usually filed under "requirements churn." What this lens adds is the reason that complaint
is not a failure of discipline: intent is discovered by contact with a working system as much as it is specified
ahead of one, and a description, however carefully authored, is a snapshot of intent at the moment someone was
willing to commit it to writing — not a claim that intent will hold still afterward. Peter Naur's argument about
programming as theory-building has the same shape once more, discussed at length below: the theory a team holds
is not static either, and the reason it degrades on turnover is the same reason a description degrades under drift.

There is a natural instinct worth stating so it can be qualified: survivor evidence only grows more valuable
with age, so the case for resampling only weakens over time. That holds when intent stands still. Under drift,
an old artifact's survivor evidence is evidence about a target that no longer fully exists — still valuable, but
depreciating on a schedule nobody has found how to write down. How to tell, in practice, whether a given incident
is asking for a local correction or announcing that the target has moved far enough to make the whole battlefield
the wrong one, is answered only partially by what follows.

### Outrunning drift

Chasing drift through successive local diffs means walking the terrain between the point currently held and
wherever the new intent's optimum now sits, and that walk inherits every cost already named. The local gradient
gets steeper the farther the walk has to go, because a diff is built to make a small, bounded change, not to
reshape an artifact's structure wholesale. And the walk runs straight through the regime where anchoring — an
agent, or a person, over-weighting the existing artifact against what the spec now says — does the most damage:
the more intent has moved, the more the existing artifact resists exactly the range of change now required. Drift
large enough to demand reshaping architecture, not adjusting a rule, is drift large enough that local correction
may cost more than starting over.

Starting over, though, is no longer the operation it used to be. Re-specifying is cheap — rewriting a description
in natural language to match a new understanding of intent costs a fraction of what re-architecting the same
amount of behavior by hand would. And once the description is rewritten, generation is cheap too. The comparison
that decides whether to patch through drift or resample past it is therefore no longer patch-cost against the
sunk survivor evidence alone. It is patch-cost — inflated by how far intent has moved and by anchoring risk —
against the cost of re-specifying plus a fresh sample. That comparison used to favor patching almost by default,
because generation was the expensive side of it. Whether it still does, in a given case, is no longer something
the old default can answer for you.

Drift is a signal to resample rather than merely patch exactly when the distance it has moved exceeds what a local
diff can absorb without inheriting anchoring's failure mode. That criterion should be read for what it is —
a sharper restatement of the tradeoff, not a resolution of it, because both sides of the comparison are exactly
the unmeasurable quantities conceded throughout this section: nobody has a number for how far intent has moved
or how much a local diff can absorb before anchoring takes over. What changed is not that the comparison became
computable. It is that the collapse in generation cost moved the break-even point somewhere a team now has to
actually go look for, instead of a point so far away that the old default could stand in without anyone checking.

One assumption here deserves the same scrutiny the battlefield assumption already turned on itself: "generation is
cheap" has been quietly carrying "and generates something adequate" alongside it, and those are different claims. A
cheap resample that lands somewhere worse than the patch it replaced is not a counterexample to anything argued
here, but nothing establishes that cheap generation is competent generation — only that it is cheap. Where that
gap is wide, the comparison above should be read as conditional on it closing, not as evidence that it already has.

### What decays without anyone touching it

One more category belongs here, distinct from both underspecification and drift, even though it can look like
either from inside a single incident.

A dependency deprecates. A platform stops receiving security patches. An API the artifact was built against changes
its contract on a schedule this project does not control. None of this touches the artifact's own text, and none
of it requires intent to have moved — the stakeholder may want exactly what they always wanted. What has moved is
the ambient space the artifact's continued region-membership was silently conditioned on — the same zeroth-order
dependency discussed later for the choice of language, except now degrading instead of merely aging. A description
that was completely satisfied yesterday can be violated tomorrow with the artifact, the intent, and the description
all held perfectly still.

This is not drift, because nobody's understanding of what they want changed. It is not underspecification, because
the description was not silent on anything relevant — the gap opened in the ground the description was standing
on, not in the description. Call it **environmental drift**, to keep the vocabulary while marking the difference:
intent-drift moves the target; environmental drift moves the ground the artifact stands on, with no stakeholder
and no discovery event behind it, purely a function of time acting on everything the artifact depends on but does
not itself define. A codebase that is never touched is not, contrary to an assumption running quietly through most
of this essay's economics, a codebase whose region-membership is stable. It may simply be a codebase whose decay is
not yet visible, for the same reason a dormant bug is not yet visible: nobody has run the query that would reveal it.

## Part IV — Where to draw the lines

### Decomposition as a lever

Reuse, componentization, and object-oriented encapsulation are usually taught as hygiene: don't repeat code, group
related things, hide what callers should not see. Read under this lens they are something sharper — a decision
about where to draw the boundaries of a battlefield, made because the primitives apply recursively. A component is
not a metaphor for a smaller version of the system; it is a smaller instance of the exact same structure — its
own intent, its own description, its own generator, its own oracle, its own artifact accumulating its own survivor
evidence — nested inside a larger one.

**Reuse**, seen this way, is a decision about how many independent samples a subproblem gets drawn as. Solve it
once and reference the single result from every call site, and there is one artifact, one description, one oracle to
ratify fixes into, and one accumulating record of survival. Solve it independently at each site — copy the logic,
or re-derive it from scratch — and each copy is its own draw from the space, with its own uncorrelated defect
set, the cost of resampling triggered here by duplication instead of by time. Reuse also accelerates maturity in a
way plain deduplication does not get credit for: every call site's traffic funnels through the same point, so the
shared component meets the environment acting as oracle far more often, per unit of time, than any one duplicate
would alone. Solved once and shared, a subproblem matures at the rate of the whole system's use of it. Solved N
times, it matures N times more slowly, with N different defect sets to eventually discover.

**Encapsulation** — hiding a component's internals behind a public contract — is a defense against Hyrum's
Law, drawn structurally rather than promised by convention. Hyrum's Law says that with enough users, every
observable behavior becomes something somebody depends on, whatever the contract says — and it bites on whatever
is observable. A caller cannot ratify a dependency on a resolution it cannot see. This buys something the other two
levers do not: internals can be resampled or restructured freely, accumulating none of the lock-in Hyrum's Law
would otherwise impose, while the public contract keeps maturing on its own, undisturbed survivor evidence —
internal churn and external stability decoupled by the same wall that keeps a caller from seeing what it should
not.

**Single Responsibility** narrows the battlefield itself rather than just its visibility. A component that owns
one concern has fewer behaviorally load-bearing dimensions bundled into one region, which moves three things in its
favor. Diminishing returns on its spec arrive sooner and cheaper. Drift is contained: when the stakeholder concern
behind one responsibility moves, only the component that owns it needs to be re-fought, and the survivor evidence
accumulated by every unrelated component stays untouched. And the union-bound risk of a local move nicking something
it shouldn't drops with it: fewer nearby load-bearing dimensions to possibly touch.

### Partitioning dimensions, not shrinking them

One correction is worth making before decomposition is mistaken for something stronger than it is. Componentization
does not shrink the total dimensionality of a problem — the decisions a system has to make do not disappear
by being split across files, and drawing a boundary between two components typically adds a few new ones at the
seam: what the contract says, how a failure crosses it, what either side may assume about the other. The union of
dimensions across every component is not smaller than the monolith's, and is sometimes slightly larger.

What shrinks is not the count. It is the **interaction surface** — how many other dimensions any given dimension
can combinatorially entangle with. A monolith holds every dimension in one region, where a local move can, in
principle, reach any of them. Partition the same dimensions into components with a real boundary between them,
and a local move inside one component can only ever reach the dimensions inside it — the reachable set per move
drops from the whole system to one component's share of it, even though nothing about the system's total complexity
changed. Fewer dimensions to entangle with per box, not fewer dimensions overall.

Reuse is a related but separate lever. Partitioning decides how dimensions are grouped; reuse decides how many
independent samples get drawn for a given group. The two compose well precisely because they act on different things.

## Part V — Where the tacit half used to live

Here is the fact worth taking seriously without overstating it: two teams handed the same requirement produce
different codebases. The same developer, implementing the same ticket on two different Mondays, produces different
modules. There has never been a deterministic executor turning intent into code. Calling this sampling, in the literal
sense used so far, would claim too much. The process by which a person turns a ticket into code is not one context,
one pass, one check afterward. It is thousands of interleaved micro-operations — write a line, run it, look at
what happened, revise, ask the person who filed the ticket what they actually meant, revise the understanding of
the ticket itself mid-stream. Generator, description, and oracle are not separable roles for a human the way they
are for a model; they are entangled in a loop that produces the same directional fact — output varies under
incomplete specification — without ever being one clean draw from one clean distribution. The primitives here
fit a model's process exactly. Read onto a human's, they are a simplification of something more tangled, useful
because the tangle still produces the variability, not because the tangle is secretly the same shape underneath.

What that variability looked like, historically, had two features that made it easy to miss. The first is that
a whole system's worth of it happened once per system, across a career — nobody notices they are looking at
variability when they have only ever seen one output from one team. The second, and more important: the human
process, tangled as it is, remembers its own history in a way nothing about a model does. This is where Peter
Naur's 1985 argument about programming as theory-building lands with precision. Naur's point was that a program
is not its text — it is the theory the team holds about how the text maps onto the world, and that theory is not
recoverable from the artifact once the team disperses. Through this lens, that theory is exactly the tacit half of a
pair: the record of which ambiguities were resolved, and how, and why. The pair — description plus resolution
— always existed. The description-half was written down, sometimes. The resolution-half lived in the heads of
the people who wrote the code, and moved when they did.

### What expertise actually is

Extend the same idea across a career and it names something worth stating precisely: an engineer who has spent years
working in one region of the space of programs is not merely faster than a novice. They are better calibrated in
three distinct places this apparatus already separates.

They are a **better generator**. Enough excursions into a domain condition an engineer's own sampling the way a
description conditions a model's: the first solution that comes to mind already carries the weight of every past
excursion that worked and every one that did not.

They write **better descriptions**. An engineer who has been burned by a domain's specific traps knows where they are
without discovering them again, and a spec they write states precisely the dimensions that matter while staying silent,
correctly and cheaply, on the ones that do not. A novice's silence is ignorance. An expert's silence is judgment.

And they are a **good oracle in their own right**. A senior engineer reading a candidate solution can often sense
something is wrong before any test catches it — not intuition in any mystical sense, but compressed pattern
recognition, built from having personally paid for enough failures in that region to recognize their shape on
sight. This is the environment acting as the slowest oracle, run at the scale of a career instead of a single artifact.

A cheap generator is a fast sampler. It is not, by itself, a well-aimed one, and it is not, by itself, a competent
oracle — the three compressions an expert carries in one head are exactly the tacit half of the pair that does
not survive in a memoryless executor. What an experienced engineer used to supply silently, for free, bundled
into personal judgment, now has to be supplied on purpose, written down, by someone. This is not a smaller job
than the one experienced engineers already did. It is the same job, made explicit.

## Part VI — What AI actually changed

AI changes three of the parameters above. It changes none of the structure.

**The cost of generating collapses.** Sampling, which used to happen once per system across a career, becomes
a routine operation, cheap enough to run on every change. This is also the first time the distribution becomes
observable: the non-determinism was always there — two engineers, two Mondays — the model just made it fast
and frequent enough to see.

**The generator loses its memory** — or, more precisely, whatever memory it has is not versioned, which turns out
to be the load-bearing half of the claim. A hosted model retains nothing between invocations by default, and even
where retrieval, long context, or persistent agent memory patch some of that back, what gets retrieved is usually
the same lossy record already argued not to carry the tacit half — old text, not old understanding. Real or
simulated, that memory is not hashed, not reviewed, and not portable across a model swap the way a spec is. Naur's
theory, which used to survive precariously in the heads of a stable team, now has no home built for it by default,
memory features or not.

**The sampling rate explodes**, which puts the oracle under a kind of pressure it never faced from slow,
well-intentioned human executors: a cheap generator iterating against a test suite is something closer to an
adversarial search for the suite's blind spots than to an engineer trying in good faith to make it pass.

Nothing about the space of programs, the existence of intent, or the need for an oracle changed. What changed is
that the thing that used to be free — the generator's memory — now has a price, and the thing that used to be
nearly free — a sample — now is.

### What an adversarial generator is

Call a generator adversarial not because it intends harm, but because of what cheap, repeated sampling against
a fixed, partial oracle does structurally, regardless of intent. When generating is expensive, a generator that
fails the oracle simply fails, and the cost of trying again discourages searching for the edge cases the oracle
cannot see. When generating is nearly free, failing the oracle costs almost nothing, and an oracle with any blind
spot at all becomes more likely to be found the more attempts get thrown at it — not because the generator is
malicious, but because search converges on whatever the fitness function rewards, and the fitness function here is
"passed the oracle," not "satisfies the intent the oracle was built to protect." How much this matters in practice
depends on how many attempts actually get thrown, and at the retry counts typical of most generation loops today
— a handful, not an exhaustive search — this is a real, directional pressure rather than the fully realized
adversarial search the phenomenon describes at its strongest. The mechanism does not require malice or scale to
exist. It requires scale to dominate, and that is a claim about a trend, not yet where things already stand.

This is the same phenomenon documented under different names in adjacent fields: reward hacking in reinforcement
learning, specification gaming, Goodhart's law read as a law of engineering rather than of economics. A model asked
to make a failing test pass may find it cheaper to special-case the exact input the test checks than to fix the
underlying logic; asked to raise coverage, it may add assertions that exercise a line without constraining its
behavior. Both satisfy the oracle. Neither moves the artifact closer to the region the spec actually defines.

The consequence for oracle design is direct: an oracle that shares its blind spots with the generator that has to
satisfy it will be found and exploited faster than an oracle that does not. Requiring that a test spec be generated
from a component's public contract, not its implementation, ensures the two readings of the same intent do not
inherit the same gap. Independence does not close the blind spot — nothing does — but it makes the blind spot
of the generator and the blind spot of the oracle two different sets, which is the only defense a partial oracle
has against a generator cheap enough to search for their intersection.

### The corollaries

Read against this shift, the whole apparatus stops being ideas about AI coding tools and becomes the predictable
response to a changed cost structure.

The tacit half of the pair must be **externalized**, because the generator no longer carries it: the spec defines
the region, the artifact records the resolution, and the pair — not either half alone — is the unit that must
be versioned.

The boundary between what is **ratified** and what merely **survived by luck** becomes the variable that decides
whether a regeneration is safe: everything written into the spec and pinned by a test is preserved across a re-roll;
everything that worked by accident is re-decided, and may not land the same way twice.

Exploration is cheapest at **birth**, before an artifact has accumulated survivor evidence worth losing, and gets
more expensive with every day it runs — resample early, hold steady later. Drift complicates this: intent that keeps
moving can force the same choice again, later in an artifact's life, no matter how much survivor evidence has piled up.

And regeneration should be **minimal** by default — a local move against the existing point, not a fresh draw
from the space — exactly because a fresh draw destroys accumulated evidence the spec never captured.

Two limits are worth stating plainly here.

What actually gets externalized by a clause or a pinned test is narrower than the theory Naur described: durable
protection against one already-discovered failure mode, not the understanding that would help with the next one
nobody has found yet. A spec clause says what must hold, not why. The fix is to put the why where the generator
actually reads it — inside the same description a generation is produced from, not a separate document nothing
consults — so a rationale that goes stale eventually produces a generation that contradicts it, the same discovery
loop this essay has relied on throughout, now pointed at reasons instead of only requirements.

And every probability-weighted argument in this essay — a rule's power, a crossover point, which silences are
safe to leave alone — was computed against "the generator's own distribution," which is itself unversioned and
can change on a provider's schedule without a line of any description moving. When it changes, those valuations
silently reprice. The partial reassurance: models trained on overlapping corpora likely share priors about common
failure modes even where they diverge on rare ones, so a rule guarding against a common failure probably keeps
its value across a swap, while one guarding against something rare to a specific model's training does not —
a testable claim, not a settled one. Every crossover and diminishing-returns curve here should be read as valid
for a stated generator, not guaranteed to survive its retirement.

### The type system as a pre-paid oracle

A compiler is an oracle in the strict sense already given: a mechanical, binary procedure that decides membership
against one real, if narrow, slice of intent — shape, not behavior. When the language itself was a deliberate
choice, as it usually is, failing to compile is a genuine intent violation, not a technicality outside intent's
reach: whoever chose the language meant the software to be expressible in it. That holds for the choice itself,
not for every incidental rule a toolchain happens to enforce underneath it — a linter's arbitrary style
preference does not become intent merely because failing it produces an error. The line is deliberateness, not
mere enforceability. What compiling does not do is extend that credit to the rest of intent. Type-correctness is
necessary for almost every purpose and sufficient for almost none.

But the choice of language does something an ordinary description does not: it does not narrow a region inside
a fixed space of programs, it selects which space is being sampled from in the first place — a decision made
once, early, closer to a zeroth-order description than an ordinary spec clause. And it is worth naming how that
oracle got paid for. An ordinary test or spec clause is authored retail, at this project's cost. A type system is
authored wholesale, once, by a language's designers, and every program in that language inherits its coverage for
free at the moment of compilation. Choosing a strongly-typed language is buying oracle coverage in bulk, prepaid
by a community, instead of building it retail.

None of this is free on the other side. A stricter type system also narrows the space it draws from, and a correct,
intent-satisfying program the type checker cannot prove safe is rejected all the same — the type system's own
version of the false positive every partial oracle can produce. This tracks the birth-versus-maturity policy
already given: a system whose intent is still being discovered benefits from a larger, more permissive space; a
system with a long life ahead of it, high fan-in, or high stakes collects more value from prepaid coverage than it
loses to the narrower space. The choice of language is the earliest and longest-lived instance of the exact trade
this essay has been describing throughout — how much oracle coverage to buy, and when — made before there is
even a project to make it for.

The boundary this section draws between shape and behavior is not fixed by nature, and language design has spent
decades moving it. Rust's borrow checker is a closed oracle in exactly the sense already given, authored once with
no independent second account to disagree with it, and it is total authority over a dimension considerably richer
than parsing and type arity: a program that borrows without violating the checker's rules is memory-safe and free of
data races in the code the checker covers, not merely well-shaped. The same move scales further in dependently-typed
languages, where a passing type check can certify an arbitrary proposition the type was built to state. The frontier
between what a closed oracle can settle for free and what only an open oracle, tested at runtime, can settle is a
design choice a type system's authors make, and every property moved across it stops needing a test. What a closed
oracle can never do, no matter how rich the type system, is confirm a property nobody thought to encode.

### Feeding the verdict back

The closed-versus-open distinction from Part I decides the safety of a loop every generation workflow contains: a
candidate fails a check, and the failure message goes back to the generator for another attempt. Whether that loop
is safe scales with the gradient rather than switching on a category — the further toward the closed end the
oracle sits, the safer it is to hand its verdict back and retry, so long as the verdict stays a signal to try
again and never becomes something more dangerous: a constraint the description never stated, silently governing
what gets generated. Near the closed end, a verdict can be treated as nonconformance with negligible risk of a
second reading — retrying with the verdict in hand is re-sampling with a cheap directional signal, not consulting
an unauthorized description. Nearer the open end, the same verdict needs the
nonconformance-versus-underspecification fork resolved before any retry is safe, because a meaningfully live share
of what it might mean is a disagreement between the test and the spec that only a human can resolve without
quietly erasing it. This also names a fifth operation the primitives did not yet have a place for: a **bounded,
ephemeral resample** — scoped to a single generation event, guided by a closed oracle's own verdict, leaving no
trace in anything versioned once the event ends.

### The oracle is generated too

A test is not a primitive — it is itself an artifact, generated from a description (a test spec) by a generator
(a person or an agent), and it inherits every uncertainty already named: the test-generation step can miss what
the test spec actually said, or resolve a silence in a way nobody intended. Ratifying a fix "into a test" is not
the bedrock it is allowed to sound like. It is durable relative to the artifact it protects, but generated, aimed,
and fallible relative to whatever generated it — trustworthy to the extent it was authored independently, exercised
by mutation testing, and matured by the same production-oracle mechanism as anything else. The chain of trust
bottoms out at a sufficiently ratified oracle or a sufficiently careful human, and nowhere firmer than that.

### What a model's variability costs

The conflation named in Part I is easy to grant in the abstract and easy to re-commit the moment a model is the
generator in question — the fluency invites the sense that it has joined the description as an unwritten second
half. It has not; nothing about a generator resolving silence changes because the generator got better at it.
What is genuinely new is not where the model sits but what its variability costs. A human generator's prior changes
slowly, across a career, mostly in the direction of improvement. A model's prior does not change across a career
at all — it changes the moment a provider retrains or swaps a version, for reasons outside any project's control,
and the artifact that comes out can move without a single line of the description changing. A hash of the description
records the state of the description faithfully; it cannot record the state of a generator that is not part of what
it hashes. This is not evidence the generator belongs inside the versioned half. It is evidence that an unversioned,
unaccountable input sits upstream of every artifact — and the discipline of diverting what must not vary to
a deterministic oracle or a hard type, leaving to the generator only what is safe to leave stochastic, exists
because of exactly that boundary.

### What actually licenses a short spec

There is a premise sitting underneath every defense of a short spec, classical and current alike: leaving something
unwritten is safe because a competent executor resolves it correctly. Read against the primitives, that premise
smuggles in a claim it has no right to. Competence, however real, is a property of the generator, and a generator
only ever aims. "Correctly" is a confirm-shaped word, and nothing about a generator's prior can supply it.

What a competent executor actually contributes is a higher hit rate — a prior concentrated on sensible defaults
rather than spread uniformly over the space, so that most silences resolve into something workable on the first
sample. But a higher hit rate is not certainty, and nothing about it tells you, for any specific resolved silence,
whether this one landed on the right side. That answer becomes available only through the mechanism described
throughout this essay: the incident that surfaces a bad resolution, the fix that gets ratified into a test or
a narrower clause, and the slow accumulation of maturity. A short spec is not safe because competence resolves
its silences correctly. It is safe because competence keeps the volume of wrong resolutions low enough that the
discovery-and-ratification loop can afford to catch the rest — a claim about a rate and a cost, not a claim
about correctness at the moment of generation.

This was never a claim that the executor gets it right, for any specification in history. It was always a claim
about a hit rate good enough that the remaining misses are economically absorbable by whatever discovery loop
happens to be in place — code review, a QA cycle, a customer complaint, a regulator's audit. Slow discovery loops
made the claim viable historically because misses were rare and generation itself was rare. A model as executor
raises the sampling rate the loop has to keep up with. The reason a short spec is safe was never the executor's
competence alone. It was the loop behind it, and the loop has to run faster now because the executor got faster,
not because it got any more entitled to the word "correctly."

The concentration named early on — a generator's prior, even before any description narrows it, already sitting
on a vanishingly small, highly conventional corner of the space — is the precondition for this whole argument,
not a detail. A short spec is only a good bet because the silences it leaves do not resolve anywhere near
uniformly; they resolve toward whatever training already made common, which is a reasonable proxy for what a
competent human would also have reached for by default. This is a split worth cashing out in the generator's own
prior directly: the routine part — idiomatic structure, conventional error handling, the boilerplate any
experienced engineer would produce without thinking — is exactly what a concentrated prior supplies for free,
leaving the spec's real job to be the domain-specific part, the content no training corpus could have contained
because it belongs to this project alone.

The same concentration cuts the other way wherever intent is genuinely unconventional. A prior that reliably
resolves silence toward the common case resists, by the same mechanism, resolving it toward an uncommon one — a
business rule that deliberately breaks the usual pattern needs to be stated explicitly and forcefully, not left
silent, because silence invites exactly the default the prior was built to prefer. The spec's highest-value
sentences are, by this logic, the ones fighting the prior rather than riding it — precisely where a short spec's
usual bet, that silence resolves itself reasonably, stops paying off for free.

### When the generators disappear

Two ways the local-gradient cost can rise have been named: technical debt, the code's own structure degrading,
and drift, intent moving out from under a fixed description. There is a third, and it has
nothing to do with the code or the intent: the population of generators competent to work on it can collapse out
from under both.

Expertise is located in the generator, not the artifact — that competence is not guaranteed to persist at the level
of an industry. A language can have working, well-specified, well-tested code and, decades later, almost nobody
left who can read it fluently enough to make even a small local move with confidence. The code has not moved. The
intent has not moved. The supply of generators able to stand on that point and fight has gone to zero. "We cannot
find people who can maintain this system" is a report that one whole term in the crossover has gone to infinity,
and migrating can be entirely rational even when nothing about behavior needed to change at all.

It is worth naming, tentatively, what a cheap and competent generator does to this specific trigger, because it may
be the one case AI genuinely changes rather than merely accelerates. A model's competence is a property of training,
not of a living community — it does not retire, does not need replacing, and does not care how few humans still
read the language fluently. If a model can generate and verify code in a scarce language as competently as it
can in a common one, the generator-extinction trigger for migration weakens in a way none of this essay's other
arguments do, because the scarcity it responds to was always about people. This is speculative: it depends on a
model's competence in a given language actually holding up, which nothing here has tested.

## Part VII — Applying it

### What this says about generating tests

Apply the whole apparatus to one concrete practice: how tests should get written when a machine writes them.

**Generate from the contract, never from the implementation.** A test that reads the code it is meant to check
inherits that code's blind spots along with its intent, and a cheap, repeated generator will eventually find
whatever gap the two share. The rule is the only defense a partial oracle has against a generator built, in effect,
to search for its weaknesses.

**Prioritize by expected value, not by coverage.** A test's worth is the probability mass of the failure it guards
against, multiplied by the cost of that failure going undetected. Coverage percentage counts lines exercised,
not stakes protected, and a generator asked to raise it can satisfy the number by exercising a line without
constraining its behavior.

**Aim tests at the load-bearing dimensions on purpose.** Boundary values, error paths, concurrent access, resource
exhaustion — the traditional targets of boundary-value analysis and equivalence partitioning — are exactly the
small set of dimensions that carry outsized sensitivity, named by a different vocabulary decades before this essay
existed. Spreading effort uniformly across the rest of the surface is not thoroughness. It is diminishing returns,
paid at full price.

**Write the test at the level of the contract the fix defends, not the shape of the code that happened to fail.**
A test written against an accident freezes it as a requirement, vetoing a better resolution some future generation
would otherwise be free to reach.

**Weight investment by reuse, not by size.** A shared component's defect set is discovered once and closed for
everyone who calls it; a used-once component's fixes only ever pay off locally.

**Match rigor to what a miss costs, not to a house style.** A single test-writing standard applied uniformly
across a codebase either wastes effort on the cheap components or under-protects the expensive ones, and usually
does both at once.

**Spend the cheap window at birth.** Broad, exploratory testing earns its cost early in a component's life; a
mature component gets more from a few precise regression tests than from continuing to search a region that has
already given up its low-hanging defects.

**Measure the oracle by whether it resists being gamed, not by how much of the code it touches.** Mutation testing —
deliberately injecting small defects and checking whether the suite notices — makes this operational: it asks the
suite the same question a cheap, adversarial generator will eventually ask it, before the generator gets the chance.

### Test, spec, or both

The apparatus gives a decision procedure for this, not just a set of concepts.

Start with the taxonomy already given. If the failure is nonconformance — the description already spoke, and
the sample still landed outside it — the spec was never the problem, and the fix is a test, or simply another
sample against the same description. If the same nonconformance keeps recurring despite the spec already speaking
plainly, that repetition is evidence the current wording is not registering — an aim problem to solve by rewording,
not a reason to add a new rule.

If the failure is underspecification, the default is both, because a spec clause is probabilistic and durable while
a test is mechanical and local. The clause conditions every future sample, including one from a full resample, on
every variant of the mistake its wording actually covers; but prose present in full has still gone
unfollowed, so a clause alone carries no guarantee. The test guarantees the specific case will be caught if it recurs,
but its guarantee stops exactly at the case it names, and its value depends on staying in the same battlefield unless
it is written at the level of the contract rather than the accident. Together they cover for each other's weak side.

Where budget forces a choice between only one, weigh stakes and durability. A component with high fan-in, high
failure cost, or a long remaining life earns the spec clause even at the cost of writing it carefully. A component
about to be resampled anyway, used once, or cheap to fail earns the test and nothing more. And a dimension that
is behaviorally free but cost-relevant — naming — earns neither a test, which would have nothing to check,
nor a heavy clause, only a light convention note.

One clarification is easy to conflate with the rest: consulting the description at every fix is necessary, but
it is an act of aim, not confirmation. Reading the spec before making a change shifts the odds that the change
respects it; it does not guarantee the change stays inside the region. The only operation that actually confirms a
fix has not carried the point outside the region is running the oracle against the result, and even that confirms
only on the dimensions the oracle happens to cover. Consulting the spec on every fix is the right habit — but
it buys better odds, not a guarantee. The guarantee, bounded as it always is, comes from the test that runs after.

### What this says about code review

Apply the same apparatus to the practice sitting next to testing in every engineering culture, because the two
are usually taught together and turn out to answer different questions.

**Review is a human oracle, and it covers exactly what mechanical oracles cannot.** A type system confirms shape;
a test confirms behavior against whatever it was written to check; neither can say anything about a dimension
that carries no behavioral difference at all. Naming, structure, the shape of an abstraction — cost-relevant,
behaviorally free dimensions — are invisible to every oracle that only watches what a program does. A human
reading the code is the only oracle layer that can confirm these, which is also why no amount of test investment
ever makes review fully redundant.

**Independence is what makes it work, and its absence is what makes it decorative.** A reviewer who is the author,
or a model reviewing its own prior output in the same context, is not a second reading — it is the same generator
checking its own satisfaction, prone to the same blind spots for the same reason. A reviewer who did not write
the code, given the contract and the diff and nothing else, is a genuinely separate sample of judgment, and that
separation, not seniority or diligence, is what the practice's value depends on.

**A review comment is a discovery, and it needs the same choice given above.** A reviewer catching a bug before
merge is shift-left at its cheapest layer. But a comment fixed only in that one diff and never generalized is
exactly the ephemeral, unratified knowledge already located in a person's head: real, and gone the moment that
reviewer is not in the room for the next similar diff. A recurring comment is the same signal a rule bought at
diminishing returns already gave: the aim is in debt, and the fix is not another reviewer saying it again.

**Review runs on probability mass too, and diminishing returns follows the same arithmetic.** A reviewer's catch
rate on any class of defect is a probability, high for common failure modes and falling into a long tail for
rare ones. A second pass buys less than the first for the identical reason a second spec clause buys less than
the first. This gives reviewer selection a lever spec-writing never had: since the union bound's cost comes from
overlap, a second reviewer with genuinely different background catches more, on net, than a second reviewer who
thinks about the code the same way the first one does.

**A reviewer's own attention allocation is the crossover, run by one person in real time.** Spending three careful
passes on the code that touches money and one skim on a rename is the crossover executed at the grain of individual
lines instead of individual components.

**Judging whether the code will be legible to a reader who is not here yet is close to uniquely review's job today.**
The judgment is a prediction about whether a future, different mind will be able to reconstruct reasons that are
not written down anywhere, discussed further below — a theory-of-mind operation, not a behavioral check.

**The bottleneck it inherits is reading's, not writing's**, discussed at length below. A model producing ten times
the diffs does not make review ten times easier; it asks the one operation that never got cheaper to absorb ten
times the volume.

**Rigor should track stakes and reach, not apply uniformly.** A two-reviewer policy applied identically to a one-line
copy change and a payment-authorization path wastes effort on the cheap component and leaves the expensive one thin.

**Whether review is necessary is the wrong question, because review is not one thing.** It bundles confirming
the free-but-cost-relevant dimensions no mechanical oracle sees, catching underspecification before the next,
more expensive oracle layer has to, and supplying independence against a generator's blind spots. Each has a
different crossover.

**And a fourth function is not technical at all, and predicts its own decay.** An approval attached to a name
is a record that due diligence happened — an accountability signal, not a confirm operation. This function
survives perfectly intact even after the other three have collapsed to ritual, because it is cheap to produce and
slow to audit. Evaluate reviewers on throughput — reviews closed per day, the fast, visible proxy — and the
same problem Goodhart's law names elsewhere in this essay shows up again, one level up the stack: the reviewer who
reads carefully and catches less-visible defects looks, on the only signal available, slower than one who approves
quickly and rarely blocks anything, and the incentive tilts toward exactly the rubber-stamping that keeps the
paperwork intact while the technical function it was named for quietly stops happening.

### Where a human reviewer and a model reviewer differ

Naming review as a bundle of functions makes the human-versus-model question answerable function by function.

**Accountability is not a capability gap; it is a structural one.** An approval standing for due diligence depends
on attaching legal and organizational responsibility to a party who can bear it. No amount of model competence
changes this, because current organizations and law do not recognize a model as a party that can hold liability.

**Independence is a real question for a model reviewing its own output, and a subtler one for a model reviewing
anything.** Two different humans typically carry different training, genuinely decorrelated blind spots. The same
model reviewing its own prior generation, even in a fresh session, is not a second sample in the same sense — it
is the same generator checking its own satisfaction. A genuinely different model, trained differently, is closer
to a second, decorrelated reader: vary the model, not only the prompt, if the review is meant to catch what the
generator could not see in itself.

**Cost changes which crossover is even worth running.** A model review is cheap enough to run on every change,
which shifts effort toward applying it everywhere mechanical judgment reaches. This does not remove the reading
bottleneck — it only moves it to whatever residue a model cannot yet judge well.

**Theory of mind is a capability question, not a structural one, and it may already be closing.** Predicting whether
a future reader will reconstruct an artifact's reasoning is a simulation a competent model may perform adequately
now or soon — nothing about the function is reserved for a biological reader by definition.

**Locally earned competence is the harder gap to close.** A long-tenured reviewer's value is partly generic skill and
partly a tacit, project-specific history — this team's past incidents, this codebase's specific fragile corners. A
model has this only to the extent it was actually given it, which is exactly the argument for externalizing the
tacit half into a versioned description: the gap between a tenured human reviewer and a model reviewer is a measure
of how much of that project's history has been written down anywhere the model can read it, not a permanent ceiling.

**Consistency cuts in both directions.** A model given the same diff twice is close to the same reviewer twice —
no fatigue, no throughput pressure degrading care, but also no lucky day where an unrelated association catches
something a systematic pass would miss, and whatever blind spot the model carries, it carries on every review.

### The questions this reframes

A few questions recur in every conversation about specs versus code. The lens does not settle them with a number,
but it replaces each with a version that has an answer.

*Does the spec have value?* The spec is the only description that distinguishes a region from a point, a decision from
an accident. Its value is the value of the option it preserves — to regenerate, to hand the system to a new team,
to bring in an executor with no memory of what came before — and that option was cheap to give up only while
the generator remembered and the big move was rare. Both premises are gone.

*Is code alone sufficient?* Code alone was never sufficient. It looked sufficient because it was never alone —
it was paired with a tacit half held by whoever wrote it. The live question is where the other half of the pair
lives now, and whether it survives the departure of whoever is holding it.

*When should you regenerate instead of patching?* When the evidence a resample would destroy is worth less than the
cost of staying in the current neighborhood, discounted by how much of that evidence the oracle can hand back for free —
or, separately, when intent has drifted far enough that walking a local diff toward it, anchoring risk included,
costs more than re-specifying and drawing fresh.

*What is codebase maturity?* A posterior narrowed by selection — a defect set that contact with reality has
shrunk, held in a point that has stopped moving. A statistical property of a single sample, not a property of the
code's age or its aesthetics.
