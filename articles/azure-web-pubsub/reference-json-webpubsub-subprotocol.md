---
title: リファレンス - Azure Web PubSub でサポートされる JSON WebSocket サブプロトコル `json.webpubsub.azure.v1`
description: このリファレンスでは、Azure Web PubSub でサポートされる WebSocket サブプロトコル `json.webpubsub.azure.v1` について説明します
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/06/2021
ms.openlocfilehash: 2208c41ea49bae4ad3791a7e26f694e4cf4fbbd3
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997858"
---
#  <a name="azure-web-pubsub-supported-json-websocket-subprotocol"></a>Azure Web PubSub でサポートされる JSON WebSocket サブプロトコル
     
このドキュメントでは、サブプロトコル `json.webpubsub.azure.v1` について説明します。

クライアントがこのサブプロトコルを使用しているときには、送信データ フレームと受信データ フレームの両方が **JSON** ペイロードであると想定されます。

## <a name="overview"></a>概要

サブプロトコル `json.webpubsub.azure.v1` を使用すると、クライアントでは、上流サーバーへのラウンド トリップではなく、直接の発行またはサブスクライブを行うことができます。 PubSub WebSocket クライアントである `json.webpubsub.azure.v1` サブプロトコルを使用して WebSocket 接続を呼び出します。

