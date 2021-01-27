---
title: クイックスタート -  Azure Key Vault のシークレットを GitHub Actions ワークフローで使用する
description: GitHub Actions で Key Vault のシークレットを使用してソフトウェア開発ワークフローを自動化する
author: juliakm
ms.custom: github-actions-azure
ms.author: jukullam
ms.date: 11/24/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: c1e8062210de1d7d99f57a4e0b155492f7dfdd9a
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/26/2021
ms.locfileid: "98785978"
---
# <a name="use-key-vault-secrets-in-github-actions-workflows"></a>Key Vault のシークレットを GitHub Actions ワークフローで使用する

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) で Key Vault のシークレットを使用して、パスワードや他のシークレットを Azure Key Vault に安全に格納します。 [Key Vault](../general/overview.md) の詳細を確認してください。 

Key Vault と GitHub Actions を使用すると、一元化されたシークレット管理ツールのベネフィットと GitHub Actions のあらゆる利点を活かすことができます。 GitHub Actions は、ソフトウェア開発ワークフローを自動化する一連の GitHub 機能です。 コードを保存する場所と同じ場所にワークフローをデプロイし、プル要求や問題に対して共同作業を行うことができます。 


## <a name="prerequisites"></a>前提条件 
- GitHub アカウント。 お持ちでない場合は、[無料](https://github.com/join)でサインアップしてください。  
- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- GitHub リポジトリに接続された Azure アプリ。 この例では、「[Azure App Service にコンテナーをデプロイする](/azure/developer/javascript/tutorial-vscode-docker-node-01)」を使用します。 
- Azure Key Vault。  Azure portal、Azure CLI、または Azure PowerShell を使用して、Azure Key Vault を作成できます。

## <a name="workflow-file-overview"></a>ワークフロー ファイルの概要

ワークフローは、お使いのリポジトリの `/.github/workflows/` パスの YAML (.yml) ファイルに定義されます。 この定義には、ワークフローを構成するさまざまな手順とパラメーターが含まれます。

GitHub Actions に対する認証に関して、このファイルには次の 2 つのセクションがあります。

|Section  |タスク  |
|---------|---------|
|**認証** | 1.サービス プリンシパルを定義します。 <br /> 2.GitHub シークレットを作成します。 <br /> 3. ロールの割り当てを追加します。 |
|**Key Vault** | 1. キー コンテナー アクションを追加します。 <br /> 2. キー コンテナー シークレットを参照します。 |

## <a name="authentication"></a>認証

[サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)は、[Azure CLI](/cli/azure/) で [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) コマンドを使用して作成できます。 このコマンドは、Azure portal で [Azure Cloud Shell](https://shell.azure.com/) を使用するか、 **[試してみる]** ボタンを選択して実行します。

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

上記の例で、プレースホルダーを実際のサブスクリプション ID とリソース グループ名に置き換えます。 `myApp` のプレースホルダーはアプリケーションの名前に置き換えます。 これにより、以下のようなご自分の App Service アプリにアクセスするためのロールの割り当て資格情報を含む JSON オブジェクトが出力されます。 この JSON オブジェクトを後のためにコピーします。 必要なのは、`clientId`、`clientSecret`、`subscriptionId`、および `tenantId` の値を持つセクションだけです。 

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

### <a name="configure-the-github-secret"></a>GitHub シークレットの構成

Azure の資格情報、リソース グループ、およびサブスクリプションのシークレットを作成します。 

1. [GitHub](https://github.com/) でリポジトリを参照します。

1. **[Settings]\(設定\) > [Secrets]\(シークレット\) > [New secret]\(新しいシークレット\)** の順に選択します。

1. Azure CLI コマンドからの JSON 出力全体をシークレットの値フィールドに貼り付けます。 シークレットに `AZURE_CREDENTIALS` と名前を付けます。

1. 後で使用するため、`clientId` の値をコピーしておきます。 

### <a name="add-a-role-assignment"></a>ロールの割り当てを追加する 
 
キー コンテナーにアクセスして `get` 操作や `list` 操作を行えるように、Azure サービス プリンシパルにアクセス権を与える必要があります。 この作業を行わないと、サービス プリンシパルは使用できません。 

`keyVaultName` は実際のキー コンテナーの名前に、また、`clientIdGUID` は実際の `clientId` の値に置き換えてください。 

```azurecli-interactive
    az keyvault set-policy -n {keyVaultName} --secret-permissions get list --spn {clientIdGUID}
```

## <a name="use-the-azure-key-vault-action"></a>Azure キー コンテナー アクションを使用する

[Azure キー コンテナー アクション](https://github.com/marketplace/actions/azure-key-vault-get-secrets)を使用すると、Azure Key Vault のインスタンスからシークレットをフェッチして、GitHub Actions ワークフローの中で利用することができます。

フェッチしたシークレットは出力として設定されるほか、環境変数としても設定されます。 変数は、コンソールやログに出力される際に自動的にマスクされます。

```yaml
    - uses: Azure/get-keyvault-secrets@v1
      with:
        keyvault: "my Vault" # name of key vault in Azure portal
        secrets: 'mySecret'  # comma separated list of secret keys to fetch from key vault 
      id: myGetSecretAction # ID for secrets that you will reference
```

ワークフローでキー コンテナーを使用するには、キー コンテナー アクションが必要であり、また、そのアクションを参照する必要もあります。 

この例のキー コンテナーの名前は `containervault` です。 この環境には、キー コンテナー アクションを使用して、キー コンテナーのシークレットが 2 つ追加されています (`containerPassword` および `containerUsername`)。 

これらのキー コンテナーの値を、その後 docker login タスク内から `steps.myGetSecretAction.outputs` というプレフィックスで参照しています。 

```yaml
name: Example key vault flow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - uses: actions/checkout@v2
    - uses: Azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - uses: Azure/get-keyvault-secrets@v1
      with: 
        keyvault: "containervault"
        secrets: 'containerPassword, containerUsername'
      id: myGetSecretAction
    - uses: azure/docker-login@v1
      with:
        login-server: myregistry.azurecr.io
        username: ${{ steps.myGetSecretAction.outputs.containerUsername }}
        password: ${{ steps.myGetSecretAction.outputs.containerPassword }}
    - run: |
        docker build . -t myregistry.azurecr.io/myapp:${{ github.sha }}
        docker push myregistry.azurecr.io/myapp:${{ github.sha }}     
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'myregistry.azurecr.io/myapp:${{ github.sha }}'
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure アプリ、GitHub リポジトリ、キー コンテナーが不要になったら、キー コンテナー、GitHub リポジトリ、アプリのリソース グループを削除して、デプロイしたリソースをクリーンアップしてください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Key Vault の詳細を確認する](../general/overview.md)