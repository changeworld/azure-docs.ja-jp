---
title: GitHub Actions を使用したBicep ファイルのデプロイ
description: GitHub Actions を使用して Bicep ファイルをデプロイする方法を説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.custom: github-actions-azure
ms.openlocfilehash: 808c196ba5ae58e37889ef4b23da64be0eea4d81
ms.sourcegitcommit: b59e0afdd98204d11b7f9b6a3e55f5a85d8afdec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114371448"
---
# <a name="deploy-bicep-files-by-using-github-actions"></a>GitHub Actions を使用したBicep ファイルのデプロイ

[GitHub Actions](https://docs.github.com/en/actions) は GitHub の一連の機能であり、コードを格納するのと同じ場所でソフトウェア開発ワークフローを自動化したり、 pull request や問題に対して共同作業を行ったりするために使用します。

[Azure Resource Manager テンプレートのデプロイ アクション](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template)を使って、Bicep ファイルの Azure へのデプロイを自動化します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- GitHub アカウント。 お持ちでない場合は、[無料](https://github.com/join)でサインアップしてください。

  - Bicep ファイルとワークフロー ファイルを保存するための GitHub リポジトリ。 リポジトリを作成するには、[新しいリポジトリの作成](https://docs.github.com/github/creating-cloning-and-archiving-repositories/creating-a-new-repository)に関するページをご覧ください。

## <a name="workflow-file-overview"></a>ワークフロー ファイルの概要

ワークフローは、お使いのリポジトリの `/.github/workflows/` パスの YAML (.yml) ファイルに定義されます。 この定義には、ワークフローを構成するさまざまな手順とパラメーターが含まれます。

このファイルには 2 つのセクションがあります。

|Section  |タスク  |
|---------|---------|
|**認証** | 1.サービス プリンシパルを定義します。 <br /> 2.GitHub シークレットを作成します。 |
|**デプロイする** | 1. Bicep ファイルをデプロイします。 |

## <a name="generate-deployment-credentials"></a>デプロイ資格情報を生成する

[サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)は、[Azure CLI](/cli/azure/) で [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) コマンドを使用して作成できます。 このコマンドは、Azure portal で [Azure Cloud Shell](https://shell.azure.com/) を使用するか、 **[試してみる]** ボタンを選択して実行します。

リソース グループがまだない場合は、リソース グループを作成します。

```azurecli-interactive
    az group create -n {MyResourceGroup} -l {location}
```

`myApp` のプレースホルダーはアプリケーションの名前に置き換えます。

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

上記の例で、プレースホルダーを実際のサブスクリプション ID とリソース グループ名に置き換えます。 これにより、以下のようなご自分の App Service アプリにアクセスするためのロールの割り当て資格情報を含む JSON オブジェクトが出力されます。 この JSON オブジェクトを後のためにコピーします。 必要なのは、`clientId`、`clientSecret`、`subscriptionId`、および `tenantId` の値を持つセクションだけです。

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
> 常に最小限のアクセス権を付与することをお勧めします。 前の例のスコープは、リソース グループに限定されています。

## <a name="configure-the-github-secrets"></a>GitHub シークレットを構成する

Azure の資格情報、リソース グループ、およびサブスクリプションのシークレットを作成する必要があります。

1. [GitHub](https://github.com/) でリポジトリを参照します。

1. **[Settings]\(設定\) > [Secrets]\(シークレット\) > [New secret]\(新しいシークレット\)** の順に選択します。

1. Azure CLI コマンドからの JSON 出力全体をシークレットの値フィールドに貼り付けます。 シークレットに `AZURE_CREDENTIALS` と名前を付けます。

1. `AZURE_RG` という名前の別のシークレットを作成します。 リソース グループの名前をシークレットの値フィールドに追加します (例: `myResourceGroup`)。

1. `AZURE_SUBSCRIPTION` という名前の追加のシークレットを作成します。 シークレットの値フィールドにサブスクリプション ID を追加します (例: `90fd3f9d-4c61-432d-99ba-1273f236afa2`)。

## <a name="add-a-bicep-file"></a>Bicep ファイルを追加する

GitHub リポジトリに Bicep ファイルを追加します。 次の Bicep ファイルでは、ストレージ アカウントが作成されます。

```url
https://raw.githubusercontent.com/mumian/azure-docs-json-samples/master/get-started-with-templates/add-variable/azuredeploy.bicep
```

Bicep ファイルは **storagePrefix** と呼ばれる 1 つのパラメーターを 3 文字から 11 文字で受け取ります。

ファイルは、リポジトリ内のどこに置いてもかまいません。 次のセクションのワークフロー サンプルでは、Bicep ファイル名が **azuredeploy.bicep** であり、リポジトリのルートに保存されていることを想定しています。

## <a name="create-workflow"></a>ワークフローを作成する

ワークフロー ファイルは、リポジトリのルートにある **.github/workflows** フォルダーに保存する必要があります。 ワークフロー ファイルの拡張子には、 **.yml** または **.yaml** を指定できます。

1. GitHub リポジトリの上部のメニューで、 **[Actions]\(アクション\)** を選択します。
1. **[New workflow]\(新しいワークフロー\)** を選択します。
1. **[Set up a workflow yourself]\(ワークフローを自分でセットアップする\)** を選択します。
1. **main.yml** 以外の別の名前を使用する場合は、ワークフロー ファイルの名前を変更します。 例: **deployBicepFile.yml**。
1. yml ファイルの内容を以下に置き換えます。

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@main

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

          # Deploy Bicep file
        - name: deploy
          uses: azure/arm-deploy@v1
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./azuredeploy.bicep
            parameters: storagePrefix=mystore
            failOnStdErr: false
    ```

    **mystore** を独自のストレージ アカウント名プレフィックスに置き換えます。

    > [!NOTE]
    > 代わりに、ARM デプロイ アクション (例: `.azuredeploy.parameters.json`) に JSON 形式のパラメーター ファイルを指定できます。

    ワークフロー ファイルの最初のセクションには次のものが含まれます。

    - **name**:ワークフローの名前。
    - **on**: ワークフローをトリガーする GitHub イベントの名前。 メイン ブランチでプッシュ イベントが発生し、指定された 2 つのファイルの少なくとも 1 つが変更されると、ワークフローがトリガーされます。 この 2 つのファイルは、ワークフロー ファイルと Bicep ファイルです。

1. **[Start commit]\(コミットの開始\)** を選択します。
1. **[Commit directly to the main branch]\(メイン ブランチに直接コミットする\)** を選択します。
1. **[Commit new file]\(新しいファイルをコミットする\)** (または **[Commit changes]\(変更をコミットする\)** ) を選択します。

ワークフローは、ワークフロー ファイルまたは Bicep ファイルの更新によってトリガーされるように構成されているため、変更をコミットするとすぐにワークフローが開始されます。

## <a name="check-workflow-status"></a>ワークフローの状態を確認する

1. **[Actions]\(アクション\)** タブを選択します。**Create deployStorageAccount.yml** ワークフローが表示されます。 ワークフローの実行には 1 から 2 分かかります。
1. ワークフローを選択して開きます。
1. メニューから **[Run ARM deploy]\(ARM デプロイを実行\)** を選択して、デプロイを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループとリポジトリが不要になったら、リソース グループと GitHub リポジトリを削除して、デプロイしたリソースをクリーンアップします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Learn モジュール: GitHub Actions を使用して ARM テンプレートのデプロイを自動化する](/learn/modules/deploy-templates-command-line-github-actions/)
