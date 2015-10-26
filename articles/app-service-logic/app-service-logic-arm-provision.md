<properties 
	pageTitle="ロジック アプリの作成" 
	description="Azure リソース マネージャー テンプレートを使用し、ワークフローを定義するための空のロジック アプリをデプロイします。" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/01/2015" 
	ms.author="tomfitz"/>

# テンプレートを使用したロジック アプリの作成

このトピックでは、ワークフローの定義に使用できる空のロジック アプリを作成するための、Azure リソース マネージャー テンプレートを作成する方法について説明します。さらに、デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法について説明します。このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

ロジック アプリのプロパティの詳細については、[ロジック アプリ ワークフローの管理 API](https://msdn.microsoft.com/library/azure/dn948513.aspx) に関するページを参照してください。定義自体の例については、「[ロジック アプリの定義の作成](app-service-logic-author-definitions.md)」を参照してください。

テンプレートの作成の詳細については、[Azure リソース マネージャーのテンプレートの作成](../resource-group-authoring-templates.md)に関するページを参照してください。

完全なテンプレートについては、[ロジック アプリのテンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json)に関するページを参照してください。

## デプロイ対象

このテンプレートを使用して、ロジック アプリをデプロイします。

展開を自動的に実行するには、次のボタンをクリックします。

[![Azure への展開](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## パラメーター

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### testUri

     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/status/feed/"
      }
    
## デプロイ対象のリソース

### App Service プラン

App Service プランを作成します。

デプロイ先のリソース グループと同じ場所を使用します。

    {
        "apiVersion": "2014-06-01",
        "name": "[parameters('svcPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "AppServicePlan"
        },
        "properties": {
            "name": "[parameters('svcPlanName')]",
            "sku": "[parameters('sku')]",
            "workerSize": "[parameters('svcPlanSize')]",
            "numberOfWorkers": 1
        }
    }

### ロジック アプリ

ロジック アプリを作成します。

テンプレートでは、ロジック アプリ名のパラメーター値を使用します。ロジック アプリの場所がリソース グループと同じ場所に設定されます。

この特定の定義は、1 時間に 1 回実行され、**testUri** パラメーターで指定された場所に ping を実行します。

    {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-02-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "LogicApp"
        },
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
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
                        }
                    }
                },
                "outputs": { }
            },
            "parameters": { }
        }
    }

## デプロイを実行するコマンド

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup


 

<!---HONumber=Oct15_HO3-->