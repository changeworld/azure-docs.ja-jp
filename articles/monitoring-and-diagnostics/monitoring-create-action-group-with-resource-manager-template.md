---
title: "Resource Manager を使用してアクション グループを作成する | Microsoft Docs"
description: "アクション グループを使用すると、特定のイベントが発生したときに電子メールや SMS で通知したり、webhook を呼び出したりできます。"
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
ms.date: 03/31/2017
ms.author: ancav
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 88e7b2e7781b80ea360531f4c3a45256de83b594
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="create-an-action-group-with-a-resource-manager-template"></a>Resource Manager テンプレートを使用したアクション グループの作成
この記事では、[Azure Resource Manager テンプレート](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) を使用して、アクション グループを構成する方法について説明します。 テンプレートを使用すると、リソースの作成時にアクション グループがリソースに自動的に設定され、アラートがトリガーされると、すべての適切なパーティが通知を受け取るように設定できます。

基本的な手順は次のとおりです。

1.  アクション グループの作成方法が記述された JSON ファイルとしてテンプレートを作成します。
2.  [任意のデプロイ方法を使用して、テンプレートをデプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)します。

最初に、アクション定義がテンプレートにハードコーディングされたアクショングループに対して、次に、テンプレートのデプロイ時に入力パラメーターとして webhook 構成情報を取得するテンプレートに対して、Resource Manager テンプレートを作成する方法を以下に示します。

## <a name="resource-manager-template-for-an-action-group"></a>アクション グループの Resource Manager テンプレート

Resource Manager テンプレートを使用してアクション グループを作成するには、`Microsoft.Insights/actionGroups` 型のリソースを作成し、関連するすべてのプロパティを入力します。 次に、アクション グループを作成するサンプル テンプレートをいくつか示します。

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
      "apiVersion": "2017-04-01",
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
            "emailAddress": "devops@contoso.com"
          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com"
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1"
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2"
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
        "description": "Webhook receiver service URI."
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
      "apiVersion": "2017-04-01",
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
            "serviceUri": "[parameters('webhookServiceUri')]"
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
[アクション グループ](monitoring-action-groups.md)の詳細については、こちらをご覧ください。  
[アラート](monitoring-overview-alerts.md)の詳細については、こちらをご覧ください。  
[Resource Manager テンプレートを使用するアラート](monitoring-create-activity-log-alerts-with-resource-manager-template.md)を追加する方法

