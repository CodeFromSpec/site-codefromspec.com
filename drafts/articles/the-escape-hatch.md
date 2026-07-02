# The escape hatch

## Assembly in the C file

C compilers let you write assembly inside a C file. Almost nobody
does. The feature is laborious, niche, and in most codebases never
used at all — and it is one of the reasons C could be trusted for
fifty years. You do not write C worrying about "what if I need one
specific instruction?", because you know the hatch is there. The
day the compiler's output is not good enough for one hot loop, you
can drop down, pin the exact instructions, and come back up. The
value of the escape hatch was never its frequency of use. It is
what its existence does to your confidence in the level above.

Grading abstraction inside a single artifact is not an exotic
trick. It is arguably the core competence of software engineering:
names and comments carry intent in prose, declarative code sits
where it can, and the bit-twiddling appears only in the hot path.
A good programmer has always worked at several altitudes in the
same file.

Specifications written for an AI executor add a rung above the
highest programming language — and the discourse around them
quietly assumes that once you are up there, you must stay. The spec
is natural language; writing code in it would be an admission of
failure. This article argues the opposite: nothing about generating
code from specs forbids putting code *in* the specs, exactly where
that is justified — and the methodology has been doing it all
along.

## The ladder is already in use

Look at what actually sits in a working spec tree, and you find a
gradient, not a purity.

At the top, prose: "validate that the transfer amount is positive."
One step down, a named function: when generated code matched file
patterns with a platform-dependent standard-library function and
broke on another OS, the fix was three words in the spec naming the
platform-independent one — prose that pins a single identifier.
Another step down, the `# Agent` sections that guide generation are
routinely step-by-step pseudocode: open this, write that, close,
return this string. And at the bottom of the ladder, literal
source: an instruction that says use
`strings.ReplaceAll(logicalName, "/", "_")` — an exact line of Go,
inside the spec, at the one point where nothing less precise would
do. Interface sections pin exact type signatures the same way.

Every rung is the same move at a different altitude: the author
decides how much freedom the executor gets at this point, and
writes at the altitude that grants exactly that much. Most of the
spec lives high, because that is what keeps it readable and cheap
to write. It descends where getting the detail exactly right
matters more than brevity — and at the last rung, the freedom
granted is zero.

## What the prohibition actually prohibits

There is an apparent contradiction here worth dissolving, because
dissolving it is the point of the article.

The methodology's hardest rule is: never edit generated code. That
sounds like a rule about authorship — humans do not write code
anymore, the agent does. It is not. It is a rule about **location**.
A hand edit to a generated file is code that lives outside the
source of truth: unversioned as spec, invisible to the chain, dead
at the next regeneration — the manifest flags it as drift the
moment it appears. The same lines written *into the spec* are the
opposite of drift: hashed, inherited, delivered in every future
chain, permanent. The prohibition was never "no human code." It is
"no code outside the source of truth."

So when the generator keeps getting one function wrong, or one
line simply must be what it must be, the move is not to grit your
teeth and re-prompt, and not to sneak a hand edit into the
artifact. The move is to descend the ladder in the spec: pin the
function name, or the pseudocode, or the exact code. Written
there, it is not a workaround. It is specification — at low
altitude.

In the terms of [Collapsing the wave function](/articles/collapsing-the-wave-function):
a spec defines a space of conforming programs, and every silence is
a degree of freedom. A literal code block is a region of the spec
with no degrees of freedom at all — a point where the wave function
arrives already collapsed, at authoring time, by the author. The
dial of how much you couple to the model's judgment runs from fully
coupled (pure prose) to zero (verbatim code), and it is set per
passage, not per project.

## Honest limits

Three things keep the hatch from being a free lunch, and they are
worth naming precisely.

**The seam still passes through the model.** When you drop to
assembly in C, both levels are executed by functions, and the seam
between them is a specified ABI. Here, the thing that wires your
literal block into the surrounding generated code is the model
itself. The block removes variance inside its boundaries — not at
them. The code around it can still arrive differently across
generations, and a test is still what pins the boundary.

**Verbatim is reliable, not guaranteed.** Today, a literal block
reaches the artifact because the generation agent copies it. In
practice this is highly dependable — the agent has the exact text
in its chain and no reason to improvise. But it is fidelity by
behavior, not by mechanism; nothing in the tooling splices the
bytes directly.

**Overuse collapses the point of the exercise.** A spec that is
mostly literal code is just code with extra steps — it pays the
chain's overhead and gives up the legibility that justified prose
in the first place, including the ability of a non-programmer to
read the parts that concern them. The hatch has a working range:
wide enough to descend whenever precision demands it, narrow
enough that descending remains the exception.

## Future work: a mechanical bottom rung

The second limit points at a tooling direction we find genuinely
interesting: giving the bottom of the ladder a mechanical
guarantee.

The natural extension is to let a spec node declare a complete
source file as literal spec content — hand-written, versioned in
the tree, delivered to the artifact byte-for-byte by the tooling
rather than through the agent. Hand-written code would then have a
first-class home inside the source of truth: hashed like any other
position, participating in staleness, visible as context to the
nodes that depend on it — and exempt from the sampler entirely.
The escape hatch, with the hatch actually welded to the airframe.

We have not built this. It is the kind of mechanism that should be
pulled into existence by a real case — a file that must be exact,
lives in a spec-driven project, and keeps paying the cost of the
missing guarantee — rather than built on principle. But the shape
is clear, and nothing in the methodology resists it.

## Trust flows down from the hatch

The conclusion mirrors where we started. Nobody trusts C *despite*
rarely using inline assembly; the unused hatch is part of why the
trust is rational. The same holds one level up. You can specify in
prose — reviewable by your domain, cheap to write, high above the
implementation — precisely because you know that any passage that
misbehaves can be pinned: a word, a function name, a block of
pseudocode, a line of Go. The ladder's lower rungs are what make
living on the upper ones a choice instead of a gamble.

A methodology that forbade code in specs would be defending its
purity at the cost of its usability. This one has no purity to
defend. The spec is not a genre. It is the authoritative artifact —
and authority can be stated at whatever altitude the passage
requires.
