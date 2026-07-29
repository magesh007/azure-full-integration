# Forwards logs from Azure Service Bus

Collects log messages from an Azure Service Bus queue and forwards them to Site24x7 AppLogs.

You can configure an automated deployment by following the steps mentioned in this [document](https://www.site24x7.com/help/log-management/azure-diagnostic-logs.html).

[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fsite24x7%2Fapplogs-azure-function%2Fmaster%2Fdeployment%2Fsite24x7-azure-servicebus-logs-deployment.json)

| Field | Description |
|---|---|
| Subscription | Choose your subscription mode. |
| Resource group | Create a new resource group with a name similar to Site24x7-Azure-SB-Logs. |
| Location | Choose a location. |
| Name | The function name will be prefilled. You don't need to change it. |
| Log Type Config | Navigate to the Site24x7 web client, select **Admin > Applogs > Log Profile**, then select the created log profile, and copy the code that appears on the screen as the input for the variable logtypeConfig. |
| Service Bus Connection String | Connection string of your Service Bus namespace (needs Listen rights). Get it from **Service Bus Namespace > Shared access policies** in the Azure portal. |
| Service Bus Queue Name | Name of the queue in your namespace to collect logs from. |

### How it works

- The deployment creates a function app with a Service Bus queue trigger on your existing queue. The queue name and connection string are supplied as deployment parameters and stored in the `ServiceBusQueueName` and `AzureServiceBusConnectionString` app settings.
- Messages sent to the queue are expected to be JSON. Supported payload formats:
  - Azure diagnostic log format: `{"records": [ {...}, {...} ]}`
  - A JSON array of log events: `[ {...}, {...} ]`
  - A single JSON log event: `{...}`
- Parsed events are batched, gzip-compressed, and uploaded to the Site24x7 AppLogs endpoint configured in `logTypeConfig`.

### Changing the queue later

Update the `ServiceBusQueueName` and/or `AzureServiceBusConnectionString` app settings on the function app — no code change needed.

### Using a topic subscription instead of a queue

Replace `queueName` in [function.json](function.json) with:

```json
"topicName": "<your-topic>",
"subscriptionName": "<your-subscription>"
```
