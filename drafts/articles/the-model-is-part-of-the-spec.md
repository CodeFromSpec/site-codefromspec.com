# The Model Is Part of the Spec

Consider what the spec does *not* say. It does not explain how to
write a `for` loop, define the syntax of the language, or say
what a hash map is. The spec for a payment handler does not teach
the agent to program, any more than a memo teaches a colleague to
read. We assume all of it. The entire methodology rests on the
assumption that the executor already knows how to turn intent
into working code.

So the specification has two halves. One is written, versioned,
and reviewed: the spec tree. The other is unwritten — everything
we never had to say because the executor already knew it. The
second half is invisible, and load-bearing. Remove it and nothing
generates.

This is not unusual. Every specification in history assumed a
competent reader. A blueprint assumes someone who can read a
blueprint; a recipe assumes someone who can cook. Assuming
competence in the executor is what makes a spec shorter than the
thing it specifies. What is new is the kind of competence
assumed, and — the part the first telling of this idea missed —
that the unwritten half is not one thing. It is two, and the
difference between them is the difference between a delegation
that is safe and one that is not.

## The unwritten half is not one executor

When the spec omits something, the gap is filled. The question
the original framing glossed is: filled *by what*. There is more
than one executor standing behind a generated artifact, and they
do not behave alike.

One is the model. When a node says "validate the transfer amount
is positive" and never mentions that integers compare with `>`,
the model supplies the comparison. This is the **stochastic
unwritten half**: filled from training, varying with the model
and, at the margin, with the run. It resolves ambiguity the way a
competent programmer reading a terse spec does — usually well,
never guaranteed identical twice.

But there are other executors, and they are different in kind.
When a column is declared `BIGINT` holding centavos, the
constraint that money is an integer is not supplied by the model
— it is enforced by the database, the same way on every
generation, regardless of which model ran. When `CHECK(amount >
0)` sits on the table, "amount must be positive" is filled by
Postgres, deterministically, whether or not the agent thought of
it. When a strong type makes an illegal state unrepresentable,
the compiler fills the gap before any model is consulted. This is
the **deterministic unwritten half**: also absent from the spec
prose, also assumed, but supplied by an executor that does not
vary, does not hallucinate, and does not depend on which weights
are loaded.

Both halves are unwritten. Both are delegations. But a delegation
to the deterministic half is safe in a way a delegation to the
stochastic half is not — because the deterministic executor gives
the same answer forever, and the stochastic one gives an answer
that was correct this time. Almost everything that makes the
unwritten half feel dangerous is about the stochastic part. The
deterministic part is not a risk to manage; it is leverage to
reach for.

## Every omission is a delegation — to the model by default

Here is the fact that reorganizes the rest. Silence does not go
nowhere. Every meaning the spec leaves unspecified flows
somewhere, and unless you have arranged otherwise, it flows to
the model. The model is the **default sink** for unspecified
meaning. It is where a silence ends up when you did not write it
down, did not give it a type, and did not pin it with a test.

Most of the time this default is exactly right. A node that says
"validate the amount is positive" should not have to specify
integer comparison; letting that silence flow to the model is
what keeps the spec readable as intent instead of code. The
hidden dependency is the compression that lets a spec be a memo
rather than a transcript. The default sink is a feature for every
silence that falls inside shared competence.

It is a hazard for every silence that does not. A case from the
framework's own development: a spec said to match file patterns
but not which standard-library function to use. The language
offered two — one platform-dependent, one not. The agent picked
the platform-dependent one. The code was correct on one machine
and wrong on another. The fix was three words added to the spec.

The usual lesson drawn from this is "make the delegation
explicit — move it from the unwritten half to the written half."
That is one move, and often not the best one. The deeper lesson
is about routing. That silence had flowed to the default sink,
and the default sink resolved it with a coin-flip the author
never knew was being tossed. The fix was to **divert the silence
away from the stochastic sink** — and prose is only one of the
places you can divert it to. You can write it down, so a
competent-but-stochastic reader is told the answer instead of
guessing it. You can give it a type, so a deterministic executor
settles it and the model never gets a vote. You can pin it with a
test, so that if the stochastic reader guesses wrong the failure
is loud instead of silent. The platform-function case took the
prose route because the choice was a one-line convention. A money
representation takes the type route, because `int64` centavos
removes the question from the model's reach entirely. A date
format carried in a `TEXT` column — where no type is watching —
can only take the prose-plus-test route, because nothing
deterministic will catch it.

So the engineering is not "eliminate the unwritten half." It is
**managing the default sink**: knowing which silences are safe to
let flow to the model, and diverting the rest to whichever sink
can hold them without stochastic variance. The unwritten half
does not shrink to zero and should not. It shrinks to the core of
shared competence — and what leaves it does not all go to prose.
The fidelity-critical part goes to types and tests, where the
model's variance cannot reach it.

