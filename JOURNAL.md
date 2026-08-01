## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/154

**Issue title:** Health check DB probe passes a raw SQL string, which fails under SQLAlchemy 2.x

**Tier:** [x] Tier 1 [ ] Tier 2 [ ] Tier 3

**Problem summary:**
In this case, the /health endpoint should verify the app and its DB connection are working. It uses SQL to test the DB as "SELECT 1", but the version of SQLAlchemy here does not allow the direct use of raw strings for queries. Because of this, the DB check throws an error every time as it always shows the DB as “down”, even when is running fine. The part of the codebase that this exists is in api/routes/health.py in the DB probe portion of the health check logic. An ideal solution will make the DB check run successfully and return the actual running status of the DB connection.

**Selection reasoning:**
I chose Tier 1 because this is my first time working in a large and unfamiliar codebase. Moreover, this particular issue is relatively easy to fix since it is isolated to one file (api/routes/health.py) with a clear cause, so I could describe the before/after without much digging. Given the 3 to 6 hours estimate for Tier 1 work, I'm confident this fits comfortably within the Week 8–9 timeline.

**Branch name:** fix/154-health-check-sql-text

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger

## Week 8 — Reproduction & solution planning — Issue #154

**Reproduction commit link:** https://github.com/Andresc06/pathreview/commit/5a097c5

**Reproduction summary:**
I reproduced the bug by running the app locally in Docker using `make run` and then, calling `GET /health` (`curl http://localhost:8000/health` as per the SETUP.md). Postgres was healthy in the container, but the endpoint still returned a 503 with `postgres: "unhealthy"` in the `dependencies` object, confirming the raw SQL string issue described in the issue. In this case, this is the exact output:

```json
{"detail":
    {"status":"unhealthy","dependencies":
        {
            "postgres":"unhealthy","redis":"unhealthy","vector_db":"healthy"
        },
    "safety_events_last_hour":0,"timestamp":"2026-07-24T21:04:44.544803"}
}
```

**PLAN.md link:** https://github.com/Andresc06/pathreview/blob/fix/154-health-check-sql-text/PLAN.md

**Blockers or open questions:**
Still need to confirm the fix works against a real Postgres connection in Docker (not just my isolated reproduction script), and I'm deciding whether to add a new test file (tests/unit/test_health.py) before opening the PR, since one doesn't currently exist for this route.

## Week 9 — Solution building & PR submission

### Check-in 1 (mid-week)

**Current progress:**
I was able to implement the the fix from my plan which was the wrapping of the raw SQL string in `sqlalchemy.text()` (`api/routes/health.py`). Then, I ran the reproduction script again and confirmed the `ArgumentError` is gone. And finally, I added `tests/unit/test_health.py` with two tests covering the healthy and unhealthy cases for the Postgres probe.

**Next steps:**
Now i need to make sure the fix works (not just mocks), run a full self-review comparing `make check` and `make test-unit` before/after my change, and open the PR.

**Blockers:**
None.

