---
code-from-spec: ROOT/content/sections/blog/context-management@OT0TQYdnFAowswef7xwevooF3wc
title: Context management by construction
description: How Code from Spec gives AI agents exactly the context they need — no more, no less.
date: 2026-06-08
---

# Context management by construction

June 8, 2026

## The context problem

AI agents have a finite context window. No matter how large it becomes, a non-trivial system will always contain more knowledge than fits in a single prompt.

In a traditional AI-assisted workflow, the agent reads source files, grep results, and conversation history to assemble context. This is ad hoc and fragile. The agent does not know what it does not know. It reads what it finds, infers what it can, and guesses the rest. Each session starts from scratch — the previous session's understanding is lost.

## Code is a poor context source

Code records mechanism, not intent. When an agent reads a function, it sees what the function does — not why it does it that way, what alternatives were considered, or what constraints it respects.

The agent reconstructs these things by reading more code, which provides more mechanism and less intent. Context assembled from code is voluminous but shallow.

## The chain: precise context by construction

The spec tree inverts this. Every node is a unit of explicit context — intent, constraints, decisions, interfaces. The `depends_on` mechanism is simultaneously a dependency declaration (for staleness tracking) and a context declaration (for generation).

When a generation subagent receives a chain, it receives exactly the context it needs: the ancestors' constraints, the dependencies' interfaces, the external references, the target's spec. Nothing more, nothing less.

A typical chain for a leaf node is 3,000–5,000 tokens. A project with 40 packages might have 50,000+ lines of code. The chain for any single package is under 200 lines of spec. The ratio is roughly 10:1 — for the same information quality, the chain costs an order of magnitude fewer tokens.

The author of a spec node does not think about "what context will the agent need." They declare dependencies because the node genuinely depends on them. The context assembly follows automatically. As the tree grows, adding hundreds of new nodes does not inflate the context for existing nodes — each node's chain includes only what it declared.

This is context management by construction, not by effort.

## Context that accumulates across sessions

In a traditional workflow, a productive four-hour session with an AI produces code changes and maybe some comments. The next session starts over — the agent reads the code and tries to reconstruct what happened. The context from the previous session is gone.

In Code from Spec, a productive session produces spec changes. Those changes are the context. The next session — with the same agent or a different one — picks up the spec tree as it stands. Every decision made in every previous session is present in the tree, structured and accessible.

The spec tree can represent more knowledge than any single context window can hold, while still providing each agent invocation with precisely the context it needs. The total knowledge in the tree is unbounded. The context per generation is bounded and curated.

## Guard nodes amortize rule costs

A guard node is an intermediate node whose public content prescribes concrete rules that all descendants inherit. From a token perspective, guard nodes are remarkably efficient.

A single rule — like "import the error library without alias" — is perhaps 20 tokens in the ancestor node. It propagates to every leaf in the subtree. In a project with 60 leaf nodes under that ancestor, the rule costs 20 tokens per generation (it appears once in each chain) but prevents an error that would cost thousands of tokens to diagnose and fix per occurrence.

Without the guard node, the rule would need to be repeated in each leaf's spec — 60 × 20 = 1,200 tokens of redundant specification, plus the maintenance burden of keeping 60 copies in sync. With inheritance, 20 tokens in one place does the work of 1,200.

More importantly, guard nodes prevent the expensive failure mode: a subagent generates incorrect code, tests fail, a human diagnoses the issue, fixes the spec, regenerates. Each iteration of this cycle costs 15,000–30,000 subagent tokens. A 20-token rule that prevents the failure across 60 nodes saves potentially 900,000–1,800,000 tokens in avoided rework.

## The economics

Confinement — the rule that generation subagents can only read the chain and write the declared output — is usually discussed as a correctness mechanism. But it is equally a cost mechanism:

- No exploration tokens. The agent does not spend tokens reading files to figure out what to do. The chain tells it.
- No false starts. The agent does not generate code based on incorrect assumptions assembled from repository exploration, then need to regenerate.
- Predictable cost. Each generation costs roughly `chain_tokens + output_tokens`. Both are bounded by the spec size, which is known in advance.

In a traditional "read the repo and implement" workflow, the agent might read 20,000 tokens of code before writing 500 lines. With confinement, it reads 4,000 tokens of spec and writes the same 500 lines. The reading cost is 5x lower, and the output quality is higher because the context is intent, not mechanism.

## The cheapest path is the most correct path

Each design decision in the framework — confinement, inheritance, chain assembly — serves both correctness and cost. They are not in tension.

Context management is not a feature of Code from Spec. It is a consequence of the spec tree's structure. Build the tree right, and context takes care of itself.
