# Component-based architecture is the shape of a spec tree

Component-based architecture is usually presented as one style
among several — a choice a team makes, with tradeoffs, against
alternatives. Read against Code from Spec it is something
stronger: the spec tree's primitives are component architecture's
primitives, close to one-for-one, and the methodology's economics
actively reward good decomposition and punish its absence. This
article makes that claim precise, and then walks the places where
it needs honesty rather than enthusiasm.

## The isomorphism

Take component architecture's load-bearing ideas one at a time
and hold them against the tree.

A component exposes an **interface** and hides an implementation.
A spec node exposes a `# Public` section and hides everything
else. The `# Public` is literally "what others may use" — the
rest of the node does not exist for anyone downstream.
Information hiding is not a convention the team maintains; it is
what is and is not in the chain.

A component declares its **dependencies**. A node declares
`depends_on`, and the declaration is simultaneously the wiring
diagram and the source of truth — the artifact does not generate
without it, unlike import statements scattered through code,
which describe wiring only to whoever goes looking.

Components **compose** into larger structures. Intermediate nodes
compose; leaves are atoms. One leaf, one artifact: the atomic
component.

And component architecture's central promise — that internals
can change freely while the contract holds — is enforced here
rather than requested. A generation agent sees only its chain:
its own spec plus its dependencies' public interfaces. It
physically cannot reach into another component's internals,
cannot imitate them, cannot ratify an accidental dependency on
a resolution nobody promised. Encapsulation, confinement, and
contract-only test generation turn out to be the same move,
applied to three different observers — callers, generation
agents, verification agents — all kept from depending on what
they should not see.

Building with Code from Spec is doing component-based
architecture whether or not anyone says the words. The tree
forces the encapsulation, the explicit dependencies, and the
composition; the only thing it leaves open is where the
boundaries go.

## Same cut, two consumers

Component architecture and Code from Spec answer the same
question — how to tame complexity through decomposition and
information hiding — for two different consumers. Component
architecture tames it for the human: reason about one component
at a time, hold one contract in your head. Code from Spec tames
it for the model's context: generate from one bounded chain at a
time.

The consequence is worth stating as a rule: **good component
boundaries are good context boundaries — the same cut.** A
well-decomposed system, low coupling and high cohesion, produces
small clean chains. A tangled system produces tangled chains.
Cohesion and coupling were always about what a mind has to hold
at once; the mind in question just stopped being exclusively
human.

## The economics are baked in

In traditional development, keeping interfaces lean takes
vigilance — nothing pushes back when an interface grows fat
except review culture. In Code from Spec the pushback is
structural. The interface is what propagates through chains, so
a fat interface means fat chains for every consumer, more tokens
per generation, and a wider staleness cascade on every change.
Tangled dependencies mean huge chains. The cheapest-to-generate
architecture and the well-componentized architecture are the
same architecture, and a team feels the difference in cost long
before it would have felt it in maintainability.

There is a deeper layer to this than token counts, and it comes
from the defect side. "Write a spec so complete that any sample
from it is safe" is not achievable in general — diminishing
returns make pre-empting the long tail of failure modes
expensive precisely where it matters. But the claim is
scale-dependent. A small, pure component with a narrow interface
has a short list of load-bearing dimensions: a spec that covers
them is affordable, an oracle over its behavior can be driven
close to exhaustive, and the residual probability of an unsafe
sample becomes negligible. Small components live in a regime
where regenerating on every change is safe in practice.

Decomposition, in Code from Spec, is therefore not hygiene. It
is the manufacturing of that regime — the deliberate cutting of
a system into pieces small enough that per-node regeneration is
trustworthy. That is why component boundaries matter more under
regeneration than they ever did under hand-maintenance: they are
not organizing the code, they are bounding the defect exposure
of every generation event.

## What the tree does not give you

Three honest limits, each the beginning of a practice rather
than a flaw.

**Seams are their own battlefield.** Splitting a system adds
dimensions at every seam: what the contract actually means, how
a failure crosses it, what either side may assume about the
other. If every component has its leaf but the integration has
no spec and no oracle, those dimensions have no owner — and a
system of individually perfect components can still fail
wholesale at the joints. Integration test specs deserve nodes of
their own.

**The chain enforces the information boundary, not the runtime
boundary.** Confinement guarantees that no agent sees another
component's internals. It does not guarantee that no artifact
shares mutable state, exhausts a common resource, or takes a
neighbor down when it crashes. Those are properties of the
running system, not of the tree, and they hold only if the specs
say so — constraints written once in a guard node and inherited
by every descendant. The tree gives context boundaries for free;
execution boundaries remain spec discipline.

**Granularity is a crossover, not a maximum.** Too fine, and
seam dimensions dominate the system — every boundary added is
contract, failure handling, and mutual assumptions that did not
exist before. Too coarse, and the component exits the
safe-regeneration regime while its chains bloat. Where to cut is
the same marginal-value-against-marginal-cost judgment that
governs how much spec to write, applied to boundaries — and like
that judgment, it has no formula, only a direction to look.

## Where the engineer's judgment lives

The model generates a component. It does not decide, with any
judgment worth trusting, that the component should exist, or
where it begins and ends. Boundary-drawing is exactly the
decomposition skill experienced engineers already carry, and the
methodology does not automate it — it elevates it. When drift
arrives, the boundaries decide the blast radius: one stakeholder
concern per subtree means one subtree re-specified while every
unrelated component's accumulated evidence stays intact. When a
subproblem is shared, the boundaries decide the maturity rate: a
component solved once and referenced everywhere meets production
at the rate of the whole system's use of it, while N independent
copies mature N times more slowly with N defect sets to
discover.

One clarification, to keep the claim from being read narrower
than it is: "component" here is not object orientation. Nothing
about it requires classes, inheritance, or a particular
paradigm. What maps is the decomposition instinct — an
encapsulated unit with a declared interface — which exists as
comfortably in a Go package as in anything with a class
hierarchy.

## Architectures that fight the grain

The fit is strong because the tree is a static component
decomposition, and it is honest to say what resists it. A
monolithic ball of mud has no clean `# Public` to declare — it
becomes one giant node, and the methodology's economics punish
it immediately. Heavily cross-cutting designs — concerns woven
through everything — are expressible through a dependency on a
shared concern subtree, but they are the awkward case, not the
natural one. And runtime-dynamic composition, where the wiring
is decided while the system runs, can be described by the static
tree but is not mirrored by it: the tree records what may
connect, not what is connected now.

None of these are refutations. They are the boundary of the
claim — the same kind of boundary every component is supposed
to have.
