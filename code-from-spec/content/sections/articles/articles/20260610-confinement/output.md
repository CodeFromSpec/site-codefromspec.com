---
code-from-spec: ROOT/content/sections/articles/articles/20260610-confinement@eXfc_jJBmgIM0G7POOCan4uuWDY
title: Confinement
description: Why Code from Spec's generation agents see only the spec chain — and why taking context away from an AI makes its output trustworthy.
date: 2026-06-10
---

# Confinement

June 10, 2026

## What confinement is

In Code from Spec, the agent that generates an artifact does not see the project. It receives the spec chain — the inherited constraints of every ancestor node, the declared dependencies, the referenced external files, the target's own spec — and it writes one declared output file. That is the entire interaction. It does not browse the repository, read neighboring code, or fetch documentation. If the chain is not enough to generate the artifact, the correct output is not code — it is a report of exactly what is missing.

This sounds like a limitation. It is the most important design decision in the methodology.

Confinement makes generation a function: the output is determined by the chain and nothing else. This is what gives the framework's other guarantees their meaning. Staleness detection compares a hash of the chain against the hash recorded in the artifact — a comparison that only proves something because nothing outside the chain influences the result. Remove confinement, and the hash still computes, but it no longer tells the truth.

## The third option

To see why confinement matters, watch what an unconfined agent does when a spec is ambiguous.

It compensates. It reads other source files to see how similar problems were solved. It looks at tests for patterns. It explores the repository for context. This feels productive — the agent is gathering information, being diligent. But the information it gathers may be wrong, outdated, or irrelevant, and the agent has no way to know which. It builds a mental model from fragments and generates code that satisfies the model instead of the spec. The output looks sophisticated. It is subtly wrong in ways that are hard to detect, because the errors trace back to context nobody chose to provide.

A confined agent facing the same ambiguity has exactly two options: make its best judgment from the chain it has, or report that the spec is insufficient. Both are useful. The first produces code that tests can evaluate; the second produces a finding that improves the spec. What confinement removes is the third option — inventing context that looks like research. That option is the dangerous one, precisely because it looks like the most diligent one.

This is not a hypothetical failure mode. During the development of the framework's own tooling, the working convention was that every Go component gets three spec nodes: interface, implementation, test. The convention had been agreed in conversation — but it was not yet written into a node that new specs would inherit. The next time nodes were created, the agent skipped the interface nodes. Not as a decision; it simply was not in its chain. The lesson generalizes: an agent's compliance is only as good as its chain. Tell it once in conversation, and it might comply. Put the rule in the chain, and it complies every time.

## Ambiguity becomes signal

An unconfined agent hides spec problems. It papers over gaps with inference — and the gap stays invisible until it surfaces as a bug nobody can trace. A confined agent does the opposite: it converts spec problems into evidence.

Two incidents from real sessions show the shape of this.

A project's tests needed to change the working directory before creating temporary files, so that project-relative paths would resolve correctly. The tests in one subtree had this pattern in their chain, inherited from an ancestor node. The tests in another subtree did not. The first group passed. The second group failed — every single test, with the same error. The failure pattern pointed directly at the missing context: the fix was not to regenerate, but to add the pattern to the common ancestor. After that, every test generation followed it.

In another case, a spec said to match file patterns but not which function to use. Go has two: one is platform-dependent, the other is not. The agent picked one; it was wrong on Windows. The diagnosis ended with three words added to the spec — and the problem was solved permanently, for every future generation, by any agent.

This is the property worth noticing: under confinement, failures are not random. Each one traces to a specific place where the chain was silent or ambiguous. Fixing the spec closes the gap forever. A team that regenerates everything from scratch and watches what fails is running a diagnostic on its own specifications — and confinement is what makes the diagnostic readable.

The same property shows up in the economics. A confined generation costs roughly the chain plus the output — both bounded by the spec, both known in advance. There are no exploration tokens, and no false starts built on context the agent assembled wrong. The cheapest path and the most correct path are the same path.

## Autonomy in the right place

Confinement is not a statement of distrust in AI. The orchestrator — the agent that decides what is stale, assembles the work, dispatches generations, and verifies results — remains free and capable. Code from Spec removes autonomy from exactly one place: the moment where spec becomes artifact. Autonomy stays where judgment lives. Confinement applies where fidelity matters more than initiative.

This mirrors the methodology's organizational design. A guard node protects a domain expert from the dimensions they cannot evaluate: the product manager who adds an endpoint cannot accidentally bypass the security policy, because the policy sits above them in the tree. Confinement protects the generation step in the same way: the agent cannot drift from the contributors' intent, because the chain is the only channel through which intent reaches it.

That single channel is what makes distributed authorship hold. The compliance officer's rule governs the generated code only if the agent cannot learn a different behavior somewhere else. In one real session, code merged in from another service imported a shared error library under an alias — a violation of the target project's constraint, written once in a guard node. The generation agent corrected it without being asked. The subagent followed the chain, not the existing code. Confinement does not only block invention; it enforces the rules that are actually in force, even against code that disagrees.

## Trust the framework, not the agent

Agents make mistakes. They hallucinate, they misread, they pick the wrong function from two reasonable candidates. A methodology that depends on AI generation has to start from that fact, not hope around it.

Confinement is what makes the mistakes survivable. A confined agent's errors are bounded: they originate in the chain, which means they are reproducible, diagnosable, and fixable at the source — once, permanently. An unconfined agent's errors are unbounded: they originate in whatever it happened to read, a context nobody chose, nobody reviewed, and nobody can reconstruct. Bounded errors are caught by tests and closed by spec fixes. Unbounded errors are investigated by archaeology.

Confinement is design, not dogma — and the proof is its one deliberate exception. When an artifact is regenerated, the agent receives the existing file alongside the chain, so it can produce a minimal diff instead of a full rewrite. The exception is carefully fenced: the existing artifact does not participate in the chain hash, and when an agent anchors on a bug in the old code, the remedy is to delete the file and regenerate from the chain alone. Even what enters through this door is curated — generated artifacts carry no comments, because a comment is a narrative the agent will treat as a second source of truth, competing with the spec it is supposed to follow.

The conclusion is the methodology's quiet inversion of the industry's current instinct. The prevailing direction is to hand agents more tools, more access, more autonomy, and to trust that capability scales into reliability. Code from Spec trusts something else: structure. You do not trust the agent. You trust the chain that feeds it, the tests that check it, and the hash that says whether its work is current. Confinement is what makes that trust rational — because everything the agent did, it did from inputs you wrote, reviewed, and versioned.
