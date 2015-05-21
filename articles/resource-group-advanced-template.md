<properties
   pageTitle="Azure リソース マネージャーの高度なテンプレート操作"
   description="Azure にアプリをデプロイするときに Azure リソース マネージャーのテンプレートで入れ子になったテンプレートを使用し、コピー操作を行う方法を説明します。"
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="tomfitz;ilygre"/>

# 高度なテンプレートの操作

このトピックでは、リソースを Azure にデプロイする際、より高度なタスクを実行する際に使用できるコピー操作と入れ子になったテンプレートについて説明します。

## copy

リソースをデプロイするときに、配列の反復処理を行い、各要素を使用できるようにします。
   
次の例では、examplecopy-Contoso、examplecopy-Fabrikam、examplecopy-Coho という名前の 3 つの Web サイトをデプロイします。

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      },
      "count": { 
         "type": "int", 
         "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
      } 
    ]

## 入れ子になったテンプレート

時々、2 つのテンプレートのマージや、親テンプレートからの子テンプレートの開始が必要になることがあります。これは、子テンプレートをデプロイするマスター テンプレート内のデプロイメント リソースを使用して実現できます。次に示すように、入れ子になったテンプレートの URI を提供します。

    "variables": {"templatelink":"https://www.contoso.com/ArmTemplates/newStorageAccount.json"}, 
    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {"uri":"[variables('templatelink')]","contentVersion":"1.0.0.0"}, 
           "parameters": { 
              "StorageAccountName":{"value":"[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

## 次のステップ
- [Azure リソース マネージャーのテンプレートの作成](./resource-group-authoring-templates.md)
- [Azure リソース マネージャーのテンプレートの関数](./resource-group-template-functions.md)
- [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](./resouce-group-template-deploy.md)
- [Azure リソース マネージャーの概要](./resource-group-overview.md)

<!--HONumber=52-->
