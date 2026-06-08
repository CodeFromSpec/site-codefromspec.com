---
title: Rationale
description: Why Code from Spec exists — the full rationale for the methodology and how it changes software development.
code-from-spec: ROOT/content/sections/rationale@CLD5p-0AuKX57Y6hQwqZjN5YERI
---

> Code from Spec is a methodology where code is a generated artifact, not the source of truth. Specifications are the source of truth. To change behavior, you change the specifications and regenerate the code. AI is the enabler, not the point. The disruption is in who participates, how knowledge flows, and where accountability lives.

## The problem

Software is written by people who hold context in their heads. The engineer receives requirements, translates them into code, and in that translation makes hundreds of decisions that are never recorded. When the engineer leaves, the decisions leave too.

Code expresses mechanism, not intent. You can read code and understand what it does. You cannot read it and understand why, what alternatives were considered, or what constraints it silently respects.

The industry built compensating mechanisms: comments, wikis, ADRs, onboarding docs. None work at scale because they exist separately from the system. They describe a system that changes independently. They drift. The team stops trusting them. The knowledge returns to people's heads.

## Why specifications failed before

The 1970s and 1980s produced rigorous methods for capturing domain knowledge before writing code. They failed not because they were wrong but because they were expensive. Maintaining a specification in sync with evolving code required constant manual effort. The spec drifted. The cost exceeded the benefit.

The industry responded with agility: shorter cycles, working software over documentation. This was rational. If specifications cannot be kept current, get feedback faster instead.

Agile solved the bottleneck by removing the spec. The knowledge became invisible — encoded in code that only the programmer could read.

## The programmer bottleneck

The industry tried to close the gap between domain knowledge and software from multiple directions. No-code and low-code platforms attempted to let non-programmers build software directly. They succeeded for narrow cases: forms, workflows, dashboards. They failed for anything complex enough to require real engineering judgment: error handling, concurrency, security, integration.

The fundamental issue persisted: someone had to translate domain knowledge into something executable. The programmer remained indispensable — expensive to train, scarce in supply, and the sole bridge between what the organization knew and what the software did.

## AI changes the economics

AI inverts the cost structure. Code generation is cheap. The scarce resource is no longer writing code — it is knowing what to write.

When code is generated from spec, synchronization is automatic by construction. The spec does not drift from the code because the code is derived from the spec. The argument that killed formal specification in the 1980s no longer applies.

## How it works

Specifications are organized as a tree. Each node adds precision to its parent — high-level intent at the root, implementation detail at the leaves. Only leaf nodes generate artifacts.

An orchestrator dispatches a generation subagent for each stale artifact. The subagent receives the chain — the ordered set of ancestor constraints, dependency interfaces, external references, and the target node's specification. The chain is the complete context. Nothing outside it is needed.

## Why it can be trusted

AI is not infallible. The methodology compensates through structure:

- **Small scope.** Each leaf generates a small, focused piece of code.
- **Complete context.** The subagent reads the full chain. Every constraint from every ancestor is present.
- **Tests as guardrails.** Every leaf has a sibling test node. Tests describe expected behavior, not implementation.
- **Build verification.** Every regeneration ends with build + test.

This is not blind trust. It is a framework that uses AI's strengths while compensating for its weaknesses with structural guardrails.

## Precision, not documentation

The word "specification" suggests a document. In practice, a spec node is a machine component that must fit precisely with every other part.

Precision means: every error has a formal name. Every function name is chosen once and used identically across every layer. Every record field has an explicit type.

When a spec says "file unreadable," different agents produce different implementations. When a spec says `FileUnreadable`, every agent produces `ErrFileUnreadable`. The difference between prose and formal names is the difference between approximate and exact generation.

## The real work

Spec authoring is harder and more iterative than it appears. A spec that seems clear to a human may be ambiguous to an agent. The tests fail. The team diagnoses, discovers the ambiguity, adds a constraint, and regenerates.

A single leaf may go through ten iterations before the spec reliably produces correct code. This is not failure. It is the methodology working. Each iteration makes the spec more precise and the team's understanding more explicit.

## Tests as accumulated knowledge

Test nodes describe what to verify, not how to implement. But generated test code often contains knowledge beyond the spec: specific values that triggered a production bug, sequences that exposed a race condition.

This knowledge accumulates. Before regenerating a test file, review it for knowledge that lives only in code. Migrate that knowledge to the test spec. Then regenerate.

## Software as a collaborative product

Code from Spec makes every contributor a direct author — not by making everyone a programmer, but by giving each domain expert a medium they can read and evaluate.

A compliance officer contributes regulatory constraints. A product manager contributes business rules. A legal team member contributes contract interpretations. An infosec engineer contributes security constraints. A software engineer contributes technical constraints.

Guard nodes at intermediate levels enforce constraints that all descendants must respect. The spec makes quality observable — correctness is no longer invisible, and shortcuts are no longer undetectable.

## AI as spec co-author

The same AI that generates code helps write specs. In practice, AI is an intellectual collaborator in spec development. It reviews specs, proposes function names and record structures, diagnoses test failures, traces them to specific spec gaps.

But AI does not make design decisions. The pattern is consistent: AI proposes, human disposes.

## The cost of change

Changing a business rule costs the same whether it is day one or year three: update the spec, regenerate the code.

Changes cascade widely but mechanically. The blast radius is large in files touched, small in decisions needed.

## The spec as organizational asset

A spec tree that grows with the system is a complete, versionable representation of organizational knowledge. It is not documentation — documentation drifts. The spec tree is the system; code is its shadow.

The asset compounds. The software becomes more correct not because the engineers got better, but because the domain knowledge got more explicit.

## Auditability

Every generated file carries an artifact tag linking it to the spec that produced it. The chain hash is a fingerprint of everything that contributed to the generation.

In regulated environments, this is compliance by construction.

## Context management

AI agents have finite context windows. The spec tree solves this by construction: each node's chain includes only what it declared. Adding hundreds of nodes does not inflate the context for existing nodes.

The total knowledge in the tree is unbounded. The context per generation is bounded and curated.

## Building trust

Trust in AI-generated code is built through evidence:

- **Phase 1: Full review.** Humans review specs and generated code. Every regeneration is inspected.
- **Phase 2: Sampling.** Humans review specs thoroughly but examine generated code by sampling.
- **Phase 3: Spec review only.** Humans review specs. Code is verified by tests and CI.
- **Phase 4: Governance gate.** Humans review specs only at the production deployment boundary.

Each transition is earned by evidence. Trust can regress. The phases are a dial to adjust, not a ladder to climb.

## The endgame

If code is derived from specs, it does not need to be versioned. The logical conclusion: a repository that contains only the spec tree and test specs. The CI pipeline generates code, runs tests, and deploys.

This vision is not yet realized. Every step toward it makes the methodology more valuable.

## The return to engineering

The engineer's job is analysis and design: understanding the domain, structuring the spec tree, placing guard nodes, resolving ambiguities. The agent types. The engineer thinks.

The engineer is not hired to write code. The engineer is hired because they solve complex problems.

## Caveats

AI is the weakest link. The structural guardrails exist because the agent cannot be trusted on its own.

The organizational shift is political — the methodology enables it, leadership causes it.

Not for everything: prototypes and trivial systems do not benefit. Code from Spec is designed for systems where the cost of getting it wrong exceeds the cost of specifying it precisely.

Implicit knowledge is invisible knowledge — if it is not in the spec tree, it will not be followed.
