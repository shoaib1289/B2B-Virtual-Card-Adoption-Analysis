# B2B Virtual Card Adoption Revenue & Customer Segmentation Analysis

Personal analytics project exploring how a B2B payment platform could
analyze its virtual card payment program unit economics, customer
segmentation, processing efficiency, and growth opportunities.

Built on synthetic transaction data. Framework, derived columns, and
analytical choices are my own.

---

## Project overview

When a B2B payment platform pays a supplier by virtual card instead of
ACH, it earns interchange revenue typically 150–250 basis points on
the transaction. A portion of that interchange is shared back to the
customer as cashback, and the remainder is the platform's gross margin
after processing costs.

This project takes 2,000 completed virtual card transactions over a
four-month window and answers four questions a Card Line of Business
team would actually need to answer:

1. **What does volume growth look like, and what's driving it?**
2. **How do the unit economics actually work gross to net?**
3. **Which customer segments are driving spend, and where's the upside?**
4. **What operational differences exist across industries, and what do
   they imply for prioritization?**

The output is three concrete recommendations for Customer Success, Sales,
and Product each grounded in a specific pattern in the data.

---

## Analytical approach

### Derived columns the most important design decisions

The raw data didn't surface the most interesting patterns. Three derived
columns made them visible:

**1. Cashback rate per transaction.**
Calculated as `cashback_paid / payment_amount` per row. When I looked at
the distribution across all transactions, it didn't vary continuously
it showed exactly two distinct values. That's the signal that the
platform runs a tiered cashback program, not a continuous one. Discovering
the tier structure was upstream of all the customer segmentation analysis
that followed.

**2. Processing days per transaction.**
Calculated as `transaction_date - payment_created_date`. This isn't a
field in the raw data it has to be derived. Processing days surfaced
operational efficiency differences across industries that aren't visible
from spend data alone.

**3. Processing cost per transaction.**
Calculated as `gross_interchange - net_interchange - cashback_paid`. The
raw data shows gross and net interchange but doesn't break out processing
cost. Deriving it explicitly was what made the unit economics legible
gross to cashback to processing to net, in that order.

### Why this matters

Counting transactions and summing spend would have produced an executive
summary. Deriving these three columns is what made the analysis
*diagnostic* pointing at specific levers, not just describing aggregate
performance.

---

## Key findings

### Finding 1 Volume growth is broad-based, not concentrated

Month-over-month growth between the two full months in the window was
**41.5% in card spend**. Decomposing the growth showed it wasn't a single
large customer or single category transaction count grew 31% and
average transaction size grew 8%, indicating broad participation
expansion rather than concentration risk.

This matters for forecasting: broad-based growth is more durable and
projectable than concentrated growth, which can reverse if one large
customer churns.

### Finding 2 Unit economics are stable, predictable, and tier-driven

For every $100 in card spend across the dataset:

| Component | Per $100 | Rate |
|---|---|---|
| Gross interchange earned | $1.92 | 1.92% |
| Cashback paid to customer | $0.50 | 0.50% |
| Processing cost absorbed | $0.48 | 0.48% |
| **Net retained by platform** | **$0.94** | **0.94%** |

Net margin holds at roughly 49.5% of gross interchange across all four
months. This means revenue scales proportionally with card volume
attractive for forecasting and capacity planning.

### Finding 3 The cashback tier structure is the dominant segmentation lever

Cashback rate distribution analysis surfaced two distinct tiers in the
program (rather than continuous rate variation). Customers on the
higher-tier program (0.50% cashback) generated **83% more card spend**
than lower-tier customers (0.25%), with **16% higher average transaction
size**.

The implication: tier is associated with payment behavior. Whether the
relationship is causal (premium cashback drives behavior) or selection
(higher-spend customers were placed in the premium tier) requires data
this dataset doesn't include. Either way, tier is the strongest predictor
of spend in the dataset.

### Finding 4 Industry mix surfaces operational gaps, not revenue gaps

Across merchant categories, interchange rates were structurally similar
(2.6%–3.0%). The differentiator wasn't interchange it was operational
efficiency.

**Education** generated the highest average transaction values ($14–16K)
with **3.8 days average processing time** the fastest in the dataset.

**Retail** generated the highest volume but **5.5 days average processing
time** a 45% efficiency gap versus Education.

The pattern that emerges from a quadrant view (processing speed × average
transaction size):

| Quadrant | Industry | Implication |
|---|---|---|
| High value, fast processing | Education, Healthcare | Priority strongest economics |
| High value, slow processing | (none) | |
| Low value, fast processing | Professional Services | Volume play |
| High volume, slow processing | Retail | Operational opportunity |

---
## Dashboard

The full dashboard is built across five pages, each designed for a
specific audience.

### Executive Overview
![Executive Overview](Visuals/01_Executive_Overview.png)

### Revenue Economics
![Revenue Economics](Visuals/02_Revenue_Economics.png)

### Customer Segmentation
![Customer Segmentation](Visuals/03_Customer_Segmentation.png)

### Industry Analysis
![Industry Analysis](Visuals/04_Industry_Analysis.png)

### Outlier Identification
![Outlier identification](Visuals/05_Outlier_identification.png)

### ERP Analysis
![ERP Analysis](Visuals/05_ERP_Analysis.png)

## Recommendations

Three concrete actions, each tied to a specific pattern in the data:

