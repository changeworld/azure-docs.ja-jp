---
title: Azure Monitor API の提供終了
description: 以前のバージョンの OperationalInsights リソース プロバイダー API の提供終了について説明します。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: c9a7ba63246f747a132c315e22452ca9c7144448
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92058184"
---
# <a name="operationalinsights-api-version-retirement"></a>OperationalInsights API バージョンの提供終了
Microsoft は、新しい/サポートされるバージョンに速やかに移行する目的で、API の提供終了を少なくともその 12 か月前に通知します。 **OperationalInsights** リソース プロバイダー API の新しいバージョン (2020-08-01) がリリースされたため、2023 年 10 月 31 日に以前の API バージョンをすべて提供終了します。 新しい機能と最適化は最新の API にのみ追加されるため、できるだけ早く最新の API バージョンにアップグレードしてください。

2023 年 10 月 31 日以降、Azure Monitor は 2020-08-01 より前の API バージョンをサポートしなくなります。 アップグレードしない場合でも、以前のバージョンから送信される要求は、2023 年 10 月 31 日まで引き続き Azure Monitor サービスによって処理されます。

## <a name="migration-steps"></a>移行の手順
使用する構成方法に応じて、**REST** 要求と **Resource Manager テンプレート**で新しいバージョンを更新する必要があります。 API バージョンを更新するには、下の例に従います。

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


## <a name="next-steps"></a>次のステップ

- [OperationalInsights ワークスペース API についてのリファレンス](https://docs.microsoft.com/rest/api/loganalytics/workspaces)をご覧ください。
