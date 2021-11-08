---
title: リファレンス - Azure Web PubSub の CloudEvents 拡張機能
description: このリファレンスでは、Azure Web PubSub サービス用に定義されている CloudEvents 拡張機能について説明します
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 52c9d3f303b657a437beba31e6e6945346397e54
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997364"
---
#  <a name="cloudevents-extension-for-azure-web-pubsub"></a>Azure Web PubSub の CloudEvents 拡張機能

このサービスは、[CloudEvents HTTP プロトコル](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md)を使用して、アップストリームの Webhook にクライアント イベントを配信します。

サービスからサーバーへのデータ送信は、常に CloudEvents `binary` 形式です。

- [Webhook の検証](#protection)
- [Web PubSub CloudEvents の属性の拡張](#extension)
- [イベント](#events)
    - ブロック イベント
        - [システム `connect` イベント](#connect)
        - [ユーザー イベント](#message)
    - 非ブロック イベント
        - [システム `connected` イベント](#connected)
        - [システム `disconnected` イベント](#disconnected)

## <a name="webhook-validation"></a>Webhook の検証

<a name="protection"></a>

Webhook の検証は、[CloudEvents](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection) に従います。 要求のヘッダーには常に `WebHook-Request-Origin: xxx.webpubsub.azure.com` が含まれます。

配信ターゲットでイベントの配信が許可されている場合にのみ、`WebHook-Allowed-Origin` ヘッダーを含めることによって要求に応答する必要があります。次に例を示します。

`WebHook-Allowed-Origin: *`

または:

`WebHook-Allowed-Origin: xxx.webpubsub.azure.com`

現在、[WebHook-Request-Rate](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#414-webhook-request-rate) と [WebHook-Request-Callback](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#413-webhook-request-callback) はサポートされていません。


## <a name="web-pubsub-cloudevents-attribute-extension"></a>Web PubSub CloudEvents の属性の拡張
<a name="extension"></a>

> また、HTTP 仕様は似たパターンに従うようになっており、拡張 HTTP ヘッダーにプレフィックスとして X- が付いていないことも指摘されました。

この拡張機能では、Web PubSub によって生成されるすべてのイベントに使用される属性が定義されています。

### <a name="attributes"></a>属性

| 名前 | 種類 | 説明 | 例|
|--|--|--|--|
| `userId` | `string` | 接続が認証されるユーザー | |
| `hub` | `string` | 接続が属しているハブ | |
| `connectionId` | `string` | connectionId はクライアント接続に対して一意です | |
| `eventName` | `string` | プレフィックスのないイベントの名前 | |
| `subprotocol` | `string` | クライアントで使用されているサブプロトコル (ある場合) | |
| `connectionState` | `string` | 接続の状態を定義します。 同じ応答ヘッダーを使用して、状態の値をリセットできます。 複数の `connectionState` ヘッダーは使用できません。 内部に複雑な文字が含まれている場合は、base64 で文字列値をエンコードします。たとえば、この属性を使用して `base64(jsonString)` で複合オブジェクトを渡せます。| |
| `signature` | `string` | 受信要求が予期される配信元からのものであるかどうかを検証するための、アップストリーム Webhook の署名。 サービスによって、プライマリ アクセス キーとセカンダリ アクセス キーの両方を HMAC キーとして使用して、値が計算されます: `Hex_encoded(HMAC_SHA256(accessKey, connectionId))`。 上流では、要求を処理する前に、それが有効かどうかを確認する必要があります。 | |

## <a name="events"></a>events

イベントには 2 つの種類があります。1 つは、サービスがイベントの応答を待ってから続行する "*ブロック*" イベントです。 もう 1 つは、サービスが次のメッセージを処理する前にイベントの応答を待たない "*非ブロック*" イベントです。

- ブロック イベント
    - [システム `connect` イベント](#connect)
    - [ユーザー イベント](#message)
- 非ブロック イベント
    - [システム `connected` イベント](#connected)
    - [システム `disconnected` イベント](#disconnected)

### <a name="system-connect-event"></a>システム `connect` イベント
<a name="connect"></a>

* `ce-type`: `azure.webpubsub.sys.connect`
* `Content-Type`: `application/json`

#### <a name="request-format"></a>要求の形式:

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json; charset=utf-8
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.sys.connect
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: connect

{
    "claims": {},
    "query": {},
    "headers": {},
    "subprotocols": [],
    "clientCertificates": [
        {
            "thumbprint": "ABC"
        }
    ]
}

```

#### <a name="success-response-format"></a>成功応答の形式:
* 状態コード:
    * `204`: 成功。コンテンツはありません。
    * `200`: 成功。コンテンツは JSON 形式である必要があり、次のプロパティが許可されます。
* ヘッダー `ce-connectionState`: このヘッダーが存在する場合、この接続の接続状態はヘッダーの値に更新されます。 接続状態を更新できるのは *ブロック* イベントのみである点に注意してください。 次の例では、base64 でエンコードされた JSON 文字列を使用して、接続の複雑な状態を格納します。
* 
```HTTP
HTTP/1.1 200 OK
ce-connectionState: eyJrZXkiOiJhIn0=

{
    "groups": [],
    "userId": "",
    "roles": [],
    "subprotocol": ""
}

```

* `subprotocols`

    `connect` イベントによって、クライアントから上流にサブプロトコルと認証情報が転送されます。 Azure SignalR Service により、状態コードを使用して、要求が WebSocket プロトコルにアップグレードされるかどうかが判断されます。

    要求に `subprotocols` プロパティが含まれる場合、サーバーはサポートしている 1 つのサブプロトコルを返す必要があります。 サーバーでどのサブプロトコルも使用する必要がない場合は、応答で `subprotocol` プロパティを送信 **しない** ようにする必要があります。 [空のヘッダーを送信することは無効です](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_servers#Subprotocols)。

* `userId`: `{auth-ed user ID}`

    サービスでは匿名接続が許可されているので、サービスにクライアント接続のユーザー ID を通知するのは `connect` イベントの役割です。 応答ペイロード `userId` が存在する場合、サービスによってそこからユーザー ID が読み取られます。 要求のクレームからも、`connect` イベントの応答ペイロードからも、ユーザー ID を読み取ることができない場合、接続は削除されます。

<a name="connect_response_header_group"></a>
 
* `groups`: `{groups to join}`

    このプロパティにより、ユーザーがこの接続を 1 つ以上のグループに追加する便利な方法が提供されます。 これにより、この接続を何らかのグループに追加するために別の呼び出しを行う必要はありません。

* `roles`: `{roles the client has}`
    
    このプロパティにより、アップストリームでクライアントを承認するための方法が提供されます。 ロールが異なると、クライアントに対して定義される初期アクセス許可が異なります。これは、クライアントが PubSub WebSocket クライアントである場合に便利です。 アクセス許可の詳細については、[クライアントのアクセス許可](./concept-client-protocols.md#permissions)に関する記事を参照してください。

#### <a name="error-response-format"></a>エラー応答の形式:

* `4xx`: エラー。上流からの応答が、クライアント要求への応答として返されます。

```HTTP
HTTP/1.1 401 Unauthorized
```

### <a name="system-connected-event"></a>システム `connected` イベント
<a name="connected"></a> クライアントによる WebSocket ハンドシェイクが完了し、正常に接続されると、サービスによって上流が呼び出されます。

* `ce-type`: `azure.webpubsub.sys.connected`
* `Content-Type`: `application/json`
* `ce-connectionState`: `eyJrZXkiOiJhIn0=`

要求本文は空の JSON です。

#### <a name="request-format"></a>要求の形式:

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json; charset=utf-8
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.sys.connected
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: connect
ce-subprotocol: abc
ce-connectionState: eyJrZXkiOiJhIn0=

{}

```

#### <a name="response-format"></a>応答の形式:

`2xx`: 成功時の応答。

`connected` は非同期イベントであり、応答の状態コードが非成功の場合、サービスによってログにエラーが記録されます。

```HTTP
HTTP/1.1 200 OK
```


### <a name="system-disconnected-event"></a>システム `disconnected` イベント
<a name="disconnected"></a>
**connect** イベントから `2xx` 状態コードを返された場合、クライアントの要求が完了すると、`disconnected` イベントが **常に** トリガーされます。

* `ce-type`: `azure.webpubsub.sys.disconnected`
* `Content-Type`: `application/json`

#### <a name="request-format"></a>要求の形式:

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json; charset=utf-8
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.sys.disconnected
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: disconnect
ce-subprotocol: abc
ce-connectionState: eyJrZXkiOiJhIn0=

{
    "reason": "{Reason}"
}

```

* `reason`

    `reason` では、クライアントが切断された理由が示されています。


#### <a name="response-format"></a>応答の形式:

`2xx`: 成功時の応答。

`disconnected` は非同期イベントであり、応答の状態コードが非成功の場合、サービスによってログにエラーが記録されます。

```HTTP
HTTP/1.1 200 OK
```


### <a name="user-event-message-for-the-simple-websocket-clients"></a>シンプル WebSocket クライアントに対するユーザー イベント `message`
<a name="message"></a> すべての WebSocket メッセージ フレームに対し、サービスによって上流のイベント ハンドラーが呼び出されます。

* `ce-type`: `azure.webpubsub.user.message`
* `Content-Type`: バイナリ フレームの場合は `application/octet-stream`。テキスト フレームの場合は `text/plain`。 

UserPayload は、クライアントが送信するものです。

#### <a name="request-format"></a>要求の形式:

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/octet-stream | text/plain | application/json
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.message
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: message
ce-connectionState: eyJrZXkiOiJhIn0=

UserPayload

```

#### <a name="success-response-format"></a>成功応答の形式

* status code
    * `204`: 成功。コンテンツはありません。
    * `200`: 成功。`UserResponsePayload` の形式は、応答の `Content-Type` によって異なります。
* `Content-Type`: バイナリ フレームの場合は `application/octet-stream`。テキスト フレームの場合は `text/plain`。 
* ヘッダー `Content-Type`: バイナリ フレームの場合は `application/octet-stream`。テキスト フレームの場合は `text/plain`。 
* ヘッダー `ce-connectionState`: このヘッダーが存在する場合、この接続の接続状態はヘッダーの値に更新されます。 接続状態を更新できるのは *ブロック* イベントのみである点に注意してください。 次の例では、base64 でエンコードされた JSON 文字列を使用して、接続の複雑な状態を格納します。

`Content-Type` が `application/octet-stream` の場合、サービスからクライアントに `binary` WebSocket フレームを使用して `UserResponsePayload` が送信されます。 `Content-Type` が `text/plain` の場合、サービスからクライアントに `text` WebSocket フレームを使用して `UserResponsePayload` が送信されます。 

```HTTP
HTTP/1.1 200 OK
Content-Type: application/octet-stream (for binary frame) or text/plain (for text frame)
Content-Length: nnnn
ce-connectionState: eyJrZXkiOiJhIn0=

UserResponsePayload
```

#### <a name="error-response-format"></a>エラー応答の形式
状態コードが成功でない場合は、エラー応答と見なされます。 `message` 応答状態コードが成功でない場合、接続は **切断** されます。

### <a name="user-custom-event-custom_event-for-pubsub-websocket-clients"></a>PubSub WebSocket クライアントに対するユーザー カスタム イベント `{custom_event}`
<a name="custom_event"></a>

すべての有効なカスタム イベント メッセージについて、サービスによりイベント ハンドラー Webhook が呼び出されます。

#### <a name="case-1-send-event-with-text-data"></a>ケース 1: テキスト データを含むイベントを送信する:
```json
{
    "type": "event",
    "event": "<event_name>",
    "dataType" : "text",
    "data": "text data"
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
ce-subprotocol: json.webpubsub.azure.v1
ce-connectionState: eyJrZXkiOiJhIn0=

text data

```

#### <a name="case-2-send-event-with-json-data"></a>ケース 2: JSON データでイベントを送信する:
```json
{
    "type": "event",
    "event": "<event_name>",
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
ce-subprotocol: json.webpubsub.azure.v1
ce-connectionState: eyJrZXkiOiJhIn0=

{
    "hello": "world"
}

```

#### <a name="case-3-send-event-with-binary-data"></a>ケース 3: バイナリ データでイベントを送信する:
```json
{
    "type": "event",
    "event": "<event_name>",
    "dataType" : "binary",
    "data": "aGVsbG8gd29ybGQ=" // base64 encoded binary
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
ce-subprotocol: json.webpubsub.azure.v1

<binary data>

```

#### <a name="success-response-format"></a>成功応答の形式

```HTTP
HTTP/1.1 200 OK
Content-Type: application/octet-stream | text/plain | application/json
Content-Length: nnnn

UserResponsePayload
```
* status code
    * `204`: 成功。コンテンツはありません。
    * `200`: 成功。PubSub WebSocket クライアントに送信されるデータは、`Content-Type` に依存します。 
* ヘッダー `ce-connectionState`: このヘッダーが存在する場合、この接続の接続状態はヘッダーの値に更新されます。 接続状態を更新できるのは *ブロック* イベントのみである点に注意してください。 次の例では、base64 でエンコードされた JSON 文字列を使用して、接続の複雑な状態を格納します。
* ヘッダー `Content-Type` が `application/octet-stream` の場合、サービスからクライアントに `binary` として `dataType` を使用して `UserResponsePayload` が返送されます。ペイロードは base64 でエンコードされています。 応答のサンプル:
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType": "binary",
        "data" : "aGVsbG8gd29ybGQ="
    }
    ```
* `Content-Type` が `text/plain` の場合、サービスからクライアントに `dataType` として `text` を使用して `UserResponsePayload` が送信されます。ペイロードは文字列です。
* `Content-Type` が `application/json` の場合、サービスからクライアントに `dataType`=`json` を使用して `UserResponsePayload` が送信されます。応答のペイロードの本文は `data` 値トークンです。


#### <a name="error-response-format"></a>エラー応答の形式
状態コードが成功でない場合は、エラー応答と見なされます。 `{custom_event}` 応答状態コードが成功でない場合、接続は **切断** されます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [next step](includes/include-next-step.md)]
