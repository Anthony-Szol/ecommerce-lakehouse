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
