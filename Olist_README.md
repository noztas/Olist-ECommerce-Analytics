# Olist E-Commerce Analytics

**End-to-end data pipeline and analytics dashboard built on 100K+ real e-commerce transactions**

Built with Databricks (PySpark + SQL), Delta Lake, Unity Catalog, and Power BI — following Medallion Architecture (Bronze → Silver → Gold).

---

## The Business Problem

Olist is a Brazilian e-commerce marketplace connecting small merchants to major retail channels. With 100K+ orders across 73 product categories, 3,000+ sellers, and customers in every Brazilian state, the business needs to understand:

- **Revenue:** What's driving growth and where is it coming from?
- **Products:** Which categories generate the most revenue — and which have quality issues?
- **Delivery:** Are orders arriving on time? Where are the bottlenecks?
- **Customers:** Are customers coming back, or is this a one-time marketplace?

This project answers all four questions through a complete data pipeline and interactive dashboard.

---

## Architecture

```
Raw CSVs (9 files)
    │
    ▼
┌──────────────┐
│   BRONZE     │  Raw data → Delta tables (no transformations)
│  9 tables    │  Preserves source of truth
└──────┬───────┘
       │
       ▼
┌──────────────┐
│   SILVER     │  Cleaned + standardized individual tables
│  9 tables    │  Type casting, null normalization, validation flags
│              │  Feature engineering (delivery metrics, freight ratio)
└──────┬───────┘
       │
       ▼
┌──────────────┐
│   SILVER     │  7-table JOIN into one analysis-ready dataset
│  ENRICHED    │  Pre-aggregated payments & reviews to prevent row multiplication
│  112K rows   │  ~30 columns covering orders, products, customers, sellers
└──────┬───────┘
       │
       ▼
┌──────────────┐
│    GOLD      │  Business KPI tables (aggregated answers)
│  5 tables    │  Monthly revenue, seller performance, product analysis,
│              │  delivery performance, customer segments
└──────┬───────┘
       │
       ▼
┌──────────────┐
│  POWER BI    │  5-page interactive dashboard
│  DASHBOARD   │  Executive overview, products, delivery, customers, summary
└──────────────┘
```

### Pipeline DAG (Databricks Workflows)

![Pipeline DAG](screenshots/pipeline_dag.png)

*12 tasks, dependency-aware execution. All Silver tables run in parallel after Bronze, funnel through Silver Enriched, then produce Gold analytics. Full pipeline runs in ~9 minutes.*

---

## Key Findings

### 📈 Revenue Growth
- **120% YoY revenue growth** to R$ 16M in 2018, with order volume tripling between Q1 and Q4 2017
- **Health & Beauty and Watches** drive 30% of total GMV
- Top 5 categories generate 60% of all revenue — concentration creates both opportunity and risk

### 🚚 Delivery Performance
- Average delivery time: **12 days**, but Olist consistently delivers **~12 days earlier than promised** — an underpromise/overdeliver tactic protecting review scores
- Delivery times **improved 40%** from peak (17 days) to August 2018
- **North/South divide:** Northern states (AP: 28 days) wait 2× longer than São Paulo — the gap is logistical, not categorical

### 👥 Customer Retention
- **96% of customers are one-time buyers** — repeat purchase rate is only 3.05%
- The 3% repeat segment spends **2–3× more per order** (R$ 779 vs R$ 161)
- VIP customers rate higher (4.4 vs 4.1) — satisfaction drives loyalty
- Acquisition matters more than retention at this stage

### 📦 Product Quality
- Diapers, security products, and furniture have the **lowest review scores** (2.5–3.7) — these categories need immediate quality investigation
- Housewares has the **highest freight ratio** (40% of order value) — shipping costs eat into the customer experience

### 💡 Recommendations
1. **Focus logistics improvement** on northern states (AP, RR, AM) where delivery takes 25+ days
2. **Investigate low-rated categories** — security (2.5 stars) and diapers (3.4 stars) need quality audits
3. **Implement customer retention program** — loyalty rewards, post-purchase emails, repeat purchase incentives
4. **Optimize freight costs** for housewares and furniture decor where shipping exceeds 35% of order value

