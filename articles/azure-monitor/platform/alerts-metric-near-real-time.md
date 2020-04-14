---
title: Azure Monitor のメトリック アラートでサポートされるリソース
description: Azure Monitor のメトリック アラートでサポートされるメトリックとログのリファレンス
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 3/5/2020
ms.subservice: alerts
ms.openlocfilehash: c036fa3708d718d6199d27989e60b11015a1227e
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585849"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Azure Monitor のメトリック アラートでサポートされるリソース

Azure Monitor が[新しいタイプのメトリック アラート](../../azure-monitor/platform/alerts-overview.md)をサポートするようになりました。このタイプには、古い[クラシック メトリック アラート](../../azure-monitor/platform/alerts-classic.overview.md)と比較して多くのメリットがあります。 メトリックは[さまざまな Azure サービスで](../../azure-monitor/platform/metrics-supported.md)利用できます。 新しいアラートが対応しているリソース タイプは限られていますが、その数は増えています。 この記事では、このサブセットを一覧で示します。

新しいメトリック アラートは、メトリックとして抽出された、Log Analytics ワークスペースに格納された一般的なログ データに対しても使用できます。 詳細については、[ログのメトリック アラート](../../azure-monitor/platform/alerts-metric-logs.md)に関するページを参照してください。

## <a name="portal-powershell-cli-rest-support"></a>ポータル、PowerShell、CLI、REST のサポート
現在、新しいメトリック アラートは Azure portal、[REST API](https://docs.microsoft.com/rest/api/monitor/metricalerts/)、または [Resource Manager テンプレート](../../azure-monitor/platform/alerts-metric-create-templates.md)でのみ作成できます。 PowerShell と Azure CLI バージョン 2.0 以降を使用した新しいアラートの構成は、近日中にサポートされる予定です。

## <a name="metrics-and-dimensions-supported"></a>サポートされるメトリックとディメンション
新しいメトリック アラートでは、ディメンションを使用するメトリックのアラートがサポートされています。 ディメンションを使用すると、メトリックを適切なレベルにフィルター処理できます。 サポートされるすべてのメトリックと適用可能なディメンションは、[Azure Monitor - メトリックス エクスプローラー](../../azure-monitor/platform/metrics-charts.md)から探索および視覚化できます。

新しいアラートでサポートされている Azure Monitor のメトリック ソースの完全な一覧を次に示します。

|リソースの種類  |サポートされるディメンション |マルチリソース アラート| 使用可能なメトリック|
|---------|---------|-----|----------|
|Microsoft.ApiManagement/service | はい| いいえ | [API Management](../../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)|
|Microsoft.AppPlatform/Spring |いいえ| はい|
|Microsoft.Automation/automationAccounts | はい| いいえ | [Automation アカウント](../../azure-monitor/platform/metrics-supported.md#microsoftautomationautomationaccounts)|
|Microsoft.Batch/batchAccounts | 該当なし| いいえ | [Batch アカウント](../../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)|
|Microsoft.Cache/Redis|はい| いいえ |[Azure Cache for Redis](../../azure-monitor/platform/metrics-supported.md#microsoftcacheredis)|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic|いいえ|はい|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/blobServices|いいえ|はい|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/fileServices|いいえ|はい|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/queueServices|いいえ|はい|
|Microsoft.ClassicStorage/storageAccounts/mmxclassic/tableServices|いいえ|はい| |
|Microsoft.CognitiveServices/accounts| 該当なし | いいえ | [Cognitive Services](../../azure-monitor/platform/metrics-supported.md#microsoftcognitiveservicesaccounts)|
|Microsoft.Compute/virtualMachines |はい | はい | [Virtual Machines](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)|
|Microsoft.Compute/virtualMachineScaleSets |該当なし | はい |[仮想マシン スケール セット](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)|
|Microsoft.ContainerInstance/containerGroups | はい| いいえ | [コンテナー グループ](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups)|
|Microsoft.ContainerService/managedClusters | はい | いいえ | [マネージド クラスター](../../azure-monitor/platform/metrics-supported.md#microsoftcontainerservicemanagedclusters)|
|Microsoft.DataBoxEdge/dataBoxEdgeDevices | はい | はい | |
|Microsoft.DataFactory/datafactories| はい| いいえ | [データ ファクトリ V1](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactorydatafactories)|
|Microsoft.DataFactory/factories |はい | いいえ |[データ ファクトリ V2](../../azure-monitor/platform/metrics-supported.md#microsoftdatafactoryfactories)|
|Microsoft.DataShare/accounts |いいえ| はい|
|Microsoft.DBforMySQL/servers |該当なし| いいえ |[DB for MySQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers |該当なし | いいえ | [DB for PostgreSQL](../../azure-monitor/platform/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Microsoft.Devices/IotHubs | 該当なし | いいえ |[IoT Hub メトリック](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs)|
|Microsoft.Devices/provisioningServices| はい | いいえ |[DPS メトリック](../../azure-monitor/platform/metrics-supported.md#microsoftdevicesprovisioningservices)|
|Microsoft.EventGrid/domains|いいえ|はい| |
|Microsoft.EventGrid/topics |はい | いいえ |[Event Grid トピック](../../azure-monitor/platform/metrics-supported.md#microsofteventgridtopics)|
|Microsoft.EventHub/clusters |はい| いいえ |[Event Hubs クラスター](../../azure-monitor/platform/metrics-supported.md#microsofteventhubclusters)|
|Microsoft.EventHub/namespaces |はい| いいえ |[Event Hubs](../../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)|
|Microsoft.KeyVault/vaults| いいえ |いいえ |[資格情報コンテナー](../../azure-monitor/platform/metrics-supported.md#microsoftkeyvaultvaults)|
|Microsoft.Logic/workflows |該当なし | いいえ |[Logic Apps](../../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) |
|Microsoft.MachineLearningServices/workspaces|はい| いいえ | [Machine Learning](../../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Microsoft.NetApp/netAppAccounts/capacityPools |はい| いいえ | [Azure NetApp 容量プール](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Microsoft.NetApp/netAppAccounts/capacityPools/volumes |はい| いいえ | [Azure NetApp ボリューム](../../azure-monitor/platform/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Microsoft.Network/applicationGateways|該当なし| いいえ |  |
|Microsoft.Network/dnsZones | 該当なし| いいえ | [DNS Zones](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | 該当なし | いいえ |[Express Route 回線](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Microsoft.Network/loadBalancers (Standard SKU に対してのみ)| はい| いいえ | [Load Balancers](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkloadbalancers) |
|Microsoft.Network/natGateways|いいえ|はい|
|Microsoft.Network/privateEndpoints|いいえ|はい|
|Microsoft.Network/privateLinkServices|いいえ|はい|
|Microsoft.Network/publicipaddresses |該当なし | いいえ |[パブリック IP アドレス](../../azure-monitor/platform/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | はい | いいえ | [Traffic Manager プロファイル](../../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| はい | いいえ | [Log Analytics ワークスペース](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Microsoft.Relay/namespaces | はい | いいえ | [リレー](../../azure-monitor/platform/metrics-supported.md#microsoftrelaynamespaces)|
|Microsoft.Peering/peeringServices|いいえ|はい|
|Microsoft.PowerBIDedicated/capacities | 該当なし | いいえ | [Capacities](../../azure-monitor/platform/metrics-supported.md#microsoftpowerbidedicatedcapacities)|
|Microsoft.Search/searchServices |該当なし|いいえ | [検索サービス](../../azure-monitor/platform/metrics-supported.md#microsoftsearchsearchservices)|
|Microsoft.ServiceBus/namespaces |はい| いいえ |[Service Bus](../../azure-monitor/platform/metrics-supported.md#microsoftservicebusnamespaces)|
|Microsoft.Sql/servers/elasticPools |    いいえ | はい |
|Microsoft.Sql/servers/databases    | いいえ | はい |
|Microsoft.Storage/storageAccounts |はい | いいえ | [ストレージ アカウント](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccounts)|
|Microsoft.Storage/storageAccounts/services | はい| いいえ | [Blob service](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsblobservices)、[ファイル サービス](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsfileservices)、[Queue サービス](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountsqueueservices)、および [Table service](../../azure-monitor/platform/metrics-supported.md#microsoftstoragestorageaccountstableservices)|
|Microsoft.StreamAnalytics/streamingjobs |該当なし| いいえ | [Stream Analytics](../../azure-monitor/platform/metrics-supported.md#microsoftstreamanalyticsstreamingjobs)|
|Microsoft.Microsoft.VMWareCloudSimple/virtualMachines |はい|いいえ |[CloudSimple 仮想マシン](../../azure-monitor/platform/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines)|
|Microsoft.Web/hostingEnvironments/multiRolePools | はい | いいえ | [App Service Environment マルチロール プール](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Microsoft.Web/hostingEnvironments/workerPools | はい | いいえ | [App Service Environment ワーカー プール](../../azure-monitor/platform/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Microsoft.Web/serverfarms | はい | いいえ | [App Service プラン](../../azure-monitor/platform/metrics-supported.md#microsoftwebserverfarms)|
|Microsoft.Web/sites | はい | いいえ | [App Services](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-excluding-functions) と [Functions](../../azure-monitor/platform/metrics-supported.md#microsoftwebsites-functions)|
|Microsoft.Web/sites/slots | はい | いいえ | [App Service スロット](../../azure-monitor/platform/metrics-supported.md#microsoftwebsitesslots)|

## <a name="payload-schema"></a>ペイロード スキーマ

> [!NOTE]
> [共通アラート スキーマ](https://aka.ms/commonAlertSchemaDocs)を使用することもできます。このスキーマの利点は、Azure Monitor のすべてのアラート サービスの垣根を越えて、拡張可能かつ一元化された単一のアラート ペイロードによって Webhook の統合を実現できることです。 [共通アラート スキーマの定義については、こちらを参照してください。](https://aka.ms/commonAlertSchemaDefinitions)


適切に構成された[アクション グループ](../../azure-monitor/platform/action-groups.md)が使用されている場合、POST 操作には、すべての新しいメトリック アラートに対する以下の JSON ペイロードとスキーマが含まれます。

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
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
            "metricValue": 1
          }
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

## <a name="next-steps"></a>次のステップ

* 新しい[アラート エクスペリエンス](../../azure-monitor/platform/alerts-overview.md)の詳細について学習します。
* [Azure でのログ アラート](../../azure-monitor/platform/alerts-unified-log.md)について学習します。
* [Azure のアラート](../../azure-monitor/platform/alerts-overview.md)について確認します。
