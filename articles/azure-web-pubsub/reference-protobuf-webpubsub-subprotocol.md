---
title: リファレンス - Azure Web PubSub でサポートされる Protobuf WebSocket サブプロトコル `protobuf.webpubsub.azure.v1`
description: このリファレンスでは、Azure Web PubSub でサポートされる WebSocket サブプロトコル `protobuf.webpubsub.azure.v1` について説明します。
author: chenyl
ms.author: chenyl
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 8e801391e5ac2ebe2a4dee276f525885ff84f449
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997801"
---
#  <a name="the-azure-web-pubsub-supported-protobuf-websocket-subprotocol"></a>Azure Web PubSub でサポートされる Protobuf WebSocket サブプロトコル
     
このドキュメントでは、サブプロトコル `protobuf.webpubsub.azure.v1` について説明します。

クライアントがこのサブプロトコルを使用している場合、送信データ フレームと受信データ フレームの両方がプロトコル バッファー (protobuf) ペイロードである必要があります。

## <a name="overview"></a>概要

サブプロトコル `protobuf.webpubsub.azure.v1` を使用すると、クライアントでは、上流サーバーへのラウンド トリップではなく、直接の発行またはサブスクライブ (PubSub) を行うことができます。 `protobuf.webpubsub.azure.v1` サブプロトコルを持つ WebSocket 接続を、PubSub WebSocket クライアントと呼びます。

たとえば、JavaScript では、次のコードを使用して、protobuf サブプロトコルを使用する PubSub WebSocket クライアントを作成できます。

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```

シンプルな WebSocket クライアントの場合、サーバーにはクライアントからイベントを処理するために *必要な* ロールがあります。 シンプルな WebSocket 接続では、メッセージを送信するときには必ず `message` イベントをトリガーします。また、常にサーバー側に依存して、メッセージを処理したり他の操作を行ったりします。 `protobuf.webpubsub.azure.v1` サブプロトコルを使用することで、許可されているクライアントは、[参加要求](#join-groups)を使用してグループに参加し、[発行要求](#publish-messages)を直接使用してグループにメッセージを発行できます。 クライアントは、[イベント要求](#send-custom-events)を使用して、メッセージが属する *イベント* をカスタマイズすることで、さまざまな上流イベント ハンドラにメッセージをルーティングすることもできます。

> [!NOTE]
> 現在、Web PubSub サービスは [proto3](https://developers.google.com/protocol-buffers/docs/proto3) のみをサポートしています。

## <a name="permissions"></a>アクセス許可

以前の PubSub WebSocket クライアントの説明でお気づきかもしれませんが、クライアントから他のクライアントに発行できるのは、その処理が *承認されている* 場合に限定されます。 次の表に示されているように、クライアントのロールによって *初期* アクセス権限が決定されます。

| ロール | アクセス許可 |
|---|---|
| 指定なし | クライアントは、イベント要求を送信できます。 |
| `webpubsub.joinLeaveGroup` | クライアントは、どのグループについても、参加または脱退が可能です。 |
| `webpubsub.sendToGroup` | クライアントは、どのグループにもメッセージを発行できます。 |
| `webpubsub.joinLeaveGroup.<group>` | クライアントは、グループ `<group>` について、参加または脱退が可能です。 |
| `webpubsub.sendToGroup.<group>` | クライアントはグループ `<group>` にメッセージを発行できます。 |
| | |

REST API またはサーバー SDK により、サーバー側でクライアントのアクセス許可を動的に許可または取り消すこともできます。

## <a name="requests"></a>Requests

すべての要求メッセージは、次の protobuf 形式に従います。

```protobuf
syntax = "proto3";

import "google/protobuf/any.proto";

message UpstreamMessage {
    oneof message {
        SendToGroupMessage send_to_group_message = 1;
        EventMessage event_message = 5;
        JoinGroupMessage join_group_message = 6;
        LeaveGroupMessage leave_group_message = 7;
    }

    message SendToGroupMessage {
        string group = 1;
        optional uint64 ack_id = 2;
        MessageData data = 3;
    }

    message EventMessage {
        string event = 1;
        MessageData data = 2;
        optional uint64 ack_id = 3;
    }
    
    message JoinGroupMessage {
        string group = 1;
        optional uint64 ack_id = 2;
    }

    message LeaveGroupMessage {
        string group = 1;
        optional uint64 ack_id = 2;
    }
}

