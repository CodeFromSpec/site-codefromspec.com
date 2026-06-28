# On Living in Git

## The door every methodology has to fit through

A methodology for generating software with AI can be elegant
in every internal respect — clean specs, sound confinement,
reliable staleness detection — and still fail for a reason
that has nothing to do with any of that. The generated code
has to get into a real codebase. In nearly every organization
that writes software today, that means a pull request: a diff,
a reviewer, a comment thread, an approval, a merge. The pull
request is the door, and a methodology that does not fit
through it does not get adopted, no matter what is on the
other side.

This is not a constraint we chose and it is not specific to
Code from Spec. It applies to any approach that uses AI to
produce code. If the output is hostile to review — diffs full
of noise, regenerations that touch files that did not need to
change, metadata that pollutes `git blame` — then the people
who have to approve it will resist it, and they will be right
to. Reviewability is not a finishing touch. It is a condition
of survival, and it has to be designed for from the start.

## The good news first

It would be easy to assume that git is the wrong tool here —
that a methodology where code is derived from specs needs some
new kind of review system. It does not, and the reason is
worth stating plainly: git is not a tool for code. It is a
tool for versioned text. It diffs, blames, and tracks the
history of a Markdown file as well as it does a Go file. And
the apparatus built around it — required approvals, inline
comments, change requests, linked issues, branch protection —
is agnostic to what is being built. A comment on a line of
spec works exactly like a comment on a line of code. A change
request against a constraint is as expressible as one against
a function.

So specs fit into git without friction. The thing being
reviewed changes — natural-language specifications instead of,
or in addition to, source code — but the machinery does not
care. A methodology built on versioned specs does not have to
wait for a tool that does not exist. The one it needs is the
one everyone already uses.

## The one thing the tool does not do

There is a single place where the fit is imperfect, and naming
it precisely matters, because it is easy to overstate.

When a pull request changes a spec and regenerates the code,
git shows two diffs: the change to the spec and the change to
the generated file. It shows them with equal prominence,
neutral about their relationship. But in this methodology the
two are not peers. The spec is the cause; the code is the
effect. The spec changed because a human decided something;
the code changed because the spec did. Git does not know this.
It cannot tell the reviewer that the spec diff is the thing to
read and the code diff is the consequence to verify. That
ordering — cause before effect — is the one piece the tool
does not supply, and the methodology has to establish it by
convention rather than expect it from the software.

This is a small gap, not a structural inadequacy. The tool
shows everything that changed; it simply does not rank what
changed by causal role. Everything else — the versioning, the
diffing, the whole review process — works. The work is in
teaching the reviewer where to look, not in replacing what
they look with.

## Maturity is local

Where the reviewer looks is not a fixed property of the
methodology. It moves with trust, and trust is earned locally
rather than declared globally.

The ideal end state is that a reviewer reads only the spec
diff and trusts that the generation faithfully reflects it. But
that trust is not a milestone the whole methodology crosses at
once. It accumulates by region. A mature project, well covered
by tests, can be reviewed at the level of its specs — the
reviewer reads what the spec now says and lets the verification
pipeline guard the translation. A new project, or a new module
inside a mature one, has not earned that yet; there the
reviewer still reads the generated code, because the confidence
that the generation is faithful has not been built up in that
area.

This means reviewing code is not a stage the methodology
outgrows. It is a zoom level, chosen per region according to
how much trust the tests have accumulated there. One project
can be read at the spec level while another is read at the code
level, and within a single project a hardened subtree can be
reviewed by its specs while a freshly written one is reviewed
line by line. A mixed practice is not a transitional
embarrassment; it is the correct state, and it reflects an
honest fact about where confidence has and has not been earned.

Git serves all of this without complaint, because choosing to
read the spec diff or the code diff is just choosing which part
of a pull request to focus on — something every reviewer
already does.

## The hidden principle

Several decisions in this methodology were made for reasons
that looked unrelated to each other. Seen together, they are
the same decision.

Generated code carries no comments. The reasoning was that a
comment is the most variable part of generated output — it
changes from one generation to the next even when the code is
identical, producing diff noise that obscures real changes, and
it competes with the spec as a second account of what the code
is for.

