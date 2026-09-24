# Project Scope

## Business Problem

An eCommerce company collects sales, inventory, product, customer, and
marketing data across multiple systems.

Because these datasets are stored and structured differently, analysts
spend significant time preparing data before it can be used for reporting
and analysis.

The organization needs a centralized analytics architecture that provides
consistent, reliable, and reusable business data.

## Objective

Build an end-to-end eCommerce analytics lakehouse using Microsoft Fabric,
PySpark, and SQL.

The solution will use a medallion architecture to transform raw source data
into analytics-ready datasets that support business intelligence and
AI-enabled analytics.

## Architecture

### Bronze
Store raw source data with minimal transformation.

### Silver
Clean, standardize, validate, and integrate source data.

### Gold
Create business-ready datasets for sales, inventory, products, customers,
and marketing performance.

## Analytics Layer

Gold datasets will support:

- Revenue and sales analysis
- Product performance
- Inventory analysis
- Customer behavior
- Marketing performance
- Power BI reporting
- AI-powered business analytics

## Dimensional Model

### Fact Tables

| Fact table | Grain | Business purpose | Key measures |
|---|---|---|---|
| `fact_sales` | One row per order line | Records products ordered, including cancellations | Ordered quantity, gross amount, discount amount, tax amount, ordered amount, cancelled quantity, cancelled amount |
| `fact_shipments` | One row per order line within a shipment | Tracks split shipments and delivery activity | Shipped quantity, delivered quantity, shipping cost |
| `fact_returns` | One row per order line within a return transaction | Tracks partial returns, refunds, return dates and reasons | Returned quantity, refund amount |
| `fact_inventory_snapshot` | One row per snapshot date × product × location | Stores end-of-day inventory availability by location | On-hand quantity, reserved quantity, available quantity, inventory value |
| `fact_marketing_performance` | One row per date × channel × campaign | Measures campaign-level marketing performance | Spend, impressions, clicks, conversions, attributed revenue |

### Fact Table Design Notes

- Cancelled order lines remain in `fact_sales`; they are not deleted.
- `fact_sales` contains the current order-line status and cancellation measures.
- Shipment and delivery events are stored separately in `fact_shipments`.
- Split shipments are supported because one order line can connect to multiple shipment rows.
- Partial returns are supported because one order line can connect to multiple return rows.
- Inventory snapshots are end-of-day snapshots.
- Inventory measures are semi-additive: they can be summed across products and locations, but not across dates.
- Marketing ratios such as CTR, conversion rate and ROAS are calculated from base measures rather than stored.

### Shared Dimensions

| Dimension | Purpose | History strategy |
|---|---|---|
| `dim_date` | Shared calendar attributes for order, shipment, delivery, return, inventory and marketing dates | Static dimension |
| `dim_product` | Product name, brand, category, subcategory and product attributes | SCD Type 1 |
| `dim_customer` | Customer attributes used for sales and return analysis | To be decided |
| `dim_location` | Warehouses, fulfillment locations and geographic attributes | To be decided |
| `dim_channel` | Sales and marketing channels | SCD Type 1 |
| `dim_campaign` | Marketing campaign attributes | To be decided |
| `dim_return_reason` | Standardized return-reason classifications | SCD Type 1 |

### Product Dimension Decision

`dim_product` will use Slowly Changing Dimension Type 1 behavior.

When a product attribute changes, such as moving from `Cameras` to `Professional Imaging`, the existing dimension row will be updated. Historical sales will therefore be reported using the product's current category.

This approach intentionally restates history according to the latest product hierarchy and avoids maintaining multiple historical versions of the same product.
