---
title: チュートリアル - WebSocket API と Azure Web PubSub サービス SDK を使用してメッセージの発行とサブスクライブを行う
description: Azure Web PubSub サービスと Azure Functions を使用してサーバーレス アプリケーションをビルドする方法を説明するチュートリアルです。
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 11/01/2021
ms.openlocfilehash: 13e3ee8db088db794c538e6da7af1a117c5ebd11
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345909"
---
# <a name="tutorial-publish-and-subscribe-messages-using-websocket-api-and-azure-web-pubsub-service-sdk"></a>チュートリアル: WebSocket API と Azure Web PubSub サービス SDK を使用してメッセージの発行とサブスクライブを行う

Azure Web PubSub サービスは、WebSocket とパブリッシュ-サブスクライブ パターンを使用して、リアルタイム メッセージング Web アプリケーションを作成するのに役立ちます。 このチュートリアルでは、WebSocket API を使用してサービスにサブスクライブし、サービス SDK を使用してメッセージを発行する方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Web PubSub サービス インスタンスを作成する
> * 完全な URL を生成して WebSocket 接続を確立する
> * 標準の WebSocket プロトコルを使用してメッセージをサブスクライブする
> * サービス SDK を使用してメッセージを発行する

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- このセットアップには、Azure CLI のバージョン 2.22.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-an-azure-web-pubsub-instance"></a>Azure Web PubSub インスタンスを作成する

### <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

### <a name="create-a-web-pubsub-instance"></a>Web PubSub インスタンスを作成する

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

### <a name="get-the-connectionstring-for-future-use"></a>将来使用するために ConnectionString を取得する

[!INCLUDE [Get the connection string](includes/cli-awps-connstr.md)]

フェッチされた **ConnectionString** をコピーします。これは、このチュートリアルの後半で `<connection_string>` の値として使用します。

## <a name="set-up-the-project"></a>プロジェクトのセットアップ
### <a name="prerequisites"></a>前提条件

# <a name="c"></a>[C#](#tab/csharp)

* [.NET Core 2.1 以降](https://dotnet.microsoft.com/download)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x 以降](https://nodejs.org)

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)

# <a name="java"></a>[Java](#tab/java)
- [Java Development Kit (JDK)](/java/azure/jdk/) バージョン 8 以降
- [Apache Maven](https://maven.apache.org/download.cgi)

---

## <a name="set-up-the-subscriber"></a>サブスクライバーをセットアップする

クライアントは、[JSON Web Token (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) 認証を使用して、標準の WebSocket プロトコルにより Azure Web PubSub サービスに接続します。 サービス SDK には、トークンを生成するヘルパー メソッドがあります。 このチュートリアルでは、サブスクライバーによって *ConnectionString* からトークンが直接生成されます。 実際のアプリケーションでは、通常、認証や認可のワークフローを処理するためにサーバー側アプリケーションを使用します。 ワークフローについての理解を深めるには、[チャット アプリの構築](./tutorial-build-chat.md)に関するチュートリアルを参照してください。

# <a name="c"></a>[C#](#tab/csharp)

1. まず、このプロジェクト用の新しいフォルダー `subscribe` を作成し、必要な依存関係をインストールします。
    * パッケージ [Websocket.Client](https://github.com/Marfusios/websocket-client) は WebSocket 接続をサポートするサードパーティーのパッケージです。 これを行うために、WebSocket をサポートする任意の API やライブラリを使用できます。
    * SDK パッケージ `Azure.Messaging.WebPubSub` を使用すると、JWT トークンを生成できます。 

    ```bash
    mkdir subscriber
    cd subscriber
    dotnet new console
    dotnet add package Websocket.Client --version 4.3.30
    dotnet add package Azure.Messaging.WebPubSub --version 1.0.0-beta.3
    ```

2. `Program.cs` ファイルを更新してサービスに接続します。

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.WebPubSub;
    using Websocket.Client;

    namespace subscriber
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                if (args.Length != 2)
                {
                    Console.WriteLine("Usage: subscriber <connectionString> <hub>");
                    return;
                }
                var connectionString = args[0];
                var hub = args[1];

                // Either generate the URL or fetch it from server or fetch a temp one from the portal
                var service = new WebPubSubServiceClient(connectionString, hub);
                var url = service.GenerateClientAccessUri();

                using (var client = new WebsocketClient(url))
                {
                    // Disable the auto disconnect and reconnect because the sample would like the client to stay online even no data comes in
                    client.ReconnectTimeout = null;
                    client.MessageReceived.Subscribe(msg => Console.WriteLine($"Message received: {msg}"));
                    await client.Start();
                    Console.WriteLine("Connected.");
                    Console.Read();
                }
            }
        }
    }
    ```

    上記のコードでは、Azure Web PubSub のハブに接続するための WebSocket 接続を作成します。 ハブは Azure Web PubSub の論理ユニットです。ここで、クライアントのグループにメッセージを発行できます。 [主要な概念](./key-concepts.md)に関するページには、Azure Web PubSub で使用される用語に関する詳細な説明があります。
    
    Azure Web PubSub サービスでは [JSON Web Token (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) 認証が使用されます。そのため、コード サンプルでは、Web PubSub SDK で `WebPubSubServiceClient.GenerateClientAccessUri()` を使用して、有効なアクセス トークンを持つ完全な URL を含むサービスへの URL を生成します。
    
    接続が確立されると、WebSocket 接続を介してメッセージを受信します。 そのため、受信メッセージをリッスンするために `client.MessageReceived.Subscribe(msg => ...));` を使用します。

3. [前の手順](#get-the-connectionstring-for-future-use)でフェッチされた **ConnectionString** で `<connection_string>` を置き換えて、下のコマンドを実行します。

    ```bash
    dotnet run "<connection_string>" "myHub1"
    ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. まず、このプロジェクト用の新しいフォルダー `subscriber` を作成し、必要な依存関係をインストールします。

    ```bash
    mkdir subscriber
    cd subscriber
    npm init -y
    npm install --save ws
    npm install --save @azure/web-pubsub@1.0.0-alpha.20211102.4

    ```
