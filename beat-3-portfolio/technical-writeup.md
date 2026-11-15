# How I Built and Refined issue-scout to Vet Open-Source Issues

## Overview

Picking an open-source issue on a tight deadline carries a real risk: an issue can look open and available even when someone has already claimed it, opened a pull request, or merged a fix without closing the thread. To avoid spending two weeks on stale or already-resolved work, I built `issue-scout`, an automated screening tool inside Claude Code that searches GitHub, ranks candidates, and grades the top five issues against a written checklist I tuned and refined. After an early scan approved an issue that appeared to have already been fixed, I added a mandatory "Not already fixed" rule and updated the ranking order. The next day, the new rule caught and rejected an unclosed SurfSense issue (#1964) whose fix had already merged. The tool is installed and actively used in my workflow—it helped me vet `Snoww3d/jwst-data-analysis` #1396, where I opened a fix in PR #2179 (currently open, awaiting review).

## What happened

I built `issue-scout` to filter GitHub issues using search queries and a structured grading checklist tailored to small, bounded tasks that require no paid infrastructure. Each run searches GitHub, ranks the results, reads the top five issues in full, and outputs an accept or reject verdict with quoted evidence.

On 2026-10-17, I ran the first full scan (costing roughly $1). It graded five issues, rejecting four and accepting `SQLMesh/sqlmesh` #6035. However, reading the tool's own output revealed a miss: merged pull request #5949 (from 2026-09-28) appeared to have already resolved that exact issue, even though #6035 remained open. Because my starting checklist had no rule requiring the tool to check for already-merged fixes on open issues, it let #6035 pass:

```json
{"name": "Issue is one bounded piece of work", "grade": "pass", "evidence": "Single deliverable: preserve BigQuery syntax in MODEL meta expressions on `sqlmesh format`; none of fail conditions (a)-(e) apply (tension: merged PR #5949 appears to already fix it)"}
```

To fix that gap, I updated my checklist to add a required **"Not already fixed"** check—failing any issue if a merged PR or a maintainer comment shows the behavior is already resolved while the issue remains open. I also changed the ranking filter to sort by fewest comments first so issues already picked up by others wouldn't surface at the top.

**Before** (`tools/issue-select/rubric.md`):

* No "Not already fixed" check existed (`grep` count: 0). Required checks only covered repo maintenance, bounded scope, whether anyone was already working on it, and AI policy.

**After** (`~/.claude/skills/issue-scout/rubric.md`, line 40):

> Not already fixed | The linked PRs and mentioned PRs in the thread, and any maintainer comment saying the problem is resolved (live: also search merged PRs in the repo that name the issue number or its title). | Fail only if a MERGED PR states it fixes/closes this issue or this exact behavior, or a maintainer says it is already fixed on the default branch, while the issue is still open. Pass if no such merged PR or statement exists. A merged PR that only touches the same area, or that fixed a related but different problem, passes.

The change paid off on 2026-10-18 when I graded `MODSetter/SurfSense` #1964. On the surface, the issue was still open and another contributor had asked to take it. With the new rule in place, `issue-scout` checked merged pull requests referencing the issue, found that PR #2123 had already merged on 2026-10-04 and completed the work, and rejected the issue:

> Not already fixed | **fail** | PR #2123, "test(license): cover the portal routes, their rate limits and Keygen", was merged on 2026-10-04 by Cedric921. It implements the issue's whole list: route tests, rate-limit buckets, the opt-in Keygen contract test, and the docs update. The issue is still open.

Later, on 2026-10-26, a course-supplied scope patch introduced a regression on a subset of 8 saved benchmark issues: a known reject flipped to an accept across 3 of 3 re-runs, dropping accuracy to 7 of 8. Tracing the diff showed the patch had dropped a condition checking for abandoned pull requests. After briefly reverting the patch by mistake, I restored it with an eval-mode clause that preserved the strict scope conditions, bringing the full 20-issue benchmark suite back to 20 of 20 passing.

Using the updated tool as one input alongside manual verification, I selected `Snoww3d/jwst-data-analysis` #1396 (graded accept, straightforward) and opened a fix in PR #2179, which is currently open and awaiting maintainer review.

## Limitations and what's next

One real gap in `issue-scout` is that its claim check has no age limit on old comments. In testing, an unanswered claim comment from 2022 blocked `ArduPilot` #14439 as "already taken," while a 2020 claim did not block `weblate` #4192, leading to inconsistent verdicts on stale threads.

Next, I plan to add a 90-day time-window rule to the claim check—treating a claim comment without a linked pull request or maintainer follow-up after 90 days as stale—and test that rule against historical issues with old claim comments.
