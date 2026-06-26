# The model is part of the spec

## The question

When you generate code from a specification, where does the
specification end?

The obvious answer is: at the edge of the spec tree. Everything
the agent needs is in the chain — the inherited constraints, the
declared dependencies, the target node's own text. If it is not
in the chain, it does not govern the artifact. That is the whole
premise of confinement, and it is true.

But it is not complete. Consider what the spec does *not* say. It
does not explain how to write a `for` loop. It does not define
the syntax of the language, the semantics of the standard library,
or what a hash map is. The spec for a payment handler does not
teach the agent to program, any more than a memo to a colleague
teaches them to read. We assume all of that. We build the entire
methodology on the assumption that the agent already knows how to
turn intent into working code in a real language.

So the specification has two halves. One is written, versioned,
and reviewed: the spec tree. The other is unwritten, and lives in
the weights of the model: everything we never had to say because
the agent already knew it. The second half is invisible, but it
is load-bearing. Remove it and nothing generates.

This is not a flaw to be engineered away. It is the foundation.
The reason AI code generation is worth doing at all is precisely
that we *don't* have to specify how to program. The unwritten half
is the leverage. The interesting question is not how to eliminate
it — it is what follows from accepting that it exists.

## Hidden dependencies

Every node declares its dependencies: `depends_on`, `input`, the
inheritance path to the root. Those are the dependencies we can
see. But there is another kind the framework cannot name.

When an agent generates a correct artifact from an underspecified
node, the gap between what the spec said and what the code needed
was filled by the model. That fill is a dependency — the artifact
depends on it as surely as it depends on any `depends_on` entry —
but it appears in no frontmatter, contributes to no chain hash,
and triggers no staleness. It is a hidden dependency, and its
provider is not another node. Its provider is the model's training.

Most of the time this is exactly what we want. A node that says
"validate the transfer amount is positive" does not need to
specify that integers can be compared with `>`. The model supplies
that, correctly, every time, and the spec stays readable. The
hidden dependency is doing useful work: it is the compression that
lets a spec be intent instead of code.

The hidden dependency only becomes visible when it fails to be
satisfied. And the cleanest way to make it fail is to change the
thing that was satisfying it.

## Changing the model exposes them

A real case from the framework's own development. A spec said to
match file patterns, but did not say which standard-library
function to use. The language offered two — one platform-dependent,
one not. The agent picked the platform-dependent one. The code was
correct on the machine it was generated on and wrong elsewhere.

The fix was three words added to the spec, prescribing the
portable function. After that, every generation was correct. The
incident is usually told as a lesson about diagnosis: trace the
failure, fix the spec, the fix is permanent. But look at it from
the other direction. Before those three words, the choice of
function was a hidden dependency — the spec delegated it to the
model's judgment, and the model's judgment happened to be wrong
on one axis. The spec did not change to *add* a requirement. It
changed to *move* a requirement from the unwritten half to the
written half.

Now run the thought experiment. Suppose the spec had never been
fixed, and instead the model were swapped for a different one. A
second model might pick the portable function by default — and the
artifact would silently become correct, with no spec change at
all. Or it might pick differently in a node that had been working
for months, and an artifact that was correct would silently need
attention. The written spec is identical in both cases. What
changed is the unwritten half.

This is the precise sense in which **the model is part of the
specification.** Not a tool that reads the specification — a
component of it. The artifact is a function of the chain *and* the
model, and we have only been writing down one argument.

## What this is not

It would be easy to read this as a confession of fragility, so it
is worth being exact about what does and does not follow.

It does not mean the spec is worthless without the model — every
specification in history assumed a competent reader. A blueprint
assumes someone who can read a blueprint; a recipe assumes someone
who can cook. Assuming competence in the executor is not a defect
of specifications. It is what makes them shorter than the thing
they specify.

It does not mean generation is unrepeatable in practice. Today the
subagent pins the model and the effort level — the immediate
reason is token cost, but the same pin serves repeatability. A
fixed model is a fixed unwritten half. Under a pinned model, the
hidden dependencies hold still, and the spec converges against them
the same way it converges against everything else: each failure
exposes a gap, each fix closes it, and the closed gaps stay closed.

And it does not mean a model swap is a catastrophe. This is where
we move from what we have seen to what we expect. We have not run
a large migration between models and measured the fallout — so
what follows is intuition, offered as intuition. Our expectation is
that a reasonably capable model, plus a well-developed spec, plus
unit tests with real coverage, makes a model swap a *manageable
event* rather than a reset. The reasoning is that the three
combine to shrink the surface where the unwritten halves of two
models disagree. A capable model has fewer wrong defaults to begin
with. A mature spec has already moved its most load-bearing hidden
dependencies into the written half — that is what maturing *is*.
And good tests turn whatever disagreement remains into the kind of
failure the methodology is built to handle: loud, located, fixable
at the source.

That last point is the one we hold most confidently, because it
follows from confinement rather than from optimism. When a swapped
model's unwritten half disagrees with the spec, a confined agent
cannot paper over the gap with invented context — it has none to
invent. It generates from the chain it has, and where the chain
relied on a hidden dependency the new model does not supply, the
artifact fails a test. The disagreement surfaces as a red test
pointing at a specific node, not as a subtle wrongness discovered
in production months later. Confinement does not prevent the
coupling. It changes the *register* of the coupling's failures
from silent to noisy — and noisy is survivable.

## Convergence, and what a swap does to it

A spec converges. Each generation that fails teaches the spec
something, and a fix that lands never has to be relearned. Over a
project's life the written half grows and the unwritten half it
leans on shrinks — not to zero, never to zero, but toward the
core of genuinely shared competence that no specification should
have to state.

