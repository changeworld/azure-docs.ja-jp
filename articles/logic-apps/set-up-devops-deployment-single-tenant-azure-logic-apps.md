---
title: シングルテナントの Azure Logic Apps 用に DevOps を設定する
description: シングルテナント の Azure Logic Apps 用に DevOps デプロイを設定する方法。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 05/25/2021
ms.openlocfilehash: bd35af7ac6602bba7e23bd10eda2f2b0fdff71db
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110379761"
---
# <a name="set-up-devops-deployment-for-single-tenant-azure-logic-apps"></a>シングルテナントの Azure Logic Apps 用に DevOps のデプロイを設定する

この記事では、DevOps ツールとプロセスを使用して、Visual Studio Code からインフラストラクチャにシングルテナント ベースのロジック アプリ プロジェクトをデプロイする方法について示します。 デプロイに GitHub と Azure DevOps のどちらを好むかに基づいて、シナリオに最適なパスとツールを選択します。 サンプルのロジック アプリ プロジェクトと Azure デプロイの例を含む同梱のサンプルは、GitHub または Azure DevOps を使用して使用できます。 シングルテナント用の DevOps の詳細については、「[DevOps deployment overview for single-tenant Azure Logic Apps](devops-deployment-single-tenant-azure-logic-apps.md)」\(シングル テナント の Azure Logic Apps 用の DevOps デプロイの概要\)を参照してください。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 Azure サブスクリプションをお持ちでない場合は、[無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

- [Visual Studio Code と Azure Logic Apps (Standard) 拡張機能](create-single-tenant-workflows-visual-studio-code.md#prerequisites)を使用して作成するシングル テナント ベースのロジック アプリ プロジェクト。

  ロジック アプリ プロジェクトまたはインフラストラクチャをまだ設定していない場合は、使用するソースとデプロイのオプションに基づき、同梱のサンプル プロジェクトを使用してサンプル アプリとインフラストラクチャをデプロイできます。 これらのサンプル プロジェクトと、サンプル ロジック アプリを実行するために含まれるリソースの詳細については、「[Deploy your infrastructre](#deploy-infrastructure)」\(インフラストラクチャのデプロイ\) を参照してください。

- Azure にデプロイする場合は、Azure で作成された既存の **ロジック アプリ (Standard)** リソースが必要です。 空のロジック アプリ リソースをすばやく作成するには、「[Create single-tenant based logic app workflows - Portal](create-single-tenant-workflows-azure-portal.md)」\(シングルテナント ベースのロジック アプリ ワークフローの作成 - ポータル\)を参照してください。

<a name="deploy-infrastructure"></a>

## <a name="deploy-infrastructure-resources"></a>インフラストラクチャ リソースのデプロイ

ロジック アプリ プロジェクトまたはインフラストラクチャをまだ設定していない場合は、使用するソースとデプロイのオプションに基づき、次のサンプル プロジェクトを使用してサンプル アプリとインフラストラクチャをデプロイできます。

- [シングルテナントの Azure Logic Apps 用の GitHub サンプル](https://github.com/Azure/logicapps/tree/master/github-sample)

  このサンプルには、シングルテナントの Azure Logic Apps 用のロジック アプリ プロジェクトの例とAzure のデプロイと GitHub Actions の例が含まれます。

- [シングルテナントの Azure Logic Apps 用の Azure DevOps サンプル](https://github.com/Azure/logicapps/tree/master/azure-devops-sample)

  このサンプルには、シングルテナントの Azure Logic Apps 用のロジック アプリ プロジェクトの例とAzure のデプロイと Azure Pipelines の例が含まれます。
  
どちらのサンプルにも、ロジックアプリが実行に使用する次のリソースが含まれています。

| リソース名 | 必須 | Description |
|---------------|----------|-------------|
| ロジック アプリ (Standard) | Yes | この Azure リソースには、シングルテナント Azure Logic Apps で実行されるワークフローが含まれています。 |
| Functions Premium または App Service ホスティング プラン | Yes | この Azure リソースでは、計算、処理、ストレージ、ネットワークなど、ロジック アプリの実行に使用するホスティング リソースを指定します。 <p><p>**重要**: 現在のエクスペリエンスでは、 **ロジック アプリ (standard)** リソースには、Functions Premium ホスティング プランに基づく [**Workflow Standard** ホスティング プラン](logic-apps-pricing.md#standard-pricing)が必要です。 |
| Azure ストレージ アカウント | はい (ステートレス ワークフロー用) | この Azure リソースには、ワークフローに関するメタデータ、状態、入力、出力、実行履歴、およびその他の情報が格納されます。 |
| Application Insights | オプション | この Azure リソースは、ワークフローの監視機能を提供します。 |
| API 接続 | 省略可能 (存在しない場合) | これらの Azure リソースは、ワークフローで、Office 365、SharePoint などのマネージ コネクタ操作を実行するために使用するマネージド API 接続を定義します。 <p><p>**重要**: ロジック アプリ プロジェクトでは、**connections.json** ファイルに、ワークフローで使用する任意のマネージド API 接続と Azure functions のメタデータ、エンドポイント、およびキーが含まれています。 環境ごとに異なる接続と関数を使用するには、**connections.json** ファイルをパラメーター化し、エンドポイントを更新してください。 <p><p>詳細については、 [API 接続リソースとアクセスポリシー](#api-connection-resources)を確認してください。 |
| Azure Resource Manager (ARM) テンプレート | オプション | この Azure リソースでは、再利用または [エクスポート](../azure-resource-manager/templates/template-tutorial-export-template.md)できるベースライン インフラストラクチャのデプロイを定義します。 このテンプレートには、例えばマネージド API 接続を使用するために必要なアクセスポリシーも含まれています。 <p><p>**重要**: ARM テンプレートのエクスポートには、ワークフローで使用する API 接続リソースに関連するすべてのパラメーターが含まれているわけではありません。 詳細については、「[Find API connection parameters](#find-api-connection-parameters)」\(API 接続パラメーターの検索\)を確認してください。 |
||||

<a name="api-connection-resources"></a>

## <a name="api-connection-resources-and-access-policies"></a>API 接続リソースとアクセス ポリシー

シングルテナントの Azure Logic Apps では、ワークフロー内のすべてのマネージドまたは API 接続リソースに、関連付けられたアクセス ポリシーが必要です。 このポリシーでは、マネージド コネクタ インフラストラクチャにアクセスするための適切なアクセス許可を提供するために、ロジック アプリの ID が必要です。 同梱のサンプル プロジェクトには、これらのアクセス ポリシーを含む、必要なすべてのインフラストラクチャ リソースを含む ARM テンプレートが含まれています。

次の図は、ロジック アプリ プロジェクトとインフラストラクチャ リソース間の依存関係を示しています。

![シングルテナントの Azure Logic Apps モデルでのロジック アプリ プロジェクト用にインフラストラクチャの依存関係を示す概念図。](./media/set-up-devops-deployment-single-tenant-azure-logic-apps/infrastructure-dependencies.png)

<a name="find-api-connection-parameters"></a>

### <a name="find-api-connection-parameters"></a>API 接続パラメーターを検索する

ワークフローでマネージド API 接続を使用している場合、エクスポート テンプレート機能を使用しても、関連するすべてのパラメーターが含まれるわけではありません。 ARM テンプレートでは、すべての [API 接続リソース定義の](logic-apps-azure-resource-manager-templates-overview.md#connection-resource-definitions) 一般的な形式は次のとおりです。

```json
{
   "type": "Microsoft.Web/connections",
   "apiVersion": "2016–06–01",
   "location": "[parameters('location')]",
   "name": "[parameters('connectionName')]",
   "properties": {}
}
```

接続リソース定義を完了するために `properties` オブジェクトで使用する必要がある値を見つけるには、特定のコネクタに対して次のAPI を使用できます。

`PUT https://management.azure.com/subscriptions/{subscription-ID}/providers/Microsoft.Web/locations/{location}/managedApis/{connector-name}?api-version=2018–07–01-preview`

応答において `connectionParameters` オブジェクトを探します。このオブジェクトには、その特定のコネクタのリソース定義を完了するために必要なすべての情報が含まれます。 次の例は、SQL マネージド接続のリソース定義の例を示しています。

```json
{
   "type": "Microsoft.Web/connections",
   "apiVersion": "2016–06–01",
   "location": "[parameters('location')]",
   "name": "[parameters('connectionName')]",
   "properties": {
      "displayName": "sqltestconnector",
      "api": {
         "id": "/subscriptions/{subscription-ID}/providers/Microsoft.Web/locations/{location}/managedApis/sql"
      },
      "parameterValues": {
         "authType": "windows", 
         "database": "TestDB",
         "password": "TestPassword",
         "server": "TestServer",
         "username": "TestUserName"
      }
   }
}
```

Logic Apps デザイナーで接続を作成するときのネットワーク トレースを確認するやり方もあります。 前に説明したように、コネクタのマネージド API の `PUT` 呼び出しを見つけ、必要なすべての情報で要求本文を確認します。

## <a name="deploy-logic-app-resources-zip-deploy"></a>ロジック アプリ リソースのデプロイ (zip デプロイ)

ロジック アプリ プロジェクトをソース リポジトリにプッシュした後、Azure 内外のインフラストラクチャにロジック アプリをデプロイするパイプラインのビルドおよびリリースを設定できます。

### <a name="build-your-project"></a>プロジェクトをビルドする

ロジック アプリのプロジェクトの種類に基づいてビルド パイプラインを設定するには、対応するアクションに従います。

* Nuget ベース: NuGet ベースのプロジェクト構造は、次の.NET Framework に基づきます。 これらのプロジェクトをビルドするには、.NET Standard のビルド手順に従ってください。 詳細については、「[Create a NuGet package using MSBuild](/nuget/create-packages/creating-a-package-msbuild)」\(MSBuild ドキュメントを使用した NuGet パッケージの作成\)を参照してください。

* バンドルベース: 拡張機能のバンドルベースのプロジェクトは言語固有ではないので言語固有のビルド手順は必要ありません。 任意のメソッドを使用して、プロジェクトファイルを zip 圧縮できます。

  > [!IMPORTANT]
  > .Zip ファイルには、すべてのワークフロー フォルダー、host.json、connections.js などの構成ファイル、およびその他の関連ファイルが含まれていることを確認します。

### <a name="release-to-azure"></a>Azure へのリリース

Azure にデプロイするリリース パイプラインを設定するには、GitHub、Azure DevOps、または Azure CLI に関連付けられているオプションを選択します。

> [!NOTE]
> 現在、Azure Logic Apps は Azure デプロイ スロットをサポートしていません。

#### <a name="github"></a>[GitHub](#tab/github)

Github のデプロイでは、[GitHub Actions](https://docs.github.com/actions) (Azure Functions の GitHub Actions など) を使用してロジック アプリをデプロイできます。 この操作を行うには、次の情報を渡す必要があります。

* 自社ビルド成果物
* デプロイに使用するロジック アプリの名前
* 自社発行プロファイル

```yaml
- name: 'Run Azure Functions Action'
  uses: Azure/functions-action@v1
  id: fa
  with:
   app-name: {your-logic-app-name}
   package: '{your-build-artifact}.zip'
   publish-profile: {your-logic-app-publish-profile}
```

詳細については、「[Continuous delivery by using GitHub Action](../azure-functions/functions-how-to-github-actions.md)」\(GitHub アクションを使用することによる継続的デリバリー)ドキュメントを参照してください。

#### <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

Azure DevOps デプロイの場合、Azure Pipelines で [Azure Function App Deploy タスク](/devops/pipelines/tasks/deploy/azure-function-app)を使用してロジック アプリをデプロイできます。 この操作を行うには、次の情報を渡す必要があります。

* 自社ビルド成果物
* デプロイに使用するロジック アプリの名前
* 自社発行プロファイル

```yaml
- task: AzureFunctionApp@1
  displayName: 'Deploy logic app workflows'
  inputs:
     azureSubscription: '{your-service-connection}'
     appType: 'workflowapp'
     appName: '{your-logic-app-name}'
     package: '{your-build-artifact}.zip'
     deploymentMethod: 'zipDeploy'
```

詳細については、「[Deploy an Azure Function using Azure Pipelines](/devops/pipelines/targets/azure-functions-windows)」\(Azure Pipelines を使用する Azure 関数のデプロイ\) ドキュメントを参照してください。

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

他のデプロイ ツールを使用する場合は、シングルテナントの Azure Logic Apps の Azure CLI コマンドを使用してロジック アプリをデプロイできます。 たとえば、zip 形式の成果物を Azure リソース グループにデプロイするには、次の CLI コマンドを実行します。

`az logicapp deployment source config-zip -g {your-resource-group} --name {your-logic-app-name} --src {your-build-artifact}.zip`

---

### <a name="release-to-containers"></a>コンテナーへのリリース

ロジック アプリをコンテナー化する場合のデプロイは、デプロイして管理する他のコンテナーとほとんど同じように機能します。

エンドツーエンドのコンテナー ビルドとデプロイ パイプラインを実装する方法を示す例については、「[CI/CD for Containers](https://azure.microsoft.com/solutions/architecture/cicd-for-containers/)」\(コンテナー用の CI/CD\) を参照してください。

## <a name="next-steps"></a>次の手順

* [シングルテナントの Azure Logic Apps への DevOps のデプロイ](devops-deployment-single-tenant-azure-logic-apps.md)

シングルテナント Azure Logic Apps でのエクスペリエンスについてご意見をお聞かせください。

- バグまたは問題については、[GitHub で問題を作成してください](https://github.com/Azure/logicapps/issues)。
- 質問、要望、コメント、その他のフィードバックについては、[このフィードバック フォームを使用してください](https://aka.ms/logicappsdevops)。