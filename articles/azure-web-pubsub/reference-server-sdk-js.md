---
title: リファレンス - Azure Web PubSub サービス用の JavaScript SDK
description: このリファレンスでは、Azure Web PubSub サービス用 JavaScript SDK について説明します
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 1d507f29b184403cb9ef31d84111af60f75c1584
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113909"
---
# <a name="javascript-sdk-for-the-azure-web-pubsub-service"></a>Azure Web PubSub サービス用の JavaScript SDK

JavaScript 用として、次の 2 つのライブラリが用意されています。
- [サービス クライアント ライブラリ](#service-client-library)
    - ハブとグループにメッセージを送信します。
    - 特定のユーザーと接続にメッセージを送信します。
    - ユーザーと接続をグループに整理します。
    - 接続を終了します
    - 既存の接続のアクセス許可を付与、取り消し、チェックします
- [受信クライアント イベント](#express) を処理する Express ミドルウェア
  - 不正使用の検証要求を処理する
  - クライアント イベント要求の処理

<a name="service-client-library"></a>

## <a name="azure-web-pubsub-service-client-library-for-javascript"></a>JavaScript 用 Azure Web PubSub サービス クライアント ライブラリ
このライブラリの用途は次のとおりです。

- ハブとグループにメッセージを送信します。
- 特定のユーザーと接続にメッセージを送信します。
- ユーザーと接続をグループに整理します。
- 接続を終了します
- 既存の接続のアクセス許可を付与、取り消し、チェックします

[ソース コード](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub) |
[パッケージ (NPM)](https://www.npmjs.com/package/@azure/web-pubsub) |
[API リファレンス ドキュメント](/javascript/api/@azure/web-pubsub/) |
[製品ドキュメント](https://aka.ms/awps/doc) |
[サンプル][samples_ref]

### <a name="getting-started"></a>はじめに

#### <a name="currently-supported-environments"></a>現在サポートされている環境

- [Node.js](https://nodejs.org/) バージョン 8.x.x 以上

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

#### <a name="enable-logs"></a>ログの有効化

このライブラリの使用時にデバッグ ログを表示するには、次の環境変数を取得します。

- SignalR クライアント ライブラリからデバッグ ログを取得する

```bash
export AZURE_LOG_LEVEL=verbose
```

ログを有効にする方法の詳細については、[@azure/logger パッケージに関するドキュメント](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger)を参照してください。

<a name="express"></a>

## <a name="azure-web-pubsub-cloudevents-handlers-for-express"></a>Express 用 Azure Web Pubsub CloudEvents ハンドラー

Express ライブラリを使用する場合:
- 受信クライアント イベントを処理する Web PubSub CloudEvents ミドルウェアを追加する
  - 不正使用の検証要求を処理する
  - クライアント イベント要求の処理

[ソース コード](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub-express) |
[パッケージ (NPM)](https://www.npmjs.com/package/@azure/web-pubsub-express) |
[API リファレンス ドキュメント](/javascript/api/@azure/web-pubsub-express/) |
[製品ドキュメント](https://aka.ms/awps/doc) |
[サンプル][samples_ref]

### <a name="getting-started"></a>はじめに

#### <a name="currently-supported-environments"></a>現在サポートされている環境

- [Node.js](https://nodejs.org/) バージョン 8.x.x 以上
- [Express](https://expressjs.com/) バージョン 4.x.x 以上

#### <a name="prerequisites"></a>前提条件

- [Azure サブスクリプション][azure_sub]。
- 既存の Azure Web PubSub エンドポイント。

#### <a name="1-install-the-azureweb-pubsub-express-package"></a>1. `@azure/web-pubsub-express` パッケージをインストールする

```bash
npm install @azure/web-pubsub-express
```

#### <a name="2-create-a-webpubsubeventhandler"></a>2. WebPubSubEventHandler を作成する

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

#### <a name="client-events"></a>クライアント イベント

イベントは、クライアント接続のライフサイクル中に作成されます。 たとえば、シンプル WebSocket クライアント接続では、サービスへの接続が試みられるときに `connect` イベント、サービスに正常に接続したときに `connected` イベント、サービスにメッセージを送信するときに `message` イベント、サービスから切断したときに `disconnected` イベントが作成されます。

#### <a name="event-handler"></a>イベント ハンドラー

イベント ハンドラーには、クライアント イベントを処理するロジックが含まれています。 イベント ハンドラーは、ポータルまたは Azure CLI を使用して事前にサービスに登録し設定しておく必要があります。 イベント ハンドラー ロジックをホストする場所は、通常、サーバー側と見なされます。

### <a name="troubleshooting"></a>トラブルシューティング

#### <a name="dump-request"></a>ダンプ要求

受信要求を表示するには、`dumpRequest` を `true` に設定します。

#### <a name="live-trace"></a>ライブ トレース

ライブトラフィックを表示するには、Web PubSub サービス ポータルから **ライブ トレース** を使用します。

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript


## <a name="next-steps"></a>次のステップ

[!INCLUDE [next step](includes/include-next-step.md)]
