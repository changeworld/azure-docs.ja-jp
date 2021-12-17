---
title: チュートリアル - Azure Web PubSub サービスを使用してチャット アプリを作成する
description: Azure Web PubSub サービスを使用してチャット アプリを作成する方法を説明するチュートリアル
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 11/01/2021
ms.openlocfilehash: 38890a5bf968bf88678e0665847fb067708291c3
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547262"
---
# <a name="tutorial-create-a-chat-app-with-azure-web-pubsub-service"></a>チュートリアル: Azure Web PubSub サービスを使用してチャット アプリを作成する

[メッセージの発行とサブスクライブに関するチュートリアル](./tutorial-pub-sub-messages.md)では、Azure Web PubSub を使用したメッセージの発行とサブスクライブの基本について学習しました。 このチュートリアルでは、Azure Web PubSub のイベント システムについて学習し、これを使用してリアルタイム通信機能を備えた完全な Web アプリケーションを作成します。 

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Web PubSub サービス インスタンスを作成する
> * Azure Web PubSub のイベント ハンドラー設定を構成する
> * リアルタイムのチャット アプリを作成する

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

* [ASP.NET Core 3.1 以降](/aspnet/core)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

* [Node.js 12.x 以降](https://nodejs.org)

# <a name="java"></a>[Java](#tab/java)

- [Java Development Kit (JDK)](/java/azure/jdk/) バージョン 8 以降
- [Apache Maven](https://maven.apache.org/download.cgi)

---

## <a name="create-the-application"></a>アプリケーションを作成する

Azure Web PubSub には、サーバーとクライアントの 2 つのロールがあります。 この概念は、Web アプリケーションにおけるサーバーとクライアントのロールに似ています。 サーバーはクライアントを管理し、クライアントのメッセージをリッスンして応答する役割を担います。一方、クライアントのロールはユーザーのメッセージをサーバーに送り、サーバーからメッセージを受け取ってエンド ユーザーに視覚化することです。

このチュートリアルでは、リアル タイム チャット Web アプリケーションを作成します。 実際の Web アプリケーションでは、サーバーの役割には、クライアントの認証や、アプリケーション UI のための静的 Web ページの提供も含まれます。 

# <a name="c"></a>[C#](#tab/csharp)

[ASP.NET Core](/aspnet/core) を使用して Web ページをホストし、受信要求を処理します。

最初に、空の ASP.NET Core アプリを作成しましょう。

1.  Web アプリを作成する

    ```bash
    dotnet new web
    dotnet add package Azure.Messaging.WebPubSub
    ```

2.  次に、静的ファイルをサポートするために、`Startup.cs` の `app.UseRouting();` の前に `app.UseStaticFiles();` を追加します。 `app.UseEndpoints` 内の既定の `endpoints.MapGet` を除去します。

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseStaticFiles();

        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
        });
    }
    ```

3.  また、HTML ファイルを作成して `wwwroot/index.html` として保存します。これは、後でチャット アプリの UI に使用します。

    ```html
    <html>
    <body>
      <h1>Azure Web PubSub Chat</h1>
    </body>

    </html>
    ```

`dotnet run --urls http://localhost:8080` を実行し、ブラウザーで http://localhost:8080/index.html にアクセスすることで、サーバーをテストできます。

[メッセージの発行とサブスクライブに関するチュートリアル](./tutorial-pub-sub-messages.md)では、サブスクライバーが Web PubSub SDK の API を使用して接続文字列からアクセス トークンを生成し、それを使用してサービスに接続することを確認しました。 これは、通常は実際のアプリケーションでは安全ではありません。接続文字列にはサービスに対してあらゆる操作を実行するための高い特権があるためです。そのため、これをクライアントと共有することは推奨されません。 このアクセス トークン生成プロセスをサーバー側の REST API に変更しましょう。そうすれば、クライアントは接続で必要になるたびにこの API を呼び出してアクセス トークンを要求でき、接続文字列を保持する必要もありません。

