DAY 1 — Architecture & Ingestion Objectives

• Create a Storage Account and required containers

• Enable Event Grid notifications on blob upload events

• Create a Service Bus namespace and queue

• Build and deploy an Azure Function skeleton that responds to Event Grid events

Create Storage Account

Azure Portal → Storage Accounts → + Create

Basics Tab

• Select Subscription

• Select Resource Group

• Storage account name: stazbank (all lowercase)

Advanced Tab

• Enable Data Lake Storage Gen2

• Set Hierarchical namespace = Enabled

Networking

• Allow Public access (development mode; will secure later)

Create Containers

Portal → Storage Account → Containers → + Container

Create the following containers/folders:

• raw/transactions (subfolders will be added inside)

• raw/customers

• raw/atm

• raw/upi

Enable Event Grid on Storage Container

Event Grid will detect “Blob Created” events.

Portal → Storage Account → Events → + Event Subscription

• Event Type: Blob Created

• Endpoint Type: Service Bus (or Event Grid Topic if linking later)

• Keep endpoint blank if required until Service Bus is ready

This prepares the storage account to publish blob-upload notifications.

Create Service Bus Namespace + Queue/Topic

Portal → Service Bus → + Create

Settings:

• Pricing Tier: Basic

• Choose Resource Group and Region

After creation:

Portal → Service Bus Namespace → Queues → + Queue

Create queue name: ingestion-requests

This queue will receive ingestion jobs from Event Grid or from the Azure Function.

Create Azure Function App (Python) — Ingestion Skeleton

Portal → Function App → + Create

Basics:

• Runtime Stack: Python

• Operating System: Windows or Linux

• Hosting: Consumption Plan

After creation:

Portal → Function App → Functions → + Add

• Choose Create from template

• Select Event Grid Trigger

This will act as the ingestion entry point.

Prepare Local Function Project in VS Code

On your system:

• Create a new folder (example: azureproject-function-ingest)

• Open the folder in VS

• Initialize a Function App project

• Set up required Python packages (requirements.txt)

• Add connection strings inside local.settings.json

– Storage account connection string

– Service Bus connection string

Once configuration is done:

Deploy to Function App

VS Code → Azure Extension → Function App → Right-click → Deploy

Select your Function App in Azure to push the ingestion function.

DAY 2 — Transformation Layer & NoSQL Operational Store (Hands-on) Objectives

• Create Cosmos DB (Core SQL API)

• Design containers with correct partition keys

• Build Service Bus queue-trigger Azure Function

• Fetch blob data, validate, clean, and deduplicate

• Start Databricks/Spark PySpark transformation pipeline

Create Cosmos DB (Core SQL API)
Azure Portal → Azure Cosmos DB → Create → API: Core (SQL)

• Choose Provisioned throughput or Serverless mode

• Create a new database (if required)

• Create the following containers inside Data Explorer:

• AccountProfile — partition key: /CustomerID

• ATMTransactions — partition key: /TransactionID

• UPIEvents — partition key: /TransactionID

• FraudAlerts — partition key: /CustomerID

These containers will store validated, structured, enriched banking data.

Create Queue-Triggered Function (Processing Layer)
Create a new Function App or add a new function in existing App:

• Template: Service Bus Queue Trigger

• Function name: func-azbank-processor

• Bind to queue: ingestion-requests

Processing logic performed inside the function:

• Read message from Service Bus

• Parse details to locate file in ADLS

• Download blob content (CSV/JSON)

• Identify file type (ATM / UPI / Transactions / Customer)

• Validate schema and required fields

• Clean invalid or null records

• Deduplicate using Upsert (TransactionID or CustomerID as unique ID)

• Insert processed records into respective Cosmos DB containers

Implement Deduplication Strategy
• Use Cosmos DB Upsert operation

• If document with same TransactionID exists → update or skip

• Prevents double entries from duplicate uploads

• Ensures data consistency across ingestion attempts

Create PySpark Data Cleaning Pipeline (Databricks / Synapse Spark)
• Create a Databricks Workspace or Synapse Spark Pool

• Create a new Notebook (PySpark)

Notebook tasks:

• Read Bronze/raw data from ADLS using Spark

• Read enriched data from Cosmos DB using Spark connector

• Normalize schema (datatype fixes: timestamps, floats, integers)

• Drop duplicates and bad records

• Apply business-level transformations

• Write cleaned data into:

• Silver layer (cleaned, structured)

• Gold layer (aggregated, analytics-ready)

This transforms scattered raw files into BI-consumable datasets.

Validate End-to-End Processing
• Upload sample banking file into ADLS

• Ensure Event Grid → Queue → Processor Function works

• Verify cleaned records appear inside Cosmos DB containers

• Run PySpark notebook and confirm Silver/Gold layers are generated

✔ END OF DAY 2 DELIVERABLES

• Cosmos DB account and containers created

• Queue-trigger processing function implemented

• File parsing, validation, cleaning, and deduplication completed

• Records successfully written into Cosmos DB • Databricks PySpark pipeline created for Bronze → Silver → Gold transformation

DAY 3 — Databricks ETL Pipeline (Bronze → Silver → Gold) Objectives

• Build multi-layer ETL using Azure Databricks and Spark

• Convert raw data into clean, structured, analytics-ready datasets

Bronze Layer Processing

