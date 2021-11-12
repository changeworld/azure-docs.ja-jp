---
title: シングルテナントの Azure Logic Apps 用に DevOps を設定する
description: シングルテナント の Azure Logic Apps 用に DevOps デプロイを設定する方法。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/02/2021
ms.openlocfilehash: bfc901e3fa9c3a3f266f9cddffde84c4d1461c8f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425503"
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
| Azure ストレージ アカウント | はい (ステートフルとステートレスの両方のワークフローの場合) | この Azure リソースには、ワークフローに関するメタデータ、アクセスの制御用のキー、状態、入力、出力、実行履歴、およびその他の情報が格納されます。 |
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

`GET https://management.azure.com/subscriptions/{subscription-ID}/providers/Microsoft.Web/locations/{location}/managedApis/{connector-name}?api-version=2016-06-01`

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

ロジック アプリのプロジェクトの種類に基づいてビルド パイプラインを設定するには、次の表に記載した対応するアクションを実行します。

| プロジェクトの種類 | 説明と手順 |
|--------------|-----------------------|
| Nuget ベース | NuGet ベースのプロジェクト構造は、.NET Framework に基づきます。 これらのプロジェクトをビルドするには、.NET Standard のビルド手順に従ってください。 詳細については、「[Create a NuGet package using MSBuild](/nuget/create-packages/creating-a-package-msbuild)」\(MSBuild ドキュメントを使用した NuGet パッケージの作成\)を参照してください。 |
| バンドルベース | 拡張機能のバンドルベースのプロジェクトは言語固有ではないため、言語固有のビルド手順は必要ありません。 任意のメソッドを使用して、プロジェクトファイルを zip 圧縮できます。 <p><p>**重要**: .zip ファイルに、すべてのワークフロー フォルダー、host.json、connections.json などの構成ファイル、および他の関連ファイルなど、実際のビルド成果物が含まれていることを確認してください。 |
|||

### <a name="release-to-azure"></a>Azure へのリリース

Azure にデプロイするリリース パイプラインを設定するには、GitHub、Azure DevOps、または Azure CLI に関連付けられているオプションを選択します。

> [!NOTE]
> 現在、Azure Logic Apps は Azure デプロイ スロットをサポートしていません。

#### <a name="github"></a>[GitHub](#tab/github)