## You version only one of the two halves

Now the consequence that the staleness machinery cannot see.

The chain hash is computed over the written half. It is a precise,
honest record of one thing: that this artifact was produced from
this spec content. It says nothing about the unwritten half,
because the unwritten half has no text to hash. And the stochastic
unwritten half — the model — is a real input to the artifact. The
artifact is a function of the chain *and* the model, and the hash
versions only the first argument.

This means the boundary between the two halves is exactly where
reproducibility ends. Hold the written spec fixed and change the
model, and the artifact can change — a function chosen
differently, an ambiguity resolved the other way, an edge case
handled or dropped. The written spec is byte-for-byte identical.
The staleness system reports clean. Nothing is stale, because by
the only definition the manifest knows, nothing changed. And yet
the behavior moved, because the half of the specification that
lives in the weights moved.

State it plainly: **upgrading the model is a silent spec change
across every node at once, invisible to staleness.** It is the
largest possible edit to the specification — every artifact's
unwritten half, rewritten simultaneously — and it leaves no diff,
triggers no regeneration, and produces no hash mismatch. The
machinery built to tell you when to regenerate is structurally
blind to it, because that machinery watches the written half and
the change happened in the other one.

The model is part of the specification. Not a tool that reads it —
a component of it. And it is the component you do not version,
cannot diff, and will not be warned about when it changes. The
only thing standing between a model upgrade and a silent
regression is the part of the system that checks the artifact's
behavior rather than its provenance: the tests. This is why "the
model is part of the spec" and "tests are the immune system" are
not two observations. They are one. Because half the spec is
unhashed and can change under you, behavioral verification is not
a safety net you add for tidiness — it is the only sensor pointed
at the half of the specification the hash cannot see.

## Why natural language works — and why it is a dial, not a switch

Specification-driven development is old, and for most of its
history it meant formal notation — Z, VDM, structured analysis.
The notation was not chosen for beauty. It was chosen because the
executor was not intelligent. A compiler fills in nothing;
everything had to be formalized, exhaustively, with no gap left
for judgment because there was no judgment on the other end.
Natural language was disqualified for the very reason it is
natural: it is ambiguous, and a dumb executor cannot resolve
ambiguity.

The objection was correct against the executor it assumed. It is
no longer the executor we have. The model resolves ambiguity, and
resolves it the way a competent programmer reading a terse spec
always has. The formalism existed to eliminate something that now
has someone to handle it. Choosing natural language *is* choosing
to couple to the model — to route silences to the stochastic
sink. The prose a domain expert can review is bought with the
executor-independence the notation had. That coupling is the
price of participation, and it is real.

But the original framing presented this as a binary — formal
notation, executor-independent and excluding; or prose, coupled
and participable; pick one. The practice is not binary. The
coupling is a dial, and you set it per decision. You couple to
the model exactly as much as you choose prose over types. A real
service occupies the middle deliberately: prose specifications,
readable by the domain, sitting on a foundation of strong types
and schema constraints. The participation-critical truth — the
business rules a compliance officer must be able to read — stays
in prose, fully coupled, because legibility is the point there.
The fidelity-critical truth — money is an integer, this transfer
cannot be to its own source, this identifier is unique — is
pushed into types and constraints, where the coupling to the
model is bought back down to zero, because a deterministic
executor now holds that truth and the model's variance cannot
touch it.

This is the craft the notation-versus-prose binary hides.
Formalism put *all* the precision in the written half and leaned
on no intelligent executor: expensive, excluding, but stable.
Pure prose puts the fidelity-critical precision in the stochastic
sink along with everything else: cheap, reviewable, coupled. The
working position is neither. It is to route each silence to the
sink that fits it — shared competence to the model, business
meaning to prose, invariants to types, the rest to tests — so that
the spec stays legible where legibility matters and stays coupled
to the model only where coupling is cheap to bear.

## What convergence is converging

In the Rationale we named natural-language ambiguity as an open
problem and offered convergence as the working answer. This is
what convergence converges: not the spec toward completeness, but
each silence toward its right sink.

Every failure that traces to a hidden dependency is a silence
that flowed to the default sink and should not have. Each fix
reroutes it — to prose where a convention belongs in writing, to
a type where an invariant belongs in the schema, to a test where
a behavior needs a sensor. Over a project's life the stochastic
unwritten half shrinks, not to nothing, but to the core of shared
competence no spec should have to state — and what leaves it is
distributed across substrates, not piled into prose. The
information that defines the system is conserved; you do not make
it smaller by specifying well. You only move it out of the one
sink that resolves it differently each time, into the sinks that
hold it still.

The model is part of the spec. The discipline is to decide, for
each thing the spec does not say, whether that is a delegation you
meant to make.