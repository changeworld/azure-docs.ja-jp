---
title: チュートリアル - クライアント認証を使用してサーバーレスのリアルタイム チャット アプリを構築する
description: Azure Web PubSub サービスと Azure Functions を使用して、クライアント認証でサーバーレスのチャット アプリを構築する方法を説明するチュートリアルです。
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 11/08/2021
ms.openlocfilehash: 3e6af1542452218a5e63182f484d9d5910407c3d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715057"
---
# <a name="tutorial-create-a-serverless-real-time-chat-app-with-azure-functions-and-azure-web-pubsub-service"></a>チュートリアル: Azure Functions と Azure Web PubSub サービスを使用してサーバーレスのリアルタイム チャット アプリを作成する

Azure Web PubSub サービスは、WebSocket とパブリッシュ-サブスクライブ パターンを使用して、リアルタイム メッセージング Web アプリケーションを作成するのに役立ちます。 Azure Functions は、インフラストラクチャを管理することなくコードを実行できるサーバーレス プラットフォームです。 このチュートリアルでは、Azure Web PubSub サービスと Azure Functions を使用して、リアルタイム メッセージングとパブリッシュ-サブスクライブ パターンによるサーバーレス アプリケーションを作成する方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * サーバーレスのリアルタイム チャット アプリを構築する
> * Web PubSub 関数トリガーのバインドと出力バインドを使用する
> * 関数を Azure Function App にデプロイする
> * Azure 認証を構成する
> * イベントとメッセージをアプリケーションにルーティングするように Web PubSub イベント ハンドラーを構成する

