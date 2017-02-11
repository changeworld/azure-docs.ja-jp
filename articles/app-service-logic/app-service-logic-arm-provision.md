---
title: "Azure App Service で Azure Resource Manager テンプレートを使用してロジック アプリを作成する |Microsoft Docs"
description: "Azure リソース マネージャー テンプレートを使用し、ワークフローを定義するための空のロジック アプリをデプロイします。"
services: logic-apps
documentationcenter: 
author: MSFTMan
manager: erikre
editor: 
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 61ab873e77e3f7a691c815ba48c7f29b45269db9


---
# <a name="create-a-logic-app-using-a-template"></a>テンプレートを使用したロジック アプリの作成
Azure リソース マネージャー テンプレートを使用して、ワークフローを定義するために使用できる空のロジック アプリを作成します。 デプロイ対象のリソースと、デプロイの実行時に指定されるパラメーターを定義できます。 このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

ロジック アプリのプロパティの詳細については、 [ロジック アプリ ワークフローの管理 API](https://msdn.microsoft.com/library/azure/mt643788.aspx)に関するページを参照してください。 

定義自体の例については、「 [ロジック アプリの定義の作成](app-service-logic-author-definitions.md)」を参照してください。 

テンプレートの作成の詳細については、「 [Authoring Azure Resource Manager Templates (Azure リソース マネージャー テンプレートのオーサリング)](../resource-group-authoring-templates.md)」を参照してください。

完全なテンプレートについては、 [ロジック アプリのテンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json)に関するページを参照してください。

## <a name="what-you-will-deploy"></a>デプロイ対象
このテンプレートを使用して、ロジック アプリをデプロイします。

デプロイを自動的に実行するには、次のボタンを選択します。  

[![Azure へのデプロイ](media/app-service-logic-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## <a name="parameters"></a>パラメーター
[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### <a name="testuri"></a>testUri
     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
      }

## <a name="resources-to-deploy"></a>デプロイ対象のリソース
### <a name="logic-app"></a>ロジック アプリ
ロジック アプリを作成します。

テンプレートでは、ロジック アプリ名のパラメーター値を使用します。 ロジック アプリの場所がリソース グループと同じ場所に設定されます。 

この特定の定義は、1 時間に 1 回実行され、 **testUri** パラメーターで指定された場所に ping を実行します。 

    {
      "type": "Microsoft.Logic/workflows",
      "apiVersion": "2016-06-01",
      "name": "[parameters('logicAppName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "displayName": "LogicApp"
      },
      "properties": {
        "definition": {
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      }
    }


## <a name="commands-to-run-deployment"></a>デプロイを実行するコマンド
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup






<!--HONumber=Nov16_HO3-->


