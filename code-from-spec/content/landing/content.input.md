---
title: Code from Spec
description: A software engineering methodology where specifications are the source of truth and code is a generated artifact.
---

![Code from Spec](/images/banner.png)

# Code from Spec

Code from Spec is a software engineering methodology where specifications are the source of truth and code is a generated artifact. It is designed for AI-assisted development at every stage — from writing specifications to generating code, reviewing quality, and enabling non-technical contributors to participate directly. The methodology is built around the practical limitations of AI: it delivers exactly the right context to the agent, minimizes hallucination through structural constraints, and verifies every output mechanically.

## The problem

Every organization that builds software has people who know what the software should do — compliance officers who know the regulations, accountants who know the rules, product managers who know the users. But they cannot turn that knowledge into software. The only path is through the engineering team: explain what you need, hope the translation is faithful, discover the gaps months later in a failed audit or a reconciliation that doesn't balance.

AI promised to change this. Code generation is now cheap. But generating code is not the bottleneck — it never was. The bottleneck is expressing what to build precisely enough that the result is correct. Without structure, AI fills the gaps with reasonable guesses that look right and silently miss the constraints that matter.

## The vision

Software is becoming a commodity — like hardware before it. In the 1960s, every company that needed computation designed its own hardware. Today, you buy it. Only those with ultra-specialized needs design their own.

Software is at the same inflection point. AI commoditized code generation. The question is no longer who writes the code — it is how the organization expresses what it needs in a form that reliably becomes software.

Code from Spec answers that question. Specifications are organized as a tree — structured natural language that domain experts can read, write, and review. The engineering team becomes a small, specialized group — like legal or compliance — that defines the structural guardrails and supports the rest of the organization in delivering software directly.

The compliance officer writes the regulatory constraints. The product manager writes the business rules. The accountant writes the reconciliation logic. The engineer defines the technical guardrails — concurrency patterns, error handling, security policies. AI assists everyone and generates the code. No one overwrites anyone else's contribution. The software reflects the entire organization's knowledge, not one team's interpretation of it.

## How it works

Specifications are organized as a tree. High-level intent at the root, implementation detail at the leaves. To change behavior, you change the spec and regenerate the code.

```
code-from-spec/
├── architecture/
│   └── backend/        ← conventions, constraints
├── domain/
│   └── transfers/      ← business rules
└── implementation/
    └── transfers/      ← leaf → generates source code
```

Each generated file is traceable to the spec that produced it. When a spec changes, affected artifacts are detected automatically and regenerated. The spec tree is the source of truth — code is its shadow.

## Get started

1. Install the init skill — copy a prompt into Claude Code to download it.
2. Run `/cfs-init-repo` to initialize the project.
3. Run `/cfs-init-session` at the start of each session.

See the [framework repository](https://github.com/CodeFromSpec/framework) for full documentation.

- [Read the full rationale](/rationale)
- [Browse articles](/blog)
- [View on GitHub](https://github.com/CodeFromSpec/framework)