---

## Dashboard

### Executive Overview
![Executive Overview](screenshots/executive_overview.png)

### Product Category Analysis
![Product Analysis](screenshots/product_analysis.png)

### Delivery Performance
![Delivery Performance](screenshots/delivery_performance.png)

### Customer Analysis
![Customer Analysis](screenshots/customer_analysis.png)

### Summary Dashboard
![Summary Dashboard](screenshots/summary_dashboard.png)

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| **Databricks** | Platform — Unity Catalog, Delta Lake, Workflows |
| **PySpark** | All Silver-layer transformations and feature engineering |
| **SQL** | Silver enriched JOINs and Gold-layer analytics |
| **Delta Lake** | Storage format — ACID transactions, schema enforcement |
| **Power BI** | Interactive dashboard with DAX measures |
| **GitHub** | Version control with structured commits |
| **Python / pandas** | Initial data exploration and auditing |

---

## Project Structure

```
Olist-ECommerce-Analytics/
│
├── 01_bronze_ingestion          # Raw CSV → Delta tables (infrastructure setup)
│
├── 02a_silver_customers         # Clean customer dimension
├── 02b_silver_sellers           # Clean seller dimension
├── 02c_silver_products          # Clean products (categories, typos, dimensions)
├── 02d_silver_orders            # Clean + engineer orders (delivery metrics, timeline validation)
├── 02e_silver_order_items       # Clean + engineer items (DECIMAL, freight ratio)
├── 02f_silver_order_payments    # Clean + engineer payments (installments, vouchers)
├── 02g_silver_order_reviews     # Clean + engineer reviews (type casting, response time)
├── 02h_silver_geolocation       # Clean + aggregate geolocation (avg lat/lng per zip)
├── 02i_silver_category_translation  # Clean category name translations
│
├── 03_silver_enriched           # 7-table JOIN with pre-aggregated payments/reviews
│
├── 04_gold_analytics            # 5 Gold KPI tables (revenue, sellers, products, delivery, customers)
│
├── 05_pipeline_orchestration    # Databricks Workflow DAG — automated end-to-end execution
│
├── Olist_Dashboard.pbix         # Power BI dashboard file
│
├── screenshots/                 # Pipeline DAG, dashboard pages, data model
│
└── README.md
```

---

## Dataset

**Olist Brazilian E-Commerce** — real transactional data from a real marketplace ([Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce))

| Table | Rows | Description |
|-------|------|-------------|
| orders | 99,441 | Core table — timestamps, status, customer link |
| order_items | 112,650 | Line items — product, seller, price, freight |
| customers | 99,441 | Customer location (city, state, zip) |
| payments | 103,886 | Payment type, installments, value |
| reviews | 99,224 | Star ratings and optional comment text |
| products | 32,951 | Category, weight, dimensions |
| sellers | 3,095 | Seller location |
| geolocation | 1,000,163 | Lat/lng per zip code |

**Date range:** September 2016 – October 2018

---

## Data Engineering Highlights

### Consistent Cleaning Patterns
Every Silver notebook follows the same recipe: dynamic string trimming (schema-driven, no hardcoded columns), null normalization (converting garbage strings like "N/A", "null", "" into true database NULLs), table-specific transformations, validation flags, and quality checks.

### Null Normalization
```python
NULL_STRINGS = ["", "null", "none", "n/a", "na", "unknown", "-"]

for field in df.schema.fields:
    if isinstance(field.dataType, StringType):
        df = df.withColumn(
            field.name,
            when(lower(trim(col(field.name))).isin(NULL_STRINGS), None)
            .otherwise(col(field.name))
        )
```
Converts all garbage string representations of "missing" into true NULLs so downstream `fillna()` and `.isNull()` work consistently.

### Three-Way Delivery Classification
```python
df = df.withColumn(
    "is_delivered_late",
    when(col("order_delivered_customer_date") > col("order_estimated_delivery_date"), True)
    .when(col("order_delivered_customer_date").isNotNull(), False)
    .otherwise(None)  # Not delivered = unknown, not "not late"
)
```
True = late, False = on time, None = not yet delivered. This prevents undelivered orders from artificially lowering the late delivery rate.