2. 次に、WebSocket API を使用してサービスに接続します。 下のコードを使用して `subscribe.js` ファイルを作成します。

    ```javascript
    const WebSocket = require('ws');
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    async function main() {
      const hub = "pubsub";
      let service = new WebPubSubServiceClient(process.env.WebPubSubConnectionString, hub);
      let token = await service.getClientAccessToken();
      let ws = new WebSocket(token.url);
      ws.on('open', () => console.log('connected'));
      ws.on('message', data => console.log('Message received: %s', data));
    }

    main();
    ```
    
    上記のコードでは、Azure Web PubSub のハブに接続するための WebSocket 接続を作成します。 ハブは Azure Web PubSub の論理ユニットです。ここで、クライアントのグループにメッセージを発行できます。 [主要な概念](./key-concepts.md)に関するページには、Azure Web PubSub で使用される用語に関する詳細な説明があります。
    
    Azure Web PubSub サービスでは [JSON Web Token (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) 認証が使用されます。そのため、コード サンプルでは、Web PubSub SDK で `WebPubSubServiceClient.getClientAccessToken()` を使用して、有効なアクセス トークンを持つ完全な URL を含むサービスへの URL を生成します。
    
    接続が確立されると、WebSocket 接続を介してメッセージを受信します。 そのため、受信メッセージをリッスンするために `WebSocket.on('message', ...)` を使用します。
    
3. [前の手順](#get-the-connectionstring-for-future-use)でフェッチされた **ConnectionString** で `<connection_string>` を置き換えて、下のコマンドを実行します。

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    node subscribe
    ```

# <a name="python"></a>[Python](#tab/python)

1. まず、このプロジェクト用の新しいフォルダー `subscriber` を作成し、必要な依存関係をインストールします。

    ```bash
    mkdir subscriber
    cd subscriber
    # Create venv
    python -m venv env
    # Activate venv
    source ./env/bin/activate

    pip install azure-messaging-webpubsubservice
    pip install websockets

    ```

2. 次に、WebSocket API を使用してサービスに接続します。 下のコードを使用して `subscribe.py` ファイルを作成します。

    ```python
    import asyncio
    import sys
    import websockets
    
    from azure.messaging.webpubsubservice import WebPubSubServiceClient
    
    
    async def connect(url):
        async with websockets.connect(url) as ws:
            print('connected')
            while True:
                print('Received message: ' + await ws.recv())
    
    if __name__ == '__main__':
    
        if len(sys.argv) != 3:
            print('Usage: python subscribe.py <connection-string> <hub-name>')
            exit(1)
    
        connection_string = sys.argv[1]
        hub_name = sys.argv[2]
    
        service = WebPubSubServiceClient.from_connection_string(connection_string, hub=hub_name)
        token = service.get_client_access_token()
    
        try:
            asyncio.get_event_loop().run_until_complete(connect(token['url']))
        except KeyboardInterrupt:
            pass
    
    ```

    上記のコードでは、Azure Web PubSub のハブに接続するための WebSocket 接続を作成します。 ハブは Azure Web PubSub の論理ユニットです。ここで、クライアントのグループにメッセージを発行できます。 [主要な概念](./key-concepts.md)に関するページには、Azure Web PubSub で使用される用語に関する詳細な説明があります。
    
    Azure Web PubSub サービスでは [JSON Web Token (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) 認証が使用されます。そのため、コード サンプルでは SDK で提供された `service.get_client_access_token()` を使用して、有効なアクセス トークンを持つ完全な URL を含むサービスへの URL を生成します。
    
    接続が確立されると、WebSocket 接続を介してメッセージを受信します。 そのため、受信メッセージをリッスンするために `await ws.recv()` を使用します。

3. [前の手順](#get-the-connectionstring-for-future-use)でフェッチされた **ConnectionString** で `<connection_string>` を置き換えて、下のコマンドを実行します。

    ```bash
    python subscribe.py "<connection_string>" "myHub1"
    ```

# <a name="java"></a>[Java](#tab/java)
1. まず、このチュートリアル用に新しいフォルダー `pubsub` を作成します。
    ```cmd
    mkdir pubsub
    cd pubsub
    ```

1. 次に、この `pubsub` フォルダーの中で Maven を使用して新しいコンソール アプリ `webpubsub-quickstart-subscriber` を作成し、*webpubsub-quickstart-subscriber* フォルダーに移動します。

    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.quickstart --define artifactId=webpubsub-quickstart-subscriber --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-quickstart-subscriber
    ```

2. WebSocket と Azure Web PubSub SDK の依存関係を `pom.xml` の `dependencies` ノードに追加します。

    * `azure-messaging-webpubsub`: Java 用 Web PubSub サービス SDK
    * `Java-WebSocket`: Java 用 WebSocket クライアント SDK

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.2</version>
    </dependency>

    <dependency>
        <groupId>org.java-websocket</groupId>
        <artifactId>Java-WebSocket</artifactId>
        <version>1.5.1</version>
    </dependency>

    ```

3. Azure Web PubSub では、サービスに接続し、WebSocket 接続を介してメッセージにサブスクライブできます。 WebSocket は全二重通信チャネルなので、サービスはリアルタイムでクライアントにメッセージをプッシュできます。 これを行うために、WebSocket をサポートする任意の API やライブラリを使用できます。 このサンプルでは、パッケージ [Java-WebSocket](https://github.com/TooTallNate/Java-WebSocket) を使用します。 */src/main/java/com/webpubsub/quickstart* ディレクトリに移動し、エディタで *App.java* ファイルをオープンして、下のコードに置き換えます。

    ```java
    
    package com.webpubsub.quickstart;

    import com.azure.messaging.webpubsub.*;
    import com.azure.messaging.webpubsub.models.*;
    
    import org.java_websocket.client.WebSocketClient;
    import org.java_websocket.handshake.ServerHandshake;

    import java.io.IOException;
    import java.net.URI;
    import java.net.URISyntaxException;

    /**
    * Connect to Azure Web PubSub service using WebSocket protocol
    *
    */
    public class App 
    {
        public static void main( String[] args ) throws IOException, URISyntaxException
        {
            if (args.length != 2) {
                System.out.println("Expecting 2 arguments: <connection-string> <hub-name>");
                return;
            }

            WebPubSubServiceClient service = new WebPubSubClientBuilder()
                .connectionString(args[0])
                .hub(args[1])
                .buildClient();

            WebPubSubAuthenticationToken token = service.getAuthenticationToken(new GetAuthenticationTokenOptions());

            WebSocketClient webSocketClient = new WebSocketClient(new URI(token.getUrl())) {
                @Override
                public void onMessage(String message) {
                    System.out.println(String.format("Message received: %s", message));
                }

                @Override
                public void onClose(int arg0, String arg1, boolean arg2) {
                    // TODO Auto-generated method stub
                }

                @Override
                public void onError(Exception arg0) {
                    // TODO Auto-generated method stub
                }

                @Override
                public void onOpen(ServerHandshake arg0) {
                    // TODO Auto-generated method stub
                }
                
            };

            webSocketClient.connect();
            System.in.read();
        }
    }

    ```

    上記のコードでは、Azure Web PubSub のハブに接続するための WebSocket 接続を作成します。 ハブは Azure Web PubSub の論理ユニットです。ここで、クライアントのグループにメッセージを発行できます。 [主要な概念](./key-concepts.md)に関するページには、Azure Web PubSub で使用される用語に関する詳細な説明があります。
    
    Azure Web PubSub サービスでは [JSON Web Token (JWT)](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) 認証が使用されます。そのため、コード サンプルでは、Web PubSub SDK で `WebPubSubServiceClient.getAuthenticationToken(new GetAuthenticationTokenOptions())` を使用して、有効なアクセス トークンを持つ完全な URL を含むサービスへの URL を生成します。
    
    接続が確立されると、WebSocket 接続を介してメッセージを受信します。 そのため、受信メッセージをリッスンするために `onMessage(String message)` を使用します。

4. *pom.xml* ファイルを含むディレクトリに移動し、下のコードを使用してアプリを実行します。その際に、[前の手順](#get-the-connectionstring-for-future-use)でフェッチされた **ConnectionString** で `<connection_string>` を置き換えます。

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>' 'myHub1'"
    ```

---

## <a name="publish-messages-using-service-sdk"></a>サービス SDK を使用してメッセージを発行する

次に、Azure Web PubSub SDK を使用して、接続されているクライアントにメッセージを発行します。

# <a name="c"></a>[C#](#tab/csharp)

1. まず、このプロジェクト用の新しいフォルダー `publisher` を作成し、必要な依存関係をインストールします。

    ```bash
    mkdir publisher
    cd publisher
    dotnet new console
    dotnet add package Azure.Messaging.WebPubSub --version 1.0.0-beta.3
    ```

2. 次に、`Program.cs` ファイルを更新し、`WebPubSubServiceClient` クラスを使用してクライアントにメッセージを送信します。

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.WebPubSub;

    namespace publisher
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                if (args.Length != 3) {
                    Console.WriteLine("Usage: publisher <connectionString> <hub> <message>");
                    return;
                }
                var connectionString = args[0];
                var hub = args[1];
                var message = args[2];

                var service = new WebPubSubServiceClient(connectionString, hub);
                
                // Send messages to all the connected clients
                // You can also try SendToConnectionAsync to send messages to the specific connection
                await service.SendToAllAsync(message);
            }
        }
    }
    ```

    `SendToAllAsync()` の呼び出しでは、単純にハブ内のすべての接続済みクライアントにメッセージが送信されます。

3. [前の手順](#get-the-connectionstring-for-future-use)でフェッチされた **ConnectionString** で `<connection_string>` を置き換えて、下のコマンドを実行します。

    ```bash
    dotnet run "<connection_string>" "myHub1" "Hello World"
    ```

4. 前のサブスクライバーが下のメッセージを受信したのを確認できます。
   
    ```
    Message received: Hello World
    ```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. まず、このプロジェクト用の新しいフォルダー `publisher` を作成し、必要な依存関係をインストールします。

    ```bash
    mkdir publisher
    cd publisher
    npm init -y
    npm install --save @azure/web-pubsub@1.0.0-alpha.20211102.4

    ```
2. 次に、Azure Web PubSub SDK を使用して、サービスにメッセージを発行しましょう。 下のコードを使用して `publish.js` ファイルを作成します。

    ```javascript
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    const hub = "pubsub";
    let service = new WebPubSubServiceClient(process.env.WebPubSubConnectionString, hub);

    // by default it uses `application/json`, specify contentType as `text/plain` if you want plain-text
    service.sendToAll(process.argv[2], { contentType: "text/plain" });
    ```

    `service.sendToAll()` の呼び出しでは、単純にハブ内の接続されているすべてのクライアントにメッセージが送信されます。

3. [前の手順](#get-the-connectionstring-for-future-use)でフェッチされた **ConnectionString** で `<connection_string>` を置き換えて、下のコマンドを実行します。

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    node publish "Hello World"
    ```

