---
title: Java を使用した Azure Functions と SignalR Service によるチャット ルームの作成
description: Azure SignalR Service と Azure Functions で Java を使用して、GitHub の星の数を表示するアプリを作成するクイックスタート。
author: sffamily
ms.author: zhshang
ms.date: 06/09/2021
ms.topic: quickstart
ms.service: signalr
ms.devlang: java
ms.custom: devx-track-java, mode-api
ms.openlocfilehash: 5cea13b3f6410df1d5a63efe4acdd080b21f2f5c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069580"
---
# <a name="quickstart-use-java-to-create-an-app-showing-github-star-count-with-azure-functions-and-signalr-service"></a>クイック スタート: Java を使用して Azure Functions と SignalR Service で GitHub の星の数を表示するアプリを作成する

Azure SignalR Service を使用すると、アプリケーションに対してリアルタイム機能を簡単に追加できます。Azure Functions は、インフラストラクチャを管理することなくコードを実行できるサーバーレス プラットフォームです。 このクイックスタートでは、SignalR Service と Azure Functions を使用して、Java でサーバーレス アプリケーションを構築し、クライアントにメッセージをブロードキャストする方法について説明します。

> [!NOTE]
> この記事に記載されているコードはすべて、[GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/QuickStartServerless/java) で入手することができます

## <a name="prerequisites"></a>前提条件

