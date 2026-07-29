# AppLogs-Azure-Function
This repository has Site24x7 Azure functions for collecting logs from Microsoft Azure Event Hubs, Service Bus, and Storage Blob, along with Azure ARM templates for automated deployment.

All functions share a common parser ([shared_code/log_processor.py](shared_code)) for Site24x7 log type config resolution, JSON path parsing, filtering, masking, hashing, derived fields, and upload. Each trigger folder contains only the trigger-specific glue code.

| Function | Description | Deployment |
|---|---|---|
| [Site24x7 Event Hub Function](EventHubs_Logs) | Collects and forwards Azure diagnostics logs from Event Hub to Site24x7 AppLogs. | [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmagesh007%2Fazure-full-integration%2Fmaster%2Fdeployment%2Fsite24x7-azure-deployment.json) |
| [Site24x7 Service Bus Function](ServiceBus_Logs) | Collects and forwards logs from an Azure Service Bus queue to Site24x7 AppLogs. | [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmagesh007%2Fazure-full-integration%2Fmaster%2Fdeployment%2Fsite24x7-azure-servicebus-logs-deployment.json) |
| [Site24x7 Storage Blob Function](Blob_Logs) | Collects and forwards Azure logs (diagnostic blobs, NSG / VNET flow logs, plain log files) from Azure Blob Storage to Site24x7 AppLogs. | [![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmagesh007%2Fazure-full-integration%2Fmaster%2Fdeployment%2Fsite24x7-azure-blob-logs-deployment.json) |

> **Note (development repo):** This is the UAT/development integration repo. Deploy buttons and ARM `RepoUrl` values point to `magesh007/azure-full-integration`. Before merging to the production `site24x7` repos, repoint these URLs to the production repository.

### Repository layout

```
shared_code/          Common parser used by every trigger
  log_processor.py    Config load, parse, filter, mask/hash, upload to Site24x7
  nsg_parser.py       NSG flow-log tuple parser
  vnet_parser.py      VNET flow-log tuple parser
EventHubs_Logs/       Event Hub trigger (thin wrapper)
ServiceBus_Logs/      Service Bus queue trigger (thin wrapper)
Blob_Logs/            Blob trigger (download, checkpointing) + shared parser
deployment/           ARM templates, one per source
```

The former `NsgFlow_Logs` function was removed; `Blob_Logs` supersedes it (handles NSG flow logs, VNET flow logs, and generic blobs with checkpointing).
