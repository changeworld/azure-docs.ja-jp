---
title: 'チュートリアル: GitHub Actions を使用して App Service カスタム コンテナーにデプロイし、データベースに接続する'
description: GitHub Actions を使用して ASP.NET Core アプリを Azure と Azure SQL Database にデプロイする方法について説明します
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/22/2021
ms.author: jukullam
ms.custom: github-actions-azure
ms.openlocfilehash: 24001d71721f2e3ee243248705ed8d4b64571c83
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128675404"
---
# <a name="tutorial-use-github-actions-to-deploy-to-an-app-service-custom-container-and-connect-to-a-database"></a>チュートリアル: GitHub Actions を使用して App Service カスタム コンテナーにデプロイし、データベースに接続する

このチュートリアルでは、GitHub Actions ワークフローを設定して、[Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md) バックエンドでコンテナー化された ASP.NET Core アプリケーションをデプロイする手順について説明します。 これが完了すると、ASP.NET アプリは Azure 内で実行され、SQL Database に接続された状態になります。 まず、[ARM テンプレート](../azure-resource-manager/templates/overview.md) の GitHub Actions ワークフローを使用して Azure リソースを作成します。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
>
> - GitHub Actions ワークフローを使用して、Azure Resource Manager テンプレート (ARM テンプレート) を使って Azure にリソースを追加する
> - GitHub Actions ワークフローを使用して、最新の Web アプリ変更を含むコンテナーを構築する

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- GitHub アカウント。 ない場合は、[無料](https://github.com/join)でサインアップしてください。
  - Resource Manager テンプレートとワークフロー ファイルを保存するための GitHub リポジトリ。 リポジトリを作成するには、[新しいリポジトリの作成](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository)に関するページをご覧ください。

## <a name="download-the-sample"></a>サンプルのダウンロード

Azure Samples リポジトリに[サンプル プロジェクトをフォーク](https://github.com/Azure-Samples/dotnetcore-containerized-sqldb-ghactions/)します。

```
https://github.com/Azure-Samples/dotnetcore-containerized-sqldb-ghactions/
```

## <a name="create-the-resource-group"></a>リソース グループの作成

https://shell.azure.com で Azure Cloud Shell を開きます。 ローカルにインストールした場合は、Azure CLI を使用することもできます (Cloud Shell の詳細については、Cloud Shell の概要に関するページを参照してください)。

```azurecli-interactive
    az group create --name {resource-group-name} --location {resource-group-location}
```

## <a name="generate-deployment-credentials"></a>デプロイ資格情報を生成する

リソース デプロイ スクリプトを機能させるには、サービス プリンシパルを使用して認証を行う必要があります。 [サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)は、[Azure CLI](/cli/azure/) で [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) コマンドを使用して作成できます。 このコマンドは、Azure portal で [Azure Cloud Shell](https://shell.azure.com/) を使用するか、 **[試してみる]** ボタンを選択して実行します。

```azurecli-interactive
    az ad sp create-for-rbac --name "{service-principal-name}" --sdk-auth --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
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

> [!IMPORTANT]
> 常に最小限のアクセス権を付与することをお勧めします。 前の例の範囲は、リソース グループ全体ではなく、特定の App Service アプリに限定されます。

## <a name="configure-the-github-secret-for-authentication"></a>認証用の GitHub シークレットを構成する

[GitHub](https://github.com/) で自分のリポジトリを参照し、 **[設定]、[シークレット]、[Add a new secret]\(新しいシークレットの追加\)** の順に選択します。

[ユーザー レベルの資格情報](#generate-deployment-credentials)を使用するには、Azure CLI コマンドからの JSON 出力全体をシークレットの値フィールドに貼り付けます。 シークレットに `AZURE_CREDENTIALS` と名前を付けます。

## <a name="add-a-sql-server-secret"></a>SQL Server シークレットを追加する

リポジトリに `SQL_SERVER_ADMIN_PASSWORD` の新しいシークレットを作成します。 このシークレットには、パスワード セキュリティの Azure 標準を満たす任意のパスワードを設定できます。 このパスワードに再度アクセスすることはできないため、別途保存してください。

## <a name="create-azure-resources"></a>Azure リソースを作成する

Azure リソースの作成ワークフローにより、[ARM テンプレート](../azure-resource-manager/templates/overview.md)が実行され、リソースが Azure にデプロイされます。 ワークフローは次のようになります。

- [Checkout アクション](https://github.com/marketplace/actions/checkout)を使用してソース コードをチェックアウトします。
- [Azure Login アクション](https://github.com/marketplace/actions/azure-login)を使用して Azure にログインし、環境と Azure リソース情報を収集します。
- [Azure Resource Manager デプロイ アクション](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template)を使用してリソースをデプロイします。

Azure リソースの作成ワークフローを実行するには、次のようにします。

1. リポジトリ内の `.github/workflows` の `azuredeploy.yaml` ファイルを開きます。

1. `AZURE_RESOURCE_GROUP` 値をお使いのリソース グループ名に変更します。

1. **[Actions]\(アクション\)** に移動し、 **[Run workflow]\(ワークフローの実行\)** を選択します。

   :::image type="content" source="media/github-actions-workflows/github-actions-run-workflow.png" alt-text="GitHub Actions ワークフローを実行して、リソースを追加する。":::

1. **[Actions]\(アクション\)** ページで緑色のチェックマークを確認して、アクションが正常に実行されたことを確認します。

   :::image type="content" source="media/github-actions-workflows/create-resources-success.png" alt-text="リソースの作成の正常実行。":::

## <a name="add-container-registry-and-sql-secrets"></a>コンテナー レジストリと SQL シークレットを追加する

1. Azure portal で、リソース グループで新しく作成される Azure コンテナー レジストリを開きます。

1. **[アクセス キー]** に移動し、ユーザー名とパスワードの値をコピーします。

1. リポジトリに `ACR_USERNAME` と `ACR_PASSWORD` パスワードの新しい GitHub シークレットを作成します。

1. Azure portal で Azure SQL データベースを開きます。 **[接続文字列]** を開いて、値をコピーします。

1. `SQL_CONNECTION_STRING` の新しいシークレットを作成します。 `{your_password}` は実際の `SQL_SERVER_ADMIN_PASSWORD` に置き換えます。

## <a name="build-push-and-deploy-your-image"></a>イメージのビルド、プッシュ、デプロイ

ビルド、プッシュ、デプロイのワークフローにより、最新のアプリ変更を含むコンテナーがビルドされ、コンテナーが [Azure コンテナー レジストリ](../container-registry/index.yml)にプッシュされ、プッシュされた最新のコンテナーを指す Web アプリケーション ステージング スロットが更新されます。 ワークフローには、ビルド ジョブとデプロイ ジョブを含めます。

- ビルド ジョブでは、[Checkout アクション](https://github.com/marketplace/actions/checkout)を使用してソース コードがチェックアウトされます。 次に、[Docker Login アクション](https://github.com/marketplace/actions/docker-login)とカスタム スクリプトを使用して Azure Container Registry で認証し、コンテナー イメージがビルドされ、Azure Container Registry にデプロイされます。
- デプロイ ジョブでは、[Azure Login アクション](https://github.com/marketplace/actions/azure-login)を使用して Azure にログインし、環境と Azure リソース情報が収集されます。 次に、Web アプリ設定が [Azure App Service Settings アクション](https://github.com/marketplace/actions/azure-app-service-settings)を使用して更新され、[Azure Web Deploy アクション](https://github.com/marketplace/actions/azure-webapp)を使用して App Service ステージング スロットにデプロイされます。 最後に、カスタム スクリプトを実行して SQL データベースが更新され、ステージング スロットが運用に入れ替えられます。

ビルド、プッシュ、およびデプロイ ワークフローを実行するには、次のようにします。

1. リポジトリ内の `.github/workflows` の `build-deploy.yaml` ファイルを開きます。

1. `AZURE_RESOURCE_GROUP` と `WEB_APP_NAME` の環境変数が `azuredeploy.yaml` の環境変数と一致することを確認します。

1. Azure Container Registry ログイン サーバーの `ACR_LOGIN_SERVER` 値を更新します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure と GitHub の統合に関する詳細](/azure/developer/github/)