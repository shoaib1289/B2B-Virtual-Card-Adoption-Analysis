# Methodology Notes

## Framework design principles

Six principles applied building this:

**1. Goal first, metrics second.** Every metric in the analysis traces
back to the platform revenue goal. If a metric doesn't connect to that
goal in two steps or fewer, it's not in the dashboard.

**2. One row per transaction as the grain.** This is the grain that
supports both funnel analysis and revenue analysis without requiring
re-aggregation. Choosing the wrong grain forces every downstream
analyst to rebuild the foundation each time.

**3. Reason codes matter when failures exist.** This dataset contained
only completed transactions, so failure segmentation wasn't possible.
A production version of this analysis would require reason codes on
failed transactions to surface where in the funnel volume is lost.

**4. Team-specific views over one universal dashboard.** Sales, CS,
Product, and Ops make different decisions from the same underlying
data. The dashboard defines a shared data layer and separate team
views on top, rather than a single dashboard trying to serve everyone.

**5. Cadence aligned to decision rhythm.** Operational metrics like
processing time refresh on the operational cadence the team makes
decisions on; strategic metrics like net margin refresh monthly to
align with leadership reviews.

**6. Limitations as legible as predictions.** Every finding in the
analysis is paired with a clear statement of what the data doesn't
tell you. Numbers that don't disclose their boundaries get misused.

## Outlier treatment

One transaction was flagged during review — a $175,950 payment where
processing cost represented 63% of gross interchange versus the dataset
average of 25%. The transaction was retained in the analysis. Aggregate
findings remain consistent whether the outlier is included or removed,
so removing it would have been a cosmetic choice rather than a
methodological one.

## Why Power Query for derived columns

Three derived columns (cashback rate, processing days, processing
cost) were built at the Power Query layer rather than as DAX
calculated columns. This was a deliberate choice:

- **Performance** — Power Query transformations execute once at refresh
  and are stored. Calculated columns recompute on every interaction.
- **Foundational reuse** — these derived fields feed every downstream
  measure and visual. Materializing them at the query layer keeps the
  model clean and the DAX measures focused on aggregation logic, not
  row-level math.

## Why one row per transaction

Three alternative grains were considered:

- **One row per supplier** — too aggregated; loses the ability to
  analyze individual payment patterns
- **One row per customer** — even more aggregated; loses transaction-
  level segmentation entirely
- **One row per transaction** — selected. Captures every event with
  full attribute resolution; can be aggregated up to supplier or
  customer level when needed

The cost of the chosen grain is volume — a meaningful customer base
would produce millions of rows. Mitigated by appropriate column data
types and Power BI's columnar compression engine.