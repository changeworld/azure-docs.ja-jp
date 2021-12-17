---
title: JavaScript を使って Azure Functions と SignalR Service を使用するチャット ルームを作成する
description: Azure SignalR Service と Azure Functions で JavaScript を使用して、GitHub の星の数を表示するアプリを作成するクイックスタート。
author: sffamily
ms.author: zhshang
ms.date: 06/09/2021
ms.topic: quickstart
ms.service: signalr
ms.devlang: javascript
ms.custom: devx-track-js, mode-api
ms.openlocfilehash: 8dd69eb071275b1ae54b6d381cc7d7f0371ca118
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069560"
---
# <a name="quickstart-use-javascript-to-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service"></a>クイック スタート: JavaScript を使用して Azure Functions と SignalR Service で GitHub の星の数を表示するアプリを作成する

Azure SignalR Service を使用すると、アプリケーションに対してリアルタイム機能を簡単に追加できます。Azure Functions は、インフラストラクチャを管理することなくコードを実行できるサーバーレス プラットフォームです。 このクイックスタートでは、SignalR Service と Azure Functions を使用して、JavaScript でサーバーレス アプリケーションを構築し、クライアントにメッセージをブロードキャストする方法について説明します。

> [!NOTE]
> この記事に記載されているコードはすべて、[GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/javascript) で入手することができます

## <a name="prerequisites"></a>前提条件

