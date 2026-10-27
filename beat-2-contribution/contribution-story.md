# Contribution: test: type the ResizeObserver stub in WcsGridOverlay.test.tsx instead of any[]

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** 1
**Student:** Tanaka Mbavarira
**GitHub Username:** smtanaka00
**Issue:** https://github.com/Snoww3d/jwst-data-analysis/issues/1396
**Pull Request:** [GitHub PR link, once opened]

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Installed the issue-scout and wrote a repo-pinned SQL-tooling search strategy**

I installed the scout over my Unit 1 skill and wrote a search strategy from my fit profile (SQL tooling written in Python). My first draft query, "sql" plus "good first issue" plus Python, returned mostly hobby repos, so I tested every query in the terminal and switched to bug-label queries pinned to sqlfluff, sqlglot, sqlfmt, SQLMesh and dbt-utils. The first scan gave 1 accept and 4 rejects; four of the five failed "Nobody is already working it" on an open PR or an unanswered claim comment, because my ranking put `has-repro` issues first and those were the ones others had already picked up. I reordered the ranking to fewest comments first, raised the per-repo cap from 2 to 3, and added a "Not already fixed" check to the rubric after the only accepted issue turned out to have a merged PR that may already fix it.

**Unit 6: Installed the voice guard, patched the scout's scope check, and wrote a memory file for the JWST repo**

I installed the voice guard and wired its hook into my global settings, and the wiring test stopped a claim that promised a PR date, as it should. Two things fell short: my first claim on #1396 went up before the hook was live and broke my own rule ("a claim names my first step, never a fix or a date"), and the guard stops with "the claude CLI is not on PATH" in my setup until the hook command adds the VS Code extension's bundled `claude` folder to PATH. I replaced the live comment with a version that passes, fixed the hook's PATH, and now run the guard on every draft before I post it. The memory file (`CLAUDE.local.md`, 44 lines, importing the repo's `AGENTS.md`) holds the PR-body rules that CI enforces, the exact one-line edit, and the working commands, and it is excluded from git through `.git/info/exclude`; I wrote it from the repo's docs this week and have not yet seen it save a session's worth of orientation.

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I started from the profile I wrote for the scout: SQL tooling written in Python, a small bug fix I can reproduce with pytest and no paid services. My scan and the issues I graded by hand over the next week surfaced plenty of candidates, but the ones that fit best kept disappearing: sqlfluff #8729, SQLMesh #6035 and avogadrolibs #3190 were all closed before I could claim them, and the picks I hoped to take from the Issue Selection site never materialized. I then claimed SQLMesh #5770 (a `JSON_KEYS` conversion bug whose fix belongs in sqlglot) and released it the same day, because it had an open design question about `max_depth`, the fix and the issue lived in different repos, and I only have about a week left. I chose Snoww3d/jwst-data-analysis #1396 instead: a one-line TypeScript test fix that replaces `(entries: any[]) => void` with `ResizeObserverCallback` in the `ResizeObserver` stub in `WcsGridOverlay.test.tsx` and drops an `eslint-disable`. My scout accepted it with the patched rubric and gave it the difficulty note **straightforward**, since the issue names the file, the lines and the exact edit. It does not fit my profile: it is TypeScript and React test code, not SQL or Python. I chose it knowingly, because it is small enough to finish and verify in the time left and needs only free tooling; the scout's fit check is preferred-only and says so. I hope to learn how to take a change through a repo that enforces its PR format in CI, and how a maintainer who is mostly automated responds to an outside contributor (the repo's last 100 PRs are 99 Dependabot and 1 from the owner, so mine may be the first outside PR).

Claim comment: https://github.com/Snoww3d/jwst-data-analysis/issues/1396#issuecomment-6431310610

On the scout itself: it held up on the checks that matter most. It caught a ghost issue, rejected issues with open PRs or stale claims, and agreed with my read on #1396. It fell short in these ways on real repos:
- The `-linked:pr` search filter did not catch issues that already had PRs, so open PRs have to be found by reading the thread.
- The "nobody is working it" check has no rule for my own claim comment, so I had to read it by judgment.
- The maintained check passes a repo whose recent commits are all Dependabot.
- repro-check told me #1396's repo has no PR template when it has one that CI enforces.
My patches were the "Not already fixed" check, the reordered ranking, and the course's `scope-feasible` row, which I made keep the strict Unit 1 conditions in eval mode only so the Unit 1 harness still passes (20/20). I logged the gaps I did not fix in `scan-notes.md`.

**Toolkit**

- Tool: issue-scout, with my own search strategy and my patched rubric (the Unit 6 `scope-feasible` row plus a difficulty note). I used it for the scan, for grading about twenty issues one at a time, and for a final re-grade of #1396 after the patch.
- What it produced: one scan (1 accept, 4 rejects) and a read-out per graded issue in `scan-output.md`. For #1396 it gave accept, with all five required checks passing, the fit check passing on the one-week exception, and the difficulty note straightforward.
- Difficulties: the gaps listed above: `-linked:pr` missed open PRs, no rule for my own claim, a maintained check that Dependabot-only commits satisfy, and a closed-before-claim rate high enough that three top picks were lost.
- What changed: I reordered the ranking to fewest comments first, added "Not already fixed", applied the course scope patch with an eval-mode clause, and now claim upstream the moment I commit to an issue. I did not apply the stale-claim window, the "fix lands in the same repo" check or the human-commit check; they are logged in `scan-notes.md`.

---

## Understanding the Issue

**Problem Description**

[In your own words, what is broken or missing?]

**Expected Behavior**

[What should happen?]

**Current Behavior**

[What actually happens?]

**Affected Components**

[Which parts of the codebase are involved?]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Reproduction Process

**Environment Setup**

[What a stranger would need to stand this up: your operating system,
the relevant versions (language, runtime, package manager, the project
itself), and how you set the project up. Then the challenges you faced
and how you solved them, or that none came up.]

**Steps to Reproduce**

1. [Step 1]
2. [Step 2]
3. [Observed result]

**Reproduction Evidence**

- **Commit showing reproduction:** [link to commit in your fork]
- **Screenshots/logs:** [if applicable]
- **My findings:** [what you discovered during reproduction; an
  honest, evidenced cannot-reproduce that led you to re-pick or ask
  the maintainer belongs here and costs you nothing]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Solution Approach

**Analysis**

[Your analysis of the root cause: what is causing the issue, traced
to the files and functions involved.]

**Proposed Solution**

[High-level description of your fix approach, and what you are
deliberately NOT changing.]

**Implementation Plan**

1. [Modify file X to do Y]
2. [Add or change Z]
3. [Update tests]

[Record deviations here as they happen: what changed mid-build and
why. A recorded deviation is honest work; silent drift is not.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**[Date]**

[Entry]

**[Date]**

[Entry]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** [GitHub PR URL when opened]

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- [Date]: [feedback received, or "none yet"; silence is a normal
  state and costs you nothing]
- [Date]: [how you addressed it]

**Status:** [Awaiting review / Iterating / Merged / Closed. The
status is a fact you record, never a grade: the PR is graded as
opened.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
