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
   ms.date="06/29/2015"
   ms.author="tomfitz"/>

# 高度なテンプレートの操作

このトピックでは、リソースを Azure にデプロイする際、より高度なタスクを実行する際に使用できるコピー操作と入れ子になったテンプレートについて説明します。

## コピー

リソースをデプロイするときに、指定した回数だけ反復処理を実行します。
   
コピー操作は、配列内の各要素に対して反復処理するため、配列で作業するときに特に便利です。**copyIndex()** 関数は、反復処理の現在の値を返します。次の 3 つの Web サイトをデプロイできます。

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

次のテンプレートを使用します。

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

また、配列でなくてもコピー操作を実行することができます。たとえば、デプロイ済みの各リソース名の末尾に、インクリメンタル数を追加することもできます。次の 3 つの Web サイトをデプロイできます。

- examplecopy-0
- examplecopy-1
- examplecopy-2.

次のテンプレートを使用します。

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
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

前述の例では、インデックス値が 0 から 2 に増えることがわかります。インデックス値をオフセットするには、**copyIndex(1)** のように、**copyIndex()** 関数に値を渡します。実行する反復処理の数は copy 要素で指定されたままですが、copyIndex の値が指定された値でオフセットされます。そのため前の例と同じテンプレートを使用しても、**copyIndex(1)** を指定すると、次の名前の 3 つの Web サイトがデプロイされます。

- examplecopy-1
- examplecopy-2
- examplecopy-3

## 入れ子になったテンプレート

2 つのテンプレートをマージしたり、親テンプレートから子テンプレートを作成したりする場合があります。このような場合、入れ子になったテンプレートをポイントするマスター テンプレート内にあるデプロイ リソースを使用します。**templateLink** プロパティを、入れ子になったテンプレートの URI に設定します。入れ子になったテンプレートのパラメーター値を入力するには、テンプレート内で値を直接指定するか、パラメーター ファイルにリンク付けします。最初の例では、 **parameters** プロパティを使用して、パラメーター値を直接指定しています。

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/ArmTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

次の例では、 **parametersLink** プロパティを使用して、パラメーター ファイルにリンク付けしています。

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/ArmTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/ArmTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

## 次のステップ
- [Azure リソース マネージャーのテンプレートの作成](./resource-group-authoring-templates.md)
- [Azure リソース マネージャーのテンプレートの関数](./resource-group-template-functions.md)
- [Deploy an application with Azure Resource Manager template (Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ)](azure-portal/resource-group-template-deploy.md)
- [Azure リソース マネージャーの概要](./resource-group-overview.md)

<!---HONumber=July15_HO3-->