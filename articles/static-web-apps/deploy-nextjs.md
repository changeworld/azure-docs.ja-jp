---
title: チュートリアル:静的にレンダリングされた Next.js の Web サイトを Azure Static Web Apps にデプロイする
description: Next.js の動的サイトを生成し、Azure Static Web Apps を使用してデプロイします。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: eb4a9c69ed29b1f13ab2769044c0067779a5e195
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2021
ms.locfileid: "112454134"
---
# <a name="deploy-static-rendered-nextjs-websites-on-azure-static-web-apps"></a>静的にレンダリングされた Next.js の Web サイトを Azure Static Web Apps にデプロイする

このチュートリアルでは、[Next.js](https://nextjs.org) で生成された静的 Web サイトを [Azure Static Web Apps](overview.md) にデプロイする方法について説明します。 まず、Next.js アプリを設定、構成、デプロイする方法を学習します。 このプロセスの間に、Next.js を使用して静的ページを生成するときによく発生する一般的な課題に対処する方法についても学習します

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/)。
- GitHub アカウント。 [無料でアカウントを作成できます](https://github.com/join)。
- [Node.js](https://nodejs.org) がインストールされていること。

## <a name="set-up-a-nextjs-app"></a>Next.js アプリを設定する

Next.js CLI を使用してアプリを作成するのではなく、既存の Next.js アプリを含むスターター リポジトリを使用できます。 このリポジトリには動的ルートを使用する Next.js アプリが用意されており、デプロイに関する一般的な問題が明らかになっています。 動的ルートには、後で詳しく説明する追加のデプロイ構成が必要です。

最初に、お使いの GitHub アカウントに、テンプレート リポジトリから新しいリポジトリを作成します。

1. [https://github.com/staticwebdev/nextjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nextjs-starter/generate) に移動します
1. リポジトリに **nextjs-starter** という名前を設定します
1. 次に、新しいリポジトリをお使いのコンピューターに複製します。 `<YOUR_GITHUB_ACCOUNT_NAME>` をアカウント名に置き換えます。

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nextjs-starter
    ```

1. 新しく複製された Next.js アプリに移動します。

   ```bash
   cd nextjs-starter
   ```

1. 依存関係をインストールします。

    ```bash
    npm install
    ```

1. 開発環境で Next.js アプリを起動します。

    ```bash
    npm run dev
    ```

`http://localhost:3000` に移動してアプリを開きます。いつも使用しているブラウザーで、次の Web サイトが開かれます。

:::image type="content" source="media/deploy-nextjs/start-nextjs-app.png" alt-text="Next.js アプリを開始する":::

フレームワークまたはライブラリをクリックすると、選択した項目に関する詳細ページが表示されます。

:::image type="content" source="media/deploy-nextjs/start-nextjs-details.png" alt-text="詳細ページ":::

## <a name="generate-a-static-website-from-nextjs-build"></a>Next.js のビルドから静的 Web サイトを生成する

`npm run build` を使用して Next.js のサイトをビルドすると、アプリは静的サイトではなく、従来の Web アプリとしてビルドされます。 静的サイトを生成するには、次のアプリケーション構成を使用します。

1. 静的ルートを構成するには、自分のプロジェクトのルートに _next.config.js_ という名前のファイルを作成し、次のコードを追加します。

    ```javascript
    module.exports = {
      trailingSlash: true,
      exportPathMap: function() {
        return {
          '/': { page: '/' }
        };
      }
    };
    ```

      この構成では、`/` ルートに対して提供され、_pages/index.js_ ページ ファイルである Next.js ページに、`/` がマップされます。

1. ビルドの後 `next export` コマンドを使用して静的サイトも生成されるように、_package.json_ のビルド スクリプトを更新します。 `export` コマンドにより、静的サイトが生成されます。

    ```json
    "scripts": {
      "dev": "next dev",
      "build": "next build && next export",
    },
    ```

    このコマンドを追加すると、コミットをプッシュするたびに Static Web Apps によって `build` スクリプトが実行されるようになります。

1. 静的サイトを生成します。

    ```bash
    npm run build
    ```

    静的サイトが生成され、作業ディレクトリのルートにある _out_ フォルダーにコピーされます。

    > [!NOTE]
    > このフォルダーは、デプロイ時に CI/CD によって生成される必要があるため、 _.gitignore_ ファイルにリストされています。

## <a name="push-your-static-website-to-github"></a>静的 Web サイトを GitHub にプッシュする

Azure Static Web Apps により、GitHub リポジトリからアプリがデプロイされます。これは、指定されたブランチにプッシュされるすべてのコミットに対し、常に行われます。 次のコマンドを使用して、変更を GitHub に同期します。

1. 変更されたすべてのファイルをステージします

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

### <a name="create-a-static-app"></a>静的アプリを作成する

1. [Azure Portal](https://portal.azure.com) に移動します。
1. **[リソースの作成]** を選択します。
1. **Static Web Apps** を検索します。
1. **Static Web Apps** を選択します。
1. **［作成］** を選択します
1. _[基本]_ タブで、次の値を入力します。

    | プロパティ | 値 |
    | --- | --- |
    | _サブスクリプション_ | Azure サブスクリプション名。 |
    | _リソース グループ_ | **my-nextjs-group**  |
    | _名前_ | **my-nextjs-app** |
    | _[プランの種類]_ | **Free** |
    | _Azure Functions API のリージョンとステージング環境_ | 最も近いリージョンを選択します。 |
    | _ソース_ | **GitHub** |

1. **[GitHub でサインイン]** を選択し、GitHub で認証します。

1. 次の GitHub 値を入力します。

    | プロパティ | 値 |
    | --- | --- |
    | _組織_ | ご自分の希望する GitHub 組織を選択します。 |
    | _リポジトリ_ | **nextjs-starter** を選択します。 |
    | _ブランチ_ | **[main]\(メイン\)** を選択します。 |

1. _[Build Details]\(ビルドの詳細\)_ セクションで、 _[Build Presets]\(ビルドのプリセット\)_ ドロップダウンから **[Custom]\(カスタム\)** を選択し、既定値をそのままにします。

1. _[App location]\(アプリの場所\)_ ボックスに「 **/** 」と入力します。
1. _[Api location]\(API の場所\)_ ボックスはからのままにします。
1. _[Output location]\(出力の場所\)_ ボックスに、「**out**」と入力します。

### <a name="review-and-create"></a>[Review and create] (確認および作成)

1. **[確認および作成]** ボタンを選択して、詳細がすべて正しいことを確認します。

1. **[作成]** を選択して、App Service Static Web App の作成を開始し、デプロイのための GitHub アクションをプロビジョニングします。

1. デプロイが完了したら、 **[リソースに移動]** をクリックします。

1. _[概要]_ ウィンドウで、 *[URL]* リンクをクリックして、デプロイしたアプリケーションを開きます。

Web サイトがすぐに読み込まれない場合は、バックグラウンドで GitHub Actions ワークフローがまだ実行されています。 ワークフローが完了したら、クリックしてブラウザーを最新の情報に更新し、Web アプリを表示できます。
Web サイトがすぐに読み込まれない場合は、バックグラウンドで GitHub Actions ワークフローがまだ実行されています。 ワークフローが完了したら、クリックしてブラウザーを最新の情報に更新し、Web アプリを表示できます。

リポジトリの Actions に移動することで、Actions ワークフローの状態を確認できます。

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nextjs-starter/actions
```

### <a name="sync-changes"></a>変更を同期する

アプリを作成すると、Azure Static Web Apps によってリポジトリに GitHub Actions ワークフロー ファイルが作成されます。 git 履歴が同期されるように、このファイルをローカル リポジトリに取り込む必要があります。

ターミナルに戻り、`git pull origin main` コマンドを実行します。

## <a name="configure-dynamic-routes"></a>動的なルートを構成する

新しくデプロイされたサイトに移動し、フレームワークまたはライブラリのロゴの 1 つをクリックします。 詳細ページは表示されず、代わりに 404 エラー ページが表示されます。

:::image type="content" source="media/deploy-nextjs/404-in-production.png" alt-text="動的ルートでの 404":::

このエラーが発生するのは、Next.js ではアプリケーションの構成に基づいてホーム ページだけが生成されるためです。

## <a name="generate-static-pages-from-dynamic-routes"></a>動的ルートから静的ページを生成する

1. _next.config.js_ ファイルを更新し、Next.js が使用可能なすべてのデータの一覧を使用して、各フレームワークまたはライブラリの静的ページを生成できるようにします。

   ```javascript
   const data = require('./utils/projectsData');

   module.exports = {
     trailingSlash: true,
     exportPathMap: async function () {
       const { projects } = data;
       const paths = {
         '/': { page: '/' },
       };
  
       projects.forEach((project) => {
         paths[`/project/${project.slug}`] = {
           page: '/project/[path]',
           query: { path: project.slug },
         };
       });
  
       return paths;
     },
   };
   ```

   > [!NOTE]
   > `exportPathMap` 関数は非同期関数であるため、この関数で API に対する要求を行い、返された一覧を使用してパスを生成できます。

2. 新しい変更を GitHub リポジトリにプッシュし、GitHub Actions によってサイトが再度ビルドされるまで数分待ちます。 ビルドが完了すると、404 エラーは表示されなくなります。

   :::image type="content" source="media/deploy-nextjs/404-in-production-fixed.png" alt-text="修正された動的ルートでの 404":::

> [!div class="nextstepaction"]
> [カスタム ドメインの設定](custom-domain.md)
