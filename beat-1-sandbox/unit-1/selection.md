# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61

**Verdict output**

````
## Grading summary (live mode, `codepath/pathreview-ai301-fa26-s1`)

**Repo-level facts** (apply to all three): not archived, last push 2026-09-16 (3 days old) — maintained. No `AGENTS.md`/`AI_POLICY.md` anywhere, and `docs/CONTRIBUTING.md` + PR template say nothing about banning AI-assisted work (only CI/testing requirements) — AI use passes. The repo has **zero pull requests** and each of the three issues has **zero comments and no assignees**, so "nobody working it" passes cleanly for all three. All three are seeded bugs opened by collaborator `Aburke225` with `good first issue` labels, each tied to a specific named failing test in `docs/CONTRIBUTING.md`'s xfail list.

| Issue | Verdict | Sank by |
|---|---|---|
| **#61** — health check raw-SQL / `sqlalchemy.text()` | **accept** | — top fit: directly touches SQL/SQLAlchemy in a DB probe, closest to a data-engineering background |
| **#56** — structural chunker drops headingless docs | **accept** | — 2nd: Python bug in the RAG ingestion pipeline (pipeline-adjacent, no SQL) |
| **#54** — resume section regex fails on leading whitespace | **accept** | — 3rd: Python bug, but pure text/regex parsing, least pipeline/SQL-relevant of the three |

