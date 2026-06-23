# Data Dictionary

## Transactions table

| Column | Type | Description |
|---|---|---|
| transaction_id | Text | Unique transaction identifier |
| customer_id | Text | Customer making the payment |
| supplier_id | Text | Supplier receiving the payment |
| transaction_date | Date | Date payment was processed |
| payment_created_date | Date | Date payment was initiated |
| payment_amount | Decimal | Transaction amount in USD |
| gross_interchange | Decimal | Gross interchange earned (USD) |
| cashback_paid | Decimal | Cashback paid to customer (USD) |
| net_interchange | Decimal | Net interchange retained (USD) |
| merchant_category | Text | Industry category of the supplier |

## Customers table

| Column | Type | Description |
|---|---|---|
| customer_id | Text | Unique customer identifier |
| customer_name | Text | Display name |
| employee_count | Whole Number | Customer organization size |
| signup_date | Date | Date customer joined the platform |

## Derived columns (built in Power Query)

| Column | Source | Formula |
|---|---|---|
| cashback_rate | transactions | cashback_paid / payment_amount |
| processing_days | transactions | transaction_date − payment_created_date |
| processing_cost | transactions | gross_interchange − net_interchange − cashback_paid |
| customer_size | customers | Small / Mid / Large from employee_count |
| cashback_tier | transactions | Tier 1 / Tier 2 from cashback_rate |