---
title: Azure Web PubSub の WebSocket クライアント プロトコル
description: この記事では、Azure Web PubSub のクライアント プロトコルの概要について説明します。
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 8f78dbcdecc552e94c3d871f610ef227a1795f8e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128576262"
---
#  <a name="websocket-client-protocols-for-azure-web-pubsub"></a>Azure Web PubSub の WebSocket クライアント プロトコル

クライアントは、標準の [WebSocket](https://datatracker.ietf.org/doc/html/rfc6455) プロトコルを使用して Azure Web PubSub に接続します。

## <a name="service-endpoints"></a>サービス エンドポイント
Web PubSub サービスには、クライアントが接続する 2 種類のエンドポイントが用意されています。
* `/client/hubs/{hub}`
* `/client/?hub={hub}`

`{hub}` は、さまざまなアプリケーションの分離として機能する必須パラメーターです。 パスまたはクエリのいずれかで設定できます。

## <a name="authorization"></a>承認

クライアントからサービスへの接続には、JSON Web トークン (JWT) が使用されます。 トークンは、`/client/?hub={hub}&access_token={token}` のようにクエリ文字列、または `Authorization: Bearer {token}` のように `Authorization` ヘッダーに含めることができます。

一般的な承認ワークフローを次に示します。

1. クライアントからアプリケーション サーバーに対してネゴシエートされます。 アプリケーション サーバーには承認ミドルウェアが含まれています。ここでクライアントの要求が処理され、クライアントがサービスに接続できるように JWT に署名されます。
1. アプリケーション サーバーからクライアントに対して JWT とサービス URL が返されます。
1. クライアントから Web PubSub サービスに接続するために、アプリケーション サーバーから返された URL と JWT が使用されます。

<a name="simple_client"></a>

## <a name="the-simple-websocket-client"></a>シンプル WebSocket クライアント

シンプル WebSocket クライアントとは、その名前が示すように、単純な WebSocket 接続です。 これにも独自のカスタム サブプロトコルを使用できます。 

たとえば、JavaScript では、次のようなコードを使用して簡単な WebSocket クライアントを作成できます。

```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')
```

## <a name="the-pubsub-websocket-client"></a>PubSub WebSocket クライアント

PubSub WebSocket クライアントは、次の 2 つのサブプロトコルをサポートしています。
* `json.webpubsub.azure.v1`
* `protobuf.webpubsub.azure.v1`

#### <a name="the-json-subprotocol"></a>JSON サブプロトコル

たとえば、JavaScript では、次のコードを使用して、JSON サブプロトコルを使用する PubSub WebSocket クライアントを作成できます。

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

#### <a name="the-protobuf-subprotocol"></a>protobuf サブプロトコル

プロトコル バッファー (protobuf) は、言語的にもプラットフォーム的にも中立なバイナリベースのプロトコルであり、バイナリ データの送信が簡単になります。 protobuf には、Java、Python、Objective-C、C#、C++ などの多くの言語のクライアントを生成するためのツールが用意されています。 [Protobuf の詳細を表示](https://developers.google.com/protocol-buffers)。

たとえば、JavaScript では、次のコードを使用して、protobuf サブプロトコルを使用する PubSub WebSocket クライアントを作成できます。

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```

クライアントがサブプロトコルを使用しているときは、送信と受信のデータ フレームの両方が JSON ペイロードであると想定されます。 承認済みクライアントは、Azure Web PubSub サービスを介して、メッセージを他のクライアントに直接発行できます。

### <a name="permissions"></a>アクセス許可

以前の PubSub WebSocket クライアントの説明でお気づきかもしれませんが、クライアントから他のクライアントに発行できるのは、その処理が "*承認されている*" 場合に限定されます。 クライアントのアクセス許可は、接続されているとき、または接続の有効期間中に付与できます。

| ロール | アクセス許可 |
|---|---|
| 指定なし | クライアントは、イベント要求を送信できます。 |
| `webpubsub.joinLeaveGroup` | クライアントは、どのグループについても、参加または脱退が可能です。 |
| `webpubsub.sendToGroup` | クライアントは、どのグループにもメッセージを発行できます。 |
| `webpubsub.joinLeaveGroup.<group>` | クライアントは、グループ `<group>` について、参加または脱退が可能です。 |
| `webpubsub.sendToGroup.<group>` | クライアントはグループ `<group>` にメッセージを発行できます。 |
| | |

JSON サブプロトコルの詳細については、[JSON サブプロトコル](./reference-json-webpubsub-subprotocol.md)に関するページを参照してください。

protobuf サブプロトコルの詳細については、[protobuf サブプロトコル](./reference-protobuf-webpubsub-subprotocol.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [next step](includes/include-next-step.md)]