### Customer Success Convert mid-size customers to higher cashback tier

Mid-size customers represent 33% of spend and are the segment most
concentrated on the lower cashback tier. If the proportion currently on
the lower tier converted up and exhibited the same 16% transaction-size
premium observed in existing higher-tier customers, monthly spend from
this segment would increase materially generating incremental net
interchange with no new customer acquisition cost.

### Sales Prioritize Education and Healthcare verticals

Education and Healthcare generate the highest economic efficiency per
customer in the dataset high average transaction values combined with
fast processing. These verticals deliver disproportionate interchange per
unit of acquisition effort and are the strongest targeting candidates
for new logo acquisition.

### Product Investigate Retail processing delay

Retail's 5.5-day processing time versus Education's 3.8-day processing is
the largest operational efficiency gap in the program. Retail is also
the highest-volume segment, so resolving this friction improves the
economics on one of the largest revenue contributors without requiring
additional supplier enablement or customer acquisition.

---

## Methodology notes

### Outlier treatment

One transaction was flagged during review a $175,950 payment where
processing cost represented 63% of gross interchange versus the dataset
average of 25%. The transaction was retained in the analysis. Aggregate
findings remain consistent whether the outlier is included or removed,
so removing it would have been a cosmetic choice rather than a
methodological one.

### What the dataset doesn't include

- **Failed and reverted payments** only completed transactions were in
  the sample. This means full funnel metrics (qualification rate,
  enrollment conversion, activation rate) cannot be calculated from this
  data alone. A production framework would need failed-attempt data with
  reason codes to surface where in the funnel volume is lost.
- **Pre-transaction supplier behavior** no data on outreach attempts,
  enrollment timing, or supplier decision-making. The analysis observes
  what happens *after* a supplier is paying by card, not what determines
  whether they enroll in the first place.
- **Customer-level cost attribution** acquisition costs, CS engagement
  hours, and enablement effort aren't in the dataset, so net contribution
  margin per customer can't be calculated.

### Why this scope was the right scope

A full funnel analysis would have required failure data the dataset
doesn't include. Rather than guessing at funnel mechanics, I focused on
what the data could support: unit economics, customer segmentation,
operational efficiency, and growth recommendations grounded in observable
patterns.

---
## Tools

End-to-end Power BI:

- **Power Query (M language)** data import, cleaning, derived columns,
  joins
- **DAX** measures, growth calculations, segmentation logic, quadrant
  classifications
- **Power BI visualization layer** executive overview, segmentation,
  and recommendations views


---

## Analytical approach

### Derived columns built in Power Query

The raw data didn't surface the most interesting patterns. Three derived
columns made them visible all built in Power Query:

**1. Cashback rate per transaction.**
M expression dividing cashback_paid by payment_amount per row. When I
profiled the distribution across all transactions, it didn't vary
continuously exactly two distinct values appeared across 2,000 rows.
That's the signal that the platform runs a tiered cashback program, not
a continuous one. Discovering the tier structure was upstream of all the
customer segmentation analysis that followed.

**2. Processing days per transaction.**
M expression computing the day difference between transaction_date and
payment_created_date. Not a field in the raw data it had to be derived.
Processing days surfaced operational efficiency differences across
industries that aren't visible from spend data alone.

**3. Processing cost per transaction.**
M expression computing gross_interchange minus net_interchange minus
cashback_paid. The raw data shows gross and net interchange but doesn't
break out processing cost. Deriving it explicitly was what made the unit
economics legible gross to cashback to processing to net, in that order.

### DAX measures

The aggregations and growth calculations layered on top of the
transformed data:

- **Month-over-month growth decomposition** transaction count growth
  vs. average transaction size growth, isolated as separate measures so
  each could be visualized independently
- **Net margin** calculated as net_interchange divided by
  gross_interchange across selected filters, allowing the headline 49.5%
  figure to recalculate dynamically with any filter applied
- **Tier-segmented spend ratios** comparing average transaction size
  and total spend across the two derived cashback tiers
- **Industry quadrant classification** DAX measure assigning each
  industry to one of four quadrants based on processing time and average
  transaction size

### Why this matters

Counting transactions and summing spend would have produced an executive
summary. The Power Query transformations and DAX measures together are
what made the analysis *diagnostic* pointing at specific levers, not
just describing aggregate performance.
---

## What I'd build next

Given more data, the highest-leverage extensions:

1. **Failure analysis** add reason codes on failed transactions to
   surface where in the funnel volume is lost
2. **Cohort retention** model how supplier card-spend behavior evolves
   over the first 12 months post-enrollment
3. **Forecasting layer** once the dataset spans enough months, build a
   SARIMAX or cohort-decay forecast for projecting monthly net interchange
4. **Attribution decomposition** when card spend increases for a
   customer, separate the effect of Sales push, CS engagement, and
   supplier-side readiness

---

## Repository structure

\`\`\`
virtual-card-adoption-analysis/
├── README.md
├── data/
│   └── (synthetic transaction data see generate_data.py)
├── sql/
│   ├── derived_columns.sql
│   ├── customer_segmentation.sql
│   └── industry_analysis.sql
├── python/
│   └── exploratory_analysis.ipynb
├── dashboard/
│   └── virtual_card_analysis.pbix
├── slides/
│   └── analysis_summary.pptx
└── visuals/
    └── (dashboard and slide exports)
\`\`\`
