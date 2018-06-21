---
title: 新しい Azure Monitor メトリック アラートでサポートされるリソース
description: Azure のほぼリアルタイムの新しいメトリック アラートでサポートされているメトリックとログのリファレンスです。
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: d5eaa4dafc9c155d3e6f85bc67c578c8a12da7cf
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35264512"
---
# <a name="supported-metrics-and-creation-methods-for-new-metric-alerts"></a>新しいメトリック アラートでサポートされるメトリックと作成方法
Azure Monitor が[新しいタイプのメトリック アラート](monitoring-overview-unified-alerts.md)をサポートするようになりました。このタイプには、古い[クラシック メトリック アラート](insights-alerts-portal.md)と比較して多くのメリットがあります。 古いアラートは[多数のメトリック](monitoring-supported-metrics.md)をサポートします。 新しいアラートは、この多数の中のサブセットをサポートします (拡大中)。 この記事では、このサブセットを一覧で示します。 

## <a name="portal-powershell-cli-rest-support"></a>ポータル、PowerShell、CLI、REST のサポート
現在、新しいメトリック アラートは Azure Portal、[REST API](https://docs.microsoft.com/en-us/rest/api/monitor/metricalerts/createorupdate)、または[リソース マネージャー テンプレート](monitoring-create-metric-alerts-with-templates.md)でのみ作成できます。 PowerShell および Azure コマンド ライン インターフェイス (Azure CLI 2.0) を使用した新しいアラートの構成は、近日中にサポートされる予定です。

## <a name="metrics-and-dimensions-supported"></a>サポートされるメトリックとディメンション
新しいメトリック アラートでは、ディメンションを使用するメトリックのアラートがサポートされています。 ディメンションを使用すると、メトリックを適切なレベルにフィルター処理できます。 サポートされるすべてのメトリックと適用可能なディメンションは、[Azure Monitor - メトリックス エクスプローラー (プレビュー)](monitoring-metric-charts.md) から探索および視覚化できます。

新しいアラートでサポートされている Azure Monitor のメトリック ソースの完全な一覧を次に示します。

|リソースの種類  |サポートされるディメンション  | 使用可能なメトリック|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | [はい]        | [API Management](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     [はい]   | [Automation アカウント](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | 該当なし| [Batch アカウント](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    該当なし     |[Redis Cache](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    該当なし     | [Virtual Machines](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   該当なし      |[仮想マシン スケール セット](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | [はい]| [コンテナー グループ](monitoring-supported-metrics.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.DataFactory/datafactories| [はい]| [データ ファクトリ V1](monitoring-supported-metrics.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   [はい]     |[データ ファクトリ V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   該当なし      |[DB for MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    該当なし     | [DB for PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  [はい]      |[Event Hubs](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| いいえ  | [資格情報コンテナー](monitoring-supported-metrics.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     該当なし    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    該当なし     | [アプリケーション ゲートウェイ](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/dnsZones | 該当なし| [DNS Zones](monitoring-supported-metrics.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers (Standard SKU に対してのみ)| [はい]| [Load Balancers](monitoring-supported-metrics.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  該当なし       |[パブリック IP アドレス](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | 該当なし | [Capacities](monitoring-supported-metrics.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices     |   該当なし      |[検索サービス](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  [はい]       |[Service Bus](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    [はい]     | [ストレージ アカウント](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     [はい]    | [Blob service](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices)、[ファイル サービス](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices)、[Queue サービス](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices)、および [Table service](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  該当なし       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    該当なし     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces (プレビュー) | [はい]|[Log Analytics ワークスペース](#log-analytics-logs-as-metrics-for-alerting)|


## <a name="log-analytics-logs-as-metrics-for-alerting"></a>アラートのためのメトリックとしての Log Analytics ログ 

新しいメトリック アラートは、ログのプレビューからのメトリックの一部としてメトリックとして抽出された一般的な Log Analytics ログに対しても使用できます。  
- Windows および Linux マシンの[パフォーマンス カウンター](../log-analytics/log-analytics-data-sources-performance-counters.md)
- [Agent Health のためのハートビート レコード](../operations-management-suite/oms-solution-agenthealth.md)
- [更新管理](../operations-management-suite/oms-solution-update-management.md)レコード
 
> [!NOTE]
> 特定のメトリックやディメンションは、選択された期間内にそのためのデータが存在する場合にのみ表示されます。 これらのメトリックは、このプレビューに登録した、米国東部、米国西中部、および西ヨーロッパにワークスペースを持つ顧客が使用できます。 このプレビューに登録する場合は、[アンケート](https://aka.ms/MetricLogPreview)を使用してサインアップします。

サポートされている Log Analytics ログ ベースのメトリック ソースの一覧は次のとおりです。

メトリック名/詳細  |サポートされるディメンション  | ログの種類  |
|---------|---------|---------|
|Average_Avg。 Disk sec/Read     |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Windows パフォーマンス カウンター      |
| Average_Avg。 Disk sec/Write     |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Windows パフォーマンス カウンター      |
| Average_Current Disk Queue Length   |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Windows パフォーマンス カウンター      |
| Average_Disk Reads/sec    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Windows パフォーマンス カウンター      |
| Average_Disk Transfers/sec    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Windows パフォーマンス カウンター      |
|   Average_% Free Space    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Windows パフォーマンス カウンター      |
| Average_Available MBytes     |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Windows パフォーマンス カウンター      |
| Average_% Committed Bytes In Use    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Windows パフォーマンス カウンター      |
| Average_Bytes Received/sec    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Windows パフォーマンス カウンター      |
|  Average_Bytes Sent/sec    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Windows パフォーマンス カウンター      |
|  Average_Bytes Total/sec    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Windows パフォーマンス カウンター      |
|  Average_% Processor Time    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Windows パフォーマンス カウンター      |
|   Average_Processor Queue Length    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Windows パフォーマンス カウンター      |
|   Average_% Free Inodes   |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_% Free Space   |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_% Used Inodes  |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_% Used Space   |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Disk Read Bytes/sec    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Disk Reads/sec |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Disk Transfers/sec |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Disk Write Bytes/sec   |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Disk Writes/sec    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Free Megabytes |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Logical Disk Bytes/sec |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_% Available Memory |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_% Available Swap Space |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_% Used Memory  |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_% Used Swap Space  |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Available MBytes Memory    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Available MBytes Swap  |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Page Reads/sec |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Page Writes/sec    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Pages/sec  |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Used MBytes Swap Space |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Used Memory MBytes |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Total Bytes Transmitted    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Total Bytes Received   |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Total Bytes    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Total Packets Transmitted  |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Total Packets Received |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Total Rx Errors    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Total Tx Errors    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Total Collisions   |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Avg。 Disk sec/Read |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Avg。 Disk sec/Transfer |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Avg。 Disk sec/Write    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Physical Disk Bytes/sec    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Pct Privileged Time    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Pct User Time  |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Used Memory kBytes |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Virtual Shared Memory  |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_% DPC Time |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_% Idle Time    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_% Interrupt Time   |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_% IO Wait Time |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_% Nice Time    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_% Privileged Time  |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_% Processor Time   |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_% User Time    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Free Physical Memory   |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Free Space in Paging Files |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Free Virtual Memory    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Processes  |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Size Stored In Paging Files    |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Uptime |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Average_Users  |     はい - Computer、ObjectName、InstanceName、CounterPath、SourceSystem    |   Linux パフォーマンス カウンター      |
|    Heartbeat  |     はい - Computer、OSType、Version、SourceComputerId    |   ハートビート レコード |
|    アップデート |     はい - Computer、Product、Classification、UpdateState、Optional、Approved    |   更新管理 |



## <a name="payload-schema"></a>ペイロード スキーマ

適切に構成された[アクション グループ](monitoring-action-groups.md)が使用されている場合、POST 操作には、すべての新しいメトリック アラートに対する以下の JSON ペイロードとスキーマが含まれます。

```json
{"schemaId":"AzureMonitorMetricAlert","data":
    {
    "version": "2.0",
    "status": "Activated",
    "context": {
    "timestamp": "2018-02-28T10:44:10.1714014Z",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
    "name": "StorageCheck",
    "description": "",
    "conditionType": "SingleResourceMultipleMetricCriteria",
    "condition": {
      "windowSize": "PT5M",
      "allOf": [
        {
          "metricName": "Transactions",
          "dimensions": [
            {
              "name": "AccountResourceId",
              "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
            },
            {
              "name": "GeoType",
              "value": "Primary"
            }
          ],
          "operator": "GreaterThan",
          "threshold": "0",
          "timeAggregation": "PT5M",
          "metricValue": 1.0
        },
      ]
    },
    "subscriptionId": "00000000-0000-0000-0000-000000000000",
    "resourceGroupName": "Contoso",
    "resourceName": "diag500",
    "resourceType": "Microsoft.Storage/storageAccounts",
    "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
    "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
  },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    }
}
```

## <a name="next-steps"></a>次の手順

* 新しい[アラート エクスペリエンス](monitoring-overview-unified-alerts.md)の詳細について学習します。
* [Azure でのログ アラート](monitor-alerts-unified-log.md)について学習します。
* [Azure のアラート](monitoring-overview-alerts.md)について確認します。
