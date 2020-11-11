---
title: Azure Monitor API の提供終了
description: 以前のバージョンの OperationalInsights リソース プロバイダー API の提供終了について説明します。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/29/2020
ms.openlocfilehash: 7ddfb3221c73c740a339dd8d9d4f60cca46c6425
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026770"
---
# <a name="operationalinsights-api-version-retirement"></a>OperationalInsights API バージョンの提供終了
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、API の提供終了を少なくともその 12 か月前に通知します。 **OperationalInsights** リソース プロバイダー API の新しいバージョン (2020-08-01) がリリースされたため、2024 年 2 月 29 日に以前の API バージョンは廃止されます。

バージョン 2020-08-01 の使用をすぐに開始することをお勧めします。これにより、[専用クラスター](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters)、[カスタマー マネージド キー](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys)、[プライベート リンク](https://docs.microsoft.com/azure/azure-monitor/platform/private-link-security)、[データ エクスポート](https://docs.microsoft.com/azure/azure-monitor/platform/logs-data-export)などの新機能のメリットを得ることができます。 また、新しい機能や機能性、および最適化は、現在の API にのみ追加されます。

2024 年 2 月 29 日以降、Azure Monitor では 2020-08-01 より前の API バージョンがサポートされなくなります。 アップグレードしない場合でも、以前のバージョンから送信される要求は、2024 年 2 月 29 日まで引き続き Azure Monitor サービスによって処理されます。

## <a name="migration-steps"></a>移行の手順
使用する構成方法に応じて、 **REST** 要求と **Resource Manager テンプレート** で新しいバージョンを更新する必要があります。 API バージョンを更新するには、下の例に従います。

1. REST API 要求では、要求の URL で API バージョンを使用します。 次の例に示すように、そのバージョンを最新バージョン (2020-08-01) に置き換えます。

    ```rest
    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}?api-version=2020-08-01
    ```

2. Azure Resource Manager テンプレートでは、リソースの **apiVersion** プロパティで API バージョンを使用します。 次の例に示すように、そのバージョンを最新バージョン (2020-08-01) に置き換えます。


    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-08-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string",
                "metadata": {
                "description": "Name of the workspace."
                }
            },
            "resources": [
            {
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('workspaceName')]",
                "apiVersion": "2020-08-01",
                "location": "westus",
                "properties": {
                    "sku": {
                        "name": "pergb2018"
                    },
                    "retentionInDays": 30,
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0,
                        "enableLogAccessUsingOnlyResourcePermissions": true
                    }
                }
            }
        ]
    }
    }
    ```


## <a name="next-steps"></a>次の手順

- [OperationalInsights ワークスペース API についてのリファレンス](/rest/api/loganalytics/workspaces)をご覧ください。
