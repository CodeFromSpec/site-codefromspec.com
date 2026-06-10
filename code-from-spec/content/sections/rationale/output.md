---
code-from-spec: ROOT/content/sections/rationale@j1l-vT4YD8MEWSl6dbjDFbsd6wQ
title: Rationale
description: Why Code from Spec exists — how AI changes the economics of software, and why specifications are the key to making software a collaborative product.
---

![Rationale](/images/banner_rationale.png)

# Rationale

> The disruption is not in the coding — it is in who participates, how knowledge flows, and where accountability lives. AI is the enabler, not the goal.

## The problem

Software is written by people who hold context in their heads. The engineer receives requirements, translates them into code, and in that translation makes hundreds of small decisions that are never recorded anywhere. When the engineer leaves, the decisions leave too. What remains is code that works — until it doesn't — and that nobody fully understands anymore.

Code is not a good medium for expressing intent. It expresses mechanism. You can read code and understand what it does; you cannot read it and understand why it does that, what alternatives were considered, or what constraints it is silently respecting. The industry built compensating mechanisms: comments, wikis, ADRs, runbooks, onboarding docs. None of them work at scale because they are separate from the system. They drift. The team stops trusting them. The knowledge returns to people's heads.

Every organization that builds software has two kinds of knowledge: domain knowledge and technical knowledge. Domain knowledge lives in the people who understand the business — compliance officers who know the regulations, legal teams who know the contracts, accountants who know the financial rules, product managers who know the user needs. Technical knowledge lives in the engineers who know how to build systems.

The engineer was the only author of software. They received domain knowledge as requirements — documents, meetings, conversations — and translated it into code. This translation was lossy. Every handoff lost information. Every interpretation introduced error. The result was systems that worked technically but implemented the domain approximately. Compliance rules that were almost right. Business logic that handled the common case but missed the edge case that the domain expert knew about but never thought to mention.

## Why specifications failed before

The software engineering community recognized this problem decades ago. Structured analysis, stepwise refinement, formal specifications — the 1970s and 1980s produced rigorous methods for capturing domain knowledge before writing code. The goal was exactly what Code from Spec describes: a structured artifact that expressed intent, could be reviewed by domain experts, and guided implementation.

The methods failed not because they were wrong but because they were expensive. Maintaining a specification in sync with evolving code required constant manual effort. The spec drifted. The team stopped trusting it. The cost of maintaining the spec exceeded the cost of fixing the bugs it would have prevented.

The industry responded with agility: shorter cycles, working software over documentation, embrace change. This was a rational response to a real constraint. If specifications cannot be kept current, don't maintain them — get feedback faster instead.

Agile solved the bottleneck by removing the spec. The knowledge that would have been in the spec became invisible — encoded in code that only the programmer could read. When the programmer left, the knowledge left too.

## The programmer bottleneck

The software industry has always known that the bottleneck is not typing code — it is understanding what to build. The cost of producing programmers is enormous. Years of formal education, years of practical experience, continuous learning as platforms and languages evolve. And the output of this investment is a person who translates — who takes someone else's understanding and re-expresses it in a language machines can execute.

The industry tried to close the gap from multiple directions. No-code and low-code platforms attempted to let non-programmers build software directly. They succeeded for narrow cases: simple workflows, forms, dashboards. They failed for anything complex enough to require real engineering judgment: error handling, concurrency, security, integration with other systems.

The fundamental issue persisted: someone had to translate domain knowledge into something executable. The programmer remained indispensable — expensive to train, scarce in supply, and the sole bridge between what the organization knew and what the software did.

## The quality problem

Agile compensated for the loss of specifications with short cycles. When the programmer made a wrong domain decision, the delivery cycle was short enough that the user saw the result quickly and corrected it. This worked as implicit domain feedback — not a spec review, but "I saw it running, that's wrong, fix it."

