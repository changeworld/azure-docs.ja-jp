---
title: "Azure Monitor のほぼリアルタイムのメトリック アラート| Microsoft Docs"
description: "ほぼリアルタイムのメトリック アラートによって、1 分間隔で Azure リソースのメトリックを監視できます。"
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: d3e88a98e0ba93a630d131c25ca4dd5cb16f1b1a
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>ほぼリアルタイムのメトリック アラート (プレビュー)
Azure Monitor は、ほぼリアルタイムのメトリック アラート (プレビュー) という名前の新しい種類のメトリック アラートをサポートするようになりました。 現在、この機能はパブリック プレビュー段階にあります。
このアラートは、いくつかの点で、通常のメトリック アラートとは違っています。

- **待ち時間の短縮**: ほぼリアルタイムのメトリック アラートでは、メトリック値の変化を 1 分間隔で監視できます。
- **メトリックの条件に対する詳細な制御**: ほぼリアルタイムのメトリック アラートでは、詳細なアラート ルールを定義できます。 このアラートでは、メトリックの最大値、最小値、平均値、および合計値の監視をサポートします。
- **複数のメトリックの監視の組み合わせ**: ほぼリアルタイムのメトリック アラートでは、複数のメトリック (現時点では 2 つ) を 1 つのルールで監視できます。 両方のメトリックが指定された期間のしきい値を超えた場合、アラートがトリガーされます。
- **モジュール式の通知システム**: ほぼリアルタイムのメトリック アラートでは、[アクション グループ](monitoring-action-groups.md)を使用します。 この機能を使用して、モジュール方式でアクションを作成し、多数のアラート ルールで再利用できます。

> [!NOTE]
> ほぼリアルタイムのメトリック アラート機能は、現在パブリック プレビュー段階です。 機能やユーザー エクスペリエンスは変更されることがあります。
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>ほぼリアルタイムのメトリック アラートを作成できるリソース
ほぼリアルタイムのメトリック アラートがサポートされるリソースの種類の完全な一覧を次に示します。

* Microsoft.ApiManagement/service
* Microsoft.Automation/automationAccounts
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Storage/storageAccounts
* Microsoft.Storage/storageAccounts/services
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.CognitiveServices/accounts

## <a name="near-real-time-metric-alerts-on-metrics-with-dimensions"></a>ディメンションを含むメトリックのほぼリアルタイムのメトリック アラート
ほぼリアルタイムのメトリック アラートでは、ディメンションを含むメトリックのアラートをサポートしています。 ディメンションにより、メトリックを適切なレベルにフィルター処理できます。 ディメンションを含むメトリックのほぼリアルタイムのメトリック アラートは、次のリソースの種類でサポートされています。

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (米国リージョンのストレージ アカウントでのみサポート)
* Microsoft.Storage/storageAccounts/services (米国リージョンのストレージ アカウントでのみサポート)


## <a name="create-a-near-real-time-metric-alert"></a>ほぼリアルタイムのメトリック アラートを作成する
現時点では、ほぼリアルタイムのメトリック アラートは、Azure ポータルでのみ作成できます。 PowerShell、コマンド ライン インターフェイス (CLI)、および Azure Monitor REST API によるほぼリアルタイムのメトリック アラートの作成は、近日対応予定です。

ほぼリアルタイムのメトリック アラートのアラートの作成エクスペリエンスは、新しい**アラート (プレビュー)** エクスペリエンスに移動しました。 現在の [アラート] ページに **[Add Near Real-Time Metric alert]\(ほぼリアルタイムのメトリック アラートの追加\)** が表示されていても、新しいエクスペリエンスにリダイレクトされます。

ほぼリアルタイムのメトリック アラートは、[こちら](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal)で説明する手順を使用して作成できます。

## <a name="managing-near-real-time-metric-alerts"></a>ほぼリアルタイムのメトリック アラートの管理
**ほぼリアルタイムのメトリック アラート**を作成したら、[こちら](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal)で説明する手順を使用して管理できます。

## <a name="payload-schema"></a>ペイロード スキーマ

POST 操作には、すべてのほぼリアルタイムのメトリック アラートについて以下の JSON ペイロードとスキーマが含まれます。

```json
{
    "WebhookName": "Alert1510875839452",
    "RequestBody": {
        "status": "Activated",
        "context": {
            "condition": {
                "metricName": "Percentage CPU",
                "metricUnit": "Percent",
                "metricValue": "17.7654545454545",
                "threshold": "1",
                "windowSize": "10",
                "timeAggregation": "Average",
                "operator": "GreaterThan"
            },
            "resourceName": "ContosoVM1",
            "resourceType": "microsoft.compute/virtualmachines",
            "resourceRegion": "westus",
            "portalLink": "https://portal.azure.com/#resource/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/automationtest/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "timestamp": "2017-11-16T23:54:03.9517451Z",
            "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/microsoft.insights/alertrules/VMMetricAlert1",
            "name": "VMMetricAlert1",
            "description": "A metric alert for the VM Win2012R2",
            "conditionType": "Metric",
            "subscriptionId": "00000000-0000-0000-0000-000000000000",
            "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoVM/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
            "resourceGroupName": "ContosoVM"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
    },
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "s1events.azure-automation.net",
        "User-Agent": "azure-insights/0.9",
        "x-ms-request-id": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="next-steps"></a>次の手順

* [新しいアラート (プレビュー) エクスペリエンスの詳細を確認する](monitoring-overview-unified-alerts.md)
* [Azure アラート (プレビュー) のログ アラートについて確認する](monitor-alerts-unified-log.md)
* [Azure のアラートについて確認する](monitoring-overview-alerts.md)