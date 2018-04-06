---
title: Azure Monitor のほぼリアルタイムのメトリック アラート | Microsoft Docs
description: ほぼリアルタイムのメトリック アラートを使用して、1 分という細かい頻度で Azure リソース メトリックを監視する方法を説明します。
author: snehithm
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: snmuvva, vinagara
ms.custom: ''
ms.openlocfilehash: 15b9b0b69f3805b3e3af1d3973fd3a77bea62ab9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="use-the-newer-metric-alerts-for-azure-services-in-azure-portal"></a>Azure Portal で Azure サービスの新しいメトリック アラートを使用する
Azure Monitor は、ほぼリアルタイムのメトリック アラートと呼ばれる新しいアラートの種類をサポートしています。 

ほぼリアルタイムのメトリック アラートは、いくつかの点で[従来のメトリック アラート](insights-alerts-portal.md)とは異なります。

- **待機時間の短縮**: ほぼリアルタイムのメトリック アラートは 1 分ごとに実行できます。 古いメトリック アラートは常に、5 分の頻度で実行されます。
- **多次元メトリックのサポート**: 各次元のメトリックに関するアラートを生成して、メトリックの関心のあるセグメントを監視できます。
- **メトリックの条件に対する詳細な制御**: ほぼリアルタイムのメトリック アラートでは、より詳細なアラート ルールを定義できます。 このアラートでは、メトリックの最大値、最小値、平均値、および合計値の監視がサポートされています。
- **複数のメトリックの監視の組み合わせ**: ほぼリアルタイムのメトリック アラートでは、複数のメトリック (現時点では最大で 2 つ) を 1 つのルールで監視できます。 両方のメトリックが、指定された期間にわたってしきい値を超えた場合、アラートがトリガーされます。
- **モジュール式の通知システム**: ほぼリアルタイムのメトリック アラートでは、[アクション グループ](monitoring-action-groups.md)を使用します。 アクション グループを使用すると、モジュール形式のアクションを作成することができます。 アクション グループは、複数のアラート ルールで再利用できます。
- **ログからのメトリック**: [Log Analytics](../log-analytics/log-analytics-overview.md) に入力される一般的なログ データから、メトリックを Azure Monitor に抽出し、ほぼリアルタイムで警告できます。


## <a name="metrics-and-dimensions-supported"></a>サポートされるメトリックとディメンション
ほぼリアルタイムのメトリック アラートでは、ディメンションを使用するメトリックのアラートがサポートされています。 ディメンションを使用すると、メトリックを適切なレベルにフィルター処理できます。 サポートされるすべてのメトリックと適用可能なディメンションは、[Azure Monitor - メトリックス エクスプローラー (プレビュー)](monitoring-metric-charts.md) から探索および視覚化できます。

ほぼリアルタイムのメトリック アラートでサポートされている Azure Monitor ベースのメトリック ソースの完全な一覧を次に示します。

|リソースの種類  |サポートされるディメンション  | 使用可能なメトリック|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | [はい]        | [API Management](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     [はい]   | [Automation アカウント](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | 該当なし| [Batch アカウント](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    該当なし     |[Redis Cache](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.Compute/virtualMachines     |    該当なし     | [Virtual Machines](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   該当なし      |[仮想マシン スケール セット](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.DataFactory/factories     |   [はい]     |[データ ファクトリ V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   該当なし      |[DB for MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    該当なし     | [DB for PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  [はい]      |[Event Hubs](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.Logic/workflows     |     該当なし    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    該当なし     | [アプリケーション ゲートウェイ](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/publicipaddresses     |  該当なし       |[パブリック IP アドレス](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Search/searchServices     |   該当なし      |[検索サービス](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  [はい]       |[Service Bus](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    [はい]     | [ストレージ アカウント](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     [はい]    | [Blob service](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices)、[ファイル サービス](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices)、[Queue サービス](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices)、および [Table service](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  該当なし       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.CognitiveServices/accounts     |    該当なし     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.OperationalInsights/workspaces (プレビュー) | [はい]|[Log Analytics ワークスペース](#support-for-oms-logs-as-metrics-for-alerting)|


## <a name="create-a-newer-metric-alert"></a>新しいメトリック アラートを作成する
現在、新しいメトリック アラートは Azure Portal または REST API でのみ作成できます。 PowerShell および Azure コマンド ライン インターフェイス (Azure CLI) を使用したほぼリアルタイムのメトリック アラートの構成のサポートは近日対応予定です。

Azure Portal で新しいメトリック アラートを作成する方法を学習するには、「[ Azure Portal でアラート ルールを作成する](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal)」を参照してください。

## <a name="manage-newer-metric-alerts"></a>新しいメトリック アラートを管理する
ほぼリアルタイムのメトリック アラートの作成後、[Azure Portal でのアラートの管理](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal)に関するページで説明されている手順を使用して、アラートを管理することができます。

## <a name="support-for-oms-logs-as-metrics-for-alerting"></a>アラートのためのメトリックとしての OMS ログのサポート

ほぼリアルタイムのメトリック アラートは、ログのプレビューからのメトリックの一部としてメトリックとして抽出された一般的な OMS ログに対しても使用できます。  
- Windows および Linux マシンの[パフォーマンス カウンター](../log-analytics/log-analytics-data-sources-performance-counters.md)
- [Agent Health のためのハートビート レコード](../operations-management-suite/oms-solution-agenthealth.md)
- [更新管理](../operations-management-suite/oms-solution-update-management.md)レコード

ほぼリアルタイムのメトリック アラートでサポートされている OMS ログ ベースのメトリック ソースの完全な一覧を次に示します。

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
|    プライマリの |     はい - Computer、Product、Classification、UpdateState、Optional、Approved    |   更新管理 |

> [!NOTE]
> 特定のメトリックやディメンションは、選択された期間内にそのためのデータが存在する場合にのみ表示されます。 これらのメトリックは、このプレビューに登録した、米国東部、米国西中部、および西ヨーロッパにワークスペースを持つ顧客が使用できます。 このプレビューに登録する場合は、[アンケート](https://aka.ms/MetricLogPreview)を使用してサインアップします。


## <a name="payload-schema"></a>ペイロード スキーマ

適切に構成された[アクション グループ](monitoring-action-groups.md)が使用されている場合、POST 操作には、すべてのほぼリアルタイムのメトリック アラートに対する以下の JSON ペイロードとスキーマが含まれます。

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
