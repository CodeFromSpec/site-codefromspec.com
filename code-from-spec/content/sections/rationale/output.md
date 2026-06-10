---
code-from-spec: ROOT/content/sections/rationale@awf1xoqfVAfySLK9q3vM1tm182E
title: Rationale
description: Why Code from Spec exists — how AI changes the economics of software, and why specifications are the key to making software a collaborative product.
---

![Rationale](/images/banner_rationale.png)

# Rationale

> The disruption is not in the coding — it is in who participates, how knowledge flows, and where accountability lives. AI is the enabler, not the goal.

## The promise and the reality

AI promised that anyone can create software. We are not there yet. Without software engineering knowledge, it is not possible to use AI to produce reliable software. That knowledge remains necessary and indispensable. But it should not be a barrier preventing other people in an organization from generating value through software.

Today, a non-engineer who asks AI to write code receives something that appears to work. But they have no way to evaluate whether it is correct — whether the error handling is adequate, whether the business logic covers the edge cases, whether the code is secure. They accept what they receive because they have no frame of reference to question it.

Inside organizations, the engineer remains the bottleneck — not because they write code slowly, but because they are the only channel through which domain knowledge becomes software. They absorb domain expertise that others already have. They end up making product decisions that should belong to domain experts. The "democratization of software" has not changed this dynamic. It has only made the translation faster, not better.

## Why specifications failed before

The software engineering community recognized this problem decades ago. Structured analysis, stepwise refinement, formal specifications — the 1970s and 1980s produced rigorous methods for capturing domain knowledge before writing code. The goal was exactly what Code from Spec describes: a structured artifact that expressed intent, could be reviewed by domain experts, and guided implementation.

The methods failed not because they were wrong but because they were expensive. Maintaining a specification in sync with evolving code required constant manual effort. The spec drifted. The team stopped trusting it. The cost of maintaining the spec exceeded the cost of fixing the bugs it would have prevented.

The industry responded with agility: shorter cycles, working software over documentation. This was rational. If specifications cannot be kept current, get feedback faster instead. Agile solved the bottleneck by removing the spec. The knowledge became invisible — encoded in code that only the programmer could read.

## The quality problem

Agile compensated with short cycles. When the programmer made a wrong domain decision, the delivery cycle was short enough that the user saw the result quickly and corrected it. This worked when the end user was the domain expert.

It worked less well for domains where the person who sees the demo is not the person who knows the rules. A product demo shows screens and flows — it does not show that the provisioning calculation uses the wrong cutoff date, or that the settlement logic violates a regulatory constraint. The compliance officer, the accountant, the legal analyst — they are not in the demo. Or if they are, they cannot tell from a demo whether the underlying logic is correct.

This creates a dynamic similar to the market for lemons: when the people who hold domain knowledge cannot evaluate the quality of the implementation, quality degrades. An engineering team that implements the domain rules precisely is indistinguishable — from the stakeholder's perspective — from one that implements them approximately. Both pass the demo. The difference surfaces months later, in a failed audit or a reconciliation that doesn't balance.

The developer who gets edge cases right delivers more slowly. The one who cuts corners delivers faster and receives praise. The quality that matters is invisible in the short term: it manifests only as the absence of problems, which no one notices.

## AI changes the economics

AI inverts the cost structure. Code generation is cheap. The scarce resource is no longer writing code — it is knowing what to write.

When code is generated from spec, synchronization is automatic by construction. The spec does not drift from the code because the code is derived from the spec. The argument that killed formal specification in the 1980s — that maintaining specs was too expensive — no longer applies. This is the removal of the constraint that made formal specifications impractical for forty years.

Code from Spec restores what agile conceded — without reintroducing the bottleneck. The spec tree provides the structured specification that formal methods promised, kept current by construction. The short cycles and feedback loops of agile remain — but now each iteration produces a spec change and a regeneration, not an ad-hoc code change that drifts from an outdated document.

## Software as commodity

