---
title: リファレンス - Azure Web PubSub 用の JavaScript SDK
description: このリファレンスでは、Azure Web PubSub サービス用 JavaScript SDK について説明します
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/01/2021
ms.openlocfilehash: 4571bb8581892525785c3f08e0c627bf20f511aa
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578349"
---
# <a name="javascript-sdk-for-azure-web-pubsub"></a>リファレンス - Azure Web PubSub 用の JavaScript SDK

JavaScript には、サービス クライアント ライブラリと Express ミドルウェアの 2 つのライブラリが用意されています。 以下のセクションでは、これらのライブラリの詳細について説明します。

<a name="service-client-library"></a>

## <a name="azure-web-pubsub-service-client-library-for-javascript"></a>JavaScript 用 Azure Web PubSub サービス クライアント ライブラリ

このライブラリを使用して、次の操作を実行できます。
- ハブとグループにメッセージを送信します。 
- 特定のユーザーと接続にメッセージを送信します。
- ユーザーと接続をグループに整理します。
- 接続を終了します
- 既存の接続のアクセス許可を付与、取り消し、確認します

[ソース コード](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub) |
[パッケージ (NPM)](https://www.npmjs.com/package/@azure/web-pubsub) |
[API リファレンス ドキュメント](/javascript/api/@azure/web-pubsub/) |
[製品ドキュメント](./index.yml) |
[サンプル][samples_ref]

### <a name="get-started"></a>はじめに

[Node.js](https://nodejs.org/) バージョン 8.x.x 以降。 さらに、次の前提条件を満たしていることを確認します。

- [Azure サブスクリプション][azure_sub]。
- 既存の Azure Web PubSub サービス インスタンス。

#### <a name="install-the-azureweb-pubsub-package"></a>`@azure/web-pubsub` パッケージのインストール

```bash
npm install @azure/web-pubsub
```

#### <a name="create-and-authenticate-webpubsubserviceclient"></a>`WebPubSubServiceClient` の作成と認証

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
```

エンドポイントと `AzureKeyCredential` を使用して `WebPubSubServiceClient` を認証することもできます。

```js
const { WebPubSubServiceClient, AzureKeyCredential } = require("@azure/web-pubsub");

const key = new AzureKeyCredential("<Key>");
const serviceClient = new WebPubSubServiceClient("<Endpoint>", key, "<hubName>");
```

### <a name="key-concepts"></a>主要な概念

[!INCLUDE [Terms](includes/terms.md)]

### <a name="examples"></a>例

#### <a name="broadcast-a-json-message-to-all-users"></a>すべてのユーザーに JSON メッセージをブロードキャストする

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
await serviceClient.sendToAll({ message: "Hello world!" });
```

#### <a name="broadcast-a-plain-text-message-to-all-users"></a>すべてのユーザーにプレーン テキスト メッセージをブロードキャストする

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
await serviceClient.sendToAll("Hi there!", { contentType: "text/plain" });
```

#### <a name="broadcast-a-binary-message-to-all-users"></a>すべてのユーザーにバイナリ メッセージをブロードキャストする

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

const payload = new Uint8Array(10);
await serviceClient.sendToAll(payload.buffer);
```

### <a name="troubleshooting"></a>トラブルシューティング

このライブラリの使用時にデバッグ ログを表示するには、次の環境変数を取得します。

- SignalR クライアント ライブラリからデバッグ ログを取得する

```bash
export AZURE_LOG_LEVEL=verbose
```

ログを有効にする方法の詳細については、[@azure/logger パッケージに関するドキュメント](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger)を参照してください。

<a name="express"></a>

## <a name="azure-web-pubsub-cloudevents-handlers-for-express"></a>Express 用 Azure Web Pubsub CloudEvents ハンドラー

Express ライブラリを使用して、次の操作を実行できます。
- 受信クライアント イベントを処理する Web PubSub CloudEvents ミドルウェアを追加する
  - 不正使用の検証要求を処理する
  - クライアント イベント要求の処理

[ソース コード](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub-express) |
[パッケージ (NPM)](https://www.npmjs.com/package/@azure/web-pubsub-express) |
[API リファレンス ドキュメント](/javascript/api/@azure/web-pubsub-express/) |
[製品ドキュメント](./index.yml) |
[サンプル][samples_ref]

### <a name="get-started"></a>はじめに

[Node.js](https://nodejs.org/)バージョン 8.x.x 以降、または [Express](https://expressjs.com/) バージョン 4.x.x 以降を使用してください。 さらに、次の前提条件を満たしていることを確認します。

- [Azure サブスクリプション][azure_sub]。
- 既存の Azure Web PubSub エンドポイント。

#### <a name="install-the-azureweb-pubsub-express-package"></a>`@azure/web-pubsub-express` パッケージのインストール

```bash
npm install @azure/web-pubsub-express
```

#### <a name="create-webpubsubeventhandler"></a>`WebPubSubEventHandler` を作成します。

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler(
  "chat",
  ["https://<yourAllowedService>.webpubsub.azure.com"],
  {
    handleConnect: (req, res) => {
      // auth the connection and set the userId of the connection
      res.success({
        userId: "<userId>"
      });
    }
  }
);

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

### <a name="key-concepts"></a>主要な概念

- **クライアント イベント**: クライアントは、接続のライフサイクル中にイベントを作成します。 たとえば、単純な WebSocket クライアント接続では、次のイベントが作成されます。
  - サービスに接続しようとしたときの `connect` イベント。
  - サービスに正常に接続されたときの `connected` イベント。
  - サービスにメッセージを送信したときの `message` イベント。
  - サービスから切断したときの `disconnected` イベント。

- **イベント ハンドラー**: イベント ハンドラーには、クライアント イベントを処理するロジックが含まれています。 イベント ハンドラーは、Azure portal または Azure CLI を使用して事前にサービスに登録し設定しておく必要があります。 サーバーは、通常、イベント ハンドラーのロジックをホストします。

### <a name="troubleshooting"></a>トラブルシューティング

#### <a name="dump-request"></a>ダンプ要求

受信要求を表示するには、`dumpRequest` を `true` に設定します。

#### <a name="live-trace"></a>ライブ トレース

ライブトラフィックを表示するには、Web PubSub サービス ポータルから **ライブ トレース** を使用します。

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript


## <a name="next-steps"></a>次のステップ

[!INCLUDE [next step](includes/include-next-step.md)]