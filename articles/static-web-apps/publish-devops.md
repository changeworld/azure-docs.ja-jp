---
title: 'チュートリアル: Azure DevOps を使用して Azure Static Web Apps を発行する'
description: Azure DevOps を使用して Azure Static Web Apps を発行する方法について説明します。
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 08/17/2021
ms.author: apedward
ms.openlocfilehash: bc1e6c5ad5423f48eed50c65237b0d42a4e61d7a
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129236220"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>チュートリアル: Azure DevOps を使用して Azure Static Web Apps を発行する

この記事では、[Azure DevOps](https://dev.azure.com/) を使用して [Azure Static Web Apps](./overview.md) にデプロイする方法について説明します。

このチュートリアルで学習する内容は次のとおりです。

- Azure Static Web Apps サイトをセットアップする
- 静的 Web アプリをビルドして発行するための Azure パイプラインを作成する

## <a name="prerequisites"></a>前提条件

- **アクティブな Azure アカウント:** お持ちでない場合は、[無料でアカウントを作成](https://azure.microsoft.com/free/)できます。
- **Azure DevOps プロジェクト:** お持ちでない場合は、[無料でプロジェクトを作成](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/)できます。
  - Azure DevOps には、**Azure Pipelines** が含まれます。 Azure Pipelines の作業の開始に支援が必要な場合は、「[最初のパイプラインの作成](/azure/devops/pipelines/create-first-pipeline?preserve-view=true&view=azure-devops)」を参照してください。
  - 現在、静的な Web アプリ パイプライン タスクは **Linux** マシンでのみ機能します。 以下に記載されているパイプラインを実行する場合は、Linux VM で実行されていることを確認してください。

## <a name="create-a-static-web-app-in-an-azure-devops"></a>Azure DevOps に静的 Web アプリを作成する

  > [!NOTE]
  > リポジトリに既存のアプリがある場合は、このセクションをスキップして次のセクションにお進みください。

1. Azure Repos でリポジトリに移動します。

1. **[インポート]** を選択して、サンプル アプリケーションのインポートを開始します。
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps リポジトリ":::

1. **[クローン URL]** に「`https://github.com/staticwebdev/vanilla-api.git`」と入力します。

1. **[インポート]** を選択します。

## <a name="create-a-static-web-app"></a>静的 Web アプリを作成する

1. [Azure Portal](https://portal.azure.com) に移動します。

1. **[リソースの作成]** を選択します。

1. **Static Web Apps** を検索します。

1. **Static Web Apps** を選択します。

1. **［作成］** を選択します

1. 次の値を使用して、新しい静的 Web アプリを作成します。

    :::image type="content" source="media/publish-devops/azure-portal-static-web-apps-devops.png" alt-text="[デプロイの詳細] - [その他]":::

    | 設定 | 値 |
    |---|---|
    | サブスクリプション | Azure サブスクリプション名。 |
    | リソース グループ | 既存のグループ名を選択するか、新規作成します。 |
    | Name | 「**myDevOpsApp**」と入力します。 |
    | ホスティング プランの種類 | **[無料]** を選択します。 |
    | リージョン | 最も近いリージョンを選択します。 |
    | source | **[その他]** を選択します。 |

1. **[確認と作成]** を選択します

1. **［作成］** を選択します

1. デプロイが正常に完了したら、 **[リソースに移動]** を選択します。

1. **[Manage deployment token]\(デプロイトークンの管理\)** を選択します。

1. **デプロイ トークン** をコピーし、デプロイ トークンの値を他の画面で使用できるようテキスト エディターに貼り付けます。

    > [!NOTE]
    > 以降の手順でコピーして貼り付ける値が他にもあるため、ひとまず、この値は保存しておいてください。

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="デプロイ トークン":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Azure DevOps でパイプライン タスクを作成する

1. 先ほど作成した Azure Repos リポジトリに移動します。

2. **[ビルドのセットアップ]** を選択します。

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="ビルド パイプライン":::

3. *[パイプラインの構成]* セクションで、 **[スタート パイプライン]** を選択します。

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="パイプラインの構成":::

4. 次の YAML をコピーし、パイプラインで生成された構成をこのコードに置き換えます。

    ```yaml
    trigger:
      - main

    pool:
      vmImage: ubuntu-latest

    steps:
      - checkout: self
        submodules: true
      - task: AzureStaticWebApp@0
        inputs:
          app_location: '/src'
          api_location: 'api'
          output_location: '/src'
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > サンプル アプリを使用していない場合は、実際のアプリケーションの値に合わせて、`app_location`、`api_location`、`output_location` の各値を変更する必要があります。

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    `azure_static_web_apps_api_token` の値は自己管理となるため、手動で構成します。

5. **[変数]** を選択します。

6. **[New variable]** を選択します。

7. この変数に **deployment_token** という名前を付けます (ワークフローでの名前と一致させます)。

8. 先ほどテキスト エディターに貼り付けたデプロイ トークンをコピーします。

9. _[値]_ ボックスにデプロイ トークンを貼り付けます。

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="変数トークン":::

10. **[この値を誰にも教えないようにします]** をオンにします。

11. **[OK]** を選択します。

12. **[保存]** を選択して、パイプラインの YAML に戻ります。

13. **[保存および実行]** を選択して _[保存および実行]_ ダイアログを開きます。

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="パイプライン":::

14. **[保存および実行]** を選択してパイプラインを実行します。

15. デプロイが成功したら、デプロイ構成へのリンクが記載されている、Azure Static Web Apps の **[概要]** に移動します。 _[Source]\(ソース\)_ のリンク先が、Azure DevOps リポジトリのブランチと場所になっていることに注目してください。

16. **[URL]** を選択すると、新しくデプロイした Web サイトが表示されます。

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="配置場所":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. このチュートリアルで使用したリソース グループ名を選択します。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Static Web Apps を構成する](./configuration.md)
