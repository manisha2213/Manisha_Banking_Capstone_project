# Manisha_Banking_Capstone_project
Day 1 — Ingestion Layer Setup (Synopsis)

Day 1 focused on building the Ingestion Layer, which is the first and most important entry point of the Banking Data Engineering Platform. The goal was to automate the process of detecting new banking files and pushing them into the processing pipeline using event-driven Azure services.

1. Created Azure Storage Account (Raw Zone)

Set up an Azure Storage Account with ADLS Gen2 features enabled.

This storage acts as the Raw Zone, the landing area for all incoming banking files such as:

ATM transaction logs

UPI payment data

NEFT/RTGS reports

2. Developed and Deployed Event Grid Trigger Function

Created an Azure Function App (Python) to handle event-driven ingestion.

Developed an EventGridTrigger Function locally using VS Code.

Deployed the function to Azure.

The function automatically triggers when a new file is uploaded to the storage account and extracts file details for further processing.

3. Configured Event Grid → Function Integration

Enabled Event Grid on the storage account.

Created an Event Subscription that listens to the Blob Created event.

Linked Event Grid to the Event Grid Trigger Function.

Verified that every file uploaded to the raw/ container triggers the function instantly.

4. Established Message Routing Using Service Bus Queue (Optional for Day 1)

Created a Service Bus Namespace and Queue named ingestion-requests.

The Function App pushes file metadata (file path, name, URL) into the queue.

This ensures reliable, decoupled communication between ingestion and downstream processing.


Screenshots:


<img width="1280" height="638" alt="image" src="https://github.com/user-attachments/assets/bb15ffe9-7984-4d2b-9bea-21cd07bb8262" />
<img width="1280" height="640" alt="image" src="https://github.com/user-attachments/assets/7f5363b3-5c67-4593-a066-be5124b73b03" />

<img width="1366" height="768" alt="image" src="https://github.com/user-attachments/assets/1e4cb87c-9d26-4db1-b051-cf7d65964a7c" />
![WhatsApp Image 2025-12-10 at 1 26 52 AM](https://github.com/user-attachments/assets/d0e26f25-83f7-4b17-97c9-4ed731feda0b)






