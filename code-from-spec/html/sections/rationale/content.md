---
title: Rationale
description: Why Code from Spec exists — how AI changes the economics of software, and why specifications are the key to making software a collaborative product.
---

![Rationale](/images/banner_rationale.png)

# Rationale

> The disruption is not in how code is written. It is in who participates in building software, and how their knowledge reaches the product. AI is the enabler, not the goal.

## The knowledge that never makes it into the software

Every organization that builds software is full of people who know exactly what the software should do. The compliance officer knows the regulations better than any engineer ever will. The accountant knows the reconciliation rules. The product manager knows the users. Yet none of them can put that knowledge into the software directly. The only path runs through the engineering team: explain what you need, wait, and hope the translation was faithful.

It rarely is — not because engineers are careless, but because translation is lossy by nature. The expert explains; the engineer interprets; the interpretation becomes code that the expert cannot read. The gaps stay invisible until they surface months later as a failed audit, a reconciliation that doesn't balance, or an edge case nobody handled because nobody knew it existed outside one person's head.

AI was supposed to close this gap. Code generation is now within everyone's reach: describe what you want, and the agent writes the code. For simple tasks, this works. For anything that matters to an organization, it doesn't — because a domain expert who asks AI to build something receives output they cannot evaluate. The code appears to work, and they accept it, because the alternative requires expertise they don't have. AI makes mistakes, hallucinates, and — perhaps more importantly — faithfully implements ambiguous requests in ways the requester never intended.

So the gap isn't closed. It has moved. The expert no longer depends on an engineer to write the code, but still depends on someone to tell them whether the code is right. Participation without the ability to evaluate is not participation. It is delegation with extra steps.

## What real participation requires

For a domain expert to genuinely contribute to software, two things must be true.

First, their knowledge must be expressed in a form they can read, write, and review themselves. Not code, not pseudocode — a representation where the compliance officer can look at a regulatory rule and say "this is wrong" before any code exists. The moment the knowledge is translated into a form only engineers understand, the expert is out of the loop again.

Second, their contribution must be protected from everything they cannot evaluate. The expert knows the business rule; they do not know about race conditions, injection attacks, or what happens under load. If contributing to software requires them to get those things right too, they cannot contribute safely — and no amount of AI assistance changes that, because they cannot evaluate the AI's output on those dimensions either.

These two requirements define a division of labor. Domain experts contribute what the software should do. Engineers contribute the constraints that make any implementation safe: error handling, security policies, performance boundaries. The two contributions must combine without either side translating, overwriting, or even fully understanding the other's work.

This is what Code from Spec is built to provide.

## Why this hasn't worked before

The idea of capturing knowledge in structured artifacts before writing code is not new. Formal methods in the 1970s and 1980s — structured analysis, Z notation, VDM — pursued a version of this goal. The research was rigorous. The results were promising. Mainstream adoption never followed.

The reason was economic, not technical. Maintaining a specification in sync with evolving code required constant manual effort. The spec inevitably drifted from the implementation, the team stopped trusting it, and the cost exceeded the benefit. Specifications survived only in industries where regulators force the issue — aviation, medical devices — because there, an external force pays the cost of rigor. Everywhere else, the cheapest process wins, and the cheapest process is code-and-fix.

There was a second, quieter reason: formal specifications were written in notations only specialists could read. They solved precision but not participation. The compliance officer was no closer to verifying a Z schema than to verifying the code itself. The knowledge still had to pass through a translator.

AI changes both constraints at once. Generating code from a specification is now fast and cheap, and when code is derived from the spec, the spec cannot drift — it is the source, not a parallel document to maintain. And because AI can work from structured natural language, the specification no longer needs to be written in a notation that excludes the very people whose knowledge it captures. For the first time, the artifact that drives the software is also an artifact the whole organization can read.

## How the pieces fit

Code from Spec makes this concrete through a few interlocking choices.

Specifications are structured natural language. This is what makes participation real rather than aspirational. The compliance officer reads the spec for a regulatory rule and evaluates it directly — before any code is generated. Quality becomes observable to the people who know the domain, which was never possible when the rule lived in code.

Contributions are additive and layered. The domain expert contributes business rules. The engineer contributes technical guardrails. Neither overwrites the other, and the guardrails propagate automatically: a security policy written once applies to every part of the system it governs, without the domain expert needing to know it exists. This is how an expert can contribute safely to a system they don't fully understand — the parts they cannot evaluate are enforced around them, not expected of them.

Specs capture intent, not just mechanism. Code expresses what the software does. Specs preserve why: the regulation behind a rule, the edge case deliberately excluded, the alternative considered and rejected. When the source of truth is code, this knowledge evaporates. When it is the spec tree, it accumulates.

The spec tree lives in git. PRs, diffs, branch protection — the collaboration infrastructure engineering already trusts works unchanged. But now the compliance officer can review the PR that changes a regulatory rule, and the accountant can review the PR that changes reconciliation logic, because what they're reading is structured natural language. The PR stops being an engineering gate and becomes an organizational one.

The methodology is designed around AI's actual limitations. Context windows are finite, so the spec tree assembles exactly the context each generation needs. Agents hallucinate, so generation is confined to the spec chain and outputs are verified against test specs. The design assumes an imperfect AI, because that is the AI we have.

## What happens to the engineer

The engineer does more engineering, not less.

In the hardware industry, components became commodities — and the specialized expertise didn't lose its value. It moved upstream, into designing the components everyone else builds with. The same movement applies here. When domain experts contribute content directly, the engineering team's work shifts from absorbing and translating other people's knowledge to designing the system that makes their knowledge reliably become software: architecture, constraints, failure modes, the guardrails that hold everything else up. The work the title "engineer" always implied.

This expertise remains indispensable. Software engineering judgment about system behavior, security, and failure modes is not something AI replaces, and it is not something domain experts acquire. The point was never to remove it — it was to stop using it as a gate that everyone else's knowledge must pass through.

## What we haven't solved

A rationale should be honest about its open problems, and this methodology has them.

Natural language is ambiguous. This is the historical reason specifications used formal notation, and choosing natural language for the sake of participation reopens the problem. Our working answer is convergence: specs become more precise through iteration, each cycle narrowing the room for interpretation, with test specs anchoring the meaning mechanically. Whether this converges fast enough, for domains complex enough, is being learned in practice — not assumed.

Generation is not deterministic. The same spec can produce different code across runs. Verification against test specs bounds the variation in behavior, but the methodology's guarantees today are about what the software does, not about the bytes that do it. Workflows that need stronger guarantees than that are not yet well served.

Truly non-technical authorship is still being figured out. Today, domain experts contribute most effectively alongside the structure engineers have set up. Making it practical for someone with no software background to produce software entirely on their own is the direction, not the current state.

These are open problems, not footnotes. They are also, for the first time, problems worth working on — because the economics that killed every previous attempt at specification-driven development have genuinely changed. The cost of writing and maintaining specs has collapsed. What remains is the work of making participation reliable. That is what Code from Spec is for.
