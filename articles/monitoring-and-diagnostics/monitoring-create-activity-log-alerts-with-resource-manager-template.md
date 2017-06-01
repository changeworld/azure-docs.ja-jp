---
title: "Resource Manager テンプレートでのアクティビティ ログ アラートの作成 | Microsoft Docs"
description: "Azure リソースの作成時に通知を受け取ります。"
author: anirudhcavale
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 239b8fe2a7724bb34e7060c90af73825e61d8398
ms.contentlocale: ja-jp
ms.lasthandoff: 04/12/2017


---
# <a name="create-an-activity-log-alert-with-a-resource-manager-template"></a>Resource Manager テンプレートでのアクティビティ ログ アラートの作成
この記事では、[Azure Resource Manager テンプレート](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates) を使用して Azure アクティビティ ログ アラートを構成する方法について説明します。 この方法を使用すると、リソースが自動デプロイ プロセスの一部として作成された時に、リソースにアラートを自動的に設定できます。

基本的な手順は次のとおりです。

1.    アクティビティ ログ アラートの作成方法が記述された JSON ファイルとしてテンプレートを作成します。
2.    [任意のデプロイ方法を使用して、テンプレートをデプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)します。

ここでは、Resource Manager テンプレートを作成して 1 つのアクティビティ ログ アラートを作成する方法を説明した後で、他のリソースを作成しながらアクティビティ ログ アラートを作成する方法を説明します。

## <a name="resource-manager-template-for-an-activity-log-alert"></a>アクティビティ ログ アラート用の Resource Manager テンプレート
Resource Manager テンプレートを使用してアクティビティ ログ アラートを作成するには、`microsoft.insights/activityLogAlerts` 型のリソースを作成し、関連するすべてのプロパティを入力します。 アクティビティ ログ アラートを作成するテンプレートを以下に示します。

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

## <a name="next-steps"></a>次のステップ
[アラート](monitoring-overview-alerts.md)の詳細については、こちらをご覧ください。  
[Resource Manager テンプレートを使用するアクション グループ](monitoring-create-action-group-with-resource-manager-template.md)を追加する方法