たとえば JS では、以下を使用して PubSub WebSocket クライアントを作成できます。
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```
シンプル WebSocket クライアントの場合は、"*サーバー*" が、クライアントからのイベントを処理するためのロールを "持っている必要があります"。 シンプル WebSocket 接続では、メッセージを送信するときには必ず `message` イベントをトリガーします。そして常にサーバー側に依存して、メッセージを処理したり他の操作を行ったりします。 `json.webpubsub.azure.v1` サブプロトコルの助けにより、承認されたクライアントは、[参加要求](#join-groups)を使用してグループに参加し、[発行要求](#publish-messages)を直接使用してグループにメッセージを発行できます。 [イベント要求](#send-custom-events)を使用してメッセージが属する "*イベント*" をカスタマイズすることで、異なる上流 (イベント ハンドラー) にメッセージをルーティングすることもできます。

## <a name="permissions"></a>アクセス許可

PubSub WebSocket クライアントについて説明するときに、クライアントは "*承認*" されている場合にのみ、他のクライアントに発行できることに気付いたかも知れません。 クライアントの `role` により、クライアントが持つ "*最初*" のアクセス許可が決まります。

| Role | アクセス許可 |
|---|---|
| 指定なし | クライアントは、イベント要求を送信できます。
| `webpubsub.joinLeaveGroup` | クライアントは、どのグループについても、参加と脱退が可能です。
| `webpubsub.sendToGroup` | クライアントは、どのグループにもメッセージを発行できます。
| `webpubsub.joinLeaveGroup.<group>` | クライアントはグループ `<group>` について、参加と脱退が可能です。
| `webpubsub.sendToGroup.<group>` | クライアントはグループ `<group>` にメッセージを発行できます。

REST API またはサーバー SDK により、サーバー側でクライアントのアクセス許可を動的に許可または取り消すこともできます。

## <a name="requests"></a>Requests

### <a name="join-groups"></a>グループに参加する

形式:

```json
{
    "type": "joinGroup",
    "group": "<group_name>",
    "ackId" : 1
}
```

* `ackId` は各要求の ID で、一意である必要があります。 要求の処理結果を通知するために、サービスから[確認応答メッセージ](#ack-response)が送信されます。 詳細については、[AckId と確認応答](./concept-client-protocols.md#ackid-and-ack-response)に関するページを参照してください。

### <a name="leave-groups"></a>グループを脱退する

形式:

```json
{
    "type": "leaveGroup",
    "group": "<group_name>",
    "ackId" : 1
}
```

* `ackId` は各要求の ID で、一意である必要があります。 要求の処理結果を通知するために、サービスから[確認応答メッセージ](#ack-response)が送信されます。 詳細については、[AckId と確認応答](./concept-client-protocols.md#ackid-and-ack-response)に関するページを参照してください。

### <a name="publish-messages"></a>メッセージを発行する

形式:

```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "ackId" : 1,
    "noEcho": true|false,
    "dataType" : "json|text|binary",
    "data": {}, // data can be string or valid json token depending on the dataType 
}
```

* `ackId` は各要求の ID で、一意である必要があります。 要求の処理結果を通知するために、サービスから[確認応答メッセージ](#ack-response)が送信されます。 詳細については、[AckId と確認応答](./concept-client-protocols.md#ackid-and-ack-response)に関するページを参照してください。
* `noEcho` はオプションです。 true に設定した場合、このメッセージは同じ接続にエコーバックされません。 設定しない場合の既定値は false です。
* `dataType` には、`json`、`text`、`binary` のいずれかを指定できます。
     * `json`: `data` は JSON でサポートされているどの型でもよく、そのままで発行されます。`dataType` が指定されていない場合は、既定で `json` になります。
     * `text`: `data` は文字列形式である必要があり、文字列データが発行されます。
     * `binary`: `data` は base64 形式である必要があり、バイナリ データが発行されます。

#### <a name="case-1-publish-text-data"></a>ケース 1: テキスト データを発行する:
```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "dataType" : "text",
    "data": "text data",
    "ackId": 1
}
```

* このグループ `<group_name>` 内のサブプロトコル クライアントは以下を受け取ります。
```json
{
    "type": "message",
    "from": "group",
    "group": "<group_name>",
    "dataType" : "text",
    "data" : "text data"
}
```
* このグループ `<group_name>` 内の生クライアントが受け取るのは、文字列データ `text data` です。

#### <a name="case-2-publish-json-data"></a>ケース 2: JSON データを発行する:
```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "dataType" : "json",
    "data": {
        "hello": "world"
    }
}
```

* このグループ `<group_name>` 内のサブプロトコル クライアントは以下を受け取ります。
```json
{
    "type": "message",
    "from": "group",
    "group": "<group_name>",
    "dataType" : "json",
    "data" : {
        "hello": "world"
    }
}
```
* このグループ `<group_name>` 内の生クライアントが受け取るのは、シリアル化された文字列データ `{"hello": "world"}` です。


#### <a name="case-3-publish-binary-data"></a>ケース 3: バイナリ データを発行する:
```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "dataType" : "binary",
    "data": "<base64_binary>",
    "ackId": 1
}
```

* このグループ `<group_name>` 内のサブプロトコル クライアントは以下を受け取ります。
```json
{
    "type": "message",
    "from": "group",
    "group": "<group_name>",
    "dataType" : "binary",
    "data" : "<base64_binary>", 
}
```
* このグループ `<group_name>` 内の生クライアントが受け取るのは、バイナリ フレーム内の **バイナリ データ** です。

### <a name="send-custom-events"></a>カスタム イベントを送信する

形式:

```json
{
    "type": "event",
    "event": "<event_name>",
    "ackId": 1,
    "dataType" : "json|text|binary",
    "data": {}, // data can be string or valid json token depending on the dataType 
}
```

* `ackId` は各要求の ID で、一意である必要があります。 要求の処理結果を通知するために、サービスから[確認応答メッセージ](#ack-response)が送信されます。 詳細については、[AckId と確認応答](./concept-client-protocols.md#ackid-and-ack-response)に関するページを参照してください。

`dataType` には、`text`、`binary`、`json` のいずれかを指定できます。
* `json`: データは JSON でサポートされているどの型でもよく、そのままで発行されます。`dataType` が指定されていない場合は、既定で `json` になります。
* `text`: データは文字列形式である必要があり、文字列データが発行されます。
* `binary`: データは base64 形式である必要があり、バイナリ データが発行されます。

#### <a name="case-1-send-event-with-text-data"></a>ケース 1: テキスト データでイベントを送信する:
```json
{
    "type": "event",
    "event": "<event_name>",
    "ackId": 1,
    "dataType" : "text",
    "data": "text data", 
}
```

上流のイベント ハンドラーでは以下のような内容が受信され、`dataType`=`text` の場合、CloudEvents HTTP 要求の `Content-Type` は `text/plain` です

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

#### <a name="case-2-send-event-with-json-data"></a>ケース 2: JSON データでイベントを送信する:
```json
{
    "type": "event",
    "event": "<event_name>",
    "ackId": 1,
    "dataType" : "json",
    "data": {
        "hello": "world"
    }, 
}
```

上流のイベント ハンドラーでは以下のような内容が受信され、`dataType`=`json` の場合、CloudEvents HTTP 要求の `Content-Type` は `application/json` です

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

{
    "hello": "world"
}

```

#### <a name="case-3-send-event-with-binary-data"></a>ケース 3: バイナリ データでイベントを送信する:
```json
{
    "type": "event",
    "event": "<event_name>",
    "ackId": 1,
    "dataType" : "binary",
    "data": "base64_binary", 
}
```

