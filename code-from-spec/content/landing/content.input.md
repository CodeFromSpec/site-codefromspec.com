---
title: Code from Spec
description: A software engineering methodology where specifications are the source of truth and code is a generated artifact.
---

![Code from Spec](/images/banner.png)

# Code from Spec

A software engineering methodology for the age of AI.

Code from Spec is a methodology where specifications are the source of truth and code is a generated artifact.

It is designed for AI-assisted development at every stage — from writing specifications to generating code, reviewing quality, and enabling non-technical contributors to participate directly.

The methodology leverages what AI does well — generating code from precise context, synthesizing multiple constraints simultaneously, and assisting anyone in authoring specifications. It also addresses the practical limitations of AI: finite context, hallucination, and inconsistency across generations.

## The problem

Every organization that builds software has people who know what the software should do — compliance officers who know the regulations, accountants who know the rules, product managers who know the users. But they cannot turn that knowledge into software. The only path is through the engineering team: explain what you need and hope the translation is faithful. The gaps surface months later — in a failed audit, a reconciliation that doesn't balance, a regulatory finding.

AI promises to change this. Code generation is now cheap. But generating code was never the hard part — it was the most time-consuming, the one that required the most specialized knowledge, but not the hard part. The hard part is knowing what to build. AI solves construction, not comprehension — and without precise input, it builds the wrong thing just as efficiently as the right one.

## The vision

Software is becoming a commodity — like hardware before it. In the 1960s, building anything electronic meant designing your own circuits from scratch — it required specialized engineers, custom fabrication, and years of development. Today, you buy a USB controller off the shelf, pick a microcontroller from a catalog, snap a gyroscope onto a board. The components that once demanded teams of engineers are commodities. Anyone with a soldering iron and a 3D printer can build hardware that would have required a factory fifty years ago. The specialized expertise didn't disappear — it moved upstream, into the companies that design the components everyone else uses.

Software is at the same inflection point. AI commoditized code generation — the equivalent of buying components off the shelf. But just as building hardware from commodity parts still requires knowing which components to use, how they integrate, and what constraints they must respect, building software still requires knowing what to build and how the pieces fit together. The question is no longer who writes the code — it is how the organization expresses what it needs in a form that reliably becomes software.

Code from Spec answers that question. Specifications are structured natural language that domain experts can read, write, and review. The compliance officer writes the regulatory constraints. The product manager writes the business rules. The accountant writes the reconciliation logic. The software engineering team becomes a small, specialized group — like legal or compliance — that defines the technical guardrails and supports the rest of the organization in delivering software directly. AI assists everyone and generates the code.

Every contribution is additive. The compliance officer does not need to understand concurrency. The engineer does not need to understand the regulation. No one overwrites anyone else's work — each contributes what they know, and the system synthesizes code that satisfies all of it. The software reflects the entire organization's knowledge, not one team's interpretation of it.

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

- [Getting started guide](https://github.com/CodeFromSpec/framework#getting-started)
- [Read the full rationale](/rationale)
- [Browse articles](/blog)
- [View on GitHub](https://github.com/CodeFromSpec/framework)
