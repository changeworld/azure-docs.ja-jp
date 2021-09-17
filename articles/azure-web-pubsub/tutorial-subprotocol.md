---
title: チュートリアル - Azure Web PubSub サービスでサブプロトコルを使用して、WebSocket クライアント間でメッセージを発行およびサブスクライブする
description: Azure Web PubSub サービスとそのサポートされている WebSocket サブプロトコルを使用して、クライアント間で同期する方法を説明するチュートリアル。
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 08/16/2021
ms.openlocfilehash: 232489ea06020d5f2f06cfc7d841c888b2b5a5d0
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773226"
---
# <a name="tutorial-publish-and-subscribe-messages-between-websocket-clients-using-subprotocol"></a>チュートリアル: サブプロトコルを使用して、WebSocket クライアント間でメッセージを発行およびサブスクライブする

[チャット アプリを構築するチュートリアル](./tutorial-build-chat.md)では、WebSocket API を使用して Azure Web PubSub でデータを送受信する方法について学習しました。 クライアントがサービスと通信する際に、プロトコルが必要ないことが分かります。 たとえば、`WebSocket.send()` を使用してデータを送信すると、サーバーはそのままのデータを受け取ります。 これは使いやすいですが、機能が制限されています。 たとえば、サーバーにイベントを送信するときにイベント名を指定したり、サーバーに送信する代わりに他のクライアントにメッセージを発行したりすることができません。 このチュートリアルでは、サブプロトコルを使用してクライアントの機能を拡張する方法について説明します。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * Azure Web PubSub サービス インスタンスを作成する
> * 完全な URL を生成して WebSocket 接続を確立する
> * サブプロトコルを使用して WebSocket クライアント間でメッセージを発行する

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

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)

---

## <a name="using-a-subprotocol"></a>サブプロトコルの使用