- コード エディター ([Visual Studio Code](https://code.visualstudio.com/) など)。
- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing)。 Azure 関数アプリをローカルに実行する際に使用します。

   > [!NOTE]
   > Java では、必須の SignalR Service バインディングが Azure Function Core Tools バージョン 2.4.419 (ホスト バージョン 2.0.12332) 以降でのみサポートされます。

   > [!NOTE]
   > Azure Functions Core Tools では、拡張機能をインストールするために、[.NET Core SDK](https://dotnet.microsoft.com/download) がインストールされている必要があります。 ただし、JavaScript Azure Functions アプリの構築には、.NET の知識は必要ありません。

- [Java Developer Kit](https://www.azul.com/downloads/zulu/)、バージョン 11
- [Apache Maven](https://maven.apache.org)、バージョン 3.0 以降

> [!NOTE]
> このクイック スタートは、macOS、Windows、または Linux で実行できます。

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsjava)。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure アカウントで Azure Portal (<https://portal.azure.com/>) にサインインします。

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsjava)。

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsjava)。


## <a name="configure-and-run-the-azure-function-app"></a>Azure Function アプリを構成して実行する

1. Azure Function Core Tools、Java (サンプルではバージョン 11)、Maven がインストールされていることを確認します。
    
    ```bash
    mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=11
    ```

    Maven により、プロジェクトの生成を終了するために必要な値が要求されます。 次の値を指定できます。

    | Prompt | 値 | 説明 |
    | ------ | ----- | ----------- |
    | **groupId** | `com.signalr` | Java の[パッケージ命名規則](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7)に従って、すべてのプロジェクトにわたって対象のプロジェクトを一意に識別する値。 |
    | **artifactId** | `java` | バージョン番号のない、jar の名前である値。 |
    | **version** | `1.0-SNAPSHOT` | 既定値を選択します。 |
    | **package** | `com.signalr` | 生成された関数コードの Java パッケージである値。 既定値を使用します。 |   

2. プロジェクトを初期化した後。 フォルダー `src/main/java/com/signalr` に移動し、次のコードを `Function.java` にコピーします

    ```java
    package com.signalr;
  
    import com.google.gson.Gson;
    import com.microsoft.azure.functions.ExecutionContext;
    import com.microsoft.azure.functions.HttpMethod;
    import com.microsoft.azure.functions.HttpRequestMessage;
    import com.microsoft.azure.functions.HttpResponseMessage;
    import com.microsoft.azure.functions.HttpStatus;
    import com.microsoft.azure.functions.annotation.AuthorizationLevel;
    import com.microsoft.azure.functions.annotation.FunctionName;
    import com.microsoft.azure.functions.annotation.HttpTrigger;
    import com.microsoft.azure.functions.annotation.TimerTrigger;
    import com.microsoft.azure.functions.signalr.*;
    import com.microsoft.azure.functions.signalr.annotation.*;
    
    import org.apache.commons.io.IOUtils;
    
    
    import java.io.IOException;
    import java.io.InputStream;
    import java.net.URI;
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.net.http.HttpResponse.BodyHandlers;
    import java.nio.charset.StandardCharsets;
    import java.util.Optional;
    
    public class Function {
        @FunctionName("index")
        public HttpResponseMessage run(
                @HttpTrigger(
                    name = "req",
                    methods = {HttpMethod.GET},
                    authLevel = AuthorizationLevel.ANONYMOUS)HttpRequestMessage<Optional<String>> request,
                final ExecutionContext context) throws IOException {
            
            InputStream inputStream = getClass().getClassLoader().getResourceAsStream("content/index.html");
            String text = IOUtils.toString(inputStream, StandardCharsets.UTF_8.name());
            return request.createResponseBuilder(HttpStatus.OK).header("Content-Type", "text/html").body(text).build();
        }
  
        @FunctionName("negotiate")
        public SignalRConnectionInfo negotiate(
                @HttpTrigger(
                    name = "req",
                    methods = { HttpMethod.POST },
                    authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> req,
                @SignalRConnectionInfoInput(
                    name = "connectionInfo",
                    hubName = "serverless") SignalRConnectionInfo connectionInfo) {
                    
            return connectionInfo;
        }
    
        @FunctionName("broadcast")
        @SignalROutput(name = "$return", hubName = "serverless")
        public SignalRMessage broadcast(
            @TimerTrigger(name = "timeTrigger", schedule = "*/5 * * * * *") String timerInfo) throws IOException, InterruptedException {
            
            HttpClient client = HttpClient.newHttpClient();
            HttpRequest req = HttpRequest.newBuilder().uri(URI.create("https://api.github.com/repos/azure/azure-signalr")).header("User-Agent", "serverless").build();
            HttpResponse<String> res = client.send(req, BodyHandlers.ofString());
            Gson gson = new Gson();
            GitResult result = gson.fromJson(res.body(), GitResult.class);
            return new SignalRMessage("newMessage", "Current star count of https://github.com/Azure/azure-signalr is:".concat(result.stargazers_count));
        }
    
        class GitResult {
            public String stargazers_count;
        }
    }
    ```

3. 依存関係をいくつか追加する必要があります。 そのため、`pom.xml` を開き、コードで使用されている依存関係を追加します。

    ```xml
    <dependency>
        <groupId>com.microsoft.azure.functions</groupId>
        <artifactId>azure-functions-java-library-signalr</artifactId>
        <version>1.0.0</version>
    </dependency>
    <dependency>
        <groupId>commons-io</groupId>
        <artifactId>commons-io</artifactId>
        <version>2.4</version>
    </dependency>
    <dependency>
        <groupId>com.google.code.gson</groupId>
        <artifactId>gson</artifactId>
        <version>2.8.7</version>
    </dependency>
    ```

4. このサンプルのクライアント インターフェイスは Web ページです。 `index` 関数の `content/index.html` から HTML コンテンツを読み込むと考え、`resources` ディレクトリに `content/index.html` というファイルを新規に作成します。 ディレクトリ ツリーは次のようになります。
    
    ```
    FunctionsProject
     | - src
     | | - main
     | | | - java
     | | | | - com
     | | | | | - signalr 
     | | | | | | - Function.java
     | | | - resources
     | | | | - content
     | | | | | - index.html
     | - pom.xml
     | - host.json
     | - local.settings.json
    ```

    `index.html` を開き、次の内容をコピーします。

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

5. もう少しで完了です。 最後の手順では、Azure Functions の設定に SignalR Service の接続文字列を設定します。

    1. Azure portal を開いているブラウザーのポータルの上部にある検索ボックスから、以前にデプロイした SignalR Service インスタンスの名前で検索し、インスタンスが正常に作成されたことを確認します。 インスタンスを選択して開きます。

        ![SignalR Service インスタンスの検索](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

    1. **[Key]\(キー\)** を選択し、SignalR Service インスタンスの接続文字列を表示します。
    
        ![プライマリ接続文字列が強調表示されているスクリーンショット。](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

    1. プライマリ接続文字列をコピーします。 そして、以下のコマンドを実行します。
    
        ```bash
        func settings add AzureSignalRConnectionString "<signalr-connection-string>"
        # Also we need to set AzureWebJobsStorage as Azure Function's requirement
        func settings add AzureWebJobsStorage "UseDevelopmentStorage=true"
        ```
    
6. Azure Functions をローカルで実行します:

    ```bash
    mvn clean package
    mvn azure-functions:run
    ```

    その後、Azure Functions がローカルで実行されます。 ブラウザーを使用して `http://localhost:7071/api/index` にアクセスすると、現在の星の数を確認できます。 また、GitHub で星を付けたり外したりすると、数秒ごとに星の数が更新されます。

    > [!NOTE]
    > SignalR のバインディングには Azure Storage が必要ですが、Function がローカルで動作している場合は、ローカル ストレージ エミュレーターを使用することができます。
    > `There was an error performing a read operation on the Blob Storage Secret Repository. Please ensure the 'AzureWebJobsStorage' connection string is valid.` のようなエラーが発生した場合は、[Storage Emulator](../storage/common/storage-use-emulator.md) をダウンロードして有効にする必要があります
    
問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsjava)。


[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsjava)。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、リアルタイムのサーバーレス アプリケーションを構築してローカルで実行しました。 Azure Functions で SignalR Service のバインディングを使用する方法について確認します。
次に、SignalR Service を使用して、クライアントと Azure Functions の間で双方向通信を行う方法について詳しく説明します。

> [!div class="nextstepaction"]
> [Azure Functions における SignalR サービスのバインド](../azure-functions/functions-bindings-signalr-service.md)

> [!div class="nextstepaction"]
> [サーバーレスでの双方向通信](https://github.com/aspnet/AzureSignalR-samples/tree/main/samples/BidirectionChat)

> [!div class="nextstepaction"]
> [Java と Maven を使用して初めての関数を作成する](../azure-functions/create-first-function-cli-csharp.md?pivots=programming-language-java%2cprogramming-language-java)
