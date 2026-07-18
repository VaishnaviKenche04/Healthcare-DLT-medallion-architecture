# Healthcare-DLT-medallion-architecture
A medallion-architecture (Bronze → Silver → Gold) data pipeline built on Databricks Delta Live Tables (DLT), processing daily patient admission data and diagnosis reference data into analytics-ready tables, with data quality enforced through DLT expectations and automated scheduling via Databricks Workflows.

<img width="1702" height="767" alt="Dlt_pipeline_flow" src="https://github.com/user-attachments/assets/35a178b5-38ea-4cb3-834a-d9dcf0843626" />
What it does

Bronze layer


diagnostic_mapping — batch-loaded reference table mapping diagnosis codes to human-readable descriptions. Drops rows with a null diagnosis code or description.
daily_patients — streaming ingestion (STREAM()) of daily patient admissions. Drops rows missing a patient ID or any required demographic/admission field (name, age, gender, address, contact number, admission date).


Silver layer


processed_patient_data — left-joins streaming patient records to the diagnosis mapping table to attach a readable diagnosis description. Drops rows where no diagnosis description could be resolved.


Gold layer — three aggregate tables built off the silver table for downstream reporting:


patient_statistics_by_admission_date — patient count and average age per admission date + diagnosis.
patient_statistics_by_diagnosis — patient count, age stats (avg/min/max), and gender diversity per diagnosis.
patient_statistics_by_gender — patient count, age stats, and diagnosis diversity per gender.


Data quality — every bronze/silver table uses DLT CONSTRAINT ... EXPECT (...) ON VIOLATION DROP ROW expectations, so bad records are dropped before they reach downstream tables, with drop counts visible in the DLT pipeline UI.

<img width="1312" height="542" alt="sample_data" src="https://github.com/user-attachments/assets/93b3f608-95ec-4c1a-9d76-142c14da162d" />



Orchestration — the DLT pipeline is scheduled via Databricks Workflows for automated, recurring runs (no manual trigger needed).

**Tech stack**

Databricks Delta Live Tables · Delta Lake · SQL · Unity Catalog · DLT Expectations · Databricks Workflows