message MessageData {
    oneof data {
        string text_data = 1;
        bytes binary_data = 2;
        google.protobuf.Any protobuf_data = 3;
    }
}
```

### <a name="join-groups"></a>グループに参加する

形式:

`join_group_message.group` をグループ名に設定します。

* `ackId` は各要求の ID で、一意である必要があります。 要求の処理結果を通知するために、サービスから[確認応答メッセージ](#ack-response)が送信されます。 詳細については、[AckId と確認応答](./concept-client-protocols.md#ackid-and-ack-response)に関するページを参照してください。

### <a name="leave-groups"></a>グループを脱退する

形式:

`leave_group_message.group` をグループ名に設定します。

* `ackId` は各要求の ID で、一意である必要があります。 要求の処理結果を通知するために、サービスから[確認応答メッセージ](#ack-response)が送信されます。 詳細については、[AckId と確認応答](./concept-client-protocols.md#ackid-and-ack-response)に関するページを参照してください。

### <a name="publish-messages"></a>メッセージを発行する

形式:

* `ackId` は各要求の ID で、一意である必要があります。 要求の処理結果を通知するために、サービスから[確認応答メッセージ](#ack-response)が送信されます。 詳細については、[AckId と確認応答](./concept-client-protocols.md#ackid-and-ack-response)に関するページを参照してください。

暗黙的な`dataType` があり、これは設定した `MessageData` の `data` に基づいて、`protobuf`、`text`、または `binary` のいずれかにすることができます。 受信側クライアントは `dataType` を利用して、コンテンツを正しく処理できます。

* `protobuf`: `send_to_group_message.data.protobuf_data` を設定した場合、暗黙的な `dataType` は `protobuf` です。 `protobuf_data` は、[任意の](https://developers.google.com/protocol-buffers/docs/proto3#any)メッセージ型にすることができます。 他のすべてのクライアントは protobuf でエンコードされたバイナリを受け取り、protobuf SDK によって逆シリアル化できます。 テキスト ベースのコンテンツのみをサポートするクライアント (例: `json.webpubsub.azure.v1`) は、Base64 でエンコードされたバイナリを受け取ります。

* `text`: `send_to_group_message.data.text_data` を設定した場合、暗黙的な `dataType` は `text` です。 `text_data` は文字列である必要があります。 他のプロトコルを使用するすべてのクライアントは、UTF-8 でエンコードされた文字列を受け取ります。

* `binary`: `send_to_group_message.data.binary_data` を設定した場合、暗黙的な `dataType` は `binary` です。 `binary_data` はバイト配列である必要があります。 他のプロトコルを使用するすべてのクライアントは、protobuf エンコードされていない未加工のバイナリを受け取ります。 テキスト ベースのコンテンツのみをサポートするクライアント (例: `json.webpubsub.azure.v1`) は、Base64 でエンコードされたバイナリを受け取ります。

#### <a name="case-1-publish-text-data"></a>ケース 1: テキスト データを発行する

`send_to_group_message.group` を `group` に設定し、`send_to_group_message.data.text_data` を `"text data"` に設定します。

* グループ `group` 内の protobuf サブプロトコル クライアントはバイナリ フレームを受け取り、[DownstreamMessage](#responses) を使用して逆シリアル化できます。

* グループ `group` 内の JSON サブプロトコル クライアントは以下を受け取ります。

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "group",
        "dataType" : "text",
        "data" : "text data"
    }
    ```

* グループ `group` 内の生のクライアントは、文字列 `text data` を受け取ります。

#### <a name="case-2-publish-protobuf-data"></a>ケース 2: protobuf データを発行する

顧客メッセージがある場合を想定します。

```
message MyMessage {
    int32 value = 1;
}
```

`send_to_group_message.group` を `group` に、`send_to_group_message.data.protobuf_data` を `Any.pack(MyMessage)` に設定し、`value = 1` を指定します。

* グループ `group` 内の protobuf サブプロトコル クライアントはバイナリ フレームを受け取り、[DownstreamMessage](#responses) を使用して逆シリアル化できます。

* グループ `group` 内のサブプロトコル クライアントは以下を受け取ります。

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "G",
        "dataType" : "protobuf",
        "data" : "Ci90eXBlLmdvb2dsZWFwaXMuY29tL2F6dXJlLndlYnB1YnN1Yi5UZXN0TWVzc2FnZRICCAE=" // Base64-encoded bytes
    }
    ```

    > [!NOTE]
    > データは、Base64 でエンコードされた逆シリアル化可能な protobuf バイナリです。 

次の protobuf 定義を使用し、`Any.unpack()` を使用して逆シリアル化できます。

```protobuf
syntax = "proto3";

