---
title: チュートリアル:静的にレンダリングされた Next.js の Web サイトを Azure Static Web Apps にデプロイする
description: Next.js の動的サイトを生成し、Azure Static Web Apps を使用してデプロイします。
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 08/05/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 1f8ef3146ce7ef1b1767c04284ddbdb191b50d81
ms.sourcegitcommit: d01c2b2719e363178720003b67b968ac2a640204
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122455905"
---
# <a name="deploy-static-rendered-nextjs-websites-on-azure-static-web-apps"></a>静的にレンダリングされた Next.js の Web サイトを Azure Static Web Apps にデプロイする

このチュートリアルでは、[Next.js](https://nextjs.org) で生成された静的 Web サイトを [Azure Static Web Apps](overview.md) にデプロイする方法について説明します。 Next.js の仕様の詳細については、[スターター テンプレートの readme](https://github.com/staticwebdev/nextjs-starter#readme) を参照してください。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/)。
- GitHub アカウント。 [無料でアカウントを作成できます](https://github.com/join)。
- [Node.js](https://nodejs.org) がインストールされていること。

## <a name="set-up-a-nextjs-app"></a>Next.js アプリを設定する

Next.js CLI を使用してアプリを作成するのではなく、スターター リポジトリを使用できます。 スターター リポジトリには、動的ルートをサポートする既存の Next.js アプリケーションが含まれています。

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

フレームワークまたはライブラリを選択すると、選択した項目に関する詳細ページが表示されます。

:::image type="content" source="media/deploy-nextjs/start-nextjs-details.png" alt-text="詳細ページ":::

## <a name="deploy-your-static-website"></a>静的 Web サイトをデプロイする

次の手順では、アプリを Azure Static Web Apps にリンクする方法を示します。 Azure に移動すると、アプリケーションを運用環境にデプロイできます。

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
    | _組織_ | 適切な GitHub 組織を選択します。 |
    | _リポジトリ_ | **nextjs-starter** を選択します。 |
    | _ブランチ_ | **[main]\(メイン\)** を選択します。 |

1. _[Build Details]\(ビルドの詳細\)_ セクションで、 _[Build Presets]\(ビルドのプリセット\)_ から **[Custom]\(カスタム\)** を選択します。 ビルド構成として、次の値を追加します。

    | プロパティ | 値 |
    | --- | --- |
    | _App location (アプリの場所)_ | ボックスに「 **/** 」と入力します。 |
    | _Api location (API の場所)_ | このボックスは空のままにします。 |
    | _Output location (出力場所)_ | ボックスに「**out**」 と入力します。 |

### <a name="review-and-create"></a>[Review and create] (確認および作成)

1. **[確認および作成]** ボタンを選択して、詳細がすべて正しいことを確認します。

1. **[作成]** を選択して、App Service Static Web App の作成を開始し、デプロイのための GitHub アクションをプロビジョニングします。

1. デプロイが完了したら、 **[リソースに移動]** を選択します。

1. _[概要]_ ウィンドウで、 *[URL]* リンクを選択して、デプロイしたアプリケーションを開きます。

Web サイトがすぐに読み込めない場合、ビルドはまだ実行中です。 ワークフローが完了したら、ブラウザーを最新の情報に更新して Web アプリを表示できます。

Actions ワークフローの状態を確認するには、リポジトリの Actions ダッシュボードに移動します。

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nextjs-starter/actions
```

これで、`main` ブランチに加えた変更によって、Web サイトの新しいビルドとデプロイが開始されます。

### <a name="sync-changes"></a>変更を同期する

アプリを作成したとき、Azure Static Web Apps によってリポジトリに GitHub Actions ファイルが作成されました。 最新のものをローカル リポジトリにプルして、サーバーと同期します。

ターミナルに戻り、`git pull origin main` コマンドを実行します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順を使用して Azure Static Web Apps インスタンスを削除することができます。

1. [Azure portal](https://portal.azure.com) を開きます。
1. 上部の検索バーから「**my-nextjs-group**」を検索します。
1. グループ名を選択します。
1. **[削除]** ボタンを選択します。
1. **[はい]** を選択して、削除操作を確定します。

> [!div class="nextstepaction"]
> [カスタム ドメインの設定](custom-domain.md)
