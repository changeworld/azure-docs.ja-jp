---
title: チュートリアル:Jekyll サイトを Azure Static Web Apps に公開する
description: Azure Static Web Apps に Jekyll アプリケーションをデプロイする方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: cshoe
ms.openlocfilehash: fa97100e670e1d96bdd33c362b2e133d78d8dae7
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2021
ms.locfileid: "129155441"
---
# <a name="tutorial-publish-a-jekyll-site-to-azure-static-web-apps"></a>チュートリアル:Jekyll サイトを Azure Static Web Apps に公開する

この記事では、[Jekyll](https://jekyllrb.com/) Web アプリケーションを作成して [Azure Static Web Apps](overview.md) にデプロイする方法を説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> - Jekyll Web サイトを作成する
> - Azure Static Web Apps リソースのセットアップ
> - Jekyll アプリを Azure にデプロイする

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

- [Jekyll](https://jekyllrb.com/docs/installation/) のインストール
  - 必要に応じて Linux 用 Windows サブシステムを使用し、Ubuntu の手順に従うこともできます。
- アクティブなサブスクリプションが含まれる Azure アカウント。 アカウントがない場合は、[無料でアカウントを作成する](https://azure.microsoft.com/free/)ことができます。
- GitHub アカウント。 アカウントがない場合は、[無料でアカウントを作成する](https://github.com/join)ことができます。

## <a name="create-jekyll-app"></a>Jekyll アプリを作成する

Jekyll コマンド ライン インターフェイス (CLI) を使用して Jekyll アプリを作成します。

1. ターミナルから Jekyll CLI を実行し、新しいアプリを作成します。

   ```bash
   jekyll new static-app
   ```

1. 新しく作成されたアプリに移動します。

   ```bash
   cd static-app
   ```

1. 新しい Git リポジトリを初期化します。

   ```bash
    git init
   ```

1. 変更をコミットします。

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>アプリケーションを GitHub にプッシュする

Azure Static Web Apps では、Web サイトの公開に GitHub を使用します。 次の手順は、GitHub リポジトリの作成方法を説明したものです。

1. [https://github.com/new](https://github.com/new) で **jekyll-azure-static** という名前で空の GitHub リポジトリを作成します (README は作成しないでください)。

1. GitHub リポジトリをリモートとしてローカル リポジトリに追加します。 次のコマンドの `<YOUR_USER_NAME>` プレースホルダーの代わりに、GitHub のユーザー名を追加してください。

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/jekyll-azure-static
   ```

1. ローカル リポジトリを GitHub にプッシュします。

   ```bash
   git push --set-upstream origin main
   ```

   > [!NOTE]
   > Git ブランチは、`main` とは異なる名前になっている可能性があります。 このコマンドの `main` を正しい値で置き換えてください。

## <a name="deploy-your-web-app"></a>Web アプリのデプロイ

次の手順では、新しい静的サイト アプリを作成し、運用環境にデプロイする方法について説明します。

### <a name="create-the-application"></a>アプリケーションを作成する

1. [Azure Portal](https://portal.azure.com) に移動します
1. **[リソースの作成]** を選択します
1. **[Static Web Apps]** を探します
1. **[Static Web Apps]** を選択します。
1. **[作成]**
1. _[基本]_ タブで、次の値を入力します。

    | プロパティ | 値 |
    | --- | --- |
    | _サブスクリプション_ | Azure サブスクリプション名。 |
    | _リソース グループ_ | **jekyll-static-app**  |
    | _名前_ | **jekyll-static-app** |
    | _[プランの種類]_ | **Free** |
    | _Azure Functions API のリージョンとステージング環境_ | 最も近いリージョンを選択します。 |
    | _ソース_ | **GitHub** |

1. **[GitHub でサインイン]** を選択し、GitHub で認証します。

1. 次の GitHub 値を入力します。

    | プロパティ | 値 |
    | --- | --- |
    | _組織_ | ご自分の希望する GitHub 組織を選択します。 |
    | _リポジトリ_ | **[jekyll-static-app]** を選択します。 |
    | _ブランチ_ | **[main]\(メイン\)** を選択します。 |

1. _[Build Details]_ セクションで、 _[Build Presets]_ ドロップダウンから **[Custom]** を選択し、既定値をそのままにします。

1. _[App location]\(アプリの場所\)_ ボックスに「 **./** 」と入力します。

1. _[Api location]\(API の場所\)_ ボックスはからのままにします。

1. _[Output location]\(出力の場所\)_ ボックスに、「 **_site**」と入力します。

### <a name="review-and-create"></a>[Review and create] (確認および作成)

1. **[確認および作成]** ボタンを選択して、詳細がすべて正しいことを確認します。

1. **[作成]** を選択して、App Service Static Web App の作成を開始し、デプロイのための GitHub アクションをプロビジョニングします。

1. デプロイが完了したら、 **[リソースに移動]** をクリックします。

1. リソース画面で、 _[URL]_ リンクをクリックして、デプロイしたアプリケーションを開きます。 GitHub アクションが完了するまで 1 - 2 分かかることがあります。

   :::image type="content" source="./media/publish-jekyll/deployed-app.png" alt-text="デプロイされたアプリケーション":::

#### <a name="custom-jekyll-settings"></a>カスタム Jekyll の設定

静的 Web アプリを生成すると、アプリケーションの発行構成設定を含む[ワークフロー ファイル](./build-configuration.md)が生成されます。

`JEKYLL_ENV` などの環境変数を構成するには、ワークフローの Azure Static Web Apps GitHub アクションに `env` セクションを追加します。

```yaml
- name: Build And Deploy
   id: builddeploy
   uses: Azure/static-web-apps-deploy@v1
   with:
      azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
      repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
      action: "upload"
      ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
      # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
      app_location: "/" # App source code path
      api_location: "" # Api source code path - optional
      output_location: "_site" # Built app content directory - optional
      ###### End of Repository/Build Configurations ######
   env:
      JEKYLL_ENV: production
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム ドメインの追加](custom-domain.md)