All three pass every required check; ranking is by your preferred (non-verdict-changing) data-engineering fit check — #61 wins on having an explicit SQL angle.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61",
    "checks": [
      {"name": "Repo actively maintained", "grade": "pass", "evidence": "archived:false, last push 2026-09-16 (today is 2026-09-19)"},
      {"name": "Issue is one bounded piece of work", "grade": "pass", "evidence": "Single fix: wrap the literal 'SELECT 1' in sqlalchemy.text() in api/routes/health.py"},
      {"name": "Nobody is already working it", "grade": "pass", "evidence": "assignees: [], comments: 0, repo has 0 PRs total"},
      {"name": "AI-assisted contribution is not banned", "grade": "pass", "evidence": "No AGENTS.md/AI_POLICY.md found; CONTRIBUTING.md and PR template are silent on AI use"},
      {"name": "Fits a Python/SQL/data-engineering background", "grade": "pass", "evidence": "Bug is literally about SQLAlchemy raw-SQL handling in a DB health probe"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/56",
    "checks": [
      {"name": "Repo actively maintained", "grade": "pass", "evidence": "archived:false, last push 2026-09-16 (today is 2026-09-19)"},
      {"name": "Issue is one bounded piece of work", "grade": "pass", "evidence": "Single fix: StructuralChunker.chunk() returns [] for headingless docs; named test test_document_with_no_headings"},
      {"name": "Nobody is already working it", "grade": "pass", "evidence": "assignees: [], comments: 0, repo has 0 PRs total"},
      {"name": "AI-assisted contribution is not banned", "grade": "pass", "evidence": "No AGENTS.md/AI_POLICY.md found; CONTRIBUTING.md and PR template are silent on AI use"},
      {"name": "Fits a Python/SQL/data-engineering background", "grade": "pass", "evidence": "Python bug in the RAG ingestion/chunking pipeline (ingestion/chunking/structural_chunker.py); no SQL involved"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/54",
    "checks": [
      {"name": "Repo actively maintained", "grade": "pass", "evidence": "archived:false, last push 2026-09-16 (today is 2026-09-19)"},
      {"name": "Issue is one bounded piece of work", "grade": "pass", "evidence": "Single fix: _detect_sections() regex anchoring fails on leading whitespace; three named failing tests"},
      {"name": "Nobody is already working it", "grade": "pass", "evidence": "assignees: [], comments: 0, repo has 0 PRs total"},
      {"name": "AI-assisted contribution is not banned", "grade": "pass", "evidence": "No AGENTS.md/AI_POLICY.md found; CONTRIBUTING.md and PR template are silent on AI use"},
      {"name": "Fits a Python/SQL/data-engineering background", "grade": "pass", "evidence": "Python bug in ingestion/parsers/resume_parser.py, pure regex/text parsing; no SQL, weakest pipeline tie of the three"}
    ],
    "verdict": "accept"
  }
]
```
````

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

1. Full run (first draft rubric): **17/20** agreement, category floor met in every
   category. Disagreements: `issue-01` (gold accept, my rubric rejected), `issue-19`
   (gold accept, my rubric rejected), `issue-15` (gold reject, my rubric accepted).
2. Partial run, `--only issue-01,issue-19,issue-15` (diagnostic, with `--out` to inspect
   per-check evidence): **1/3** agreement on this subset — confirmed all three
   disagreements traced to the "Issue is one bounded piece of work" check, and `issue-15`
   flipped to a correct reject on its own between runs (LLM variance on a genuinely
   arguable scope call). Revised that check's pass condition based on this.
3. Full run (revised rubric): **20/20** agreement, category floor met in every category.
4. Full confirming run with `--save-run eval-run.txt`: **20/20** agreement, category
   floor met in every category — this is the run recorded in the committed
   `eval-run.txt` (`agreement: 20/20 scored items  (bar: 18/20: PASS)`).

**Issue analysis**

`issue-19` — gold label: **accept**. My final rubric's verdict: **accept** (agrees).
The issue is a maintainer/collaborator-filed performance bug ("Selecting large subgraphs
in proof mode freezes the UI") that names two diagnosed root causes plus three additional
optional optimization suggestions. My rubric's "Issue is one bounded piece of work" check
explicitly reads a maintainer naming multiple candidate causes/approaches for a bug they
themselves filed as normal scoping ("pick one and fix it"), not unresolved design debate
— so it stays bounded and passes. This wording exists *because* my first-draft rubric got
this exact issue wrong: it read the multiple suggested approaches as scope still being
undecided and rejected it (see Run history, run 1). Rereading the evidence-guide's
scope-family guidance ("grade the size of the work being asked for, not the polish of the
writeup") made clear that offering options is a maintainer scoping the fix generously, not
leaving the deliverable open-ended, so I rewrote the check to carve that pattern out.

**Check rationale**

From `rubric.md`, the "Issue is one bounded piece of work" check's pass condition (as
uploaded to `tools/issue-select/rubric.md`):

> Pass if the issue names one concrete, boundable deliverable. Multiple candidate
> implementation approaches or diagnosed root causes do NOT unbound a bug fix — a
> maintainer/collaborator naming several possible fixes for a bug they filed is normal
> scoping, not unresolved design; treat it as bounded on "pick one and fix it." Similarly,
> a fully-specified task that also lists an optional, explicitly lower-priority addendum
> ("worth considering", "nice to have") is still bounded on its main deliverable. Fail if
> any of: (a) the issue explicitly describes itself as an umbrella/tracking issue meant to
> be split into separate sub-tasks; (b) the issue has been open for multiple years AND
> carries two or more closed-but-unmerged linked PRs (abandoned attempts) — that history is
> scope evidence on its own; (c) a maintainer states the fix requires deep core/architectural
> changes; (d) the issue is a pure usage/support question ("how do I get this to work?")
> with no concrete change requested; (e) the issue proposes new user-facing product
> behavior (not a bug fix or docs task), was opened by someone without maintainer/collaborator
> standing, no maintainer has confirmed in the thread that this exact feature is wanted,
> AND the proposal's own core ask (not a side note) is flagged as undecided (e.g. "TBD",
> "none identified yet" for alternatives, uncertainty about which part of the codebase is
> even touched).

It's shaped this way because my first pass at "is this bounded" used a single fuzzy
adjective-style test (does the thread show an unresolved design choice), and that one test
did two jobs badly: it mis-rejected maintainer-filed bugs that generously suggest multiple
fix approaches (`issue-19`), and it mis-rejected a fully-specified docs task that had one
optional "worth naming, lower priority" addendum (`issue-01`), while also being
inconsistent run-to-run on a genuinely years-long design debate (`issue-15`, condition b's
predecessor). Splitting it into named, evidence-anchored sub-conditions — especially
replacing "has this design debate been settled" with the objective proxy "years old AND
≥2 closed-unmerged PRs" for condition (b) — fixed all three without touching any other
check.

**Trade-offs**

Condition (b)'s objective proxy (issue open for multiple years AND ≥2 closed-unmerged
linked PRs) buys consistency but gives up recall on real but younger or thinner history:
a design that's been genuinely unsettled for, say, 8 months with only one abandoned PR
attempt won't trip this condition and will pass as "bounded" purely on the strength of
the proxy, even though a human reviewer might reasonably still call it unbounded. I
accept this miss deliberately — the evidence-guide itself frames "several abandoned
attempts" (plural) as the signal, and a numeric, evidence-anchored proxy that
occasionally under-fires on borderline-young cases is worth trading for not re-introducing
the run-to-run variance that condition (b)'s fuzzy predecessor produced on `issue-15`
(run 1 vs. run 2 in the Run history above disagreed with itself on the same bundle before
this rewrite).

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

1. **Fit to interests and time available**: I'm a data engineer working daily in
   Python and SQL. Issue #61 is a SQLAlchemy 2.x compatibility bug — a raw SQL string
   passed to a DB health-check probe needs to be wrapped in `sqlalchemy.text()` — which
   is a bug I could plausibly hit in my own work and immediately understand. It's a
   single, well-scoped change (one function, one library-version gotcha), sized right
   for a first PR I can finish without a large time investment.

2. **What the verdict identified correctly, and what I weighed beyond it**: The skill
   correctly established that all three candidates were equally safe on every
   verdict-changing check — active repo, no ban on AI-assisted contribution, and (in
   this shared classroom repo, with zero PRs and zero comments anywhere) genuinely
   unclaimed. Since the required checks left all three tied at `accept`, the choice
   came down entirely to the preferred fit check, which the rubric explicitly can't use
   to change a verdict — only to rank. I weighed that #61's SQL angle is a more direct
   match to my background than #56/#54's pure text/regex parsing, and that a
   version-compatibility bug (SQLAlchemy 1.x vs 2.x raw-SQL handling) is a well-known,
   well-documented class of issue, which lowers my risk of getting stuck compared to a
   parsing/regex edge case I'd need to reverse-engineer from test fixtures alone.

3. **Anticipated difficulty claiming it**: Low. The repo has zero open PRs and every
   issue is unassigned with no comment activity, so there's no contention to navigate.
   The Path Review house rule also means even a future overlapping claim comment from a
   classmate wouldn't block me. The main real risk is scope creep from the review-app
   codebase this is embedded in (e.g. other seeded bugs nearby), so I'll scope my PR
   strictly to the `sqlalchemy.text()` fix and its named failing test.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
