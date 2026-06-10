---
title: Rationale
description: Why Code from Spec exists — how AI changes the economics of software, and why specifications are the key to making software a collaborative product.
---

![Rationale](/images/banner_rationale.png)

# Rationale

> The disruption is not in how code is written. It is in who participates in building software, and how their knowledge reaches the product. AI is the enabler, not the goal.

## The promise

AI put code generation within everyone's reach. The narrative is that anyone can now build software: describe what you want, and the agent writes the code. For simple tasks, this might work. For anything that matters to an organization — compliance, finance, operations, security — it doesn't. Not yet.

A non-engineer who asks AI to build something receives output that appears to work. But they have no way to evaluate whether the error handling is adequate, whether the business logic covers the edge cases, whether the code is secure, whether it will behave correctly under load. They accept what they receive because they lack the expertise to question it.

This matters because AI makes mistakes, hallucinates, and — perhaps more importantly — faithfully implements ambiguous requests in ways the user never intended.

## Specifications have been considered before

The idea of capturing domain knowledge in structured artifacts before writing code is not new. Formal methods in the 1970s and 1980s — structured analysis, Z notation, VDM, stepwise refinement — pursued exactly this goal. The research was rigorous. The results were promising. But maintaining a specification in sync with evolving code required constant manual effort — the spec inevitably drifted from the implementation, the team stopped trusting it, and eventually the cost exceeded the benefit. Mainstream adoption never followed.

The reason is economic, not technical. In every industry, the natural incentive is speed and cost. Rigor only exists when an external force demands it. In construction, building codes exist because buildings collapse and people die. In aviation, DO-178C exists because planes crash. In medical devices, IEC 62304 exists because patients are harmed. In those domains, formal specifications survive — not because organizations chose them voluntarily, but because regulators require them.

Software engineering operates almost entirely without this forcing function. The consequences of poor software are usually delayed, diluted, and invisible until they become catastrophic. Without external pressure, the cheapest process wins. And the cheapest process is to code and fix — write something, test it, patch it, iterate. The result is that software development, despite carrying "engineering" in its name, is closer to craft than to engineering. Knowledge is tacit, quality depends on individual skill, and the process is largely ad-hoc. This is not a failure of discipline. It is a rational response to economic incentives.

AI changes the economics. Generating code from a specification is now fast and cheap. Maintaining the specification itself is cheaper too — AI assists in authoring, reviewing, and identifying gaps. When code is derived from spec, the spec does not drift because it is the source, not a separate document to maintain. The most expensive aspects of formal specifications — writing them, keeping them current — are drastically reduced. For the first time, rigor and speed point in the same direction.

## What changes in the organization

As AI makes code generation cheaper, a hidden cost becomes visible: the engineering team is the only path through which domain knowledge becomes software. Every business decision, every compliance requirement, every operational constraint has to pass through engineers who must first understand the domain, then implement it, then maintain it. This scales badly — more software means more engineers — and the quality of the result depends on how well the engineer understood a domain that is not theirs.

This is also a question of power. In many organizations, software is the most valuable asset the company produces. The team that controls the only channel through which that asset is created holds disproportionate influence — over priorities, timelines, and ultimately over what the product becomes. When no one else can evaluate the work, estimates go unchallenged, technical decisions become unchallengeable, and the business leader becomes dependent on the engineering team's interpretation of what is possible, what is expensive, and what is urgent.

The financial implication is direct. Engineering teams are expensive. When the organization can contribute domain knowledge directly and the engineering team focuses on guardrails rather than production, fewer engineers are needed — but those engineers do higher-value work. The savings are not just in headcount. They are in the elimination of the handoff cycle: requirements meetings, interpretation errors, rework, and the months between "what we asked for" and "what we got."

Code from Spec changes this structure. When specifications are written in natural language and the spec tree makes the work legible to non-engineers, the information asymmetry dissolves. A business leader can read what the software is supposed to do. A domain expert can verify whether the rules are correct. The engineering team retains its technical authority — architecture, security, platform decisions — but loses the monopoly on content. The rest of the organization gains the ability to contribute directly, evaluate quality, and hold the result accountable to their expertise.

The engineer does more engineering, not less. The role shifts from absorbing other people's domain knowledge to creating the conditions under which other people's knowledge reliably becomes software. Analysis, design, constraints, quality, failure mode reasoning — the work that the title "engineer" always implied.