上流のイベント ハンドラーでは以下のような内容が受信され、`dataType`=`binary` の場合、CloudEvents HTTP 要求の `Content-Type` は `application/octet-stream` です

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

binary

```

WebSocket フレームは、テキスト メッセージ フレームの場合の `text` 形式であるか、`binary` メッセージ フレームの場合の UTF8 でエンコードされたバイナリであるかです。

メッセージが、記述された形式と一致していない場合、サービスではクライアントを拒否します。

## <a name="responses"></a>応答

クライアントによって受信されるメッセージは、`ack`、`message`、`system` など、いくつかの型である場合があります。 

### <a name="ack-response"></a>確認応答

要求に `ackId` が含まれている場合、サービスからは、この要求に対する確認応答が返されます。 クライアント実装では、`async` `await` 操作の間は確認応答を待機し、一定時間内に確認応答を受信しない場合はタイムアウト チェックを行うなど、この確認メカニズムの処理を行う必要があります。

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

クライアント実装では必ず、最初に `success` が `true` であるか `false` であるかを調べる必要があります。 `success` が `false` である場合にのみ、クライアントは `error` からの読み取りを行います。

### <a name="message-response"></a>メッセージ応答

クライアントで受信できるメッセージは、そのクライアントが参加した 1 つのグループから発行されたか、サーバーから特定のクライアントまたは特定のユーザーにメッセージが送信される、サーバー管理ロールから発行されたものです。

1. メッセージがグループからのものの場合

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "<group_name>",
        "dataType": "json|text|binary",
        "data" : {} // The data format is based on the dataType
        "fromUserId": "abc"
    }
    ```

1. メッセージがサーバーからのものの場合。

    ```json
    {
        "type": "message",
        "from": "server",
        "dataType": "json|text|binary",
        "data" : {} // The data format is based on the dataType
    }
    ```

#### <a name="case-1-sending-data-hello-world-to-the-connection-through-rest-api-with-content-typetextplain"></a>ケース 1: `Content-Type`=`text/plain` と指定した REST API を介した、データ `Hello World` の接続への送信 
* シンプル WebSocket クライアントが受信するのは、データ `Hello World` が含まれる、WebSocket テキスト フレームです。
* PubSub WebSocket クライアントで受信する内容は次のとおりです。
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType" : "text",
        "data": "Hello World", 
    }
    ```

#### <a name="case-2-sending-data--hello--world-to-the-connection-through-rest-api-with-content-typeapplicationjson"></a>ケース 2: `Content-Type`=`application/json` と指定した REST API を介した、データ `{ "Hello" : "World"}` の接続への送信
* シンプル WebSocket クライアントが受信するのは、文字列化されたデータ `{ "Hello" : "World"}` が含まれる、WebSocket テキスト フレームです。
* PubSub WebSocket クライアントで受信する内容は次のとおりです。
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType" : "json",
        "data": {
            "Hello": "World"
        }
    }
    ```

REST API で、`application/json` コンテンツ タイプを使用して文字列 `Hello World` を送信する場合、シンプル WebSocket クライアントが受信するのは JSON 文字列です。これは、`"` で文字列をラップしている `"Hello World"` です。

#### <a name="case-3-sending-binary-data-to-the-connection-through-rest-api-with-content-typeapplicationoctet-stream"></a>ケース 3: `Content-Type`=`application/octet-stream` と指定した REST API を介した、バイナリ データの接続への送信
* 単純な WebSocket クライアントが受信するのは、バイナリ データが含まれる、WebSocket バイナリ フレームです。
* PubSub WebSocket クライアントで受信する内容は次のとおりです。
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType" : "binary",
        "data": "<base64_binary>"
    }
    ```

### <a name="system-response"></a>システム応答

Web PubSub サービスでは、システム関連の応答をクライアントに送信することもできます。 

#### <a name="connected"></a>接続中

接続がサービスに接続するとき。

```json
{
    "type": "system",
    "event": "connected",
    "userId": "user1",
    "connectionId": "abcdefghijklmnop",
}
```

#### <a name="disconnected"></a>[Disconnected]\(切断済み\)

サーバーによって接続が閉じられるとき、またはサービスによってクライアントが拒否されるとき。

```json
{
    "type": "system",
    "event": "disconnected",
    "message": "reason"
}
```

## <a name="next-steps"></a>次の手順

[!INCLUDE [next step](includes/include-next-step.md)]
