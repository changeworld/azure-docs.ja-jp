---
title: Azure Web PubSub の WebSocket クライアント プロトコル
description: この記事では、Azure Web PubSub のクライアント プロトコルの概要について説明します。
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 58fef1f2ada19af2ff81ef27d5ff5ab2ad4cebf7
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997951"
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

**シンプル WebSocket クライアント** とは、その名前が示すように、単純な WebSocket 接続です。 これにも独自のカスタム サブプロトコルを使用できます。 

たとえば、JavaScript では、次のようなコードを使用して簡単な WebSocket クライアントを作成できます。

```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')
```

## <a name="the-pubsub-websocket-client"></a>PubSub WebSocket クライアント

**PubSub WebSocket クライアント** は、Azure Web PubSub サービスによって定義されたサブプロトコルを使用する WebSocket クライアントです。

* `json.webpubsub.azure.v1`
* `protobuf.webpubsub.azure.v1`

**PubSub WebSocket クライアント** では、[アクセス許可](#permissions)がある場合、サービスでサポートされているサブプロトコルを使用してメッセージをグループに直接発行できます。

### <a name="the-jsonwebpubsubazurev1-subprotocol"></a>`json.webpubsub.azure.v1` サブプロトコル

[JSON サブプロトコルの詳細をこちらで](reference-json-webpubsub-subprotocol.md)確認してください。

#### <a name="create-a-pubsub-websocket-client"></a>PubSub WebSocket クライアントの作成

```js
var pubsubClient = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

#### <a name="join-a-group-from-the-client-directly"></a>クライアントから直接グループに参加する

```js
let ackId = 0;
pubsubClient.send(    
    JSON.stringify({
        type: 'joinGroup',
        group: 'group1',
        ackId: ++ackId
    }));
```

#### <a name="send-messages-to-a-group-from-the-client-directly"></a>クライアントから直接グループにメッセージを送信する

```js
let ackId = 0;
pubsubClient.send(    
    JSON.stringify({
        type: 'sendToGroup',
        group: 'group1',
        ackId: ++ackId,
        dataType: "json",
        data: {
            "hello": "world"
        }
    }));
```

### <a name="the-protobufwebpubsubazurev1-subprotocol"></a>`protobuf.webpubsub.azure.v1` サブプロトコル

プロトコル バッファー (protobuf) は、言語的にもプラットフォーム的にも中立なバイナリベースのプロトコルであり、バイナリ データの送信が簡単になります。 protobuf には、Java、Python、Objective-C、C#、C++ などの多くの言語のクライアントを生成するためのツールが用意されています。 [Protobuf の詳細を表示](https://developers.google.com/protocol-buffers)。

たとえば、JavaScript では、次のコードを使用して、protobuf サブプロトコルを使用する **PubSub WebSocket クライアント** を作成できます。

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```

[protobuf サブプロトコルの詳細をこちらで](reference-protobuf-webpubsub-subprotocol.md)確認してください。


### <a name="ackid-and-ack-response"></a>AckId と確認応答

**PubSub WebSocket クライアント** では、`joinGroup`、`leaveGroup`、`sendToGroup`、`event` の各メッセージの `ackId` プロパティをサポートしています。 `ackId` を使用する場合、要求が処理されたら確認応答メッセージを受け取ることができます。 "ファイア アンド フォーゲット" のシナリオでは、`ackId` を省略することを選択できます。 この記事では、`ackId` を指定する場合としない場合の動作の違いについて説明します。

#### <a name="behavior-when-no-ackid-specified"></a>`ackId` を指定しない場合の動作

`ackId` が指定されていない場合、"ファイア アンド フォーゲット" です。 メッセージの仲介時にエラーが発生した場合でも、通知を受け取る手段はありません。

#### <a name="behavior-when-ackid-specified"></a>`ackId` を指定する場合の動作

##### <a name="idempotent-publish"></a>べき等発行

`ackId` は uint64 数値であり、同じ接続 ID を持つクライアント内で一意である必要があります。Web PubSub サービスによって `ackId` が記録され、同じ `ackId` を持つメッセージは同じメッセージとして扱われます。 サービスは同じメッセージを複数回仲介することを拒否しますが、これは、再試行時にメッセージの重複を避けるために役立ちます。 たとえば、`ackId=5` のメッセージを送信したクライアントが `ackId=5` の確認応答を受信できない場合、クライアントは再試行し、同じメッセージを再び送信します。 場合によっては、メッセージは既に仲介されているが、何らかの理由で確認応答が失われ、サービスは再試行を拒否し、理由が `Duplicate` の確認応答を返します。

#### <a name="ack-response"></a>確認応答

Web PubSub サービスは、`ackId` を持つ要求ごとに確認応答を送信します。

形式:
```json
{
    "type": "ack",
    "ackId": 1, // The ack id for the request to ack
    "success": false, // true or false
    "error": {
        "name": "Forbidden|InternalServerError|Duplicate",
        "message": "<error_detail>"
    }
}
```

* `ackId` は要求を関連付けます。

* `success` はブール値であり、要求がサービスによって正常に処理されたかどうかを示します。 `false` の場合、クライアントでは `error` を確認する必要があります。

* `error` が存在するのは、`success` が `false` であり、異なる `name` のための異なるロジックがクライアントに必要な場合だけです。 将来、種類としては `name` が増える可能性があることを想定する必要があります。
    - `Forbidden`: クライアントには要求に対するアクセス許可がありません。 必要なロールをクライアントに追加する必要があります。
    - `InternalServerError`: 何らかの内部エラーがサービスで発生しました。 再試行が必要です。
    - `Duplicate`: 同じ `ackId` を持つメッセージがサービスによって既に処理されています。

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

クライアントのアクセス許可は、複数の方法で付与できます。

#### <a name="1-assign-the-role-to-the-client-when-generating-the-access-token"></a>1. アクセス トークン生成時にクライアントにロールを割り当てる

クライアントは JWT トークンを使用してサービスに接続できます。トークンのペイロードは、クライアントの `role` などの情報を伝達できます。 クライアントに対する JWT トークンに署名するときに、クライアント固有のロールを付与することで、クライアントにアクセス許可を付与することができます。

たとえば、`group1` と `group2` にメッセージを送信するためのアクセス許可を持つ JWT トークンに署名する場合、次のようになります。 

```js
let token = await serviceClient.getClientAccessToken({
    roles: [ "webpubsub.sendToGroup.group1", "webpubsub.sendToGroup.group2" ]
});
```

#### <a name="2-assign-the-role-to-the-client-with-the-connect-event-handler"></a>2. `connect` イベント ハンドラーを使用してクライアントにロールを割り当てる

クライアントのロールは、`connect` イベント ハンドラーの登録時にも設定できます。アップストリーム イベント ハンドラーは、`connect` イベントを処理するときにクライアントの `roles` を Web PubSub サービスに返すことができます。

たとえば、JavaScript で、そのような処理を実行するように `handleConnect` イベントを構成できます。

```js
let handler = new WebPubSubEventHandler("hub1", {
  handleConnect: (req, res) => {
    // auth the connection and set the userId of the connection
    res.success({
      roles: [ "webpubsub.sendToGroup.group1", "webpubsub.sendToGroup.group2" ]
    });
  },
});
```

#### <a name="3-assign-the-role-to-the-client-through-rest-apis-or-server-sdks-during-runtime"></a>3. 実行中に REST API またはサーバー SDK を使用してクライアントにロールを割り当てる

```js
let service = new WebPubSubServiceClient("<your_connection_string>", "test-hub");
await service.grantPermission("<connection_id>", "joinLeaveGroup", { targetName: "group1" });
```

## <a name="next-steps"></a>次のステップ

[!INCLUDE [next step](includes/include-next-step.md)]