• Load raw files into Databricks

• Apply a defined schema to ensure consistency

• Add ingestion metadata such as timestamp and source information

• Handle corrupt or malformed records

• Store structured raw data for safe retention

Silver Layer Processing

• Clean and standardize Bronze data

• Remove duplicates

• Correct data types

• Normalize formats

• Validate business rules

• Merge related datasets for consolidated views

Gold Layer Processing

• Aggregate Silver data to create analytical summaries

• Generate KPIs and metrics (daily totals, patterns, trends)

• Add derived columns such as transaction categories or risk levels

• Prepare final datasets optimized for dashboards and fraud detection

Validation

• Check record counts across all stages

• Verify schema consistency

• Confirm transformation accuracy

DAY 4 — Fraud Detection Engine & Cosmos DB Integration

Objectives

• Build a rule-based fraud detection engine

• Store fraud alerts in a scalable NoSQL database

Fraud Detection Rules Implemented

• High-value transaction rule

• Rapid multiple transactions rule

• Unusual location or ATM usage rule

• Sudden spike in UPI activity rule

Fraud Score & Alert Generation

Each alert includes:

• Customer identifier

• Timestamp

• Transaction amount

• Triggered fraud rule

• Calculated risk score

• Additional context

Cosmos DB Integration

• Create Cosmos DB database and container

• Send generated fraud alerts to Cosmos DB

• Ensure alerts are stored in a searchable, scalable format

Testing

• Use sample data to trigger fraud rules

• Validate alerts appear correctly in the Cosmos DB container

• Test end-to-end alert flow

DAY 5 — Power BI Dashboard & End-to-End Validation

Objectives

• Build Power BI dashboards using processed datasets

• Visualize transaction patterns, performance, and fraud alerts

• Validate the entire architecture from ingestion to visualization

Dashboard Pages Created

Transaction Analytics
• Daily transaction trends

• Total volume KPIs

• Channel-wise distribution

ATM & UPI Insights
• ATM activity trends

• UPI transaction patterns

• Peak hours and geographic patterns

Fraud Monitoring
• Fraud alerts

• Daily fraud trend graph

• High-risk account indicators

End-to-End Pipeline Test

• Upload a new input file

• Event Grid triggers ingestion Function

• Service Bus processes metadata

• Queue Trigger Function extracts details

• Databricks ETL updates data layers

• Fraud engine generates new alerts

• Power BI dashboard refreshes with updated data

Project Deliverables Completed

• Fully automated Azure data pipeline

• Clean layered ETL architecture

• Fraud detection engine integrated with Cosmos DB

• Interactive dashboards for analytics and monitoring

• Complete documentation for deployment and review

FINAL PROJECT SUMMARY (Copy-Paste Ready)

• Implemented a complete cloud-based Banking Data Engineering pipeline.

• Automated ingestion built using Storage Account, Event Grid, Service Bus, and Function Apps.

• Designed a multi-layer ETL architecture using Databricks for structured processing.

• Developed a fraud detection engine with rule-based logic integrated into Cosmos DB.

• Built Power BI dashboards to visualize transactions, trends, and fraud insights.

• Successfully validated the pipeline end-to-end, ensuring accuracy, automation, and scalability.

Screenshots:*
<img width="1280" height="638" alt="image" src="https://github.com/user-attachments/assets/eee56819-8228-49e6-8786-bb4477cfd85c" />

<img width="1360" height="663" alt="image" src="https://github.com/user-attachments/assets/ed674750-82f0-4ed3-b0b1-65ab28196c85" />

<img width="1280" height="639" alt="image" src="https://github.com/user-attachments/assets/89af6268-5af4-4c5b-b04f-c0fa3f0b5101" />

<img width="1280" height="640" alt="image" src="https://github.com/user-attachments/assets/80e4c7f3-71c2-4ebf-aa70-8a0ac33bdee3" />

<img width="744" height="380" alt="image" src="https://github.com/user-attachments/assets/1fa2f609-68f7-44ad-9872-572ffe9551d9" />

<img width="537" height="593" alt="image" src="https://github.com/user-attachments/assets/fc1df5a2-ccc8-4c72-81e3-577041da972c" />

<img width="1280" height="609" alt="image" src="https://github.com/user-attachments/assets/1b373bc2-de44-4d87-8e8b-928f9b0e4d26" />

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/c05c31dc-cf81-4bd9-a1bc-ac02814f408a" />

<img width="1093" height="761" alt="image" src="https://github.com/user-attachments/assets/98698991-5444-401a-88ae-70787ac9d1ee" />

<img width="835" height="653" alt="image" src="https://github.com/user-attachments/assets/784bff23-3e79-4a31-9171-401e59831f17" />

<img width="1012" height="735" alt="image" src="https://github.com/user-attachments/assets/5031277a-26e0-4024-9a6e-baeaf1d81392" />

<img width="1013" height="467" alt="image" src="https://github.com/user-attachments/assets/1cdfa28b-d931-479a-9e90-d2c0aaab0431" />

<img width="1280" height="657" alt="image" src="https://github.com/user-attachments/assets/d84e2d82-7bf7-4da0-a5bb-875838cf04ac" />

<img width="1022" height="733" alt="image" src="https://github.com/user-attachments/assets/9b826961-a0c6-478f-ac26-ef71d8eab78a" />































