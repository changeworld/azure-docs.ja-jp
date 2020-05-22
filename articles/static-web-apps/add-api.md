---
title: Azure Functions を使用して Azure Static Web Apps に API を追加する
description: Azure Functions を使用して、静的 Web アプリにサーバーレス API を追加することで、Azure Static Web Apps の使用を開始します。
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: wachegha
ms.openlocfilehash: a6aee5c8049e03a43c547f419f6c6646617e651c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596151"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Azure Functions を使用して Azure Static Web Apps プレビューに API を追加する

Azure Functions との統合を使用して、Azure Static Web Apps にサーバーレス API を追加できます。 この記事では、Azure Static Web Apps サイトに API を追加してデプロイする方法について説明します。

API ルートをセキュリティで保護する方法については、[ルーティング ガイド](routes.md)を参照してください。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free)。
- [Visual Studio Code](https://code.visualstudio.com/)
- Visual Studio Code 用 [Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
- [Live Server Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) 拡張機能。

## <a name="create-a-git-repository"></a>Git リポジトリを作成する 

次の手順では、新しいリポジトリを作成し、お使いのコンピューターにファイルを複製する方法を示します。

1. https://github.com/staticwebdev/vanilla-basic/generate に移動して、新しいリポジトリを作成します。
1. _[リポジトリ名]_ ボックスに、「**my-vanilla-api**」と入力します。
1. **[Create repository from template]\(テンプレートからリポジトリを作成する\)** をクリックします。

   :::image type="content" source="media/add-api/create-repository.png" alt-text="vanilla-basic から新しいリポジトリを作成する":::

プロジェクトが作成されたら、Visual Studio Code を使用して Git リポジトリを複製できます。

1. コマンド パレットでコマンドを開くには、**F1** キーを押します。
1. URL を _Git:Clone_ プロンプトに貼り付け、**Enter** キーを押します。

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Visual Studio Code を使用して GitHub プロジェクトを複製する":::

   :::image type="content" source="media/add-api/github-clone-url.png" alt-text="Visual Studio Code を使用して GitHub プロジェクトを複製する":::


## <a name="create-your-local-project"></a>ローカル プロジェクトを作成する

このセクションでは、Visual Studio Code を使用して、ローカル Azure Functions プロジェクトを作成します。 その後、Functions アプリを Azure に発行します。

1. _my-vanilla-api_ プロジェクト内に、**api** という名前のサブフォルダーを作成します。

   > [!NOTE]
   > このフォルダーには任意の名前を付けることができます。 この例では、`api` を使用します。 

2. **F1** キーを押してコマンド パレットを開きます。
3. 「**Azure Functions:Create New Project...** 」と入力します
4. **Enter** キーを押します
5. **[参照]** を選択します
6. プロジェクト ワークスペースのディレクトリとして **api** フォルダーを選択します
7. **[選択]** を選択します

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Visual Studio Code を使用して新しい Azure Functions を作成する":::

8. プロンプトで、次の情報を入力します。

    - _Select a language for your function project (関数プロジェクトの言語を選択してください)_ : **[JavaScript]** を選択します
    - _Select a template for your project's first function (プロジェクトの最初の関数のテンプレートを選択してください)_ : **[HTTP トリガー]** を選択します
    - _Provide a function name (関数名を指定してください):_ 「**GetMessage**」と入力します
    - _承認レベル_: **[匿名]** を選択します。これにより、すべてのユーザーが関数エンドポイントを呼び出せるようになります。
        - 承認レベルの詳細については、[承認キー](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)に関するページを参照してください。

9. Visual Studio Code は、この情報を使用して、HTTP トリガーによる Azure Functions プロジェクトを生成します。
    - ローカル プロジェクト ファイルは Visual Studio Code のエクスプローラー ウィンドウで表示できます。
    - 作成されるファイルの詳細については、「[生成されるプロジェクト ファイル](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code#generated-project-files)」を参照してください。

10. これで、アプリのプロジェクト構造が次の例のようになります。

    ```files
    ├── api
    │   ├── GetMessage
    │   │   ├── function.json
    │   │   ├── index.js
    │   │   └── sample.dat
    │   ├── host.json
    │   ├── local.settings.json
    │   ├── package.json
    │   └── proxies.json
    ├── index.html
    ├── readme.md
    └── styles.css
    ```

11. 次に、以下のコードを使用して、_api/GetMessage/index.js_ の下の `GetMessage` 関数を更新します。

    ```JavaScript
    module.exports = async function (context, req) {
      context.res = {
        body: { 
          text: "Hello from the API" 
        }
      };
    };
    ```

12. 以下の設定を使用して `api/GetMessage/function.json` の下の `GetMessage` 構成を更新します。

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```
    
上記の設定では、API エンドポイントは次のようになります。

- 関数に対して HTTP 要求が行われたときにトリガーされます
- 認証状態に関係なくすべての要求で使用できます
- _/api/message_ ルートを介して公開されます

## <a name="run-the-function-locally"></a>関数をローカルで実行する

Visual Studio Code を [Azure Functions Core Tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local) と統合することで、このプロジェクトをローカルの開発用コンピューター上で実行してから、Azure に発行することができます。

1. **F5** キーを押して関数を実行し、Functions アプリを起動します。Core Tools の出力が _[ターミナル]_ パネルに表示されます。

2. Azure Functions Core Tools がまだインストールされていない場合は、プロンプトで **[インストール]** を選択します。

    Core Tools がインストールされると、アプリが_ターミナル_ パネルで起動します。 出力の一部として、HTTP によってトリガーされる関数の URL エンドポイントがローカルで実行されていることを確認できます。

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Visual Studio Code を使用して新しい Azure Functions を作成する":::

3. Core Tools が実行されている状態で、次の URL に移動して、`GET` 要求を実行します。

   <http://localhost:7071/api/message>

   応答が返され、ブラウザーに次のように表示されます。

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Visual Studio Code を使用して新しい Azure Functions を作成する":::

関数が正常に実行されることを確認したら、JavaScript アプリケーションから API を呼び出すことができます。

### <a name="call-the-api-from-the-application"></a>アプリケーションから API を呼び出す

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]


#### <a name="update-files-to-access-the-api"></a>API にアクセスするためのファイルを更新する

1. 次に、以下のコードを使用して _index.html_ ファイルの内容を更新し、API 関数からテキストをフェッチして画面に表示します。

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading message from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

   Core Tools を実行している状態で、[Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) Visual Studio Code 拡張機能を使用して _index.html_ ファイルを処理し、それをブラウザーで開きます。 

2. **F1** キーを押し、**Live Server:Open with Live Server** を選択して Live Server を起動します。

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Visual Studio Code を使用して新しい Azure Functions を作成する":::

   > [!NOTE]
   > 他の HTTP サーバーまたはプロキシを使用して、`index.html` ファイルを処理できます。 `file:///` から `index.html` にアクセスすることはできません。

### <a name="commit-and-push-your-changes-to-github"></a>変更をコミットして GitHub にプッシュする

Visual Studio Code を使用して、変更をコミットし、リモート Git リポジトリにプッシュします。

1. **F1** キーを押してコマンド パレットを開きます
1. 「**Git:Commit All**」と入力します
1. コミット メッセージを追加します
1. 「**Git: push**」と入力します

## <a name="create-a-static-web-app"></a>静的 Web アプリを作成する

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-1.png" alt-text="Azure portal で静的アプリを作成する - 画面 1":::

1. [Azure Portal](https://portal.azure.com) に移動します
1. **[リソースの作成]** をクリックします
1. **Static Web Apps** を検索します
1. **[Static Web Apps (Preview)]\(Static Web Apps (プレビュー)\)** をクリックします
1. **[作成]**
1. お使いの "_Azure サブスクリプション_" を選択します
1. "_リソース グループ_" を選択するか、新しく作成します
1. アプリに **my-vanilla-api** という名前を付けます
1. 最も近い "_リージョン_" を選択します
1. **[無料]** _SKU_ を選択します
1. **[GitHub でサインイン]** ボタンをクリックし、GitHub で認証します
1. 希望する "_組織_" を選択します
1. **[リポジトリ]** ドロップダウンから _my-vanilla-api_ を選択します
1. _[ブランチ]_ ドロップダウンから **[master]** を選択します
1. **[次へ:ビルド >]** ボタンをクリックして、ビルド構成を編集します

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-2.png" alt-text="Azure portal で静的アプリを作成する - 画面 2":::

次に、ビルドの詳細を以下のように追加します。

1. _[App location]\(アプリの場所\)_ に「 **./** 」と入力します。

1. _[Api location]\(Api の場所\)_ ボックスに「**api**」と入力します。

   これは、前の手順で作成した API フォルダーの名前です。
   
1. _[App artifact location]\(アプリ成果物の場所\)_ から既定値をクリアし、このボックスを空のままにします。

1. **[Review + create]\(レビュー + 作成\)** をクリックします。

| 設定 | 説明             | 必須 |
| -------- | ----------------------- |
|  App location (アプリの場所) | 静的アプリケーションのソース コードの場所 | はい |
|  Api location (Api の場所) | API バックエンドの場所。 これは Azure Functions アプリ プロジェクトのルート フォルダーを指します | いいえ |
|  App artifact location (アプリ成果物の場所) | ビルド出力フォルダーの場所。 一部のフロントエンド JavaScript フレームワークには、運用ファイルをフォルダーに配置するビル ド ステップがあります。 この設定は、ビルド出力フォルダーを指します。 | いいえ |

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-3.png" alt-text="Azure portal で静的アプリを作成する - 画面 3":::

1. **[作成]**
1. デプロイが完了するまで待ちます (これには 1 分かかる場合があります)
1. `https://github.com/<YOUR_GITHUB_ACCOUNT>/my-vanilla-api/actions?query=workflow%3A"Azure+Pages+CI%2FCD"` に移動します
1. ビルドが成功したことを確認します

:::image type="content" source="media/add-api/github-workflow-0.png" alt-text="GitHub ワークフロー":::

デプロイされた API は `https://<STATIC_APP_NAME>.azurestaticapps.net/api/<FUNCTION_OR_ROUTE_NAME>` にあります。

:::image type="content" source="media/add-api/github-workflow-1.png" alt-text="GitHub ワークフロー":::

次のように、Azure portal からアプリケーションの URL を見つけることもできます。

:::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Azure portal から静的アプリの URL にアクセスする":::

または、`https://<STATIC_APP_NAME>.azurestaticapps.net` で Azure の静的 Web アプリに直接アクセスし、ブラウザーで結果を確認することもできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを今後の使用のために保持したくない場合は、次の手順に従って、Azure の静的 Web アプリとその関連リソースを削除できます。

1. [Azure Portal](https://portal.azure.com) に移動します
1. 上部の検索バーで、 「**リソース グループ**」 と入力します
1. **[リソース グループ]** をクリックします 
1. **[myResourceGroup]** を選択します
1. _[myResourceGroup]_ ページで、一覧表示されたリソースが、削除しようとするリソースであることを確認します。
1. **[削除]** を選択します
1. テキスト ボックスに「**myResourceGroup**」と入力します
1. **[削除]** を選択します。


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [アプリケーションの設定の構成](./application-settings.md)