This worked well when the end user was the domain expert. It worked less well for domains where the person who sees the demo is not the person who knows the rules. A product demo shows screens and flows — it does not show that the provisioning calculation uses the wrong cutoff date, or that the settlement logic violates a regulatory constraint. The compliance officer, the accountant, the legal analyst — they are not in the demo. Or if they are, they cannot tell from a demo whether the underlying logic is correct.

This creates a dynamic similar to the market for lemons: when the people who hold domain knowledge cannot evaluate the quality of the implementation, quality degrades. An engineering team that implements the domain rules precisely is indistinguishable — from the stakeholder's perspective — from one that implements them approximately. Both pass the demo. Both look correct on the surface. The difference surfaces months later, in a failed audit, a reconciliation that doesn't balance, a regulatory finding. By then, the cost of correction is orders of magnitude higher.

The developer who spends time getting edge cases right, validating with domain experts, handling errors precisely — delivers more slowly. The one who cuts corners delivers faster and receives praise. The quality that matters is invisible in the short term: it manifests only as the absence of problems, which no one notices.

## AI changes the economics

AI inverts the cost structure. Code generation is now cheap. An agent can implement a well-specified component in seconds. The scarce resource is no longer writing the code — it is knowing what to write.

More importantly: when code is generated from spec, synchronization is automatic by construction. The spec does not drift from the code because the code is derived from the spec. There is no separate maintenance burden. The argument that killed formal specification in the 1980s no longer applies.

This is not a marginal improvement. It is the removal of the constraint that made formal specifications impractical for forty years.

Code from Spec restores what agile conceded — without reintroducing the bottleneck. The spec tree provides the structured specification that formal methods promised, kept current by construction because code is derived from it. The short cycles, incremental delivery, and feedback loops of agile remain — but now each iteration produces a spec change and a regeneration, not an ad-hoc code change that drifts from an outdated document.

## Software as commodity

Software is at the same inflection point that hardware reached decades ago. In the 1960s, building anything electronic meant designing your own circuits from scratch. It required specialized engineers, custom fabrication, and years of development. Today, you buy a USB controller off the shelf, pick a microcontroller from a catalog, snap a gyroscope onto a board. The components that once demanded teams of engineers to produce are readily available. Anyone with a soldering iron and a 3D printer can build hardware that would have required a factory fifty years ago. The specialized expertise didn't disappear. It moved upstream, into the companies that design the components everyone else uses.

AI is commoditizing code generation. The equivalent of buying components off the shelf. But just as building hardware from commodity parts still requires knowing which components to use, how they integrate, and what constraints they must respect, building software still requires knowing what to build and how the pieces fit together. The question is no longer who writes the code. It is how the organization expresses what it needs in a form that reliably becomes software.

This changes the economics of software organizations. Today, large engineering teams exist because someone has to translate every business decision, every compliance requirement, every operational constraint into code. That translation is the bottleneck, and it scales linearly: more software means more engineers.

Code from Spec breaks this proportionality. The engineering team becomes a small, specialized group — like legal or compliance — that defines the structural guardrails and supports the rest of the organization in delivering software directly. The engineer is not hired to write code. The engineer is hired because they solve complex problems. Writing code was the mechanism available to exercise that role — it was never the role itself.

The implications are profound. Software budgets shift from production to precision — from paying people to write code to investing in the quality of specifications. Hiring changes: the organization needs fewer engineers, but those engineers need deeper judgment about architecture, constraints, and failure modes. Onboarding changes: a new team member reads the spec tree and understands the system, instead of spending weeks reading code and asking colleagues. Vendor dependency changes: the spec tree is portable. If a better generation tool appears tomorrow, the specs still work. The investment is in knowledge, not in implementation.

## Software as a collaborative product

Code from Spec makes every contributor a direct author. But — and this is the key distinction — not by making everyone a programmer. Each contributor is a domain expert who adds their knowledge to the spec tree:

- A **compliance officer** contributes regulatory constraints. They know which rules apply, what the penalties are, what the regulator expects.
- A **product manager** contributes business rules. They know how the product should behave, what the user needs, what the edge cases are.
- A **legal team member** contributes contract interpretations. They know what the agreement says and what it means.
- An **infosec engineer** contributes security constraints. They know the threat model and the countermeasures.
- A **software engineer** contributes technical constraints — performance requirements, concurrency patterns, error handling strategies, platform considerations. They are a domain expert too: their domain is software itself.

Every contribution is additive. The compliance officer does not need to understand concurrency. The engineer does not need to understand the regulation. No one overwrites anyone else's work — each contributes what they know, and the agent synthesizes code that satisfies all of it simultaneously.

The spec makes quality observable. When the compliance officer reviews a spec node, they can tell whether the rules are right — before any code is generated. The developer who gets the domain right is visibly different from the one who approximates, because the spec makes the difference legible to the people who know. Correctness is no longer invisible, and shortcuts are no longer undetectable.

## How it works

Specifications are organized as a tree. Each node is a directory containing a specification file. Child nodes add precision to their parents — high-level intent at the root, implementation detail at the leaves. Only leaf nodes generate artifacts.

An orchestrator dispatches a generation agent for each artifact that needs updating. The agent receives the **chain** — the ordered set of ancestor constraints, dependency interfaces, external references, and the target node's specification. The chain is the complete context for generation. Nothing outside it is needed. Nothing inside it is redundant.

This is fundamentally different from "read the whole repo and figure it out." The chain gives the agent intent directly. No reconstruction needed. The more precise the chain, the less room for invention. And the less the agent invents, the more correct the output.

Guard nodes at intermediate levels enforce constraints that all descendants must respect — security policies, error handling standards, coding conventions. A contributor who adds a new feature cannot accidentally bypass security requirements. The constraints are above them in the tree. The agent reads them. The generated code respects them.

## Precision, not documentation

The word "specification" suggests a document. In practice, a spec node is a machine component — a part that must fit precisely with every other part. The level of detail required goes far beyond what "documentation" evokes.

Precision means: every error has a formal name. Every function name is chosen once and used identically across every layer. Every record field has an explicit type. Every test case prescribes not just the expected outcome but the exact setup that produces it.

When a spec says "file unreadable," different generation agents produce different sentinel names, different wrapping patterns, different test assertions. When a spec says `FileUnreadable`, every agent produces `ErrFileUnreadable`. The difference between prose and formal names is the difference between approximate and exact generation.

This precision is expensive to achieve. But the cost is paid once. Every subsequent regeneration benefits — not just the next one, but every one, forever.

## The real work

Spec authoring is harder and more iterative than it appears. A spec that seems clear to a human may be ambiguous to an agent. The agent makes a reasonable implementation choice — and it is wrong. The tests fail. The team diagnoses, discovers the ambiguity, adds a constraint to the spec, and regenerates.

A single leaf node may go through ten or more iterations before the spec is precise enough to reliably produce correct code. This is not failure. It is the methodology working. Each iteration makes the spec more precise, the generated code more predictable, and the team's understanding of the domain more explicit. The knowledge that was implicit — "obviously you'd use forward slashes," "obviously you'd skip directories" — becomes explicit in the spec. That explicit knowledge is the asset.

The ultimate test of a spec tree is not whether it generates correct code once. It is whether it generates correct code after every artifact is deleted and regenerated from scratch. If the regeneration produces a working system, the specs are sufficient. If not, the failures point directly to spec gaps.

## Tests as accumulated knowledge

In traditional development, tests are implementation artifacts — when the code is rewritten, the tests are rewritten too. Knowledge encoded in test cases is lost and rediscovered with each rewrite.

Code from Spec treats tests differently. Test nodes describe what to verify, not how to implement. But the generated test code often contains knowledge that goes beyond the spec: specific values that triggered a production bug, sequences that exposed a race condition, assertions that catch subtle regressions.

