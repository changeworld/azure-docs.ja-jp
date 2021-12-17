---
title: 'チュートリアル: GitHub Actions を使用して App Service にデプロイし、データベースに接続する'
description: GitHub Actions を使用してデータベースに基づく ASP.NET Core アプリを Azure にデプロイする
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/13/2021
ms.author: jukullam
ms.custom: github-actions-azure
ms.openlocfilehash: a63a51299cf6ffac98dd7514e79c762b2acb67bd
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130236563"
---
# <a name="tutorial-use-github-actions-to-deploy-to-app-service-and-connect-to-a-database"></a>チュートリアル: GitHub Actions を使用して App Service にデプロイし、データベースに接続する

GitHub Actions ワークフローを設定して、[Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) バックエンドで ASP.NET Core アプリケーションをデプロイする方法について説明します。 これが完了すると、ASP.NET アプリは Azure 内で実行され、SQL Database に接続された状態になります。 まず、[ARM テンプレート](../azure-resource-manager/templates/overview.md)を使用してリソースを作成します。

このチュートリアルでは、コンテナーは使用しません。 コンテナー化された ASP.NET Core アプリケーションにデプロイする場合は、[GitHub Actions を使用した App Service for Containers へのデプロイとデータベースへの接続](app-service-sql-github-actions.md)に関する記事をご覧ください。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
>
> - GitHub Actions ワークフローを使用して、Azure Resource Manager テンプレート (ARM テンプレート) を使って Azure にリソースを追加する
> - GitHub Actions ワークフローを使用して ASP.NET Core アプリケーションを構築する

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- GitHub アカウント。 ない場合は、[無料](https://github.com/join)でサインアップしてください。
  - Resource Manager テンプレートとワークフロー ファイルを保存するための GitHub リポジトリ。 リポジトリを作成するには、[新しいリポジトリの作成](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository)に関するページをご覧ください。

## <a name="download-the-sample"></a>サンプルのダウンロード

Azure Samples リポジトリに[サンプル プロジェクトをフォーク](https://github.com/Azure-Samples/dotnetcore-sqldb-ghactions)します。

```
https://github.com/Azure-Samples/dotnetcore-sqldb-ghactions
```

## <a name="create-the-resource-group"></a>リソース グループの作成

https://shell.azure.com で Azure Cloud Shell を開きます。 ローカルにインストールした場合は、Azure CLI を使用することもできます (Cloud Shell の詳細については、[Cloud Shell の概要](../cloud-shell/overview.md)に関するページを参照してください)。

```azurecli-interactive
az group create --name {resource-group-name} --location {resource-group-location}
```

## <a name="generate-deployment-credentials"></a>デプロイ資格情報を生成する

リソース デプロイ スクリプトを機能させるには、サービス プリンシパルを使用して認証を行う必要があります。 [サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)は、[Azure CLI](/cli/azure/) で [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) コマンドを使用して作成できます。 このコマンドは、Azure portal で [Azure Cloud Shell](https://shell.azure.com/) を使用するか、 **[試してみる]** ボタンを選択して実行します。

```azurecli-interactive
    az ad sp create-for-rbac --name "{service-principal-name}" --sdk-auth --role contributor --scopes /subscriptions/{subscription-id}
```

この例のプレースホルダーは、お使いのサブスクリプション ID、リソース グループ名、サービス プリンシパル名に置き換えます。 これにより、この App Service アプリにアクセスするためのロールの割り当て資格情報を含む JSON オブジェクトが出力されます。 この JSON オブジェクトを後のためにコピーします。 詳細については、「[デプロイ資格情報の構成](https://github.com/Azure/login#configure-deployment-credentials)」を参照してください。

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret-for-authentication"></a>認証用の GitHub シークレットを構成する

[GitHub](https://github.com/) で自分のリポジトリを参照し、 **[設定]、[シークレット]、[Add a new secret]\(新しいシークレットの追加\)** の順に選択します。

[ユーザー レベルの資格情報](#generate-deployment-credentials)を使用するには、Azure CLI コマンドからの JSON 出力全体をシークレットの値フィールドに貼り付けます。 シークレットに `AZURE_CREDENTIALS` という名前を付けます。

## <a name="add-github-secrets-for-your-build"></a>ビルドに GitHub シークレットを追加する

1. `SQLADMIN_PASS` と `SQLADMIN_LOGIN` の GitHub リポジトリに [2 つの新しいシークレット](https://docs.github.com/en/actions/reference/encrypted-secrets#creating-encrypted-secrets-for-a-repository)を作成します。 複雑なパスワードを選択してください。そうしないと、SQL データベース サーバーの作成手順が失敗します。 このパスワードに再度アクセスすることはできないため、別途保存してください。

2. ご自分の Azure サブスクリプション ID に `AZURE_SUBSCRIPTION_ID` シークレットを作成します。 サブスクリプション ID がわからない場合は、Azure シェルで次のコマンドを使用して検索します。 `SubscriptionId` 列の値をコピーします。
    ```azurecli
    az account list -o table
    ```
 
## <a name="create-azure-resources"></a>Azure リソースを作成する

Azure リソースの作成ワークフローにより、[ARM テンプレート](../azure-resource-manager/templates/overview.md)が実行され、リソースが Azure にデプロイされます。 ワークフローは次のようになります。

- [Checkout アクション](https://github.com/marketplace/actions/checkout)を使用してソース コードをチェックアウトします。
- [Azure Login アクション](https://github.com/marketplace/actions/azure-login)を使用して Azure にログインし、環境と Azure リソース情報を収集します。
- [Azure Resource Manager デプロイ アクション](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template)を使用してリソースをデプロイします。

Azure リソースの作成ワークフローを実行するには、次のようにします。

1. リポジトリ内の `.github/workflows` の `infraworkflow.yml` ファイルを開きます。

1. `AZURE_RESOURCE_GROUP` 値をお使いのリソース グループ名に変更します。

1. ARM デプロイ アクションの `region` の入力を、お使いのリージョンに設定します。 
    1. `templates/azuredeploy.resourcegroup.parameters.json` を開き、`rgLocation` プロパティをお使いのリージョンに更新します。
 
1. **[Actions]\(アクション\)** に移動し、 **[Run workflow]\(ワークフローの実行\)** を選択します。

   :::image type="content" source="media/github-actions-workflows/github-actions-run-workflow.png" alt-text="GitHub Actions ワークフローを実行して、リソースを追加する。":::

1. **[Actions]\(アクション\)** ページで緑色のチェックマークを確認して、アクションが正常に実行されたことを確認します。

   :::image type="content" source="media/github-actions-workflows/create-resources-success.png" alt-text="リソースの作成の正常実行。":::

1. リソースを作成したら、 **[アクション]** に移動し、[Azure リソースを作成] を選択して、ワークフローを無効にします。 
 
    :::image type="content" source="media/github-actions-workflows/disable-workflow-github-actions.png" alt-text="Azure リソースを作成ワークフローを無効にする。":::

## <a name="create-a-publish-profile-secret"></a>発行プロファイル シークレットを作成する

1. Azure portal で、`Create Azure Resources` ワークフローで作成された新しいステージング App Service (スロット) を開きます。

1. **[発行プロファイルの取得]** を選択します。

1. 公開プロファイル ファイルをテキスト エディターで開き、その内容をコピーします。 

1. `AZURE_WEBAPP_PUBLISH_PROFILE` に新しい GitHub シークレットを作成します。 

## <a name="build-and-deploy-your-app"></a>アプリをビルドし、デプロイする

ビルドを実行し、ワークフローをデプロイするには、次のようにします。

1. リポジトリ内の `.github/workflows` の `workflow.yaml` ファイルを開きます。

1. `AZURE_RESOURCE_GROUP`、`AZURE_WEBAPP_NAME`、`SQLSERVER_NAME`、`DATABASE_NAME` の環境変数が `infraworkflow.yml` の環境変数と一致することを確認します。

1. [Swap to production slot output]\(運用スロットにスワップ出力\) の URL にアクセスして、デプロイされているアプリがを確認します。 サンプル アプリの My TodoList App が表示されます。 
 
## <a name="clean-up-resources"></a>リソースをクリーンアップする

サンプル プロジェクトが不要になった場合は、Azure portal でリソース グループを削除し、GitHub でリポジトリを削除します。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure と GitHub の統合に関する詳細](/azure/developer/github/)