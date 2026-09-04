# Databricks_scm_elt

# Supply Chain Logistics Lakehouse: End-to-End ELT Pipeline

An enterprise-grade ELT pipeline built on Databricks and Delta Lake, processing over 22,000 warehouse operational records. The project implements a Medallion Architecture (Bronze $\rightarrow$ Silver $\rightarrow$ Gold) to clean inconsistent logistics telemetry, handle non-trivial missing data, and deliver business-ready dimensional aggregations for supply chain bottleneck analysis.

---

## Architecture Overview
▼ (Spark Auto Loader / Ingestion + Metadata Tagging)
[Bronze Layer: bronze_scm_raw]
│  • Raw ingestion preserving lineage (ingestion_timestamp, source_file)
│
▼ (ELT Transformation & Data Quality Rules)
[Silver Layer: silver_scm_cleansed]
│  • Impute missing worker counts using capacity-stratified medians
│  • Standardize compliance certificates ('Uncertified' fallback)
│  • Flag missing establishment years and cast operational metrics
│
▼ (Analytical Aggregations & Dimensional Modeling)
[Gold Layer: gold_scm_kpis]
│  • Regional throughput (tons shipped)
│  • Risk scoring (breakdown frequency, transport bottlenecks)
│  • Labor efficiency (throughput ton per worker)
▼
[Databricks SQL / Interactive Visualizations]


## Problem Statement & Data Quality Challenges

Supply chain logs often suffer from incomplete reporting and operational gaps. The raw telemetry dataset (`22,150` warehouse records across 24 attributes) contained several data quality hurdles addressed during the ELT phase:

* **Missing Operational Labor Data:** `workers_num` was null in 877 facilities. Instead of dropping records or applying a global mean, values were imputed using group-level medians segmented by warehouse size (`WH_capacity_size`).
* **Compliance Certification Gaps:** 805 facilities lacked government inspection cert tags. A standardized `'Uncertified'` category was assigned to retain records for risk analysis.
* **Structural Missingness:** 47.6% of facilities lacked `wh_est_year`. Built an indicator flag (`is_est_year_missing`) to preserve data lineage without skewing vintage calculations.

---

## Data Pipeline Implementation

1. Bronze Layer: Raw Ingest & Lineage Tracking
Data is ingested directly from Unity Catalog Volumes into managed Delta tables without destructively changing data types.


2. Silver Layer: Data Cleaning & Windowed Imputation
Imputation logic and data type casting are applied using Databricks SQL.


3. Gold Layer: Logistics & KPI Aggregates
Calculates regional metrics, operational breakdown frequency, and weight distributions.


Key Analytical InsightsThroughput by Zone: Large warehouses in the Northern and Western zones drive over 60% of total product tonnage.Infrastructure Reliability Impact: Warehouses lacking temperature regulation machinery (temp_reg_mach = 0) exhibited a 2.4x higher rate of reported storage degradation in the last 3 months.Transit Distance Correlation: Warehouses situated $>150\text{ km}$ from regional distribution hubs experienced a 38% increase in annual transport delays.Tech StackPlatform: Databricks (Serverless Compute / Unity Catalog)Storage / Table Format: Delta Lake (ACID Transactions, Time Travel, Schema Enforcement)Languages: PySpark, Spark SQL, PythonAnalytics & Visualization: Databricks SQL Visualizations, Matplotlib, Seaborn.




