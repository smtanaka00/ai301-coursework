# Evidence guide: where evidence lives in a plan package

## Diagnosis and grounding

Where it lives: in a bundle, the plan's `## Diagnosis` or `### Cause`
section states the stated cause; the `## Repro evidence` block holds
the ground truth, usually as a numbered list of steps, each with a
command/action and its result, and often one or two steps that are
explicitly a control or alternate-condition run (same input, one
variable changed). In live mode, the stated cause is in the student's
own `plan.md` (or draft plan text), and the repro evidence is their own
posted repro comment on the issue from week 2 (for a house issue, the
house repro pack).

What good looks like: the stated cause explains every step's result,
including the control run, with nothing left over — either because the
result is directly consistent with the cause, or because the plan
offers a specific, mechanistic reason the result is still consistent
(not a generic "unrelated" wave-off). A diagnosis that only explains
the happy-path steps and ignores a control run, or hand-waves one away
with no stated mechanism, is not grounded, however confidently it
reads — the package calib-03 (and pkg-01/07/11/16) exist specifically
to reward checking the control run, not the confidence of the prose. A
plan that notices a data point that looks awkward for its own cause and
explains specifically why it still fits (pkg-14's cache-state
explanation for why one reattach after a cache clear comes back clean)
is doing the grounding work this check rewards, not failing it.

## Scope

Where it lives: in a bundle, the plan's `## Scope` or `### Changes`
section, usually with an explicit in-scope/not-in-scope split or a
numbered change list. In live mode, the same section of the student's
`plan.md`.

What good looks like: one bounded change that does only what the named
bug requires. A deferred extra ("I'm not doing X, because Y") scopes
the plan down and is good practice, not a problem. An added extra
(a refactor, a migration, a new option, a redesign "while I'm in
there") that the issue never asked for and the fix doesn't need is
scope creep, even when the core fix inside it is correct.

## Executability

Where it lives: in a bundle, the plan's named files/functions (often
inside the Scope/Changes section) and its stated approach or order of
work. In live mode, the same content in `plan.md`.

What good looks like: a stranger could open the named file or module
and start today without asking the author anything. This does not
require the exact function or line to already be named — a plan that
points at a specific module and a decided mechanism, and shows a
concrete way to find the exact site (already-working debug output, a
traced code path), is executable even if it says the precise line will
be "pinned down in the PR." Hedge words ("somewhere," "maybe,"
"whichever is easier," an unresolved choice between two libraries or
layers, or which layer is even responsible left as an open question)
mark a decision the plan hasn't actually made yet, even if the rest of
the document is confident and detailed.

## Test plan

Where it lives: in a bundle, the plan's `## Test plan` section. In live
mode, the same section of `plan.md`, which the assignment asks to be
the student's own week-2 repro steps re-run with what they expect to
see after the fix.

What good looks like: a plan names the specific, observable thing that
flips when the fix lands — tied to the exact artifact or behavior the
repro evidence showed (a color that updates, an exit code that
changes, output that now matches a control case). "Run the test suite"
or "should feel faster" names a process or a feeling, not an outcome,
and fails this check even when the rest of the plan is otherwise ready
(the worksheet's calib-04 is built to isolate exactly this).

## Honesty

Where it lives: in a bundle, wherever the plan names risks, unknowns,
or an explicit deferral (often inside Scope or a `## Risks` section).
In live mode, the same content in `plan.md`, plus its `## Deviations`
heading after a build (filled in once the build is done, not before).

What good looks like: a stated unknown or a deferred item with its
reason attached reads as honest, not as a weakness — it is the
opposite of a drive-by addition, because it names something the plan
is choosing not to do and says why. False confidence looks like a risk
or an unknown that the plan's own evidence doesn't actually support
being silent about (for example, asserting a fix is complete when a
step of the repro evidence was never addressed). This guide's Honesty
heading informs how you read Scope and Diagnosis; the rubric has no
check named "Honesty" on its own this week, because the eval set's
five categories are covered by the other five checks and a separate
honesty gate would double-count the same evidence.

## Comms

Where it lives: in a bundle, the `## Thread highlights` section (any
explicit maintainer/collaborator direction, or a confirmed cause) and
the `## Repo facts` block's stated contribution policy line (including
any AI-use disclosure requirement), read against the `## Candidate plan
comment`. In live mode: the live issue thread (via `gh issue view
<n> --repo <scope.md repo> --comments` or the web) for maintainer
direction, the repo's `CONTRIBUTING.md`/README for its contribution and
AI-disclosure policy, and the student's draft `comment.md`.

What good looks like: the comment reads as if the author read the
thread before posting — it doesn't repeat a question a maintainer
already answered, doesn't contradict a direction already given, and
names the fix it actually settled on if the thread was debating
options. When the repo's policy requires disclosing AI assistance, the
comment says so in plain language; silence in a repo whose policy says
nothing about it is not a failure, but silence in a repo whose policy
requires it is (pkg-20 is built to isolate exactly this, independent of
how good the plan underneath it is).
