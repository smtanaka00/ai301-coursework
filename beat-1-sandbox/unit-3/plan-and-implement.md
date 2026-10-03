# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

smtanaka00

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61#issuecomment-5971292356

> Plan, building on my reproduction above: wrap the raw string in `sqlalchemy.text()` at the one call site that raises, `api/routes/health.py:32`. A repo-wide grep (`grep -rn '\.execute(\s*["\']' --include='*.py' .`) found only this one bare-string `execute()` call, so this is a single-line fix: add the `text` import, call `db.execute(text("SELECT 1"))` instead of `db.execute("SELECT 1")`. No other line in the function changes.
>
> Not touching the `redis` check in the same route — that's a different failure (`'Settings' object has no attribute 'redis_host'`), already tracked as #62, with its own cause.
>
> Test plan: re-run my isolated repro script against the fixed code (expect it to return `1` with no `ArgumentError`, like my control run already did) and re-hit `GET /health` (expect the `postgres` key to flip to `"healthy"`; the endpoint will still return 503 overall until #62 also lands, since the redis check fails independently).
>
> One more thing the fix touches: `pyproject.toml`'s mypy override for this module disables `call-overload` alongside `attr-defined` (#62's suppression) — `call-overload` is this bug's mypy signature, so I'll drop it from the override list per the seeded-bug convention in CONTRIBUTING.md, leaving `attr-defined` in place for #62.

---

## Your branch

**Branch**

fix/61-wrap-select-in-text

**Evidence**