message MyMessage {
    int32 value = 1;
}
```

* グループ `group` 内の生のクライアントは、バイナリ フレームを受け取ります。

    ```
    # Show in hexadecimal
    0A 2F 74 79 70 65 2E 67 6F 6F 67 6C 65 61 70 69 73 2E 63 6F 6D 2F 61 7A 75 72 65 2E 77 65 62 70 75 62 73 75 62 2E 54 65 73 74 4D 65 73 73 61 67 65 12 02 08 01
    ```

#### <a name="case-3-publish-binary-data"></a>ケース 3: バイナリ データを発行する

`send_to_group_message.group` を `group` に設定し、`send_to_group_message.data.binary_data` を `[1, 2, 3]` に設定します。

* グループ `group` 内の protobuf サブプロトコル クライアントはバイナリ フレームを受け取り、[DownstreamMessage](#responses) を使用して逆シリアル化できます。

* グループ `group` 内の JSON サブプロトコル クライアントは以下を受け取ります。

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "group",
        "dataType" : "binary",
        "data" : "AQID", // Base64-encoded [1,2,3]
    }
    ```

    JSON サブプロトコル クライアントはテキスト ベースのメッセージングのみをサポートします。バイナリは常に Base64 でエンコードされます。

* グループ `group` 内の生クライアントは、バイナリ フレーム内のバイナリ データを受け取ります。

    ```
    # Show in hexadecimal
    01 02 03
    ```

### <a name="send-custom-events"></a>カスタム イベントを送信する

暗黙的な`dataType` があり、これは設定した `dataType` に基づいて、`protobuf`、`text`、または `binary` のいずれかにすることができます。 受信側クライアントは `dataType` を利用して、コンテンツを正しく処理できます。

* `protobuf`: `event_message.data.protobuf_data` を設定した場合、暗黙的な `dataType` は `protobuf` です。 `protobuf_data` には、サポートされている任意の protobuf 型を指定できます。 イベント ハンドラーは protobuf でエンコードされたバイナリを受け取り、任意の protobuf SDK によって逆シリアル化できます。

* `text`: `event_message.data.text_data` を設定した場合、暗黙的な値は `text` です。 `text_data` は文字列である必要があります。 イベント ハンドラーは、UTF-8 でエンコードされた文字列を受け取ります。

* `binary`: `event_message.data.binary_data` を設定した場合、暗黙的な値は `binary` です。 `binary_data` はバイト配列である必要があります。 イベント ハンドラーは、生のバイナリ フレームを受け取ります。

#### <a name="case-1-send-an-event-with-text-data"></a>ケース 1: テキスト データを含むイベントを送信する

`event_message.data.text_data` を `"text data"` に設定します。

アップストリーム イベント ハンドラーは、次のような要求を受け取ります。 CloudEvents HTTP 要求の `Content-Type` は `text/plain` です。ここで、`dataType`=`text` になります。

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: text/plain
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.<event_name>
ce-source: /client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub_name}
ce-eventName: <event_name>

text data

```

#### <a name="case-2-send-an-event-with-protobuf-data"></a>ケース 2: protobuf データを含むイベントを送信する

次の顧客メッセージを受信したとします。

```
message MyMessage {
    int32 value = 1;
}
```

`event_message.data.protobuf_data` を `any.pack(MyMessage)` に設定し、`value = 1` を指定します。

アップストリーム イベント ハンドラーは、次のような要求を受け取ります。 CloudEvents HTTP 要求の `Content-Type` は `application/x-protobuf` です。ここで、`dataType`=`protobuf` になります。

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.<event_name>
ce-source: /client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub_name}
ce-eventName: <event_name>

// Just show in hexadecimal; read it as binary
0A 2F 74 79 70 65 2E 67 6F 6F 67 6C 65 61 70 69 73 2E 63 6F 6D 2F 61 7A 75 72 65 2E 77 65 62 70 75 62 73 75 62 2E 54 65 73 74 4D 65 73 73 61 67 65 12 02 08 01
```

