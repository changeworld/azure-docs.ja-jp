---
title: "Azure Monitor のほぼリアルタイムのメトリック アラート | Microsoft Docs"
description: "ほぼリアルタイムのメトリック アラートを使用して、1 分という細かい頻度で Azure リソース メトリックを監視する方法を説明します。"
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
ms.openlocfilehash: 6370f4596e6b20962c6de0dbcbd5f86c3b7777cc
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2018
---
# <a name="near-real-time-metric-alerts-preview"></a>ほぼリアルタイムのメトリック アラート (プレビュー)
Azure Monitor では、ほぼリアルタイムのメトリック アラート (プレビュー) と呼ばれる新しいアラートの種類をサポートしています。 現在、この機能はパブリック プレビュー段階にあります。

ほぼリアルタイムのメトリック アラートは、いくつかの点で通常のメトリック アラートとは異なります。

- **待ち時間の短縮**: ほぼリアルタイムのメトリック アラートでは、メトリック値の変化を 1 分という細かい間隔で監視できます。
- **メトリックの条件に対する詳細な制御**: ほぼリアルタイムのメトリック アラートでは、より詳細なアラート ルールを定義できます。 このアラートでは、メトリックの最大値、最小値、平均値、および合計値の監視がサポートされています。
- **複数のメトリックの監視の組み合わせ**: ほぼリアルタイムのメトリック アラートでは、複数のメトリック (現時点では最大で 2 つ) を 1 つのルールで監視できます。 両方のメトリックが、指定された期間にわたってしきい値を超えた場合、アラートがトリガーされます。
- **モジュール式の通知システム**: ほぼリアルタイムのメトリック アラートでは、[アクション グループ](monitoring-action-groups.md)を使用します。 アクション グループを使用すると、モジュール形式のアクションを作成することができます。 アクション グループは、複数のアラート ルールで再利用できます。

> [!NOTE]
> ほぼリアルタイムのメトリック アラート機能は、現在、パブリック プレビュー段階です。 機能やユーザー エクスペリエンスは変更されることがあります。
>

## <a name="resources-you-can-use-with-near-real-time-metric-alerts"></a>ほぼリアルタイムのメトリック アラートで使用できるリソース
ほぼリアルタイムのメトリック アラートでサポートされているリソースの種類の完全な一覧を、次に示します。

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

## <a name="near-real-time-metric-alerts-for-metrics-that-use-dimensions"></a>ディメンションを使用するメトリックのほぼリアルタイムのメトリック アラート
ほぼリアルタイムのメトリック アラートでは、ディメンションを使用するメトリックのアラートがサポートされています。 ディメンションを使用すると、メトリックを適切なレベルにフィルター処理できます。 ディメンションを使用するメトリックのほぼリアルタイムのメトリック アラートは、次のリソースの種類でサポートされています。

* Microsoft.ApiManagement/service
* Microsoft.Storage/storageAccounts (米国リージョンのストレージ アカウントでのみサポート)
* Microsoft.Storage/storageAccounts/services (米国リージョンのストレージ アカウントでのみサポート)

## <a name="create-a-near-real-time-metric-alert"></a>ほぼリアルタイムのメトリック アラートを作成する
現時点で、ほぼリアルタイムのメトリック アラートは、Azure Portal でのみ作成できます。 PowerShell、Azure コマンド ライン インターフェイス (Azure CLI)、および Azure Monitor REST API を使用したほぼリアルタイムのメトリック アラートの構成は、近日対応予定です。

ほぼリアルタイムのメトリック アラートの作成エクスペリエンスは、新しい **[アラート (プレビュー)]** ページに移動されました。 現在のアラート ページに **[Add Near Real-Time Metric alert]\(ほぼリアルタイムのメトリック アラートの追加\)** が表示されていても、**[アラート (プレビュー)]** ページにリダイレクトされます。

ほぼリアルタイムのメトリック アラートの作成方法については、「[Azure Portal でアラート ルールを作成する](monitor-alerts-unified-usage.md#create-an-alert-rule-with-the-azure-portal)」を参照してください。

## <a name="manage-near-real-time-metric-alerts"></a>ほぼリアルタイムのメトリック アラートを管理する
ほぼリアルタイムのメトリック アラートの作成後、[Azure Portal でのアラートの管理](monitor-alerts-unified-usage.md#managing-your-alerts-in-azure-portal)に関するページで説明されている手順を使用して、アラートを管理することができます。

## <a name="payload-schema"></a>ペイロード スキーマ

POST 操作には、すべてのほぼリアルタイムのメトリック アラートに対する以下の JSON ペイロードとスキーマが含まれます。

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

* 新しい[アラート (プレビュー) エクスペリエンス](monitoring-overview-unified-alerts.md)の詳細を確認します。
* [Azure Alerts (プレビュー) のログ アラート](monitor-alerts-unified-log.md)について確認します。
* [Azure のアラート](monitoring-overview-alerts.md)について確認します。