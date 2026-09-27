# Evidence guide: where proof lives in a reproduction package

## Environment

**Where it lives.** In an eval bundle: the repro report's own environment
line/section (usually the first thing after "Steps"), read against the
`## Repo facts` block's `latest release` line and anything the `## Issue`
section names (a specific version, OS, or build the reporter targeted). In
live mode: the student's draft repro report, read against the issue thread
on GitHub (the issue body, and any maintainer comment narrowing the target
version/platform).

**What good looks like.** Every runtime detail needed to place the attempt
is named explicitly — tool/library version, OS/platform, and any config or
build flag the issue's bug depends on (release vs. debug build, backend,
locale, etc.). If the version or platform used differs from what the issue
targets, the report says so in plain words ("tested on 1.5.3; the issue is
confirmed on latest/main") rather than presenting it as if it matched. A
report that lists version numbers with no OS, or an OS with no version, is
still incomplete if the issue's behavior depends on the missing piece.

## Steps

**Where it lives.** The repro report's numbered/ordered steps or command
sequence. In live mode, also check whether any file, config, or fixture the
steps reference is actually included inline, linked, or otherwise shareable
— not just named.

**What good looks like.** A stranger with the stated environment could
follow the steps verbatim and land on the same trigger, with no missing
setup step, no reference to state that only exists on the reporter's own
machine (a private monorepo, an unshared config file, "my local branch"),
and no unrelated setup bundled in that isn't part of triggering the bug.
Steps that omit a detail the issue itself flags as load-bearing (e.g. a
platform-specific driver flag on a platform-specific issue) are not
followable even if every command shown "works."

## Behavior shown

**Where it lives.** The artifact block in the repro report — terminal
output, an error trace, a log excerpt, a described screenshot — read
side-by-side with the `## Issue` section's description of the failure
(error type, symptom, exit behavior) and any narrowing comment in `## Thread
highlights`.

**What good looks like.** The artifact is unedited (a real captured
output, not a paraphrase or a cleaned-up summary) and it shows the *same*
failure the issue names — same error type, same symptom, same qualitative
outcome (a crash artifact for a reported crash, not a graceful validation
error at a different exit code; a wrong-value output for a reported
wrong-value bug, not a different wrong value from a different input). An
artifact produced by a modified trigger, a different input shape, or a
substituted argument is evidence about something adjacent, not about the
issue, even when it looks superficially similar or is narrated confidently
as a match — read the artifact itself, not the caption on it. A real
attempt whose artifact honestly shows the issue's behavior *not*
occurring counts as showing behavior too, provided it's a genuine capture
from a real run rather than an absence of any attempt — see "Honesty"
below for how that case is graded on its own terms.

## Honesty

**Where it lives.** The repro report's own expected/actual (or
hypothesis/observation) statement, read against what the "Behavior shown"
artifact actually contains.

**What good looks like.** "Actual" is a direct description of what the
shown artifact contains, in the reporter's own words, not a restatement of
the issue's claim or a conclusion the artifact doesn't itself support. An
honest cannot-reproduce — "I did not see the reported behavior; here is
what I saw instead, and here is what differs from the issue's setup" — is
exactly as valid as a successful repro, provided it's backed by a real
attempt and artifact. What fails this family is confidence outrunning
evidence: a diagnosis, a root cause, or a "confirmed" claim stated with no
artifact behind it at all, or an artifact that shows something narrower or
different than what's claimed about it.

## Comms

**Where it lives.** The claim comment and repro report text themselves,
read against two things: the repo-facts block's stated bug-report template
asks and contribution policy (including any AI-use disclosure requirement),
and — in live mode only — the student's own `voice-guide.md`.

**What good looks like.** The claim comment names the specific issue and
promises investigation only (never a fix, a date, or a "guaranteed"
timeline). The repro report reads as specific to this bug: concrete
versions, concrete commands, concrete artifacts — not text generic enough
to paste into a different issue unchanged, not a raw unformatted dump of a
terminal pasted into prose with no framing, not enthusiasm or flattery
standing in for content. Where the repo's stated policy requires disclosing
AI assistance, the text discloses the tool and the extent of the help; where
the policy is silent or merely permissive, no disclosure is required to
pass. In live mode, a draft that breaks a rule from the student's own
`voice-guide.md` gets that rule quoted back in the grading summary, even on
checks the rubric itself grades independently of that file.
