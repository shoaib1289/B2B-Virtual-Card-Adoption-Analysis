# Power Query Transformations

Documentation of the M-language transformations applied to the raw
transaction data before reaching the model layer.

## Transformation 1: Cashback rate

**Purpose:** Surface the cashback tier structure embedded in the program.

**M expression** (added as custom column on the transactions table):

\`\`\`
= if [payment_amount] > 0 
  then [cashback_paid] / [payment_amount] 
  else null
\`\`\`

**Why this transformation:** The raw data has cashback_paid and
payment_amount as separate columns, but their ratio isn't materialized.
Looking at the distribution of the derived ratio across all
transactions showed only two distinct values, indicating a tiered
pricing program rather than continuous variation.

## Transformation 2: Processing days

**Purpose:** Measure operational efficiency by industry.

**M expression:**

\`\`\`
= Duration.Days([transaction_date] - [payment_created_date])
\`\`\`

**Why this transformation:** Processing duration isn't in the raw
data — only the start and end timestamps. Deriving it as a numeric
column makes it possible to aggregate and segment by industry, which
is what surfaced the Education-Retail efficiency gap.

## Transformation 3: Processing cost

**Purpose:** Make the unit economics visible end-to-end.

**M expression:**

\`\`\`
= [gross_interchange] - [net_interchange] - [cashback_paid]
\`\`\`

**Why this transformation:** Gross interchange, net interchange, and
cashback are all in the raw data, but the residual — processing cost —
isn't. Deriving it explicitly turns the unit economics into a
four-component breakdown (gross to cashback to processing to net)
rather than a two-component before-and-after.

## Transformation 4: Customer size mapping

**Purpose:** Group customers into Small / Mid / Large categories from
employee count ranges.

**M expression:**

\`\`\`
= if [employee_count] < 100 then "Small"
  else if [employee_count] < 1000 then "Mid"
  else "Large"
\`\`\`

## Transformation 5: Cashback tier classification

**Purpose:** Label each transaction with its cashback tier based on
the derived cashback rate.

**M expression:**

\`\`\`
= if [cashback_rate] >= 0.005 then "Tier 2 (Premium)"
  else "Tier 1 (Standard)"
\`\`\`

**Why this transformation:** Once the two-tier structure was
discovered via the cashback rate distribution, labeling each
transaction with its tier explicitly enabled tier-level segmentation
in every downstream visual.

---

## Notes on the M-language approach

All transformations were applied at the Power Query stage rather than
as DAX calculated columns, for two reasons:

1. **Performance** — Power Query transformations execute once at
   refresh time and are stored. Calculated columns recompute on every
   interaction.
2. **Reusability** — these derived fields are foundational to every
   downstream measure and visual. Materializing them at the query
   layer keeps the model clean.

DAX is used for measures (aggregations that depend on filter context),
not for the derived columns above.