## <a name="prerequisites"></a>必須コンポーネント

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* コード エディター ([Visual Studio Code](https://code.visualstudio.com/) など)。

* [Node.js](https://nodejs.org/en/download/) (バージョン 10.x)。
   > [!NOTE]
   > サポートされているバージョンの Node.js の詳細については、[Azure Functions ランタイム バージョンのドキュメント](../azure-functions/functions-versions.md#languages)を参照してください。
* Azure Function アプリをローカルで実行して Azure にデプロイするための [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (v3 以上を推奨)。

* Azure リソースを管理するための [Azure コマンドライン インターフェイス (Azure CLI)](/cli/azure)。

* (省略可能) Web PubSub サービスのイベント ハンドラーとしてローカル関数を公開するための [ngrok](https://ngrok.com/download)。 これを省略できるのは、関数アプリをローカルで実行する場合のみです。

# <a name="c"></a>[C#](#tab/csharp)

* コード エディター ([Visual Studio Code](https://code.visualstudio.com/) など)。

* Azure Function アプリをローカルで実行して Azure にデプロイするための [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (v3 以上を推奨)。

* Azure リソースを管理するための [Azure コマンドライン インターフェイス (Azure CLI)](/cli/azure)。

* (省略可能) Web PubSub サービスのイベント ハンドラーとしてローカル関数を公開するための [ngrok](https://ngrok.com/download)。 これを省略できるのは、関数アプリをローカルで実行する場合のみです。

---

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="create-the-functions"></a>関数を作成する

1. [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) がインストールされていることを確認します。 次に、プロジェクトの空のディレクトリを作成します。 この作業ディレクトリの下でコマンドを実行します。

    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    ```bash
    func init --worker-runtime javascript
    ```

    # <a name="c"></a>[C#](#tab/csharp)
    ```bash
    func init --worker-runtime dotnet
    ```

2. `Microsoft.Azure.WebJobs.Extensions.WebPubSub` 関数拡張機能パッケージをインストールします。

    > [!NOTE]
    > [拡張機能バンドル](../azure-functions/functions-bindings-register.md#extension-bundles)がサポートされている場合、この手順は省略可能です。

   a. `host.json` の `extensionBundle` セクションを削除して、次の手順で特定の拡張機能パッケージをインストールできるようにします。 または、ホスト json を下のように単純にします。
    ```json
    {
        "version": "2.0"
    }
    ```
   b. 特定の関数拡張機能パッケージをインストールするコマンドを実行します。
    ```bash
    func extensions install --package Microsoft.Azure.WebJobs.Extensions.WebPubSub --version 1.0.0
    ```

3. クライアントの静的 Web ページを読み取ってホストする `index` 関数を作成します。
    ```bash
    func new -n index -t HttpTrigger
    ```
   # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - `index/function.json` を更新して次の json コードをコピーします。
        ```json
        {
            "bindings": [
                {
                    "authLevel": "anonymous",
                    "type": "httpTrigger",
                    "direction": "in",
                    "name": "req",
                    "methods": [
                      "get",
                      "post"
                    ]
                },
                {
                    "type": "http",
                    "direction": "out",
                    "name": "res"
                }
            ]
        }
        ```
   - `index/index.js` を更新して次のコードをコピーします。
        ```js
        var fs = require('fs');
        var path = require('path');

        module.exports = function (context, req) {
            var index = 'index.html';
            if (process.env["HOME"] != null)
            {
                index = path.join(process.env["HOME"], "site", "wwwroot", index);
            }
            context.log("index.html path: " + index);
            fs.readFile(index, 'utf8', function (err, data) {
                if (err) {
                console.log(err);
                context.done(err);
                }
                context.res = {
                status: 200,
                headers: {
                    'Content-Type': 'text/html'
                },
                body: data
                };
                context.done();
            });
        }
        ```

   # <a name="c"></a>[C#](#tab/csharp)
   - `index.cs` を更新して `Run` 関数を次のコードに置き換えます。
        ```c#
        [FunctionName("index")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
        {
            string indexFile = "index.html";
            if (Environment.GetEnvironmentVariable("HOME") != null)
            {
                indexFile = Path.Join(Environment.GetEnvironmentVariable("HOME"), "site", "wwwroot", indexFile);
            }
            log.LogInformation($"index.html path: {indexFile}.");
            return new ContentResult
            {
                Content = File.ReadAllText(indexFile),
                ContentType = "text/html",
            };
        }
        ```

4. クライアントがアクセス トークンを含むサービス接続 URL を取得するのに役立つ `negotiate` 関数を作成します。
    ```bash
    func new -n negotiate -t HttpTrigger
    ```
    > [!NOTE]
    > このサンプルでは、[AAD](../app-service/configure-authentication-user-identities.md) ユーザー ID ヘッダー `x-ms-client-principal-name` を使用して `userId` を取得します。 また、これはローカル関数では機能しません。 ローカルで実行する際には、これを空にするか他の方法に変えることで `userId` を取得または生成できます。 たとえば、`negotiate` 関数を呼び出してサービスの接続 URL を取得する際に、クライアントがユーザー名を入力し、それを `?user={$username}` のようなクエリで渡すようにします。 また、`negotiate` 関数で `userId` を値 `{query.user}` に設定します。
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - `negotiate/function.json` を更新して次の json コードをコピーします。
        ```json
        {
            "bindings": [
                {
                    "authLevel": "anonymous",
                    "type": "httpTrigger",
                    "direction": "in",
                    "name": "req"
                },
                {
                    "type": "http",
                    "direction": "out",
                    "name": "res"
                },
                {
                    "type": "webPubSubConnection",
                    "name": "connection",
                    "hub": "simplechat",
                    "userId": "{headers.x-ms-client-principal-name}",
                    "direction": "in"
                }
            ]
        }
        ```
   - `negotiate/index.js` を更新して次のコードをコピーします。
        ```js
        module.exports = function (context, req, connection) {
            context.res = { body: connection };
            context.done();
        };
        ```
   # <a name="c"></a>[C#](#tab/csharp)
   - `negotiate.cs` を更新して `Run` 関数を次のコードに置き換えます。
        ```c#
        [FunctionName("negotiate")]
        public static WebPubSubConnection Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            [WebPubSubConnection(Hub = "simplechat", UserId = "{headers.x-ms-client-principal-name}")] WebPubSubConnection connection,
            ILogger log)
        {
            log.LogInformation("Connecting...");
            return connection;
        }
        ```

5. サービスを使用してクライアント メッセージをブロードキャストするための `message` 関数を作成します。
   ```bash
   func new -n message -t HttpTrigger
   ```

   > [!NOTE]
   > この関数では、実際には `WebPubSubTrigger` が使用されています。 ただし、`WebPubSubTrigger` は関数のテンプレートには統合されていません。 `HttpTrigger` を使用して関数テンプレートを初期化し、コードでトリガーの種類を変更します。

   # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - `message/function.json` を更新して次の json コードをコピーします。
        ```json
        {
            "bindings": [
                {
                    "type": "webPubSubTrigger",
                    "direction": "in",
                    "name": "data",
                    "hub": "simplechat",
                    "eventName": "message",
                    "eventType": "user"
                },
                {
                    "type": "webPubSub",
                    "name": "actions",
                    "hub": "simplechat",
                    "direction": "out"
                }
            ]
        }
        ```
   - `message/index.js` を更新して次のコードをコピーします。
        ```js
        module.exports = async function (context, data) {
            context.bindings.actions = {
                "actionName": "sendToAll",
                "data": `[${context.bindingData.request.connectionContext.userId}] ${data}`,
                "dataType": context.bindingData.dataType
            };
            // UserEventResponse directly return to caller
            var response = { 
                "data": '[SYSTEM] ack.',
                "dataType" : "text"
            };
            return response;
        };
        ```

   # <a name="c"></a>[C#](#tab/csharp)
   - `message.cs` を更新して `Run` 関数を次のコードに置き換えます。
        ```c#
        [FunctionName("message")]
        public static async Task<UserEventResponse> Run(
            [WebPubSubTrigger(WebPubSubEventType.User, "message")] UserEventRequest request,
            BinaryData data,
            WebPubSubDataType dataType,
            [WebPubSub(Hub = "simplechat")] IAsyncCollector<WebPubSubAction> actions)
        {
            await actions.AddAsync(WebPubSubAction.CreateSendToAllAction(
                BinaryData.FromString($"[{request.ConnectionContext.UserId}] {message.ToString()}"),
                dataType
            );
            return new UserEventResponse
            {
                Data = BinaryData.FromString("[SYSTEM] ack"),
                DataType = WebPubSubDataType.Text
            };
        }
        ```

6. プロジェクトのルート フォルダーにクライアントのシングル ページ `index.html` を追加し、次のようにコンテンツをコピーします。
    ```html
    <html>
        <body>
            <h1>Azure Web PubSub Serverless Chat App</h1>
            <div id="login"></div>
            <p></p>
            <input id="message" placeholder="Type to chat...">
            <div id="messages"></div>
            <script>
                (async function () {
                    let authenticated = window.location.href.includes('?authenticated=true');
                    if (!authenticated) {
                        // auth
                        let login = document.querySelector("#login");
                        let link = document.createElement('a');
                        link.href = `${window.location.origin}/.auth/login/aad?post_login_redirect_url=/api/index?authenticated=true`;
                        link.text = "login";
                        login.appendChild(link);
                    }
                    else {
                        // negotiate
                        let messages = document.querySelector('#messages');
                        let res = await fetch(`${window.location.origin}/api/negotiate`, {
                            credentials: "include"
                        });
                        let url = await res.json();
                        // connect
                        let ws = new WebSocket(url.url);
                        ws.onopen = () => console.log('connected');
                        ws.onmessage = event => {
                            let m = document.createElement('p');
                            m.innerText = event.data;
                            messages.appendChild(m);
                        };
                        let message = document.querySelector('#message');
                        message.addEventListener('keypress', e => {
                            if (e.charCode !== 13) return;
                            ws.send(message.value);
                            message.value = '';
                        });
                    }
                })();
            </script>
        </body>
    </html>
    ```

    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    # <a name="c"></a>[C#](#tab/csharp)
    C# プロジェクトではファイルが別の出力フォルダーにコンパイルされるため、コンテンツ ページがそれに対応するように `*.csproj` を更新する必要があります。
    ```xml
    <ItemGroup>
        <None Update="index.html">
            <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
        </None>
    </ItemGroup>
    ``

## Create and Deploy the Azure Function App

Before you can deploy your function code to Azure, you need to create 3 resources:
* A resource group, which is a logical container for related resources.
* A storage account, which is used to maintain state and other information about your functions.
* A function app, which provides the environment for executing your function code. A function app maps to your local function project and lets you group functions as a logical unit for easier management, deployment and sharing of resources.

Use the following commands to create these items. 

1. If you haven't done so already, sign in to Azure:

    ```bash
    az login
    ```

1. リソース グループを作成します。または、Azure Web PubSub サービスのいずれかを再利用してスキップできます。

    ```bash
    az group create -n WebPubSubFunction -l <REGION>
    ```

1. リソース グループとリージョン内に汎用ストレージ アカウントを作成します。

    ```bash
    az storage account create -n <STORAGE_NAME> -l <REGION> -g WebPubSubFunction
    ```

1. Azure に関数アプリを作成します。

    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    ```bash
    az functionapp create --resource-group WebPubSubFunction --consumption-plan-location <REGION> --runtime node --runtime-version 12 --functions-version 3 --name <FUNCIONAPP_NAME> --storage-account <STORAGE_NAME>
    ```

    # <a name="c"></a>[C#](#tab/csharp)

    ```bash
    az functionapp create --resource-group WebPubSubFunction --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --name <FUNCIONAPP_NAME> --storage-account <STORAGE_NAME>
    ```

1. Azure に関数プロジェクトをデプロイする:

    Azure への関数アプリの作成に成功したら、[func azure functionapp publish](./../azure-functions/functions-run-local.md) コマンドを使用して、ローカル関数プロジェクトをデプロイすることができます。

    ```bash
    func azure functionapp publish <FUNCIONAPP_NAME>
    ```
1. 関数アプリ用に `WebPubSubConnectionString` を構成します。

   最初に、**Azure portal** から Web PubSub リソースを見つけ、 **[キー]** から接続文字列をコピーします。 次に、**Azure portal** ->  **[設定]**  ->  **[構成]** の [関数アプリの設定] に移動します。 **[アプリケーション設定]** の下に新しい項目を追加します。名前は `WebPubSubConnectionString` にし、値は Web PubSub リソースの接続文字列とします。

## <a name="configure-the-web-pubsub-service-event-handler"></a>Web PubSub サービス `Event Handler` を構成する

このサンプルでは、`WebPubSubTrigger` を使用して、サービスのアップストリーム要求をリッスンしています。 そのため、Web PubSub では、ターゲット クライアント要求を送信するために、関数のエンドポイント情報を知る必要があります。 また、Azure Function App には、拡張機能固有の Webhook メソッドに関するセキュリティのためのシステム キーが必要です。 前の手順で `message` 関数を使用して Function App をデプロイした後、システム キーを取得できます。

**Azure portal** に移動して、自分の Function App リソースを見つけ、 **[アプリ キー]**  ->  **[システム キー]**  ->  **`webpubsub_extension`** の順に移動します。 `<APP_KEY>` として、値をコピーします。

:::image type="content" source="media/quickstart-serverless/func-keys.png" alt-text="関数のシステム キーを取得するスクリーンショット。":::

Azure Web PubSub サービスに `Event Handler` を設定します。 **Azure portal** に移動して、自分の Web PubSub リソースを見つけ、 **[設定]** に移動します。 次のように、新しいハブ設定と使用中の 1 つの関数とのマッピングを追加します。 `<FUNCTIONAPP_NAME>` と `<APP_KEY>` を自分のものに置き換えます。

   - ハブ名: `simplechat`
   - URL テンプレート: **https://<FUNCTIONAPP_NAME>.azurewebsites.net/runtime/webhooks/webpubsub?code=<APP_KEY>**
   - ユーザー イベント パターン: *
   - システム イベント: (このサンプルでは構成する必要はありません)

:::image type="content" source="media/quickstart-serverless/set-event-handler.png" alt-text="イベント ハンドラーの設定のスクリーンショット。":::

> [!NOTE]
> ローカルで関数を実行している場合、 関数の開始後にコマンド `ngrok http 7071` で [ngrok](https://ngrok.com/download) を使用して関数の URL を公開できます。 また、Web PubSub サービス `Event Handler` を `https://<NGROK_ID>.ngrok.io/runtime/webhooks/webpubsub` という URL で設定します。 

## <a name="configure-to-enable-client-authentication"></a>クライアント認証を有効に構成する

**Azure portal** に移動して、自分の Function App リソースを見つけ、 **[認証]** に移動します。 ページの下部にある **`Add identity provider`** 」の説明に従って、アプリケーションにシングル サインオンできるようになります。 **App Service 認証の設定** を **[認証されていないアクセスを許可する]** に設定すると、認証にリダイレクトされる前に、匿名のユーザーがクライアントのインデックス ページにアクセスできます。 その後、 **[保存]** を選択します。

ここでは、ID プロバイダーとして `Microsoft` を選択します。これにより、`negotiate` 関数で `userId` として `x-ms-client-principal-name` が使用されます。 なお、下のリンクから他の ID プロバイダーを構成することもできます。それに応じて `negotiate` 関数の `userId` 値を忘れずに更新してください。

* [Microsoft (Azure AD)](../app-service/configure-authentication-provider-aad.md)
* [Facebook](../app-service/configure-authentication-provider-facebook.md)
* [Google](../app-service/configure-authentication-provider-google.md)
* [Twitter](../app-service/configure-authentication-provider-twitter.md)

## <a name="try-the-application"></a>アプリケーションを試す

これで、関数アプリからページをテストできるようになりました (`https://<FUNCTIONAPP_NAME>.azurewebsites.net/api/index`)。 下のスナップショットを参照してください。
1. `login` をクリックして自分を認証します。
2. 入力ボックスにメッセージを入力してチャットします。

メッセージ関数では、呼び出し元のメッセージをすべてのクライアントにブロードキャストし、呼び出し元にメッセージ `[SYSTEM] ack` を返します。 そのため、下のサンプルのチャット スナップショットでは、最初の 4 つのメッセージは現在のクライアントからのものであり、最後の 2 つのメッセージは別のクライアントからのものであることがわかります。

:::image type="content" source="media/quickstart-serverless/chat-sample.png" alt-text="チャット サンプルのスクリーンショット。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリの使用を続けない場合は、次の手順に従って、このドキュメントで作成したすべてのリソースを削除して、課金が発生しないようにします。

1. Azure Portal の左端で **[リソース グループ]** を選択し、作成したリソース グループを選択します。 検索ボックスを使用して名前でリソース グループを検索することもできます。

1. 表示されたウィンドウでリソース グループを選択し、 **[リソース グループの削除]** を選択します。

1. 新しいウィンドウで、削除するリソース グループの名前を入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、サーバーレス チャット アプリケーションを実行する方法について説明しました。 これで、独自のアプリケーションの作成を始められます。 

> [!div class="nextstepaction"]
> [Azure Functions の Azure Web PubSub バインディング](https://azure.github.io/azure-webpubsub/references/functions-bindings)

> [!div class="nextstepaction"]
> [クイックスタート: Azure Web PubSub で簡単なチャットルームを作成する](https://azure.github.io/azure-webpubsub/getting-started/create-a-chat-app/js-handle-events)

> [!div class="nextstepaction"]
> [Azure Web PubSub のサンプルをさらに確認する](https://github.com/Azure/azure-webpubsub/tree/main/samples)