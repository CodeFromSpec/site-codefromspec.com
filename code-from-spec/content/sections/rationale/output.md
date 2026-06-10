---
code-from-spec: ROOT/content/sections/rationale@n2asRLkGyd9YuVruXYluZh6_AFM
title: Rationale
description: Why Code from Spec exists — how AI changes the economics of software, and why specifications are the key to making software a collaborative product.
---

![Rationale](/images/banner_rationale.png)

# Rationale

> The disruption is not in the coding — it is in who participates, how knowledge flows, and where accountability lives. AI is the enabler, not the goal.

## The promise

AI put code generation within everyone's reach. The narrative is that anyone can now build software — describe what you want, and the agent writes the code. For simple tasks, this might work. For anything that matters to an organization — compliance, finance, operations, security — it doesn't. Not yet.

A non-engineer who asks AI to build something receives output that appears to work. But they have no way to evaluate whether the error handling is adequate, whether the business logic covers the edge cases, whether the code is secure, whether it will behave correctly under load. They accept what they receive because they lack the expertise to question it.

## Specifications have been tried before

The idea of capturing domain knowledge in structured artifacts before writing code is not new. Formal methods in the 1970s and 1980s — structured analysis, Z notation, VDM, stepwise refinement — pursued exactly this goal. In safety-critical domains like aviation and medical devices, they succeeded and are still required today.

They never gained mainstream adoption. The reasons were multiple: maintaining a specification in sync with evolving code required constant manual effort and the spec inevitably drifted. The methods demanded specialized skills that most engineers didn't have. They were poorly suited to iterative development. For most organizations, the cost of maintaining the spec exceeded the cost of fixing the bugs it would have prevented.

The industry moved toward agility — shorter cycles, working software over comprehensive documentation, continuous collaboration. This was a rational response. Agile didn't eliminate specification entirely — user stories, acceptance criteria, and definitions of done are forms of specification. But it reduced upfront formal specification in favor of fast feedback. The tradeoff was that much of the domain knowledge that would have been in a formal spec ended up encoded implicitly in code, recoverable only by the people who wrote it.

AI changes this equation. When code is generated from spec, the synchronization problem disappears — the spec does not drift because the code is derived from it. There is no separate maintenance burden. The economic argument against formal specification, which held for forty years, no longer applies. Code from Spec builds on this: structured specifications that express intent, kept current by construction, without the maintenance cost that made them impractical before.

## What changes in the organization

AI is commoditizing code generation. But code generation was never the real cost. The real cost is the translation: every business decision, every compliance requirement, every operational constraint has to pass through the engineering team before it becomes software. That translation scales linearly — more software means more engineers.

Code from Spec breaks this proportionality. The engineering team defines the technical guardrails — architecture, error handling, security, platform constraints — and the rest of the organization contributes domain knowledge directly. The engineer stops being the translator and starts being the architect of the system that makes translation unnecessary.

This is not a demotion of engineering. It is the opposite. The engineer freed from translation does more engineering — analysis, design, constraints, quality, failure mode reasoning. The title finally matches the practice. The engineer is not hired to write code. The engineer is hired because they solve complex problems. Writing code was the mechanism available — it was never the role itself.

Software engineering knowledge remains necessary to produce reliable software. That is not going to change — even as AI improves, the judgment about architecture, failure modes, and system behavior requires human expertise. But this knowledge should not be a gate that prevents everyone else in the organization from contributing. The compliance officer knows the regulations better than any engineer. The accountant knows the financial rules. The product manager knows the users. Their knowledge should flow directly into the software, not through an engineer's interpretation.

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
