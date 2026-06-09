---
code-from-spec: ROOT/content/landing@yvSrDnUDQooGeX9daw-U-8bMK70
title: Code from Spec
description: A methodology where specifications are the source of truth and code is a generated artifact.
---

![Code from Spec](/images/banner.png)

# Code from Spec

A methodology where code is a generated artifact, not the source of truth.

## The problem

Software is written by people who hold context in their heads. The engineer receives requirements, translates them into code, and makes hundreds of decisions that are never recorded. When the engineer leaves, the decisions leave too.

Code expresses mechanism, not intent. You can read code and understand what it does. You cannot read it and understand why, what alternatives were considered, or what constraints it silently respects.

The industry built compensating mechanisms: comments, wikis, ADRs, onboarding docs. None work at scale because they exist separately from the system. They drift. The team stops trusting them.

## The solution

Specifications are the source of truth. They are organized as a tree — high-level intent at the root, implementation detail at the leaves. To change behavior, you change the spec and regenerate the code. You never edit generated code directly.

```
code-from-spec/
├── architecture/
│   └── backend/        ← conventions, constraints
├── domain/
│   └── transfers/      ← business rules
└── implementation/
    └── transfers/      ← leaf → generates source code
```

AI generates the code. The spec does not drift because the code is derived from it. The argument that killed formal specification forty years ago — that maintaining specs in sync with code was too expensive — no longer applies.

## Who benefits

**Engineers** return to what engineering was always supposed to be: analysis, design, resolving ambiguities. The agent types. The engineer thinks.

**Domain experts** — compliance officers, accountants, product managers — contribute directly to the spec tree in structured natural language. The software reflects their knowledge, not an engineer's interpretation of it.

**Organizations** get an auditable, versionable asset. Every decision recorded. Every constraint traceable. Every behavior attributable to a spec node with an author and a version.

## Get started

1. Install the init skill — copy a prompt into Claude Code to download it.
2. Run `/cfs-init-repo` to initialize the project.
3. Run `/cfs-init-session` at the start of each session.

See the [framework repository](https://github.com/CodeFromSpec/framework) for full documentation.

- [Read the full rationale](/rationale)
- [Browse articles](/blog)
- [View on GitHub](https://github.com/CodeFromSpec/framework)
