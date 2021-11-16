---
title: クイック スタート:Node.js Web アプリを作成する
description: Azure App Service に、初めての Node.js の Hello World を数分でデプロイします。
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 09/14/2021
ms.custom: mvc, devcenter, seodec18, mode-portal
zone_pivot_groups: app-service-vscode-cli-portal
ms.openlocfilehash: 70d98c6aed2e8f561aeb40e4e60fdeab603f58c6
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524058"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Azure で Node.js Web アプリを作成する

このクイックスタートでは、初めての Node.js ([Express](https://www.expressjs.com)) Web アプリを作成し、[Azure App Service](overview.md) にデプロイする方法について説明します。 App Service では、Linux と Windows の両方でさまざまなバージョンの Node.js がサポートされます。 

このクイックスタートでは、**Free** レベルで App Service アプリを構成するため、Azure サブスクリプションのコストは発生しません。

## <a name="set-up-your-initial-environment"></a>初期環境を設定する

:::zone target="docs" pivot="development-environment-vscode"

- アクティブなサブスクリプションが含まれる Azure アカウントを用意します。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)。
- [Node.js and npm](https://nodejs.org) をインストールします。 `node --version` コマンドを実行して、Node.js がインストールされていることを確認します。
- [Visual Studio Code](https://code.visualstudio.com/) をインストールします。
- Visual Studio Code 用の [Azure App Service 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)。
 <!-- - <a href="https://git-scm.com/" target="_blank">Install Git</a> -->

::: zone-end

:::zone target="docs" pivot="development-environment-cli"

- アクティブなサブスクリプションが含まれる Azure アカウントを用意します。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)。
- [Node.js and npm](https://nodejs.org) をインストールします。 `node --version` コマンドを実行して、Node.js がインストールされていることを確認します。
- <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> をインストールします。これを任意のシェルで使用してコマンドを実行し、Azure リソースのプロビジョニングと構成を行います。

::: zone-end


:::zone target="docs" pivot="development-environment-azure-portal"

- アクティブなサブスクリプションが含まれる Azure アカウントを用意します。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension)。
- [Node.js and npm](https://nodejs.org) をインストールします。 `node --version` コマンドを実行して、Node.js がインストールされていることを確認します。
- ご自分のアプリに接続するには、FTP クライアント ([FileZilla](https://filezilla-project.org) など) を用意します。

::: zone-end
## <a name="create-your-nodejs-application"></a>Node.js アプリケーションの作成

この手順では、スターター Node.js アプリケーションを作成し、お使いのコンピューターで実行できることを確認します。

> [!TIP]
> [Node.js チュートリアル](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)を既に完了している場合は、「[Azure へのデプロイ](#deploy-to-azure)」に進んでかまいません。

1. [Express Generator](https://expressjs.com/starter/generator.html) を使用してシンプルな Node.js アプリケーションを作成します。これは、既定では Node.js と NPM と共にインストールされています。

    ```bash
    npx express-generator myExpressApp --view pug
    ```

1. アプリケーションのディレクトリに変更し、NPM パッケージをインストールします。

    ```bash
    cd myExpressApp
    npm install
    ```

1. 開発サーバーを起動します。

    ```bash
    npm start
    ```

1. ブラウザーで、`http://localhost:3000` に移動します。 次のような結果が表示されます。

    ![Express アプリケーションの実行](./media/quickstart-nodejs/express.png)

:::zone target="docs" pivot="development-environment-vscode"
> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)
::: zone-end

## <a name="deploy-to-azure"></a>Deploy to Azure (Azure へのデプロイ)

続行する前に、すべての前提条件がインストールされ、構成されていることを確認してください。

> [!NOTE]
> Node.js アプリケーションを Azure で実行するには、`PORT` 環境変数で提供されるポートをリッスンする必要があります。 生成された Express アプリでは、この環境変数は、スタートアップ スクリプト *bin/www* (`process.env.PORT` を検索) で既に使用されています。
>

:::zone target="docs" pivot="development-environment-vscode"

#### <a name="sign-in-to-azure"></a>Azure へのサインイン

1. ターミナルで、*myExpressApp* ディレクトリにいることを確認し、次のコマンドを使用して Visual Studio Code を開始します。

    ```bash
    code .
    ```

1. Visual Studio Code の [アクティビティ バー](https://code.visualstudio.com/docs/getstarted/userinterface)で、**Azure** のロゴを選択します。

1. **App Service** エクスプローラーで、 **[Azure にサインイン]** を選択し、指示に従います。

    Visual Studio Code では、ステータス バーにお使いの Azure メール アドレスが表示され、**Azure App Service** エクスプローラーにお使いのサブスクリプションが表示されます。

    ![Azure にサインイン](./media/quickstart-nodejs/sign-in.png)

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

#### <a name="configure-the-app-service-app-and-deploy-code"></a>App Service アプリを構成してコードをデプロイする

1. **App Service** エクスプローラーで **[Web アプリにデプロイ]** アイコンを選択します。

    :::image type="content" source="media/quickstart-nodejs/deploy.png" alt-text="選択されている青い矢印アイコンを示している、Visual Studio Code での Azure App Service のスクリーンショット。":::
        
1. *myExpressApp* フォルダーを選択します。

# <a name="deploy-to-linux"></a>[Linux にデプロイする](#tab/linux)

3. **[Create new Web App]\(新しい Web アプリを作成する\)** を選択します。 既定では、Linux コンテナーが使用されます。
1. 自分の Web アプリ用にグローバルに一意な名前を入力し、**Enter** キーを押します。 名前は Azure 全体で一意である必要があり、英数字 ("A-Z"、"a-z"、"0-9") およびハイフン ("-") のみを使用する必要があります。
1. [ランタイム スタックを選択してください] で、目的の Node.js バージョンを選択します。 **LTS** バージョンが推奨されます。
1. [価格レベルを選択してください] で、 **[Free (F1)]** を選択し、Azure でリソースがプロビジョニングされるのを待ちます。
1. ポップアップ **[Always deploy the workspace "myExpressApp" to \<app-name>"]\(ワークスペース "myExpressApp" を常に \<app-name> にデプロイする\)** で、 **[はい]** を選択します。 これにより、同じワークスペース内にいる限り、Visual Studio Code では、毎回同じ App Service アプリにデプロイされます。

    Visual Studio Code では、Azure リソースをプロビジョニングしてコードをデプロイしている間、[進捗状況通知](https://code.visualstudio.com/api/references/extension-guidelines#notifications)が表示されます。

1. デプロイが完了したら、通知ポップアップで **[Web サイトの参照]** を選択します。 ブラウザーに Express の既定のページが表示されます。

# <a name="deploy-to-windows"></a>[Windows にデプロイする](#tab/windows)

3. **[新しい Web アプリを作成する]、[詳細設定]** を選択します。
1. 自分の Web アプリ用にグローバルに一意な名前を入力し、**Enter** キーを押します。 名前は Azure 全体で一意である必要があり、英数字 ("A-Z"、"a-z"、"0-9") およびハイフン ("-") のみを使用する必要があります。
1. **[新しいリソース グループの作成]** を選択し、リソース グループの名前 (例: *AppServiceQS-rg*) を入力します。
1. 目的の Node.js バージョンを選択します。 **LTS** バージョンが推奨されます。
1. オペレーティング システムとして **[Windows]** を選択します。
1. アプリの提供元の場所を選択します (例: "*西ヨーロッパ*")。
1. **[新しい App Service プランの作成]** を選択し、プランの名前 (例: *AppServiceQS-plan*) を入力し、価格レベルとして **[F1 Free]** を選択します。
1. **[Select an Application Insights resource for your app]\(アプリの Application Insights リソースを選択してください\)** では、 **[今はしない]** を選択し、Azure でリソースがプロビジョニングされるのを待ちます。
1. ポップアップ **[Always deploy the workspace "myExpressApp" to \<app-name>"]\(ワークスペース "myExpressApp" を常に \<app-name> にデプロイする\)** で、 **[はい]** を選択します。 これにより、同じワークスペース内にいる限り、Visual Studio Code では、毎回同じ App Service アプリにデプロイされます。

    Visual Studio Code では、Azure リソースをプロビジョニングしてコードをデプロイしている間、[進捗状況通知](https://code.visualstudio.com/api/references/extension-guidelines#notifications)が表示されます。

    > [!NOTE]
    > プロジェクトのルートに *web.config* がないため、デプロイが完了しても、Azure アプリはまだ実行されません。残りの手順に従って、自動的に生成します。 詳細については、「[このディレクトリまたはページを表示するためのアクセス許可がありません](configure-language-nodejs.md#you-do-not-have-permission-to-view-this-directory-or-page)」を参照してください。

1. Visual Studio Code の **App Service** エクスプローラーで、新しいアプリのノードを展開し、 **[アプリケーションの設定]** を右クリックして、 **[新しい設定の追加]** を選択します。

    ![アプリ設定の追加コマンド](media/quickstart-nodejs/add-setting.png)

1. 設定キーとして「`SCM_DO_BUILD_DURING_DEPLOYMENT`」と入力します。
1. 設定値として「`true`」と入力します。

    このアプリ設定により、デプロイ時にビルドの自動化が有効になり、開始スクリプトが自動的に検出され、それを使用して *web.config* が生成されます。

1. **App Service** エクスプローラーで **[Web アプリにデプロイ]** アイコンを再度選択し、 **[デプロイ]** を再度クリックして確認します。
1. デプロイが完了するまで待ち、通知ポップアップで **[Web サイトの参照]** を選択します。 ブラウザーに Express の既定のページが表示されます。

-----

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

::: zone-end

:::zone target="docs" pivot="development-environment-cli"

ターミナルで、*myExpressApp* ディレクトリにいることを確認し、`az webapp up` コマンドを使用して、ローカル フォルダー (*myExpressApp*) にコードをデプロイします。

# <a name="deploy-to-linux"></a>[Linux にデプロイする](#tab/linux)

```azurecli
az webapp up --sku F1 --name <app-name>
```

# <a name="deploy-to-windows"></a>[Windows にデプロイする](#tab/windows)

```azurecli
az webapp up --sku F1 --name <app-name> --os-type Windows
```

-----

- `az` コマンドが認識されない場合は、「[初期環境を設定する](#set-up-your-initial-environment)」の説明に従って Azure CLI がインストールされていることを確認してください。
- `<app_name>` を Azure 全体で一意の名前で置き換えます ("*有効な文字は、`a-z`、`0-9`、および `-` です*")。 会社名とアプリ識別子を組み合わせて使用すると、適切なパターンになります。
- `--sku F1` 引数を使用すると、コストが発生しない Free 価格レベルで Web アプリが作成されます。
- 必要に応じて、引数 `--location <location-name>` を含めることができます。ここで、`<location_name>` は利用可能な Azure リージョンです。 [`az account list-locations`](/cli/azure/appservice#az_appservice_list_locations) コマンドを実行すると、お使いの Azure アカウントで使用可能なリージョンの一覧を取得できます。
- このコマンドにより、既定で Node.js の Linux アプリが作成されます。 代わりに Windows アプリを作成するには、`--os-type` 引数を使用します。 
- "Could not auto-detect the runtime stack of your app" \(アプリのランタイム スタックを自動検出できませんでした\) というエラーが表示された場合は、*myExpressApp* ディレクトリでコマンドを実行していることを確認します ([az webapp up の自動検出に関する問題のトラブルシューティング](https://github.com/Azure/app-service-linux-docs/blob/master/AzWebAppUP/runtime_detection.md)に関するページを参照してください)。

コマンドが完了するまでに数分かかる場合があります。 実行中、リソース グループ、App Service プラン、アプリ リソースの作成、ログの構成、ZIP のデプロイの実行に関するメッセージが表示されます。 次に、"http://&lt;app-name&gt;.azurewebsites.net でアプリを起動することができます" という内容のメッセージが表示されます。これは、Azure 上のアプリの URL です。

<pre>
The webapp '&lt;app-name>' doesn't exist
Creating Resource group '&lt;group-name>' ...
Resource group creation complete
Creating AppServicePlan '&lt;app-service-plan-name>' ...
Creating webapp '&lt;app-name>' ...
Configuring default logging for the app, if not already enabled
Creating zip with contents of dir /home/cephas/myExpressApp ...
Getting scm site credentials for zip deployment
Starting zip deployment. This operation can take a while to complete ...
Deployment endpoint responded with status code 202
You can launch the app at http://&lt;app-name>.azurewebsites.net
{
  "URL": "http://&lt;app-name>.azurewebsites.net",
  "appserviceplan": "&lt;app-service-plan-name>",
  "location": "centralus",
  "name": "&lt;app-name>",
  "os": "&lt;os-type>",
  "resourcegroup": "&lt;group-name>",
  "runtime_version": "node|10.14",
  "runtime_version_detected": "0.0",
  "sku": "FREE",
  "src_path": "//home//cephas//myExpressApp"
}
</pre>

[!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]

::: zone-end

:::zone target="docs" pivot="development-environment-azure-portal"
### <a name="sign-in-to-azure-portal"></a>Azure Portal にサインインする

Azure Portal ( https://portal.azure.com ) にサインインします。

### <a name="create-azure-resources"></a>Azure リソースを作成する

1. 検索に「**app services**」と入力します。 **[サービス]** で、**[App Services]** を選択します。

     :::image type="content" source="./media/quickstart-nodejs/portal-search.png?text=Azure portal search details" alt-text="ポータルでの検索のスクリーンショット":::

1. **[App Services]** ページで、**[作成]** を選択します。
1. **[基本]** タブの **[Project details] (プロジェクトの詳細)** で、正しいサブスクリプションが選択されていることを確認し、リソース グループの **[新規作成]** を選択します。 名前として「*myResourceGroup*」と入力します。

    :::image type="content" source="./media/quickstart-nodejs/project-details.png" alt-text="Azure サブスクリプションと Web アプリのリソース グループを選択する場所を示す [プロジェクトの詳細] セクションのスクリーンショット。":::

1. **[インスタンスの詳細]** で、ご自分の Web アプリにグローバルに一意の名前を入力し、**[コード]** を選択します。 [*Node 14 LTS* **Runtime stack]\(ノード 14 LTS ランタイム スタック)**、**[オペレーティング システム]**、アプリを提供する **[リージョン]** を選択します。

    :::image type="content" source="./media/quickstart-nodejs/instance-details.png" alt-text="[インスタンスの詳細] セクションのスクリーンショット。ここで、仮想マシンの名前を指定し、そのリージョン、イメージ、サイズを選択します。":::

1. **[App Service プラン]** で、App Service プランの **[新規作成]** を選択します。 名前に「*myAppServicePlan*」と入力します。 Free レベルに変更するには、**[サイズの変更]** をクリックし、**[Dev/Test]** タブを選択し、**[F1]** を選択して、ページの下部にある **[適用]** ボタンを選択します。

    :::image type="content" source="./media/quickstart-nodejs/app-service-plan-details.png" alt-text="管理者のユーザー名とパスワードを入力する [管理者アカウント] セクションのスクリーンショット":::

1. ページの下部にある **[確認と作成]** ボタンを選択します。

    :::image type="content" source="./media/quickstart-nodejs/review-create.png" alt-text="ページの下部にある [確認と作成] ボタンを示すスクリーンショット":::

1. 検証の実行後、ページの下部にある **[作成]** ボタンを選択します。

1. デプロイが完了したら、 **[リソースに移動]** を選択します。

    :::image type="content" source="./media/quickstart-nodejs/next-steps.png" alt-text="リソースに移動する、次のステップを示すスクリーンショット":::

### <a name="get-ftp-credentials"></a>FTP 資格情報を取得する

Azure App Service では、FTP/S のデプロイに [**2 種類の資格情報**](deploy-configure-credentials.md)がサポートされています。 これらの資格情報は Azure サブスクリプションの資格情報とは異なります。 このセクションでは、FileZilla で使用する "アプリケーション スコープの資格情報" を取得します。

1. App Service のアプリ ページで左側のメニューの **[Deployment Center]\(デプロイ センター\)** をクリックし、**[FTPS credentials]\(FTPS 資格情報\)** タブを選択します。

    :::image type="content" source="./media/quickstart-nodejs/ftps-deployment-credentials.png" alt-text="FTPS デプロイ資格情報":::

1. **FileZilla** を開き、新しいサイトを作成します。

1. **[FTPS credentials]\(FTPS 資格情報\)** タブから、**FTPS エンドポイント**、**ユーザー名**、**パスワード** を FileZilla にコピーします。

    :::image type="content" source="./media/quickstart-nodejs/filezilla-ftps-connection.png" alt-text="FTPS 接続の詳細":::

1. FileZilla で **[接続する]** をクリックします。
 
### <a name="deploy-files-with-ftp"></a>FTP を使用してファイルをデプロイする

1. すべてのファイルとディレクトリ ファイルを Azure の [**/site/wwwroot**](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) ディレクトリにコピーします。
    
    :::image type="content" source="./media/quickstart-nodejs/filezilla-deploy-files.png" alt-text="FileZilla のデプロイ ファイル":::

1. アプリの URL を参照して、アプリが正しく動作していることを確認します。

::: zone-end
## <a name="redeploy-updates"></a>更新の再デプロイ

このアプリに変更をデプロイするには、Visual Studio Code で編集を行い、ファイルを保存し、Azure アプリに再デプロイします。 次に例を示します。

1. サンプル プロジェクトから *views/index.pug* を開き、次のように変更します。

    ```PUG
    p Welcome to #{title}
    ```

    to
    
    ```PUG
    p Welcome to Azure!
    ```

:::zone target="docs" pivot="development-environment-vscode"

2. **App Service** エクスプローラーで **[Web アプリにデプロイ]** アイコンを再度選択し、 **[デプロイ]** を再度クリックして確認します。

1. デプロイが完了するまで待ち、通知ポップアップで **[Web サイトの参照]** を選択します。 `Welcome to Express` メッセージが `Welcome to Azure!` に変更されたことがわかります。

::: zone-end

:::zone target="docs" pivot="development-environment-cli"

2. 変更を保存した後、引数を指定しないで `az webapp up` コマンドを再度使用してアプリを再デプロイします。

    ```azurecli
    az webapp up
    ```
    
    このコマンドでは、 *.azure/config* ファイルにローカルでキャッシュされている値 (アプリ名、リソース グループ、App Service プランなど) を使用します。
    
1. デプロイが完了したら、Web ページ `http://<app-name>.azurewebsites.net` を最新の情報に更新します。 `Welcome to Express` メッセージが `Welcome to Azure!` に変更されたことがわかります。

::: zone-end

:::zone target="docs" pivot="development-environment-azure-portal"

2. 変更を保存し、FTP クライアントを使用してアプリを再デプロイします。
    
1. デプロイが完了したら、Web ページ `http://<app-name>.azurewebsites.net` を最新の情報に更新します。 `Welcome to Express` メッセージが `Welcome to Azure!` に変更されたことがわかります。

::: zone-end

## <a name="stream-logs"></a>ストリーム ログ

:::zone target="docs" pivot="development-environment-vscode"

Azure アプリからのログ出力 (`console.log()` の呼び出し) を Visual Studio Code の出力ウィンドウで直接ストリーミングできます。

1. **App Service** エクスプローラーで、アプリ ノードを右クリックし、 **[Start Streaming Logs]\(ログのストリーム配信を開始する\)** を選択します。

    ![[Start Streaming Logs]\(ログのストリーム配信を開始する\)](./media/quickstart-nodejs/view-logs.png)

1. アプリの再起動を求められたら、 **[はい]** をクリックします。 アプリが再起動すると、ログ ストリームへの接続と共に Visual Studio Code の出力ウィンドウが開きます。

1. 数秒後、ログストリーミング サービスに接続されていることを示すメッセージが出力ウィンドウに表示されます。 ブラウザーでページを更新することにより、より多くの出力アクティビティを生成できます。

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

::: zone-end

:::zone target="docs" pivot="development-environment-cli"

アプリ、およびそれを実行するコンテナー内から生成されたコンソール ログに、アクセスすることができます。 ログには、`console.log()`の呼び出しによって生成された出力が含まれます。

ログをストリーミングするには、[az webapp log tail](/cli/azure/webapp/log#az_webapp_log_tail) コマンドを実行します。

```azurecli
az webapp log tail
```

このコマンドでは、 *.azure/config* ファイルにキャッシュされたリソース グループ名が使用されます。

また、`az webapp up` コマンドに `--logs` パラメーターを指定して、デプロイ時にログ ストリームを自動的に開くこともできます。

ブラウザーでアプリを最新の情報に更新して、コンソール ログを生成します。これには、アプリに対する HTTP 要求に関するメッセージが含まれています。 すぐに出力が表示されない場合は、30 秒後に再試行してください。

ログ ストリーミングを任意のタイミングで停止するには、ターミナルで **Ctrl** + **C** キーを押します。

::: zone-end

:::zone target="docs" pivot="development-environment-azure-portal"

アプリ、およびそれを実行するコンテナー内から生成されたコンソール ログに、アクセスすることができます。 Node.js アプリからのログ出力 (`console.log()` への呼び出し) は、Azure portal から直接ストリーミングできます。

1. ご自分のアプリの同じ **[App Service]** ページで、左側のメニューを使用して [監視] セクションまでスクロールし、**[ログ ストリーム]** をクリックします。

    :::image type="content" source="./media/quickstart-nodejs/log-stream.png" alt-text="Azure App Service 内のログ ストリームのスクリーンショット。":::

1. 数秒後、ログストリーミング サービスに接続されていることを示すメッセージが出力ウィンドウに表示されます。 ブラウザーでページを更新することにより、より多くの出力アクティビティを生成できます。

    <pre>
    Connecting...
    2021-10-26T21:04:14  Welcome, you are now connected to log-streaming service.
    Starting Log Tail -n 10 of existing logs ----
    /appsvctmp/volatile/logs/runtime/81b1b83b27ea1c3d598a1cdec28c71c4074ce66c735d0be57f15a8d07cb3178e.log
    2021-10-26T21:04:08.614384810Z: [INFO]
    2021-10-26T21:04:08.614393710Z: [INFO]  # Enter the source directory to make sure the script runs where the user expects
    2021-10-26T21:04:08.614399010Z: [INFO]  cd "/home/site/wwwroot"
    2021-10-26T21:04:08.614403210Z: [INFO]
    2021-10-26T21:04:08.614407110Z: [INFO]  export NODE_PATH=/usr/local/lib/node_modules:$NODE_PATH
    2021-10-26T21:04:08.614411210Z: [INFO]  if [ -z "$PORT" ]; then
    2021-10-26T21:04:08.614415310Z: [INFO]          export PORT=8080
    2021-10-26T21:04:08.614419610Z: [INFO]  fi
    2021-10-26T21:04:08.614423411Z: [INFO]
    2021-10-26T21:04:08.614427211Z: [INFO]  node /opt/startup/default-static-site.js
    Ending Log Tail of existing logs ---
    </pre>

::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

:::zone target="docs" pivot="development-environment-vscode"

前の手順では、リソース グループ内に Azure リソースを作成しました。 このクイックスタートの作成手順では、すべてのリソースがこのリソース グループに格納されます。 クリーンアップするには、リソース グループを削除する必要があります。


1. Visual Studio の Azure 拡張機能で、**リソース グループ** エクスプローラーを展開します。

1. サブスクリプションを展開し、前に作成したリソース グループを右クリックして、 **[削除]** を選択します。

    :::image type="content" source="media/quickstart-nodejs/clean-up.png" alt-text="App Service リソースを含むリソースを削除するための Visual Studio Code ナビゲーションのスクリーンショット。":::

1. メッセージが表示されたら、削除するリソース グループの名前を入力して、削除を確認します。 確認すると、リソース グループが削除され、完了すると、[通知](https://code.visualstudio.com/api/references/extension-guidelines#notifications)が表示されます。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=clean-up)

::: zone-end

:::zone target="docs" pivot="development-environment-cli"

前の手順では、リソース グループ内に Azure リソースを作成しました。 リソース グループには、お客様の場所に応じて "appsvc_rg_Linux_CentralUS" のような名前が付いています。

今後これらのリソースが不要である場合は、次のコマンドを実行してリソース グループを削除します。

```azurecli
az group delete --no-wait
```

このコマンドでは、 *azure/config* ファイルにキャッシュされているリソース グループ名を使用します。

`--no-wait` 引数を使用すると、操作が完了する前にコマンドから戻ることができます。

::: zone-end

:::zone target="docs" pivot="development-environment-azure-portal"

必要がなくなったら、リソース グループ、App Service、およびすべての関連リソースを削除できます。

1. App Service の[概要] ページで、「[Azure リソースを作成する](#create-azure-resources)」の手順で作成した "リソース グループ" をクリックします。

    :::image type="content" source="./media/quickstart-nodejs/resource-group.png" alt-text="App Service の [概要] ページのリソース グループ":::

1. [リソース グループ] ページで、**[リソース グループの削除]** を選択します。 リソース グループの名前を確認してリソースの削除を終了します。

    リソース グループの削除

::: zone-end

## <a name="next-steps"></a>次のステップ

おめでとうございます。このクイックスタートを正常に完了できました。

> [!div class="nextstepaction"]
> [チュートリアル:Node.js アプリと MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Node.js アプリの構成](configure-language-nodejs.md)

他の Azure 拡張機能もチェックしてみましょう。

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Docker ツール](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Azure CLI Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

または、[Node Pack for Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) 拡張機能パックをインストールして、これらすべてを入手しましょう。