1.  依存関係をインストールし、.NET Core 用の[シークレット マネージャー](/aspnet/core/security/app-secrets#secret-manager) ツールを使用して接続文字列を設定します。 `<connection_string>` を、[前の手順](#get-the-connectionstring-for-future-use)でフェッチされたものに置き換えて下のコマンドを実行します

    ```bash
    dotnet add package Microsoft.Extensions.Azure
    dotnet user-secrets init
    dotnet user-secrets set Azure:WebPubSub:ConnectionString "<connection-string>"
    ```

2. `ConfigureServices` 内のサービス クライアントを DI し、忘れずに `<connection_string>` を自分のサービスの 1 つに置き換えます。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAzureClients(builder =>
        {
            builder.AddWebPubSubServiceClient(Configuration["Azure:WebPubSub:ConnectionString"], "chat");
        });
    }
    ```
2.  `/negotiate` API を `app.UseEndpoints` 内のサーバーに追加してトークンを生成します

    ```csharp
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGet("/negotiate", async context =>
        {
            var id = context.Request.Query["id"];
            if (id.Count != 1)
            {
                context.Response.StatusCode = 400;
                await context.Response.WriteAsync("missing user id");
                return;
            }
            var serviceClient = context.RequestServices.GetRequiredService<WebPubSubServiceClient>();
            await context.Response.WriteAsync(serviceClient.GetClientAccessUri(userId: id).AbsoluteUri);
        });
    });
    ```

    このトークン生成コードは、[メッセージの発行とサブスクライブに関するチュートリアル](./tutorial-pub-sub-messages.md)で使用したものと似ていますが、トークンを生成するときにもう 1 つの引数 (`userId`) を渡す点が異なります。 ユーザー ID を使用してクライアントの ID を識別すると、メッセージを受信したとき、メッセージがどこから来たかがわかります。

    この API は、`dotnet run --urls http://localhost:8080` を実行して `http://localhost:8080/negotiate?id=<user-id>` にアクセスすることでテストできます。これにより、アクセス トークンを持つ Azure Web PubSub の完全な URL が提供されます。

3.  次に、`index.html` を更新して次のスクリプトを含め、サーバーからトークンを取得し、サービスに接続します
 
    ```html
    <html>
      <body>
        <h1>Azure Web PubSub Chat</h1>
      </body>
  
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');
        })();
      </script>
    </html>
    ```

    Chrome を使用している場合は、ホーム ページを開き、自分のユーザー名を入力してテストできます。 F12 キーを押して開発者ツール ウィンドウを開き、**コンソール** テーブルに切り替えます。`connected` がブラウザー コンソールに出力されているのがわかります。


# <a name="javascript"></a>[JavaScript](#tab/javascript)

