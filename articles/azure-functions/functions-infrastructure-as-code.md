---
title: "Azure Functions のリソースのデプロイの自動化 | Microsoft Docs"
description: "Functions App を Microsoft Azure にデプロイする Azure Resource Manager テンプレートを作成する方法について説明します。"
services: Functions
documtationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 関数, サーバーレス アーキテクチャ, コードとしてのインフラストラクチャ, Azure Resource Manager"
ms.assetid: 
ms.server: functions
ms.devlang: multiple
ms.topic: 
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/23/2017
ms.author: cfowler;glenga
translationtype: Human Translation
ms.sourcegitcommit: d11ef8865d21dfe4e56bcb6fa08ce58c53f3d309
ms.openlocfilehash: 7c55a1d34df71c2c958f42f43810fbce7bd74e5d


---

# <a name="automate-the-deployment-of-resources-for-your-azure-functions-app"></a>Azure Functions App のリソースのデプロイを自動化する

このトピックでは、Function App をデプロイする Azure Resource Manager テンプレートを作成する方法について説明します。 Azure Functions に必要なリソースのベースラインと、デプロイの実行時に指定されるパラメーターを定義する方法について学習します。 関数で使用する[トリガーとバインド](functions-triggers-bindings.md)によっては、コードとしてのインフラストラクチャとしてアプリケーション全体をカバーするには、追加のリソースをデプロイすることが必要になる場合があります。

テンプレートの作成の詳細については、「[Azure Resource Manager のテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)」を参照してください。

