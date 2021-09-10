---
title: Azure Web PubSub サービスへの WebSocket 接続を開始する方法
description: Azure Web PubSub サービスへの WebSocket 接続を開始する方法の、さまざまな言語での説明。
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 08/26/2021
ms.openlocfilehash: 91d326197737ab6dde07bbb72584648cfe025b05
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113912"
---
#  <a name="how-to-start-websocket-connection-to-the-azure-web-pubsub-service"></a>Azure Web PubSub サービスへの WebSocket 接続を開始する方法

クライアントから Azure Web PubSub サービスへの接続には、標準の [WebSocket](https://tools.ietf.org/html/rfc6455) プロトコルが使用されます。 そのため、WebSocket クライアントをサポートする言語を使用して、サービス用のクライアントを作成することができます。 以下のセクションでは、さまざまな言語の WebSocket クライアント サンプルを紹介します。

## <a name="auth"></a>Auth
Web PubSub サービスには、クライアントの検証と認証に [JWT トークン](https://tools.ietf.org/html/rfc7519.html)が使用されます。 クライアントは、サービスへの接続時に、`access_token` クエリ パラメーターにトークンを配置するか、`Authorization` ヘッダーにトークンを配置することができます。

一般的なワークフローは、クライアントがまずアプリ サーバーと通信し、サービスの URL とトークンを取得します。 そして、クライアントにより、受け取った URL とトークンが使用され、サービスへの WebSocket 接続が開かれます。

また、ポータルには、クライアントがクイック テストを開始できるように、トークンを含む動的に生成された *[クライアント URL]* が表示されます。

:::image type="content" source="./media/howto-websocket-connect/generate-client-url.png" alt-text="クライアント URL ジェネレーターの場所を示すスクリーンショット。":::

> [!NOTE]
> トークンを生成するときには、必要なロールのみを含めるようにしてください。
>

このサンプル ワークフローを簡略化するために、以下のセクションでは、接続するクライアント用にポータルから一時的に生成された URL を使用し、その値を表すために `<Client_URL_From_Portal>` を使用しています。 生成されるトークンの有効期限は既定で 50 分なので、有効期限が切れた場合は、忘れずに再生成してください。

このサービスでは、シンプルな WebSocket クライアントと、PubSub WebSocket クライアントの 2 種類の WebSocket クライアントがサポートされています。 ここでは、これら 2 種類のクライアントがサービスに接続する方法について説明します。 これらの 2 種類のクライアントの詳細については、「[Azure Web PubSub 用の WebSocket クライアント プロトコル](./concept-client-protocols.md)」を参照してください。

## <a name="dependency"></a>依存関係

# <a name="in-browser"></a>[ブラウザー内](#tab/browser)
最新のブラウザーでは、`WebSocket` API がネイティブでサポートされています。 

# <a name="nodejs"></a>[Node.js](#tab/javascript)

* [Node.js 12.x 以降](https://nodejs.org)
* `npm install ws`

# <a name="python"></a>[Python](#tab/python)
* [Python](https://www.python.org/)
* `pip install websockets`

# <a name="c"></a>[C#](#tab/csharp)

* [.NET Core 2.1 以降](https://dotnet.microsoft.com/download)
* `dotnet add package Websocket.Client`
    * [Websocket.Client](https://github.com/Marfusios/websocket-client) は、再接続とエラー処理が組み込まれたサードパーティー製の WebSocket クライアントです。

# <a name="java"></a>[Java](#tab/java)
- [Java Development Kit (JDK)](/java/azure/jdk/) バージョン 8 以降。
- [Apache Maven](https://maven.apache.org/download.cgi)。

---

## <a name="simple-websocket-client"></a>シンプル WebSocket クライアント

# <a name="in-browser"></a>[ブラウザー内](#tab/browser)

html ページの `script` ブロック内:
```html
<script>
    // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
    let ws = new WebSocket("<Client_URL_From_Portal>");
    ws.onopen = () => {
        // Do things when the WebSocket connection is established
    };

    ws.onmessage = event => {
        // Do things when messages are received.
    };
</script>
```

# <a name="nodejs"></a>[Node.js](#tab/javascript)

```js
const WebSocket = require('ws');
// Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
const client = new WebSocket("<Client_URL_From_Portal>");
client.on('open', () => {
     // Do things when the WebSocket connection is established
});
client.on('message', msg => {
     // Do things when messages are received.
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import asyncio
import websockets

async def hello():
    # Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
    uri = '<Client_URL_From_Portal>'
    async with websockets.connect(uri) as ws:
        while True:
            await ws.send('hello')
            greeting = await ws.recv()
            print(greeting)

asyncio.get_event_loop().run_until_complete(hello())
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Websocket.Client;

namespace subscriber
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
            using (var client = new WebsocketClient(new Uri("<Client_URL_From_Portal>")))
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

# <a name="java"></a>[Java](#tab/java)

```java
package client;

import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.WebSocket;
import java.util.concurrent.CompletionStage;

/**
 * A simple WebSocket Client.
 *
 */
public final class SimpleClient {
    private SimpleClient() {
    }

    /**
     * Starts a simple WebSocket connection.
     * @param args The arguments of the program.
     */
    public static void main(String[] args) throws Exception {
        // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
        WebSocket ws = HttpClient.newHttpClient().newWebSocketBuilder()
                .buildAsync(URI.create("<Client_URL_From_Portal>"), new WebSocketClient()).join();
        System.in.read();
    }

    private static final class WebSocketClient implements WebSocket.Listener {
        private WebSocketClient() {
        }

        @Override
        public void onOpen(WebSocket webSocket) {
            System.out.println("onOpen using subprotocol " + webSocket.getSubprotocol());
            WebSocket.Listener.super.onOpen(webSocket);
        }

        @Override
        public CompletionStage<?> onText(WebSocket webSocket, CharSequence data, boolean last) {
            System.out.println("onText received " + data);
            return WebSocket.Listener.super.onText(webSocket, data, last);
        }

        @Override
        public void onError(WebSocket webSocket, Throwable error) {
            System.out.println("Bad day! " + webSocket.toString());
            WebSocket.Listener.super.onError(webSocket, error);
        }
    }
}

```


---

## <a name="pubsub-websocket-client"></a>PubSub WebSocket クライアント

# <a name="in-browser"></a>[ブラウザー内](#tab/browser)

html ページの `script` ブロック内:
```html
<script>
    // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
    let ws = new WebSocket("<Client_URL_From_Portal>", 'json.webpubsub.azure.v1');
    ws.onopen = () => {
        // Do things when the WebSocket connection is established
    };

    ws.onmessage = event => {
        // Do things when messages are received.
    };
</script>
```

# <a name="nodejs"></a>[Node.js](#tab/javascript)

```js
const WebSocket = require('ws');
// Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
const client = new WebSocket("<Client_URL_From_Portal>", "json.webpubsub.azure.v1");
client.on('open', () => {
     // Do things when the WebSocket connection is established
});
client.on('message', msg => {
     // Do things when messages are received.
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import asyncio
import websockets

async def join_group():
    # Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
    uri = '<Client_URL_From_Portal>'
    async with websockets.connect(uri, subprotocols=['json.webpubsub.azure.v1']) as ws:
        await ws.send('{"type":"joinGroup","ackId":1,"group":"group1"}')
        return await ws.recv()

print(asyncio.get_event_loop().run_until_complete(join_group()))
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.IO;
using System.Net.WebSockets;
using System.Text;
using System.Threading.Tasks;
namespace subscriber
{
    class Program
    {
        static async Task Main(string[] args)
        {
            // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
            using (var client = new WebsocketClient(new Uri("<Client_URL_From_Portal>"), () =>
            {
                var inner = new ClientWebSocket();
                inner.Options.AddSubProtocol("json.webpubsub.azure.v1");
                return inner;
            }))
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

# <a name="java"></a>[Java](#tab/java)

```java
package client;

import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.WebSocket;
import java.util.concurrent.CompletionStage;

/**
 * A PubSub WebSocket Client.
 *
 */
public final class SubprotocolClient {
    private SubprotocolClient() {
    }

    /**
     * Starts a PubSub WebSocket connection.
     * @param args The arguments of the program.
     */
    public static void main(String[] args) throws Exception {
        // Don't forget to replace this <Client_URL_From_Portal> with the value fetched from the portal
        WebSocket ws = HttpClient.newHttpClient().newWebSocketBuilder().subprotocols("json.webpubsub.azure.v1")
                .buildAsync(URI.create("<Client_URL_From_Portal>"), new WebSocketClient()).join();

        ws.sendText("{\"type\":\"joinGroup\",\"ackId\":1,\"group\":\"group1\"}", true);
        System.in.read();
    }

    private static final class WebSocketClient implements WebSocket.Listener {
        private WebSocketClient() {
        }

        @Override
        public void onOpen(WebSocket webSocket) {
            System.out.println("onOpen using subprotocol " + webSocket.getSubprotocol());
            WebSocket.Listener.super.onOpen(webSocket);
        }

        @Override
        public CompletionStage<?> onText(WebSocket webSocket, CharSequence data, boolean last) {
            System.out.println("onText received " + data);
            return WebSocket.Listener.super.onText(webSocket, data, last);
        }

        @Override
        public void onError(WebSocket webSocket, Throwable error) {
            System.out.println("Bad day! " + webSocket.toString());
            WebSocket.Listener.super.onError(webSocket, error);
        }
    }
}
```

---

## <a name="next-step"></a>次の手順

この記事では、ポータルから生成された URL を使用してサービスに接続する方法について説明します。  実際のアプリケーションで、クライアントがアプリケーション サーバーと通信して URL を取得する方法については、次のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [チュートリアル: Azure Web PubSub を使用してチャット ルームを作成する](./tutorial-build-chat.md)

> [!div class="nextstepaction"]
> [チュートリアル: サービスがサポートするサブプロトコルを使用したクライアント ストリーミング](./tutorial-subprotocol.md)

> [!div class="nextstepaction"]
> [Azure Web PubSub のサンプルをさらに確認する](https://aka.ms/awps/samples)

