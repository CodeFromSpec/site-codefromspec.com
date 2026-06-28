---
title: Code from Spec
description: A software engineering methodology where specifications are the source of truth and code is a generated artifact.
---

![Code from Spec](/images/banner.png)

# Code from Spec

A software engineering methodology for the age of AI.

**Specifications are the source of truth, and code is a generated artifact.**

You write specifications. An agent generates the code from them. When a specification changes, the tooling detects which code is now stale, and that code is regenerated. The specification is the thing you author, review, and version; the code is its output.

The generated code is downstream of a specification tree and stays there, enforced rather than hoped for. The rest follows from how that enforcement works.

## The specification tree

Specifications are organized as a tree of nodes. Each node is a directory with a `_node.md` file, and its position in the filesystem is its position in the hierarchy. High-level intent sits at the root; implementation detail sits at the leaves. Only leaf nodes generate artifacts.

A node inherits the public content of every ancestor, automatically. A constraint written once at an ancestor node — an error-handling rule, a convention, a type — governs every leaf beneath it, including leaves added months later. What a node needs from elsewhere in the tree, it declares. The context for any generation is the path from the root to the node plus what the node declares — not searched for, not guessed. The structure decides what the agent sees.

Specifications are structured natural language — readable and reviewable by anyone who knows the domain, not only by programmers. A compliance officer can read a regulatory constraint in the spec and say "this is wrong" before any code is generated.

Specs live in git, reviewed in pull requests, diffed, and blamed, with the same machinery a team already uses for code.

## Confinement

The agent that generates a file sees only that node's chain — the inherited constraints, the declared dependencies, the node's own specification — and writes one declared output file. It does not browse the repository, read neighboring code, or fetch anything else. If the chain is not enough to generate the artifact, the correct output is not code. It is a report of exactly what is missing.

Confinement is enforced by the tooling, not requested of the agent. An unconfined agent facing an ambiguous spec compensates: it reads other files, infers patterns, and builds output that satisfies a model it assembled from context nobody chose. The result looks diligent but is subtly wrong in ways that are hard to trace. A confined agent has two options instead — generate from what it has, or report the gap. The third option, inventing context that looks like research, is the one confinement removes.

## Staleness, by hash

Each generated artifact records the hash of the chain that produced it. Change a specification, and the hash of every artifact that inherited or depended on it no longer matches. Those artifacts are stale, and stale is the signal to regenerate. The bookkeeping lives in a single manifest; generated files carry no framework metadata and no comments, so a regeneration that produces identical code produces no diff at all.

The hash answers one question: was this artifact produced from this specification. Whether the code is correct is a separate question, for a separate layer.

## Verification

An agent can hallucinate, omit a required step, misread an unambiguous spec, or preserve old behavior the spec has changed — and the code will compile and the manifest will read clean. Tests catch these. Tests are specifications too: authored, reviewed, and versioned alongside the rest, expressed as behavior — given this scenario, this expected result. The hash checks where the code came from; the tests check what it does. The methodology assumes an imperfect agent, because that is the agent that exists.

## Where we are

Code from Spec is on its fifth version, developed and refined through real projects. The spec tree format — how specifications are structured, how context is assembled, how staleness is detected — is approaching a definitive form. The core mechanics are being tested in practice.

Open problems remain. Natural language is ambiguous; the working answer is convergence, with test specs anchoring meaning mechanically — whether it converges fast enough for complex domains is unproven. Generation is non-deterministic; the guarantees are about behavior, verified by tests, not about the generated code itself. Authorship by someone with no software background is a direction, not a current capability. The tooling targets Claude Code; the spec format is client-agnostic, the orchestration is not.

[Explore the framework on GitHub](https://github.com/CodeFromSpec/framework)
