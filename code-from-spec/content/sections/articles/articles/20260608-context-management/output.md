---
code-from-spec: SPEC/content/sections/articles/articles/20260608-context-management@wXnnKa91RveQve4YJ_5IO1iIvoQ
title: Context management by construction
description: The industry builds machinery to assemble context for AI agents. Code from Spec gets it as a byproduct of structure.
date: 2026-06-08
---

# Context management by construction

June 8, 2026

## The context problem

![The context problem](/images/articles/20260608_image_1.png)

AI agents have a finite context window. No matter how large it becomes, a non-trivial system will always contain more knowledge than fits in a single prompt. The practical question is not whether the agent can hold the entire system in context — it cannot — but how the right context reaches the agent at the right time.

In a traditional AI-assisted workflow, the agent assembles context for itself: it reads source files, greps for symbols, scrolls through conversation history. This is ad hoc and fragile. The agent does not know what it does not know. It reads what it finds, infers what it can, and guesses the rest.

## Code is a poor context source

Code records mechanism, not intent. When an agent reads a function, it sees what the function does — not why it does it that way, what alternatives were considered, or what constraints it respects.

The agent reconstructs these things by reading more code, which provides more mechanism and less intent. Context assembled from code is voluminous but shallow.

## Retrieval guesses. Structure guarantees.

The industry's answer to the context problem is machinery: retrieval-augmented generation, memory systems, embeddings, context compaction. All of it shares one assumption — that relevance must be discovered at runtime. Some algorithm looks at the task, searches the available knowledge, and assembles what it predicts the agent will need. When the prediction is good, the agent gets useful context. When it is not, the agent gets noise, or silence, and cannot tell the difference.

Code from Spec has no context machinery at all — and that is the point. The spec tree is organized so that context follows from structure. Every node inherits the public content of its ancestors: the constraints that govern a leaf are, by definition, the path from the root to it. Everything else a node needs, it declares — `depends_on` is simultaneously a dependency declaration for staleness tracking and a context declaration for generation. One field, two meanings, no drift between them.

The consequence is a different kind of confidence. Context assembled by retrieval is probable: the algorithm found what looked relevant. Context assembled from the tree is guaranteed: every constraint that applies to the target is present because the structure puts it there, not because a search ranked it highly. The agent reading a chain does not hedge against unknown requirements somewhere in the repository. If it is not in the chain, it does not govern this artifact.

## Nobody designs the context

The author of a spec node never thinks about "what context will the agent need." They place the node where it belongs in the tree, and they declare the dependencies the node genuinely has. The context assembly follows automatically — the chain is computed from the structure, the same way for every node, every time.

This is why the approach scales. As the tree grows, adding new nodes does not inflate the context for existing ones: each node's chain contains its ancestors and its declarations, nothing else. The total knowledge in the tree is unbounded. The context per generation is bounded and curated. The tree can represent more than any context window can hold, while every individual generation receives exactly what it needs.

This is context management by construction, not by effort.

## Rules reach leaves that do not exist yet

Inheritance does more than trim context — it distributes it across time.

A guard node is an intermediate node whose public content prescribes concrete rules for everything below it: which library to use and how to import it, how to handle errors, what conventions tests must follow. Written once, the rule appears in the chain of every descendant — including descendants created long after the rule was written. A new component added today inherits constraints recorded months ago, by someone who may have since left the team. The tree does not care when a leaf was added. It enforces the same rules on all of them.

The alternative is what every team without this structure does: repeat the conventions in every prompt, hope the agent infers them from existing code, or rediscover them through failures. A rule that lives in an ancestor node is written once and obeyed everywhere, forever.

## Context accumulates across sessions

In a traditional workflow, a productive session with an AI produces code changes and maybe some comments. The next session starts over — the agent reads the code and tries to reconstruct what happened. The understanding built in the previous session is gone.

In Code from Spec, a productive session produces spec changes. Those changes are the context. The next session — with the same agent or a different one — picks up the spec tree as it stands. Every decision made in every previous session is present in the tree, structured and accessible. Context does not evaporate between sessions; it compounds across them, across contributors, across time.

## The other half

Curated context only means something if it is the only context. The same framework that assembles the chain also confines the generation agent to it: the agent reads the chain, writes the declared output, and touches nothing else. That restriction — why it exists and what it buys — is the subject of its own article, on confinement.

Together they form one design: the tree decides what the agent sees, and the framework ensures it sees nothing else. Context management is not a feature of Code from Spec. It is a consequence of the spec tree's structure. Build the tree right, and context takes care of itself.
