<img width="2345" height="1119" alt="image" src="https://github.com/user-attachments/assets/92cf933e-961e-4dd1-bb86-d02e3f4a4563" />


# Azure Spark Data Pipeline: Blob Storage to Data Lake Gen2

This project demonstrates a Spark-based ETL pipeline that copies raw CSV data from **Azure Blob Storage** to **Azure Data Lake Storage Gen2 (ADLS Gen2)** using OAuth authentication, then verifies, cleans, and saves the data as Parquet files for optimized analytics.

---

## Project Structure

| Notebook                         | Description                                                         |
|---------------------------------|---------------------------------------------------------------------|
| `00_config.ipynb`    | Setup storage access configuration, for all notebooks |
| `01_Azure_Blob_to_Datalake_Copy_OAuth`       | Reads raw CSV from Blob Storage using OAuth, writes it to ADLS Gen2 |
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
1.	Copy data from Blob Storage to Data Lake Gen2 (Raw Layer):
Run 01_Copy_Blob_to_Datalake notebook to read CSV file from Blob Storage and write to Data Lake raw zone with date-partitioned folders.
2.	Verify copied files:
Run 02_Verify_Datalake_Data to list files in the raw folder and preview the data.
3.	Clean and transform data:
Run 03_Transform_Clean_Data to perform example cleaning steps like dropping nulls and casting columns.
4.	Save cleaned data as Parquet:
Run 04_Save_Cleaned_As_Parquet to write transformed data into the processed folder in Parquet format optimized for analytics.


## Notes
	•	This pipeline uses OAuth 2.0 Client Credentials flow for secure authentication without embedding storage keys.
	•	The date-based folder structure (YYYY/MM/DD) helps organize data by ingestion date.
  <img width="2271" height="950" alt="image" src="https://github.com/user-attachments/assets/4794c142-c126-4cd1-8c3a-554fd96d6c67" />

	•	Modify cleaning and transformation logic in notebook 3 to fit your specific data requirements.
	•	Make sure your service principal has Storage Blob Data Contributor role on both storage accounts.
	•	For production, consider orchestration tools (e.g., Azure Data Factory, Databricks Jobs) to automate notebook execution.

⸻

## Troubleshooting
	•	Authentication errors: Verify client ID, secret, and tenant ID in secret scope.
	•	Access denied: Check service principal roles and permissions on storage accounts.
	•	File not found: Confirm the file path and container names in the parameters.
	•	Networking issues: Ensure your Spark cluster can access Azure Storage endpoints (firewalls, VNets).