A model swap reopens part of that convergence. But "part" is the
word that matters, and it is why this is not a discouraging
picture. The swap does not return the spec to zero. It reopens
only the subset where the two models' unwritten halves disagree —
and a more capable successor tends to *infer more* of the same
spec, not less, so the disagreements that reopen are, on balance,
fewer than the ones that came before. The convergence is not lost.
It is perturbed, and re-converges from a point far above the start.

You do not discard progress because it is partial. A spec that has
converged against one model is genuinely more complete than it was
empty — and a swap costs the delta between two models, not the
whole accumulated investment. That is the ordinary shape of an
engineering dependency. You pin your compiler version; upgrading it
is an event with a diff, occasionally requiring you to adjust code
that leaned on the old version's behavior; you do it deliberately,
not never. The model is a dependency of exactly this kind. We have
simply not been in the habit of naming it as one, because it is the
first dependency in the history of software that arrives already
knowing how to program.

## Why natural language works at all

There is a larger claim hiding inside all of this, and it is worth
bringing into the open, because it is the reason the methodology
can be what it is.

Specification-driven development is old, and for most of its history
it meant formal notation — Z, VDM, structured analysis. The notation
was not chosen for its beauty. It was chosen because the executor was
not intelligent. A compiler fills in nothing; it does exactly what it
is told, so everything had to be told, formalized, exhaustively, with
no gap left for judgment because there was no judgment on the other
end. Natural language was disqualified for precisely the reason it is
natural: it is ambiguous, and a dumb executor cannot resolve
ambiguity. Precision had to be infinite and self-contained, and only
mathematics offered that.

That objection was correct against the executor it assumed. It is no
longer the executor we have. The model resolves ambiguity — and
resolves it the way a competent programmer reading a terse spec
always has. So the residual ambiguity of natural language and the
ambiguity a human engineer was always trusted to settle become the
same ambiguity, with the same kind of resolver on the far side. The
formalism existed to eliminate something that now has someone to
handle it. The specification no longer has to carry infinite,
self-contained precision, because it is not working alone. It carries
the half a competent reader cannot supply, and delegates the rest.

This is the same fact as "the model is part of the spec," seen from
the other side. The model being part of the specification is exactly
what lets the specification be prose. Formal notation put *all* the
precision in the written half and leaned on no intelligent executor —
expensive to write, excluding to read, but portable and stable: a Z
schema means the same against any reader who can parse Z. Natural
language puts part of the precision in the unwritten half and leans on
the model — cheap, readable by the compliance officer, participable —
and coupled to the model, which is the very debt this article names.
You do not get both. The prose a domain expert can review is bought
with the executor-independence the notation had. Choosing natural
language *is* choosing to couple to the model. They are one decision,
not two.

Said plainly: the coupling is not an accident the methodology tolerates.
It is the price of the participation the whole approach is built to
enable. That reframes the debt. It is not a latent flaw discovered
after the fact — it is what was paid, knowingly, for everything the
choice of natural language buys.

None of this makes formal notation obsolete. It is a trade, not a
victory. Where an external force pays the cost of rigor and stability
outweighs participation — aviation, medical devices — exhaustive
formal precision and its independence from any executor are still the
right tools. What changed is not that natural language won. It is that
AI reopened natural language as a viable option in the vast territory
where the economics had always ruled it out.

## Where this leaves us

If the model is part of the spec, two things follow that are worth
sitting with.

The first is a discipline. The boundary between the written and
unwritten halves is a choice, even when we make it by default.
Every time we leave something unsaid, we are delegating it to the
model — sometimes wisely, because it is shared competence, and
sometimes by accident, because we did not notice we were relying on
a particular model's particular judgment. Maturing a spec is, in
large part, the work of moving the *accidental* delegations into
the written half while leaving the *deliberate* ones where they
belong. The skill is telling them apart.

The second is a frame. We have been treating "client-agnostic
format, specific orchestration" as a statement about tooling — the
spec files port, the orchestration assumes one client. But the
deeper version of that boundary is not about tooling at all. The
format is portable; the *content's reliance on a model* is not, at
least not perfectly. The line between what ports and what does not
runs straight through the spec, separating its two halves. Naming
that line does not move it. But it tells you what you are actually
carrying when you carry a spec to a new model — and what you should
expect to re-converge when you get there.

This is early. We are confident about the mechanism — confinement
makes the coupling's failures loud, and that much we have watched
happen. We are reasoning, not reporting, about the size of a swap,
and we would rather say so than dress intuition as evidence. The
question we find worth thinking about, and worth handing to anyone
else building this way, is not whether the model is part of the
spec. It is. The question is how to specify well *given* that it
is — how to choose, deliberately, which half of the specification
each thing belongs in.

One last way to see the whole shape. AI generating code is a signal
with noise. The model knows how to program, and most of what it
produces is the signal we want; but it also drifts, picks wrong
defaults, resolves an ambiguity differently than we would have. The
methodology is the apparatus that extracts the signal from that
source — not by pretending the source is clean, but by being built
around the fact that it is not. The spec is our model of what the
signal should be. The tests are the measurements that correct the
estimate. Confinement keeps unmodeled noise from leaking in. And
convergence is the apparatus settling over time.

The metaphor has one edge worth keeping sharp, because it is the same
edge as this article's argument. A filter does not cancel a bias by
averaging more readings — a consistent wrong default is not noise that
repetition removes, which is exactly why regenerating against it only
reproduces it. When the measurement reveals a systematic drift, you do
not take more samples. You update the model. Maturing a spec is that
update: the unwritten half is our estimate of what the executor
supplies, and every failure that traces back to it is a correction to
the estimate. Swapping the model changes the source. The apparatus is
how we re-converge on the signal when it does.