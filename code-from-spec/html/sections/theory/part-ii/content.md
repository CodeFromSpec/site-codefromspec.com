---
title: "Part II — The apparatus at its limits"
description: "What happens when each primitive is pushed to its limit, and the corner where current practice already sits."
date: 2026-07-18
---

![Part II — The apparatus at its limits](/images/theory/20260718_image_part_II.png)

# Part II — The apparatus at its limits

Part II tests the apparatus by pushing each primitive to its limits.

### The limits of intent

Suppose intent had everything it never has: full knowledge, stillness, finite encodability, consistency
among stakeholders. Then the description can be finished. Corrections now land on a target that holds
still, so ratification compounds and nothing expires. This is where formal methods belong, and the regime
is common: some of it specialized (an avionics control law, a cryptographic primitive, a compiler answering
to a frozen standard), most of it ordinary — a calculator, a JSON parser, a bignum library, their intent
bounded and done moving long ago.

Now the opposite limit: no intent at all, only a stakeholder who will know it when they see it. The apparatus
does not break here; it runs backward. A draw is no longer an attempt to satisfy intent but a probe to discover
it. The oracle that matters is the stakeholder's reaction, which does not check a region so much as sketch one.
And aiming gives way to coverage: the goal is candidates worth reacting to. This is generation used as
elicitation.

Prototyping always lived here, and so do the ephemeral workflows: prompt, react, keep nothing. A throwaway
probe needs no durable description, because the description is what the probe exists to produce. The economics
runs backward too: survivor evidence is worth nothing where nothing is meant to survive, and a fix lands not
in this artifact's spec but in the next one's intent. The one expensive mistake in this regime is failing to
notice which regime you are in — shipping the probe.

Settled intent is a property of problems, not a discipline a team adopts. The bounded problems hold it whole;
every other project holds both regimes at once: a settled layer (machinery whose intent stopped moving years
ago, answering to no stakeholder in particular) and a moving core, the business, still being discovered.
Settling happens piece by piece. The axis is old enough that methodologies have fought over it. Waterfall bet
that a project's intent sits at the settled limit, and lost on most business software. Agile was the
counter-bet: intent is not absent so much as undiscovered, held partially, moving, learned by contact or not
at all. Both bets treated a project as one single regime, and neither had a vocabulary for the line between
the layers. What this limit leaves a project with is a question asked of each piece: which side of the line
does it sit on, and which economics follows.

### The limits of the description

Take the absent description limit first, a draw conditioned on nothing but the prior and the point
it stands on. What comes out is not noise but something more dangerous: perfectly plausible code, aimed at
what code in general has been and at whatever this artifact already is. With no region of the project's own
defined, hardly any draw can be nonconforming — the language's region still stands, the build can still
fail, but past that gate every defect is underspecification, total silence resolved by convention. And the
artifact quietly takes the description's place. Its accidents pass for intent, and harden into house style —
anchoring with no counter-force, because there is no description to provide one. This is the everyday
practice of the agent that infers a project from its repository.

The operations separate here too. A local move keeps something to stand on, and the danger just described is its price.
A resample keeps nothing: the fresh draw has only the prior left, and what comes back is a generic program, not
this project re-derived. Without a description, the artifact is the only carrier of the project's identity, so
discarding the point discards the project. This is why the workflows that keep no durable description
collapse onto the local-move end of the scale. They did not renounce resampling; it was never available
to them.

What can still act on a draw when not even a prompt is in the context? Whatever verdicts the loop feeds back,
because verdicts are descriptions in disguise. A compiler error aims the next attempt at the language's region,
a description of what programs may be, not of what this one is for. A failing test aims it precisely, because a
test is a description. A production incident aims it best of all, a verdict from
intent itself, converted into text by whoever files the report. And notice the conversion in every case: a
verdict about behavior re-enters the loop as text, the only substance a draw can be conditioned on. Strip
every description away and the loop rebuilds one out of verdicts; aim seeps back in through the oracles whether
anyone writes it or not. Test-driven development is that seepage adopted on purpose: write the verdicts first,
and the description is authored directly as oracles.