クライアントは、特定の[サブプロトコル](https://datatracker.ietf.org/doc/html/rfc6455#section-1.9)を使用して WebSocket 接続を開始できます。 Azure Web PubSub サービスでは、`json.webpubsub.azure.v1` と呼ばれるサブプロトコルがサポートされています。これにより、クライアントはアップストリーム サーバーへのラウンド トリップではなく、発行/サブスクライブを直接実行できます。 サブプロトコルの詳細については、「[Azure Web PubSub でサポートされる JSON WebSocket サブプロトコル](./reference-json-webpubsub-subprotocol.md)」をご覧ください。

> 他のプロトコル名を使用すると、それはサービスによって無視され、接続イベント ハンドラー内のサーバーにパススルーされます。そのため、独自のプロトコルを構築することができます。

それでは、`json.webpubsub.azure.v1` サブプロトコルを使用して Web アプリケーションを作成しましょう。

1.  依存関係のインストール

    # <a name="c"></a>[C#](#tab/csharp)
    ```bash
    mkdir logstream
    cd logstream
    dotnet new web
    dotnet add package Microsoft.Extensions.Azure
    dotnet add package Azure.Messaging.WebPubSub --prerelease
    ```
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    
    ```bash
    mkdir logstream
    cd logstream
    npm init -y
    npm install --save express
    npm install --save ws
    npm install --save node-fetch
    npm install --save @azure/web-pubsub
    ```

    # <a name="python"></a>[Python](#tab/python)
    
    ```bash
    mkdir logstream
    cd logstream

    # Create venv
    python -m venv env

    # Active venv
    ./env/Scripts/activate

    # Or call .\env\Scripts\activate when you are using CMD under Windows

    pip install azure-messaging-webpubsubservice
    ```
    
    ---
    
2.  `/negotiate` API と Web ページをホストするサーバー側を作成します。

    # <a name="c"></a>[C#](#tab/csharp)

    下のコードを使用して `Startup.cs` を更新します。
    - `ConfigureServices` メソッドを更新してサービス クライアントを追加し、構成から接続文字列を読み取ります。 
    - `Configure` メソッドを更新して、`app.UseRouting();` の前に `app.UseStaticFiles();` を追加して静的ファイルをサポートします。 
    - `app.UseEndpoints` を更新して、`/negotiate` 要求を持つクライアント アクセス トークンを生成します。

    ```csharp
    using Azure.Messaging.WebPubSub;

    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Extensions.Azure;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Hosting;
    
    namespace logstream
    {
        public class Startup
        {
            public Startup(IConfiguration configuration)
            {
                Configuration = configuration;
            }
    
            public IConfiguration Configuration { get; }
    
            public void ConfigureServices(IServiceCollection services)
            {
                services.AddAzureClients(builder =>
                {
                    builder.AddWebPubSubServiceClient(Configuration["Azure:WebPubSub:ConnectionString"], "stream");
                });
            }
    
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
                    endpoints.MapGet("/negotiate", async context =>
                    {
                        var serviceClient = context.RequestServices.GetRequiredService<WebPubSubServiceClient>();
                        var response = new
                        {
                            url = serviceClient.GenerateClientAccessUri(roles: new string[] { "webpubsub.sendToGroup.stream", "webpubsub.joinLeaveGroup.stream" }).AbsoluteUri
                        };
                        await context.Response.WriteAsJsonAsync(response);
                    });
                });
            }
        }
    }
    
    ```
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    `server.js` を作成し、以下のコードを追加します。

    ```javascript
    const express = require('express');
    const { WebPubSubServiceClient } = require('@azure/web-pubsub');

    let endpoint = new WebPubSubServiceClient(process.argv[2], 'stream');
    const app = express();

    app.get('/negotiate', async (req, res) => {
      let token = await endpoint.getAuthenticationToken({
        roles: ['webpubsub.sendToGroup.stream', 'webpubsub.joinLeaveGroup.stream']
      });
      res.send({
        url: token.url
      });
    });

    app.use(express.static('public'));
    app.listen(8080, () => console.log('server started'));
    ```

    # <a name="python"></a>[Python](#tab/python)
    
    `server.py` を作成し、`/negotiate` API と Web ページをホストします。

    ```python
    import json
    import sys
    from http.server import HTTPServer, SimpleHTTPRequestHandler
    from azure.messaging.webpubsubservice import (
        build_authentication_token
    )

    class Resquest(SimpleHTTPRequestHandler):
        def do_GET(self):
            if self.path == '/':
                self.path = 'public/index.html'
                return SimpleHTTPRequestHandler.do_GET(self)
            elif self.path == '/negotiate':
                token = build_authentication_token(sys.argv[1], 'stream', roles=['webpubsub.sendToGroup.stream', 'webpubsub.joinLeaveGroup.stream'])
                print(token)
                self.send_response(200)
                self.send_header('Content-Type', 'application/json')
                self.end_headers()
                self.wfile.write(json.dumps({
                    'url': token['url']
                }).encode())

    if __name__ == '__main__':
        if len(sys.argv) != 2:
            print('Usage: python server.py <connection-string>')
            exit(1)

        server = HTTPServer(('localhost', 8080), Resquest)
        print('server started')
        server.serve_forever()
    ```

    ---
    
3.  Web ページの作成

    # <a name="c"></a>[C#](#tab/csharp)
    以下のコンテンツを含む HTML ページを作成し、`wwwroot/index.html` として保存します。
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    以下のコンテンツを含む HTML ページを作成し、`public/index.html` として保存します。

    # <a name="python"></a>[Python](#tab/python)

    以下のコンテンツを含む HTML ページを作成し、`public/index.html` として保存します。
    
    ---
    
    ```html
    <html>

    <body>
      <div id="output"></div>
      <script>
        (async function () {
          let res = await fetch('/negotiate')
          let data = await res.json();
          let ws = new WebSocket(data.url, 'json.webpubsub.azure.v1');
          ws.onopen = () => {
            console.log('connected');
          };

          let output = document.querySelector('#output');
          ws.onmessage = event => {
            let d = document.createElement('p');
            d.innerText = event.data;
            output.appendChild(d);
          };
        })();
      </script>
    </body>

    </html>
    ```

    これは単にサービスに接続し、受信したメッセージをページに出力します。 主な変更は、WebSocket 接続の作成時にサブプロトコルを指定することです。

4. サーバーを実行する
    # <a name="c"></a>[C#](#tab/csharp)
    .NET Core 用の [Secret Manager](/aspnet/core/security/app-secrets#secret-manager) ツールを使用して接続文字列を設定します。 `<connection_string>` を、[前の手順](#get-the-connectionstring-for-future-use)でフェッチされたものに置き換えて下のコマンドを実行し、ブラウザで http://localhost:5000/index.html を開きます。

    ```bash
    dotnet user-secrets init
    dotnet user-secrets set Azure:WebPubSub:ConnectionString "<connection-string>"
    dotnet run
    ```
    
    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    
    `<connection-string>` を、[前の手順](#get-the-connectionstring-for-future-use)でフェッチされた **ConnectionString** に置き換えて下のコマンドを実行し、ブラウザで http://localhost:8080 を開きます。

    ```bash
    
    node server "<connection-string>"
    ```
    
    # <a name="python"></a>[Python](#tab/python)

    `<connection-string>` を、[前の手順](#get-the-connectionstring-for-future-use)でフェッチされた **ConnectionString** に置き換えて下のコマンドを実行し、ブラウザで http://localhost:8080 を開きます。

    ```bash
    python server.py "<connection-string>"
    ```
    ---

    Chrome を使用している場合には、F12 を押すか、右クリックして **[検証]**  ->  **[開発者ツール]** を選択し、 **[ネットワーク]** タブを選択します。Web ページを読み込むと、WebSocket 接続が確率されたことを確認できます。 クリックして、WebSocket 接続を検査します。次の `connected` イベント メッセージがクライアントで受信されていることを確認できます。 このクライアントに `connectionId` を生成できることが分かります。
    
    ```json
    {"type":"system","event":"connected","userId":null,"connectionId":"<the_connection_id>"}
    ```

サブプロトコルを使用すると、接続が `connected` のときに接続のメタデータを取得できます。

また、クライアントはプレーンテキストではなく、メッセージの種類や送信元など、より多くの情報を含む JSON メッセージを受信するようになります。 そのため、この情報を使用して、メッセージに対してより多くの処理を実行できます (たとえば、別のソースからの場合は、別のスタイルでメッセージを表示するなど)。これは、後のセクションで説明します。

## <a name="publish-messages-from-client"></a>クライアントからメッセージを発行する

[チャット アプリの構築](./tutorial-build-chat.md)に関するチュートリアルで、クライアントが WebSocket 接続を介してメッセージを送信すると、サーバー側でユーザー イベントがトリガーされます。 サブプロトコルを使用すると、JSON メッセージを送信することにより、クライアントの機能が向上します。 たとえば、クライアントから他のクライアントに直接メッセージを発行できます。

これは、リアルタイムで大量のデータを他のクライアントにストリーミングする場合に便利です。 この機能を使用し、コンソール ログをリアルタイムでブラウザーにストリーミングできる、ログ ストリーミング アプリケーションを構築しましょう。

1. ストリーミング プログラムの作成
 
    # <a name="c"></a>[C#](#tab/csharp)
    `stream` プログラムを作成します。
    ```bash
    mkdir stream
    cd stream
    dotnet new console
    ```
    
    次の内容を使用して `Program.cs` を更新します。
    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.WebSockets;
    using System.Text;
    using System.Text.Json;
    using System.Threading.Tasks;
    
    namespace stream
    {
        class Program
        {
            private static readonly HttpClient http = new HttpClient();
            static async Task Main(string[] args)
            {
                // Get client url from remote
                var stream = await http.GetStreamAsync("http://localhost:5000/negotiate");
                var url = (await JsonSerializer.DeserializeAsync<ClientToken>(stream)).url;
                var client = new ClientWebSocket();
                client.Options.AddSubProtocol("json.webpubsub.azure.v1");
    
                await client.ConnectAsync(new Uri(url), default);
    
                Console.WriteLine("Connected.");
                var streaming = Console.ReadLine();
                while (streaming != null)
                {
                    if (!string.IsNullOrEmpty(streaming))
                    {
                        var message = JsonSerializer.Serialize(new
                        {
                            type = "sendToGroup",
                            group = "stream",
                            data = streaming + Environment.NewLine,
                        });
                        Console.WriteLine("Sending " + message);
                        await client.SendAsync(Encoding.UTF8.GetBytes(message), WebSocketMessageType.Text, true, default);
                    }
    
                    streaming = Console.ReadLine();
                }
    
                await client.CloseAsync(WebSocketCloseStatus.NormalClosure, null, default);
            }
    
            private sealed class ClientToken
            {
                public string url { get; set; }
            }
        }
    }
    
    ```

    # <a name="javascript"></a>[JavaScript](#tab/javascript)
    次の内容を使用して `stream.js` を作成します。
    
    ```javascript
    const WebSocket = require('ws');
    const fetch = require('node-fetch');

    async function main() {
      let res = await fetch(`http://localhost:8080/negotiate`);
      let data = await res.json();
      let ws = new WebSocket(data.url, 'json.webpubsub.azure.v1');
      ws.on('open', () => {
        process.stdin.on('data', data => {
          ws.send(JSON.stringify({
            type: 'sendToGroup',
            group: 'stream',
            dataType: 'text',
            data: data.toString()
          }));
          process.stdout.write(data);
        });
      });
      process.stdin.on('close', () => ws.close());
    }

    main();
    ```

    上記のコードは、サービスへの WebSocket 接続を作成し、データを受信するたびに `ws.send()` を使用してそのデータを発行します。 他のグループに発行するには、`type` を `sendToGroup` に設定し、メッセージでグループ名を指定します。

    # <a name="python"></a>[Python](#tab/python)

    `stream` プログラム用の別の bash ウィンドウを開き、`websockets` 依存関係をインストールします。

    ```bash
    mkdir stream
    cd stream

    # Create venv
    python -m venv env

    # Active venv
    ./env/Scripts/activate

    # Or call .\env\Scripts\activate when you are using CMD under Windows
    pip install websockets
    ```

    次の内容を使用して `stream.py` を作成します。

    ```python
    import asyncio
    import sys
    import threading
    import time
    import websockets
    import requests
    import json


    async def connect(url):
        async with websockets.connect(url, subprotocols=['json.webpubsub.azure.v1']) as ws:
            print('connected')
            id = 1
            while True:
                data = input()
                payload = {
                    'type': 'sendToGroup',
                    'group': 'stream',
                    'dataType': 'text',
                    'data': str(data + '\n'),
                    'ackId': id
                }
                id = id + 1
                await ws.send(json.dumps(payload))
                await ws.recv()

    res = requests.get('http://localhost:8080/negotiate').json()

    try:
        asyncio.get_event_loop().run_until_complete(connect(res['url']))
    except KeyboardInterrupt:
        pass

    ```

    上記のコードは、サービスへの WebSocket 接続を作成し、データを受信するたびに `ws.send()` を使用してそのデータを発行します。 他のグループに発行するには、`type` を `sendToGroup` に設定し、メッセージでグループ名を指定します。
    
    ---
    
    ここに新しい概念 "グループ" があるのを確認できます。 グループは、接続のグループにメッセージを発行できる、ハブの論理的な概念です。 ハブでは、複数のグループを使用でき、1 つのクライアントは同時に複数のグループをサブスクライブできます。 サブプロトコルを使用する場合には、ハブ全体にブロードキャストするのではなく、グループにのみ発行できます。 用語の詳細については、「[基本的な概念](./key-concepts.md)」をご覧ください。

2.  ここではグループを使用するため、`ws.onopen` コールバック内で WebSocket 接続を確立する際に、Web ページ `index.html` を更新し、グループに参加する必要もあります。
    
    ```javascript
    ws.onopen = () => {
      console.log('connected');
      ws.send(JSON.stringify({
        type: 'joinGroup',
        group: 'stream'
      }));
    };
    ```

    クライアントがグループに参加しているのを確認するには、`joinGroup` 型のメッセージを送信します。

3.  また、`ws.onmessage` コールバック ロジックを少々更新して、JSON 応答を解析し、`stream` グループからのメッセージのみを出力するようにして、ライブ ストリーム プリンターとして機能させます。

    ```javascript
    ws.onmessage = event => {
      let message = JSON.parse(event.data);
      if (message.type === 'message' && message.group === 'stream') {
        let d = document.createElement('span');
        d.innerText = message.data;
        output.appendChild(d);
        window.scrollTo(0, document.body.scrollHeight);
      }
    };
    ```

4.  セキュリティ上の理由から、既定では、クライアントは、それ自体でグループに発行したり、またはグループをサブスクライブすることはできません。 そのため、トークンを生成するときに、クライアントに `roles` が設定されていることに気づいたかもしれません。

    # <a name="c"></a>[C#](#tab/csharp)
    `Startup.cs` の`GenerateClientAccessUri` で、`roles` を下のように設定します。
    ```csharp
    serviceClient.GenerateClientAccessUri(roles: new string[] { "webpubsub.sendToGroup.stream", "webpubsub.joinLeaveGroup.stream" })
    ```

    # <a name="javascript"></a>[JavaScript](#tab/javascript)

    `server.js` の`getAuthenticationToken` で、`roles` を下のように追加します。

    ```javascript
    app.get('/negotiate', async (req, res) => {
      let token = await endpoint.getAuthenticationToken({
        roles: ['webpubsub.sendToGroup.stream', 'webpubsub.joinLeaveGroup.stream']
      });
      ...
    });
    
    ```
    
    # <a name="python"></a>[Python](#tab/python)
    
    トークン生成コードを更新し、`server.py` の `build_authentication_token` で、クライアントに次のような `roles` を与えます。

    ```python
    token = build_authentication_token(sys.argv[1], 'stream', roles=['webpubsub.sendToGroup.stream', 'webpubsub.joinLeaveGroup.stream'])
    
    ```
    ---
    

5.  最後に、適切に表示されるようにするため、`index.html` にスタイルを適用します。

    ```html
    <html>

      <head>
        <style>
          #output {
            white-space: pre;
            font-family: monospace;
          }
        </style>
      </head>
    ```

次に、下のコードを実行し、任意のテキストを入力すると、それがブラウザーにリアル タイムで表示されます。

# <a name="c"></a>[C#](#tab/csharp)

```bash
ls -R | dotnet run

# Or call `dir /s /b | dotnet run` when you are using CMD under Windows

```

または速度を低下させ、データがリアル タイムでブラウザーにストリーミングされるのを確認します。

```bash
for i in $(ls -R); do echo $i; sleep 0.1; done | dotnet run
```

このチュートリアルの完成したコード サンプルは、[こちら][code-csharp]にあります。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`node stream`

または、このアプリを使用し、別のコンソール アプリからの出力をパイプしてブラウザーにストリーミングすることもできます。 例:

```bash
ls -R | node stream

# Or call `dir /s /b | node stream` when you are using CMD under Windows
```

または速度を低下させ、データがリアル タイムでブラウザーにストリーミングされるのを確認します。

```bash
for i in $(ls -R); do echo $i; sleep 0.1; done | node stream
```

このチュートリアルの完成したコード サンプルは、[こちら][code-js]にあります。

# <a name="python"></a>[Python](#tab/python)

次に、`python stream.py` を実行し、任意のテキストを入力すると、それがブラウザーにリアルタイムで表示されます。

または、このアプリを使用し、別のコンソール アプリからの出力をパイプしてブラウザーにストリーミングすることもできます。 例:

```bash
ls -R | python stream.py

# Or call `dir /s /b | python stream.py` when you are using CMD under Windows
```

または速度を低下させ、データがリアル タイムでブラウザーにストリーミングされるのを確認します。

```bash
for i in $(ls -R); do echo $i; sleep 0.1; done | python stream.py
```

このチュートリアルの完成したコード サンプルは、[こちら][code-python]にあります。


---


## <a name="next-steps"></a>次の手順

このチュートリアルでは、Web PubSub サービスに接続する方法と、サブプロトコルを使用して、接続されているクライアントにメッセージを発行する方法に関する基本的な考え方を説明しています。

サービスの使用方法の詳細については、他のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Azure Web PubSub のサンプルをさらに確認する](https://aka.ms/awps/samples)

[code-csharp]: https://github.com/Azure/azure-webpubsub/tree/main/samples/csharp/logstream/

[code-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/logstream/

[code-python]: https://github.com/Azure/azure-webpubsub/tree/main/samples/python/logstream/
