# Azure-Databricks-End-to-End-Data-Warehouse
## 🏗️ Data Architecture & Pipeline Topology
This project implements a multi-stage **Medallion Architecture** to process retail transaction datasets (orders, customers, products, and regional mappings) seamlessly from raw ingestion to an optimized analytical serving layer.

### 📁 Data Pipelines Breakdown
*   **Bronze Layer (Ingestion):** Leverages **Spark Structured Streaming Autoloader** with cloud file notifications to read incoming columnar Parquet files incrementally. It automatically infers schema updates and utilizes stateful `RocksDB` configurations inside checkpoint directories to guarantee strict **exactly-once (idempotent)** processing semantics.
*   **Silver Layer (Enrichment):** Performs core transformations including field isolation, string data manipulation (e.g., extracting domain indicators), and timestamp type casting using native PySpark dataframe routines. Advanced row-level ordering metrics are computed via partition-based functional **Spark Windowing** queries (`row_number`, `rank`, `dense_rank`).
*   **Gold Layer (Dimensional Modeling & Serving):** Implements a highly optimized **Star Schema** to isolate business dimensions and collect numeric facts:
    *   **Dimension Customers:** Maintained via a custom programmatic Python pipeline enforcing **SCD Type 1 (Upsert/Merge)** mechanics, automatically calculating fresh maximum surrogate keys over continuous pipeline iterations.
    *   **Dimension Products:** Maintained using **Delta Live Tables (DLT)** to construct a declarative execution graph. It automatically handles complex version historical record snapshots using **SCD Type 2** tracking alongside structural quality validation metadata constraints (**DLT Expectations**).
    *   **Fact Orders:** Combines incoming transactional parameters with corresponding dynamic dimension tracking keys while isolating numeric analysis primitives.

---

## 🛠️ Infrastructure Component Layout
The full underlying cloud-native footprint is programmatically cataloged via the included **ARM template configuration** under an unified resource directory:
*   **Storage Plane:** Azure Data Lake Storage (ADLS Gen2) configured with hierarchical namespaces isolating individual layer environments.
*   **Compute & Analytics Plane:** Shared Apache Spark runtime environments alongside managed job-scoped cluster assets for DLT handling.
*   **Data Governance:** Managed end-to-end via **Unity Catalog**, enforcing cross-resource authentication wrapper constraints via dedicated Azure Access Connectors mapping directly to ADLS scopes.
*   **Analytical Execution Engine:** Dedicated Serverless SQL Warehouses configured to optimize low-latency downstream BI and semantic queries.

---

## 🚀 How to Explore this Project
1.  **Codebase:** Explore the code logic changes saved directly within the static notebook archives.
2.  **Infrastructure Blueprint:** The underlying cloud framework and resource dependencies are recorded natively within the `/Infra_armtemp.json` template wrapper.
3.  **Visual Verification:** Pipeline orchestration DAG graphs and execution logs confirming successful end-to-end data processing are cataloged inside the `/Visual Proof` directory.