Software engineering knowledge remains necessary to produce reliable software. That is not going to change — even as AI improves, the judgment about architecture, failure modes, and system behavior requires human expertise. But this knowledge should not be a gate that prevents everyone else from contributing. The compliance officer knows the regulations better than any engineer. The accountant knows the financial rules. The product manager knows the users. Their knowledge should flow directly into the software, not be filtered through someone else's understanding.

## Why this approach works

The premise is simple: if the people who know what the software should do can express that knowledge in a form that AI can reliably turn into correct code, then the engineer no longer needs to be the translator. The engineer becomes the architect of the system that makes this possible.

Code from Spec makes this concrete through several interlocking choices.

Specifications are written in structured natural language. Not code, not pseudocode, not diagrams — language that any domain expert can read, evaluate, and contribute to. This is what makes the collaboration real. The compliance officer can read the spec for a regulatory rule and say "this is wrong" before any code is generated. That was never possible when the rule lived in code.

Specs are the source of truth, not code. Code expresses mechanism — what the software does. Specs express intent — why it does it, what constraints it respects, what alternatives were considered. When the source of truth is code, knowledge that doesn't fit in code gets lost: the rationale behind a decision, the regulation that motivated a rule, the edge case that was deliberately excluded. When the source of truth is specs, all of that lives permanently in the tree.

The methodology is designed around AI's actual capabilities and limitations, not an idealized version of AI. Context windows are finite — so the spec tree structures knowledge into chains that deliver exactly the right context for each generation, without inflating as the system grows. Agents hallucinate — so generation is confined: the agent only sees the spec chain, cannot explore the repository, and every output is verified mechanically. Agents are inconsistent across runs — so specs converge toward precision through iteration, leaving less room for interpretation with each cycle.

Contributions are additive. The compliance officer contributes regulatory constraints. The engineer contributes technical guardrails. Neither overwrites the other. Guard nodes at intermediate levels propagate rules automatically to everything below them — a security policy written once applies to every descendant without anyone having to remember to apply it. Quality becomes observable: the domain expert can review a spec and evaluate whether it is correct before any code exists.

The spec tree lives in git. This matters more than it sounds. PRs, diffs, blame, branch protection — the entire collaboration infrastructure that engineering already uses works unchanged. But now the compliance officer can review the PR that changes a regulatory rule, reading structured natural language instead of code. The accountant can review the PR that changes reconciliation logic. The PR becomes an organizational gate — not just an engineering gate.

And the knowledge compounds. Every bug found adds a constraint to the spec. Every edge case discovered adds a verification. The spec tree grows more precise over time. The investment is in the specs — portable, versionable, independent of any specific AI model or tool. If a better generation engine appears tomorrow, the specs still work. The asset is the knowledge, not the implementation.

But specifying is not automatically rigorous. A bad spec produces bad code — with more confidence, which is worse. The rigor comes from the structure around the spec: guard nodes that propagate constraints, reviews by domain experts who can evaluate correctness, mechanical verification of every output, and an engineering team that designs the guardrails.

Rigor can also be incremental. An early-stage product can start with a minimal spec tree — few constraints, fast iteration. If the product proves its value, guard nodes, tests, security constraints, and compliance rules can be added at any point. The cost of adding rigor is the same on day one or year three — it is always a spec change and a regeneration. There is no legacy code to refactor. No accumulated technical debt to pay down. The decision to invest in quality does not have to be made before the product has proven it deserves the investment.

## The endgame

If code is derived from specs, it does not need to be versioned. It is a build artifact — like a compiled binary, generated from source and reproducible from it.

Today, repositories contain both specs and generated code. This is a transitional state. It exists because the methodology is young, trust is still being built, and teams need to inspect the generated output.

The logical conclusion is a repository that contains only the spec tree, test specs, and external dependencies. The CI pipeline receives the specs, dispatches agents to generate the code, runs the tests, and deploys. Code is ephemeral — generated, verified, and discarded.

Imagine the workflow: a compliance officer updates a regulatory constraint. A product manager adds a business rule. An engineer refines a concurrency pattern. Each commits to the spec tree. The pipeline generates the code, runs the tests, and deploys. No one wrote code. No one reviewed code. No one merged code. The entire team worked in specs — the artifact they all understand — and the software took care of itself.
