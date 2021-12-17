---
title: Azure SignalR Service サーバーレス クイック スタート - C#
description: Azure SignalR Service と Azure Functions で C# を使用して、GitHub のスターの数を表示するアプリを作成するクイックスタート。
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 06/09/2021
ms.author: zhshang
ms.openlocfilehash: 3a3fa958ac6ad1cb440f30b5c680ae3f9139d29a
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444925"
---
# <a name="quickstart-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service-using-c"></a>クイック スタート: Azure Functions と SignalR Service で C\# を使用して、GitHub のスターの数を表示するアプリを作成する

Azure SignalR Service を使用すると、アプリケーションにリアルタイム機能を簡単に追加できます。 Azure Functions は、インフラストラクチャを管理することなくコードを実行できるサーバーレス プラットフォームです。 このクイックスタートでは、SignalR Service と Azure Functions を使用して、C# を用いてサーバーレス アプリケーションを構築し、クライアントにメッセージをブロードキャストする方法について説明します。

> [!NOTE]
> この記事に記載されているコードはすべて、[GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/csharp) で入手することができます

## <a name="prerequisites"></a>前提条件

Visual Studio Code がまだインストールされていない場合は、無料でダウンロードして使用できます (https://code.visualstudio.com/Download) )。

[Azure Functions Core Tools](../azure-functions/functions-run-local.md?tabs=windows%2Ccsharp%2Cbash#v2) を使用して、このチュートリアルをコマンド ライン (macOS、Windows、または Linux) で実行することもできます。 さらに、[.NET Core SDK](https://dotnet.microsoft.com/download) または任意のコード エディター。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料でアカウントを 1 つ](https://azure.microsoft.com/free/dotnet)作成してください。

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qscsharp)。

## <a name="log-in-to-azure-and-create-signalr-service-instance"></a>Azure へのログインと SignalR Service インスタンスの作成

Azure アカウントで Azure Portal (<https://portal.azure.com/>) にサインインします。

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qscsharp)。

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qscsharp)。

<<<<<<< HEAD
[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qscsharp)。

## <a name="configure-and-run-the-azure-function-app"></a>Azure Function アプリを構成して実行する

1. Visual Studio (または他のコード エディター) を起動し、クローンしたリポジトリの *src/chat/csharp* フォルダーにあるソリューションを開きます。

