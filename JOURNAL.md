## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/154

**Issue title:** Health check DB probe passes a raw SQL string, which fails under SQLAlchemy 2.x

**Tier:** [x] Tier 1  [ ] Tier 2  [ ] Tier 3

**Problem summary:**
In this case, the /health endpoint should verify the app and its DB connection are working. It uses SQL to test the DB as "SELECT 1", but the version of SQLAlchemy here does not allow the direct use of raw strings for queries. Because of this, the DB check throws an error every time as it always shows the DB as “down”, even when is running fine. The part of the codebase that this exists is in api/routes/health.py in the DB probe portion of the health check logic. An ideal solution will make the DB check run successfully and return the actual running status of the DB connection.

**Branch name:** fix/154-health-check-sql-text

**Setup confirmation:** [x] App runs locally at localhost:5173

**Cohort ledger:** [x] Issue added to cohort ledger