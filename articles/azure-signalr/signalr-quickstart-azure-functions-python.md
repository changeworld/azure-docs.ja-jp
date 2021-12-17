---
title: Azure SignalR Service サーバーレス クイックスタート - Python
description: Azure SignalR Service と Azure Functions で Python を使用して、GitHub のスターの数を表示するアプリを作成するクイックスタート。
author: anthonychu
ms.author: antchu
ms.date: 06/09/2021
ms.topic: quickstart
ms.service: signalr
ms.devlang: python
ms.custom: devx-track-python, mode-api
ms.openlocfilehash: 0d6066e5305cf3acda350dcaef4572d6564395f2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069598"
---
# <a name="quickstart-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service-using-python"></a>クイック スタート: Azure Functions と SignalR Service で Python を使用して、GitHub のスターの数を表示するアプリを作成する

Azure SignalR Service を使用すると、アプリケーションにリアルタイム機能を簡単に追加できます。 Azure Functions は、インフラストラクチャを管理することなくコードを実行できるサーバーレス プラットフォームです。 このクイックスタートでは、SignalR Service と Azure Functions を使用して、Python を用いてサーバーレス アプリケーションを構築し、クライアントにメッセージをブロードキャストする方法について説明します。

> [!NOTE]
> この記事に記載されているコードはすべて、[GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/python) で入手することができます

## <a name="prerequisites"></a>前提条件

このクイック スタートは、macOS、Windows、または Linux で実行できます。

[Visual Studio Code](https://code.visualstudio.com/) などのコード エディターがインストールされていることを確認してください。

Python Azure Function アプリをローカルで実行するために、[Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (バージョン 2.7.1505 以降) をインストールします。

Azure Functions には [Python 3.6 以降](https://www.python.org/downloads/)が必要です ([サポートされている Python のバージョン](../azure-functions/functions-reference-python.md#python-version)を参照してください)。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qspython)。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure アカウントで Azure Portal (<https://portal.azure.com/>) にサインインします。

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qspython)。

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qspython)。

<<<<<<< HEAD
[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qspython)。

## <a name="configure-and-run-the-azure-function-app"></a>Azure Function アプリを構成して実行する

1. Azure portal を開いているブラウザーのポータルの上部にある検索ボックスから、以前にデプロイした SignalR Service インスタンスの名前で検索し、インスタンスが正常に作成されたことを確認します。 インスタンスを選択して開きます。

    ![SignalR Service インスタンスの検索](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. **[Key]\(キー\)** を選択し、SignalR Service インスタンスの接続文字列を表示します。

1. プライマリ接続文字列を選択してコピーします。

    ![プライマリ接続文字列を選択してコピーします。](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. コード エディターで、クローンしたリポジトリの *src/chat/python* フォルダーを開きます。
=======
>>>>>>> repo_sync_working_branch

## <a name="setup-and-run-the-azure-function-locally"></a>Azure Functions をローカルで設定して実行する

1. Azure Function Core Tools がインストールされていることを確認します。 空のディレクトリを作成し、コマンド ラインを使用してディレクトリに移動します。

    ```bash
    # Initialize a function project
    func init --worker-runtime python
    ```

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

        `index/__init__.py` を開いて次のコードをコピーします。

        ```javascript
        import os
    
        import azure.functions as func
        
        
        def main(req: func.HttpRequest) -> func.HttpResponse:
            f = open(os.path.dirname(os.path.realpath(__file__)) + '/../content/index.html')
            return func.HttpResponse(f.read(), mimetype='text/html')
        ```
    
    2. クライアントでアクセス トークンを取得するための `negotiate` 関数を作成します。
    
        ```bash
        func new -n negotiate -t SignalRNegotiateHTTPTrigger
        ```
        
        `negotiate/function.json` を開いて次の JSON コードをコピーします。
    
        ```json
        {
          "scriptFile": "__init__.py",
          "bindings": [
            {
              "authLevel": "anonymous",
              "type": "httpTrigger",
              "direction": "in",
              "name": "req",
              "methods": [
                "post"
              ]
            },
            {
              "type": "http",
              "direction": "out",
              "name": "$return"
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

        次に `negotiate/__init__.py` を開いて次のコードをコピーします。

        ```python
        import azure.functions as func
    
        
        def main(req: func.HttpRequest, connectionInfo) -> func.HttpResponse:
            return func.HttpResponse(connectionInfo)
        ```
    
    3. すべてのクライアントにメッセージをブロードキャストするための `broadcast` 関数を作成します。 このサンプルでは、時間トリガーを使用してメッセージを定期的にブロードキャストします。
    
        ```bash
        func new -n broadcast -t TimerTrigger
        # install requests
        pip install requests
        ```

        `broadcast/function.json` を開いて次のコードをコピーします。

        ```json
        {
          "scriptFile": "__init__.py",
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
    
        `broadcast/__init__.py` を開いて次のコードをコピーします。
    
        ```python
        import requests
        import json
        
        import azure.functions as func
        
        
        def main(myTimer: func.TimerRequest, signalRMessages: func.Out[str]) -> None:
            headers = {'User-Agent': 'serverless'}
            res = requests.get('https://api.github.com/repos/azure/azure-signalr', headers=headers)
            jres = res.json()
        
            signalRMessages.set(json.dumps({
                'target': 'newMessage',
                'arguments': [ 'Current star count of https://github.com/Azure/azure-signalr is: ' + str(jres['stargazers_count']) ]
            }))
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
    
4. もう少しで完了です。 最後の手順では、Azure Functions の設定に SignalR Service の接続文字列を設定します。

    1. Azure portal を開いているブラウザーのポータルの上部にある検索ボックスから、以前にデプロイした SignalR Service インスタンスの名前で検索し、インスタンスが正常に作成されたことを確認します。 インスタンスを選択して開きます。

        ![SignalR Service インスタンスの検索](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

    2. **[Key]\(キー\)** を選択し、SignalR Service インスタンスの接続文字列を表示します。
    
        ![プライマリ接続文字列が強調表示されているスクリーンショット。](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

    3. プライマリ接続文字列をコピーします。 そして、以下のコマンドを実行します。
    
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

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qspython)。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、リアルタイムのサーバーレス アプリケーションを構築してローカルで実行しました。 Azure Functions で SignalR Service のバインディングを使用する方法について確認します。
次に、SignalR Service を使用して、クライアントと Azure Functions の間で双方向通信を行う方法について詳しく説明します。

> [!div class="nextstepaction"]
> [Azure Functions における SignalR サービスのバインド](../azure-functions/functions-bindings-signalr-service.md)

> [!div class="nextstepaction"]
> [サーバーレスでの双方向通信](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/BidirectionChat)

> [!div class="nextstepaction"]
> [VS Code で Azure Functions をデプロイする](/azure/developer/javascript/tutorial-vscode-serverless-node-01)
