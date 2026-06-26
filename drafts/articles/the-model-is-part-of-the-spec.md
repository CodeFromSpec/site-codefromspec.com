# The model is part of the spec

## The question

When you generate code from a specification, where does the
specification end?

The obvious answer is: at the edge of the spec tree. Everything the
agent needs is in the chain — the inherited constraints, the declared
dependencies, the target node's own text. If it is not in the chain,
it does not govern the artifact. That is the whole premise of
confinement, and it is true.

But it is not complete. Consider what the spec does *not* say. It does
not explain how to write a `for` loop, define the syntax of the
language, or say what a hash map is. The spec for a payment handler
does not teach the agent to program, any more than a memo teaches a
colleague to read. We assume all of it. The entire methodology rests
on the assumption that the agent already knows how to turn intent into
working code in a real language.

So the specification has two halves. One is written, versioned, and
reviewed: the spec tree. The other is unwritten, and lives in the
weights of the model — everything we never had to say because the
agent already knew it. The second half is invisible, and load-bearing.
Remove it and nothing generates.

This is not a flaw to engineer away. It is the foundation. The reason
AI code generation is worth doing at all is precisely that we *don't*
have to specify how to program. The unwritten half is the leverage.
The question is not how to eliminate it — it is what follows from
accepting that it exists.

## Hidden dependencies

Every node declares its dependencies: `depends_on`, `input`, the
inheritance path to the root. Those are the dependencies we can see.
There is another kind the framework cannot name.

When an agent generates a correct artifact from an underspecified
node, the gap between what the spec said and what the code needed was
filled by the model. That fill is a dependency — the artifact relies
on it as surely as on any `depends_on` entry — but it appears in no
frontmatter, contributes to no chain hash, and triggers no staleness.
Its provider is not another node. It is the model's training.

Most of the time this is what we want. A node that says "validate the
transfer amount is positive" need not specify that integers can be
compared with `>`. The model supplies that, correctly, every time, and
the spec stays readable. The hidden dependency is the compression that
lets a spec be intent instead of code.

It only becomes visible when it fails to be satisfied. And the
cleanest way to make it fail is to change the thing that was
satisfying it.

## Changing the model exposes them

A real case from the framework's own development. A spec said to match
file patterns, but not which standard-library function to use. The
language offered two — one platform-dependent, one not. The agent
picked the platform-dependent one. The code was correct on the machine
it was generated on and wrong elsewhere.

The fix was three words added to the spec, prescribing the portable
function. The incident is usually told as a lesson about diagnosis:
trace the failure, fix the spec, the fix is permanent. But look at it
from the other direction. Before those three words, the choice of
function was a hidden dependency — the spec delegated it to the model's
judgment, and that judgment was wrong on one axis. The spec did not
change to *add* a requirement. It changed to *move* one from the
unwritten half to the written half.

Now run the thought experiment. Suppose the spec had never been fixed,
and instead the model were swapped. A second model might pick the
portable function by default — and the artifact would silently become
correct, with no spec change at all. Or it might pick differently in a
node that had worked for months, and an artifact that was correct
would silently need attention. The written spec is identical in both
cases. What changed is the unwritten half.

This is the precise sense in which **the model is part of the
specification.** Not a tool that reads it — a component of it. The
artifact is a function of the chain *and* the model, and we have been
writing down only one argument.

## What a swap actually costs

It would be easy to read this as a confession of fragility, so it is
worth being exact about what follows.

It does not mean the spec is worthless without the model. Every
specification in history assumed a competent reader: a blueprint
assumes someone who can read a blueprint, a recipe someone who can
cook. Assuming competence in the executor is what makes a spec shorter
than the thing it specifies.

It does not mean generation is unrepeatable. Today the subagent pins
the model and the effort level — the immediate reason is token cost,
but the same pin serves repeatability. A fixed model is a fixed
unwritten half: the hidden dependencies hold still, and the spec
converges against them like it converges against everything else. Each
failure exposes a gap, each fix closes it, and closed gaps stay
closed. Over a project's life the written half grows and the unwritten
half shrinks — never to zero, but toward the core of shared competence
no spec should have to state.

And it does not mean a swap is a catastrophe. Here we move from what we
have seen to what we expect, and we offer it as intuition, not
evidence. A swap reopens part of the convergence — but only *part*: the
subset where two models' unwritten halves disagree. It does not return
the spec to zero. A spec that converged against one model is genuinely
more complete than an empty one, and a swap costs the delta between two
models, not the whole investment. A more capable successor tends to
*infer more* of the same spec, not less, so the reopened disagreements
are, on balance, fewer than those that came before. This is the
ordinary shape of an engineering dependency: you pin your compiler,
and upgrading it is an event with a diff, done deliberately, not never.
The model is a dependency of exactly that kind — the first in the
history of software that arrives already knowing how to program.