- コード エディター ([Visual Studio Code](https://code.visualstudio.com/) など)。
- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) バージョン 2.以上。 Azure 関数アプリをローカルに実行する際に使用します。
- [Node.js](https://nodejs.org/en/download/) (バージョン 10.x)

   > [!NOTE]
   > サンプルは、他のバージョンの Node.js でも動作するはずです。詳細については、[Azure Functions ランタイム バージョンのドキュメント](../azure-functions/functions-versions.md#languages)を参照してください。

> [!NOTE]
> このクイック スタートは、macOS、Windows、または Linux で実行できます。

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsjs)。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure アカウントで Azure Portal (<https://portal.azure.com/>) にサインインします。

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsjs)。

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsjs)。


## <a name="setup-and-run-the-azure-function-locally"></a>Azure Functions をローカルで設定して実行する

1. Azure Function Core Tools がインストールされていることを確認します。 空のディレクトリを作成し、コマンド ラインを使用してディレクトリに移動します。

    ```bash
    # Initialize a function project
    func init --worker-runtime javascript
    ```

<<<<<<< HEAD
1. コード エディターで、クローンしたリポジトリの *src/chat/javascript* フォルダーを開きます。
=======
2. プロジェクトを初期化した後、関数を作成する必要があります。 このサンプルでは、3 つの関数を作成する必要があります。

    1. 以下のコマンドを実行して、クライアント用の Web ページをホストする `index` 関数を作成します。

        ```bash
        func new -n index -t HttpTrigger
        ```
        `index/function.json` を開いて次の JSON コードをコピーします。

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

        `index/index.js` を開いて次のコードをコピーします。

        ```javascript
        var fs = require('fs').promises
    
        module.exports = async function (context, req) {
            const path = context.executionContext.functionDirectory + '/../content/index.html'
            try {
                var data = await fs.readFile(path);
                context.res = {
                    headers: {
                        'Content-Type': 'text/html'
                    },
                    body: data
                }
                context.done()
            } catch (error) {
                context.log.error(err);
                context.done(err);
            }
        }
        ```
    
    2. クライアントでアクセス トークンを取得するための `negotiate` 関数を作成します。
    
        ```bash
        func new -n negotiate -t SignalRNegotiateHTTPTrigger
        ```
        
        `negotiate/function.json` を開いて次の JSON コードをコピーします。
    
        ```json
        {
          "disabled": false,
          "bindings": [
            {
              "authLevel": "anonymous",
              "type": "httpTrigger",
              "direction": "in",
              "methods": [
                "post"
              ],
              "name": "req",
              "route": "negotiate"
            },
            {
              "type": "http",
              "direction": "out",
              "name": "res"
            },
            {
              "type": "signalRConnectionInfo",
              "name": "connectionInfo",
              "hubName": "serverless",
              "connectionStringSetting": "AzureSignalRConnectionString",
              "direction": "in"
            }
          ]
        }
        ```
    
    3. すべてのクライアントにメッセージをブロードキャストするための `broadcast` 関数を作成します。 このサンプルでは、時間トリガーを使用してメッセージを定期的にブロードキャストします。
    
        ```bash
        func new -n broadcast -t TimerTrigger
        ```
    
        `broadcast/function.json` を開いて次のコードをコピーします。
    
        ```json
        {
          "bindings": [
            {
              "name": "myTimer",
              "type": "timerTrigger",
              "direction": "in",
              "schedule": "*/5 * * * * *"
            },
            {
              "type": "signalR",
              "name": "signalRMessages",
              "hubName": "serverless",
              "connectionStringSetting": "AzureSignalRConnectionString",
              "direction": "out"
            }
          ]
        }
        ```
    
        `broadcast/index.js` を開いて次のコードをコピーします。
    
        ```javascript
        var https = require('https');
        
        module.exports = function (context) {
            var req = https.request("https://api.github.com/repos/azure/azure-signalr", {
                method: 'GET',
                headers: {'User-Agent': 'serverless'}
            }, res => {
                var body = "";
        
                res.on('data', data => {
                    body += data;
                });
                res.on("end", () => {
                    var jbody = JSON.parse(body);
                    context.bindings.signalRMessages = [{
                        "target": "newMessage",
                        "arguments": [ `Current star count of https://github.com/Azure/azure-signalr is: ${jbody['stargazers_count']}` ]
                    }]
                    context.done();
                });
            }).on("error", (error) => {
                context.log(error);
                context.res = {
                  status: 500,
                  body: error
                };
                context.done();
            });
            req.end();
        }    
        ```

3. このサンプルのクライアント インターフェイスは Web ページです。 `index` 関数で `content/index.html` から HTML コンテンツを読み込むと考え、プロジェクト ルート フォルダーの下の `content` ディレクトリに新たに `index.html` ファイルを作成します。 そして、次の内容をコピーします。

    ```html
    <html>
    
    <body>
      <h1>Azure SignalR Serverless Sample</h1>
      <div id="messages"></div>
      <script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.7/signalr.min.js"></script>
      <script>
        let messages = document.querySelector('#messages');
        const apiBaseUrl = window.location.origin;
        const connection = new signalR.HubConnectionBuilder()
            .withUrl(apiBaseUrl + '/api')
            .configureLogging(signalR.LogLevel.Information)
            .build();
          connection.on('newMessage', (message) => {
            document.getElementById("messages").innerHTML = message;
          });
    
          connection.start()
            .catch(console.error);
      </script>
    </body>
    
    </html>
    ```
>>>>>>> repo_sync_working_branch

4. もう少しで完了です。 最後の手順では、Azure Functions の設定に SignalR Service の接続文字列を設定します。

    1. Azure portal を開いているブラウザーのポータルの上部にある検索ボックスから、以前にデプロイした SignalR Service インスタンスの名前で検索し、インスタンスが正常に作成されたことを確認します。 インスタンスを選択して開きます。

        ![SignalR Service インスタンスの検索](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

    1. **[Key]\(キー\)** を選択し、SignalR Service インスタンスの接続文字列を表示します。
    
        ![プライマリ接続文字列が強調表示されているスクリーンショット。](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

    1. プライマリ接続文字列をコピーします。 そして、以下のコマンドを実行します。
    
        ```bash
        func settings add AzureSignalRConnectionString "<signalr-connection-string>"
        ```
    
5. Azure Functions をローカルで実行します:

    ```bash
    func start
    ```

    その後、Azure Functions がローカルで実行されます。 ブラウザーを使用して `http://localhost:7071/api/index` にアクセスすると、現在の星の数を確認できます。 また、GitHub で星を付けたり外したりすると、数秒ごとに星の数が更新されます。

    > [!NOTE]
    > SignalR のバインディングには Azure Storage が必要ですが、Function がローカルで動作している場合は、ローカル ストレージ エミュレーターを使用することができます。
    > `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.` のようなエラーが発生した場合は、[Storage Emulator](../storage/common/storage-use-emulator.md) をダウンロードして有効にする必要があります

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qscsharp)

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsjs)。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、リアルタイムのサーバーレス アプリケーションを構築してローカルで実行しました。 Azure Functions で SignalR Service のバインディングを使用する方法について確認します。
次に、SignalR Service を使用して、クライアントと Azure Functions の間で双方向通信を行う方法について詳しく説明します。

> [!div class="nextstepaction"]
> [Azure Functions における SignalR サービスのバインド](../azure-functions/functions-bindings-signalr-service.md)

> [!div class="nextstepaction"]
> [サーバーレスでの双方向通信](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/BidirectionChat)

> [!div class="nextstepaction"]
> [VS Code で Azure Functions をデプロイする](/azure/developer/javascript/tutorial-vscode-serverless-node-01)
