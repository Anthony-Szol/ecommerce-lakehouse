# Dimensional Model

## Modeling Principles

- Gold uses a dimensional star-schema design.
- Every fact table has a clearly defined grain.
- Facts connect through conformed dimensions rather than fact-to-fact joins.
- Gold facts use surrogate foreign keys.
- Source business identifiers are retained for traceability and incremental loading.
- Missing dimension values use key `0`.
- Valid late-arriving dimension values create inferred members.
- Monetary values use `DECIMAL(18,2)`.
- Version one supports USD only.
- UTC is the canonical timestamp standard.
- Business dates are derived using `America/Los_Angeles`.
- Direct customer PII is excluded from Gold.

## Fact Tables

| Fact table | Grain | Business key |
|---|---|---|
| `fact_sales` | One row per order line | Source system + order ID + order-line ID |
| `fact_shipments` | One row per shipment line | Source system + shipment ID + shipment-line ID |
| `fact_returns` | One row per return line | Source system + return ID + return-line ID |
| `fact_inventory_snapshot` | One row per snapshot date, product and location | Source system + snapshot date + product + location |
| `fact_marketing_performance` | One row per date, channel and campaign | Source system + activity date + channel + campaign ID |

## Dimensions

| Dimension | Grain | History strategy |
|---|---|---|
| `dim_date` | One row per date | Generated/static |
| `dim_product` | One row per product | SCD Type 1 |
| `dim_customer` | One row per customer version | Hybrid SCD Type 1 and Type 2 |
| `dim_location` | One row per fulfillment or inventory location | SCD Type 1 |
| `dim_channel` | One row per standardized channel | SCD Type 1 |
| `dim_campaign` | One row per marketing campaign | SCD Type 1 |
| `dim_return_reason` | One row per standardized return reason | SCD Type 1 |
| `dim_shipping_service` | One row per carrier and service level | SCD Type 1 |

## Bus Matrix

| Fact table | Date | Product | Customer | Location | Channel | Campaign | Return reason | Shipping service |
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| `fact_sales` | ✓ | ✓ | ✓ |  | ✓ |  |  |  |
| `fact_shipments` | ✓ | ✓ | ✓ | ✓ | ✓ |  |  | ✓ |
| `fact_returns` | ✓ | ✓ | ✓ | ✓ | ✓ |  | ✓ |  |
| `fact_inventory_snapshot` | ✓ | ✓ |  | ✓ |  |  |  |  |
| `fact_marketing_performance` | ✓ |  |  |  | ✓ | ✓ |  |  |

## Fact Sales

### Grain

One row per source-system order line.

### Measures

- Ordered quantity
- Cancelled quantity
- Unit price
- Gross sales amount
- Discount amount
- Net sales amount
- Tax amount
- Cancelled amount

### Monetary Definitions

```text
gross_sales_amount =
    ordered_quantity × unit_price

net_sales_amount =
    gross_sales_amount − discount_amount

customer_paid_amount =
    net_sales_amount + tax_amount

net_ordered_sales_amount =
    net_sales_amount − cancelled_amount
