---
title: チュートリアル:Hugo サイトを Azure Static Web Apps に発行する
description: Azure Static Web Apps に Hugo アプリケーションをデプロイする方法について説明します。
services: static-web-apps
author: aaronpowell
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: aapowell
ms.openlocfilehash: 243caef4a1f2cd1d543e7bbb9f3bc127d703296e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124774806"
---
# <a name="tutorial-publish-a-hugo-site-to-azure-static-web-apps"></a>チュートリアル:Hugo サイトを Azure Static Web Apps に発行する

この記事では、[Hugo](https://gohugo.io/) Web アプリケーションを作成して [Azure Static Web Apps](overview.md) にデプロイする方法を説明します。 最終結果は、アプリのビルドと発行の方法を制御できる GitHub Actions が関連付けられた新しい Azure Static Web App になります。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> - Hugo アプリを作成する
> - Azure Static Web Apps をセットアップする
> - Hugo アプリを Azure にデプロイする

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 アカウントがない場合は、[無料でアカウントを作成する](https://azure.microsoft.com/free/)ことができます。
- GitHub アカウント。 アカウントがない場合は、[無料でアカウントを作成する](https://github.com/join)ことができます。

## <a name="create-a-hugo-app"></a>Hugo アプリを作成する

Hugo コマンド ライン インターフェイス (CLI) を使用して Hugo アプリを作成します。

1. お使いの OS での Hugo の[インストール ガイド](https://gohugo.io/getting-started/installing/)に従ってください。

1. ターミナルを開きます

1. Hugo CLI を実行して新しいアプリを作成します。

   ```bash
   hugo new site static-app
   ```

1. 新しく作成されたアプリに移動します。

   ```bash
   cd static-app
   ```

1. Git リポジトリを初期化します。

   ```bash
   git init
   ```

1. ブランチ名が `main` であることを確認します。

   ```bash
   git branch -M main
   ```

1. 次に、git サブモジュールとしてテーマをインストールしてから、Hugo 構成ファイルでそれを指定することにより、サイトにテーマを追加します。

   ```bash
   git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke
   echo 'theme = "ananke"' >> config.toml
   ```

1. 変更をコミットします。

   ```bash
   git add -A
   git commit -m "initial commit"
   ```

## <a name="push-your-application-to-github"></a>アプリケーションを GitHub にプッシュする

Azure Static Web Apps に接続するには、GitHub のリポジトリが必要です。 次の手順では、サイトのリポジトリの作成方法を説明します。

1. [https://github.com/new](https://github.com/new) で **hugo-static-app** という名前で空の GitHub リポジトリを作成します (README は作成しないでください)。

1. GitHub リポジトリをリモートとしてローカル リポジトリに追加します。 次のコマンドの `<YOUR_USER_NAME>` プレースホルダーの代わりに、GitHub のユーザー名を追加してください。

   ```bash
   git remote add origin https://github.com/<YOUR_USER_NAME>/hugo-static-app
   ```

1. ローカル リポジトリを GitHub にプッシュします。

   ```bash
   git push --set-upstream origin main
   ```

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
    | _リソース グループ_ | **my-hugo-group**  |
    | _名前_ | **hugo-static-app** |
    | _[プランの種類]_ | **Free** |
    | _Azure Functions API のリージョンとステージング環境_ | 最も近いリージョンを選択します。 |
    | _ソース_ | **GitHub** |

1. **[GitHub でサインイン]** を選択し、GitHub で認証します。

1. 次の GitHub 値を入力します。

    | プロパティ | 値 |
    | --- | --- |
    | _組織_ | ご自分の希望する GitHub 組織を選択します。 |
    | _リポジトリ_ | **hugo-static-app** を選択します。 |
    | _ブランチ_ | **[main]\(メイン\)** を選択します。 |

1. _[Build Details]\(ビルドの詳細\)_ セクションで、 _[Build Presets]\(ビルドのプリセット\)_ ドロップダウンから **[Hugo]** を選択し、既定値をそのままにします。

### <a name="review-and-create"></a>[Review and create] (確認および作成)

1. **[確認および作成]** ボタンを選択して、詳細がすべて正しいことを確認します。

1. **[作成]** を選択して、App Service Static Web App の作成を開始し、デプロイのための GitHub アクションをプロビジョニングします。

1. デプロイが完了したら、 **[リソースに移動]** をクリックします。

1. リソース画面で、 _[URL]_ リンクをクリックして、デプロイしたアプリケーションを開きます。 GitHub アクションが完了するまで 1 - 2 分かかることがあります。

   :::image type="content" source="./media/publish-hugo/deployed-app.png" alt-text="デプロイされたアプリケーション":::

#### <a name="custom-hugo-version"></a>カスタム Hugo バージョン

静的 Web アプリを生成すると、アプリケーションの発行構成設定を含む[ワークフロー ファイル](./build-configuration.md)が生成されます。 `env` セクション内に `HUGO_VERSION` の値を指定することにより、ワークフロー ファイル内に特定の Hugo バージョンを指定できます。 次の構成例は、Hugo を特定のバージョンに設定する方法を示しています。

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
          # For more information regarding Static Web App workflow configurations, please visit: https://aka.ms/swaworkflowconfig
          app_location: "/" # App source code path
          api_location: "api" # Api source code path - optional
          output_location: "public" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######
        env:
          HUGO_VERSION: 0.58.0
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cleanup-resource](../../includes/static-web-apps-cleanup-resource.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム ドメインの追加](custom-domain.md)
