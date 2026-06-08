---
title: Code from Spec
description: A methodology where specifications are the source of truth and code is a generated artifact.
---

A methodology where code is a generated artifact, not the source of truth.

Specifications are organized as a tree. Each node adds precision to its parent. Only leaf nodes generate artifacts. To change behavior, you change the spec and regenerate. You never edit generated code directly.

[View on GitHub](https://github.com/CodeFromSpec/framework)

## The problem software has always had

Software is written by people who hold context in their heads. The engineer receives requirements, translates them into code, and makes hundreds of decisions that are never recorded. When the engineer leaves, the decisions leave too.

Code expresses mechanism, not intent. You can read code and understand what it does. You cannot read it and understand why, what alternatives were considered, or what constraints it silently respects.

The industry built compensating mechanisms: comments, wikis, ADRs, onboarding docs. None work at scale because they exist separately from the system. They drift. The team stops trusting them.

## How it works

Specifications are organized as a tree. Each node is a directory containing a `_node.md` file. Child nodes add precision to their parents — high-level intent at the root, implementation detail at the leaves.

```
code-from-spec/
├── architecture/
│   └── backend/        ← conventions, constraints
├── domain/
│   └── transfers/      ← business rules
└── implementation/
    └── transfers/      ← leaf → generates source code
```

An orchestrator dispatches a confined subagent for each stale artifact. The subagent receives the chain — the ordered set of ancestor constraints, dependency interfaces, and the target node's specification. The chain is the complete context. Nothing outside it is needed.

Staleness is detected automatically by comparing hashes. When a spec changes, every affected artifact is flagged for regeneration.

## Why now — AI changes the economics

The 1970s and 1980s produced rigorous methods for capturing domain knowledge before writing code. They failed because maintaining a specification in sync with evolving code was too expensive. The spec drifted. The industry abandoned specifications and embraced agility.

AI inverts the cost structure. Code generation is cheap. When code is generated from spec, synchronization is automatic — the spec does not drift because the code is derived from it. The argument that killed formal specification forty years ago no longer applies.

## Who benefits

**Engineers** return to what engineering was always supposed to be: analysis, design, structuring the spec tree, resolving ambiguities. The agent types. The engineer thinks.

**Domain experts** — compliance officers, accountants, product managers — contribute directly to the spec tree. They write what they know in structured natural language. The software reflects their knowledge, not an engineer's interpretation of it.

**Organizations** get an auditable, versionable asset. Every decision recorded. Every constraint traceable. Every behavior attributable to a spec node with an author and a version.

## Getting started

1. Install the init skill — copy a prompt into Claude Code to download the skill.
2. Run `/cfs-init-repo` to initialize the project.
3. Run `/cfs-init-session` at the start of each session.

See the [framework repository](https://github.com/CodeFromSpec/framework) for full documentation.

## Learn more

- [Read the full rationale](/rationale)
- [Browse articles](/blog)
- [View on GitHub](https://github.com/CodeFromSpec/framework)
