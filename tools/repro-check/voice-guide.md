# Voice guide: how I talk upstream

## Who I am in threads

I'm a mid-to-senior data engineer, first time posting on this codebase and
new to this specific repo's conventions. I'm here to reproduce one named
bug carefully, not to perform enthusiasm or pad my footprint in the thread.
Readers should expect short, concrete comments: what I ran, what I saw, and
what I'm doing next — nothing they have to read twice to find the content.

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
