---
title: Azure Web PubSub サービスの内部構造
description: Azure Web PubSub サービスの内部構造、アーキテクチャ、接続、データの送信方法について説明します。
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 660ae01280f116097d1a16282c8fed4faa18b6c2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132706972"
---
#  <a name="azure-web-pubsub-service-internals"></a>Azure Web PubSub サービスの内部構造

Azure Web PubSub サービスでは、シンプル [WebSocket](https://tools.ietf.org/html/rfc6455) 接続を使用して、メッセージを発行/サブスクライブする簡単な方法が提供されます。

- クライアントは、WebSocket のサポートがある任意の言語で記述できます
- 1 つの接続内でテキスト メッセージとバイナリ メッセージの両方がサポートされています
- クライアントがクライアント間メッセージの直接発行を行うための単純なプロトコル
- サービスによって WebSocket 接続が管理されます

## <a name="terms"></a>用語
* **サービス**: Azure Web PubSub サービス。

[!INCLUDE [Terms](includes/terms.md)]

* **クライアント接続** と **ConnectionId**: クライアントは `/client` エンドポイントに接続します。接続されると、サービスによって一意の `connectionId` がクライアント接続の一意の ID として生成されます。 その後、ユーザーはこの `connectionId` を使用してクライアント接続を管理できます。 詳細については、「[クライアント プロトコル](#client_protocol)」セクションを参照してください。

* **クライアント イベント**: イベントは、クライアント接続のライフサイクル中に作成されます。 たとえば、シンプル WebSocket クライアント接続では、サービスへの接続が試みられるときに `connect` イベント、サービスに正常に接続したときに `connected` イベント、サービスにメッセージを送信するときに `message` イベント、サービスから切断したときに `disconnected` イベントが作成されます。 *クライアント イベント* の詳細については、「[クライアント プロトコル](#client_protocol)」セクションを参照 してください。

* **イベント ハンドラー**: イベント ハンドラーには、クライアント イベントを処理するロジックが含まれています。 事前にポータルまたは Azure CLI から、サービスにイベント ハンドラーを登録して構成します。 詳細については、「[イベント ハンドラー](#event_handler)」セクションを参照してください。 イベント ハンドラー ロジックをホストする場所は、サーバー側と見なされます。

* **サーバー**: サーバーでは、クライアント イベントの処理、クライアント接続の管理、およびグループへのメッセージの発行を行うことができます。 サーバーは、クライアントと比較して信頼できます。 **サーバー** の詳細については、「[サーバー プロトコル](#server_protocol)」セクションを参照してください。

<a name="workflow"></a>

## <a name="workflow"></a>ワークフロー

![Web PubSub サービス ワークフローを示す図。](./media/concept-service-internals/workflow.png)

上のワークフロー グラフに示されているように:
1. *クライアント* は、WebSocket トランスポートを使用してサービス `/client` エンドポイントに接続します。 サービスは、すべての WebSocket フレームを、構成されているアップストリーム (サーバー) に転送します。 WebSocket 接続では、サーバーが処理する任意のカスタム サブプロトコルを使用して接続できます。また、サービスでサポートされているサブプロトコル `json.webpubsub.azure.v1` を使用して接続することもでき、これにより、クライアントが pub/sub を直接実行できます。 詳細については、「[クライアント プロトコル](#client_protocol)」を参照してください。
2. サービスは、さまざまなクライアント イベントで **CloudEvents HTTP プロトコル** を使用してサーバーを呼び出します。 [**CloudEvents**](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md) は、Cloud Native Computing Foundation (CNCF) によってホストされるイベントの構造とメタデータ記述の標準化されたプロトコルに依存しない定義です。 詳細については、「[サーバー プロトコル](#server_protocol)」を参照してください。
3. サーバーは、REST API を使用してサービスを呼び出し、クライアントにメッセージを送信したり、接続されているクライアントを管理したりできます。 詳細については、「[サーバー プロトコル](#server_protocol)」を参照してください

<a name="client_protocol"></a>

## <a name="client-protocol"></a>クライアント プロトコル

クライアント接続では、[WebSocket プロトコル](https://tools.ietf.org/html/rfc6455)を使用して、サービスの `/client` エンドポイントに接続します。 WebSocket プロトコルは、単一の TCP 接続で全二重通信チャネルを提供し、2011 年に RFC 6455 として IETF によって標準化されました。 ほとんどの言語に、WebSocket 接続を開始するためのネイティブ サポートがあります。 

ここのサービスでは、次の 2 種類のクライアントをサポートします。
- 1 つは[シンプル WebSocket クライアント](#simple_client)と呼ばれます
- もう 1 つは [PubSub WebSocket クライアント](#pubsub_client)と呼ばれます

<a name="simple_client"></a>

### <a name="the-simple-websocket-client"></a>シンプル WebSocket クライアント
シンプル WebSocket クライアントとは、その名前が示すように、単純な WebSocket 接続です。 これもそのカスタム サブプロトコルを使用することができます。 

たとえば JS では、以下を使用してシンプル WebSocket クライアントを作成できます。
```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')

```

次のシーケンス図に示すように、シンプル WebSocket クライアントはクライアント <-> サーバー アーキテクチャに従います。![クライアント接続のシーケンスを示す図。](./media/concept-service-internals/simple-client-sequence.png)


1. クライアントが WebSocket ハンドシェイクを開始すると、サービスが WebSocket ハンドシェイクのための `connect` イベント ハンドラー (サーバー) の呼び出しを試みます。 ユーザーは、このハンドラーを使用して WebSocket ハンドシェイクを処理し、使用するサブプロトコルを決定し、クライアントを認証して、クライアントをいくつかのグループに参加させることができます。
2. クライアントが正常に接続されると、サービスは `connected` イベント ハンドラーを呼び出します。 これは通知のようなものとして機能し、クライアントのメッセージの送信をブロックしません。 ユーザーは、このハンドラーを使用して何らかのデータ ストレージを実行し、クライアントに送信するメッセージで応答できます。
2. クライアントがメッセージを送信すると、サービスがイベント ハンドラー (サーバー) に対して `message` イベントをトリガーし、送信されるメッセージを処理します。 このイベントは、WebSocket フレームで送信されるメッセージを含む一般的なイベントです。 ユーザーは、独自にこのイベント ハンドラー内でメッセージをディスパッチする必要があります。
3. クライアントが切断された場合、サービスは、切断を検出すると、イベント ハンドラー (サーバー) に対して `disconnected` イベントのトリガーを試みます。

イベントは 2 つのカテゴリに分けられます。
* 同期イベント (ブロッキング) : 同期イベントにより、クライアント ワークフローがブロックされます。 このようなイベント トリガーが失敗すると、サービスはクライアント接続を切断します。
    * `connect`
    * `message`
* 非同期イベント (非ブロッキング) : 非同期イベントでは、クライアント ワークフローがブロックされず、それはアップストリーム イベント ハンドラーへの通知のようなものとして機能します。 このようなイベント トリガーが失敗すると、サービスによってエラーの詳細がログに記録されます。
    * `connected`
    * `disconnected`
    
#### <a name="scenarios"></a>シナリオ:
このような接続は、クライアントがサーバーにメッセージを送信し、サーバーが[イベント ハンドラー](#event_handler)を使用して受信メッセージを処理する、一般的なクライアント サーバー アーキテクチャで使用できます。 また、顧客がアプリケーション ロジックで既存の[サブプロトコル](https://www.iana.org/assignments/websocket/websocket.xml)を適用する場合にも使用できます。

<a name="pubsub_client"></a>

### <a name="the-pubsub-websocket-client"></a>PubSub WebSocket クライアント
このサービスでは、`json.webpubsub.azure.v1` と呼ばれる特定のサブプロトコルもサポートされています。これにより、クライアントはアップストリーム サーバーへのラウンド トリップではなく、発行/サブスクライブを直接実行できます。 PubSub WebSocket クライアントである `json.webpubsub.azure.v1` サブプロトコルを使用して WebSocket 接続を呼び出します。

たとえば JS では、以下を使用して PubSub WebSocket クライアントを作成できます。
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

PubSub WebSocket クライアントでは次が可能です。
* グループに参加する。例:
    ```json
    {
        "type": "joinGroup",
        "group": "<group_name>"
    }
    ```
* グループを脱退する。例:
    ```json
    {
        "type": "leaveGroup",
        "group": "<group_name>"
    }
    ```
* グループにメッセージを発行する。例:
    ```json
    {
        "type": "sendToGroup",
        "group": "<group_name>",
        "data": { "hello": "world" }
    }
    ```
* カスタム イベントをアップストリーム サーバーに送信する。例:

    ```json
    {
        "type": "event",
        "event": "<event_name>",
        "data": { "hello": "world" }
    }
    ```

[PubSub WebSocket サブプロトコル](./reference-json-webpubsub-subprotocol.md)に関するページで、`json.webpubsub.azure.v1` サブプロトコルの詳細を説明しています。

[シンプル WebSocket クライアント](#simple_client)の場合、*サーバー* は、クライアントからのイベントを処理するための MUST HAVE (必須) ロールであることにお気付きかもしれません。 シンプル WebSocket 接続では、メッセージを送信するときには必ず `message` イベントをトリガーします。そして常にサーバー側に依存して、メッセージを処理したり他の操作を行ったりします。 `json.webpubsub.azure.v1` サブプロトコルのおかげで、許可されているクライアントはグループに参加し、メッセージをグループに直接発行できます。 また、メッセージが属する *イベント* をカスタマイズすることで、別のアップストリーム (イベント ハンドラー) にメッセージをルーティングすることもできます。 

#### <a name="scenarios"></a>シナリオ:
このようなクライアントは、クライアントが相互に通信する必要がある場合に使用できます。 メッセージは `client1` からサービスに送信され、クライアントがその権限を持つ場合に、サービスによってメッセージが `client2` に直接配信されます。

Client1:

```js
var client1 = new WebSocket("wss://xxx.webpubsub.azure.com/client/hubs/hub1", "json.webpubsub.azure.v1");
client1.onmessage = e => {
    if (e.data) {
        var message = JSON.parse(e.data);
        if (message.type === "message" 
        && message.group === "Group1"){
            // Only print messages from Group1
            console.log(message.data);
        }
    }
};

client1.onopen = e => {
    client1.send(JSON.stringify({
        type: "joinGroup",
        group: "Group1"
    }));
};
```

Client2:

```js
var client2 = new WebSocket("wss://xxx.webpubsub.azure.com/client/hubs/hub1", "json.webpubsub.azure.v1");
client2.onopen = e => {
    client2.send(JSON.stringify({
        type: "sendToGroup",
        group: "Group1",
        data: "Hello Client1"
    });
};
```

上の例に示すように、`client1` が含まれる `Group1` にメッセージを発行することで、`client2` はデータを `client1` に直接送信します。

<a name="client_message_limit"></a>

### <a name="client-message-limit"></a>クライアント メッセージの制限
1 つの WebSocket フレームで許可される最大メッセージ サイズは **1 MB** です。

<a name="client_auth"></a>

### <a name="client-auth"></a>クライアント認証

#### <a name="auth-workflow"></a>認証ワークフロー

クライアントは、署名された JWT トークンを使用してサービスに接続します。 アップストリームでは、それが受信クライアントの `connect` イベント ハンドラーである場合に、クライアントを拒否できます。 イベント ハンドラーでは、クライアントが持つ `userId` と `role` を Webhook 応答に指定することで、クライアントを認証するか、または 401 でクライアントを拒否します。 「[イベント ハンドラー](#event_handler)」セクションで、これについて詳しく説明します。

次のグラフに、ワークフローについて説明します。

![クライアント認証ワークフローを示す図。](./media/concept-service-internals/client-connect-workflow.png)

PubSub WebSocket クライアントについて説明したときにお気付きかもしれませんが、クライアントは *認可されている* 場合にのみ、他のクライアントに発行できます。 クライアントの `role` により、クライアントが持つ "*最初*" のアクセス許可が決まります。

| ロール | アクセス許可 |
|---|---|
| 指定なし | クライアントはイベントを送信できます。
| `webpubsub.joinLeaveGroup` | クライアントは、どのグループについても、参加と脱退が可能です。
| `webpubsub.sendToGroup` | クライアントは、どのグループにもメッセージを発行できます。
| `webpubsub.joinLeaveGroup.<group>` | クライアントはグループ `<group>` について、参加と脱退が可能です。
| `webpubsub.sendToGroup.<group>` | クライアントはグループ `<group>` にメッセージを発行できます。

後のセクションで示されているとおりに、サーバー側では、[サーバー プロトコル](#connection_manager)によって、クライアントのアクセス許可を動的に許可または取り消すこともできます。

<a name="server_protocol"></a>

## <a name="server-protocol"></a>サーバー プロトコル

サーバー プロトコルは、サーバーがクライアント接続とグループを管理するための機能を提供します。

一般に、サーバー プロトコルには次の 2 つのロールがあります。
1. [イベント ハンドラー](#event_handler)
2. [Connection manager](#connection_manager)

<a name="event_handler"></a>

### <a name="event-handler"></a>イベント ハンドラー
イベント ハンドラーでは、受信クライアント イベントが処理されます。 イベント ハンドラーは、事前にポータルまたは Azure CLI を通じて登録し構成して、クライアント イベントがトリガーされたときに、そのイベントが処理されることが期待されているかどうかをサービスが識別できるようにします。 そして、`PUSH` モードを使用してイベント ハンドラーを呼び出します。そのイベント ハンドラーはサーバー側として、イベントがトリガーされたときに、サービスによって呼び出されるパブリックにアクセス可能なエンドポイントを公開します。 それは、**Webhook** として機能します。 

サービスは、[CloudEvents HTTP プロトコル](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md)を使用して、アップストリームの Webhook にクライアント イベントを配信します。

すべてのイベントに対して、登録されたアップストリームへの HTTP POST 要求を形成し、HTTP 応答を期待します。 

サービスからサーバーに送信されるデータは、常に CloudEvents `binary` 形式です。

![Web PubSub サービス イベント プッシュ モードを示す図。](./media/concept-service-internals/event-push.png)

#### <a name="upstream-and-validation"></a>アップストリームと検証

イベント ハンドラーは、事前にポータルまたは Azure CLI を通じて登録し構成して、クライアント イベントがトリガーされたときに、そのイベントが処理されることが期待されているかどうかをサービスが識別できるようにする必要があります。 パブリック プレビューでは、`PUSH` モードを使用してイベント ハンドラーを呼び出します。そのイベント ハンドラーはサーバー側として、イベントがトリガーされたときに、サービスによって呼び出されるパブリックにアクセス可能なエンドポイントを公開します。 それは、**Webhook** **アップストリーム** として機能します。 

Webhook エンドポイントを構成するときに、URL に `{event}` パラメーターを使用して URL テンプレートを定義できます。 サービスでは、クライアント要求を受信すると、Webhook URL の値が動的に計算されます。 たとえば、ハブ `/client/hubs/chat` にイベント ハンドラー URL パターン `http://host.com/api/{event}` が構成されている状態で、要求 `chat` を受信すると、クライアントは接続するときに、最初に URL `http://host.com/api/connect` に POST します。 これは、PubSub WebSocket クライアントがカスタム イベントを送信するときに役立つ可能性があり、イベント ハンドラーでは、さまざまなイベントをさまざまなアップストリームにディスパッチできます。 URL ドメイン名では `{event}` パラメーターを使用できないことに注意してください。

Azure portal または CLI を使用して、アップストリームにイベントハンドラーを設定すると、サービスによって [CloudEvents の不正使用防止](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection)に従ってアップストリームの Webhook が検証されます。 `WebHook-Request-Origin` 要求ヘッダーはサービス ドメイン名 `xxx.webpubsub.azure.com` に設定されており、これは応答にこのドメイン名を含むヘッダー `WebHook-Allowed-Origin` があることが期待されます。

検証を実行すると、`{event}` パラメーターは `validate` に解決されます。 たとえば、URL を `http://host.com/api/{event}` に設定しようとすると、サービスによって、`http://host.com/api/validate` に対する要求の **OPTIONS** が試みられ、応答が有効な場合にのみ、構成を正常に設定できます。

現時点では、[WebHook-Request-Rate](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#414-webhook-request-rate) と [WebHook-Request-Callback](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#413-webhook-request-callback) はサポートされていません。

#### <a name="authentication-between-service-and-webhook"></a>サービスと Webhook 間の認証
- 匿名モード
- `code` が構成済みの Webhook URL を介して提供される単純認証。
- AAD 認証。 
   - ポータル/CLI を介して、AAD の [アプリの登録] にクライアント シークレットを追加し、[クライアント シークレット] を Azure Web PubSub に提供します。
   - ポータル/CLI を介して Azure Web PubSub に [ID](../app-service/overview-managed-identity.md?tabs=dotnet) を提供します

<a name="connection_manager"></a>

### <a name="connection-manager"></a>[ODBC 入力元エディター]

サーバーは本質的に許可されているユーザーです。 *イベント ハンドラー ロール* のおかげで、サーバーはクライアントのメタデータ (`connectionId` や `userId` など) を認識できるため、次のことが可能になります。
   - クライアント接続を閉じる
   - メッセージをクライアントに送信する
   - 同じユーザーに属するクライアントにメッセージを送信する
   - クライアントをグループに追加する
   - 同じユーザーとして認証されたクライアントをグループに追加する
   - クライアントをグループから削除する
   - 同じユーザーとして認証されたクライアントをグループから削除する
   - グループにメッセージを発行する

また、PubSub クライアントの発行/参加アクセス許可を許可または取り消すこともできます。
   - 特定のグループまたはすべてのグループに参加/発行アクセス許可を許可する
   - 特定のグループまたはすべてのグループの参加/発行アクセス許可を取り消す
   - クライアントに、特定のグループまたはすべてのグループへの参加/発行アクセス許可があるかどうかを確認する
   
サービスによって、サーバーが接続管理を行うための REST API が提供されます。

![Web PubSub サービス接続マネージャーのワークフローを示す図。](./media/concept-service-internals/manager-rest.png)

詳細な REST API プロトコルは[こちら][rest]に定義されています。

### <a name="summary"></a>まとめ
*イベント ハンドラー ロール* では、サービスからサーバーへの通信が処理されますが、*マネージャー ロール* では、サーバーからサービスへの通信が処理されることにお気づきかもしれません。 このため、2 つのロールを組み合わせると、サービスとサーバー間のデータフローは、HTTP プロトコルを使用すると、次のようになります。

![Web PubSub サービスの双方向ワークフローを示す図。](./media/concept-service-internals/http-service-server.png)

[rest]: /rest/api/webpubsub/

## <a name="next-steps"></a>次の手順

[!INCLUDE [next step](includes/include-next-step.md)]