Software is at the same inflection point that hardware reached decades ago. In the 1960s, building anything electronic meant designing your own circuits from scratch. It required specialized engineers, custom fabrication, and years of development. Today, you buy a USB controller off the shelf, pick a microcontroller from a catalog, snap a gyroscope onto a board. The components that once demanded teams of engineers to produce are readily available. The specialized expertise didn't disappear. It moved upstream, into the companies that design the components everyone else uses.

AI is commoditizing code generation. But just as building hardware from commodity parts still requires knowing which components to use, how they integrate, and what constraints they must respect, building software still requires knowing what to build and how the pieces fit together. The question is no longer who writes the code. It is how the organization expresses what it needs in a form that reliably becomes software.

This changes the economics of software organizations. Today, large engineering teams exist because someone has to translate every business decision, every compliance requirement, every operational constraint into code. That translation is the bottleneck, and it scales linearly: more software means more engineers.

Code from Spec breaks this proportionality. The engineering team becomes a small, specialized group — like legal or compliance — that defines the structural guardrails and supports the rest of the organization in delivering software directly. The engineer is not hired to write code. The engineer is hired because they solve complex problems. Writing code was the mechanism available to exercise that role — it was never the role itself. Freed from translation, the engineer does more engineering — not less.

Software budgets shift from production to precision. Hiring changes: the organization needs fewer engineers, but those engineers need deeper judgment. Onboarding changes: a new team member reads the spec tree and understands the system, instead of spending weeks reading code. The spec tree is portable — if a better generation tool appears tomorrow, the specs still work. The investment is in knowledge, not in implementation.

## Why this approach

Code from Spec attacks the problem in multiple dimensions simultaneously.

**Specs in natural language.** Specifications are written in structured natural language. This allows anyone with domain knowledge to contribute directly, at the level of abstraction they understand, without learning to program.

**Specs as source of truth.** Code expresses mechanism. Specs express intent, decisions, constraints, rationale. Information that normally gets lost — why a rule exists, what alternatives were discarded, what constraint is being respected — lives permanently in the spec tree.

**Designed for AI.** Code from Spec is not a methodology adapted to use AI. It was built around the capabilities and limitations of AI from the start: context management that scales beyond any context window, confinement that minimizes hallucination, mechanical verification of every output, precision convergence through iteration.

**Additive and verifiable contributions.** No one overwrites anyone else's work. The domain expert contributes rules. The engineer contributes technical constraints. Guard nodes propagate rules automatically to all descendants. The domain expert can review a spec and evaluate whether it is correct before any code is generated. Quality becomes observable.

**Knowledge that accumulates.** The spec tree grows more precise over time. Every bug found, every edge case discovered, every constraint added becomes a permanent part of the spec. The asset is the spec tree, not the code. Changing a rule on day one or year three costs the same.

**Git as collaboration platform.** The spec tree is a directory of markdown files that lives in git. PRs, diffs, blame, branch protection — everything works without special tooling. But what changes is who participates: the diff of a spec is legible to any domain expert. The compliance officer reviews the PR that changes a regulatory rule. The accountant reviews the PR that changes reconciliation logic. The PR becomes an organizational gate, not just an engineering gate.

**The engineer does more engineering.** Freed from translation, the engineer does what the title always implied: analysis, design, constraints, quality. Not less engineering — more.

## The endgame

If code is derived from specs, it does not need to be versioned. It is a build artifact — like a compiled binary, generated from source and reproducible from it.

Today, repositories contain both specs and generated code. This is a transitional state. It exists because the methodology is young, trust is still being built, and teams need to inspect the generated output.

The logical conclusion is a repository that contains only the spec tree, test specs, and external dependencies. The CI pipeline receives the specs, dispatches agents to generate the code, runs the tests, and deploys. Code is ephemeral — generated, verified, and discarded.

Imagine the workflow: a compliance officer updates a regulatory constraint. A product manager adds a business rule. An engineer refines a concurrency pattern. Each commits to the spec tree. The pipeline generates the code, runs the tests, and deploys. No one wrote code. No one reviewed code. No one merged code. The entire team worked in specs — the artifact they all understand — and the software took care of itself.
