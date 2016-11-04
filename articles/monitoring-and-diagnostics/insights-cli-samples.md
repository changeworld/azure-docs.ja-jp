---
title: 'Azure Insights: Azure Insights CLI quick start samples. | Microsoft Docs'
description: Sample CLI command can help you access Azure Insights monitoring features. Azure Insights is a Microsoft Azure service which allows you to autoScale Cloud Services, Virtual Machines, and Web Apps, send alert notifications, or call web URLs based on values of configured telemetry data.
author: kamathashwin
manager: ''
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: ashwink

---
# <a name="azure-insights-cross-platform-cli-quick-start-samples"></a>Azure Insights Cross-platform CLI quick start samples
This article shows you sample command-line interface (CLI) commands to help you access Azure Insights monitoring features. Azure Insights allows you to AutoScale Cloud Services, Virtual Machines, and Web Apps and to send alert notifications or call web URLs based on values of configured telemetry data.

## <a name="prerequisites"></a>Prerequisites
If you haven't already installed the Azure CLI, see [Install the Azure CLI](../xplat-cli-install.md). If you're unfamiliar with Azure CLI, you can read more about it at [Use the Azure CLI for Mac, Linux, and Windows with Azure Resource Manager](../xplat-cli-azure-resource-manager.md).

In Windows, install npm from the [Node.js website](https://nodejs.org/). After you complete the installation, using CMD.exe with Run As Administrator privileges, execute the following from the folder where npm is installed:

```
npm install azure-cli --global
```

Next, navigate to any folder/location you want and type at the command-line:

```
azure help
```

## <a name="log-in-to-azure"></a>Log in to Azure
The first step is to login to your Azure account.

```
azure login
```

After running this command, you have to sign in via the instructions on the screen. Afterward, you see your Account, TenantId, and default Subscription Id. All commands work in the context of your default subscription.

To list the details of your current subscription, use the following command.

```
azure account show
```

To change working context to a different subscription, use the following command.

```
azure account set "subscription ID or subscription name"
```

To use Azure Resource Manager and Azure Insights commands, you need to be in Azure Resource Manager mode.

```
azure config mode arm
```

To view a list of all supported Azure Insights commands, perform the following.

```
azure insights
```

## <a name="view-audit-logs-for-a-subscription"></a>View audit logs for a subscription
To view a list of audit logs, perform the following.

```
azure insights logs list [options]
```

Try the following to view all available options.

```
azure insights logs list -help
```

Here is an example to list logs by a resourceGroup

```
azure insights logs list --resourceGroup "myrg1"
```

Example to list logs by caller

```
azure insights logs list --caller "myname@company.com"
```

Example to list logs by caller on a resource type, within a start and end date

```
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Work with alerts
You can use the information in the section to work with alerts.

### <a name="get-alert-rules-in-a-resource-group"></a>Get alert rules in a resource group
```
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### <a name="create-a-metric-alert-rule"></a>Create a metric alert rule
```
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### <a name="create-a-log-alert-rule"></a>Create a log alert rule
```
azure insights alerts rule log set ruleName eastus resourceGroupName someOperationName
```

### <a name="create-webtest-alert-rule"></a>Create webtest alert rule
```
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### <a name="delete-an-alert-rule"></a>Delete an alert rule
```
azure insights alerts rule delete abhingrgtest123 andy0323
```

## <a name="log-profiles"></a>Log profiles
Use the information in this section to work with log profiles.

### <a name="get-a-log-profile"></a>Get a log profile
```
azure insights logprofile list
azure insights logprofile get -n default
```


### <a name="add-a-log-profile-without-retention"></a>Add a log profile without retention
```
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Remove a log profile
```
azure insights logprofile delete --name default
```

### <a name="add-a-log-profile-with-retention"></a>Add a log profile with retention
```
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Add a log profile with retention and EventHub
```
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## <a name="diagnostics"></a>Diagnostics
Use the information in this section to work with diagnostic settings.

### <a name="get-a-diagnostic-setting"></a>Get a diagnostic setting
```
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### <a name="disable-a-diagnostic-setting"></a>Disable a diagnostic setting
```
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### <a name="enable-a-diagnostic-setting-without-retention"></a>Enable a diagnostic setting without retention
```
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## <a name="autoscale"></a>Autoscale
Use the information in this section to work with autoscale settings. You need to modify these examples.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Get autoscale settings for a resource group
```
azure insights autoscale setting list montest2
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Get autoscale settings by name in a resource group
```
azure insights autoscale setting list montest2 -n setting2
```


### <a name="set-auotoscale-settings"></a>Set auotoscale settings
```
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```



<!--HONumber=Oct16_HO2-->


