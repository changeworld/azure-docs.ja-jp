---
title: チュートリアル:Jekyll サイトを Azure Static Web Apps に公開する
description: Azure Static Web Apps に Jekyll アプリケーションをデプロイする方法について説明します。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 04/28/2021
ms.author: cshoe
ms.openlocfilehash: 0f572d49867fe9149416664a405309253dd01af2
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108202941"
---
# <a name="tutorial-publish-a-jekyll-site-to-azure-static-web-apps-preview"></a>チュートリアル:Jekyll サイトを Azure Static Web Apps プレビューに公開する

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
   git remote add origin https://github.com/<YOUR_USER_NAME>/jekyll-static-app
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

1. [Azure Portal](https://portal.azure.com) に移動します。

1. **[リソースの作成]** をクリックします。

1. **Static Web Apps** を検索します。

1. **[Static Web Apps (プレビュー)]** をクリックします。

1. **Create** をクリックしてください。

1. **[サブスクリプション]** で、リストされているサブスクリプションを受け入れるか、ドロップダウン リストから新しいサブスクリプションを選択します。

1. _[リソース グループ]_ で、 **[新規]** を選択します。 _[新しいリソース グループ名]_ に「**jekyll-static-app**」と入力し、 **[OK]** を選択します。

1. 次に、 _[名前]_ ボックスに対象のアプリの名前を入力します。 有効な文字には、`a-z`、`A-Z`、`0-9`、および `-` があります。

1. _[リージョン]_ で、近くの使用可能なリージョンを選択します。

1. _[SKU]_ で、 **[Free]** を選択します。

1. _[デプロイの詳細]_ で、 _[ソース]_ に **[GitHub]** を選択します。

1. **[GitHub でサインイン]** ボタンをクリックします。

1. リポジトリを作成した **[組織]** を選択します。

1. _[リポジトリ]_ として **jekyll-static-app** を選択します。

1. _[ブランチ]_ では、**main** を選択します。

### <a name="build"></a>Build

次に、ビルド プロセスがアプリのビルドに使用する構成設定を追加します。 次の設定では、GitHub アクション ワークフロー ファイルが構成されます。

1. _[ビルドのプリセット]_ で **[カスタム]** を選択します。

1. _[App location]\(アプリの場所\)_ を「 **/** 」に設定します。

1. _[出力場所]_ を **[_site]** に設定します。

   この時点では API をデプロイしていないため _[API location]\(アプリの場所\)_ の値は必要ありません。

   :::image type="content" source="./media/publish-jekyll/github-actions-inputs.png" alt-text="GitHub Actions の入力":::

### <a name="review-and-create"></a>[Review and create] (確認および作成)

1. **[確認および作成]** ボタンをクリックして、詳細がすべて正しいことを確認します。

1. **[作成]** をクリックして、Azure Static Web Apps の作成を開始し、デプロイのための GitHub アクションをプロビジョニングします。

1. GitHub アクションが完了するのを待ちます。

1. Azure portal で新しく作成された Azure Static Web Apps リソースの _[概要]_ ウィンドウに移動し、 _[URL]_ リンクをクリックして、デプロイしたアプリケーションを開きます。

   :::image type="content" source="./media/publish-jekyll/deployed-app.png" alt-text="デプロイされたアプリケーション":::

#### <a name="custom-jekyll-settings"></a>カスタム Jekyll の設定

静的 Web アプリを生成すると、アプリケーションの発行構成設定を含む[ワークフロー ファイル](./github-actions-workflow.md)が生成されます。

`JEKYLL_ENV` などの環境変数を構成するには、ワークフローの Azure Static Web Apps GitHub アクションに `env` セクションを追加します。

```yaml
- name: Build And Deploy
   id: builddeploy
   uses: Azure/static-web-apps-deploy@v0.0.1-preview
   with:
      azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
      repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
      action: "upload"
      ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
      # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
      app_location: "/" # App source code path
      api_location: "" # Api source code path - optional
      output_location: "_site_" # Built app content directory - optional
      ###### End of Repository/Build Configurations ######
   env:
      JEKYLL_ENV: production
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム ドメインの追加](custom-domain.md)
