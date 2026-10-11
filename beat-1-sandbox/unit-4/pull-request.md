# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s1/pull/125

**Branch**

`fix/61-wrap-select-in-text`

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Smoke run, 3 packages (`--limit 3 --workers 3`): 3/3 agreement (sanity check before
   spending on a full run; not a scored run).
2. Full run #1, 20 packages (`--workers 8`): **20/20 scored items, bar PASS**, every
   category matched (`clear-accept 7/7  not-tested 4/4  silent-drift 4/4  standards-wall
   2/2  unreviewable 3/3`). No rubric revision was needed after this run.
3. Full run #2, 20 packages, with `--save-run eval-run.txt`: **20/20 scored items, bar
   PASS**, every category matched again, identical to run #1. This is the run written to
   the committed `eval-run.txt`.

**Package analysis**

`pkg-12` (`sharkdp/bat#3845`) — gold label `reject` (category `unreviewable`), my rubric's
verdict: `reject` — agree.

The core fix in this package is correct and even evidenced: the diff saturates the
look-back buffer's `+ 1` and caps preallocation, the repro's `capacity overflow` abort is
gone, exit code flips to 0, and a regression test is added. A rubric that only asked "does
the fix work" would accept this. My "Reviewable diff" check rejects it anyway, because the
same diff carries a commented-out first attempt (`// let mut buffered_lines: ... VecDeque
... min(MAX_PREALLOC)`), a leftover `// eprintln!("DBG buffer_size = {}", buffer_size);`
debug line, a dead `_unused_buffer_probe` function the author admits "was used to bisect
the abort threshold," and commit messages `wip` / `fix` / `fmt + cleanup` that match the
debris exactly. My rubric's pass condition for that check states debris fails the check
"regardless of how correct the core fix is elsewhere in the same diff" — written in
specifically to stop a correct fix from buying back a diff nobody could actually review
the way it was submitted. That's exactly what decided this package: the diff's hunks were
read on their own terms, independent of whether the behavior change worked.

**Check rationale**

From `rubric.md`'s "Reviewable diff" row, the pass condition as it reads now:

> "Fail if the diff carries debris: commented-out code, debug prints/`eprintln`/`console.log`
> left in, dead functions or unused variables kept "for reference," stray TODOs unrelated to
> the fix, pure formatting/re-indent churn, or an unrelated hunk folded in alongside the real
> change — regardless of how correct the core fix is. Commit messages like "wip," "fix," or
> "misc cleanup" are a signal to look harder at the diff for exactly this kind of debris, not
> a failure on their own."

I wrote it this way from the start, not after a miss — `eval/gold-labels.json`'s notes for
the `unreviewable` category (`pkg-12`, `pkg-15`, `pkg-18`) all describe a *correct* fix
sitting next to debris, and `CONTRACT.md`'s own grading-discipline guarantee ("grade the
thing, not the polish... a beautiful confident one can be hiding drift") pointed at the
same failure mode. So the pass condition explicitly separates two questions that are easy
to collapse into one: "is the fix right" (a different check's job) and "can a reviewer
actually review this diff as submitted" (this check's job) — and says the second one fails
on its own, however the first one turns out. The last sentence (commit messages as a
signal, not a verdict) exists so the check doesn't fail a terse, honestly-labeled "wip"
commit history that happens to contain zero actual debris; the debris itself has to be
found in the diff, not inferred from a commit message.

**Trade-offs**

This check has no severity threshold: one forgotten debug line and a diff full of three
abandoned attempts both fail it the same way, with no partial credit for "almost clean."
That means a PR that is one quick `git commit --fixup` away from accept gets the identical
`reject` as one that would need a real rewrite to be reviewable — the check can tell you
*that* a diff isn't reviewable, not *how far* it is from being one.

Nothing changed in this check (or any other) between the two full runs, and here is how I
know: run #1 and run #2 produced the identical 20/20 result with the identical
per-category tally, including `unreviewable 3/3` both times — no package flipped, so there
was no revision cycle to canary-check in the first place. This unit's rubric cleared the
bar on the first full run.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
