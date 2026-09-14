# # End-to-End Azure Databricks Medallion Architecture project
This is an azure data engineering project covering ADLS gen2 medallion architecture and unity catalog


## Project Overview
This repository contains a production-grade data engineering pipeline built on Azure Databricks. It transforms raw sales data into actionable, business-ready metrics using the Medallion Architecture (Bronze, Silver, Gold). 

Beyond basic ETL, this project demonstrates "Day 2" operational maturity by integrating Unity Catalog for data governance, FinOps practices for cloud storage cost control, and a fully automated CI/CD deployment pipeline via GitHub Actions.

## Technical Stack
* **Cloud Platform:** Azure Databricks, Azure Data Lake Storage Gen2 (ADLS)
* **Compute Engine:** Apache Spark (PySpark & Spark SQL)
* **Data Format:** Delta Lake
* **Governance:** Unity Catalog
* **Orchestration:** Databricks Workflows (IaC via JSON)
* **CI/CD:** GitHub Actions, Databricks CLI

---

## 🏛️ Architecture: The Medallion Approach

1. **Bronze Layer (Raw):** Ingests raw source data exactly as it arrives. Acts as the historical archive and recovery point.
2. **Silver Layer (Cleaned & Conformed):** Data is cleansed, deduplicated, and typed. Missing values are handled, and the schema is strictly enforced.
3. **Gold Layer (Aggregated):** Business-level aggregates (e.g., daily sales, revenue by status) optimized for downstream BI tools like Power BI.

---

## 🚀 Key Engineering Highlights

### 1. Advanced Delta Lake Optimization & FinOps
To solve the "small file problem" and ensure optimal read performance for BI engines, the pipeline implements a targeted optimization strategy:
* **Z-Ordering:** Applied strictly to high-cardinality filtering columns in the massive Silver tables to maximize data-skipping efficiency.
* **Bin-Packing:** Standard `OPTIMIZE` applied to low-cardinality Gold aggregates to conserve cluster compute.
* **Tombstone Cleanup:** Automated `VACUUM` commands purge unreferenced Parquet files older than 7 days, preventing silent Azure Blob Storage cost inflation.

### 2. Unity Catalog Governance
Transitioned away from legacy Hive Metastore (`hive_metastore`) to Unity Catalog. Configured secure external locations, storage credentials, and managed catalogs to ensure strictly governed data access boundaries.

### 3. Infrastructure-as-Code (IaC) & Automation
* **Workflow Orchestration:** Pipeline logic is orchestrated via Databricks Workflows, exported as JSON for disaster recovery and version control.
* **CI/CD Pipeline:** Integrated GitHub Actions with a securely scoped Databricks Personal Access Token. Every merge to the `main` branch triggers the Databricks CLI to automatically sync the workspace Repos, eliminating manual "ClickOps" deployments and preventing version drift.

---

## 📁 Repository Structure
* `/notebooks`: Core PySpark and Spark SQL ETL scripts for Bronze, Silver, and Gold transformations.
* `/orchestration`: Contains the `portfolio_medallion_etl.json` job definition.
* `/docs`: Detailed architectural logs, error resolution analyses, and optimization methodologies.
* `/.github/workflows`: Contains `databricks_sync.yml` for automated CI/CD deployments.