Generated files carry no framework metadata. Earlier versions
stamped each file with a tag — the node's logical name and the
chain hash at generation time, written into a comment. It
worked, but it had a cost that compounded with scale. When a
shared convention at the root of the tree changed, every
artifact that inherited from it went stale, and after
regeneration each file carried a new hash in its tag. The pull
request showed dozens or hundreds of files with a single
changed line each — the hash in a comment. That is pure noise:
it pollutes review, ruins `git blame`, and buries the few real
changes in a sea of mechanical ones. So the metadata moved out
of the files and into a single manifest that records, per
artifact, its path, a checksum, and the chain hash. Staleness
detection reads the manifest. The generated files became pure
application code. Now when a cascade regeneration produces
identical code, the file on disk does not change at all — only
the manifest entry updates. The diff shows one file instead of
hundreds, and the files whose behavior did not change stay
untouched, accumulating the quiet trust that comes from a line
in `git blame` that has not moved in months.

Regeneration preserves the existing artifact and aims for
minimal change, so that a spec edit produces a small,
reviewable diff rather than a wholesale rewrite with different
variable names and reordered functions.

Each of these was presented as a local fix. The common thread
is that all of them exist to keep the diff clean enough that a
reviewer *can* look at the cause instead of drowning in the
effect. Noise in the generated output does not just look
untidy — it actively defeats the shift toward reviewing specs,
because a reviewer faced with three hundred one-line hash
changes has no choice but to wade through the effect. Removing
the noise is what makes it possible to ignore the code diff and
trust it, which is the whole direction the methodology is
trying to move in. The manifest is the clearest case: by
collapsing a cascade into a single changed file, it physically
pushes the reviewer's eye away from the generated code and
toward the spec that caused the change.

There is a second benefit to keeping non-functional content out
of the generated file, and it points back at generation rather
than review. Every piece of information the generation agent
receives that is not the spec is a potential distraction. A
27-character hash and a logical name that looks like a path are
exactly the kind of significant-looking tokens a model attends
to — and attention spent on metadata it only has to copy into a
comment is attention not spent on the specification. Moving the
metadata into the manifest did not only clean the diff; it
removed a distraction from the generation itself. The same
decision served both ends.

## The two-edged sword

It is worth being honest about which part of this is solved and
which part is not.

Making the output fit git — clean diffs, no spurious churn, no
polluting metadata — is the part that is nearly done. It is
mechanical, it is within the methodology's control, and the
decisions above largely achieve it. This is the easy edge.

Shifting the unit of review from code to spec is the hard edge,
and it is not a tooling problem at all. It is a change in habit.
A team trained for years to treat the code diff as the thing
they approve has to learn to treat the spec diff as the thing
that matters and the code diff as a consequence to spot-check —
and, in mature regions, to trust without reading. No amount of
clean output forces that change. It has to be earned, the same
way the local maturity it depends on is earned: through a
verification pipeline strong enough that trusting the generation
is justified rather than hopeful. The friction of adoption lives
here, in the habit, not in the format.

These two edges are related in a way that is easy to miss. The
clean output is a precondition for the habit change, not a
substitute for it. You cannot ask a reviewer to stop reading the
code diff while the code diff is full of noise that demands
attention — the noise makes the old habit mandatory. Removing it
is necessary but not sufficient. It clears the path; walking it
is the team's to do, region by region, as trust is earned.

## Living in the world that exists

Git was not designed for a workflow where code is a derived
artifact and the specification is the source of truth. It is a
little remarkable that it serves this workflow as well as it
does — that a tool built to version hand-written source turns
out to host versioned specifications, route reviewers, and
gate merges without modification. That it fits is partly luck
and partly the fact that text and process are general things,
and git committed to the general case.

One could imagine a review tool built for generation from the
ground up — one that knew the spec was the cause and the code
the effect, that ordered a pull request by that relationship
and let a reviewer descend from spec to artifact only when they
chose to. That tool would fit this methodology better than git
does. But it does not exist, and a methodology that waits for
its ideal tool is a methodology that does not ship. The
practical fact is that the tool we have is good enough, the
process around it is agnostic enough, and the gap that remains
is a matter of convention and habit rather than capability. A
methodology that wants to be used has to live in the world that
exists. For this one, that world is git, and git is hospitable
enough.