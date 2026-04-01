# Bike Shop Data Platform - ETL Pipeline

## Project Summary
This project implements the foundational Data Engineering and ETL (Extract, Transform, Load) pipeline for our Bike Shop's modern Data Lakehouse. Specifically, this repository contains the **Bronze Layer** ingestion processes, which are responsible for extracting raw data from our primary operational systems—our Customer Relationship Management (CRM) system and Enterprise Resource Planning (ERP) system—and loading it into our Data Lakehouse using Delta tables.

By establishing this automated ingestion pipeline, the Bike Shop can consolidate disparate data sources (like customer profiles, product catalogs, sales records, and location data) into a single source of truth. This empowers our analysts and data scientists to build robust downstream reporting, track inventory efficiently, understand customer behaviors, and ultimately make data-driven decisions to increase sales and optimize operations.

## Architecture & Data Flow

The pipeline is built on **Apache Spark** and utilizes **Delta Lake** architecture on **Databricks** for reliable and scalable data processing. 

The data flows from raw storage volumes into the `bronze` schema of our data catalog.

### Data Sources
1. **CRM System (`source_crm`)**:
   - `cust_info.csv`: Customer contact and demographic information.
   - `prd_info.csv`: Information regarding bicycle products, parts, and accessories.
   - `sales_details.csv`: Granular sales transaction records.

2. **ERP System (`source_erp`)**:
   - `CUST_AZ12.csv`: Internal ERP customer account mapping.
   - `LOC_A101.csv`: Store and warehouse location master data.
   - `PX_CAT_G1V2.csv`: Procurement and inventory categorization data.

## Ingestion Process Details

The primary ingestion process is governed by the `ingestion.ipynb` Databricks notebook. The execution flow is professionally structured into the following steps:

1. **Initialize Environment**: The pipeline initializes the necessary logging configuration and imports PySpark SQL functions alongside Delta Lake modules.
2. **Load Utilities**: Executes common utility scripts (`/common/utilities`) required for a standardized pipeline execution framework.
3. **Parameterization (Widgets)**: Sets up Databricks widgets to allow dynamic execution environments. Defaults are configured for:
   - `catalog`: Target Databricks Unity Catalog (e.g., `abcgroup`)
   - `volume`: Target storage volume (e.g., `raw_sources`)
   - `data_source_one`: CRM source directory
   - `data_source_two`: ERP source directory
4. **Capture Configuration**: Retrieves dynamic parameters to be used in the current pipeline run.
5. **Configure Ingestion Paths**: Maps the raw CSV file paths from the volume to their respective target Delta table names in the bronze schema (e.g., `crm_cust_info`, `erp_loc_a101`).
6. **Execute Ingestion**: Iteratively reads each CSV file utilizing Spark (inferring schema and headers) and writes the data as a Delta table into the target catalog and schema using `overwrite` mode.

## Prerequisites
- **Databricks Workspace**: The pipeline is designed to run as a Databricks Notebook.
- **Apache Spark & Delta Lake**: The environment must support PySpark (`pyspark.sql`) and Delta table operations (`delta.tables`).
- **Storage Volumes**: Unity Catalog volumes must be configured with the necessary raw CSV files present in the specified directory structure.

## Deployment & Execution
To deploy and execute this pipeline:
1. Ensure the raw files are uploaded to the corresponding volume paths (e.g., `/Volumes/[catalog]/[bronze_schema]/[volume]/source_crm/`).
2. Attach the `ingestion.ipynb` notebook to an active Databricks cluster.
3. Provide the necessary parameters via the interactive widgets at the top of the notebook or pass them dynamically if running as a Databricks Job.
4. Execute the notebook. Check the logging output to verify that all tables were successfully ingested and saved in the Delta format.