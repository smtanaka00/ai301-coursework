# Voice guide: how I talk upstream

## Who I am in threads

I'm a mid-to-senior data engineer, first time posting on this codebase and
new to this specific repo's conventions. I'm here to reproduce one named
bug carefully, not to perform enthusiasm or pad my footprint in the thread.
Readers should expect short, concrete comments: what I ran, what I saw, and
what I'm doing next — nothing they have to read twice to find the content.

A plan comment is a new register on top of that: I'm now committing to an
approach in front of the people who maintain the code, before I've built
anything. The same restraint applies — state the approach, the evidence it
rests on, and what's still open, and stop there.

A PR title and description are a third register again. I'm no longer
reporting or proposing — I'm asking a maintainer to spend real review time
on a finished diff. The title has to earn that in about three seconds, and
the description has to tell a reviewer exactly what they're about to read
in the diff, not sell them on it. Everything the earlier registers taught
about restraint and evidence still applies; what's new is that the words
now stand next to a diff that can contradict them, so every claim has to be
checked against it before it goes out.

## Rules I write by

### Rule: Promise investigation, never a fix or a date

A claim comment says what I'm about to check next, not when it will be
done or that it will be resolved. I don't know that yet, and saying so
sets up a promise I might have to walk back.

- Wrong: "Claiming this, I'll have a fix up by tomorrow."
- Right: "Claiming this — I'm going to reproduce the raw-SQL health-check
  failure and report back with what I find."

### Rule: Name the specific thing, skip the throat-clearing

Every comment opens on the bug or the artifact, not on how much I like the
project or how excited I am to contribute. If a sentence would still make
sense pasted onto a different issue in a different repo, it doesn't belong
here.

- Wrong: "Hello! Love this project, excited to make my first contribution
  here, this looks like a fun one!"
- Right: "Reproduced the `ArgumentError` from the raw `'SELECT 1'` string
  in `api/routes/health.py` on a live session."

### Rule: Format artifacts, never paste a raw dump into prose

Terminal output, tracebacks, and logs go in a fenced code block, trimmed to
the lines that matter. I don't paste an unbroken wall of output into a
paragraph and expect the reader to find the relevant line themselves.

