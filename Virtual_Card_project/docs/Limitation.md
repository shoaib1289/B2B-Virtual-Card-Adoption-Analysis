# Limitations

What this analysis intentionally doesn't address, and what data would
be needed to address it.

## Failed and reverted transactions

The provided sample contained only completed transactions. This means
full funnel metrics — qualification rate, enrollment conversion,
activation rate, recurring conversion — cannot be calculated from this
data alone.

**What would address this:** Failed-attempt records with reason codes
(e.g., supplier declined, invoice over limit, customer overrode to
ACH, processing error). With those codes attached to failed
transactions, the analysis would extend to:
- Funnel drop-off rates per stage
- Failure mode distribution by customer and supplier segment
- Recoverable revenue estimation

## Pre-transaction supplier behavior

No data was available on outreach attempts, enrollment timing, or
supplier decision-making. The analysis observes what happens after a
supplier is paying by card, not what determines whether they enroll in
the first place.

**What would address this:** Enrollment event logs with outreach
attempts, response timing, and outcome reasons. Combined with the
existing transaction data, this would enable cohort retention analysis
and time-to-first-payment metrics.

## Customer-level cost attribution

Acquisition costs, CS engagement hours, and enablement effort aren't
in the dataset, so net contribution margin per customer can't be
calculated.

**What would address this:** Customer-level cost data from Sales,
CS, and Enablement teams. With those joined to the existing transaction
data, the analysis would extend to true contribution margin and
customer LTV modeling.

## Causation versus selection

The finding that higher-tier cashback customers generate more spend is
associative. Whether premium cashback drives the behavior or higher-
spend customers were placed in the premium tier can't be separated
from this data.

**What would address this:** Either a randomized assignment of customers
to tiers (unlikely in a real business) or panel data showing tier
changes within the same customer over time. With longitudinal data,
within-customer tier effects could be isolated.

## Time window

Four months isn't enough to model seasonality, capture cohort dynamics
beyond the first quarter of supplier behavior, or build a defensible
forecasting layer. A production version of this analysis would need at
least 18-24 months of data to support cohort-decay modeling and seasonal
adjustment.