4. 前のサブスクライバーが下のメッセージを受信したのを確認できます。
   
    ```
    Message received: Hello World
    ```

# <a name="python"></a>[Python](#tab/python)

1. まず、このプロジェクト用の新しいフォルダー `publisher` を作成し、必要な依存関係をインストールします。
    ```bash
    mkdir publisher
    cd publisher
    # Create venv
    python -m venv env
    # Active venv
    source ./env/bin/activate

    pip install azure-messaging-webpubsubservice

    ```
2. 次に、Azure Web PubSub SDK を使用して、サービスにメッセージを発行しましょう。 下のコードを使用して `publish.py` ファイルを作成します。

    ```python
    import sys
    from azure.messaging.webpubsubservice import WebPubSubServiceClient
    
    if __name__ == '__main__':
    
        if len(sys.argv) != 4:
            print('Usage: python publish.py <connection-string> <hub-name> <message>')
            exit(1)
    
        connection_string = sys.argv[1]
        hub_name = sys.argv[2]
        message = sys.argv[3]
    
        service = WebPubSubServiceClient.from_connection_string(connection_string, hub=hub_name)
        res = service.send_to_all(message, content_type='text/plain')
        print(res)
    ```

    `send_to_all()` では、ハブ内の全ての接続済みクライアントにメッセージが送信されます。

