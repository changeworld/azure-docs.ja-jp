---
title: チュートリアル - Azure Web PubSub サービスと Azure Functions を使用してサーバーレス通知アプリを作成する
description: Azure Web PubSub サービスと Azure Functions を使用してサーバーレス通知アプリケーションを作成する方法を説明するチュートリアルです。
author: JialinXin
ms.author: jixin
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 11/01/2021
ms.openlocfilehash: c9e0a59e5e228d500ca011ce20c20153c3a9558b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132709047"
---
# <a name="tutorial-create-a-serverless-notification-app-with-azure-functions-and-azure-web-pubsub-service"></a>チュートリアル: Azure Functions と Azure Web PubSub サービスを使用してサーバーレス通知アプリを作成する

Azure Web PubSub サービスは、WebSocket を使用して、リアルタイム メッセージング Web アプリケーションを作成するのに役立ちます。 Azure Functions は、インフラストラクチャを管理することなくコードを実行できるサーバーレス プラットフォームです。 このチュートリアルでは、Azure Web PubSub サービスと Azure Functions を使用して、通知シナリオでリアルタイム メッセージングを使用したサーバーレス アプリケーションを作成する方法について説明します。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * サーバーレス通知アプリを作成する
> * Web PubSub 関数の入力および出力のバインドを使用する
> * サンプル関数をローカルで実行する
> * 関数を Azure Function App にデプロイする

