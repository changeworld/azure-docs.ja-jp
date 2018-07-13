---
title: Azure Resource Manager テンプレートを使用してロジック アプリを作成する | Microsoft Docs
description: Azure Resource Manager テンプレートを使用してロジック アプリのワークフローを作成してデプロイする
services: logic-apps
documentationcenter: ''
author: ecfan
manager: jeconnoc
editor: ''
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 0772ed0e6cca98c4e59b563a23549909636d55d0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38572622"
---
# <a name="create-and-deploy-logic-apps-with-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用してロジック アプリを作成してデプロイする

Azure Logic Apps には、ワークフローを自動化するためのロジック アプリの作成だけでなく、デプロイで使用されるリソースとパラメーターの定義にも使用できる Azure Resource Manager テンプレートが用意されています。 このテンプレートを独自のビジネス シナリオで使用することも、要件に合わせてカスタマイズすることもできます。 詳細については、[ロジック アプリ用の Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json)と [Azure Resource Manager テンプレートの構造と構文](../azure-resource-manager/resource-group-authoring-templates.md)に関する記事を参照してください。

## <a name="define-the-logic-app"></a>ロジック アプリを定義する

このロジック アプリの定義の例は、1 時間に 1 回実行され、`testUri` パラメーターに指定された場所に ping を実行します。
このテンプレートでは、ロジック アプリ名 (```logicAppName```) と ping テスト用の場所 (```testUri```) のパラメーター値を使用します。 詳細については、[テンプレートへのこれらのパラメーターの定義](#define-parameters)の説明を参照してください。 テンプレートには、ロジック アプリの場所として Azure リソース グループと同じ場所が設定されます。 

``` json
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
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "testURI": {
               "type": "string",
               "defaultValue": "[parameters('testUri')]"
            }
         },
         "triggers": {
            "Recurrence": {
               "type": "Recurrence",
               "recurrence": {
                  "frequency": "Hour",
                  "interval": 1
               }
            }
         },
         "actions": {
            "Http": {
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
``` 

<a name="define-parameters"></a>

### <a name="define-parameters"></a>パラメーターを定義する

[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

テンプレート内のパラメーターの説明を次に示します。

| パラメーター | 説明 | JSON 定義の例 | 
| --------- | ----------- | ----------------------- | 
| `logicAppName` | テンプレートで作成するロジック アプリの名前を定義します。 | "logicAppName": { "type": "string", "metadata": { "description": "myExampleLogicAppName" } } |
| `testUri` | ping テスト用の場所を定義します。 | "testUri": { "type": "string", "defaultValue": "http://azure.microsoft.com/status/feed/"} | 
||||

詳細については、[ロジック アプリのワークフロー定義とプロパティ用の REST API](https://docs.microsoft.com/rest/api/logic/workflows) と [JSON を使用したロジック アプリの定義の作成](logic-apps-author-definitions.md)に関する記事を参照してください。

## <a name="deploy-logic-apps-automatically"></a>ロジック アプリを自動的にデプロイする

ロジック アプリを作成して Azure に自動的にデプロイするには、**[Azure へデプロイ]** を選択します。

[![Azure へのデプロイ](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

このアクションで Azure ポータルにサインインして、ロジック アプリの詳細を指定したり、テンプレートやパラメーターに変更を加えたりすることができます。 たとえば、Azure ポータルでは、次の詳細の入力を求められます。

* Azure サブスクリプション名
* 使用するリソース グループ
* ロジック アプリの場所
* ロジック アプリの名前
* テスト URI
* 規定の使用条件への同意

## <a name="deploy-logic-apps-with-commands"></a>ロジック アプリをコマンドを使用してデプロイする

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup
``` 

### <a name="azure-cli"></a>Azure CLI

```
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup
```

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、[Azure Logic Apps フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、[Logic Apps のユーザー フィードバック サイト](http://aka.ms/logicapps-wish)にアクセスしてください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ロジック アプリの監視](../logic-apps/logic-apps-monitor-your-logic-apps.md)