1. Azure portal を開いているブラウザーのポータルの上部にある検索ボックスから、以前にデプロイした SignalR Service インスタンスの名前で検索し、インスタンスが正常に作成されたことを確認します。 インスタンスを選択して開きます。

    ![SignalR Service インスタンスの検索](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. **[Key]\(キー\)** を選択し、SignalR Service インスタンスの接続文字列を表示します。

1. プライマリ接続文字列を選択してコピーします。

1. Visual Studio の **ソリューション エクスプローラー** に戻り、*local.settings.sample.json* の名前を *local.settings.json* に変更します。

1. *local.settings.json* で、**AzureSignalRConnectionString** 設定に接続文字列の値を貼り付けます。 ファイルを保存します。

1. *Functions.cs* を開きます。 この関数アプリには、HTTP によってトリガーされる関数が 2 つあります。

    - **GetSignalRInfo** - `SignalRConnectionInfo` 入力バインディングを使用し、有効な接続情報を生成して返します。
    - **SendMessage** - 要求本文でチャット メッセージを受信し、*SignalR* 出力バインディングを使用して接続されているすべてのクライアント アプリケーションにメッセージをブロードキャストします。

1. Azure 関数アプリをローカルで起動するには、次のオプションのいずれかを使用します。

    - **Visual Studio**: *[デバッグ]* メニューの *[デバッグの開始]* を選択してアプリケーションを実行します。

        ![アプリケーションのデバッグ](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-debug-vs.png)

    - **コマンド ライン**:次のコマンドを実行して、関数ホストを開始します。

=======
## <a name="setup-and-run-the-azure-function-locally"></a>Azure Functions をローカルで設定して実行する

1. Azure Function Core Tools がインストールされていることを確認します。 空のディレクトリを作成し、コマンド ラインを使用してディレクトリに移動します。

    ```bash
    # Initialize a function project
    func init --worker-runtime dotnet
    
    # Add SignalR Service package reference to the project
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService
    ```

2. プロジェクトを初期化した後。 *Function.cs* という名前の新しいファイルを作成します。 次のコードを *Function.cs* に追加します。
   
    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Http;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.Azure.WebJobs.Extensions.SignalRService;
    using Newtonsoft.Json;
    
    namespace CSharp
    {
        public static class Function
        {
            private static HttpClient httpClient = new HttpClient();
    
            [FunctionName("index")]
            public static IActionResult Index([HttpTrigger(AuthorizationLevel.Anonymous)]HttpRequest req, ExecutionContext context)
            {
                var path = Path.Combine(context.FunctionAppDirectory, "content", "index.html");
                return new ContentResult
                {
                    Content = File.ReadAllText(path),
                    ContentType = "text/html",
                };
            }
    
            [FunctionName("negotiate")]
            public static SignalRConnectionInfo Negotiate( 
                [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req,
                [SignalRConnectionInfo(HubName = "serverlessSample")] SignalRConnectionInfo connectionInfo)
            {
                return connectionInfo;
            }
    
            [FunctionName("broadcast")]
            public static async Task Broadcast([TimerTrigger("*/5 * * * * *")] TimerInfo myTimer,
            [SignalR(HubName = "serverlessSample")] IAsyncCollector<SignalRMessage> signalRMessages)
            {
                var request = new HttpRequestMessage(HttpMethod.Get, "https://api.github.com/repos/azure/azure-signalr");
                request.Headers.UserAgent.ParseAdd("Serverless");
                var response = await httpClient.SendAsync(request);
                var result = JsonConvert.DeserializeObject<GitResult>(await response.Content.ReadAsStringAsync());
                await signalRMessages.AddAsync(
                    new SignalRMessage
                    {
                        Target = "newMessage",
                        Arguments = new[] { $"Current star count of https://github.com/Azure/azure-signalr is: {result.StarCount}" }
                    });
            }
    
            private class GitResult
            {
                [JsonRequired]
                [JsonProperty("stargazers_count")]
                public string StarCount { get; set; }
            }
        }
    }
    ```
    これらのコードには 3 つの関数があります。 `Index` は、Web サイトをクライアントとして取得するために使用されます。 `Negotiate` は、クライアントがアクセス トークンを取得するために使用されます。 `Broadcast` は、GitHub から定期的に星の数を取得し、すべてのクライアントにメッセージをブロードキャストします。

3. このサンプルのクライアント インターフェイスは Web ページです。 `GetHomePage` 関数で `content/index.html` から HTML コンテンツを読み込むと考え、プロジェクト ルート フォルダーの下の `content` ディレクトリに新たに `index.html` ファイルを作成します。 そして、次の内容をコピーします。
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

4. `*.csproj` を更新して、ビルド出力フォルダー内にコンテンツ ページを作成します。

    ```html
    <ItemGroup>
      <None Update="content/index.html">
        <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
      </None>
    </ItemGroup>
    ```

5. もう少しで完了です。 最後の手順では、Azure Functions の設定に SignalR Service の接続文字列を設定します。

    1. Azure portal を開いているブラウザーのポータルの上部にある検索ボックスから、以前にデプロイした SignalR Service インスタンスの名前で検索し、インスタンスが正常に作成されたことを確認します。 インスタンスを選択して開きます。

        ![SignalR Service インスタンスの検索](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

    2. **[Key]\(キー\)** を選択し、SignalR Service インスタンスの接続文字列を表示します。
    
        ![プライマリ接続文字列が強調表示されているスクリーンショット。](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

    3. プライマリ接続文字列をコピーします。 そして、以下のコマンドを実行します。
    
>>>>>>> repo_sync_working_branch
        ```bash
        func settings add AzureSignalRConnectionString "<signalr-connection-string>"
        ```
    
6. Azure Functions をローカルで実行します:

    ```bash
    func start
    ```

    その後、Azure Functions がローカルで実行されます。 ブラウザーを使用して `http://localhost:7071/api/index` にアクセスすると、現在の星の数を確認できます。 また、GitHub で星を付けたり外したりすると、数秒ごとに星の数が更新されます。

    > [!NOTE]
    > SignalR のバインディングには Azure Storage が必要ですが、Function がローカルで動作している場合は、ローカル ストレージ エミュレーターを使用することができます。
    > `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.` のようなエラーが発生した場合は、[Storage Emulator](../storage/common/storage-use-emulator.md) をダウンロードして有効にする必要があります

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qscsharp)

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
> [Visual Studio を使用して Azure Function App にデプロイする](../azure-functions/functions-develop-vs.md#publish-to-azure)