## <a name="prerequisites"></a>必須コンポーネント

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* コード エディター ([Visual Studio Code](https://code.visualstudio.com/) など)。

* [Node.js](https://nodejs.org/en/download/) (バージョン 10.x)。
   > [!NOTE]
   > サポートされているバージョンの Node.js の詳細については、[Azure Functions ランタイム バージョンのドキュメント](../azure-functions/functions-versions.md#languages)を参照してください。

* Azure Function アプリをローカルで実行して Azure にデプロイするための [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (v3 以上を推奨)。

* Azure リソースを管理するための [Azure コマンドライン インターフェイス (Azure CLI)](/cli/azure)。

# <a name="c"></a>[C#](#tab/csharp)

* コード エディター ([Visual Studio Code](https://code.visualstudio.com/) など)。

* Azure Function アプリをローカルで実行して Azure にデプロイするための [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (v3 以上を推奨)。

* Azure リソースを管理するための [Azure コマンドライン インターフェイス (Azure CLI)](/cli/azure)。

---

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [create-instance-portal](includes/create-instance-portal.md)]

## <a name="create-and-run-the-functions-locally"></a>関数をローカルで作成して実行する

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
                    "hub": "notification",
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
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
            [WebPubSubConnection(Hub = "notification")] WebPubSubConnection connection,
            ILogger log)
        {
            log.LogInformation("Connecting...");

            return connection;
        }
        ```

5. `TimerTrigger` を使用して通知を生成する `notification` 関数を作成します。
   ```bash
    func new -n notification -t TimerTrigger
    ```
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
   - `notification/function.json` を更新して次の json コードをコピーします。
        ```json
        {
            "bindings": [
                {
                "name": "myTimer",
                "type": "timerTrigger",
                "direction": "in",
                "schedule": "*/10 * * * * *"
                },
                {
                "type": "webPubSub",
                "name": "actions",
                "hub": "notification",
                "direction": "out"
                }
            ]
        }
        ```
   - `notification/index.js` を更新して次のコードをコピーします。
        ```js
        module.exports = function (context, myTimer) {
            context.bindings.actions = {
                "actionName": "sendToAll",
                "data": `[DateTime: ${new Date()}] Temperature: ${getValue(22, 1)}\xB0C, Humidity: ${getValue(40, 2)}%`,
                "dataType": "text"
            }
            context.done();
        };

        function getValue(baseNum, floatNum) {
            return (baseNum + 2 * floatNum * (Math.random() - 0.5)).toFixed(3);
        }
        ```
   # <a name="c"></a>[C#](#tab/csharp)
   - `notification.cs` を更新して `Run` 関数を次のコードに置き換えます。
        ```c#
        [FunctionName("notification")]
        public static async Task Run([TimerTrigger("*/10 * * * * *")]TimerInfo myTimer, ILogger log,
            [WebPubSub(Hub = "notification")] IAsyncCollector<WebPubSubAction> actions)
        {
            await actions.AddAsync(new SendToAllAction
            {
                Data = BinaryData.FromString($"[DateTime: {DateTime.Now}] Temperature: {GetValue(23, 1)}{'\xB0'}C, Humidity: {GetValue(40, 2)}%"),
                DataType = WebPubSubDataType.Text
            });
        }

        private static string GetValue(double baseNum, double floatNum)
        {
            var rng = new Random();
            var value = baseNum + floatNum * 2 * (rng.NextDouble() - 0.5);
            return value.ToString("0.000");
        }
        ``` 

6. プロジェクトのルート フォルダーにクライアントのシングル ページ `index.html` を追加し、次のようにコンテンツをコピーします。
    ```html
    <html>
        <body>
        <h1>Azure Web PubSub Notification</h1>
        <div id="messages"></div>
        <script>
            (async function () {
                let messages = document.querySelector('#messages');
                let res = await fetch(`${window.location.origin}/api/negotiate`);
                let url = await res.json();
                let ws = new WebSocket(url.url);
                ws.onopen = () => console.log('connected');

                ws.onmessage = event => {
                    let m = document.createElement('p');
                    m.innerText = event.data;
                    messages.appendChild(m);
                };
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
    ```

7. Azure Function アプリを構成して実行する

    - ブラウザーで **Azure portal** を開き、先ほどデプロイした Web PubSub サービス インスタンスが正常に作成されていることを確認します。 そのインスタンスに移動します。
    - **[キー]** を選択し、接続文字列をコピーします。

    :::image type="content" source="media/quickstart-serverless/copy-connection-string.png" alt-text="Web PubSub 接続文字列のコピーのスクリーンショット。":::

    関数フォルダーで下のコマンドを実行して、サービス接続文字列を設定します。 `<connection-string`> を、必要に応じて自分の値に置き換えます。

    ```bash
    func settings add WebPubSubConnectionString "<connection-string>"
    ```

    > [!NOTE]
    > このサンプルで使用している `TimerTrigger` には Azure Storage への依存関係がありますが、Function がローカルで実行されているときは、ローカル ストレージ エミュレーターを使用することができます。 `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.` のようなエラーが発生した場合は、[Storage Emulator](../storage/common/storage-use-emulator.md) をダウンロードして有効にする必要があります。

    これで、ローカル関数を次のコマンドで実行できます。

    ```bash
    func start
    ```

    実行中のログを確認するには、`https://localhost:7071/api/index` にアクセスしてローカル ホストの静的ページにアクセスします。

## <a name="deploy-function-app-to-azure"></a>Azure に関数アプリをデプロイする

関数コードを Azure にデプロイする前に、3 つのリソースを作成する必要があります。
* リソース グループ。関連リソースの論理コンテナーです。
* ストレージ アカウント。関数についての情報 (状態など) を維持する目的で使用されます。
* 関数アプリ。関数コードを実行するための環境となります。 関数アプリは、ローカルの関数プロジェクトと対応関係にあります。これを使用すると、リソースの管理、デプロイ、共有を容易にするための論理ユニットとして関数をグループ化できます。

以下のコマンドを使用してこれらの項目を作成します。 

1. まだ Azure にサインインしていない場合は、Azure にサインインします。

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

    Azure への関数アプリの作成に成功したら、[func azure functionapp publish](../azure-functions/functions-run-local.md) コマンドを使用して、ローカル関数プロジェクトをデプロイすることができます。

    ```bash
    func azure functionapp publish <FUNCIONAPP_NAME> --publish-local-settings
    ```

    > [!NOTE]
    > ここでは、ローカル設定 `local.settings.json` をコマンド パラメーター `--publish-local-settings` と共にデプロイしています。 Microsoft Azure ストレージ エミュレーターを使用している場合は、プロンプト メッセージ `App setting AzureWebJobsStorage is different between azure and local.settings.json, Would you like to overwrite value in azure? [yes/no/show]` に続いて「`no`」と入力して、 Azure でこの値の上書きをスキップできます。 さらに、Function App の設定を **[Azure portal]**  ->  **[設定]**  ->  **[構成]** で更新できます。

1. これで、URL (`https://<FUNCIONAPP_NAME>.azurewebsites.net/api/index`) に移動して、Azure Function App から自分のサイトを確認できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリの使用を続けない場合は、次の手順に従って、このドキュメントで作成したすべてのリソースを削除して、課金が発生しないようにします。

1. Azure Portal の左端で **[リソース グループ]** を選択し、作成したリソース グループを選択します。 検索ボックスを使用して名前でリソース グループを検索することもできます。

1. 表示されたウィンドウでリソース グループを選択し、 **[リソース グループの削除]** を選択します。

1. 新しいウィンドウで、削除するリソース グループの名前を入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、サーバーレス チャット アプリケーションを実行する方法について説明しました。 これで、独自のアプリケーションの作成を始められます。 

> [!div class="nextstepaction"]
> [チュートリアル: Azure Web PubSub で簡単なチャットルームを作成する](https://azure.github.io/azure-webpubsub/getting-started/create-a-chat-app/js-handle-events)

> [!div class="nextstepaction"]
> [Azure Functions の Azure Web PubSub バインディング](https://azure.github.io/azure-webpubsub/references/functions-bindings)

> [!div class="nextstepaction"]
> [Azure Web PubSub のサンプルをさらに確認する](https://github.com/Azure/azure-webpubsub/tree/main/samples)