データは有効な protobuf バイナリです。 次の `proto` および `any.unpack()` を使用して、逆シリアル化できます。

```protobuf
syntax = "proto3";

message MyMessage {
    int32 value = 1;
}
```

#### <a name="case-3-send-an-event-with-binary-data"></a>ケース 3: バイナリ データを含むイベントを送信する

`send_to_group_message.binary_data` を `[1, 2, 3]` に設定します。

アップストリーム イベント ハンドラーは、次のような要求を受け取ります。 `dataType`=`binary` の場合、CloudEvents HTTP 要求の `Content-Type` は `application/octet-stream` です。 

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/octet-stream
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.<event_name>
ce-source: /client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub_name}
ce-eventName: <event_name>

// Just show in hexadecimal; you need to read it as binary
01 02 03 
```

WebSocket フレームは、テキスト メッセージ フレームの場合の `text` 形式であるか、`binary` メッセージ フレームの場合の UTF8 でエンコードされたバイナリです。

メッセージが、記述された形式と一致していない場合、サービスによってクライアントが拒否されます。

## <a name="responses"></a>応答

すべての応答メッセージは、次の protobuf 形式に従います。

```protobuf
message DownstreamMessage {
    oneof message {
        AckMessage ack_message = 1;
        DataMessage data_message = 2;
        SystemMessage system_message = 3;
    }
    
    message AckMessage {
        uint64 ack_id = 1;
        bool success = 2;
        optional ErrorMessage error = 3;
    
        message ErrorMessage {
            string name = 1;
            string message = 2;
        }
    }

    message DataMessage {
        string from = 1;
        optional string group = 2;
        MessageData data = 3;
    }

    message SystemMessage {
        oneof message {
            ConnectedMessage connected_message = 1;
            DisconnectedMessage disconnected_message = 2;
        }
    
        message ConnectedMessage {
            string connection_id = 1;
            string user_id = 2;
        }

        message DisconnectedMessage {
            string reason = 2;
        }
    }
}
```

クライアントによって受信されるメッセージは、`ack`、`message`、`system` の 3 つのタイプのいずれかになります。 

### <a name="ack-response"></a>確認応答

要求に `ackId` が含まれている場合、サービスからは、この要求に対する確認応答が返されます。 クライアント実装では、次を含むこの ACK メカニズムを処理する必要があります。
* `async` `await` 操作の ACK 応答を待機する。 
* 一定の期間中に ACK 応答が受信されていない場合にタイムアウト チェックを行う。

クライアント実装では必ず、`success` の状態が `true` または `false` のどちらであるかを最初に確認する必要があります。 `success` の状態が `false` の場合、クライアントは `error` プロパティからエラーの詳細を読み取れます。

### <a name="message-response"></a>メッセージ応答

クライアントは、クライアントが参加しているグループから発行されたメッセージを受信できます。 または、サーバーが特定のクライアントまたは特定のユーザーにメッセージを送信するときに、サーバー管理ロールからメッセージを受信できます。

次のシナリオでは、常に `DownstreamMessage.DataMessage` メッセージが表示されます。

- メッセージがグループからのものである場合、`from` は `group` です。 メッセージがサーバーからのものである場合、`from` は `server` です。
- メッセージがグループからのものである場合、`group` はグループ名です。

送信者の `dataType` は、次のいずれかのメッセージを送信します。 
* `dataType` が `text` の場合、`message_response_message.data.text_data` を使用します。 
* `dataType` が `binary` の場合、`message_response_message.data.binary_data` を使用します。 
* `dataType` が `protobuf` の場合、`message_response_message.data.protobuf_data` を使用します。 
* `dataType` が `json` の場合、`message_response_message.data.text_data` を使用します。また、コンテンツはシリアル化された JSON 文字列です。

### <a name="system-response"></a>システム応答

Web PubSub サービスでは、システム関連の応答をクライアントに送信することもできます。 

#### <a name="connected"></a>接続中

クライアントがサービスに接続すると、`DownstreamMessage.SystemMessage.ConnectedMessage` メッセージが表示されます。

#### <a name="disconnected"></a>[Disconnected]\(切断済み\)

サーバーによって接続が閉じられるとき、またはサービスによってクライアントが拒否されるとき、`DownstreamMessage.SystemMessage.DisconnectedMessage` メッセージを受け取ります。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [next step](includes/include-next-step.md)]
