---
title: クイック スタート:Node.js Web アプリを作成する
description: Azure App Service に、初めての Node.js の Hello World を数分でデプロイします。 デプロイには、App Service への数あるデプロイ方法の 1 つである Visual Studio Code を使用します。
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 03/04/2020
ms.custom: seodec18
experimental: false
experiment_id: a231f2b4-2625-4d
ms.openlocfilehash: 1f105792a95115580d52444a617b3fc1678843ca
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78374086"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Azure で Node.js Web アプリを作成する 

Visual Studio Code を使用して Node.js または Express アプリをローカルに作成し、アプリをクラウドにデプロイすることによって、Azure App Service の使用を開始します。 App Service の Free レベルを使用するため、このクイックスタートを完了するのにコストはかかりません。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)。
- [Node.js および npm](https://nodejs.org)。 `node --version` コマンドを実行して、Node.js がインストールされていることを確認します。
- [Visual Studio Code](https://code.visualstudio.com/)。
- Visual Studio Code 用の [Azure App Service 拡張機能](vscode:extension/ms-azuretools.vscode-azureappservice)。

## <a name="clone-and-run-a-local-nodejs-application"></a>ローカル Node.js アプリケーションを複製して実行する

1. お使いのローカル コンピューターで、ターミナルを開き、サンプル リポジトリを複製します。

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. 新しいアプリ フォルダーに移動します。

    ```bash
    cd nodejs-docs-hello-world
    ```

1. アプリを起動してローカルでテストします。

    ```bash
    npm start
    ```
    
1. ブラウザーを開き、[http://localhost:1337](http://localhost:1337) に移動します。 ブラウザーに "Hello World!" と表示されます。

1. ターミナルで **Ctrl** + **C** キーを押してサーバーを停止します。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>Azure にアプリケーションをデプロイする

このセクションでは、VS Code と Azure App Service 拡張機能を使用して自分の Node.js アプリを Azure にデプロイします。

1. ターミナルで、*nodejs-docs-hello-world* フォルダーにいることを確認し、次のコマンドを使用して Visual Studio Code を起動します。

    ```bash
    code .
    ```

1. VS Code のアクティビティ バーで Azure のロゴを選択し、**Azure App Service** エクスプローラーを表示します。 **[サインイン: Azure]** を選択して、指示に従います。 (エラーが発生した場合は、以下の「[Azure サインインのトラブルシューティング](#troubleshooting-azure-sign-in)」を参照してください)。サインインすると、エクスプローラーにご使用の Azure サブスクリプションの名前が表示されます。

    ![Azure へのサインイン](containers/media/quickstart-nodejs/sign-in.png)

1. VS Code の **Azure App Service** エクスプローラーで青色の上矢印アイコンを選択して、自分のアプリを Azure にデプロイします。 (**コマンド パレット** (**Ctrl** + **Shift** + **P** キー) から同じコマンドを呼び出すこともできます。「deploy to web app」と入力し、 **[Azure App Service: Deploy to Web App]** を選択します)。

    ![Web アプリへのデプロイ](containers/media/quickstart-nodejs/deploy.png)
        
1. *nodejs-docs-hello-world* フォルダーを選択します。

1. デプロイ先のオペレーティング システムに基づいて、作成オプションを選択します。

    - Linux: **[Create new Web App]\(新しい Web アプリの作成\)** を選択します
    - Windows: **[Create new Web App]\(新しい Web アプリの作成\)、[詳細設定]** の順に選択します

1. 自分の Web アプリ用にグローバルに一意な名前を入力し、**Enter** キーを押します。 名前は Azure 全体で一意である必要があり、英数字 ("A-Z"、"a-z"、"0-9") およびハイフン ("-") のみを使用する必要があります。

1. Linux を対象とする場合は、メッセージが表示されたら Node.js バージョンを選択します。 **LTS** バージョンが推奨されます。

1. Windows を対象とする場合は、追加のプロンプトに従います。
    1. **[Create a new resource group]\(新しいリソース グループの作成\)** を選択し、リソース グループの名前を入力します (たとえば、`AppServiceQS-rg`)。
    1. オペレーティング システムとして **[Windows]** を選択します。
    1. **[新しい App Service プランの作成]** を選択し、プランの名前 (たとえば、`AppServiceQS-plan`) を入力してから、価格レベルに **[F1 Free]** を選択します。
    1. Application Insights について確認するメッセージが表示されたら、 **[後で確認する]** を選択します。
    1. 近くのリージョン、またはアクセスするリソースの近くのリージョンを選択します。

1. すべてのプロンプトに応答すると、自分のアプリ用に作成される Azure リソースが VS Code の通知ポップアップに表示されます。

    Linux へのデプロイ時にターゲット Linux サーバーで `npm install` を実行するように構成を更新するよう求められたら、 **[はい]** を選択します。

    ![ターゲットの Linux サーバー上で構成を更新するように求めるメッセージ](containers/media/quickstart-nodejs/server-build.png)

1. "**Always deploy the workspace "nodejs-docs-hello-world" to (app name) (ワークスペース "nodejs-docs-hello-world" を常に (アプリ名) にデプロイ)** " というメッセージが表示されたら、 **[はい]** を選択します。 **[はい]** を選択すると、後続のデプロイで同じ App Service Web アプリを自動的に対象とするように VS Code に指示されます。

1. Linux へのデプロイでは、デプロイが完了したら、プロンプトで **[Web サイトの参照]** を選択して、新しくデプロイした Web アプリを表示します。 ブラウザーに "Hello World!" と表示されます。

1. Windows にデプロイする場合は、最初に Web アプリの Node.js バージョン番号を設定する必要があります。

    1. VS Code で、新しいアプリ サービスのノードを展開し、 **[アプリケーション設定]** を右クリックして、 **[新しい設定の追加]** を選択します。

        ![アプリ設定の追加コマンド](containers/media/quickstart-nodejs/add-setting.png)

    1. 設定キーとして「`WEBSITE_NODE_DEFAULT_VERSION`」と入力します。
    1. 設定値として「`10.15.2`」と入力します。
    1. アプリ サービスのノードを右クリックし、 **[再起動]** を選択します

        ![アプリ サービスの再起動コマンド](containers/media/quickstart-nodejs/restart.png)

    1. アプリ サービスのノードをもう一度右クリックし、 **[Web サイトの参照]** を選択します。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Azure サインインのトラブルシューティング

Azure にサインインするときに **"Cannot find subscription with name [subscription ID] (サブスクリプション名 [サブスクリプション ID] が見つかりません)"** というエラーが表示される場合、原因としては、プロキシの内側にいるために、Azure API に到達できないことが考えられます。 ご利用のターミナルで `export` を使用して、自分のプロキシ情報で `HTTP_PROXY` と `HTTPS_PROXY` の環境変数を構成してください。

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

環境変数を設定しても問題が解決しない場合は、上の **[問題が発生しました]** ボタンを選択してお問い合わせください。

### <a name="update-the-app"></a>アプリの更新

このアプリに対する変更をデプロイするには、VS Code で編集を行い、ファイルを保存した後、同じプロセスを使用して新しいアプリを作成する代わりに既存のものを選択するだけです。

## <a name="viewing-logs"></a>ログの表示

アプリからのログ出力 (`console.log` への呼び出し) は、VS Code の出力ウィンドウで直接表示できます。

1. **Azure App Service** エクスプローラーで、アプリ ノードを右クリックし、 **[Start Streaming Logs]\(ログのストリーム配信を開始する\)** を選択します。

    ![[Start Streaming Logs]\(ログのストリーム配信を開始する\)](containers/media/quickstart-nodejs/view-logs.png)

1. 確認を求められたら、ログを有効にしてアプリケーションを再起動します。 アプリが再起動すると、ログ ストリームへの接続と共に VS Code の出力ウィンドウが開きます。 

    ![ログの有効化と再起動](containers/media/quickstart-nodejs/enable-restart.png)

1. 数秒後、ログストリーミング サービスに接続されていることを示すメッセージが出力ウィンドウに表示されます。 ブラウザーでページを更新することにより、より多くの出力アクティビティを生成できます。

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).    
    </pre>

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>次のステップ

おめでとうございます。このクイックスタートを正常に完了できました。

次は、他の Azure 拡張機能もチェックしてみましょう。

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker ツール](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure リソース マネージャー ツール](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

または、[Node Pack for Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) 拡張機能パックをインストールして、これらすべてを入手しましょう。
