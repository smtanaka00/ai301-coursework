# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

smtanaka00

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61#issuecomment-5842458154

Claiming this one. The health probe in `api/routes/health.py` runs the literal string
`"SELECT 1"` through the session directly; SQLAlchemy 2.x requires textual SQL to be wrapped
in `sqlalchemy.text()`, which matches the `ArgumentError` this issue describes. I'm going to
reproduce that failure against the current `main` branch and report back with the
environment, the exact call that trips it, and the unedited error output before opening a PR.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61#issuecomment-5851933800

Reproduced on `main` (`f89c06f`).

Environment: Python 3.12.0, SQLAlchemy 2.1.1, asyncpg 0.31.0, PostgreSQL 16 (postgres:16-alpine), macOS 26.5.2 (arm64).

Isolated the exact call `health.py`'s postgres check makes, through the app's own `get_db()`, with nothing else from the app involved:

```python
import asyncio
from core.database import get_db

async def main():
    async for session in get_db():
        await session.execute("SELECT 1")

asyncio.run(main())
```

Output:

```
Traceback (most recent call last):
  ...
  File ".../sqlalchemy/sql/coercions.py", line 594, in _no_text_coercion
    raise exc_cls(
sqlalchemy.exc.ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')
```

Control, same call wrapped in `sqlalchemy.text()`, on the same session:

```
control result: 1
```

Expected: the health probe's postgres check succeeds against a reachable database. Actual: `session.execute("SELECT 1")` raises `ArgumentError` under SQLAlchemy 2.x, exactly as this issue describes; wrapping the identical string in `text()` on the same connection succeeds, confirming the raw-string call is what's failing, not the database connection itself.

Also hit the running `/health` endpoint directly and confirmed the same `ArgumentError` in the app's own logs, plus a 503. That run's `redis` check also fails, but on an unrelated `AttributeError` (`Settings` has no `redis_host`) — a separate pre-existing issue, not this one.

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Smoke run, `--limit 3 --workers 3` (before spending on a full run): **3/3** agreement
   (partial — no bar/floor on a partial run).
2. Full run (first draft rubric): **17/20** agreement, category floor met in every category.
   Disagreements: `pkg-05` (gold accept, my rubric rejected — "Minimal code isolation"),
   `pkg-09` (gold accept, my rubric rejected — "Explicit failure proof"), `pkg-10` (gold
   accept, my rubric rejected — "Explicit failure proof").
3. Partial run, `--only pkg-05,pkg-09,pkg-10,pkg-02,pkg-08,pkg-16,pkg-17,pkg-18,pkg-06` (the
   3 disputed packages plus 6 canaries — one from each single-package-relevant category the
   two loosened checks could touch, `wrong-target` and `unfollowable-comms`): **9/9**
   agreement — confirmed the revision fixed all three disputes without flipping anything
   that previously agreed.
4. Full run (revised rubric): **20/20** agreement, category floor met in every category.
5. Full confirming run with `--save-run eval-run.txt`: **20/20** agreement, category floor
   met in every category — this is the run recorded in the committed `eval-run.txt`
   (`agreement: 20/20 scored items  (bar: 18/20: PASS)`).

**Package analysis**

`pkg-09` (`sharkdp/fd#2033`) — gold label: **accept**. My final rubric's verdict: **accept**
(agrees). The package is an honest cannot-reproduce report: the student made a real,
well-documented attempt at the issue's scenario 2 (`--exec-batch` command reordering when
the argument-size limit is hit), ran it multiple times with a control variant, got a
negative result every time, and named exactly what likely differed from the issue's trigger
conditions (uniform file-name lengths hitting the same flush boundary, a 2 MiB `ARG_MAX`
they couldn't force lower). My first-draft rubric's "Explicit failure proof" check demanded
the artifact show *the same failure the issue names* — a condition a genuine
cannot-reproduce report can never satisfy by construction, so the first draft rejected it
alongside real no-evidence packages (see Run history, run 2). Rereading the evidence
guide's "Honesty" family — "an honest, evidenced cannot-reproduce is exactly as valid as a
successful repro" — made clear the check needed to grade the *honesty of the attempt*, not
demand a match it structurally cannot produce, so I rewrote it to accept a genuine attempt
with a real artifact and an honest "not observed" framing as its own passing path.

**Check rationale**

From `rubric.md`, the "Explicit failure proof" check's pass condition (as uploaded to
`tools/repro-check/rubric.md`):

> Pass if either: (a) there is an unedited output/log/trace artifact that shows the same
> failure mode the issue names (same error type or symptom), or (b) the report documents a
> genuine reproduction attempt with a real captured artifact and honestly states the
> issue's failure was not observed, naming what was observed instead (an honest
> cannot-reproduce). Fail if there is no real attempt or artifact at all, the artifact only
> shows normal operation with no honest cannot-reproduce framing, or the artifact reflects a
> different trigger/input/version than the one the issue names, presented as if it matched.

It's shaped this way because my first pass only had branch (a), which conflated two
different things under one test: "is there real evidence at all" and "does the evidence
match the issue." Those need different answers for a cannot-reproduce report — real
evidence, honestly not matching, is the entire point — so branch (a) alone mis-rejected
`pkg-09` and `pkg-10` (both genuine, well-instrumented cannot-reproduce attempts) while
correctly rejecting true no-evidence packages. Branch (b) was added, evidence-anchored to
the same two facts every branch-(a) grade already reads (a real artifact, and whether it's
honestly framed against the issue's claim), so a package still fails outright if it fakes an
attempt or narrates a mismatched artifact as a match — that's still branch (a)'s job, now
read against the "wrong-target" packages like `pkg-02`, `pkg-08`, `pkg-16`, `pkg-17`, which
all still correctly reject.

**Trade-offs**

The same revision round also loosened "Minimal code isolation": the first draft required
every input the repro steps depend on to be "shown or shareable," which failed `pkg-05`
because its `env.yml` content was described in exact prose ("a valid `dependencies:` list
plus a `category:` section") rather than pasted verbatim — a real signal was punished for a
formatting choice, not a substance gap. I loosened it to also accept an input "described
precisely enough (exact values/fields named) that a stranger could reconstruct an identical
input without guessing." That buys back `pkg-05` but gives up a small amount of strictness:
a config that's paraphrased vaguely, without exact field names or values, could in principle
still slip past a lenient grading run even though a stranger couldn't actually rebuild it
byte-for-byte. I accept that miss deliberately — the check still requires *exact* values to
be named, not just a general description, and I confirmed the loosening didn't cost anything
elsewhere by re-running canaries from both categories these two checks touch
(`wrong-target`: `pkg-02`, `pkg-08`, `pkg-16`, `pkg-17`; `unfollowable-comms`: `pkg-06`,
`pkg-18`) with `--only` before spending the confirming full run — all six still agreed (see
Run history, run 3), so nothing that previously passed flipped.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
