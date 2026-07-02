# The executor is not a compiler

### Why almost everything said about spec-driven development is imagining the wrong machine — and what changes when you switch the lens

---

There's a promise that's been circulating for about a year now: the specification becomes the source of truth, code becomes a derived artifact, and software development turns into the discipline of writing specs instead of writing code. The name is spec-driven development (SDD), and its evangelist version — the one you find in blog posts, papers, and on the tool vendors' landing pages — is seductive and, for the most part, wrong. Not wrong on the facts. Wrong about the *machine* it imagines on the other side of the spec.

This essay is an attempt to reframe the debate around a single error of origin, and to show what survives — and what collapses — once you correct that error. It's not a manifesto for or against SDD. It's a scalpel. And like any piece written for people who've already been burned by hype, it carries its own counterexamples along: every thesis comes with the crack that threatens it.

A methodological warning first, because it's the most honest weapon we have and it'll be used throughout.

## The fair ruler

Every time we point at a failure mode of SDD-with-AI, the obligatory question is: **does the lone developer, without a spec, without AI, suffer from this too?**

If the answer is yes, the problem isn't SDD's — it's software's. Charging it to SDD is false advertising in reverse, and the skeptical reader smells it from a mile away. Half of what gets written against AI-assisted development dies on this ruler: they're eternal costs of engineering being billed to a new paradigm just because it happened to be nearby. We'll apply the ruler without mercy — including against our own arguments.

## The wrong machine

Start with the picture SDD discourse has in its head, even when it won't admit it: a compiler.

The whole idea of "the spec is the source, the code is disposable output" is the relationship between source code and binary. You edit the `.c`, run the build, get the executable, and you *never* edit the executable — if you need to change behavior, you change the source and recompile. It's clean, it's correct, and it works for one specific and frequently forgotten reason: **the compiler is a function.** Same input, same output, always. `gcc -O2` over the same file yields the same binary. It's that determinism that makes it coherent to throw the binary away on every build — there's nothing in it worth preserving, because it's 100% reconstructible from the source.

SDD imported that entire mental model. And with it, without noticing, it imported the premise that was only valid for the binary: **regenerate from the spec.**

The trouble is that SDD's executor is not a compiler. It's an LLM. And an LLM is not a function. Same spec, same model, different systems. Non-determinism is the defining property of the creature, and it breaks precisely the foundation on which "regenerate from the spec" made sense.

This is not a technical detail. It's the error of origin from which nearly all the others descend. And it's corrected by swapping one picture for another.

## The scalpel: the LLM is a dev, not a compiler

The right analogy was never the compiler. It's a developer, and the human engineer in charge is something closer to a tech lead.

The moment you make that swap, strange things happen — the good ones first. The most technical objection to SDD-with-AI, non-determinism, simply stops being scary. Nobody ever expected a dev to be a function. You never looked at your senior and thought "I need him to produce the exact same code both times." Output variance, which under the compiler lens was nearly fatal, under the dev lens is the normal condition of working with people. And the cost of verification — which looked like a new conceptual abyss — turns into a known problem: code review, confidence calibration, breaking work into reviewable chunks, giving context up front instead of correcting after the fact. That's the tech lead's craft, and software engineering has decades of practice (good and bad) with it. The cost doesn't vanish; it stops being mysterious.

But the lens is worth less for what it comforts and more for *where it leaks*, because the leak points are precise and informative. The LLM breaks the dev analogy at specific places — and always in the parts that were *cheap* in the human dev:

A real dev **emits an uncertainty signal.** He hesitates, asks the question, hands over something visibly half-finished when he didn't understand. Half of a tech lead's instinct — "this one's drowning in that task" — is calibrated on those signals. The LLM removes them: it delivers confident, plausible, and complete, whether it understood or not. You lose, for free, the cheapest radar there was.

