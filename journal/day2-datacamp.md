# Day 2 — DataCamp: Data Warehousing Concepts

**Context:** Original 8-week skill-sprint plan remains paused (see day2-pivot.md) while
prioritising the DataCamp Associate Data Engineer in SQL track ahead of its mid-July expiry.
Currently at 77% completion, working through remaining content + refreshing earlier material.

**Today's focus:** Data Warehousing Concepts course — one of the newer areas relative to prior
hands-on experience (less overlap with the NHL capstone than the SQL querying sections).

## Concepts reviewed (self-quizzed against Claude, not just multiple-choice recognition)

**OLTP vs OLAP**
Initially swapped the normalization direction — corrected to: OLTP is highly normalized
(optimised for fast, frequent small writes; harder to query due to many joins). OLAP is
denormalised/star-schema style (optimised for fast analytical reads; not ideal for high-frequency
transactional writes). Anchored this to my own NHL pipeline — fct_play/mart_shot_zones is OLAP-style,
deliberately denormalized for fast aggregation queries.

**Star vs snowflake schema**
Got this right on the first pass: star = all dimensions link directly to the fact table; snowflake =
at least one dimension links via another dimension first (more normalised, less redundancy, more
joins). Noticed and discussed with Claude that this is the same normalisation tradeoff as OLTP/OLAP,
but applied one level down — OLTP/OLAP is a system-design choice (what kind of system am I building),
star/snowflake is a warehouse-design choice (how normalised should my dimensions be, once I've
already decided I'm building OLAP). Same lever, different altitude.

**ETL vs ELT**
Got the ordering right immediately but my first explanation of *why* each one is what it is was thin
("less cumbersome" / "faster" without real reasoning). Refined to: ETL transforms before loading —
historically necessary because traditional warehouses had limited/expensive compute, so don't waste
it on transformation; ELT loads raw data first, transforms inside the warehouse after — only viable
with modern cloud warehouses' cheap elastic compute. Added my own point: ELT trades more storage
(raw data persists) for flexibility (can re-transform later without re-extracting), while ETL is
lighter on storage but locks you into whatever transformation logic was decided upfront. Connected
this to my own pipeline — Polars/DuckDB load raw data first, dbt transforms afterward inside the
warehouse layer — i.e. I built an ELT pipeline without having the label for it at the time.

## Takeaway

Concepts I'd already built intuition for through hands-on work (star schema, OLAP-style marts)
came back quickly once correctly labelled. The genuinely new ground was articulating the *why*
behind each tradeoff, not just the definitions — that's the part worth re-testing before the
actual certification assessment.

## Next
Fact/dimension grain and additive/semi-additive/non-additive measures — flagged to revisit.
