---
title: Rationale
description: Why Code from Spec exists — how AI changes the economics of software, and why specifications are the key to making software a collaborative product.
---

![Rationale](/images/banner_rationale.png)

# Rationale

> The disruption is not in the coding — it is in who participates, how knowledge flows, and where accountability lives. AI is the enabler, not the goal.

## The promise

AI put code generation within everyone's reach. The narrative is that anyone can now build software — describe what you want, and the agent writes the code. For simple tasks, this works. For anything that matters to an organization — compliance, finance, operations, security — it doesn't. Not yet.

The gap is not in AI's ability to generate code. It is in what happens around the code. A non-engineer who asks AI to build something receives output that appears to work. But they have no way to evaluate whether the error handling is adequate, whether the business logic covers the edge cases, whether the code is secure, whether it will behave correctly under load. They accept what they receive because they have no frame of reference to question it.

Software engineering knowledge remains necessary to produce reliable software. That is not going to change — even as AI improves, the judgment about architecture, failure modes, and system behavior requires human expertise. But this knowledge should not be a gate that prevents everyone else in the organization from contributing. The compliance officer knows the regulations better than any engineer. The accountant knows the financial rules. The product manager knows the users. Their knowledge should flow directly into the software, not through an engineer's interpretation.

## The quality problem nobody talks about

Before AI, this was already broken. Agile compensated for the loss of formal specifications with short delivery cycles. When the programmer made a wrong domain decision, the user saw the result quickly and corrected it.

This worked when the end user was the domain expert. It failed for domains where the person who sees the demo is not the person who knows the rules. A product demo shows screens and flows. It does not show that the provisioning calculation uses the wrong cutoff date, or that the settlement logic violates a regulatory constraint. The compliance officer, the accountant, the legal analyst — they are not in the demo. Or if they are, they cannot tell from a demo whether the underlying logic is correct.

This creates a dynamic similar to the market for lemons. When the people who hold domain knowledge cannot evaluate the quality of the implementation, quality degrades. An engineering team that implements the domain rules precisely looks identical — from the stakeholder's perspective — to one that implements them approximately. Both pass the demo. The difference surfaces months later, in a failed audit or a reconciliation that doesn't balance. By then, the cost of correction is orders of magnitude higher.

AI did not fix this. It made code generation faster, but the quality problem is upstream of code generation. It lives in the gap between what domain experts know and what the software actually does. Making that gap wider — by letting anyone generate code without the ability to evaluate it — makes the problem worse, not better.

## Why specifications failed before — and why they won't this time

The software engineering community tried to solve this problem decades ago. Formal specifications in the 1970s and 1980s were exactly the right idea: structured artifacts that expressed intent, could be reviewed by domain experts, and guided implementation.

They failed because they were expensive. Maintaining a specification in sync with evolving code required constant manual effort. The spec drifted. The team stopped trusting it. Eventually the cost of maintaining the spec exceeded the cost of fixing the bugs it would have prevented. The industry rationally abandoned specifications in favor of agility — shorter cycles, working software over documentation.

AI removes the constraint that made specifications impractical. When code is generated from spec, synchronization is automatic — the spec does not drift because the code is derived from it. There is no separate maintenance burden. The economic argument against formal specification no longer holds.

This is not a marginal improvement. It is the removal of the constraint that made formal specifications impractical for forty years. Code from Spec restores what agile conceded — structured specifications that express intent — without reintroducing the bottleneck that killed them.

## Software is becoming a commodity

In the 1960s, building anything electronic meant designing your own circuits from scratch. It required specialized engineers, custom fabrication, and years of development. Today, you buy a USB controller off the shelf, pick a microcontroller from a catalog, snap a gyroscope onto a board. The components that once demanded teams of engineers to produce are readily available. Anyone with a soldering iron and a 3D printer can build hardware that would have required a factory fifty years ago. The specialized expertise didn't disappear. It moved upstream, into the companies that design the components everyone else uses.