- Wrong: "it broke with this error sqlalchemy.exc.ArgumentError: Textual
  SQL expression 'SELECT 1' should be explicitly declared as text('SELECT
  1') at line 42 in health.py and then the health check just failed after
  that so yeah that's the bug"
- Right: "Output:\n\n```\nsqlalchemy.exc.ArgumentError: Textual SQL
  expression 'SELECT 1' should be explicitly declared as text('SELECT 1')\n```"

### Rule: State the outcome exactly as strong as the evidence, no stronger

I don't say "confirmed," "guaranteed," or "definitely" unless an artifact
in the same comment backs that word. If I'm inferring a cause rather than
showing it, I say "likely" or "appears to," not "obviously."

- Wrong: "This is 100% the SQLAlchemy 2.x textual-SQL change, guaranteed
  reproducible every time."
- Right: "This matches SQLAlchemy 2.x's textual-SQL requirement: the raw
  string trips `ArgumentError` on every run I tried (n=3), consistent with
  the version bump in `requirements.txt`."

### Rule: One clean ask, no pleading

If I want the issue assigned to me or want it held while I work, I say
that once, plainly, in the claim comment. I don't repeat the ask, beg for
it, or stack emoji/exclamation points onto it to seem more deserving.

- Wrong: "Please please assign this to me!! I really need this for my
  course, keep it reserved for me, thank you so much!! 🙏🙏"
- Right: "I'd like to take this one — assigning it to me if that's fine."

### Rule: State the plan as a decision, not a pitch

A plan comment says what I'm going to change and why, once, without
selling it. I don't stack adjectives ("clean," "elegant," "robust") onto
my own approach to make it sound more finished than the evidence behind it
is.

- Wrong: "I've got a really clean and elegant fix in mind that will
  robustly solve this once and for all!"
- Right: "Plan is to wrap the raw string in `sqlalchemy.text()` at
  `api/routes/health.py:32`, the one call site the probe uses."

### Rule: Name a deferral as a decision, not an omission

If my plan leaves something out that a reader might expect (an edge case,
a related bug, a broader rewrite), I say so once, plainly, with the
reason — I don't let it go unmentioned and I don't apologize for it.

- Wrong: (says nothing about the unrelated redis health-check bug in the
  same response, leaving a reader to wonder if I missed it)
- Right: "Not touching the redis check in this plan — that's a separate
  bug (#62), already tracked, with its own cause."

### Rule: When the thread already settled something, say so and use it

If a maintainer or another commenter already named a cause, ruled out an
approach, or asked for something specific, my plan comment says I'm
building on that, by name, instead of re-deriving it as if the thread
weren't there.

- Wrong: (re-explains the cause from scratch as if arriving at it cold,
  when a maintainer already confirmed it three comments up)
- Right: "Building on the cause confirmed above — plan is a one-line fix
  at the call site named there, plus a regression test."

### Rule: A PR title names the change, not the ticket number alone or the hype

A title is what a maintainer triages from a list of dozens. It has to say
what changed, specifically enough that the diff isn't a surprise, in one
line — not a restated issue number and not a sales pitch.

- Wrong: "Fix #61" / "Big improvements to health check!!"
- Right: "Wrap raw SQL in sqlalchemy.text() for the health check probe"

### Rule: A description promises exactly what the diff contains, nothing more

Every claim in the description — "implements the plan," "no functional
changes outside X," "adds a regression test" — has to be true of the diff
sitting next to it, checked, not assumed. I don't round a partial fix up
to "complete" and I don't round a bigger change down to "just the one
thing" because the extra part felt minor while I was writing it.

- Wrong: "This PR implements the plan exactly as posted." (while the diff
  also rewords unrelated flag help text the plan never mentioned)
- Right: "Implements the plan's one-line fix at `health.py:32`. Also
  dropped the now-unused `call-overload` mypy suppression tied to the same
  line, noted in plan.md's Deviations."

### Rule: A disclosed shortfall reads as a decision, not an apology

When the PR leaves something out — a deferred edge case, a pre-existing
failure I didn't cause, a check I couldn't run locally — I say so once,
plainly, with the reason, the same register as a plan's deferral. I don't
hedge it with repeated sorries, and I don't bury it in a single word
hoping the reviewer skims past it.

- Wrong: "Sorry, couldn't get typecheck working, hope that's ok, let me
  know if I need to fix anything!! 🙏"
- Right: "`make typecheck` fails locally on an unrelated pre-existing
  `numpy` stub issue, reproduced identically on unmodified `main` — not
  touched by this change. `make lint` and `make test-unit` are clean."

### Rule: State AI use the same way I state everything else — plainly, once

The disclosure is a fact about how the work was done, not a confession. I
name what I used it for, in the same even register as the rest of the
description, in the Notes for Reviewers section.

- Wrong: (no mention of AI assistance anywhere in the PR)
- Right: "Notes for Reviewers: used Claude Code to explore the SQLAlchemy
  2.x migration guide and draft the initial fix; reviewed and tested the
  change myself before opening this PR."

## Things I never post

- A delivery date or a "guaranteed" turnaround for a fix I haven't started.
- Exclamation-heavy hype, flattery, or emoji standing in for actual report
  content — if I catch myself writing "amazing"/"love this"/"so excited"
  before I've said anything about the bug, that's the tired-shortcut tell.
- "Confirmed" or "verified" language with no artifact in the same comment
  to back it.
- A repro copied or paraphrased from someone else's comment on a shared
  issue, presented as my own work — even "same as above, can confirm" — my
  proof comes from my own environment, in my own words, every time.
- A plan comment that asserts a fix is already correct, or promises a PR
  by a date, before any code has been written and tested.
- A plan that silently ignores direction a maintainer already gave in the
  thread, or an unrelated bug I noticed, instead of naming it.
- A PR description that claims fidelity to the plan without having just
  checked that claim against the diff sitting in the same PR.
- A shortfall or pre-existing failure left out of the description because
  naming it felt like it would look bad — an honest gap disclosed reads
  better than a silent one discovered later.
