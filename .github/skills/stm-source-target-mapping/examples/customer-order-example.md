# Example Source-to-Target Mapping

## Context Summary

- Mapping Name: Customer Order Intake
- Source System: Orders API
- Target System: `sales_order` table
- Grain: One row per order header
- Assumptions: Line items are stored in a child table and are excluded from this header mapping.

## Mapping Table

| Source Field Path | Source Data Type | Transformation Rule | Target Column Name | Target Data Type | Default Value | Business Definition | Example of Data |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `order.id` | String | Direct | `sales_order_id` | Varchar(50) | None | Unique business identifier for the order | `SO-10452` |
| `order.createdAt` | String (ISO 8601) | Format: ISO 8601 -> UTC timestamp | `created_utc` | Timestamp | Current timestamp only if source missing and business approved | Time when the order was created in the source platform | `2026-03-27T14:21:05Z` |
| `customer.customerNumber` | String | Direct | `customer_number` | Varchar(30) | `UNKNOWN` | External customer reference used by finance and support | `CUST-8821` |
| `customer.name.full` | String | Direct | `customer_name` | Varchar(200) | None | Full display name of the customer | `Ava Brooks` |
| `order.statusCode` | String | Lookup: statusCode -> canonical_status | `order_status` | Varchar(20) | `PENDING` | Normalized lifecycle state for downstream reporting | `SHIPPED` |
| `order.total.amount` | Decimal | Direct | `order_amount` | Decimal(18,2) | `0.00` | Total order amount before settlement | `1250.75` |
| `order.currency` | String | Direct | `currency_code` | Char(3) | `USD` | ISO currency code for the transaction | `USD` |
| `shipping.address.postalCode` | String | Direct | `ship_postal_code` | Varchar(20) | None | Postal code used for delivery routing | `10001` |

## Transformation Notes

| Target Column Name | Note |
| --- | --- |
| `order_status` | Lookup requires a governed crosswalk table owned by operations. |
| `created_utc` | Normalize all timestamps to UTC before persistence. |

## Risks And Open Questions

| Type | Detail |
| --- | --- |
| Risk | Status-code crosswalk is undefined and may produce inconsistent reporting categories. |
| Question | Should missing `customer.customerNumber` reject the record or persist with `UNKNOWN`? |