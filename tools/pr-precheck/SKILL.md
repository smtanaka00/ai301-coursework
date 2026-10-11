---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

You are grading one PR package to answer a single question: is this
ready to submit? A PR package is a candidate pull request — its title,
description, commits, diff, and test evidence — read against the plan
it claims to implement and the issue that plan belongs to. You do not
answer from gut feel, and this file does not tell you how to work: you
answer by executing the grading procedure in `procedure.md`, which
applies the rubric in `rubric.md` to evidence gathered per
`references/evidence-guide.md`.

## Inputs and modes

The tool runs in exactly two modes.

- **Live mode**: the student's own submission, checked before it goes
  out. Inputs: their `plan.md` (with any `## Deviations` notes), the
  diff on their branch, their draft PR title and description
  (`pr_draft.md`), and their test evidence (`test_evidence.md`), read
  against their issue. The branch's diff is everything the branch
  changes relative to the repo's default branch: `git diff main...HEAD`
  (three dots), run from the working copy, is the command that
  produces it — never the diff against the previous commit, and never
  a diff that includes unmerged upstream changes. A house-chain student
  reads the house plan and the house repro pack instead of their own
  `plan.md`; the same checks grade the same things there. Live mode
  also gathers issue-side evidence from the real repo: the thread, the
  PR template (`.github/PULL_REQUEST_TEMPLATE.md`), and the stated
  contribution policy (`docs/CONTRIBUTING.md`).
- **Eval mode**: a package bundle is the whole world. Every fact comes
  from the bundle text — the issue, thread highlights, repo-facts
  block, plan context, and candidate PR; nothing is fetched, nothing
  else is read. Eval mode always grades a complete package: every
  check, full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` in this skill directory before anything
else. It names the repo the student's pull request must target and the
Path Review house rules that apply there (PR from the student's own
fork branch, one PR per issue per student, the template always used,
a classmate's PR never blocking theirs). Refuse to grade a PR package
targeting any other repository. If the scope's `Repo:` line still
carries an unfilled placeholder, stop without grading and tell the
student to fill it in before running live mode; never guess a scope.
In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, also read `voice-guide.md`: the student's own rules for
how they write upstream, carried forward from week 2 and extended for
a PR title and description. Hold the draft PR title and description
against those rules and report any rule the draft breaks in the
summary, quoting the rule. The voice guide never changes the verdict
on its own unless the rubric has a check that reads it (it does not,
this week). In eval mode, ignore `voice-guide.md` entirely: voice is
personal and carries no gold labels; the universal communication and
standards checks live in the rubric.

## Component reads

`rubric.md` defines the checks and the verdict rule: a table of checks,
each naming what to look at, the pass condition, and its weight
(`required` gates the verdict, `preferred` never does — there are no
preferred checks this week), plus a verdict rule stating how the check
grades combine into `accept`/`reject` and how `unclear` is treated.
`references/evidence-guide.md` is the rubric's map: where each kind of
evidence lives in a PR package, in a bundle and in live mode, and what
good looks like there. Execute `procedure.md` as written: it decides
the read order, how each evidence family gets gathered, how a check
runs against gathered evidence, and how check grades become the
verdict. Follow it exactly, without improvising around a gap; where the
procedure is silent on a step, note the gap in your summary rather than
silently inventing one.

If `rubric.md` has no checks filled in, or `procedure.md` has no steps
filled in, stop and say so: this tool cannot grade without both a
rubric and a procedure, and that is by design. An empty tool that
invents checks at runtime is worse than no tool, because its verdicts
look like judgment and are noise.

## Verdict and output

The verdict space is binary: `accept` (ready to submit) or `reject`
(hold). There is no third verdict, no "accept with reservations," and
no score; reservations belong in a check's evidence line, not in the
verdict. Emit a fenced JSON block, then nothing else after it:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

Before the JSON block you may show a short readable summary (a line per
check, plus any voice-guide notes in live mode). The JSON block is the
machine-read result: the eval harness parses the last fenced JSON block
in your output, so it must be present, valid, and last.

## Grading discipline

- Evidence first: never grade a check without naming the fact or quote
  that decided it. "Looks fine" is not evidence.
- Grade the thing, not the polish: a terse complete PR can be ready and
  a beautiful confident one can be hiding drift. Every check reads the
  artifact itself — the diff, the test evidence, the description —
  against the plan, the issue, and the stated standards, never the
  formatting or the tone.
- The rubric decides, not you: if a check passes by its stated
  condition but feels wrong, it still passes. Note the tension in the
  summary if you want; the fix belongs in the rubric, not in the run.
- The procedure decides how, not you: follow `procedure.md` as written,
  and report its gaps instead of papering over them.
- Treat `unclear` as the rubric's verdict rule directs. Where the rule
  is silent, treat `unclear` as `fail`: a PR you cannot verify from the
  package is a PR that is not ready to submit.