A real dev **learns.** You teach the house convention once and confidence compounds — six months later you don't even review what he does in the module he owns. The LLM (today) doesn't carry that across sessions. And here there's a useful rereading of the entire SDD apparatus: memory bank, constitution, `AGENTS.md`, rules files — all of it is a prosthesis for the learning a dev would do internally. The spec stops being "source code for a compiler" and becomes the permanent onboarding document you'd give a competent contractor with amnesia every morning. That's what SDD is really trying to build: forcing a stateless collaborator to behave like an employee with tenure.

A real dev **has skin in the game.** He carries the bug he shipped to production, feels the 3 a.m. page, has a reputation. Much of the bias of his variance *toward the product's intent* comes from there. The LLM has no consequence landing on it at all.

Add those three up, and you have the profile of the collaborator SDD actually deals with: someone who has read more code than any senior on the team, holds more context than any human, never tires, and has no ego in review — while at the same time being less reliable than your junior, emitting no uncertainty, not learning from you, and answering to nothing. An alien colleague, simultaneously wiser than your senior and more unstable than your intern. "Dev, not compiler" is the best available lens. But the dev in question has no human counterpart, and managing it may demand practices that neither the tech-lead playbook nor the toolchain playbook handed us ready-made.

## The cleanup: SDD is a gradient, not an identity

With the lens swapped, we can sweep the false debate that dominates the discussions off the table.

The question that structures nearly every SDD conversation is "code or spec?", as if you had to pick one regime and live in it. There are even tribes: the one that declares itself spec-driven, the one that "doesn't trust it, writes code," and the one that "doesn't use AI." Each has picked a fixed altitude on the abstraction axis and defends it as identity.

It's a category error. You don't trade fine control for natural-language expressiveness — you gain the natural on top and keep the fine underneath. It's strictly a superset of capability. The exact analogy is writing `asm` inside a `.c`: a niche feature, laborious, that almost nobody uses — and whose mere existence is what makes it honest to trust C the rest of the time. You don't program in C anxious about "what if I need a specific instruction?", because you know the valve is there. The hatch isn't worth its frequency of use; it's worth enabling confidence in the level above.

And here's the strong version of the argument: **a good programmer already operates this way, and always has.** You write names and comments (prose of intent), pseudo-code when sketching, high-level declarative code where you can, and drop down to bit-twiddling only in the hot path. Grading abstraction within a single artifact *is* the central competence of software engineering. What AI changes isn't inventing the gradient — it's extending its top end, adding a rung above what the highest language offered, where "order these requests by relevance, treating ties the way the rest of the system does" becomes something executable. SDD isn't a new paradigm fighting with code. It's the same old abstraction gradient, with one more floor at the top.

An honest caveat, because the gradient isn't uniform. When you drop down to `asm` in C, both levels are function-executors and the seam between them is a specified ABI — mixing levels is free *and* cheap. In SDD, you mix a function-executor (the literal code block you wrote) with a non-function-executor (the prose the LLM translates with variance), and the seam between them is the LLM itself deciding how to wire the two together. Every step up the gradient trades **authoring cost for verification cost**: prose is cheap to write and expensive to verify; literal code is expensive to write and cheap to verify. The freedom to choose the altitude is real. What isn't free is that the optimal altitude shifts per piece, depending on which of the two costs dominates there. Choosing well stops being style and becomes a new engineering skill: reading, piece by piece, whether the bottleneck will be you understanding/writing or you trusting/verifying — and going up or down accordingly.

What dissolves the polarization isn't finding the right point in the middle. It's realizing that no fixed point is the answer, and that the competence *is* the mobility. The tribes didn't choose better; they chose to stop choosing. The gradient asks for the opposite.

## What tests become when the executor isn't a function

If every step up trades authoring for verification, then the test is the instrument that makes the verification cost payable. Without it, going up a level isn't a trade — it's pushing the cost somewhere it stays invisible until production. The test is what brings that cost back into the loop, where you can still act on it. In the classic regime, the test protected against your future regression; in the AI regime, it's the only artifact that closes the gap the level-jump opened *in this generation*. It went from safety net to vital organ.

