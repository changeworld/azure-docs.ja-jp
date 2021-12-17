---
title: リファレンス - Azure Web PubSub 用の JavaScript SDK
description: このリファレンスでは、Azure Web PubSub サービス用 JavaScript SDK について説明します
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: 8f566159f51ccee7c7e567de83d199e55a35c76b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132705172"
---
# <a name="javascript-sdk-for-azure-web-pubsub"></a>リファレンス - Azure Web PubSub 用の JavaScript SDK

[Azure Web PubSub](./index.yml) サービスは、開発者がリアルタイムの機能と発行-サブスクライブ パターンを利用して Web アプリケーションを簡単に作成できるようにするための Azure マネージド サービスです。 サーバーとクライアント間、またはクライアント間で、リアルタイムのパブリッシュ-サブスクライブ メッセージングを必要とするあらゆるシナリオに、Azure Web PubSub サービスを使用できます。 従来のリアルタイム機能は、多くの場合、サーバーからのポーリングや HTTP 要求の送信を必要としますが、そのようなリアルタイム機能にも Azure Web PubSub サービスを使用できます。

JavaScript には、サービス クライアント ライブラリと Express ミドルウェアの 2 つのライブラリが用意されています。 以下のセクションでは、これらのライブラリの詳細について説明します。

- [サービス クライアント ライブラリ](#service-client-library)
- [Express ミドルウェア](#express)

<a name="service-client-library"></a>

## <a name="azure-web-pubsub-service-client-library-for-javascript"></a>JavaScript 用 Azure Web PubSub サービス クライアント ライブラリ

次の図に示すように、このライブラリをアプリ サーバー側で使用して、WebSocket クライアント接続を管理できます。

![このオーバーフロー図は、サービス クライアント ライブラリを使用したのオーバーフローを示しています。](media/sdk-reference/service-client-overflow.png)

- ハブとグループにメッセージを送信します。
- 特定のユーザーと接続にメッセージを送信します。
- ユーザーと接続をグループに整理します。
- 接続を終了します
- 既存の接続のアクセス許可を付与、取り消し、確認します

ここで使われている用語の詳細は、[主な概念](#key-concepts)のセクションを参照してください。

[ソース コード](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/web-pubsub/web-pubsub) |
[パッケージ (NPM)](https://www.npmjs.com/package/@azure/web-pubsub) |
[API リファレンス ドキュメント]() |
[製品ドキュメント](./index.yml) |
[サンプル][samples_ref]

### <a name="getting-started"></a>はじめに

#### <a name="currently-supported-environments"></a>現在サポートされている環境

- [Node.js の LTS バージョン](https://nodejs.org/about/releases/)

#### <a name="prerequisites"></a>前提条件

- [Azure サブスクリプション][azure_sub]。
- 既存の Azure Web PubSub サービス インスタンス。

#### <a name="1-install-the-azureweb-pubsub-package"></a>1. `@azure/web-pubsub` パッケージをインストールする

```bash
npm install @azure/web-pubsub
```

#### <a name="2-create-and-authenticate-a-webpubsubserviceclient"></a>2. WebPubSubServiceClient を作成して認証する

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
```

エンドポイントと `AzureKeyCredential` を使用して `WebPubSubServiceClient` を認証できます。

```js
const { WebPubSubServiceClient, AzureKeyCredential } = require("@azure/web-pubsub");

const key = new AzureKeyCredential("<Key>");
const serviceClient = new WebPubSubServiceClient("<Endpoint>", key, "<hubName>");
```

または、[Azure Active Directory][aad_doc] を使用して `WebPubSubServiceClient` を認証します。

1. `@azure/identity` 依存関係をインストールします。

```batch
npm install @azure/identity
```

2. `DefaultAzureCredential` を使用するようにソース コードを更新します。

```js
const { WebPubSubServiceClient, AzureKeyCredential } = require("@azure/web-pubsub");

const key = new DefaultAzureCredential();
const serviceClient = new WebPubSubServiceClient("<Endpoint>", key, "<hubName>");
```

### <a name="key-concepts"></a>主要な概念

#### <a name="connection"></a>Connection

接続は、クライアントまたはクライアント接続とも呼ばれ、Web PubSub サービスに接続されている個々の WebSocket 接続を表します。 正常に接続されると、一意の接続 ID が Web PubSub サービスによってこの接続に割り当てられます。

#### <a name="hub"></a>ハブ

ハブは、一連のクライアント接続の論理的な概念です。 通常、"チャット" ハブや "通知" ハブなど、1 つの目的に 1 つのハブを使います。 クライアント接続は、作成されると 1 つのハブに接続され、その有効期間中はそのハブに属します。 異なるアプリケーションでは、異なるハブ名を使用して、1 つの Azure Web PubSub サービスを共有できます。

#### <a name="group"></a>グループ

グループはハブへの接続のサブセットです。 必要な場合はいつでも、クライアント接続をグループに追加したり、グループからクライアント接続を削除したりできます。 たとえば、クライアントがチャット ルームに参加したり、クライアントがチャット ルームを離れたりするときに、このチャット ルームをグループと見なすことができます。 クライアントは複数のグループに参加できます。また、1 つのグループに複数のクライアントを含めることもできます。

#### <a name="user"></a>User

Web PubSub への接続を 1 人のユーザーに属させることができます。 1 人のユーザーが複数のデバイスまたは複数のブラウザー タブに接続されている場合など、ユーザーが複数の接続を持つ場合があります。

#### <a name="message"></a>Message

クライアントが接続されている場合は、WebSocket 接続を介して、アップストリーム アプリケーションにメッセージを送信したり、アップストリーム アプリケーションからメッセージを受信したりできます。

### <a name="examples"></a>例

#### <a name="get-the-access-token-for-a-client-to-start-the-websocket-connection"></a>クライアントが WebSocket 接続を開始するためのアクセス トークンを取得する

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

// Get the access token for the WebSocket client connection to use
let token = await serviceClient.getClientAccessToken();

// Or get the access token and assign the client a userId
token = await serviceClient.getClientAccessToken({ userId: "user1" });

// return the token to the WebSocket client
```

#### <a name="broadcast-messages-to-all-connections-in-a-hub"></a>ハブ内のすべてのクライアント接続にメッセージをブロードキャストする

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

// Send a JSON message
await serviceClient.sendToAll({ message: "Hello world!" });

// Send a plain text message
await serviceClient.sendToAll("Hi there!", { contentType: "text/plain" });

// Send a binary message
const payload = new Uint8Array(10);
await serviceClient.sendToAll(payload.buffer);
```

#### <a name="send-messages-to-all-connections-in-a-group"></a>グループ内のすべての接続にメッセージを送信する

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

const groupClient = serviceClient.group("<groupName>");

// Add user to the group
await groupClient.addUser("user1");

// Send a JSON message
await groupClient.sendToAll({ message: "Hello world!" });

// Send a plain text message
await groupClient.sendToAll("Hi there!", { contentType: "text/plain" });

// Send a binary message
const payload = new Uint8Array(10);
await groupClient.sendToAll(payload.buffer);
```

#### <a name="send-messages-to-all-connections-for-a-user"></a>ユーザーのすべての接続にメッセージを送信する

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

// Send a JSON message
await serviceClient.sendToUser("user1", { message: "Hello world!" });

// Send a plain text message
await serviceClient.sendToUser("user1", "Hi there!", { contentType: "text/plain" });

// Send a binary message
const payload = new Uint8Array(10);
await serviceClient.sendToUser("user1", payload.buffer);
```

#### <a name="check-if-the-group-has-any-connection"></a>グループに接続が存在するかどうかを確認する

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");
const WebSocket = require("ws");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

const groupClient = serviceClient.group("<groupName>");

// close all the connections in the group
await groupClient.closeAllConnections({ reason: "<closeReason>" });

// check if the group has any connections
const hasConnections = await serviceClient.groupExists("<groupName>");
```

#### <a name="access-the-raw-http-response-for-an-operation"></a>操作の生の HTTP 応答にアクセスする

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

function onResponse(rawResponse: FullOperationResponse): void {
  console.log(rawResponse);
}
const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
await serviceClient.sendToAll({ message: "Hello world!" }, { onResponse });
```

### <a name="troubleshooting"></a>トラブルシューティング

#### <a name="enable-logs"></a>ログの有効化

このライブラリの使用時にデバッグ ログを表示するには、次の環境変数を取得します。

- SignalR クライアント ライブラリからデバッグ ログを取得する

```bash
export AZURE_LOG_LEVEL=verbose
```

ログを有効にする方法の詳細については、[@azure/logger パッケージに関するドキュメント](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/core/logger)を参照してください。

#### <a name="live-trace"></a>ライブ トレース

ライブトラフィックを表示するには、Web PubSub サービス ポータルから **ライブ トレース** を使用します。

<a name="express"></a>

## <a name="azure-web-pubsub-cloudevents-handlers-for-express"></a>Express 用 Azure Web Pubsub CloudEvents ハンドラー

WebSocket が接続すると、Web PubSub サービスが接続ライフサイクルとメッセージを [CloudEvents 形式のイベント](concept-service-internals.md#workflow)に変換します。 次の図に示したように、このライブラリは、WebSocket 接続のライフサイクルとメッセージを表すイベントを処理するための Express ミドルウェアを備えています。

![オーバーフロー図は、イベント ハンドラー ミドルウェアの使用のオーバーフローを示します。](media/sdk-reference/event-handler-middleware.png)

ここで使われている用語の詳細は、[主な概念](#key-concepts)のセクションを参照してください。

[ソース コード](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/web-pubsub/web-pubsub-express) |
[パッケージ (NPM)](https://www.npmjs.com/package/@azure/web-pubsub-express) |
[API リファレンス ドキュメント]() |
[製品ドキュメント](./index.yml) |
[サンプル][samples_ref]

### <a name="getting-started"></a>はじめに

#### <a name="currently-supported-environments"></a>現在サポートされている環境

- [Node.js の LTS バージョン](https://nodejs.org/about/releases/)
- [Express](https://expressjs.com/) バージョン 4.x.x 以上

#### <a name="prerequisites"></a>前提条件

- [Azure サブスクリプション][azure_sub]。
- 既存の Azure Web PubSub エンドポイント。

#### <a name="1-install-the-azureweb-pubsub-express-package"></a>1. `@azure/web-pubsub-express` パッケージをインストールする

```bash
npm install @azure/web-pubsub-express
```

#### <a name="2-create-a-webpubsubeventhandler"></a>2. `WebPubSubEventHandler` を作成する

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler("chat");

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

### <a name="key-concepts"></a>主要な概念

#### <a name="connection"></a>Connection

接続は、クライアントまたはクライアント接続とも呼ばれ、Web PubSub サービスに接続されている個々の WebSocket 接続を表します。 正常に接続されると、一意の接続 ID が Web PubSub サービスによってこの接続に割り当てられます。

#### <a name="hub"></a>ハブ

ハブは、一連のクライアント接続の論理的な概念です。 通常、"チャット" ハブや "通知" ハブなど、1 つの目的に 1 つのハブを使います。 クライアント接続は、作成されると 1 つのハブに接続され、その有効期間中はそのハブに属します。 異なるアプリケーションでは、異なるハブ名を使用して、1 つの Azure Web PubSub サービスを共有できます。

#### <a name="group"></a>グループ

グループはハブへの接続のサブセットです。 必要な場合はいつでも、クライアント接続をグループに追加したり、グループからクライアント接続を削除したりできます。 たとえば、クライアントがチャット ルームに参加したり、クライアントがチャット ルームを離れたりするときに、このチャット ルームをグループと見なすことができます。 クライアントは複数のグループに参加できます。また、1 つのグループに複数のクライアントを含めることもできます。

#### <a name="user"></a>User

Web PubSub への接続を 1 人のユーザーに属させることができます。 1 人のユーザーが複数のデバイスまたは複数のブラウザー タブに接続されている場合など、ユーザーが複数の接続を持つ場合があります。

#### <a name="client-events"></a>クライアント イベント

イベントは、クライアント接続のライフサイクル中に作成されます。 たとえば、シンプル WebSocket クライアント接続では、サービスへの接続が試みられるときに `connect` イベント、サービスに正常に接続したときに `connected` イベント、サービスにメッセージを送信するときに `message` イベント、サービスから切断したときに `disconnected` イベントが作成されます。

#### <a name="event-handler"></a>イベント ハンドラー

イベント ハンドラーには、クライアント イベントを処理するロジックが含まれています。 イベント ハンドラーは、ポータルまたは Azure CLI を使用して事前にサービスに登録し設定しておく必要があります。 イベント ハンドラー ロジックをホストする場所は、通常、サーバー側と見なされます。

### <a name="examples"></a>例

#### <a name="handle-the-connect-request-and-assign-userid"></a>`connect` 要求を処理して `<userId>` を割り当てる

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler("chat", {
  handleConnect: (req, res) => {
    // auth the connection and set the userId of the connection
    res.success({
      userId: "<userId>"
    });
  },
  allowedEndpoints: ["https://<yourAllowedService>.webpubsub.azure.com"]
});

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

#### <a name="only-allow-specified-endpoints"></a>指定されたエンドポイントのみを許可する

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler("chat", {
  allowedEndpoints: [
    "https://<yourAllowedService1>.webpubsub.azure.com",
    "https://<yourAllowedService2>.webpubsub.azure.com"
  ]
});

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

#### <a name="set-custom-event-handler-path"></a>カスタム イベント ハンドラーのパスを設定する

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler("chat", {
  path: "customPath1"
});

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  // Azure WebPubSub Upstream ready at http://localhost:3000/customPath1
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

#### <a name="set-and-read-connection-state"></a>接続状態の設定と読み取りを行う

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");

const handler = new WebPubSubEventHandler("chat", {
  handleConnect(req, res) {
    // You can set the state for the connection, it lasts throughout the lifetime of the connection
    res.setState("calledTime", 1);
    res.success();
  },
  handleUserEvent(req, res) {
    var calledTime = req.context.states.calledTime++;
    console.log(calledTime);
    // You can also set the state here
    res.setState("calledTime", calledTime);
    res.success();
  }
});

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

### <a name="troubleshooting"></a>トラブルシューティング

#### <a name="enable-logs"></a>ログの有効化

このライブラリの使用時にデバッグ ログを表示するには、次の環境変数を取得します。

- SignalR クライアント ライブラリからデバッグ ログを取得する

```bash
export AZURE_LOG_LEVEL=verbose
```

ログを有効にする方法の詳細については、[@azure/logger パッケージに関するドキュメント](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/core/logger)を参照してください。

#### <a name="live-trace"></a>ライブ トレース

ライブトラフィックを表示するには、Web PubSub サービス ポータルから **ライブ トレース** を使用します。

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/

## <a name="next-steps"></a>次のステップ

[!INCLUDE [next step](includes/include-next-step.md)]