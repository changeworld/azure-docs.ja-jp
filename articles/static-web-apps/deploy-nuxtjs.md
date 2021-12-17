---
title: チュートリアル:サーバーでレンダリングされた Nuxt.js の Web サイトを Azure Static Web Apps にデプロイする
description: Nuxt.js の動的サイトを生成し、Azure Static Web Apps を使用してデプロイします。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 615bba10b410ec054227e1f2d45d2fdfde7b225d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748025"
---
# <a name="deploy-server-rendered-nuxtjs-websites-on-azure-static-web-apps"></a>サーバーでレンダリングされた Nuxt.js の Web サイトを Azure Static Web Apps にデプロイする

このチュートリアルでは、[Nuxt.js](https://nuxtjs.org) で生成された静的 Web サイトを [Azure Static Web Apps](overview.md) にデプロイする方法について説明します。 まず、Nuxt.js アプリを設定、構成、デプロイする方法を学習します。 このプロセスの間に、Nuxt.js を使用して静的ページを生成するときによく発生する一般的な課題に対処する方法についても学習します

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/)。
- GitHub アカウント。 [無料でアカウントを作成できます](https://github.com/join)。
- [Node.js](https://nodejs.org) がインストールされていること。

## <a name="set-up-a-nuxtjs-app"></a>Nuxt.js アプリを設定する

`create-nuxt-app` を使用して、新しい Nuxt.js プロジェクトを設定できます。 このチュートリアルでは、新しいプロジェクトではなく、既存のリポジトリをクローンすることで始めます。 このリポジトリは、動的 Nuxt.js アプリを静的サイトとしてデプロイする方法がわかるように設定されています。

1. お使いの GitHub アカウントに、テンプレート リポジトリから新しいリポジトリを作成します。
1. [http://github.com/staticwebdev/nuxtjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nuxtjs-starter/generate) に移動します
<<<<<<< HEAD
1. リポジトリに **nuxtjs-starter** という名前を設定します
=======
1. リポジトリに **nuxtjs-starter** という名前を付けます。
>>>>>>> repo_sync_working_branch
1. 次に、新しいリポジトリをお使いのコンピューターにクローンします。 <YOUR_GITHUB_ACCOUNT_NAME> は、必ず自分のアカウント名に置き換えてください。

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nuxtjs-starter
    ```

1. 新しくクローンされた Nuxt.js アプリに移動します。

   ```bash
   cd nuxtjs-starter
   ```

1. 依存関係をインストールします。

    ```bash
    npm install
    ```

1. 開発環境で Nuxt.js アプリを起動します。

    ```bash
    npm run dev
    ```

`http://localhost:3000` に移動してアプリを開きます。いつも使用しているブラウザーで、次の Web サイトが開かれます。

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-app.png" alt-text="Nuxt.js アプリを開始する":::

フレームワークまたはライブラリをクリックすると、選択した項目に関する詳細ページが表示されます。

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-details.png" alt-text="詳細ページ":::

## <a name="generate-a-static-website-from-nuxtjs-build"></a>Nuxt.js のビルドから静的 Web サイトを生成する

`npm run build` を使用して Nuxt.js のサイトをビルドすると、アプリは静的サイトではなく、従来の Web アプリとしてビルドされます。 静的サイトを生成するには、次のアプリケーション構成を使用します。

1. `nuxt generate` コマンドを使用して静的サイトのみが生成されるように、_package.json_ のビルド スクリプトを更新します。

    ```json
    "scripts": {
      "dev": "nuxt dev",
      "build": "nuxt generate"
    },
    ```

    このコマンドを追加すると、コミットをプッシュするたびに Static Web Apps によって `build` スクリプトが実行されるようになります。

1. 静的サイトを生成します。

    ```bash
    npm run build
    ```

    Nuxt.js によって静的サイトが生成され、作業ディレクトリのルートにある _dist_ フォルダーにコピーされます。

    > [!NOTE]
    > このフォルダーは、デプロイ時に CI/CD によって生成される必要があるため、 _.gitignore_ ファイルにリストされています。

## <a name="push-your-static-website-to-github"></a>静的 Web サイトを GitHub にプッシュする

Azure Static Web Apps により、GitHub リポジトリからアプリがデプロイされます。これは、指定されたブランチにプッシュされるすべてのコミットに対し、常に行われます。 次のコマンドを使用して、変更を GitHub に同期します。

1. 変更されたすべてのファイルをステージします。

    ```bash
    git add .
    ```

1. すべての変更をコミットします

    ```bash
    git commit -m "Update build config"
    ```

1. 変更を GitHub にプッシュします。

    ```bash
    git push origin main
    ```

## <a name="deploy-your-static-website"></a>静的 Web サイトをデプロイする

次の手順では、GitHub にプッシュしたアプリを Azure Static Web Apps にリンクする方法を示します。 Azure に移動すると、アプリケーションを運用環境にデプロイできます。

### <a name="create-an-azure-static-web-apps-resource"></a>Azure Static Web Apps リソースの作成

1. [Azure Portal](https://portal.azure.com) に移動します。
1. **[リソースの作成]** を選択します。
1. **Static Web Apps** を検索します。
1. **Static Web Apps** を選択します。
1. **［作成］** を選択します
1. _[基本]_ タブで、次の値を入力します。

    | プロパティ | 値 |
    | --- | --- |
    | _サブスクリプション_ | Azure サブスクリプション名。 |
    | _リソース グループ_ | **my-nuxtjs-group**  |
    | _名前_ | **my-nuxtjs-app** |
    | _[プランの種類]_ | **Free** |
    | _Azure Functions API のリージョンとステージング環境_ | 最も近いリージョンを選択します。 |
    | _ソース_ | **GitHub** |

1. **[GitHub でサインイン]** を選択し、GitHub で認証します。

1. 次の GitHub 値を入力します。

    | プロパティ | 値 |
    | --- | --- |
    | _組織_ | ご自分の希望する GitHub 組織を選択します。 |
    | _リポジトリ_ | 以前に選択したリポジトリを選択します。 |
    | _ブランチ_ | **[main]\(メイン\)** を選択します。 |

1. _[Build Details]\(ビルドの詳細\)_ セクションで、 _[Build Presets]\(ビルドのプリセット\)_ ドロップダウンから **[Custom]\(カスタム\)** を選択し、既定値をそのままにします。

1. _[App location]\(アプリの場所\)_ のボックスに「 **./** 」と入力します。
1. _[Api location]\(API の場所\)_ ボックスはからのままにします。
1. _[Output location]\(出力の場所\)_ ボックスに、「**out**」と入力します。

### <a name="review-and-create"></a>[Review and create] (確認および作成)

1. **[確認および作成]** ボタンを選択して、詳細がすべて正しいことを確認します。

1. **[作成]** を選択して、App Service Static Web App の作成を開始し、デプロイのための GitHub アクションをプロビジョニングします。

1. デプロイが完了したら、 **[リソースに移動]** をクリックします。

1. _[概要]_ ウィンドウで、 *[URL]* リンクをクリックして、デプロイしたアプリケーションを開きます。

Web サイトがすぐに読み込まれない場合は、バックグラウンドで GitHub Actions ワークフローがまだ実行されています。 ワークフローが完了したら、クリックしてブラウザーを最新の情報に更新し、Web アプリを表示できます。

リポジトリの Actions に移動することで、Actions ワークフローの状態を確認できます。

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nuxtjs-starter/actions
```

### <a name="sync-changes"></a>変更を同期する

アプリを作成すると、Azure Static Web Apps によってリポジトリに GitHub Actions ワークフロー ファイルが作成されます。 git 履歴が同期されるように、このファイルをローカル リポジトリに取り込む必要があります。

ターミナルに戻り、`git pull origin main` コマンドを実行します。

## <a name="configure-dynamic-routes"></a>動的なルートを構成する

新しくデプロイされたサイトに移動し、フレームワークまたはライブラリのロゴの 1 つをクリックします。 詳細ページは表示されず、代わりに 404 エラー ページが表示されます。

:::image type="content" source="media/deploy-nuxtjs/404-in-production.png" alt-text="動的ルートでの 404":::

その理由は、Nuxt.js ではホーム ページに対してのみ静的サイトが生成されているためです。 Nuxt.js では、すべての `.vue` ページ ファイルに対して同等の静的 `.html` ファイルを生成できますが、1 つ例外があります。 

ページが `_id.vue` などの動的ページの場合、そのような動的ページから静的 HTML を生成するのに十分な情報がありません。 動的ルートに使用できるパスを明示的に指定する必要があります。

## <a name="generate-static-pages-from-dynamic-routes"></a>動的ルートから静的ページを生成する

1. _nuxt.config.js_ ファイルを更新し、Nuxt.js が使用可能なすべてのデータの一覧を使用して、各フレームワークまたはライブラリの静的ページを生成できるようにします。

   ```javascript
     import { projects } from "./utils/projectsData";

     export default {
       mode: "universal",

       //...truncated

       generate: {
         async routes() {
           const paths = [];

           projects.forEach(project => {
             paths.push(`/project/${project.slug}`);
           });

           return paths;
         }
       }
     };
   ```

   > [!NOTE]
   > `routes` は非同期関数であるため、この関数で API に対する要求を行い、返された一覧を使用してパスを生成できます。

2. 新しい変更を GitHub リポジトリにプッシュし、GitHub Actions によってサイトが再度ビルドされるまで数分待ちます。 ビルドが完了すると、404 エラーは表示されなくなります。

   :::image type="content" source="media/deploy-nuxtjs/404-in-production-fixed.png" alt-text="修正された動的ルートでの 404":::

> [!div class="nextstepaction"]
> [カスタム ドメインの設定](custom-domain.md)
