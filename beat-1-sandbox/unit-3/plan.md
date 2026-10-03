# Plan: issue #61 — health check DB probe passes a raw SQL string

Issue: https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61

## Diagnosis

The postgres branch of `health_check` in `api/routes/health.py:32` calls:

```python
await db.execute("SELECT 1")
```

SQLAlchemy 2.x no longer coerces a bare Python string into textual SQL;
`execute()` requires it to be wrapped in `sqlalchemy.text()`. My posted
repro report
(https://github.com/codepath/pathreview-ai301-fa26-s1/issues/61#issuecomment-5851933800)
isolated this exact call through the app's own `get_db()`, with nothing
else from the route involved:

> ```
> sqlalchemy.exc.ArgumentError: Textual SQL expression 'SELECT 1' should
> be explicitly declared as text('SELECT 1')
> ```

and the control run, the identical string wrapped in `text()` on the
same session, succeeded (`control result: 1`), which rules out a
database-connectivity problem and pins the cause on the bare string,
not on the database being unreachable. The same report also hit the
live `/health` endpoint directly and found the identical `ArgumentError`
in the app's own logs alongside the 503.

## Scope

**In scope**: one call site, `api/routes/health.py:32`, wrapped in
`sqlalchemy.text()`. A repo-wide grep (`grep -rn '\.execute(\s*["\']'
--include='*.py' .`) found this is the only bare-string `execute()`
call in the codebase, so this is a single-site fix, not a pattern to
hunt down elsewhere.

**Not in scope**: the `redis` health check in the same route
(`api/routes/health.py:47`, `settings.redis_host`/`settings.redis_port`)
fails for an unrelated reason — `'Settings' object has no attribute
'redis_host'` — already called out as a separate bug in my repro
report and tracked as issue #62. Fixing it is not part of this change,
and this plan does not touch that branch of the function, its imports,
or `core/config.py`.

## Files

- `api/routes/health.py` — import `text` from `sqlalchemy`, wrap the
  literal string at line 32.

## Approach

1. Add `from sqlalchemy import text` to the imports in
   `api/routes/health.py`.
2. Change `await db.execute("SELECT 1")` to
   `await db.execute(text("SELECT 1"))`.
3. No other lines in the function change; the `try`/`except` structure,
   the `health_status` dict, and the other two dependency checks are
   untouched.
4. `pyproject.toml`'s `[tool.mypy.overrides]` for `api.routes.health`
   currently disables `["attr-defined", "call-overload", "index"]`, with
   a comment naming `attr-defined` as issue #62's suppression.
   `call-overload` is this issue's: `execute()`'s overloads don't accept
   a bare `str`, which is exactly what wrapping in `text()` resolves.
   Per `docs/CONTRIBUTING.md`'s rule to remove a seeded bug's suppression
   when fixing it, I'll drop `call-overload` from that list and leave
   `attr-defined` (#62) and `index` (unrelated to this fix) in place.

## Test plan

Re-run my Unit 2 repro steps against the change:

1. The isolated-call script from the repro report
   (`get_db()` + `await session.execute(text("SELECT 1"))`, now using
   the app's own fixed code path) — expect it to return `1` with no
   `ArgumentError`, matching the control run I already captured, instead
   of today's isolated failing call.
2. `GET /health` against the running stack — expect the `postgres` key
   in the response to read `"healthy"` instead of `"unhealthy"`. The
   endpoint's overall `status` will still report `"unhealthy"` and the
   response will still be a 503 until #62 (redis) is also fixed; that is
   expected and not a sign this fix is incomplete — it is the redis
   check's unrelated failure carrying through to the overall status, by
   design.
3. Capture before/after output for both checks, same as the Unit 2
   evidence.

## Risks and unknowns

- Low risk: a one-line change confined to a single call site, with no
  change to the query itself, its return handling, or any other
  dependency check.
- The overall `/health` endpoint will still return 503 after this fix
  lands, because of the separate redis bug (#62). I'm calling this out
  explicitly so it isn't mistaken for this fix being incomplete; the
  postgres key flipping to `"healthy"` is the observable this plan is
  responsible for.
- I checked for a seeded-bug test marker (`docs/CONTRIBUTING.md` says
  seeded bugs carry an `xfail` test to remove on fix) and found none for
  `health.py`/postgres specifically, so I don't expect a test-file change
  beyond the mypy override noted above. I'll confirm this holds once I
  run `make test-unit` against the branch.

## Deviations

Nothing changed; the plan held. The build was exactly the three pieces
named above: the `sqlalchemy.text()` import and wrap at
`api/routes/health.py:32`, and dropping `call-overload` from the
`api.routes.health` mypy override in `pyproject.toml` (confirmed by
re-reading the override after the edit — `attr-defined` and `index`,
both unrelated to this issue, are still suppressed for #62 and the
vector_db branch respectively). `make lint` and `make test-unit` both
passed clean (375 passed, 53 xfailed, none newly so) against the
branch. `make typecheck` could not run locally — a pre-existing,
unrelated environment issue (the installed mypy version chokes on a
`numpy` stub's Python-3.12-only syntax before it reaches any project
file; reproduced identically on `main` with no changes applied, so it
isn't something this change introduced). No test carried an `xfail`
marker for this issue to remove. The test plan's two checks ran exactly
as predicted: the isolated call now returns `1` instead of raising, and
`GET /health` now reports `"postgres": "healthy"` while the endpoint
still returns 503 overall because of the separate, already-tracked
redis bug (#62) — see Evidence in `plan-and-implement.md` for the full
before/after output.