But there's a failure mode that dominates real usage and that almost nobody names: **asking the AI to generate the code and the tests in the same pass.** This collapses the oracle and the output into the same non-function executor. The generated test doesn't embody your intent — it embodies the reading the model made of your intent, the same reading that produced the code. It verifies that the model's interpretation is consistent with itself. It passes with 90% coverage and protects you from nothing, because judge and defendant are the same person. It's theatrical verification, and it's *worse* than having no test, because it buys confidence without delivering safety.

From this comes a principle: **a test's verification value is proportional to the causal independence between it and the code it tests.** Test written by you, beforehand, from intent: maximum independence. Test the AI generates from the same spec, in a separate pass: partial independence. Test the AI spits out alongside the code: zero independence. It's a gradient — again — except the hype is all piled up at the useless end, because the useless end is the cheapest and the one that produces the prettiest metric.

Now the fair ruler, against our own principle. The developer who writes code and test alone, reading the same requirement he misunderstood, produces *exactly* the same blind spot — his test confirms his mistaken understanding, all green. That's the daily life of everyone who has ever programmed. So "pure causal independence" was never the baseline of real development; it's rare, it actually shows up only when *someone else* writes the test, and even then partially. The strong version of the objection ("SDD suffers from correlated error, therefore it's flawed") dies on the ruler. What survives is the weak version, and it's the true one: the human has *cheap, ambient independence buffers* that the AI flow removes without replacing. When you write the test ten minutes after the code, the "you of ten minutes later" has reread, changed context, sometimes catches your own error. Generating code and test in the same pass collapses that interval. It's not a new failure category — it's the erosion of a buffer the human flow had for free. Real cost, marginal, recoverable: force the interval, write from different angles, use a second agent adversarially against the spec itself.

And there's a refinement that fixes a common level error. If the spec is the source of truth, then the test code is *also* output — it descends from the same generation boundary as the functional code. It makes no sense to demand it be written by hand. Independence has to live one layer above: the **behavior spec** (condition, action, expected result) needs to be causally independent from the **functional spec**. Two specs, from different angles — one says how to do it, the other says how to recognize it was done right — both translated to code by the same process. The oracle isn't the test code; it's the test spec. And this has a handy advantage: the test-spec → test-code projection is much easier to verify by reading than the functional-code one. A translated `given/when/then` you check at a glance. Of the two generation branches, the test one is the more reliable translation — which is exactly where you want to anchor the arbitration.

## The thesis: locality matters more than generation quality

We arrive at the point that reorganizes everything, and it begins by undoing a premise the entire field carries without examining.

**Spec-as-source does not require regeneration from scratch.** The real definition of the regime is only the boundary: no human edit lives downstream of generation. Nothing in it forces the translation to be stateless. `spec + Δspec → code + Δcode` satisfies the boundary perfectly and preserves the sedimentation, because code not affected by the change simply isn't touched. The field slid from "code is derived from the spec" to "code is *re-derived whole*," and those are different things. The first is the definition. The second is the residue of the compiler fantasy — the build-from-scratch that made sense for the binary (which doesn't mature) and is disastrous for code (which does).

Why does this matter so much? Because real software isn't generated. It's *sedimented*. Every fixed bug is a line of knowledge deposited into the rock — a case reality taught you and that nobody would have specified a priori, because nobody knew it existed until production showed them. The bug → test → fix cycle is the main channel through which a system learns things that were in nobody's head at design time. Regeneration from scratch is structural amnesia: it throws the rock away and re-deposits it from a spec that never contained those cases — because if it had, they wouldn't have been bugs.

