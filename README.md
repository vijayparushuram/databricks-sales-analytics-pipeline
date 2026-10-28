# databricks-sales-analytics-pipeline

# Sales Analytics Pipeline on Databricks

An end-to-end data pipeline built in Databricks using the Bronze, Silver and Gold (medallion) pattern, with a dashboard on top.

> **Note:** The data is **synthetic**. I generated it with deliberate quality problems so I could practise cleaning and validating it. The business figures are not real, so I draw no business conclusions from them. The work being shown is the pipeline and the data quality checks.

## What this project shows

- Loading raw data into a Delta table without changing it (Bronze)
- Cleaning and standardising data with PySpark (Silver)
- Building business-ready summary tables with SQL (Gold)
- Reconciling totals across layers to prove nothing was lost or double-counted
- Presenting the results in a Databricks dashboard

## Dataset

A synthetic orders file of 60,600 rows and 15 columns (order, customer, product, quantity, price, discount, region, status). It covers January 2024 to December 2025 and includes deliberate problems: duplicate orders, missing regions and discounts, negative quantities and inconsistent customer name casing.

## Pipeline

| Layer | Table | What happens |
|---|---|---|
| Bronze | `bronze_orders` | Raw CSV loaded as-is (60,600 rows) |
| Silver | `silver_orders` | 600 duplicate orders removed (60,000 rows). Missing regions set to "Unknown", missing discounts set to 0, names standardised, negative quantities flagged as returns, line revenue calculated |
| Gold | `gold_monthly_revenue`, `gold_region_category`, `gold_top_customers`, `gold_status_summary` | Business summary tables. Only **Completed** orders count as revenue |

## Results

- Revenue (Completed orders): **£69,938,303.27**
- Completed orders: **54,085**
- Average order value: **£1,293.12** (total revenue divided by total orders)
- Order status split: 90.1% Completed, 5.9% Returned, 3.9% Cancelled
- **Reconciliation:** silver, gold monthly and gold region totals matched exactly

## Decisions and things I caught

- Returned and Cancelled orders are excluded from revenue, because including them would overstate sales.
- My first average order value tile averaged the monthly averages and showed £1,293.19. I replaced it with total revenue divided by total orders, which gives the correct £1,293.12.
- The monthly revenue line is flat because the synthetic data has no growth trend built in.
- One customer has an unusually large share of orders because of how I generated the data. I treat it as an artifact of the dataset, not a finding.

## Tools

Databricks (Free Edition), PySpark, SQL, Delta Lake, Databricks dashboards

## Files

- `sales_analytics_pipeline.ipynb`: the notebook with the Bronze, Silver and Gold code
- `dashboard.pdf`: the exported dashboard
  