完全なテンプレートの例については、[使用量ベースの Azure 関数の作成](https://github.com/Azure/azure-quickstart-templates/blob/052db5feeba11f85d57f170d8202123511f72044/101-function-app-create-dynamic/azuredeploy.json)や [App Service プラン ベースの Azure 関数の作成](https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json)に関するページをご覧ください。

## <a name="what-is-deployed"></a>デプロイ対象

次の例では、ベースラインとなる Azure Function App を作成します。 Function App に必要なリソースは次のとおりです。

* [Azure ストレージ アカウント](../storage/index.md)
* ホスティング プラン (従量課金プランまたは App Service プラン)
* Function App (種類が **functionapp** の Microsoft.Web/Site)

## <a name="parameters"></a>パラメーター

Azure リソース マネージャーを使用して、テンプレートのデプロイ時に値を指定するパラメーターを定義します。 テンプレートには、すべてのパラメーター値を含む Parameters という名前のセクションがあります。 これらの値のパラメーターを定義する必要があります。これらの値は、デプロイするプロジェクトやデプロイ先の環境に応じて異なります。

[Variables](../azure-resource-manager/resource-group-authoring-templates.md#variables) は、個別のデプロイに基づいて変更されない値や、テンプレートで使用する前に変換が必要なパラメーターに役立ちます (例: 検証規則に合格する目的で)。

デプロイ中にユーザーが指定できる値を指定するには、パラメーターを定義するときに **allowedValues** フィールドを使用します。 **defaultValue** フィールドは、デプロイ中に値が指定されなかった場合にパラメーターに割り当てる値を指定するために使用します。

テンプレートのパラメーターを記述してみましょう。

### <a name="appname"></a>appName

作成する関数の名前です。

```json
"appName": {
    "type": "string"
}
```

### <a name="location"></a>location

Function App をデプロイする場所です。

> [!NOTE]
> **defaultValue** パラメーターは、リソース グループの場所を継承するために、または PowerShell や CLI のデプロイ時にパラメーターを指定していない場合に使用します。 Portal からデプロイする場合、**allowedValues** から選択するためのドロップダウン ボックスが表示されます。

> [!TIP]
> Azure Functions のリージョンの最新の一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/)」のページにアクセスすると確認できます。

```json
"location": {
    "type": "string",
    "allowedValues": [
        "Brazil South",
        "East US",
        "East US 2",
        "Central US",
        "North Central US",
        "South Central US",
        "West US",
        "West US 2"
    ],
    "defaultValue": "[resourceGroup().location]"
}
```

### <a name="sourcecoderepositoryurl-optional"></a>sourceCodeRepositoryURL (省略可能)

```json
"sourceCodeRepositoryURL": {
    "type": "string",
    "defaultValue": "",
    "metadata": {
    "description": "Source code repository URL"
}
```

### <a name="sourcecodebranch-optional"></a>sourceCodeBranch (省略可能)

```json
    "sourceCodeBranch": {
      "type": "string",
      "defaultValue": "master",
      "metadata": {
        "description": "Sourcecode Repo branch"
      }
    }
```

### <a name="sourcecodemanualintegration-optional"></a>sourceCodeManualIntegration (省略可能)

```json
"sourceCodeManualIntegration": {
    "type": "bool",
    "defaultValue": false,
    "metadata": {
        "description": "Use 'true' if you are deploying from the base repo, 'false' if you are deploying from your own fork. If you're using 'false', make sure you have admin permissions to the repo. If you get an error, you should add GitHub integration to another web app manually, so that you get a GitHub access token associated with your Azure Subscription."
    }
}
```

## <a name="variables"></a>変数

Azure Resource Manager テンプレートには、パラメーターに加えて、変数の概念もあり、テンプレートで使用するさらに具体的な設定を構築するためのパラメーターを組み込むことができます。

次の例では、変数を活用して [Azure Resource Manager のテンプレートの関数](../azure-resource-manager/resource-group-template-functions.md)を適用し、提供された appName を取得して、Azure ストレージ アカウントの[名前付けに関する要件](../storage/storage-create-storage-account.md#create-a-storage-account)を満たすように小文字に変換していることがわかります。

```json
"variables": {
    "lowerSiteName": "[toLower(parameters('appName'))]",
    "storageAccountName": "[concat(variables('lowerSiteName'))]"
}
```

## <a name="resources-to-deploy"></a>デプロイ対象のリソース

### <a name="storage-account"></a>ストレージ アカウント

Azure ストレージ アカウントは、Azure Functions で必須のリソースです。

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[variables('storageLocation')]",
    "properties": {
        "accountType": "[variables('storageAccountType')]"
    }
}
```

### <a name="hosting-plan-consumption-vs-app-service-plan"></a>ホスティング プラン: 従量課金プランと App Service プランの比較

関数を作成するときには、関数が完全管理のスケーリング、つまりプラットフォームによってオンデマンドでスケーリングする必要があるシナリオが存在します (従量課金)。 また、関数が専用のハードウェア上で 24 時間 365 日実行される、ユーザー管理のスケーリングも選択できます (App Service プラン)。この場合、インスタンスの数は手動または自動で構成できます。 別のプランではなく特定のプランを使用するという判断は、そのプランで使用できる関数に基づいて行います。または、コスト別の設計によって意思決定が行われます。 さまざまなホスティング プランの詳細については、「[Azure Functions のスケーリング](functions-scale.md)」の記事を参照してください。

#### <a name="consumption-plan"></a>従量課金プラン

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

#### <a name="app-service-plan"></a>App Service プラン

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="function-app-site"></a>Function App (サイト)

スケーリングのオプションを選択したら、今度はコンテナーを作成します。コンテナーにはすべての関数が格納され、Function App と呼ばれます。

Function App には多くの子リソースがあり、そこに含まれる**アプリ設定**と**ソース管理オプション**を利用できます。 子リソース **sourcecontrols** を削除し、別の[デプロイ オプション](functions-continuous-deployment.md)を利用することも選択できます。

> [!IMPORTANT]
> Azure でのリソースのデプロイ方法を理解して、Azure Resource Manager を使用する際にアプリケーション向けにコードとしてのインフラストラクチャの効果的な構成を確実に作成することが重要です。 この例では、**siteConfig** を使用して適用される最上位レベルの構成があることがわかります。これらは、子リソースの **sourcecontrols/web** が適用される前に、必要な Azure Functions のランタイムとデプロイ エンジンに意味を伝えるため、最上位レベルに設定することが重要です。 これらの設定は子レベルのリソース **config/appSettings** で構成できますが、**config/appsettings** が適用される "*前*" に Function App と Azure Functions をデプロイする必要があるシナリオもあります。その理由は、Azure Functions が、[ロジック アプリ](../logic-apps/index.md)などの他のリソースと依存関係にあるためです。

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            { "name": "FUNCTIONS_EXTENSION_VERSION", "value": "~1" },
            { "name": "Project", "value": "src" }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> このテンプレートでは、[プロジェクト](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) アプリの設定を使用します。これによって、関数デプロイ エンジン (Kudu) がデプロイ可能なコードを検索するベース ディレクトリを設定します。 この例では、使用する関数がその子となっている `src` フォルダーが GitHub リポジトリに含まれるため、この値を `src` に設定しました。 リポジトリのルートに関数が直接存在するか、ソース管理からデプロイしない場合は、このアプリ設定を削除できます。

## <a name="deploying-your-template"></a>テンプレートのデプロイ

* [Powershell](../azure-resource-manager/resource-group-template-deploy.md)
* [CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [ポータル](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>[Azure へのデプロイ] ボタン

```<url-encoded-path-to-azuredeploy-json>``` を、GitHub の `azuredeploy.json` ファイルの生のパスの[エンコードされた URL](https://www.bing.com/search?q=url+encode) で置き換えます。

#### <a name="markdown"></a>Markdown

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

#### <a name="html"></a>HTML

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>次のステップ

コードから Function App をデプロイできるようになったので、この機会に Azure Functions を開発および構成する方法を学んでください。

* [Azure Functions developer reference (Azure Functions 開発者向けリファレンス)](functions-reference.md)
* [Azure Functions App の設定の構成方法](functions-how-to-use-azure-function-app-settings.md)
* [初めての Azure 関数の作成](functions-create-first-azure-function.md)


<!--HONumber=Jan17_HO4-->