And here's the mechanism, which is where the dev lens finally pays for everything. What makes a junior useful isn't that he gets it right — it's that **the cost of his error is bounded by the locality of the correction.** He errs in a function, you point it out, he fixes *that function*, and the other ninety-nine things that already worked keep working. A junior who responded to every piece of feedback by rewriting the whole file would be unemployable — not for writing worse, but because each correction would have total blast radius: fixing one thing would risk the ninety-nine. The variance that was tolerable confined to a function becomes intolerable spread across the file.

That's exactly what regeneration from scratch does. It takes a high-variance executor — acceptable while contained — and removes the containment that made the variance acceptable. LLM non-determinism was never fatal in itself. It's fatal when multiplied by the blast radius. Regeneration from scratch is the multiplier. Incremental diff is the limiter.

Hence the thesis, in its honest statement: **the viability of SDD-with-AI doesn't depend on lowering the model's variance — it depends on confining the radius in which that variance can act.** It's not about generation quality. It's about generation locality. The same AI, with the same variance, is the junior who corrects locally and matures the system in one regime, and the impossible collaborator who rewrites everything and reintroduces ten bugs to fix one in the other.

This explains, for free, the phenomenon that most frustrates practitioners: **why demos impress and real usage disappoints.** A demo is greenfield. There's no sedimentation to destroy; total blast radius is harmless because there is no "ninety-nine correct things" yet — everything is new. The regeneration-from-scratch regime is *invisible* in a demo, precisely because the demo has no past to preserve. Real usage is brownfield: sedimentation exists, and total blast radius meets the ninety-nine correct things and breaks them. The architecture most seductive in a demo is the most destructive in production — and the demo, by construction, is incapable of revealing that. It's a perfect sampling bias.

Under this light, the three-level taxonomy SDD texts love — spec-first, spec-anchored, spec-as-source, presented as an axis of increasing rigor — largely dissolves. It's an artifact of assuming regeneration from scratch. Once code is durable and evolves by diff, "incremental spec-as-source" and "spec-anchored" converge to the same object seen from two angles; the difference that remains is only the *change-origination policy* — where the change is born and with what discipline it returns to the source — and that's a continuum, not three boxes.

## Where the thesis strains (because it does)

A text for skeptics that doesn't show its own cracks doesn't deserve the skeptic. Three, of the sharpest.

**Causal independence proves too much.** By our own ruler, the ideal test is always the one written by hand by whoever holds the intent — which is just saying "don't use AI for the tests," and that pushes the entire verification cost back onto the human, killing much of the gain of going up a level. If, to trust the generation, I have to write the whole oracle by hand, how much of the trade is left? The less defeatist answer is that writing the oracle is often cheaper and more stable than writing the implementation (the "what" is smaller and changes less than the "how"). But that's an empirical bet per domain, not a law. Where correct behavior is hard to state and easy to recognize, the oracle is *more* expensive than the code, and there SDD flips from advantage to burden.

**The legacy migrates layers; it doesn't evaporate.** The spec-as-source promise was "no technical debt by construction." But if maturation is captured as spec — the only way for it to survive evolution — then the spec suite grows monotonically and is never regenerated. It becomes the legacy. You traded a pile of sedimented code for a pile of sedimented specs, which accumulate, conflict, age, and contradict each other. Maybe better, because specs are more readable. But the "no debt" fantasy dies: the debt changed floors.

**Reconciliation is the `sleep(10) // do not remove` waiting to happen.** When production is on fire, the sane path is to put your hands into the generated code now — deliberately violate the spec-as-source boundary — put out the fire, and *then*, cold, promote the lesson to spec and regenerate the block incrementally, reconciling the boundary and paying the debt. Beautiful on paper. But "hands in now, promote to spec later" has exactly the risk profile of `sleep(10)`: the "later" is the part that never comes. The one difference — and it matters — is that here the divergence is *detectable*: the hand-edited code diverges from what the spec would generate, and a tool can shout "you have unreconciled edits from forty days ago." Spec-as-source doesn't guarantee the debt gets paid; it makes non-payment visible, which is strictly better than the invisible `// do not remove`. But the advantage is *entirely conditional* on the tool policing reconciliation. Without that policing, the regime degenerates back into scattered patches — now with the added lie of "but we're spec-driven."

