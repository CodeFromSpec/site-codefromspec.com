# The two specs

Every software project has two specifications, and only one of
them is ever written down.

The first is the intent itself: what the people who need the
system would actually accept, including everything they have not
thought to say and everything they will only discover when the
software misbehaves in front of them. Nobody possesses this
specification as text. It is held partially, by several people at
once, revised on contact with reality, and never finished —
finite people writing in finite time do not close every degree of
freedom of a system's behavior.

The second is the document called "the spec": a finite encoding
of some of that content. And like any text, it is written for an
audience. What to state and what to leave silent, which rules to
put first, how forcefully to word a constraint — every one of
those choices is made, consciously or not, against a reader.

## Written for an audience

Hand a task to a colleague who has spent ten years in the
codebase and the spec is two sentences. Hand the same task to a
contractor who started on Monday and it is three pages. The
intent did not change between those two documents. The audience
did.

This is the ordinary condition of all delegation, and it means
something specific: a written spec is a delta. It covers the
distance between what the intent requires and what the executor
already brings. The ten-year veteran brings most of the distance;
the contractor brings less; the spec makes up whatever remains.
Sufficiency is not a property of the text alone. A spec is
sufficient *for* someone — the same document can be complete for
one executor and dangerously underspecified for another, without
a word of it changing.

## Enter the model

When the executor is a model, the same structure holds with the
audience swapped. A model brings its training: a strong pull
toward whatever the corpus of existing code made conventional —
idiomatic structure, standard error handling, the boilerplate any
experienced engineer would also produce without thinking. A spec
written for that executor rationally leaves the conventional
part silent and spends its sentences where the intent is
unconventional: the business rule that breaks the usual pattern,
the constraint no training data could have contained because it
belongs to this project alone.

That is good economics, and it is also a tuning. The written
spec ends up shaped by the specific executor it is used with —
precise where that executor needs precision, silent where that
executor needs nothing. The model is not part of the spec, any
more than a letter's recipient is part of the letter. But the
letter was written for the recipient, and it reads that way.

## The pressure

In a spec-as-source workflow, this tuning stops being a choice
and becomes a selection pressure.

What keeps a spec honest in such a workflow is a loop: generate,
check, fix whatever diverged. That loop is real and it works —
but it only fires on content that changes what gets generated. A
clause the generator would otherwise violate is policed: let it
rot and the next generation contradicts it, and someone notices.
A paragraph of rationale that changes nothing about the output —
the why behind a rule, the map of which parts are fragile, the
context a future maintainer will need — produces no
contradiction when it goes stale. The loop never fires on it.
Nothing does.

Left alone, the pressure has a direction: the written spec
converges toward an excellent prompt. Precise where the
generator needs precision, silent where the generator needs
nothing — and increasingly illegible as a theory of the system
for the humans who also have to read it. What affects generation
is kept alive by the loop. What only a human reader needs
survives on discipline, which is how documentation always
survived, and how it always died.

Externalizing knowledge into specs, in other words, has two
ceilings, not one. The first is old: some knowledge is tacit and
does not survive being written down. The second is newer and
easier to miss: some knowledge is perfectly writable, and no
mechanism polices it, because the only enforcement loop in the
workflow answers to the generator's needs rather than the
team's.

## Swapping the executor

Because the written spec is tuned to its executor, replacing the
executor repricess the entire document at once.

Nothing breaks. A shipped artifact does not stop working because
the model that generated it was retired — the generator's whole
influence was absorbed at the moment of generation. What shifts
is the economics of the text: clauses that fought the old
model's habits may sit idle against the new one's, and silences
that were safe stop being safe, because the new model resolves
them differently. The spec's authority is intact. Its tuning is
stale.

Two things survive the swap unchanged, and they are worth
knowing by name. The project's decisions survive: what the
system must do did not change because the executor did. And the
tests survive: a test's verdict consults nobody's prior, so
everything confirmed stays confirmed. The safety net for an
executor swap was never the spec's prose. It is the oracle.

## The oldest audience

None of this began with AI, and the proof is an experience most
engineers have had from at least one side.

The audience a spec was historically tuned for is the team.
Everything a team writes — specs, comments, wikis, the documents
nobody reads until they must — is priced against what that team
already knows. Its silences are free for the people in the room.
Its emphasis fights the misunderstandings those people actually
have.

Peter Naur argued in 1985 that a program is a theory held by its
team, not recoverable from the artifacts once the team
disperses, and that this is why turnover is so expensive. The
two-specs lens adds a second mechanism on top of his. When a
team leaves, the theory leaves in their heads — that is Naur's
half. But the written half that remains was tuned to readers who
held that theory. The new team inherits documents whose silences
were free for someone else, whose clauses fight battles the new
readers never fight, and which say nothing exactly where the new
readers most need speech.

A team swap is an executor swap, and it reprices the encoding
the same way a model swap does. The handover that feels
underdocumented despite years of diligent writing usually is
underdocumented — for this audience. It was adequately
documented for the one that left.

## What to do with it

Three practical consequences follow.

Record the tuning target. Which executor a spec is currently
tuned for is not trivia; it names the audience whose silences
the document has priced in. A model swap, like a team swap, is
cheaper when it is visible as the repricing event it is.

Give the human-facing half a home the loop cannot starve.
Code from Spec's node format separates what the generation agent
sees from what only humans see — an acknowledgment, in
structure, that the two audiences need different things. The
acknowledgment is not yet a solution: the human-facing sections
have no enforcement loop, and survive on the same discipline
documentation always depended on. A forcing function for that
half is an open problem.

And use a fresh reader as an oracle for the tuning itself. A
newcomer who must work from the documents alone, or a different
model regenerating from the spec alone, does not just test the
person or the model — it tests the encoding, by subtracting the
audience the encoding silently assumed. What fails in that
exercise is a list of exactly the silences that were priced
against a prior that is no longer in the room. That diagnostic
was always available. It was just never cheap enough to run on
purpose, until regenerating from the spec became a routine
operation instead of a thought experiment.
