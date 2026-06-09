---
code-from-spec: ROOT/content/landing@iLzSfPxODJR1EylojZdKNm5-nzQ
title: Code from Spec
description: A software engineering methodology where specifications are the source of truth and code is a generated artifact.
---

![Code from Spec](/images/banner.png)

# Code from Spec

A software engineering methodology for the age of AI.

Code from Spec is a methodology where specifications are the source of truth and code is a generated artifact.

It is designed for AI-assisted development at every stage — from writing specifications to generating code, reviewing quality, and enabling non-technical contributors to participate directly.

The methodology leverages what AI does well: generating code from precise context, synthesizing multiple constraints simultaneously, and assisting anyone in authoring specifications. It also addresses the practical limitations of AI: finite context, hallucination, and inconsistency across generations.

## The problem

Every organization that builds software is full of people who know what the software should do: compliance officers who know the regulations, accountants who know the rules, product managers who know the users. But they cannot turn that knowledge into software. The only path is through the engineering team: explain what you need and hope the translation is faithful. The gaps surface months later, in a failed audit, a reconciliation that doesn't balance, or a regulatory finding.

AI promises to close this gap, as code generation is now within everyone's reach. But making software is more than generating code. It requires turning implicit knowledge into precise specifications, reconciling constraints from different domains, handling edge cases that no single person anticipates, building for the technical platform where the software will run, and verifying that the result actually does what was intended. Code generation is the easy part. Everything around it is the hard part.

## The vision

Software is becoming a commodity, just like hardware before it.

In the 1960s, building anything electronic meant designing your own circuits from scratch. It required specialized engineers, custom fabrication, and years of development. Today, you buy a USB controller off the shelf, pick a microcontroller from a catalog, snap a gyroscope onto a board. The components that once demanded teams of engineers to produce are readily available. Anyone with a soldering iron and a 3D printer can build hardware that would have required a factory fifty years ago. Of course, the specialized expertise didn't lose its value. It simply moved upstream, into the companies that design the components everyone else uses.

Software is at the same inflection point. AI is commoditizing code generation, the equivalent of buying components off the shelf. But just as building hardware from commodity parts still requires knowing which components to use, how they integrate, and what constraints they must respect, building software still requires knowing what to build and how the pieces fit together. The question is no longer who writes the code. It is how the organization expresses what it needs in a form that reliably becomes software.

Code from Spec answers that question. Specifications are structured natural language that domain experts can read, write, and review. The compliance officer writes the regulatory constraints. The product manager writes the business rules. The accountant writes the reconciliation logic. The software engineering team becomes a small, specialized group — like legal or compliance — that defines the technical guardrails and supports the rest of the organization in delivering software directly.

AI assists everyone and generates the code.

Every contribution is additive. The compliance officer does not need to understand concurrency. The engineer does not need to understand the regulation. No one overwrites anyone else's work. Each contributes what they know, and the system synthesizes code that satisfies all of it. The software reflects the entire organization's knowledge, not one team's interpretation of it.

## How it works

A domain expert describes what the software should do: business rules, regulatory constraints, reconciliation logic, in structured natural language. An engineer defines the technical boundaries: error handling, security policies, performance requirements. Both contributions live in a specification tree, where each level adds precision: high-level intent at the top, implementation detail at the bottom.

```
code-from-spec/
├── domain/
│   └── transfers/      ← business rules
├── architecture/
│   └── backend/        ← technical guardrails
└── implementation/
    └── transfers/      ← generates source code
```

AI generates code that satisfies every constraint in the tree, from every contributor, at every level. When someone changes a spec, the system detects which code is affected and regenerates it. The spec tree is the source of truth. Code is its shadow.

To change how the software behaves, you change the spec. To understand why it behaves a certain way, you read the spec. To audit who decided what, you check the spec's version history. The code is always derived, always current, always traceable.

## Where we are

Code from Spec is in its early days. It may already be used in real projects: for example, this website, or the MCP server that powers the framework itself, were both built with it. The methodology is on its third version, actively developed and refined through practical experience. Nevertheless, it is still young.

Some things are close to stable. The spec tree format — how specifications are structured, how context is assembled for the AI, how staleness is detected — is approaching what could be considered a definitive version. It works, it has been tested through real projects, and the core mechanics are solid.

Other things are still being discovered. We are actively figuring out how to make it practical for someone who truly does not understand software to produce software on their own. Key concepts like layers — how to organize specifications into progressive refinement stages — are still being refined. The tooling today targets Claude Code; the spec format is client-agnostic, but the orchestration assumes Claude Code. Porting to other environments is not a priority at this stage. The focus is on the methodology itself.

The vision is the direction. We are not there yet, and we are honest about the distance. But every iteration brings it closer.