That last crack suggests, incidentally, that the central artifact of a viable spec-as-source may be neither the spec nor the generator, but the **reconciler**: the thing that measures the distance between the code that exists and the code the spec would generate, and turns that distance into pressure. Nobody sells that as the heart of SDD. Everybody sells the generation — which is the easy part.

## The case that respects the ruler: why Simulink stays

If "mature systems gravitate away from regeneration-from-scratch" were a law, Simulink would be a fatal counterexample: automotive control systems are extremely mature and *stay* in the most rigorous spec-as-source there is — the model is the source, the generated C is certified, and nobody edits it.

But Simulink doesn't refute the thesis; it reveals the exact condition under which spec-as-source survives maturation. In safety-critical, **no quick-and-dirty fix is permitted.** Every change already has to pass formal re-verification, no matter what. The slow path of spec-as-source — stop, promote the lesson to model, regenerate, re-verify — imposes no new cost there; it aligns with a cost certification already demanded. And the domain helps: control systems *are* naturally block diagrams and state machines, so the model is genuinely a higher abstraction than the code, not prose pretending to be.

The condition, then, is statable: **spec-as-source wins exactly where the environment already forbids the dirty shortcut** — where the discipline of "every change re-verified" is imposed by the domain, not by the tool. Outside those domains, the dirty shortcut is too good to give up, and the regime that permits the shortcut and then lets you clean it up (the anchored one, or spec-as-source with a good reconciler) wins. A greenfield web app is the opposite of Simulink: the dirty shortcut is cheap and ubiquitous, formal re-verification doesn't exist, and there rigorous spec-as-source doesn't take — exactly as the condition predicts.

## The experiment

None of the above is falsifiable in the strict sense, and it would be dishonest to dress up as science what is lens and hypothesis. "The LLM is a dev, not a compiler" and "SDD is a gradient" are reframings — you don't refute them, you judge whether they make you see more. "Regeneration from scratch explains demo-versus-production" is retrospective explanation: it's worth its parsimony, not an experiment.

But the locality thesis generates a prediction that comes close to testable, and it's worth stating as an invitation to refutation:

> For systems with a past (brownfield), a generator that produces *smaller diffs* for a given spec change will result in fewer regressions than a generator that produces code of *superior isolated quality* but with larger diffs.

The experiment: take N real changes over an existing system, two generators, measure regressions introduced per change, controlling for the quality of the code generated in isolation. If the "better code, bigger diff" generator wins, the thesis takes a beating. The devil lives in "fewer regressions" (needs an agreed definition) and in "controlling for quality" (easier said than done) — but the form is honest: here's the claim, and here's how you'd kill it.

The practical corollary doesn't wait for the experiment. If locality matters more than generation quality, the metric an SDD tool should optimize is not "how good is the generated code," but **how small is the diff it produces for a given spec change** — *diff minimality* as a first-class property. A generator that produces ten percent worse code, with ten times smaller diffs, is better for any system that has a past. Nobody is building for that metric. Everybody is building for the demo.

## Close

Spec-driven development is neither revolution nor fraud. It's the same old abstraction gradient, with a new floor at the top, operated by a collaborator of unprecedented profile — wiser than your senior, more unstable than your intern, with no learning curve and no accountability. Almost everything said wrong about it descends from a single image: that on the other side of the spec there's a compiler. There's a dev. And devs don't have the binary rebuilt from scratch on every change — they fix what they got wrong and leave the rest alone.

The question that organizes the field, then, isn't "code or spec?", nor "which level of rigor?". It's: **how confined is this collaborator's variance by where you let it act?** Answer that well, piece by piece, and you're doing engineering. Answer it by picking a fixed altitude and calling it identity, and you're just wishing — with extra steps.