このジョブを実現するために、node.js の一般的な Web フレームワークである [express.js](https://expressjs.com/) を使用します。

まず、空の express アプリを作成します。

1.  express.js をインストールします

    ```bash
    npm init -y
    npm install --save express
    ```

2.  次に express サーバーを作成し、`server.js` として保存します

    ```javascript
    const express = require('express');

    const app = express();
    app.use(express.static('public'));
    app.listen(8080, () => console.log('server started'));
    ```

3.  また、HTML ファイルを作成して `public/index.html` として保存します。これは、後でチャット アプリの UI に使用します。

    ```html
    <html>

    <body>
      <h1>Azure Web PubSub Chat</h1>
    </body>

    </html>
    ```

`node server` を実行し、ブラウザーで http://localhost:8080 にアクセスすることで、サーバーをテストできます。


[メッセージの発行とサブスクライブに関するチュートリアル](./tutorial-pub-sub-messages.md)では、サブスクライバーが Web PubSub SDK の API を使用して接続文字列からアクセス トークンを生成し、それを使用してサービスに接続することを確認しました。 これは、通常は実際のアプリケーションでは安全ではありません。接続文字列にはサービスに対してあらゆる操作を実行するための高い特権があるためです。そのため、これをクライアントと共有することは推奨されません。 このアクセス トークン生成プロセスをサーバー側の REST API に変更しましょう。そうすれば、クライアントは接続で必要になるたびにこの API を呼び出してアクセス トークンを要求でき、接続文字列を保持する必要もありません。

1.  Azure Web PubSub SDK をインストールします

    ```bash
    npm install --save @azure/web-pubsub
    ```

2.  `/negotiate` API をサーバーに追加してトークンを生成します

    ```javascript
    const express = require('express');
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    const app = express();
    const hubName = 'chat';
    const port = 8080;

    let serviceClient = new WebPubSubServiceClient(process.env.WebPubSubConnectionString, hubName);

    app.get('/negotiate', async (req, res) => {
      let id = req.query.id;
      if (!id) {
        res.status(400).send('missing user id');
        return;
      }
      let token = await serviceClient.getClientAccessToken({ userId: id });
      res.json({
        url: token.url
      });
    });

    app.use(express.static('public'));
    app.listen(8080, () => console.log('server started'));
    ```

    このトークン生成コードは、[メッセージの発行とサブスクライブに関するチュートリアル](./tutorial-pub-sub-messages.md)で使用したものと似ていますが、トークンを生成するときにもう 1 つの引数 (`userId`) を渡す点が異なります。 ユーザー ID を使用してクライアントの ID を識別すると、メッセージを受信したとき、メッセージがどこから来たかがわかります。

    次のコマンドを実行して、この API をテストします。

    ```bash
    export WebPubSubConnectionString="<connection-string>"
    node server
    ```

    `http://localhost:8080/negotiate?id=<user-id>` にアクセスします。これにより、アクセス トークンを持つ Azure Web PubSub の完全な URL が提供されます。

3.  次に、`index.html` を次のスクリプトで更新してサーバーからトークンを取得し、サービスに接続します
 
    ```html

    <html>
      <body>
        <h1>Azure Web PubSub Chat</h1>
      </body>
  
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let data = await res.json();
          let ws = new WebSocket(data.url);
          ws.onopen = () => console.log('connected');
        })();
      </script>
    </html>
    ```

    Chrome を使用している場合は、ホーム ページを開き、自分のユーザー名を入力してテストできます。 F12 キーを押して開発者ツール ウィンドウを開き、**コンソール** テーブルに切り替えます。`connected` がブラウザー コンソールに出力されているのがわかります。

# <a name="java"></a>[Java](#tab/java)

[Javalin](https://javalin.io/) Web フレームワークを使用して Web ページをホストし、受信要求を処理します。

1. まず、Maven を使用して新しいアプリ `webpubsub-tutorial-chat` を作成し、*webpubsub-tutorial-chat* フォルダーに切り替えます。

    ```console
    mvn archetype:generate --define interactiveMode=n --define groupId=com.webpubsub.tutorial --define artifactId=webpubsub-tutorial-chat --define archetypeArtifactId=maven-archetype-quickstart --define archetypeVersion=1.4
    cd webpubsub-tutorial-chat
    ```

2. `javalin` Web フレームワークの依存関係を `pom.xml` の `dependencies` ノードに追加しましょう。

    * `javalin`: Java 用の単純な Web フレームワーク
    * `slf4j-simple`: Java 用ロガー

    ```xml
    <!-- https://mvnrepository.com/artifact/io.javalin/javalin -->
    <dependency>
        <groupId>io.javalin</groupId>
        <artifactId>javalin</artifactId>
        <version>3.13.6</version>
    </dependency>

    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-simple</artifactId>
        <version>1.7.30</version>
    </dependency>
    ```

3. */src/main/java/com/webpubsub/tutorial* ディレクトリに移動し、*App.java* ファイルをエディタで開き、`Javalin.create` を使用して静的ファイルを提供します。

    ```java
    package com.webpubsub.tutorial;
    
    import io.javalin.Javalin;
    
    public class App {
        public static void main(String[] args) {
            // start a server
            Javalin app = Javalin.create(config -> {
                config.addStaticFiles("public");
            }).start(8080);
        }
    }
    ```

    設定によっては、明示的に言語レベルを Java 8 に設定する必要がある場合があります。 これは pom.xml で行うことができます。 次のスニペットを追加します。
    ```xml
    <build>
        <plugins>
            <plugin>
              <artifactId>maven-compiler-plugin</artifactId>
              <version>3.8.0</version>
              <configuration>
                <source>1.8</source>
                <target>1.8</target>
              </configuration>
            </plugin>
        </plugins>
    </build>
    ```

4. HTML ファイルを作成して、 */src/main/resources/public/index.html* に保存しましょう。 それは、後でチャット アプリの UI に使用します。

    ```html
    <html>
    <body>
      <h1>Azure Web PubSub Chat</h1>
    </body>

    </html>
    ```

サーバーをテストするには、*pom.xml* ファイルを含むディレクトリの下で次のコマンドを実行し、ブラウザーで http://localhost:8080 にアクセスします。

```console
mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false
```

[メッセージの発行とサブスクライブに関するチュートリアル](./tutorial-pub-sub-messages.md)では、サブスクライバーが Web PubSub SDK の API を使用して接続文字列からアクセス トークンを生成し、それを使用してサービスに接続することを確認しました。 これは、通常は実際のアプリケーションでは安全ではありません。接続文字列にはサービスに対してあらゆる操作を実行するための高い特権があるためです。そのため、これをクライアントと共有することは推奨されません。 このアクセス トークン生成プロセスをサーバー側の REST API に変更しましょう。そうすれば、クライアントは接続で必要になるたびにこの API を呼び出してアクセス トークンを要求でき、接続文字列を保持する必要もありません。

1. Azure Web PubSub SDK の依存関係を `pom.xml` の `dependencies` ノードに追加します。

    ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-webpubsub</artifactId>
        <version>1.0.0-beta.6</version>
    </dependency>
    ```

2. `/negotiate` API を `App.java` ファイルに追加してトークンを生成します。

    ```java
    package com.webpubsub.tutorial;
    
    import com.azure.messaging.webpubsub.WebPubSubServiceClient;
    import com.azure.messaging.webpubsub.WebPubSubServiceClientBuilder;
    import com.azure.messaging.webpubsub.models.GetClientAccessTokenOptions;
    import com.azure.messaging.webpubsub.models.WebPubSubClientAccessToken;
    import com.azure.messaging.webpubsub.models.WebPubSubContentType;
    import io.javalin.Javalin;
    
    public class App {
        public static void main(String[] args) {
            
            if (args.length != 1) {
                System.out.println("Expecting 1 arguments: <connection-string>");
                return;
            }
    
            // create the service client
            WebPubSubServiceClient service = new WebPubSubServiceClientBuilder()
                    .connectionString(args[0])
                    .hub("chat")
                    .buildClient();
    
            // start a server
            Javalin app = Javalin.create(config -> {
                config.addStaticFiles("public");
            }).start(8080);
    
            
            // Handle the negotiate request and return the token to the client
            app.get("/negotiate", ctx -> {
                String id = ctx.queryParam("id");
                if (id == null) {
                    ctx.status(400);
                    ctx.result("missing user id");
                    return;
                }
                GetClientAccessTokenOptions option = new GetClientAccessTokenOptions();
                option.setUserId(id);
                WebPubSubClientAccessToken token = service.getClientAccessToken(option);
    
                ctx.result(token.getUrl());
                return;
            });
        }
    }
    ```

    このトークン生成コードは、[メッセージの発行とサブスクライブに関するチュートリアル](./tutorial-pub-sub-messages.md)で使用したものと似ていますが、トークンを生成するときに `setUserId` メソッドを呼び出してユーザー ID を設定する点が異なります。 ユーザー ID を使用してクライアントの ID を識別すると、メッセージを受信したとき、メッセージがどこから来たかがわかります。

    この API をテストするには、次のコマンドを実行し、`<connection_string>` を [前の手順](#get-the-connectionstring-for-future-use)でフェッチした **ConnectionString** に置き換え、`http://localhost:8080/negotiate?id=<user-id>` にアクセスします。これにより、アクセス トークンを持つ Azure Web PubSub の完全な URL が提供されます。

    ```console
    mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>'"
    ```

3. 次に、`index.html` を次のスクリプトで更新してサーバーからトークンを取得し、サービスに接続します。

    ```html
    <html>
      <body>
        <h1>Azure Web PubSub Chat</h1>
      </body>
  
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');
        })();
      </script>
    </html>
    ```

    Chrome を使用している場合は、ホーム ページを開き、自分のユーザー名を入力してテストできます。 F12 キーを押して開発者ツール ウィンドウを開き、**コンソール** テーブルに切り替えます。`connected` がブラウザー コンソールに出力されているのがわかります。

---

## <a name="handle-events"></a>イベントを処理する

Azure Web PubSub では、クライアント側で特定のアクティビティが発生すると (たとえば、クライアントが接続または切断されると)、サービスはサーバーに通知を送信します。これにより、これらのイベントに対応できます。

イベントは、Webhook の形式でサーバーに配信されます。 Webhook はアプリケーション サーバーによって提供および公開され、Azure Web PubSub サービス側で登録されます。 サービスは、イベントが発生するたびに Webhook を呼び出します。

Azure Web PubSub は [CloudEvents](./reference-cloud-events.md) に従ってイベント データを記述します。 

# <a name="c"></a>[C#](#tab/csharp)
ここでは、イベントハンドラーを C# で独自に実装する必要があります。その手順は、[プロトコル仕様](./reference-cloud-events.md)に沿った簡単なもので、以下のようになります。

1. `UseEndpoints` 内にイベント ハンドラーを追加します。 イベントのエンドポイント パスを指定します。たとえば、`/eventhandler` です。 

2. まず、不正使用防止の OPTIONS 要求を処理します。ヘッダーに `WebHook-Request-Origin` ヘッダーが含まれているかどうかを確認し、ヘッダー `WebHook-Allowed-Origin` を返します。 デモ用に簡単にするために、すべてのオリジンを許可するために `*` を返します。
    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler/{*path}", async context =>
        {
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

3. 次に、受信要求が予想したとおりのイベントであるかどうかを調べます。 ここで、システム `connected` イベントについて考えてみましょう。これは、ヘッダー `ce-type` を `azure.webpubsub.sys.connected` として含むはずです。 不正使用防止の後にロジックを追加します。
    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler/{*path}", async context =>
        {
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
            else if (context.Request.Method == "POST")
            {
                // get the userId from header
                var userId = context.Request.Headers["ce-userId"];
                if (context.Request.Headers["ce-type"] == "azure.webpubsub.sys.connected")
                {
                    // the connected event
                    Console.WriteLine($"{userId} connected");
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

上のコードでは、クライアントが接続されたときにコンソールにメッセージを出力するだけです。 接続されているクライアントの ID を確認できるように、`context.Request.Headers["ce-userId"]` を使用していることを確認できます。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Web PubSub SDK を使用する場合は、CloudEvents スキーマを解析および処理する実装が既に用意されているため、これらの詳細を処理する必要はありません。

次のコードを追加して、クライアント接続イベントを処理するために REST API を `/eventhandler` で公開します (これは、Web PubSub SDK によって提供される express ミドルウェアによって実行されます)。

```bash
npm install --save @azure/web-pubsub-express@1.0.0-alpha.20211102.4
```

```javascript
const { WebPubSubEventHandler } = require('@azure/web-pubsub-express');

let handler = new WebPubSubEventHandler(hubName, {
  path: '/eventhandler',
  onConnected: async req => {
    console.log(`${req.context.userId} connected`);
  }
});

app.use(handler.getMiddleware());
```

上のコードでは、クライアントが接続されたときにコンソールにメッセージを出力するだけです。 接続されているクライアントの ID を確認できるように、`req.context.userId` を使用していることを確認できます。

# <a name="java"></a>[Java](#tab/java)
ここでは、イベントハンドラーを Java で独自に実装する必要があります。その手順は、[プロトコル仕様](./reference-cloud-events.md)に沿った簡単なもので、以下のようになります。

1. イベント ハンドラー パスの HTTP ハンドラーを追加します。たとえば `/eventhandler` です。 

2. まず、不正使用防止の OPTIONS 要求を処理します。ヘッダーに `WebHook-Request-Origin` ヘッダーが含まれているかどうかを確認し、ヘッダー `WebHook-Allowed-Origin` を返します。 デモ用に簡単にするために、すべてのオリジンを許可するために `*` を返します。
    ```java
    
    // validation: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#validation
    app.options("/eventhandler", ctx -> {
        ctx.header("WebHook-Allowed-Origin", "*");
    });
    ```

3. 次に、受信要求が予想したとおりのイベントであるかどうかを調べます。 ここで、システム `connected` イベントについて考えてみましょう。これは、ヘッダー `ce-type` を `azure.webpubsub.sys.connected` として含むはずです。 不正使用防止の後にロジックを追加します。
    ```java
    // validation: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#validation
    app.options("/eventhandler", ctx -> {
        ctx.header("WebHook-Allowed-Origin", "*");
    });

    // handle events: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#events
    app.post("/eventhandler", ctx -> {
        String event = ctx.header("ce-type");
        if ("azure.webpubsub.sys.connected".equals(event)) {
            String id = ctx.header("ce-userId");
            System.out.println(id + " connected.");
        }
        ctx.status(200);
    });

    ```

上のコードでは、クライアントが接続されたときにコンソールにメッセージを出力するだけです。 接続されているクライアントの ID を確認できるように、`ctx.header("ce-userId")` を使用していることを確認できます。

---

## <a name="set-up-the-event-handler"></a>イベント ハンドラーを設定する

### <a name="expose-localhost"></a>ローカルホストを公開する

次に Webhook URL をサービスに設定して、新しいイベントが発生したときに呼び出す場所を認識できるようにする必要があります。 しかし、このサーバーはローカルホストで実行されているので、インターネットでアクセス可能なエンドポイントがないという問題があります。 ここでは、[ngrok](https://ngrok.com/) を使用して、ローカルホストをインターネットに公開しています。

1.  まず、 https://ngrok.com/download から ngrok をダウンロードし、実行可能ファイルをローカル フォルダーまたはシステムの bin フォルダーに抽出します。
2.  ngrok を起動する
    
    ```bash
    ngrok http 8080
    ```

ngrok は、インターネットでアクセス可能な URL (`https://<domain-name>.ngrok.io`) を出力します。

### <a name="set-event-handler"></a>イベント ハンドラーを設定する

次に、サービス イベント ハンドラーを更新し、Webhook URL を設定します。

Azure CLI の [az webpubsub hub create](/cli/azure/webpubsub/hub#az_webpubsub_hub_update) コマンドを使用して、チャット ハブのイベント ハンドラーの設定を作成します

  > [!Important]
  > &lt;your-unique-resource-name&gt; を、前の手順で作成した Web PubSub リソースの名前に置き換えます。
  > &lt;domain-name&gt; を、ngrok が出力した名前に置き換えます。

```azurecli-interactive
az webpubsub hub create -n "<your-unique-resource-name>" -g "myResourceGroup" --hub-name "chat" --event-handler url-template="https://<domain-name>.ngrok.io/eventHandler" user-event-pattern="*" system-event="connected"
```

更新が完了したら、ホームページ http://localhost:8080/index.html を開き、ユーザー名を入力すると、connected メッセージがサーバー コンソールに出力されます。

## <a name="handle-message-events"></a>メッセージ イベントを処理する

クライアントは、`connected` や `disconnected` などのシステム イベント以外にも、WebSocket 接続を介してメッセージを送信できます。これらのメッセージは、`message` イベントと呼ばれる特殊な種類のイベントとしてサーバーに配信されます。 このイベントを使用して、1 つのクライアントからメッセージを受信し、それらをすべてのクライアントにブロードキャストして、これらが相互に通信できるようにすることができます。

# <a name="c"></a>[C#](#tab/csharp)

`message` イベントの `ce-type` は常に `azure.webpubsub.user.message` です。詳細は、[イベント メッセージ](./reference-cloud-events.md#message)に関するページをご覧ください。

1. メッセージ イベントを処理する

    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler/{*path}", async context =>
        {
            var serviceClient = context.RequestServices.GetRequiredService<WebPubSubServiceClient>();
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
            else if (context.Request.Method == "POST")
            {
                // get the userId from header
                var userId = context.Request.Headers["ce-userId"];
                if (context.Request.Headers["ce-type"] == "azure.webpubsub.sys.connected")
                {
                    // the connected event
                    Console.WriteLine($"{userId} connected");
                    context.Response.StatusCode = 200;
                    return;
                }
                else if (context.Request.Headers["ce-type"] == "azure.webpubsub.user.message")
                {
                    using var stream = new StreamReader(context.Request.Body);
                    await serviceClient.SendToAllAsync($"[{userId}] {await stream.ReadToEndAsync()}");
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

    このイベント ハンドラーは `WebPubSubServiceClient.SendToAllAsync()` を使用して、受信したメッセージをすべてのクライアントにブロードキャストします。

2.  `index.html` を更新して、ユーザーからのメッセージをサーバーに送信し、受信したメッセージをページに表示するロジックを追加します。

    ```html
    <html>

    <body>
      <h1>Azure Web PubSub Chat</h1>
      <input id="message" placeholder="Type to chat...">
      <div id="messages"></div>
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');

          let messages = document.querySelector('#messages');
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
        })();
      </script>
    </body>

    </html>
    ```

    上記のコードでは、`WebSocket.send()` を使用してメッセージを送信し、`WebSocket.onmessage` を使用してサービスからのメッセージをリッスンしていることがわかります。

3.  最後に `onConnected` ハンドラーを更新して、connected イベントをすべてのクライアントにブロードキャストして、チャット ルームに誰が参加したかを確認できるようにします。

    ```csharp
    app.UseEndpoints(endpoints =>
    {
        // abuse protection
        endpoints.Map("/eventhandler/{*path}", async context =>
        {
            if (context.Request.Method == "OPTIONS")
            {
                if (context.Request.Headers["WebHook-Request-Origin"].Count > 0)
                {
                    context.Response.Headers["WebHook-Allowed-Origin"] = "*";
                    context.Response.StatusCode = 200;
                    return;
                }
            }
            else if (context.Request.Method == "POST")
            {
                // get the userId from header
                var userId = context.Request.Headers["ce-userId"];
                if (context.Request.Headers["ce-type"] == "azure.webpubsub.sys.connected")
                {
                    // the connected event
                    Console.WriteLine($"{userId} connected");
                    await serviceClient.SendToAllAsync($"[SYSTEM] {userId} joined.");
                    context.Response.StatusCode = 200;
                    return;
                }
            }
        });
    });
    ```

次に、`dotnet run --urls http://localhost:8080` を使用してサーバーを実行し、 http://localhost:8080/index.html にアクセスするために複数のブラウザー インスタンスを開くと、お互いにチャットすることができます。

このチュートリアルの完成したコード サンプルは、[こちら][code-csharp]にあります。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

1. 新しい `handleUserEvent` ハンドラーを追加する

    ```javascript
    let handler = new WebPubSubEventHandler(hubName, {
      path: '/eventhandler',
      onConnected: async req => {
        console.log(`${req.context.userId} connected`);
      },
      handleUserEvent: async (req, res) => {
        if (req.context.eventName === 'message') 
            await serviceClient.sendToAll(`[${req.context.userId}] ${req.data}`, { contentType: 'text/plain' });
        res.success();
      }
    });
    ```

    このイベント ハンドラーは `WebPubSubServiceClient.sendToAll()` を使用して、受信したメッセージをすべてのクライアントにブロードキャストします。

    `handleUserEvent` には、イベントの送信者にメッセージを返信できる `res` オブジェクトもあることがわかります。 ここでは、単に `res.success()` を呼び出して WebHook が 200 を返すようにしています (この呼び出しは、クライアントに何も返したくない場合でも必要です。そうしないと、WebHook が戻らず、クライアント接続が閉じられます)。

2.  `index.html` を更新して、ユーザーからのメッセージをサーバーに送信し、受信したメッセージをページに表示するロジックを追加します。

    ```html
    <html>

      <body>
        <h1>Azure Web PubSub Chat</h1>
        <input id="message" placeholder="Type to chat...">
        <div id="messages"></div>
        <script>
          (async function () {
            let id = prompt('Please input your user name');
            let res = await fetch(`/negotiate?id=${id}`);
            let data = await res.json();
            let ws = new WebSocket(data.url);
            ws.onopen = () => console.log('connected');
  
            let messages = document.querySelector('#messages');
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
          })();
        </script>
      </body>
  
      </html>
    ```

    上記のコードでは、`WebSocket.send()` を使用してメッセージを送信し、`WebSocket.onmessage` を使用してサービスからのメッセージをリッスンしていることがわかります。

3. `sendToAll` はオブジェクトを入力として受け取り、JSON テキストをクライアントに送信します。 実際のシナリオでは、メッセージに関するより多くの情報を伝えるために、複雑なオブジェクトが必要になることがあります。 最後に、JSON オブジェクトをすべてのクライアントにブロードキャストするようにハンドラーを更新します。

    ```javascript
    let handler = new WebPubSubEventHandler(hubName, {
      path: '/eventhandler',
      onConnected: async req => {
        console.log(`${req.context.userId} connected`);
        await serviceClient.sendToAll({
          type: "system",
          message: `${req.context.userId} joined`
        });
      },
      handleUserEvent: async (req, res) => {
        if (req.context.eventName === 'message') {
          await serviceClient.sendToAll({
            from: req.context.userId,
            message: req.data
          });
        }
        res.success();
      }
    });
    ```

4. JSON データを解析するようにクライアントを更新します。
    ```html
    <html>

      <body>
        <h1>Azure Web PubSub Chat</h1>
        <input id="message" placeholder="Type to chat...">
        <div id="messages"></div>
        <script>
          (async function () {
            let id = prompt('Please input your user name');
            let res = await fetch(`/negotiate?id=${id}`);
            let data = await res.json();
            let ws = new WebSocket(data.url);
            ws.onopen = () => console.log('connected');
  
            let messages = document.querySelector('#messages');
            
            ws.onmessage = event => {
              let m = document.createElement('p');
              let data = JSON.parse(event.data);
              m.innerText = `[${data.type || ''}${data.from || ''}] ${data.message}`;
              messages.appendChild(m);
            };
  
            let message = document.querySelector('#message');
            message.addEventListener('keypress', e => {
              if (e.charCode !== 13) return;
              ws.send(message.value);
              message.value = '';
            });
          })();
        </script>
      </body>
  
    </html>
    ```

ここでサーバーを実行し、複数のブラウザー インスタンスを開くと、お互いにチャットすることができます。

このチュートリアルの完全なコード サンプルは、[こちら][code-js]にあります。

# <a name="java"></a>[Java](#tab/java)

`message` イベントの `ce-type` は常に `azure.webpubsub.user.message` です。詳細は、[イベント メッセージ](./reference-cloud-events.md#message)に関するページをご覧ください。

1. メッセージ イベントを処理する

    ```java
    // handle events: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#events
    app.post("/eventhandler", ctx -> {
        String event = ctx.header("ce-type");
        if ("azure.webpubsub.sys.connected".equals(event)) {
            String id = ctx.header("ce-userId");
            System.out.println(id + " connected.");
        } else if ("azure.webpubsub.user.message".equals(event)) {
            String id = ctx.header("ce-userId");
            String message = ctx.body();
            service.sendToAll(String.format("[%s] %s", id, message), WebPubSubContentType.TEXT_PLAIN);
        }
        ctx.status(200);
    });
    ```

    このイベント ハンドラーは `client.sendToAll()` を使用して、受信したメッセージをすべてのクライアントにブロードキャストします。

2.  `index.html` を更新して、ユーザーからのメッセージをサーバーに送信し、受信したメッセージをページに表示するロジックを追加します。

    ```html
    <html>

    <body>
      <h1>Azure Web PubSub Chat</h1>
      <input id="message" placeholder="Type to chat...">
      <div id="messages"></div>
      <script>
        (async function () {
          let id = prompt('Please input your user name');
          let res = await fetch(`/negotiate?id=${id}`);
          let url = await res.text();
          let ws = new WebSocket(url);
          ws.onopen = () => console.log('connected');

          let messages = document.querySelector('#messages');
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
        })();
      </script>
    </body>

    </html>
    ```

    上記のコードでは、`WebSocket.send()` を使用してメッセージを送信し、`WebSocket.onmessage` を使用してサービスからのメッセージをリッスンしていることがわかります。

3.  最後に `connected` イベント ハンドラーを更新して、connected イベントをすべてのクライアントにブロードキャストして、チャット ルームに誰が参加したかを確認できるようにします。

    ```java

    // handle events: https://azure.github.io/azure-webpubsub/references/protocol-cloudevents#events
    app.post("/eventhandler", ctx -> {
        String event = ctx.header("ce-type");
        if ("azure.webpubsub.sys.connected".equals(event)) {
            String id = ctx.header("ce-userId");
            service.sendToAll(String.format("[SYSTEM] %s joined", id), WebPubSubContentType.TEXT_PLAIN);
        } else if ("azure.webpubsub.user.message".equals(event)) {
            String id = ctx.header("ce-userId");
            String message = ctx.body();
            service.sendToAll(String.format("[%s] %s", id, message), WebPubSubContentType.TEXT_PLAIN);
        }
        ctx.status(200);
    });

    ```

ここで、下のコマンドでサーバーを実行し、複数のブラウザー インスタンスを開くと、お互いにチャットすることができます。

```console
mvn compile & mvn package & mvn exec:java -Dexec.mainClass="com.webpubsub.tutorial.App" -Dexec.cleanupDaemonThreads=false -Dexec.args="'<connection_string>'"
```

このチュートリアルの完全なコード サンプルは、[こちら][code-java]にあります。

---

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、イベント システムが Azure Web PubSub サービスでどのように機能するかについての基本的な説明を行います。

このサービスの使用方法について詳しくは、他のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Azure Web PubSub のサンプルをさらに確認する](https://aka.ms/awps/samples)


[code-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/chatapp/
[code-java]: https://github.com/Azure/azure-webpubsub/tree/main/samples/java/chatapp/
[code-csharp]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp/chatapp/
