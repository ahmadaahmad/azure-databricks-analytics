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
```


### 2. Configure Spark OAuth settings in notebooks

Each notebook sets OAuth configs for Blob Storage and Data Lake Gen2 to enable token-based authentication:

```python
spark.conf.set(f"fs.azure.account.auth.type.{storage_account}.blob.core.windows.net", "OAuth")
spark.conf.set(f"fs.azure.account.oauth.provider.type.{storage_account}.blob.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
spark.conf.set(f"fs.azure.account.oauth2.client.id.{storage_account}.blob.core.windows.net", client_id)
spark.conf.set(f"fs.azure.account.oauth2.client.secret.{storage_account}.blob.core.windows.net", client_secret)
spark.conf.set(f"fs.azure.account.oauth2.client.endpoint.{storage_account}.blob.core.windows.net", f"https://login.microsoftonline.com/{tenant_id}/oauth2/token")
```

## Usage
###	1.	Copy data from Blob Storage to Data Lake Gen2 (Raw Layer):
Run 01_Copy_Blob_to_Datalake notebook to read CSV file from Blob Storage and write to Data Lake raw zone with date-partitioned folders.
###	2.	Verify copied files:
Run 02_Verify_Datalake_Data to list files in the raw folder and preview the data.
###	3.	Clean and transform data:
Run 03_Transform_Clean_Data to perform example cleaning steps like dropping nulls and casting columns.
###	4.	Save cleaned data as Parquet:
Run 04_Save_Cleaned_As_Parquet to write transformed data into the processed folder in Parquet format optimized for analytics.

