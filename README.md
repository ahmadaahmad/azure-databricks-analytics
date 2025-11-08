# Azure Spark Data Pipeline: Blob Storage to Data Lake Gen2

This project demonstrates a Spark-based ETL pipeline that copies raw CSV data from **Azure Blob Storage** to **Azure Data Lake Storage Gen2 (ADLS Gen2)** using OAuth authentication, then verifies, cleans, and saves the data as Parquet files for optimized analytics.

---

## Project Structure

| Notebook                         | Description                                                         |
|---------------------------------|---------------------------------------------------------------------|
| `01_Copy_Blob_to_Datalake`       | Reads raw CSV from Blob Storage using OAuth, writes it to ADLS Gen2 |
| `02_Verify_Datalake_Data`        | Lists and previews raw files copied to ADLS Gen2                    |
| `03_Transform_Clean_Data`        | Cleans and transforms raw data (drops nulls, casts types, etc.)     |
| `04_Save_Cleaned_As_Parquet`     | Saves cleaned data as Parquet files in ADLS Gen2                    |

---

## Prerequisites

- Apache Spark environment (e.g., Azure Databricks)
- Azure Storage Accounts:
  - Blob Storage with CSV data
  - Data Lake Storage Gen2 for raw and processed data
- Azure Service Principal credentials (Client ID, Secret, Tenant ID) stored securely in Databricks Secrets or environment variables
- Permissions for the service principal to access both Blob Storage and Data Lake Gen2

---

## Setup Instructions

### 1. Store secrets in Databricks Secret Scope

Store your Azure AD Service Principal credentials securely:

```bash
dbutils.secrets.put(scope="salesdbscope", key="client-id", string_value="<your-client-id>")
dbutils.secrets.put(scope="salesdbscope", key="secret-id", string_value="<your-client-secret>")
dbutils.secrets.put(scope="salesdbscope", key="tenant-id", string_value="<your-tenant-id>")