This knowledge accumulates. Every bug found in production adds a scenario. Every edge case discovered adds a verification. The test file grows monotonically — it is a living record of everything the system has learned about its own failure modes.

Before regenerating a test file, review it for knowledge that lives only in code. Migrate that knowledge to the test spec. Then regenerate. This is the cost of the methodology — and the mechanism by which the spec tree absorbs the organization's learning.

## The cost of change

The software industry has long accepted that the cost of fixing a defect grows the later it is discovered. Code from Spec collapses this curve. Every change is a spec change. Correcting a business rule three months after the initial implementation costs the same as correcting it on day one: update the spec, regenerate the code.

Changes cascade widely but mechanically. Changing a fundamental type may touch dozens of files, but every change at every cascade point requires no creative judgment — it is mechanical regeneration. The blast radius is large in files touched, small in decisions needed.

## The spec as organizational asset

A spec tree that grows with the system is a different kind of asset from anything that exists in software organizations today.

It is not documentation. Documentation describes a system that exists independently and drifts as the system changes. The spec tree is the system — code is its shadow. When the spec changes, the code changes. There is no drift by construction.

It is not code. Code expresses mechanism. The spec tree expresses intent, decisions, constraints, and the rationale behind every behavior. It is legible to anyone who understands the domain, regardless of technical background.

The asset compounds. A team that uses Code from Spec for a year has a spec tree that reflects a year of learning — every bug that was fixed, every edge case that was discovered, every compliance requirement that was added. The software becomes more correct not because the engineers got better, but because the domain knowledge got more explicit.

## Auditability

Every generated source file carries an artifact tag linking it to the spec that produced it. The chain hash is a content-based fingerprint of the entire chain — every ancestor, dependency, and external file that contributed to the generation. The git history of the spec directory is a complete audit trail: who wrote each node, when it changed, and what it said at every point in time.

In regulated environments — finance, healthcare, government — this is compliance by construction. An auditor can trace any behavior in the running system back to a spec node, to the person who authored it, to the version that introduced it. No reconstruction needed. No forensic archaeology of code commits.

## Context management

AI agents have a finite context window. No matter how large it becomes, a non-trivial system will always contain more knowledge than fits in a single prompt.

In a traditional AI-assisted workflow, the agent reads source files, grep results, and conversation history to assemble context. This is ad hoc and fragile. The agent does not know what it does not know. Code is a poor context source — it records mechanism, not intent. Context assembled from code is voluminous but shallow.

The spec tree solves this by construction. Every node is a unit of explicit context — intent, constraints, decisions, interfaces. Each node's chain includes only what it declared. Adding hundreds of nodes to the tree does not inflate the context for existing nodes. The total knowledge in the tree is unbounded. The context per generation is bounded and curated.

The spec tree also accumulates context across sessions. A productive session produces spec changes. The next session — with the same agent or a different one — picks up the spec tree as it stands. Every decision made in every previous session is present in the tree, structured and accessible. Context compounds across sessions, across contributors, across time.

## The endgame

If code is derived from specs, it does not need to be versioned. It is a build artifact — like a compiled binary, generated from source and reproducible from it.

Today, repositories contain both specs and generated code. This is a transitional state. It exists because the methodology is young, trust is still being built, and teams need to inspect the generated output.

The logical conclusion is a repository that contains only the spec tree, test specs, and external dependencies. The CI pipeline receives the specs, dispatches agents to generate the code, runs the tests, and deploys. Code is ephemeral — generated, verified, and discarded.

Imagine the workflow: a compliance officer updates a regulatory constraint. A product manager adds a business rule. An engineer refines a concurrency pattern. Each commits to the spec tree. The pipeline generates the code, runs the tests, and deploys. No one wrote code. No one reviewed code. No one merged code. The entire team worked in specs — the artifact they all understand — and the software took care of itself.