Github のデプロイでは、[GitHub Actions](https://docs.github.com/actions) (Azure Functions の GitHub Actions など) を使用してロジック アプリをデプロイできます。 この操作を行うには、次の情報を渡す必要があります。

- デプロイに使用するロジック アプリの名前
- すべてのワークフロー フォルダー、host.json、connections.json などの構成ファイル、および他の関連ファイルなど、実際のビルド成果物が含まれている .zip ファイル。
- [発行プロファイル](../azure-functions/functions-how-to-github-actions.md#generate-deployment-credentials) (認証に使用される)

```yaml
- name: 'Run Azure Functions Action'
  uses: Azure/functions-action@v1
  id: fa
  with:
   app-name: 'MyLogicAppName'
   package: 'MyBuildArtifact.zip'
   publish-profile: 'MyLogicAppPublishProfile'
```

詳細については、「[Continuous delivery by using GitHub Action](../azure-functions/functions-how-to-github-actions.md)」\(GitHub アクションを使用することによる継続的デリバリー)ドキュメントを参照してください。

#### <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

Azure DevOps デプロイの場合、Azure Pipelines で [Azure Function App Deploy タスク](/azure/devops/pipelines/tasks/deploy/azure-function-app?view=azure-devops&preserve-view=true)を使用してロジック アプリをデプロイできます。 この操作を行うには、次の情報を渡す必要があります。

- デプロイに使用するロジック アプリの名前
- すべてのワークフロー フォルダー、host.json、connections.json などの構成ファイル、および他の関連ファイルなど、実際のビルド成果物が含まれている .zip ファイル。
- [発行プロファイル](../azure-functions/functions-how-to-github-actions.md#generate-deployment-credentials) (認証に使用される)

```yaml
- task: AzureFunctionApp@1
  displayName: 'Deploy logic app workflows'
  inputs:
     azureSubscription: 'MyServiceConnection'
     appType: 'workflowapp'
     appName: 'MyLogicAppName'
     package: 'MyBuildArtifact.zip'
     deploymentMethod: 'zipDeploy'
```

詳細については、「[Deploy an Azure Function using Azure Pipelines](/azure/devops/pipelines/targets/azure-functions-windows)」\(Azure Pipelines を使用する Azure 関数のデプロイ\) ドキュメントを参照してください。

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

他のデプロイ ツールを使用する場合は、Azure CLI を使用してシングルテナント ベースのロジック アプリをデプロイできます。 始める前に、次の項目が必要になります。

- ローカル コンピューターにインストールされた最新の Azure CLI 拡張機能。

  - この拡張機能がない場合は、[ご使用のオペレーティング システムまたはプラットフォームのインストール ガイド](/cli/azure/install-azure-cli)を確認してください。

  - 最新バージョンかどうかが不明な場合は、[環境と CLI のバージョンを確認する手順](#check-environment-cli-version)に従ってください。

- "*プレビュー*" 版のシングルテナント Azure Logic Apps (Standard) Azure CLI 用拡張機能。

  この拡張機能がない場合は、[拡張機能をインストールする手順](#install-logic-apps-cli-extension)に従ってください。 シングルテナント Azure Logic Apps は一般提供されていますが、シングルテナント Azure Logic Apps の Azure CLI 用拡張機能はまだプレビュー段階です。

- ロジック アプリをデプロイするために使用する Azure リソース グループ。

  このリソース グループがない場合は、[リソース グループを作成する手順](#create-resource-group)に従ってください。

- データと実行履歴の保持のためにロジック アプリで使用する Azure ストレージ アカウント。

  このストレージ アカウントがない場合は、[ストレージ アカウントを作成する手順](/cli/azure/storage/account#az_storage_account_create)に従ってください。

<a name="check-environment-cli-version"></a>

##### <a name="check-environment-and-cli-version"></a>環境と CLI バージョンを確認する

1. [Azure portal](https://portal.azure.com) にサインインします。 ターミナルまたはコマンド ウィンドウで、[`az login`](/cli/azure/authenticate-azure-cli) コマンド を実行してサブスクリプションがアクティブであることを確認します。

   ```azurecli-interactive
   az login
   ```

1. ターミナルまたはコマンド ウィンドウで、`az` コマンドに次の必須パラメーターを設定して実行して、Azure CLI のバージョンを確認します。

   ```azurecli-interactive
   az --version
   ```

1. 最新の Azure CLI バージョンがない場合は、[オペレーティング システムまたはプラットフォーム用のインストール ガイド](/cli/azure/install-azure-cli)に従ってインストールを更新してください。

   最新バージョンの詳細については、[最新のリリース ノート](/cli/azure/release-notes-azure-cli?tabs=azure-cli)を参照してください。

<a name="install-logic-apps-cli-extension"></a>

##### <a name="install-azure-logic-apps-standard-extension-for-azure-cli"></a>Azure Logic Apps (Standard) の Azure CLI 用拡張機能をインストールする

現時点では、この拡張機能の "*プレビュー*" バージョンのみを使用できます。 この拡張機能をまだインストールしていない場合は、次の必須パラメーターを指定して `az extension add` コマンドを実行します。

```azurecli-interactive
az extension add --yes --source "https://aka.ms/logicapp-latest-py2.py3-none-any.whl"
```

最新の拡張機能 (バージョン 0.1.2) を取得するには、これらのコマンドを実行して既存の拡張機能を削除し、ソースから最新バージョンをインストールします。

```azurecli-interactive
az extension remove --name logicapp
az extension add --yes --source "https://aka.ms/logicapp-latest-py2.py3-none-any.whl"
```

> [!NOTE]
> 新しい拡張機能のバージョンを使用できる場合、現在のバージョンとそれ以降のバージョンにはメッセージが表示されます。 この拡張機能はプレビュー段階で、次のコマンドを使用して、手動で削除してから再度インストールすることなく、最新バージョンにアップグレードできます。
>
> `az logicapp upgrade`

<a name="create-resource-group"></a>

#### <a name="create-resource-group"></a>リソース グループの作成

ロジック アプリのリソース グループをまだ設定していない場合は、`az group create` コマンド を実行してグループを作成します。 Azure アカウントの既定のサブスクリプションをまだ設定していない場合は、`--subscription` パラメーターでサブスクリプション名または識別子を使用してください。 そうでない場合は、`--subscription` パラメーターを使用する必要はありません。

> [!TIP]
> 既定のサブスクリプションを設定するには、次のコマンドを実行し、`MySubscription` をご自分のサブスクリプション名または識別子に置き換える必要があります。
>
> `az account set --subscription MySubscription`

たとえば、次のコマンドは `MyResourceGroupName` という名前のリソース グループを、`MySubscription` という名前の Azure サブスクリプションを使用して `eastus` の場所に作成します。

```azurecli
az group create --name MyResourceGroupName 
   --subscription MySubscription 
   --location eastus
```

リソース グループが正常に作成されると、出力に `provisioningState` が `Succeeded` として示されます。

```output
<...>
   "name": "testResourceGroup",
   "properties": {
      "provisioningState": "Succeeded"
    },
<...>
```

<a name="deploy-logic-app"></a>

##### <a name="deploy-logic-app"></a>ロジック アプリをデプロイする

zip 形式の成果物を Azure リソース グループにデプロイするには、次の必須パラメーターを使用して `az logicapp deployment` コマンドを実行します。

> [!IMPORTANT]
> zip ファイルにプロジェクトの成果物がルート レベルで含まれていることを確認します。 これらの成果物には、すべてのワークフロー フォルダー、host.json、connections.js などの構成ファイル、および他の関連ファイルが含まれます。 余分なフォルダーを追加したり、プロジェクト構造にまだ存在しないフォルダーに成果物を含めたりしないでください。 たとえば次の一覧は MyBuildArtifacts.zip ファイル構造の例を示しています。
>
> ```output
> MyStatefulWorkflow1-Folder
> MyStatefulWorkflow2-Folder
> connections.json
> host.json
> ```

```azurecli-interactive
az logicapp deployment source config-zip --name MyLogicAppName 
   --resource-group MyResourceGroupName --subscription MySubscription 
   --src MyBuildArtifact.zip
```

---

### <a name="release-to-containers"></a>コンテナーへのリリース

ロジック アプリをコンテナー化する場合のデプロイは、デプロイして管理する他のコンテナーとほとんど同じように機能します。

エンドツーエンドのコンテナー ビルドとデプロイ パイプラインを実装する方法を示す例については、「[CI/CD for Containers](https://azure.microsoft.com/solutions/architecture/cicd-for-containers/)」\(コンテナー用の CI/CD\) を参照してください。

## <a name="next-steps"></a>次の手順

- [シングルテナントの Azure Logic Apps への DevOps のデプロイ](devops-deployment-single-tenant-azure-logic-apps.md)

シングルテナント Azure Logic Apps でのエクスペリエンスについてご意見をお聞かせください。

- バグまたは問題については、[GitHub で問題を作成してください](https://github.com/Azure/logicapps/issues)。
- 質問、要望、コメント、その他のフィードバックについては、[このフィードバック フォームを使用してください](https://aka.ms/logicappsdevops)。
