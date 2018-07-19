---
title: 新しい Azure Monitor メトリック アラートでサポートされるリソース
description: Azure のほぼリアルタイムの新しいメトリック アラートでサポートされているメトリックとログのリファレンスです。
author: snehithm
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/29/2018
ms.author: snmuvva
ms.component: alerts
ms.openlocfilehash: 4d51b099532d3052acc190231ec4be17765a427e
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971025"
---
# <a name="introduction"></a>はじめに
Azure Monitor が[新しいタイプのメトリック アラート](monitoring-overview-unified-alerts.md)をサポートするようになりました。このタイプには、古い[クラシック メトリック アラート](insights-alerts-portal.md)と比較して多くのメリットがあります。 メトリックは[さまざまな Azure サービスで](monitoring-supported-metrics.md)利用できます。 新しいアラートが対応しているリソース タイプは限られていますが、その数は増えています。 この記事では、このサブセットを一覧で示します。 

新しいメトリック アラートは、ログ (プレビュー) からのメトリックの一部としてメトリックとして抽出された一般的な Log Analytics ログに対しても使用できます。  
- Windows および Linux マシンの[パフォーマンス カウンター](../log-analytics/log-analytics-data-sources-performance-counters.md)
- [Agent Health のためのハートビート レコード](../operations-management-suite/oms-solution-agenthealth.md)
- [更新管理](../operations-management-suite/oms-solution-update-management.md)レコード
- [イベント データ](../log-analytics/log-analytics-data-sources-windows-events.md) ログ
 
> [!NOTE]
> 特定のメトリックやディメンションは、選択された期間内にそのためのデータが存在する場合にのみ表示されます。 これらのメトリックは、米国東部、米国西中部、西ヨーロッパに Azure Log Analytics ワークスペースを持つ顧客が使用できます。 Log Analytics からのメトリックは現在、パブリック プレビューの段階にあり、変更される可能性があります。

## <a name="portal-powershell-cli-rest-support"></a>ポータル、PowerShell、CLI、REST のサポート
現在、新しいメトリック アラートは Azure Portal、[REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/createorupdate)、または[リソース マネージャー テンプレート](monitoring-create-metric-alerts-with-templates.md)でのみ作成できます。 PowerShell と Azure コマンド ライン インターフェイス (Azure CLI 2.0) を使用した新しいアラートの構成は、近日中にサポートされる予定です。

## <a name="metrics-and-dimensions-supported"></a>サポートされるメトリックとディメンション
新しいメトリック アラートでは、ディメンションを使用するメトリックのアラートがサポートされています。 ディメンションを使用すると、メトリックを適切なレベルにフィルター処理できます。 サポートされるすべてのメトリックと適用可能なディメンションは、[Azure Monitor - メトリックス エクスプローラー (プレビュー)](monitoring-metric-charts.md) から探索および視覚化できます。

新しいアラートでサポートされている Azure Monitor のメトリック ソースの完全な一覧を次に示します。

|リソースの種類  |サポートされるディメンション  | 使用可能なメトリック|
|---------|---------|----------------|
|Microsoft.ApiManagement/service     | [はい]        | [API Management](monitoring-supported-metrics.md#microsoftapimanagementservice)|
|Microsoft.Automation/automationAccounts     |     [はい]   | [Automation アカウント](monitoring-supported-metrics.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | 該当なし| [Batch アカウント](monitoring-supported-metrics.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis     |    該当なし     |[Redis Cache](monitoring-supported-metrics.md#microsoftcacheredis)|
|Microsoft.CognitiveServices/accounts     |    該当なし     | [Cognitive Services](monitoring-supported-metrics.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines     |    該当なし     | [Virtual Machines](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets     |   該当なし      |[仮想マシン スケール セット](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | [はい]| [コンテナー グループ](monitoring-supported-metrics.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | [はい] | [マネージド クラスター](monitoring-supported-metrics.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataFactory/datafactories| [はい]| [データ ファクトリ V1](monitoring-supported-metrics.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories     |   [はい]     |[データ ファクトリ V2](monitoring-supported-metrics.md#microsoftdatafactoryfactories)|
|Microsoft.DBforMySQL/servers     |   該当なし      |[DB for MySQL](monitoring-supported-metrics.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers     |    該当なし     | [DB for PostgreSQL](monitoring-supported-metrics.md#microsoftdbforpostgresqlservers)|
|Microsoft.EventHub/namespaces     |  [はい]      |[Event Hubs](monitoring-supported-metrics.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| いいえ  | [資格情報コンテナー](monitoring-supported-metrics.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows     |     該当なし    |[Logic Apps](monitoring-supported-metrics.md#microsoftlogicworkflows) |
|Microsoft.Network/applicationGateways     |    該当なし     | [アプリケーション ゲートウェイ](monitoring-supported-metrics.md#microsoftnetworkapplicationgateways) |
|Microsoft.Network/expressRouteCircuits | 該当なし |  [Express Route 回線](monitoring-supported-metrics.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/dnsZones | 該当なし| [DNS Zones](monitoring-supported-metrics.md#microsoftnetworkdnszones) |
|Microsoft.Network/loadBalancers (Standard SKU に対してのみ)| [はい]| [Load Balancers](monitoring-supported-metrics.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/publicipaddresses     |  該当なし       |[パブリック IP アドレス](monitoring-supported-metrics.md#microsoftnetworkpublicipaddresses)|
|Microsoft.PowerBIDedicated/capacities | 該当なし | [Capacities](monitoring-supported-metrics.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Network/trafficManagerProfiles | [はい] | [Traffic Manager プロファイル](monitoring-supported-metrics.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.Search/searchServices     |   該当なし      |[検索サービス](monitoring-supported-metrics.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces     |  [はい]       |[Service Bus](monitoring-supported-metrics.md#microsoftservicebusnamespaces)|
|Microsoft.Storage/storageAccounts     |    [はい]     | [ストレージ アカウント](monitoring-supported-metrics.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services     |     [はい]    | [Blob service](monitoring-supported-metrics.md#microsoftstoragestorageaccountsblobservices)、[ファイル サービス](monitoring-supported-metrics.md#microsoftstoragestorageaccountsfileservices)、[Queue サービス](monitoring-supported-metrics.md#microsoftstoragestorageaccountsqueueservices)、および [Table service](monitoring-supported-metrics.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs     |  該当なし       | [Stream Analytics](monitoring-supported-metrics.md#microsoftstreamanalyticsstreamingjobs)|
| Microsoft.Web/serverfarms | [はい] | [App Service プラン](monitoring-supported-metrics.md#microsoftwebserverfarms)  |
|Microsoft.OperationalInsights/workspaces (プレビュー) | [はい]|[Log Analytics ワークスペース](monitoring-supported-metrics.md#microsoftoperationalinsightsworkspaces)|



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