### Pre-Aggregation Before JOINs
Payments and reviews are aggregated to one row per order BEFORE joining to prevent row multiplication. Without this, an order with 3 payment methods would triple every line item row.

```sql
CREATE OR REPLACE TEMP VIEW payments_agg AS
SELECT
    order_id,
    ROUND(SUM(payment_value), 2) AS total_payment_value,
    COUNT(DISTINCT payment_sequential) AS payment_method_count,
    MAX_BY(payment_type, payment_value) AS primary_payment_type
FROM olist.silver.order_payments
GROUP BY order_id
```

### Conditional Aggregation (Gold Layer)
```sql
-- Late delivery rate: only counts delivered orders in the denominator
COUNT(DISTINCT CASE WHEN is_delivered_late THEN order_id END) * 100.0
/ NULLIF(COUNT(DISTINCT order_id), 0) AS late_delivery_pct
```

---

## Gold Layer — Business KPI Tables

| Table | Rows | Business Question |
|-------|------|-------------------|
| monthly_revenue | ~25 | Revenue, orders, AOV, satisfaction by month |
| seller_performance | ~3,000 | Revenue, delivery speed, late rate per seller |
| product_category_analysis | ~1,500 | Revenue, units, freight ratio per category per month |
| delivery_performance | ~650 | Delivery days, late %, by state and month |
| customer_segments | ~96,000 | One-time / Occasional / Frequent classification |

---

## Power BI Dashboard

The dashboard uses a **hybrid data model**:
- **KPI cards** → DAX measures against `orders_enriched` (dynamic filtering)
- **Charts** → Gold tables directly (pre-aggregated, fast rendering)
- **DateTable** → Proper date dimension with Year-Month hierarchy, marked as date table

### Key DAX Measures
```dax
Total Revenue = SUM(orders_enriched[total_item_value])
Total Orders = DISTINCTCOUNT(orders_enriched[order_id])
Avg Order Value = DIVIDE([Total Revenue], [Total Orders])
Late Delivery % = DIVIDE(
    CALCULATE(DISTINCTCOUNT(orders_enriched[order_id]), orders_enriched[is_delivered_late] = TRUE),
    CALCULATE(DISTINCTCOUNT(orders_enriched[order_id]), NOT ISBLANK(orders_enriched[order_delivered_customer_date]))
)
```

---

## How to Run This Project

### Prerequisites
- Databricks workspace with Unity Catalog
- Power BI Desktop (Windows)
- Olist dataset from [Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)

### Steps
1. Upload the CSV files to a Databricks Volume
2. Run notebooks in order: `01` → `02a-02i` (parallel) → `03` → `04`
3. Or use the Databricks Workflow (`05_pipeline_orchestration`) to run everything automatically
4. Open `Olist_Dashboard.pbix` in Power BI Desktop
5. Connect to your Databricks SQL Warehouse (or use the pre-loaded data)

---

## What I'd Do Differently in Production

1. **Explicit schemas** instead of `inferSchema` — safer against source data changes
2. **Automated data quality tests** (Great Expectations or Databricks expectations) with alerting
3. **Incremental loading** (merge/upsert) instead of full overwrite for new data
4. **Monitoring and logging** for pipeline failures and data quality metrics
5. **Partitioning** large tables by date for query performance at scale
6. **Scheduled refresh** — Databricks Workflow on daily trigger + Power BI scheduled refresh

---

## About Me

**Neslihan Oztas Ates** — Data Analyst based in Ingolstadt, Germany

Background in biology and biochemistry with applied experience in biostatistics and data analysis. Transitioned into data analytics, building a modern toolkit across SQL, Python, PySpark, Power BI, Tableau, and Databricks.

- 📧 neslihanoztas1@gmail.com
- 💼 [LinkedIn](https://www.linkedin.com/in/neslihanoztas/)
- 🌐 [Portfolio Website](https://noztas.github.io/Portfolio-Website/)
- 💻 [GitHub](https://github.com/noztas/)
