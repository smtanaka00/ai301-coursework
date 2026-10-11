# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: silent-drift)

Where it lives: in a bundle, the plan-context block's scope/changes
excerpt states the in-scope list and anything explicitly deferred; the
same block may carry a `## Deviations` note if the accepted plan was
amended. The candidate PR's diff shows what actually changed, file by
file and hunk by hunk; its description states what it claims to
deliver. In live mode: the student's own `plan.md` (in-scope list, not-
in-scope list, and `## Deviations` filled in after the build, per
calib-01's two-step plan — "remove the shadowed line, verify the file
parses" — naming exactly one file, `themes.gitconfig`), the branch's
diff (`git diff main...HEAD`), and `pr_draft.md`'s Summary/Changes text.

What good looks like: every changed file and hunk is either in the
plan's in-scope list or named in a `## Deviations` note that says what
changed and why; the description's claims about what it did match the
diff exactly, no more and no less. calib-01 is the clean case: one file
touched, one line removed, the description says only that, nothing
else moves. Silent drift runs two directions — a diff doing unplanned
extra work (a bundled rewrite, a new option nobody asked for), and a
description claiming more than the diff delivers (a "documented" piece
that was never actually added). Both are failures; an honest deviation
note explaining a real, disclosed change is not.

## Decisive test evidence (harness category: not-tested)

Where it lives: in a bundle, the plan-context block's repro evidence
(the original before-state) and test plan (the artifact that should
flip), read against the candidate PR's test-evidence section and any
named repo checks' results. In live mode: the student's `plan.md` test
plan and Unit 2 repro steps, `test_evidence.md` (before on `main`,
after on the branch, plus `make test-unit`/`make test-integration`/
`make lint`/`make typecheck` output), read against the issue's original
failure.

What good looks like: the evidence names the exact artifact the plan
said would change (calib-01's `configparser.DuplicateOptionError`
before, a clean parse after, plus a smoke check that the theme still
renders the same color) and shows it actually changing on the real bug
path, not a different, unaffected path standing in as a stand-in proof.
"Tests pass" with nothing named is not evidence. A repo check that
genuinely fails, reported honestly with the reason, still counts as
evidence; a check that was simply never run does not.

## Reviewable diff (harness category: unreviewable)

Where it lives: the unified diff itself and the commit list, in the
bundle's Candidate PR section or on the student's branch.

What good looks like: every hunk is legible as part of the one named
fix, the way calib-01's single-hunk, single-commit diff is — nothing
extra to explain away. The debris tells to watch for: a commented-out
line left next to its replacement, a debug `eprintln`/`print` someone
forgot to remove, a dead helper function kept "for reference," a stray
TODO unrelated to the fix, a pure re-indent or import-reorder hunk
riding along, or commit messages like "wip"/"fix"/"misc cleanup" that
signal an un-squashed debugging trail. One such hunk is enough to fail
this check even when the core fix next to it is entirely correct.

## Standards & disclosure (harness category: standards-wall)

Where it lives: the repo-facts block's stated PR-template sections and
contribution/AI-use policy, read against the candidate PR's title and
description. In live mode: Path Review's `.github/PULL_REQUEST_TEMPLATE.md`
and `docs/CONTRIBUTING.md`, read against the student's `pr_draft.md`.

What good looks like: every section the template asks for carries real,
specific content — calib-01 has no template to satisfy (the repo ships
none) and so passes trivially on this front; a repo that does ship one,
like Path Review, needs `Closes #<n>` filled in, a real Changes list,
and Testing boxes that are only checked when true. An AI-use disclosure
belongs wherever one is required: by the repo's own stated policy in an
eval bundle (most repos here state none; a few, like ghostty in pkg-20,
state one explicitly), and always in live mode, because the course
requires disclosing AI assistance on every graded PR regardless of what
Path Review's own template asks — when the template has no dedicated
section for it, it goes under Notes for Reviewers. A section restated
as boilerplate, an unfilled checkbox, or a disclosure that should be
there and isn't, is the failure this check exists to catch; a thread
with explicit maintainer direction the PR ignores fails it too.
