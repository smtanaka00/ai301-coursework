# Procedure: how this skill grades a plan package

## Read order

1. Live mode only: read `scope.md` first and confirm the issue is inside
   the scoped repo; note any house rules it states. Eval mode: skip
   straight to step 2.
2. Read `rubric.md` and `references/evidence-guide.md` in full. List the
   rubric's checks, in table order, and its verdict rule, before reading
   anything about the package itself.
3. Read the repro evidence next, before the plan. Note, in order: every
   step, every timing or measurement, and especially any control run or
   alternate-condition result — anything that could rule a candidate
   cause in or out. This is the ground truth the plan's diagnosis must
   survive contact with; reading it first means the diagnosis is judged
   against the evidence, not the other way around.
4. Read the issue context and thread highlights next. Note any cause a
   maintainer or collaborator has already confirmed, any direction a
   maintainer has already given (a requested approach, a "please don't
   do X," a pointer to a specific file or PR), and the repo-facts
   block's stated contribution policy, including any AI-use disclosure
   requirement.
5. Read the candidate plan in full, then the candidate plan comment.
   While reading the plan, note: its stated cause, its full list of
   changes (in-scope and not-in-scope), its named files/approach, and
   its test plan. While reading the comment, note whether it engages
   the thread direction noted in step 4 and whether it discloses AI
   assistance if required.

## Evidence gathering

- **Diagnosis grounded**: from step 3's notes, list every repro-evidence
  data point that bears on causation (steps, timings, control runs).
  From step 5, pull the plan's exact stated cause. Hold them side by
  side; this pairing is the evidence for this check, not a restatement
  of either alone.
- **Scope bounded**: from step 5, pull the plan's full change list
  (every file, migration, refactor, or feature it proposes), plus
  anything explicitly marked not-in-scope or deferred with a reason.
- **Executable**: from step 5, pull the named files/functions and the
  stated approach or order of work.
- **Decisive test plan**: from step 5, pull the plan's test plan
  section, and from step 3, pull the repro evidence's steps and
  observable artifact (the thing that visibly changes between buggy
  and fixed).
- **Thread & convention comms**: from step 4, pull any explicit
  maintainer direction and the repo-facts block's contribution policy
  and AI-disclosure line. From step 5, pull the plan comment's content
  on both fronts.
- Live mode only: gather the issue-side evidence (thread highlights,
  contribution policy, any confirmed cause) from the live thread and
  repo per `references/evidence-guide.md`'s live-mode locations, in
  place of a package's repo-facts/thread-highlights blocks. The repro
  evidence comes from the student's own posted repro comment on that
  issue (or, for a house issue, the house repro pack).

## Check execution

1. Execute the five required checks in the table's order: Diagnosis
   grounded, Scope bounded, Executable, Decisive test plan, Thread &
   convention comms. Order matters only in that Diagnosis grounded is
   graded first, since a wrong cause can make the rest of the plan look
   coherent while still being built on sand — grade it on its own
   evidence regardless of how the later checks turn out.
2. For Diagnosis grounded specifically: scan every repro-evidence data
   point gathered above for one that looks like it could contradict the
   stated cause. For each one found, check whether the plan itself
   addresses it with a specific, mechanistic explanation (a stated
   reason the data point is still consistent with the cause, not a
   generic "that's probably unrelated"). A data point the plan
   addresses this way does not fail the check; a data point left
   unaddressed, or waved away without a mechanism, does. Do not let
   polish, length, or the thread's own confidence in a cause substitute
   for checking it against the repro evidence's actual data points —
   and do not fail a plan for noticing and explaining a data point that
   a weaker plan would have ignored.
3. For Scope bounded specifically: for each item in the plan's change
   list, ask "does fixing the named bug require this?" An item the
   issue didn't ask for and the fix doesn't need fails the check, unless
   the plan explicitly defers it (scoping down, not up, is not a
   failure).
4. For Executable specifically: pass when a specific file or module is
   named and the mechanism/approach is decided, even if the exact
   function or line is left to be pinned down during tracing — so long
   as the plan shows a concrete way to find it (working debug output, a
   named code path), not just an intention to go look. Fail the moment
   the approach itself is left open ("somewhere," "maybe," an unresolved
   choice between approaches, no module named) — do not average this
   against the parts of the plan that are concrete.
5. For Decisive test plan specifically: the test plan must name the
   specific observable thing that changes for this bug, not a general
   process (running a suite) or a subjective feeling. Map it against the
   repro evidence's own observable artifact from step 3; if the test
   plan doesn't name that artifact or an equivalent, it fails.
6. For Thread & convention comms specifically: grade AI disclosure as a
   fail only when the repo-facts block's contribution policy states a
   disclosure requirement and the comment contains no disclosure —
   silence in the policy is not a requirement. Grade thread direction as
   a fail only when the thread contains an explicit, specific direction
   (not just discussion) that the comment contradicts or never
   addresses.
7. When evidence for a check is genuinely absent from the package (not
   merely unexamined), grade it `unclear` and say what's missing in the
   evidence field; do not guess, and do not silently grade it `pass`.
8. A check already graded does not get re-opened by reading a later
   section; if new evidence surfaces that bears on an earlier check,
   note it but do not revise a check already recorded without stating
   why.

## Verdict assembly

1. Apply the rubric's verdict rule exactly: accept only if all five
   required checks graded `pass`. Any `fail` or `unclear` on any one
   required check produces `reject`, regardless of how the other four
   graded.
2. For the output's `evidence` field on each check, quote the specific
   fact that decided it: the contradicting repro-evidence data point for
   a Diagnosis-grounded fail, the extra change-list item for a
   Scope-bounded fail, the open decision's exact wording for an
   Executable fail, the test plan's exact wording (or its absence) for a
   Decisive-test-plan fail, the thread direction or disclosure-policy
   line for a Thread-and-convention fail.
3. Live mode only: after assembling the verdict, hold the draft plan
   comment against `voice-guide.md` and report any broken rule in the
   summary; this never changes the verdict unless the rubric names a
   check that reads the voice guide (it does not, this week).
