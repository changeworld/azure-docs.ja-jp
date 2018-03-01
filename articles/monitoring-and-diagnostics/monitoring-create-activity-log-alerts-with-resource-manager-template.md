---
title: "Resource Manager テンプレートでのアクティビティ ログ アラートの作成 | Microsoft Docs"
description: "Azure リソースの作成時に通知を受け取ります。"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: ancav
ms.openlocfilehash: b30912c44bd66f8c6fca548dc905f750e05c8621
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>Resource Manager テンプレートでのアクティビティ ログ アラートの作成
この記事では、[Azure Resource Manager テンプレート](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)を使用してアクティビティ ログ アラートを構成する方法について説明します。 テンプレートを使用すると、自動デプロイ プロセスの一環として、特定のアクティビティ ログ イベント条件に基づいてアクティブ化する多数のアラートを簡単に設定できます。

基本的な手順は次のとおりです。

1. アクティビティ ログ アラートの作成方法が記述された JSON ファイルとしてテンプレートを作成します。

2. [任意のデプロイ方法](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)を使用してテンプレートをデプロイします。

## <a name="resource-manager-template-for-an-activity-log-alert"></a>アクティビティ ログ アラート用の Resource Manager テンプレート
Resource Manager テンプレートを使用してアクティビティ ログ アラートを作成するには、`microsoft.insights/activityLogAlerts` 型のリソースを作成します。 次に、関連するすべてのプロパティを入力します。 アクティビティ ログ アラートを作成するテンプレートを以下に示します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

アクティビティ ログ アラート テンプレートの例については、[Azure クイックスタート ギャラリー](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Insights)を参照してください。

> [!NOTE]

> また、[監視] > [[アラート (プレビュー)]](monitoring-overview-unified-alerts.md) の強化されたユーザー エクスペリエンスを使用して、アクティビティ ログ アラート ルールを作成することもできます。 作成方法の詳細については、[こちらの記事](monitoring-activity-log-alerts-new-experience.md)を参照してください。

## <a name="next-steps"></a>次の手順
- [アラート](monitoring-overview-alerts.md)の詳細について学習します。
- [Resource Manager テンプレートを使用してアクション グループ](monitoring-create-action-group-with-resource-manager-template.md)を追加する方法について確認します。
- [アクティビティ ログ アラートを作成して、サブスクリプションで自動スケールのエンジン操作をすべて監視する](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)方法について確認します。
- [アクティビティ ログ アラートを作成して、サブスクリプションで失敗した自動スケールのスケールイン/スケールアウト操作をすべて監視する](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)方法について確認します。