Before (from my Unit 2 reproduction, posted at
https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61#issuecomment-5851933800):

Isolated call, same statement the route makes, through the app's own `get_db()`:

```python
import asyncio
from core.database import get_db

async def main():
    async for session in get_db():
        await session.execute("SELECT 1")

asyncio.run(main())
```

```
Traceback (most recent call last):
  ...
  File ".../sqlalchemy/sql/coercions.py", line 594, in _no_text_coercion
    raise exc_cls(
sqlalchemy.exc.ArgumentError: Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')
```

Control, same call wrapped in `text()`, on the same session:

```
control result: 1
```

`GET /health` against the running stack:

```json
{"detail": {"status": "unhealthy",
            "dependencies": {"postgres": "unhealthy", "redis": "unhealthy", "vector_db": "healthy"},
            "safety_events_last_hour": 0,
            "timestamp": "2026-09-27T02:18:02.000000"}}
```
HTTP 503, server log: `postgres_health_check_failed error="Textual SQL expression 'SELECT 1' should be explicitly declared as text('SELECT 1')"`

After (re-run against the built change on `fix/61-wrap-select-in-text`, commit `b926361`):

Isolated call, now using the route's actual fixed statement:

```python
import asyncio
from sqlalchemy import text
from core.database import get_db

async def main():
    async for session in get_db():
        result = await session.execute(text("SELECT 1"))
        print("result:", result.scalar())
        break

asyncio.run(main())
```

```
$ .venv/bin/python repro_after.py
2026-10-03 12:58:42,636 INFO sqlalchemy.engine.Engine SELECT 1
2026-10-03 12:58:42,636 INFO sqlalchemy.engine.Engine [generated in 0.00005s] ()
result: 1
2026-10-03 12:58:42,638 INFO sqlalchemy.engine.Engine ROLLBACK
```

`GET /health` against the running stack (docker compose up, migrations at head,
`uvicorn api.main:app`):

```
$ curl -s -w "\nHTTP %{http_code}\n" http://127.0.0.1:8010/health
{"detail":{"status":"unhealthy","dependencies":{"postgres":"healthy","redis":"unhealthy","vector_db":"healthy"},"safety_events_last_hour":0,"timestamp":"2026-10-03T16:58:55.235023"}}
HTTP 503
```

Server log for that request:

```
2026-10-03 12:58:55,251 INFO sqlalchemy.engine.Engine SELECT 1
2026-10-03 12:58:55,251 INFO sqlalchemy.engine.Engine [generated in 0.00022s] ()
2026-10-03 12:58:55 [debug    ] postgres_health_check_passed   request_id=1c478ca8-b949-4fe6-bf7a-bc513442884a
2026-10-03 12:58:55 [error    ] redis_health_check_failed      error="'Settings' object has no attribute 'redis_host'" request_id=1c478ca8-b949-4fe6-bf7a-bc513442884a
2026-10-03 12:58:55 [debug    ] vector_db_health_check_passed  request_id=1c478ca8-b949-4fe6-bf7a-bc513442884a
```

`postgres` flips from `"unhealthy"` (with the `ArgumentError`) to `"healthy"` (with no error,
just `SELECT 1` executing normally), exactly as the plan's test plan predicted. The endpoint
still returns 503 overall because `redis` fails on its own, unrelated, already-tracked bug
(#62) — also exactly as the plan called out in advance, not a sign this fix is incomplete.

Also ran `make lint` (clean) and `make test-unit` (375 passed, 53 xfailed, none newly so)
against the branch. `make typecheck` fails locally on an unrelated, pre-existing environment
issue (a `numpy` stub using Python-3.12-only syntax that the installed `mypy` can't parse,
reproduced identically on unmodified `main`) — not something this change introduced.

---

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Smoke run, 6 packages (`pkg-01` through `pkg-06`): 6/6 agree.
2. Full run #1: 19/20 agree, full category floor met. One disagreement: `pkg-14`
   (`clear-accept`, gold `accept`), which my rubric rejected.
3. Targeted re-grade, `--only pkg-14,pkg-01,pkg-07,pkg-10,pkg-11,pkg-16,pkg-17,pkg-18`
   plus `--include-calibration` for `calib-03` as a free trap check (8 packages: the
   `pkg-14` fix plus canaries from every category the fix touched): 8/8 agree, `pkg-14`
   now correctly accepts, nothing else flipped.
4. Full run #2: 20/20 agree, full category floor met.
5. Full run #3, with `--save-run eval-run.txt`: 20/20 agree, full category floor met.
   **This is the committed `eval-run.txt`**, matching its agreement line:
   `agreement: 20/20 scored items  (bar: 18/20: PASS)`.

**Package analysis**

`pkg-14` (`zellij-org/zellij#5174`, category `clear-accept`). Gold label: `accept` — the
package's note calls it "honestly scoped-down: reattach handshake fix with a
regression-window repro; defers the untestable Windows variant and says so." My rubric's
first draft decided `reject`, failing it on two checks it shouldn't have:

- **Diagnosis grounded** failed because the repro evidence's cache-clear control run
  ("after `rm -rf ~/.cache/zellij`, the next attach is clean, the one after leaks again")
  looked like it could contradict the plan's stated cause (stdin wired before OSC
  responses are consumed on reattach). But the plan explicitly explains that data point —
  with an empty cache, the color data is refetched along the same code path a fresh attach
  uses, which is clean — a specific, stated mechanism, not a hand-wave. My check's pass
  condition treated "a data point that looks awkward exists" as disqualifying, instead of
  checking whether the plan had actually accounted for it.
- **Executable** failed because the plan says "exact functions to be pinned in the PR
  after tracing the query issuance with debug logs." My check's pass condition treated any
  undetermined function name as a deferred decision, when the plan had already named the
  specific modules (the reattach path in `zellij-server`'s client connection handling, and
  `zellij-client`'s terminal query issuance) and already had working trace output pointing
  at the mechanism — only the exact line was left for the PR itself.

Both pass conditions were rewritten (see Check rationale) to distinguish a plan that
engages and explains a tricky data point from one that ignores it, and a plan with a
decided mechanism and a concrete way to pin down the exact site from one that hasn't
actually decided anything yet. After the fix, `pkg-14` reads `accept`, matching gold.

**Check rationale**

From `rubric.md` as uploaded, the `Executable` check's pass condition:

> Pass if a specific file or module is named and the approach/mechanism is decided, even if
> the exact function or line is left to be pinned down while tracing — provided the plan
> shows a concrete way to find it (e.g., already-working debug output, a named code path)
> rather than a plan to go figure out which layer is even responsible. Fail if the approach
> itself is undecided — language like "somewhere," "maybe," "whichever is easier," an
> unresolved choice between layers/libraries/approaches, or no files/modules named at all.

I revised it to this from a stricter first draft ("specific files or functions are named
and the approach is a decided path a stranger could start on today... fail if a real
engineering decision is deferred to build time") after `pkg-14` showed the stricter version
conflating two different things: not having decided an approach (which should fail — the
pattern `pkg-10`/`pkg-17`/`pkg-18` are built around, e.g. `pkg-18`'s "recover() 'somewhere',
fix 'upstream or vendored, whichever is easier'") versus having decided an approach and a
specific module but leaving the exact line for the PR, backed by a working trace. Those
`unbuildable` packages still fail the revised check (I re-ran all three as canaries, 3/3
still correctly reject) because their problem is the undecided approach itself, which the
revised wording still catches.

**Trade-offs**

Loosening `Executable` to accept "exact line pinned down later, if the module and
mechanism are decided and a concrete trace exists" gives up some precision: a plan that
merely claims to have a trace (without showing any of its output) would now read the same
as one that actually pastes trace output, since the check reads the plan's stated evidence
and can't independently verify a claim like "I have `--debug` output showing the leak's
origin." A plan that overstates its own groundwork this way could pass `Executable` when it
shouldn't. I accept this miss because the alternative — the stricter version — rejected a
real accept (`pkg-14`) on the inverse mistake, and the eval set's `unbuildable` category
(the one this check is scored against) is built around approaches left genuinely open, not
around plans that overclaim a trace they don't have; no package in the 20 tests that
specific failure mode, so I have no canary confirming the check catches it, and I'm noting
that gap here rather than papering over it.
