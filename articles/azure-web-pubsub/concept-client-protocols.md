---
title: Azure Web PubSub の WebSocket クライアント プロトコル
description: Azure Web PubSub のクライアント プロトコルの概要
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: e08f595e2dc80abe06fa68d1a3e30ac68ff0097c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426348"
---
#  <a name="websocket-client-protocols-for-azure-web-pubsub"></a>Azure Web PubSub の WebSocket クライアント プロトコル

クライアントは、標準の [WebSocket](https://datatracker.ietf.org/doc/html/rfc6455) プロトコルを使用して Azure Web PubSub に接続します。

## <a name="service-endpoint"></a>サービス エンドポイント
このサービスには、クライアントが接続する 2 種類のエンドポイントが用意されています。
1. `/client/hubs/{hub}`
2. `/client/?hub={hub}`

`{hub}` は、さまざまなアプリケーションの分離として機能する必須パラメーターです。 これはパスまたはクエリ内で設定できます。

## <a name="auth"></a>Auth
1. クライアントが JWT トークンを使用してサービスに接続する

### <a name="1-the-client-connects-using-the-jwt-token"></a>1. クライアントは JWT トークンを使用して接続します

JWT トークンは、クエリ文字列 `/client/?hub={hub}&access_token={token}` または `Authorization` ヘッダー: `Authorization: Bearer {token}` で指定できます。

一般的なワークフロー:
1. クライアントはアプリケーション サーバーとネゴシエートします。 アプリケーション サーバーには Auth ミドルウェアがあり、これによってクライアント要求を処理し、クライアントがサービスに接続するための JWT トークンに署名します。
2. アプリケーション サーバーは JWT トークンとサービス URL をクライアントに返します
3. クライアントは、アプリケーション サーバーから返された URL と JWT トークンを使用して、Web PubSub サービスに接続しようとします

<a name="simple_client"></a>

## <a name="the-simple-websocket-client"></a>シンプル WebSocket クライアント
シンプル WebSocket クライアントとは、その名前が示すように、単純な WebSocket 接続です。 これもそのカスタム サブプロトコルを使用することができます。 

たとえば JS では、以下を使用してシンプル WebSocket クライアントを作成できます。
```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')

```

## <a name="the-pubsub-websocket-client"></a>PubSub WebSocket クライアント

PubSub WebSocket クライアントでは、`json.webpubsub.azure.v1` と `protobuf.webpubsub.azure.v1` の 2 つのサブプロトコルがサポートされます

#### <a name="json-subprotocol"></a>Json サブプロトコル

たとえば、JS では、json サブプロトコルを含む PubSub WebSocket クライアントは、次を使用して作成できます。
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

#### <a name="protobuf-subprotocol"></a>Protobuf サブプロトコル

Protobuf は、言語に依存せず、プラットフォームに依存しないバイナリ ベースのプロトコルであり、バイナリ データの送信に非常に便利です。 Protobuf では、Java、Python、Objective-C、C#、C++ などの多くの言語のクライアントを生成するためのツールが提供されます。 [Protobuf の詳細を表示](https://developers.google.com/protocol-buffers)。

たとえば、JS では、Protobuf サブプロトコルを含む PubSub WebSocket クライアントは、次を使用して作成できます。
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```

クライアントがサブプロトコルを使用しているときは、送信データ フレームと受信データ フレームの両方が JSON ペイロードであると想定されます。 認証されたクライアントは、Azure Web PubSub サービスを介して、メッセージを他のクライアントに直接発行できます。

### <a name="permissions"></a>アクセス許可

PubSub WebSocket クライアントについて説明したときにお気付きかもしれませんが、クライアントは *認可されている* 場合にのみ、他のクライアントに発行できます。 クライアントのアクセス許可は、接続されているとき、または接続の有効期間中に付与できます。

| ロール | アクセス許可 |
|---|---|
| 指定なし | クライアントは、イベント要求を送信できます。
| `webpubsub.joinLeaveGroup` | クライアントは、どのグループについても、参加と脱退が可能です。
| `webpubsub.sendToGroup` | クライアントは、どのグループにもメッセージを発行できます。
| `webpubsub.joinLeaveGroup.<group>` | クライアントはグループ `<group>` について、参加と脱退が可能です。
| `webpubsub.sendToGroup.<group>` | クライアントはグループ `<group>` にメッセージを発行できます。

* Json サブプロトコルの詳細については、[JSON サブプロトコル](./reference-json-webpubsub-subprotocol.md)に関するページを参照してください。
* Protobuf サブプロトコルの詳細については、[Protobuf サブプロトコル](./reference-protobuf-webpubsub-subprotocol.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [next step](includes/include-next-step.md)]
