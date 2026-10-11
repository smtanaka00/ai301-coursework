# Procedure: how this tool grades a PR package

## Read order

1. Live mode only: read `scope.md` first and confirm the PR targets the
   scoped repo; note the house rules it states (fork-branch naming,
   one-PR-per-issue, template always used). Eval mode: skip straight to
   step 2.
2. Read `rubric.md` and `references/evidence-guide.md` in full. List the
   rubric's four checks, in table order, and its verdict rule, before
   reading anything about the package itself.
3. Read the plan context next, before the diff: the plan's stated
   in-scope list, anything named not-in-scope or deferred, its test
   plan, and its `## Deviations` notes if any exist. This is the
   contract the PR is being held to; reading it first means the diff
   gets judged against what was promised, not the other way around.
4. Read the issue and thread highlights: any explicit maintainer
   direction already given, and the repo-facts block's stated PR
   template sections and contribution/AI-use policy (live mode: the
   repo's `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`).
5. Read the candidate PR last, in this order: commit list, full unified
   diff, test evidence, then title and description. Reading the diff
   before the description means the description's claims get checked
   against what the diff actually shows, not taken at face value.

## Evidence gathering

- **Plan fidelity**: from step 3, list every file/hunk the plan's scope
  names as in-bounds, plus every `## Deviations` entry. From step 5,
  list every file and hunk the diff actually touches. Hold the two
  lists side by side: anything in the diff's list not covered by
  either the scope list or a deviation note is the evidence for a
  fail. Separately, pull the description's own fidelity claims from
  step 5 and check each one against the diff's actual contents.
- **Decisive test evidence**: from step 3, pull the plan's test plan
  and the original repro's observable artifact. From step 5, pull the
  PR's test-evidence section (before/after output) and the reported
  results of the repo's own named checks. Pair the evidence's artifact
  against the plan's named artifact; a mismatch (evidence shows a
  different path than the one the plan said would flip) is the
  evidence for a fail.
- **Reviewable diff**: from step 5, walk every hunk in the diff and the
  commit list. Record any hunk that is not plainly in service of the
  named fix: commented-out code, debug output, dead code, stray TODOs,
  pure-formatting churn, or an unrelated change.
- **Standards & disclosure**: from step 4, pull the template's required
  sections and the repo's contribution/AI-use policy. From step 5, pull
  the description section-by-section and check each required section
  for real content, confirm the issue is named, and look for an AI-use
  disclosure. Live mode: a disclosure is required regardless of what
  step 4's policy says; eval mode: required only when the repo-facts
  block's policy states it.
- Live mode only: gather the issue-side evidence (template, contribution
  policy, thread direction) from the live repo and thread per
  `references/evidence-guide.md`'s live-mode locations, in place of a
  package's repo-facts/thread-highlights blocks. The diff is `git diff
  main...HEAD` (three dots) on the student's branch; the plan is their
  `plan.md`; the test evidence is their `test_evidence.md`; the title
  and description are their `pr_draft.md`.

## Check execution

1. Execute the four required checks in the table's order: Plan
   fidelity, Decisive test evidence, Reviewable diff, Standards &
   disclosure. Order matters only in that Plan fidelity is graded
   first, since a diff that silently drifted from the plan can still
   produce clean-looking tests and a tidy description; grade each check
   on its own evidence regardless of how the others turn out.
2. For Plan fidelity specifically: a deviation note that honestly
   explains a change is not a failure, even if the change is large;
   only an unaccounted-for change, or a description claim the diff
   contradicts, fails this check. Check both directions — the diff
   doing more than the plan, and the description claiming more than
   the diff delivers (a promised piece quietly missing is the same
   failure in the other direction).
3. For Decisive test evidence specifically: "tests pass" with no
   artifact named is not evidence; evidence that exercises a path the
   fix didn't change (a control case, or an adjacent feature) is not
   evidence for this fix, even if it is honestly reported. A reported
   failure with a stated reason (a documented pre-existing issue) does
   not fail this check; an omitted or unreported check does.
4. For Reviewable diff specifically: grade the diff as it stands, not
   as it would read after a hypothetical cleanup. One piece of debris
   is enough to fail the check, regardless of how correct the core fix
   is elsewhere in the same diff.
5. For Standards & disclosure specifically: a template section with
   boilerplate placeholder text or an unchecked box left for the
   reviewer counts as not filled. Grade the AI-disclosure fail
   condition per step 4's gathering rule (live mode always requires
   it; eval mode requires it only when the repo's stated policy says
   so). Grade thread-direction conflict as a fail only when the thread
   contains an explicit, specific instruction the PR visibly
   contradicts or ignores, not general discussion.
6. When evidence for a check is genuinely absent from the package (not
   merely unexamined), grade it `unclear` and say what's missing in the
   evidence field; do not guess, and do not silently grade it `pass`.
7. A check already graded does not get re-opened by reading a later
   section; if new evidence surfaces that bears on an earlier check,
   note it but do not revise a check already recorded without stating
   why.

## Verdict assembly

1. Apply the rubric's verdict rule exactly: accept only if all four
   required checks graded `pass`. Any `fail` or `unclear` on any one
   required check produces `reject`, regardless of how the other three
   graded.
2. For the output's `evidence` field on each check, quote the specific
   fact that decided it: the unaccounted-for file/hunk for a Plan
   fidelity fail, the exact vague phrase or the wrong-path artifact for
   a Decisive-test-evidence fail, the specific debris line or hunk for
   a Reviewable-diff fail, the missing section or missing disclosure
   for a Standards-and-disclosure fail.
3. Live mode only: after assembling the verdict, hold the draft PR
   title and description against `voice-guide.md` and report any broken
   rule in the summary; this never changes the verdict unless the
   rubric names a check that reads the voice guide (it does not, this
   week).
