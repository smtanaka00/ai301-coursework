# Rubric: is this a good first issue?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever checks
you define here. It ships empty on purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where. Name the source
     (repo-facts block, issue body, comment thread, or the locations in
     references/evidence-guide.md). "The repo" is not a source; "the last
     5 default-branch commit dates" is.
   - Pass condition: a condition someone else could apply and get your
     answer. Prefer thresholds with numbers ("a maintainer commented
     within 30 days") over adjectives ("maintainer is responsive").
   - Weight: `required` (a fail here rejects the issue) or `preferred`
     (never changes the verdict; a nice-to-have that helps rank the
     issues you accept).

2. A verdict rule below the table: how the check grades combine into
   accept or reject, including how `unclear` is treated. The verdict
   space is binary. If you write no rule for `unclear`, the skill treats
   it as fail.

Cover what actually kills first contributions. The lecture named four
families: the maintainer is alive, the repo is in use, the scope fits a
newcomer, and nobody else is already on it. A rubric that ignores a family
will fail eval issues designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Repo actively maintained | The `archived:` flag, the `last push to any branch` date, and the dates in `last 5 default-branch commits` under Repo facts (live: the archived banner, the front-page newest-commit date, and the recent commit list). | Pass if `archived` is `no` AND at least one of {the last-push date, the most recent of the last 5 default-branch commits} is within 180 days of the bundle's `captured` date (eval mode) or of today (live mode). Fail otherwise. | required |
| Issue is one bounded piece of work | The issue body and the full comment thread. | Pass if the issue names one concrete, boundable deliverable. Multiple candidate implementation approaches or diagnosed root causes do NOT unbound a bug fix — a maintainer/collaborator naming several possible fixes for a bug they filed is normal scoping, not unresolved design; treat it as bounded on "pick one and fix it." Similarly, a fully-specified task that also lists an optional, explicitly lower-priority addendum ("worth considering", "nice to have") is still bounded on its main deliverable. Fail if any of: (a) the issue explicitly describes itself as an umbrella/tracking issue meant to be split into separate sub-tasks; (b) the issue has been open for multiple years AND carries two or more closed-but-unmerged linked PRs (abandoned attempts) — that history is scope evidence on its own; (c) a maintainer states the fix requires deep core/architectural changes; (d) the issue is a pure usage/support question ("how do I get this to work?") with no concrete change requested; (e) the issue proposes new user-facing product behavior (not a bug fix or docs task), was opened by someone without maintainer/collaborator standing, no maintainer has confirmed in the thread that this exact feature is wanted, AND the proposal's own core ask (not a side note) is flagged as undecided (e.g. "TBD", "none identified yet" for alternatives, uncertainty about which part of the codebase is even touched). | required |
| Nobody is already working it | The `this issue: assignees` and `linked PRs` line under Repo facts, plus the comment thread (a PR mentioned only in a comment counts too; when the sidebar/facts line and the thread disagree, believe the thread). | Pass if assignees is `none` AND no linked/mentioned PR is in an `open` state AND the thread has no claim ("I'll take this" / "working on this" / similar) that stands unaddressed or that a maintainer has confirmed as the exclusive PR for this issue. Fail if any assignee is set, or any linked/mentioned PR is open, or a maintainer has told a specific person to claim it / said they aren't looking for other contributions. A closed or merged linked PR alone does not fail this check (it's a past attempt, not a current claim). | required |
| AI-assisted contribution is not banned | The `contribution policy` line under Repo facts (live: `CONTRIBUTING.md`, `AI_USAGE_POLICY.md`/`AI_POLICY.md`, `AGENTS.md`, PR/issue templates). | Pass unless the policy states outright that AI-generated contributions are not accepted (e.g. "we do not accept AI-generated code", "AI-generated contributions are not accepted"). Disclosure requirements, "must personally understand/test/explain every change," "closed if it appears untested/not understood," silence, or an `AGENTS.md` file with no accompanying ban are all conditions, not bans, and pass. | required |
| Fits a Python/SQL/data-engineering background | The issue body, repo description, and any file paths named in the issue or thread. | Pass (rank higher) if the change is in Python or SQL, touches a data-pipeline/orchestration/CLI tool (e.g. pandas, Airflow, dbt, Spark, Kafka, or a general CLI/config-driven codebase). Rank lower if the change is primarily frontend/UI/CSS work. Never fails the issue. | preferred |

## Verdict rule

Accept only if every required check passes. Reject if any required check
fails. `unclear` on a required check counts as a fail: a first issue this
skill cannot verify is not one to recommend. Preferred checks never
change the verdict; they only order the accepted list, and the summary
names the top-ranked accepted issue's preferred-check reason.
