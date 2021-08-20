---
title: 'チュートリアル: Azure DevOps を使用して Azure Static Web Apps を発行する'
description: Azure DevOps を使用して Azure Static Web Apps を発行する方法について説明します。
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: apedward
ms.openlocfilehash: 17a41bd64f1bba4a5ae4d6d9d497c03afae037e7
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114444228"
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

1. _[デプロイの詳細]_ で、 **[その他]** が選択されていることを確認します。 これで、Azure Repos 内のコードを使用できるようになります。

    :::image type="content" source="media/publish-devops/create-resource.png" alt-text="[デプロイの詳細] - [その他]":::

1. デプロイが成功したら、新しい静的 Web アプリ リソースに移動します。

1. **[Manage deployment token]\(デプロイトークンの管理\)** を選択します。

1. **デプロイ トークン** をコピーし、他の画面で使用できるようテキスト エディターに貼り付けます。

    > [!NOTE]
    > 以降の手順でコピーして貼り付ける値が他にもあるため、ひとまず、この値は保存しておいてください。

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="デプロイ トークン":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Azure DevOps でパイプライン タスクを作成する

1. 先ほど作成した Azure Repos リポジトリに移動します。

1. **[ビルドのセットアップ]** を選択します。

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="ビルド パイプライン":::

1. *[パイプラインの構成]* セクションで、 **[スタート パイプライン]** を選択します。

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="パイプラインの構成":::

1. 次の YAML をコピーしてパイプラインに貼り付けます。

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
          app_location: '/'
          api_location: 'api'
          output_location: ''
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > サンプル アプリを使用していない場合は、実際のアプリケーションの値に合わせて、`app_location`、`api_location`、`output_location` の各値を変更する必要があります。

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    `azure_static_web_apps_api_token` の値は自己管理となるため、手動で構成します。

2. **[変数]** を選択します。

3. 新しい変数を作成します。

4. この変数に **deployment_token** という名前を付けます (ワークフローでの名前と一致させます)。

5. 先ほどテキスト エディターに貼り付けたデプロイ トークンをコピーします。

6. _[値]_ ボックスにデプロイ トークンを貼り付けます。

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="変数トークン":::

7. **[この値を誰にも教えないようにします]** をオンにします。

8. **[OK]** を選択します。

9. **[保存]** を選択して、パイプラインの YAML に戻ります。

10. **[保存および実行]** を選択して _[保存および実行]_ ダイアログを開きます。

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="パイプライン":::

11. **[保存および実行]** を選択してパイプラインを実行します。

12. デプロイが成功したら、デプロイ構成へのリンクが記載されている、Azure Static Web Apps の **[概要]** に移動します。 _[Source]\(ソース\)_ のリンク先が、Azure DevOps リポジトリのブランチと場所になっていることに注目してください。

13. **[URL]** を選択すると、新しくデプロイした Web サイトが表示されます。

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="配置場所":::

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Static Web Apps を構成する](./configuration.md)
