---
title: Resource Manager テンプレートでログ アラートを作成する
description: Azure Resource Manager テンプレートと API を使用してログ アラートを作成する方法を説明します。
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: 588a0686eda1966582b82a4673a8b6805453c94c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441444"
---
# <a name="create-a-log-alert-with-a-resource-manager-template"></a>Resource Manager テンプレートでログ アラートを作成する
この記事では、Azure で [Azure Powershell](../azure-resource-manager/resource-group-template-deploy.md) と [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) を介して [Azure Resource Manager テンプレート](..//azure-resource-manager/resource-group-authoring-templates.md)を使用して、[ログ アラート](monitor-alerts-unified-log.md)をプログラムから大規模に管理する方法を示します。 現在、Azure アラートは、[Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) および [Azure Application Insights](../application-insights/app-insights-analytics-tour.md) からのクエリに関するログ アラートをサポートしています。

## <a name="managing-log-alert-on-log-analytics"></a>Log Analytics でログ アラートを管理する
[Azure Log Analytics](../log-analytics/log-analytics-tutorial-viewdata.md) のログ アラートは、[Azure の新しいアラート エクスペリエンス](monitoring-overview-unified-alerts.md)に統合されています。その一方で、引き続き Log Analytics API の外で実行され、[OMS ポータルでのアラート](..//log-analytics/log-analytics-alerts-creating.md)を管理するために以前使用されていたスキーマとの互換性が維持されています。

> [!NOTE]
> 2018 年 5 月 14 日より、ワークスペース内のすべてのアラートは Azure に自動的に拡張されるようになります。 ユーザーは 2018 年 5 月 14 日より前に、アラートの Azure への拡張を自主的に開始できます。 詳細については、[OMS から Azure へのアラートの拡張](monitoring-alerts-extend.md)に関するページを参照してください。 

### <a name="using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用
Log Analytics のアラートは、保存された検索条件を定期的に実行するアラート ルールによって作成されます。 クエリの結果が指定されている条件と一致する場合、アラート レコードが作成されて、1 つまたは複数のアクションが実行されます。 

Log Analytics の保存された検索と Log Analytics のアラートのリソース テンプレートは、ドキュメントの Log Analytics のセクションで入手できます。 詳細については、[Log Analytics の保存された検索とアラート](../operations-management-suite/operations-management-suite-solutions-resources-searches-alerts.md)に関するページを参照してください。図を含むサンプルとスキーマの詳細が記載されています。

### <a name="using-resource-template-via-apipowershell"></a>API または Powershell を介したリソース テンプレートの使用
Log Analytics のアラート REST API は RESTful であり、Azure Resource Manager REST API を使用してアクセスできます。 そのためこの API は、PowerShell コマンド ラインからアクセスすることができ、検索結果は JSON 形式で出力されるため、プログラムからさまざまな方法で結果を利用できます。

Powershell から API にアクセスする例を含めて、詳細については、[Log Analytics での REST API によるアラート ルールの作成と管理](../log-analytics/log-analytics-api-alerts.md)についてのページを参照してください。

## <a name="managing-log-alert-on-application-insights"></a>Application Insights でのログ アラートの管理
Azure Application Insights のログ アラートは、Azure Monitor 管理下の新しい Azure アラートの一部として導入されました。 そのためこれは、Azure Monitor API のもとで[スケジュール済みクエリ ルール](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/)の REST 操作グループとして実行されます。

### <a name="using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用
Application Insights リソースのログ アラートの種類は、`Microsoft.Insights/scheduledQueryRules/` です。 このリソースの種類の詳細については、[Azure Monitor - Scheduled Query Rules API の API リファレンス](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/)を参照してください。

以下に、[スケジュール済みクエリ ルールの作成](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/createorupdate)のための構造体に基づくリソース テンプレートを示します。変数として、サンプル データ セットを指定しています。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0", 
    "parameters": {      
    },   
    "variables": {
    "alertLocation": "southcentralus",
    "alertName": "samplelogalert",
    "alertTag": "hidden-link:/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
    "alertDesription": "Sample log search alert",
    "alertStatus": "true",
    "alertSource":{
        "Query":"requests",
        "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
        "Type":"ResultCount"
         },
     "alertSchedule":{
         "Frequency": 15,
         "Time": 60
         },
     "alertActions":{
         "SeverityLevel": "4"
         },
      "alertTrigger":{
        "Operator":"GreaterThan",
        "Threshold":"1"
         },
       "actionGrp":{
        "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
        "Subject": "Customized Email Header",
        "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"           
         }
  },
  "resources":[ {
    "name":"[variables('alertName')]",
    "type":"Microsoft.Insights/scheduledQueryRules",
    "apiVersion": "2018-04-16",
    "location": "[variables('alertLocation')]",
    "tags":{"[variables('alertTag')]": "Resource"},
    "properties":{
       "description": "[variables('alertDesription')]",
       "enabled": "[variables('alertStatus')]",
       "source": {
           "query": "[variables('alertSource').Query]",
           "dataSourceId": "[variables('alertSource').SourceId]",
           "queryType":"[variables('alertSource').Type]"
       },
      "schedule":{
           "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
           "timeWindowInMinutes": "[variables('alertSchedule').Time]"    
       },
      "action":{
           "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
           "severity":"[variables('alertActions').SeverityLevel]",
           "aznsAction":{
               "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
               "emailSubject":"[variables('actionGrp').Subject]",
               "customWebhookPayload":"[variables('actionGrp').Webhook]"
           },
       "trigger":{
               "thresholdOperator":"[variables('alertTrigger').Operator]",
               "threshold":"[variables('alertTrigger').Threshold]"
           }
       }
     }
   }
 ]
}
```
> [!IMPORTANT]
> [スケジュール済みクエリ ルール](https://docs.microsoft.com/en-us/rest/api/monitor/scheduledqueryrules/)の API 呼び出しまたはリソース テンプレートを使用する際には、ターゲット リソースへの非表示のリンクが指定された Tag フィールドが必須です。 

上記のサンプル JSON は、このチュートリアルの目的で (たとえば) sampleScheduledQueryRule.json として保存でき、[Azure Portal で Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) を使用してデプロイすることができます。

### <a name="using-resource-template-via-clipowershell"></a>CLI または Powershell を介したリソース テンプレートの使用
Azure Monitor - Scheduled Query Rules API は REST API であり、Azure Resource Manager REST API との完全な互換性があります。 そのためこれは、Resource Manager コマンドレットだけでなく Azure CLI を使用して、Powershell を介して使用できます。

下に、前に示したサンプル リソース テンプレート (sampleScheduledQueryRule.json) に対する Azure Resource Manager PowerShell コマンドレットの使い方を示します。
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile "D:\Azure\Templates\sampleScheduledQueryRule.json"
```
下に、前に示したサンプル リソース テンプレート (sampleScheduledQueryRule.json) に対する Azure Resource Manager コマンドの、Azure CLI での使い方を示します。

```azurecli
az group deployment create --resource-group myRG --template-file sampleScheduledQueryRule.json
```
操作が成功すると、新しいアラート ルールの作成を示す 201 が返されます。または、既存のアラート ルールが変更された場合は 200 が返されます。


## <a name="next-steps"></a>次の手順
* [ログ アラートの Webhook アクション](monitor-alerts-unified-log-webhook.md)を理解します。
* 新しい [Azure アラート](monitoring-overview-unified-alerts.md)について学習します。
* [Application Insights](../application-insights/app-insights-analytics.md) についてさらに学習します。
* [Log Analytics](../log-analytics/log-analytics-overview.md) についてさらに学習します。   
