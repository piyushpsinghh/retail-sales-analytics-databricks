# 🛍️ Retail Sales Analytics — Databricks Medallion Pipeline

An end-to-end data engineering & analytics project built on **Databricks**, using **PySpark** and **Delta Lake** to transform messy raw retail data into clean, analytics-ready tables — visualized in an interactive **Databricks SQL Dashboard**.

---

## 📌 Project Overview

This project simulates a real-world retail analytics pipeline:

- Raw **order** data (CSV) and **customer** data (JSON) are ingested into a Databricks **Volume**
- Data is cleaned and standardized through the **Medallion Architecture** (Bronze → Silver → Gold)
- The final **Gold** table powers a live dashboard with sales trends, returns, city performance, and customer loyalty insights

---

## ❓ Problem Statement (Business Use Case)

A mid-size retail company is struggling to consolidate and analyze its sales transactions, which are spread across different sources and formats. The business wants a unified analytics solution to:

- Clean messy data (inconsistent formats, missing values, duplicates)
- Build a single source of truth for sales, customers, and products
- Monitor KPIs such as revenue, return rate, repeat customers, and average order value
- Enable business teams to explore insights across product, category, customer, and time dimensions
- Use dashboards for data-driven decisions (e.g., inventory, pricing, promotions)

---

## ✅ Solution Approach — Medallion Architecture

- **Bronze Layer:** Ingest raw CSV/JSON files as-is (simulating real-world messy source files) into a Unity Catalog Volume, with zero transformation — preserving the original data as the recoverable source of truth.
- **Silver Layer:** Clean and standardize the data — fix inconsistent date formats, strip `$`/commas from prices, normalize product/category/gender names, handle missing or negative quantities, and remove duplicates.
- **Gold Layer:** Build one rich aggregate Delta table (`gold_aggregates`) with grouped metrics across product, customer, category, and time (year, month, date).

### KPIs Derived

- Total Revenue, Total Orders, Total Items Sold
- Average Order Value (AOV), Average Price per Item
- Repeat Customer Rate, Purchase Frequency
- Return Rate (by count & value)

---

## 🏗️ Architecture

```
                ┌─────────────────────┐
                │   Raw Data Sources   │
                │  (CSV + JSON files)  │
                └──────────┬──────────┘
                           │
                           ▼
                 ┌───────────────────┐
                 │      BRONZE        │   Raw ingestion, no transformation
                 │  raw_orders        │
                 │  raw_customers     │
                 └─────────┬─────────┘
                           │  Clean, standardize, dedupe
                           ▼
                 ┌───────────────────┐
                 │      SILVER        │   Parsed dates, cleaned prices,
                 │  silver_orders     │   trimmed strings, deduplicated
                 │  silver_customers  │
                 └─────────┬─────────┘
                           │  Join + aggregate
                           ▼
                 ┌───────────────────┐
                 │       GOLD          │   Business-ready aggregates
                 │  gold_aggregates   │
                 └─────────┬─────────┘
                           │
                           ▼
                 ┌───────────────────┐
                 │  Databricks SQL    │
                 │     Dashboard      │
                 └───────────────────┘
```

---

## 🧹 Data Cleaning Highlights

Real-world data is messy — this project handles it deliberately:

| Issue | Fix |
|---|---|
| Dates in 5+ formats (`2025-07-28`, `11-Sep-2025`, `14/11/2023`...) | `coalesce()` across multiple `to_timestamp` patterns |
| Prices stored as strings with `$` and commas | Stripped via `translate()`, cast to `double` |
| Inconsistent casing (`Male`, `M`, `m`, `MALE`) | Normalized with `lower(trim(...))` |
| Nulls / blanks in quantity | Defaulted to `1`, negative/zero values corrected |
| Duplicate order-product rows | Removed via `dropDuplicates()` |

---

## 📊 Dashboard

The Gold table feeds a Databricks SQL dashboard with:

- **Monthly Sales Trend** — total quantity/sales by month
- **Returned Amount by Category** — donut chart of returns
- **Top Sales by City** — bar chart across Indian cities
- **Average Price per Item by Loyalty Tier** — Bronze/Silver/Gold/Platinum
- **Average Order Value by Gender**

![Dashboard Overview](https://github.com/piyushpsinghh/retail-sales-analytics-databricks/blob/main/dashboard/Page%201.png)

---

## 🛠️ Tech Stack

- **Databricks** (Notebooks, Unity Catalog Volumes, SQL Dashboards)
- **PySpark / Spark SQL**
- **Delta Lake**
- **Python**

---

## 📁 Repository Structure

```
retail-sales-analytics-databricks/
├── README.md
├── notebooks/
│   └── bronze_to_gold_pipeline.ipynb   # Bronze → Silver → Gold pipeline
├── data/
│   ├── retail_dataset.csv              # Sample raw orders
│   └── customer_dataset.json           # Sample raw customers
├── dashboard/
│   └── dashboard.json                  # Databricks Lakeview dashboard export
│   ├── page 1.png
│   └── page 2.png
```

---

## ▶️ How to Reproduce

1. Upload `retail_dataset.csv` and `customer_dataset.json` to a Unity Catalog **Volume** in Databricks.
2. Run `notebooks/bronze_to_gold_pipeline.ipynb` top to bottom — it will:
   - Create the `retail_sales_analytics.sales` schema and volume
   - Build `silver_orders` and `silver_customers` Delta tables
   - Join and aggregate into `gold_aggregates`
3. Import `dashboard/dashboard.json` into Databricks SQL (Dashboards → Import) and point it at your `gold_aggregates` table.

---

## 📈 Key Insights

- Gurgaon and Mumbai lead in total sales volume across cities
- Platinum loyalty-tier customers have the highest average price per item
- Male customers show the highest average order value
- August 2025 saw a sharp spike in sales followed by a decline in September

---

## 🚀 Future Improvements

- Automate ingestion with Databricks Auto Loader / Jobs
- Add data quality checks (e.g., Great Expectations / Delta Live Tables expectations)
- Extend to a star schema with dedicated dimension tables
- Add CI/CD for notebook deployment

---

## 👤 Author

*Add your name, LinkedIn, and portfolio link here.*
