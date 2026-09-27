# Rubric: is this reproduction package ready to post?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Environment determinism | The report's environment record (runtime/tool/library version, OS/platform, build type, backend, relevant config), read against the version/target the issue names or the repo-facts block's latest release. | Pass if exact versions and platform details needed to place the attempt are stated, AND if the version/config used differs from what the issue targets, that deviation is named explicitly (not left silent). Fail if there is no environment record at all, or a used version/config differs from the issue's target without being disclosed as a deviation. |  required |
| Minimal code isolation | The reproduction steps/commands/config in the report. | Pass if the steps are the smallest self-contained set that triggers the exact behavior — a focused command/config sequence with no unrelated setup or unrelated logic bundled in, and every input the steps depend on is either shown verbatim, linked, or described precisely enough (exact values/fields named) that a stranger could reconstruct an identical input without guessing. Fail if the repro depends on code, data, or config that only exists on the reporter's own machine and isn't reconstructible from what's written (e.g., an unshared private-monorepo config referenced but not described), or bundles unrelated logic that obscures the trigger. |  required |
| Explicit failure proof | The artifact block in the report (terminal output, log, error trace), read against the exact failure the issue describes. | Pass if either: (a) there is an unedited output/log/trace artifact that shows the same failure mode the issue names (same error type or symptom), or (b) the report documents a genuine reproduction attempt with a real captured artifact and honestly states the issue's failure was not observed, naming what was observed instead (an honest cannot-reproduce). Fail if there is no real attempt or artifact at all, the artifact only shows normal operation with no honest cannot-reproduce framing, or the artifact reflects a different trigger/input/version than the one the issue names, presented as if it matched. |  required |
| Expected vs. actual outcome | An explicit expected/actual (or hypothesis/observation) contrast in the report, read against what the failure-proof artifact actually shows. | Pass if both expected and actual are stated in the reporter's own words and "actual" matches what the artifact shows — including an honest, evidenced cannot-reproduce that says what happened instead. Fail if "actual" is asserted without being tied to the shown artifact, or is inconsistent with (over-claims beyond) what the artifact shows. |  required |
| Clean upstream voice | The claim comment and repro report text, read on their own terms (this check is self-contained; it does not read `voice-guide.md` — see note below). | Pass if the text is specific to this issue (names concrete details a generic comment couldn't), stays factual and measured, and contains no unformatted raw log dumps pasted into prose, no content-free enthusiasm/flattery/emoji substituting for substance, and no line generic enough to paste unmodified into a different issue. Fail if any of those appear. |  required |
| No premature fixes | The claim comment and repro report text. | Pass if the text stays scoped to investigating/reproducing the current failure and promises nothing beyond that. Fail if it promises a fix, a delivery date, or a "guaranteed" timeline, self-assigns with a completion guarantee, or presents an untested/unvetted fix or patch as already verified. |  required |
| Repo conventions & disclosure | The repo-facts block's contribution/AI-use policy, read against the claim comment and repro report text. | Pass if the policy has no AI-disclosure requirement, or has a conditional/permissive one that the text satisfies, or (when disclosure is required) the text discloses AI assistance and its extent. Fail if the policy states AI-assistance disclosure is required and the text does not disclose it. |  required |

## Verdict rule

Accept only if every required check above grades `pass`. Any required check
graded `fail` holds the package (`reject`). `unclear` on a required check
counts as `fail` — evidence I cannot verify is evidence that is not ready to
post. There are no `preferred` checks in this rubric; every row above gates
the verdict.

**Claim-only draft (live mode).** Only three checks can be evaluated from a
claim comment alone, before a repro report exists: *clean upstream voice*,
*no premature fixes*, and *repo conventions & disclosure*. The other four
checks need the repro report and are graded `unclear` / "not yet applicable:
claim-only draft" per `SKILL.md`, and are excluded from the verdict on a
claim-only draft. The claim-only verdict is `accept` only if all three
applicable checks pass.

**Why "clean upstream voice" does not read `voice-guide.md`.** `SKILL.md`
tells the grader to ignore `voice-guide.md` entirely in eval mode ("voice is
personal and carries no gold labels; the universal communication-quality
checks live in the rubric"). A check whose evidence is "compliance with
`voice-guide.md`" would have nothing to quote against an eval bundle and
would grade `unclear` — i.e. `fail` — on every eval package, rejecting the
whole set. So this check's pass condition is written to stand on its own,
evaluable from the package text alone in both modes. `voice-guide.md` still
does real work in live mode: `SKILL.md` step 6 holds every live draft against
it and reports broken rules by name in the summary, independently of this
check and without needing the rubric to reference it.