The one point we hold with confidence is the one that follows from
confinement rather than optimism. When a swapped model's unwritten half
disagrees with the spec, a confined agent cannot paper over the gap
with invented context — it has none to invent. It generates from the
chain, and where the chain leaned on a hidden dependency the new model
does not supply, the artifact fails a test. The disagreement surfaces
as a red test pointing at a specific node, not as a subtle wrongness
found in production months later. Confinement does not prevent the
coupling. It changes the *register* of its failures from silent to
noisy — and noisy is survivable.

## Why natural language works at all

There is a larger claim hiding in all of this, and it is the reason the
methodology can be what it is.

Specification-driven development is old, and for most of its history it
meant formal notation — Z, VDM, structured analysis. The notation was
not chosen for beauty. It was chosen because the executor was not
intelligent. A compiler fills in nothing; everything had to be told,
formalized, exhaustively, with no gap left for judgment because there
was no judgment on the other end. Natural language was disqualified for
the very reason it is natural: it is ambiguous, and a dumb executor
cannot resolve ambiguity. Precision had to be infinite and
self-contained, and only mathematics offered that.

The objection was correct against the executor it assumed. It is no
longer the executor we have. The model resolves ambiguity — and
resolves it the way a competent programmer reading a terse spec always
has. The residual ambiguity of natural language and the ambiguity a
human engineer was always trusted to settle become the same ambiguity,
with the same kind of resolver on the far side. The formalism existed
to eliminate something that now has someone to handle it. The spec no
longer has to carry infinite, self-contained precision, because it is
not working alone. It carries the half a competent reader cannot
supply, and delegates the rest.

This is the same fact as "the model is part of the spec," seen from the
other side — and it is what lets the spec be prose. Formal notation put
*all* the precision in the written half and leaned on no intelligent
executor: expensive to write, excluding to read, but portable and
stable — a Z schema means the same to any reader who can parse Z.
Natural language puts part of the precision in the unwritten half and
leans on the model: cheap, reviewable by the compliance officer,
participable — and coupled to the model, the very debt this article
names. You do not get both. The prose a domain expert can review is
bought with the executor-independence the notation had. Choosing
natural language *is* choosing to couple to the model; they are one
decision, not two. The coupling is not an accident the methodology
tolerates. It is the price of participation, paid knowingly.

None of this makes formal notation obsolete. It is a trade, not a
victory. Where an external force pays the cost of rigor and stability
outweighs participation — aviation, medical devices — exhaustive formal
precision and its independence from any executor are still the right
tools. What changed is not that natural language won. It is that AI
reopened it as a viable option across the territory where the economics
had always ruled it out.

## Where this leaves us

If the model is part of the spec, the boundary between the written and
unwritten halves is a choice — even when we make it by default. Every
time we leave something unsaid, we delegate it to the model: sometimes
wisely, because it is shared competence, and sometimes by accident,
because we did not notice we were relying on one model's particular
judgment. Maturing a spec is, in large part, moving the *accidental*
delegations into the written half while leaving the *deliberate* ones
where they belong. The skill is telling them apart.

It also redraws a line. "Client-agnostic format, specific
orchestration" sounds like a claim about tooling — the spec files port,
the orchestration assumes one client. But the deeper boundary is not
about tooling. The format is portable; the content's reliance on a
model is not, at least not perfectly. The line between what ports and
what does not runs straight through the spec, separating its two
halves. Naming it does not move it — but it tells you what you are
actually carrying to a new model, and what to expect to re-converge
when you get there.

One last way to see the whole shape. AI generating code is a signal
with noise: the model knows how to program, and most of what it
produces is the signal we want, but it also drifts, picks wrong
defaults, resolves an ambiguity differently than we would have. The
methodology is the apparatus that extracts the signal — not by
pretending the source is clean, but by being built around the fact
that it is not. The spec is our model of what the signal should be. The
tests are the measurements that correct the estimate. Confinement keeps
unmodeled noise out. Convergence is the apparatus settling over time.

The metaphor has one edge worth keeping sharp, because it is this
article's argument. A filter does not cancel a bias by averaging more
readings — a consistent wrong default is not noise that repetition
removes, which is exactly why regenerating against it only reproduces
it. When the measurement reveals a systematic drift, you do not take
more samples. You update the model. Maturing a spec is that update: the
unwritten half is our estimate of what the executor supplies, and every
failure that traces back to it corrects the estimate. Swapping the
model changes the source. The apparatus is how we re-converge on the
signal when it does.