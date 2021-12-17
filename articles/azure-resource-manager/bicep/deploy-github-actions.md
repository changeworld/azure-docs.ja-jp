---
title: GitHub Actions を使用したBicep ファイルのデプロイ
description: このクイックスタートでは、GitHub Actions を使用して Bicep ファイルをデプロイする方法について説明します。
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 11/16/2021
ms.custom: github-actions-azure
ms.openlocfilehash: 548688e2359043485df7b60eb51cdaea243c3540
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716870"
---
# <a name="quickstart-deploy-bicep-files-by-using-github-actions"></a>クイックスタート: GitHub Actions を使用した Bicep ファイルのデプロイ

[GitHub Actions](https://docs.github.com/en/actions) は、ソフトウェア開発ワークフローを自動化する一連の GitHub 機能です。

このクイックスタートでは、[Azure Resource Manager デプロイの GitHub アクション](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template)を使って、Bicep ファイルの Azure へのデプロイを自動化します。

GitHub Actions と Bicep ファイルの概要を簡単に示します。 GitHub Actions とプロジェクトの設定に関する詳細な手順が必要な場合は、「[ラーニングパス: Bicep と GitHub Actions を使用して Azure リソースをデプロイする](/learn/paths/bicep-github-actions)」を参照してください。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- GitHub アカウント。 お持ちでない場合は、[無料](https://github.com/join)でサインアップしてください。
- Bicep ファイルとワークフロー ファイルを保存するための GitHub リポジトリ。 リポジトリを作成するには、[新しいリポジトリの作成](https://docs.github.com/github/creating-cloning-and-archiving-repositories/creating-a-new-repository)に関するページをご覧ください。

## <a name="create-resource-group"></a>リソース グループの作成

リソース グループを作成する。 このクイックスタートの後半で、このリソース グループに Bicep ファイルをデプロイします。

```azurecli-interactive
az group create -n exampleRG -l westus
```

## <a name="generate-deployment-credentials"></a>デプロイ資格情報を生成する

GitHub アクションは ID の下で実行されます。 サービス プリンシパルを作成するには、[az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) コマンドを使用して、ID の[サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)を作成します。

`myApp` のプレースホルダーはアプリケーションの名前に置き換えます。 `{subscription-id}` は、サブスクリプション ID で置き換えてください。

```azurecli-interactive
az ad sp create-for-rbac --name myApp --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/exampleRG --sdk-auth
```

> [!IMPORTANT]
> 前の例のスコープは、リソース グループに限定されています。 最低限必要なアクセス権を付与することをお勧めします。

これにより、以下のようなご自分の App Service アプリにアクセスするためのロールの割り当て資格情報を含む JSON オブジェクトが出力されます。 この JSON オブジェクトを後のためにコピーします。 必要なのは、`clientId`、`clientSecret`、`subscriptionId`、`tenantId` の値を持つセクションだけです。

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secrets"></a>GitHub シークレットを構成する

Azure の資格情報、リソース グループ、およびサブスクリプションのシークレットを作成します。

1. [GitHub](https://github.com/) で、自分のリポジトリに移動します。

1. **[Settings]\(設定\) > [Secrets]\(シークレット\) > [New secret]\(新しいシークレット\)** の順に選択します。

1. Azure CLI コマンドからの JSON 出力全体をシークレットの値フィールドに貼り付けます。 シークレットに `AZURE_CREDENTIALS` という名前を付けます。

1. `AZURE_RG` という名前の別のシークレットを作成します。 リソース グループの名前をシークレットの値フィールドに追加します (`exampleRG`)。

1. `AZURE_SUBSCRIPTION` という名前の別のシークレットを作成します。 シークレットの値フィールドにサブスクリプション ID を追加します (例: `90fd3f9d-4c61-432d-99ba-1273f236afa2`)。

## <a name="add-a-bicep-file"></a>Bicep ファイルを追加する

GitHub リポジトリに Bicep ファイルを追加します。 次の Bicep ファイルでは、ストレージ アカウントが作成されます。

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/create-storage-account/azuredeploy.bicep" :::

Bicep ファイルでは **storagePrefix** と呼ばれる 1 つのパラメーターが 3 文字から 11 文字で必要です。

ファイルは、リポジトリ内のどこに置いてもかまいません。 次のセクションのワークフロー サンプルでは、Bicep ファイル名が **main.bicep** であり、リポジトリのルートに保存されていることを想定しています。

## <a name="create-workflow"></a>ワークフローを作成する

ワークフローでは、トリガーされた場合に実行するステップを定義します。 これは、お使いのリポジトリの **/.github/workflows/** パスの YAML (.yml) ファイルです。 ワークフロー ファイルの拡張子には、 **.yml** または **.yaml** を指定できます。

ワークフローを作成するには、次の手順を実行します。

1. GitHub リポジトリの上部のメニューで、 **[Actions]\(アクション\)** を選択します。
1. **[New workflow]\(新しいワークフロー\)** を選択します。
1. **[Set up a workflow yourself]\(ワークフローを自分でセットアップする\)** を選択します。
1. **main.yml** 以外の別の名前を使用する場合は、ワークフロー ファイルの名前を変更します。 例: **deployBicepFile.yml**。
1. .yml ファイルの内容を次のコードに置き換えます。

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
            template: ./main.bicep
            parameters: storagePrefix=mystore
            failOnStdErr: false
    ```

    `mystore` を独自のストレージ アカウント名プレフィックスに置き換えます。

    > [!NOTE]
    > 代わりに、ARM デプロイ アクション (例: `.azuredeploy.parameters.json`) に JSON 形式のパラメーター ファイルを指定できます。

    ワークフロー ファイルの最初のセクションには次のものが含まれます。

    - **name**:ワークフローの名前。
    - **on**: ワークフローをトリガーする GitHub イベントの名前。 ワークフローは、メイン ブランチにプッシュ イベントがある場合にトリガーされます。

1. **[Start commit]\(コミットの開始\)** を選択します。
1. **[Commit directly to the main branch]\(メイン ブランチに直接コミットする\)** を選択します。
1. **[Commit new file]\(新しいファイルをコミットする\)** (または **[Commit changes]\(変更をコミットする\)** ) を選択します。

ワークフロー ファイルまたは Bicep ファイルのいずれかを更新すると、ワークフローがトリガーされます。 変更をコミットすると、ワークフローがすぐに開始されます。

## <a name="check-workflow-status"></a>ワークフローの状態を確認する

1. **[アクション]** タブを選択します。**Create deployStorageAccount.yml** ワークフローが表示されます。 ワークフローの実行には 1 から 2 分かかります。
1. ワークフローを選択して開きます。
1. メニューから **[Run ARM deploy]\(ARM デプロイを実行\)** を選択して、デプロイを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループとリポジトリが不要になったら、リソース グループと GitHub リポジトリを削除して、デプロイしたリソースをクリーンアップします。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name exampleRG
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name exampleRG
```

---

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Bicep ファイルの構造と構文](file.md)