Software is at the same inflection point. AI is commoditizing code generation — the equivalent of buying components off the shelf. But just as building hardware from commodity parts still requires knowing which components to use, how they integrate, and what constraints they must respect, building software still requires knowing what to build and how the pieces fit together.

This changes who needs to be involved and how. Today, large engineering teams exist because someone has to translate every business decision, every compliance requirement, every operational constraint into code. That translation scales linearly: more software means more engineers. Code from Spec breaks this proportionality. The engineering team defines the technical guardrails — architecture, error handling, security, platform constraints — and the rest of the organization contributes domain knowledge directly. The engineer is not hired to write code. The engineer is hired because they solve complex problems. Writing code was the mechanism available to exercise that role — it was never the role itself.

## Why this approach works

The premise is simple: if the people who know what the software should do can express that knowledge in a form that AI can reliably turn into correct code, then the engineer no longer needs to be the translator. The engineer becomes the architect of the system that makes this possible.

Code from Spec makes this concrete through several interlocking choices.

Specifications are written in structured natural language. Not code, not pseudocode, not diagrams — language that any domain expert can read, evaluate, and contribute to. This is what makes the collaboration real. The compliance officer can read the spec for a regulatory rule and say "this is wrong" before any code is generated. That was never possible when the rule lived in code.

Specs are the source of truth, not code. Code expresses mechanism — what the software does. Specs express intent — why it does it, what constraints it respects, what alternatives were considered. When the source of truth is code, knowledge that doesn't fit in code gets lost: the rationale behind a decision, the regulation that motivated a rule, the edge case that was deliberately excluded. When the source of truth is specs, all of that lives permanently in the tree.

The methodology is designed around AI's actual capabilities and limitations, not an idealized version of AI. Context windows are finite — so the spec tree structures knowledge into chains that deliver exactly the right context for each generation, without inflating as the system grows. Agents hallucinate — so generation is confined: the agent only sees the spec chain, cannot explore the repository, and every output is verified mechanically. Agents are inconsistent across runs — so specs converge toward precision through iteration, leaving less room for interpretation with each cycle.

Contributions are additive. The compliance officer contributes regulatory constraints. The engineer contributes technical guardrails. Neither overwrites the other. Guard nodes at intermediate levels propagate rules automatically to everything below them — a security policy written once applies to every descendant without anyone having to remember to apply it. Quality becomes observable: the domain expert can review a spec and evaluate whether it is correct before any code exists.

The spec tree lives in git. This matters more than it sounds. PRs, diffs, blame, branch protection — the entire collaboration infrastructure that engineering already uses works unchanged. But now the compliance officer can review the PR that changes a regulatory rule, reading structured natural language instead of code. The accountant can review the PR that changes reconciliation logic. The PR becomes an organizational gate — not just an engineering gate.

And the knowledge compounds. Every bug found adds a constraint to the spec. Every edge case discovered adds a verification. The spec tree grows more precise over time. The investment is in the specs — portable, versionable, independent of any specific AI model or tool. If a better generation engine appears tomorrow, the specs still work. The asset is the knowledge, not the implementation.

## The endgame

If code is derived from specs, it does not need to be versioned. It is a build artifact — like a compiled binary, generated from source and reproducible from it.

Today, repositories contain both specs and generated code. This is a transitional state. It exists because the methodology is young, trust is still being built, and teams need to inspect the generated output.

The logical conclusion is a repository that contains only the spec tree, test specs, and external dependencies. The CI pipeline receives the specs, dispatches agents to generate the code, runs the tests, and deploys. Code is ephemeral — generated, verified, and discarded.

Imagine the workflow: a compliance officer updates a regulatory constraint. A product manager adds a business rule. An engineer refines a concurrency pattern. Each commits to the spec tree. The pipeline generates the code, runs the tests, and deploys. No one wrote code. No one reviewed code. No one merged code. The entire team worked in specs — the artifact they all understand — and the software took care of itself.
