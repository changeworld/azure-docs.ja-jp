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
ms.openlocfilehash: 88995b1f3350fe485e28efccc93779ae0a42eb97
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>ほぼリアルタイムのメトリック アラート (プレビュー)
Azure Monitor では、ほぼリアルタイムのメトリック アラート (プレビュー) と呼ばれる新しいアラートの種類をサポートしています。 現在、この機能はパブリック プレビュー段階にあります。

ほぼリアルタイムのメトリック アラートは、いくつかの点で通常のメトリック アラートとは異なります。

- **待ち時間の短縮**: ほぼリアルタイムのメトリック アラートでは、メトリック値の変化を 1 分という細かい間隔で監視できます。
- **メトリックの条件に対する詳細な制御**: ほぼリアルタイムのメトリック アラートでは、より詳細なアラート ルールを定義できます。 このアラートでは、メトリックの最大値、最小値、平均値、および合計値の監視がサポートされています。
- **ログからのメトリック**: [Log Analytics](../log-analytics/log-analytics-overview.md) に入ってくる一般的なログ データから、メトリックを Azure Monitor に抽出し、ほぼリアルタイムで警告できます
- **複数のメトリックの監視の組み合わせ**: ほぼリアルタイムのメトリック アラートでは、複数のメトリック (現時点では最大で 2 つ) を 1 つのルールで監視できます。 両方のメトリックが、指定された期間にわたってしきい値を超えた場合、アラートがトリガーされます。
- **モジュール式の通知システム**: ほぼリアルタイムのメトリック アラートでは、[アクション グループ](monitoring-action-groups.md)を使用します。 アクション グループを使用すると、モジュール形式のアクションを作成することができます。 アクション グループは、複数のアラート ルールで再利用できます。

> [!NOTE]
> ほぼリアルタイムのメトリック アラートは、現在、パブリック プレビュー段階です。 ログからのメトリック機能は*限定*パブリック プレビュー段階です。 機能やユーザー エクスペリエンスは変更されることがあります。
>

## <a name="metrics-and-dimensions-supported"></a>サポートされるメトリックとディメンション
ほぼリアルタイムのメトリック アラートでは、ディメンションを使用するメトリックのアラートがサポートされています。 ディメンションを使用すると、メトリックを適切なレベルにフィルター処理できます。 サポートされるすべてのメトリックと適用可能なディメンションは、[Azure Monitor - メトリックス エクスプローラー (プレビュー)](monitoring-metric-charts.md) から探索および視覚化できます。

ほぼリアルタイムのメトリック アラートでサポートされている Azure Monitor ベースのメトリック ソースの完全な一覧を次に示します。

|メトリック名/詳細  |サポートされるディメンション  |
|---------|---------|
|Microsoft.ApiManagement/service     | [はい]        |
|Microsoft.Automation/automationAccounts     |     該当なし    |
|Microsoft.Automation/automationAccounts     |   該当なし      |
|Microsoft.Cache/Redis     |    該当なし     |
|Microsoft.Compute/virtualMachines     |    該当なし     |
|Microsoft.Compute/virtualMachineScaleSets     |   該当なし      |
|Microsoft.DataFactory/factories     |   該当なし      |
|Microsoft.DBforMySQL/servers     |   該当なし      |
|Microsoft.DBforPostgreSQL/servers     |    該当なし     |
|Microsoft.EventHub/namespaces     |   該当なし      |
|Microsoft.Logic/workflows     |     該当なし    |
|Microsoft.Network/applicationGateways     |    該当なし     |
|Microsoft.Network/publicipaddresses     |  該当なし       |
|Microsoft.Search/searchServices     |   該当なし      |
|Microsoft.ServiceBus/namespaces     |  該当なし       |
|Microsoft.Storage/storageAccounts     |    [はい]     |
|Microsoft.Storage/storageAccounts/services     |     [はい]    |
|Microsoft.StreamAnalytics/streamingjobs     |  該当なし       |
|Microsoft.CognitiveServices/accounts     |    該当なし     |


現在、ログからのメトリックでは、次の一般的な OMS ログがサポートされています。
- Windows および Linux マシンの[パフォーマンス カウンター](../log-analytics/log-analytics-data-sources-performance-counters.md)
- マシンのハートビート レコード
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
> 特定のメトリックやディメンションは、選択した期間内にそのデータが存在する場合にのみ表示されます

## <a name="create-a-near-real-time-metric-alert"></a>ほぼリアルタイムのメトリック アラートを作成する
現時点で、ほぼリアルタイムのメトリック アラートは、Azure Portal でのみ作成できます。 PowerShell、Azure コマンド ライン インターフェイス (Azure CLI)、および Azure Monitor REST API を使用したほぼリアルタイムのメトリック アラートの構成は、近日対応予定です。

ほぼリアルタイムのメトリック アラートの作成エクスペリエンスは、新しい **[アラート (プレビュー)]** ページに移動されました。 現在のアラート ページに **[Add Near Real-Time Metric alert]\(ほぼリアルタイムのメトリック アラートの追加\)** が表示されていても、**[アラート (プレビュー)]** ページにリダイレクトされます。

ほぼリアルタイムのメトリック アラートの作成方法については、「[Azure Portal でアラート ルールを作成する](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal)」を参照してください。

## <a name="manage-near-real-time-metric-alerts"></a>ほぼリアルタイムのメトリック アラートを管理する
ほぼリアルタイムのメトリック アラートの作成後、[Azure Portal でのアラートの管理](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal)に関するページで説明されている手順を使用して、アラートを管理することができます。

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

* 新しい[アラート (プレビュー) エクスペリエンス](monitoring-overview-unified-alerts.md)の詳細を確認します。
* [Azure Alerts (プレビュー) のログ アラート](monitor-alerts-unified-log.md)について確認します。
* [Azure のアラート](monitoring-overview-alerts.md)について確認します。
