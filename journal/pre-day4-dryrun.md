# Pre-Day 4 — Full Pipeline Dry Run

**Result: clean pass, no re-setup needed.**

- No stale Jupyter kernels holding the DuckDB connection (jupyter notebook list was empty) —
  the lock-conflict issue from the capstone didn't recur.
- `dbt compile` resolved cleanly.
- `dbt run`: 17/17 models built (7 staging views, 10 gold tables), 0 errors, 4.47s total.
- `dbt test`: 5/5 data tests passed.

**One non-error worth noting:** "Unable to do partial parsing because of a version mismatch" —
dbt discarded its parse cache and did a full re-parse, likely because the installed dbt/duckdb
adapter version drifted from whatever was cached from a previous run. Cost ~0.5s, not a problem,
just means the speed-up cache reset rather than anything being broken.

**Takeaway:** environment held up over the days away. Confidence the pipeline is reliable to build
on for Week 1 without needing a rebuild-from-scratch first.

**Addendum:** Likely cause of the version-mismatch message — at some point ran dbt commands
while skill-sprint's `venv` was active instead of nhl-practice's `.venv`, and the two have
different dbt/duckdb versions installed. Confirmed by comparing `dbt --version` output in both.
Lesson: check `which dbt` before running dbt commands when juggling multiple repos/venvs.

**Addendum (revised):** Checked the venv-mixup theory directly — ran `dbt --version` inside
skill-sprint's venv and found dbt isn't installed there at all, which rules that theory out.
More likely explanation: nhl-practice's own .venv had a dbt-core or dbt-duckdb version bump at
some point (e.g. via pip install -r requirements.txt), and the existing partial-parse cache was
written by the prior version — triggering the mismatch on next run. Not fully confirmed, but a
more plausible cause than cross-venv contamination.

Separate, unrelated finding: skill-sprint's venv triggers a broken Ubuntu "command-not-found"
helper (ModuleNotFoundError: apt_pkg) when an unrecognized command is run — a pre-existing
system-level issue, not a venv or dbt problem. Not urgent, noting for awareness.
