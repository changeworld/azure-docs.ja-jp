---
title: Resource Manager テンプレートを使用してアクション グループを作成する
description: Azure Resource Manager テンプレートを使用してアクション グループを作成する方法を説明します。
author: dkamstra
services: azure-monitor
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: dukek
ms.openlocfilehash: 1680ff8d209fc2680b19d3d3afe8c2b6aded9678
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044231"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Resource Manager テンプレートを使用したアクション グループの作成
この記事では、[Azure Resource Manager テンプレート](../../azure-resource-manager/templates/syntax.md)を使用して、アクション グループを構成する方法について説明します。 テンプレートを使用することで、特定の種類のアラートで再利用できるアクション グループを自動的に設定できます。 このようなアクション グループを使用することで、アラートがトリガーされたときに、すべての適切な関係者が通知を確実に受け取ることができます。

基本的な手順は次のとおりです。

1. アクション グループの作成方法が記述された JSON ファイルとしてテンプレートを作成します。

2. [任意のデプロイ方法](../../azure-resource-manager/templates/deploy-powershell.md)を使用してテンプレートをデプロイします。

## <a name="resource-manager-templates-for-an-action-group"></a>アクション グループの Resource Manager テンプレート

Resource Manager テンプレートを使用してアクション グループを作成するには、`Microsoft.Insights/actionGroups` 型のリソースを作成します。 次に、関連するすべてのプロパティを入力します。 次に、アクション グループを作成するサンプル テンプレートを 2 つ示します。

1 つ目のテンプレート。アクション グループ用の Resource Manager テンプレートを作成する方法を説明します。テンプレートにはアクション定義がハードコーディングされます。 2 つ目のテンプレート。テンプレートのデプロイ時に入力パラメータとして webhook 構成情報を受け取るテンプレートを作成する方法を説明します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2021-09-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com",
            "useCommonAlertSchema": true

          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com",
            "useCommonAlertSchema": true
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1",
            "useCommonAlertSchema": true
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2",
            "useCommonAlertSchema": true
          }
        ],
        "eventHubReceivers": [
          {
            "name": "contosoeventhub1",
            "subscriptionId": "replace with subscription id GUID",
            "eventHubNameSpace": "contosoeventHubNameSpace",
            "eventHubName": "contosoeventHub",
            "useCommonAlertSchema": true
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service Name."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2021-09-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]",
            "useCommonAlertSchema": true
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>次のステップ
* [アクション グループ](./action-groups.md)について学習します。
* [アラート](./alerts-overview.md)の詳細について学習します。
* [Resource Manager テンプレートを使用してアクション グループ](./alerts-activity-log.md)を追加する方法を学習します。