Now grant the opposite. A description whose region coincides, at the moment of writing, with the region intent
would accept. Two of the three kinds of bug die at once: mis-specification by construction, and
underspecification, because nothing intent cares about was left unsaid.

What survives the gift is nonconformance, untouched, because aiming is statistical. So the need for the
oracle survives a perfect spec: perfection fixed the description's relation to intent but did nothing to the
draw's relation to the description.

Two things keep the gift smaller than it looks, and a third puts a date on it. First, it is still a region,
not a point; generation still chooses. Second, perfection is unverifiable from inside: no oracle built from
the description can compare it against intent. The practical consequence: "our spec is now complete" is a state no
team can detect from within, so no spec quality ever licenses retiring the intent-facing oracles, and the
stopping rule for writing clauses has to be economic, never a declaration of arrival. Third, intent moves,
the text does not, and the property expires with nothing in the file changing. That is the ceiling on what
better specification alone can buy.

### The limits of the generator

The generator has two limits. Take away the prior first: a generator with no training concentrating its output,
spread as thin as the space allows. Almost every draw is noise (not wrong programs but non-programs, texts no
language's region contains) and no description can rescue it: everything left unstated resolves at random, and
no finite description states more than a sliver of what a program has to get right. And aiming has nothing to
work with. A description steers only by recruiting structure that training built (training is what
connected text like the description to code like what it asks for), and a generator with no training has no
structure to recruit. This is why a short spec ever worked: it was never good descriptions that made specs short;
it was the prior. Expertise, training, convention: their value as generators, in this vocabulary, is the distance
between the distribution they produce and that thin spread.

The opposite limit (freeze the prior on a single point) dooms aiming from the other side: the generator
emits its one habit no matter what the description says. At the prior-less limit the description's job was
impossible; at the frozen limit it is pointless. Every generator anyone has shipped sits between the two.

### The limits of the oracle

Now the oracle. Its two facings give it two perfect limits, not one. Grant each kind its perfection in turn.

The perfect description-facing oracle: complete coverage of the described region, verdicts trustworthy,
instant, free. The limit is actually reachable where the verdict is automatic (a model checker, a type system, an
enumerable domain); there, nonconformance dies at the draw, and feeding verdicts back to the generator
becomes fully safe. What no form of the grant can touch is everything above the description.
Mis-specification and underspecification pass through certified. Proving the code right against the wrong
spec is this limit's famous failure mode.

The perfect intent-facing oracle is the stronger grant (no blind spot, any sample decided against true
intent, instantly and for free) but not a clean one. Deciding against true intent requires intent settled and
whole, so this grant carries the intent gift inside it. Every kind of bug dies at the draw, including failures
on dimensions nobody has discovered yet. Every check the discipline maintains (types, tests, review, staged
rollouts, the slow feedback of production) is a partial refund on a verdict this oracle hands out whole;
ratifying into the oracle becomes a no-op.

Two lessons. The description survives, but repriced: as a source of confirmation it is now worthless (every
check that could be derived from it, this oracle already covers), yet drop it and generation becomes
generate-and-filter, at a cost set entirely by the generator's hit rate. A perfect oracle
turns the description from a carrier of correctness into a way of raising the hit rate. And maturity does not
survive this limit. Survivor evidence was never anything more than accumulated verdicts, worth holding only
where they are scarce, partial, and paid for — the intent-facing kind, since a mechanical check can be
re-bought on any build. Check every dimension at birth, and the
ten-year-old artifact knows nothing the ten-minute-old one does not. The lesson sounds circular — the grant
concedes settled intent, which is exactly what maturity spends years discovering — and the circularity is the
point: maturity was never a mystery, only an accumulation of verdicts nobody could buy up front.

Everything software trusts (the tests, the reviewer, the artifact's record, the caution around a fresh rewrite)
is an institution built on verdict scarcity. The scarcity is old; the institutions are the proof. And the
scarcity works the way a price works: when a verdict costs something, the candidate has to be worth judging,
which pushes effort into the description, into the tests, into every check that can filter before the expensive
verdict is spent. Verdicts manufactured cheaply to relieve the bottleneck do not remove the cost; they move it
onto whoever depends on what passed, paid later, as defects nobody caught.

### The costs at their limits

The operations run on two prices and on anchoring: the cost of generating a candidate, the cost of buying a
verdict on it, and how strongly the existing point captures the next draw.

Run anchoring to zero: the artifact provides context without capturing the draw, and every local move
lands where the description aims. The case for resampling nearly vanishes: why redraw, lose evidence, and
re-buy every verdict from scratch, when a local move is free? At full anchoring the draw is captured entirely: the
description asks for a change and the generator delivers the existing point back, or something so close the
difference is not the change that was asked for. No re-arming of the counter-force wins here (that is what
the limit means), so the artifact can only be abandoned, the resample is all that is left, and that is what
makes keeping the description current insurance rather than paperwork.

Of generation's two cost limits, only one needs to be imagined. The infinite price is not a hypothesis but the old
regime: local moves were always affordable; what cost a career was the fresh full draw, so most systems got
exactly one. Generation at zero does not zero the bill: free draws buy unlimited candidates, every candidate
still has to be checked, and the whole bill becomes a checking bill. A draw is only as cheap as the confirming
it obligates.

Unlimited free attempts against a fixed partial oracle find its blind spots with certainty. The oracle's quality
and cost, not the volume of generation, set what this limit delivers. And where the verdict comes from a person
(the stakeholder reacting to a candidate, the reviewer reading one), free draws saturate the judge instead. What
can be judged becomes the bound, and the budget worth planning is the judge's attention, not the price of the draws.

When the oracle's cost runs to infinity, a team can aim but not verify. Deploying becomes an economic decision about
the cost of failure and whether that cost is acceptable. There is every incentive to write the best description
possible, and no way to know whether it is one, because knowing is a verdict too. The only verdicts left are the
ones nobody buys on purpose: production's, priced as incidents rather than checks. So every lesson arrives at the
highest price there is, and only if the artifact survives it. Preserving the artifact's maturity becomes paramount:
real effort goes into keeping anchoring low, because only local moves are desirable; a resample would throw the
lessons away.

### The corner practice already occupies

The sweep pushed one primitive at a time, but the limit a reader is most likely living in is a joint one: no
durable description, generation nearly free, and checking that is cheap but partial. That is the agent
inferring the project from its repository, running in a loop against the build and the tests.

Each axis was priced above; together they compound. With no description, every defect past the build is
underspecification: silence resolved by convention. With draws nearly free, candidates arrive faster than
anyone meant to check them. And with verdicts cheap but partial, the loop retries until the checks pass.
Cheaper checking without better checking accelerates the search for exactly what the checks miss.

The three together make a machine for shipping plausible code whose unchecked dimensions nobody chose and
nobody watched.

One inversion sits under the whole corner. For as long as software has been written, generation was the
brake: a full draw cost a career, so candidates were scarce by construction, and the verdict — never cheap
itself — was never the binding constraint, because expensive generation rationed the demand for it and forced
what effort there was into aiming before drawing. Cheap generation lifts the brake and rebuilds nothing
downstream. Verdict scarcity, as old as the field, is now the only constraint left standing, and every
institution software trusts — the review, the test suite, the caution around a rewrite — was raised while it was
not the one that bound. That is the turn the rest of this essay runs on: not that generation became cheap, but
that its becoming cheap moved the binding constraint onto the verdict, and left every discipline calibrated to
a price that no longer holds.
