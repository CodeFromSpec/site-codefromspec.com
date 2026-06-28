# From Artifact Tags to a Manifest File

## The artifact tag

In earlier versions of Code from Spec, every generated
file contained an artifact tag — a comment near the top
of the file with the node's logical name and the chain
hash at the time of generation:

```go
// code-from-spec: SPEC/payments/fees/calculation@k4Xz9pQ1rLmN3vB7wY2tHsJ8dFa
```

The tag served two purposes: it identified which spec
produced the file, and it carried the hash used for
staleness detection. The tooling would read the tag,
compare the hash against the current chain, and report
whether the artifact was up to date.

It worked. But it had costs that compounded as projects
grew.

## The noise problem

When a root node changes — a shared convention, an
architecture constraint, a glossary entry — every
artifact that inherits from it becomes stale. After
regeneration, each file has a new chain hash in its
tag, even if the generated code did not change. The
git diff shows dozens or hundreds of files with one
modified line each: the hash in the comment.

This is noise. It pollutes code review, makes `git
blame` less useful, and obscures the actual changes
in a sea of hash updates. A reviewer scanning a PR
must distinguish between files where only the tag
changed and files where the code changed — and the
tag change is in every file, demanding attention
without deserving it.

We had already made a similar decision earlier: no
comments in generated code. The reasoning was the
same. Comments are the most variable part of
generated output — they change across regenerations
even when the code is identical, creating diff noise
that obscures real changes. Removing comments made
PRs cleaner and code review faster. The artifact tag
was the last piece of framework metadata left in
generated files, and it had the same problem.

## The subagent burden

The tag also affected generation quality. The
subagent received the chain hash and the logical
name in its prompt, and was responsible for writing
the tag in the correct format, in the correct
position, inside a comment appropriate for the file
type. This was a source of errors — malformed tags,
missing tags, tags in the wrong position — and it
was work unrelated to the subagent's actual job of
generating code from a specification.

More subtly, the hash and logical name in the prompt
were information the subagent did not need for
generation. It only needed to copy them into a
comment. But language models anchor on anything that
looks significant. A 27-character alphanumeric hash
and a logical name that suggests directory structure
become signals the model attends to — signals that
compete with the specification for the model's
attention. Every piece of information the subagent
receives that is not the spec is a potential
distraction.

## The manifest

The solution is to move the metadata out of the
generated file and into a centralized manifest. The
manifest is a single file that records, for each
artifact, its output path, a checksum of the file
content, and the chain hash at the time of
generation. Staleness detection reads the manifest
instead of scanning files for tags.

Generated files become pure application code —
no framework metadata, no comments, nothing that
is not part of the artifact's function. The subagent
generates code and calls `write_file`; the tooling
handles the rest. The subagent never sees the chain
hash, never writes a tag, never makes a formatting
decision about where to place a comment.

When a root node changes and a cascade regeneration
produces the same code, the generated file does not
change — only the manifest entry updates. The git
diff shows one file (the manifest) instead of
hundreds. Files whose code did not change stay
untouched. Code review focuses on code that
actually changed.

## The same principle

This is the same principle behind removing comments
from generated code, applied to the last remaining
case. Every piece of non-functional content in a
generated file — comments, metadata, framework
markers — is content that varies across
regenerations, clutters diffs, and competes for the
model's attention during generation.

The manifest absorbs the churn. The generated code
stays clean. The subagent's job gets simpler. And
code that has not changed accumulates trust
silently, because its file on disk is untouched —
not merely unchanged-except-for-the-hash.