3. [前の手順](#get-the-connectionstring-for-future-use)でフェッチされた **ConnectionString** で `<connection_string>` を置き換えて、下のコマンドを実行します。

    ```bash
    python publish.py "<connection_string>" "myHub1" "Hello World"
    ```

4. 前のサブスクライバーが下のメッセージを受信したのを確認できます。
   
    ```
    Received message: Hello World
    ```

# <a name="java"></a>[Java](#tab/java)

1.  別のターミナルを使用して `pubsub` フォルダーに戻り、パブリッシャー コンソール アプリ `webpubsub-quickstart-publisher` を作成して、*webpubsub-quickstart-publisher* フォルダーに移動します。
    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.quickstart --define artifactId=webpubsub-quickstart-publisher --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-quickstart-publisher
    ```

2. Azure Web PubSub SDK の依存関係を `pom.xml` の `dependencies` ノードに追加します。

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.2</version>
    </dependency>
    ```

3. 次に、Azure Web PubSub SDK を使用して、サービスにメッセージを発行しましょう。 */src/main/java/com/webpubsub/quickstart* ディレクトリに移動し、エディタで *App.java* ファイルをオープンして、下のコードに置き換えます。

    ```java
    package com.webpubsub.quickstart;

    import com.azure.messaging.webpubsub.*;
    import com.azure.messaging.webpubsub.models.*;

    /**
    * Publish messages using Azure Web PubSub service SDK
    *
    */
    public class App 
    {
        public static void main( String[] args )
        {
            if (args.length != 3) {
                System.out.println("Expecting 3 arguments: <connection-string> <hub-name> <message>");
                return;
            }

            WebPubSubServiceClient service = new WebPubSubClientBuilder()
                .connectionString(args[0])
                .hub(args[1])
                .buildClient();
            service.sendToAll(args[2], WebPubSubContentType.TEXT_PLAIN);
        }
    }

    ```

    `sendToAll()` の呼び出しでは、単純にハブ内のすべての接続済みクライアントにメッセージが送信されます。

4. *pom.xml* ファイルを含むディレクトリに移動し、下のコマンドを使用してプロジェクトを実行します。その際に、[前のステップ](#get-the-connectionstring-for-future-use)でフェッチされた **ConnectionString** で `<connection_string>` を置き換えます。

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.quickstart.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>' 'myHub1' 'Hello World'"
    ```

5. 前のサブスクライバーが下のメッセージを受信したのを確認できます。
   
    ```
    Message received: Hello World
    ```

---

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Web PubSub サービスに接続する方法と、接続されているクライアントにメッセージを発行する方法の基本的な考え方について説明しています。

サービスの使用方法の詳細については、他のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [チュートリアル: Azure Web PubSub を使用してチャット ルームを作成する](./tutorial-build-chat.md)

> [!div class="nextstepaction"]
> [Azure Web PubSub のサンプルをさらに確認する](https://aka.ms/awps/samples)
