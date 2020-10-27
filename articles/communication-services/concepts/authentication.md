---
title: Azure Communication Services に対する認証
titleSuffix: An Azure Communication Services concept document
description: アプリケーションまたはサービスが Communication Services に対して認証を行うさまざまな方法について説明します。
author: matthewrobertson
manager: jken
services: azure-communication-services
ms.author: marobert
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 939c36cd62dab4362232aef0da8701b34a88c6ff
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2020
ms.locfileid: "92202958"
---
# <a name="authenticate-to-azure-communication-services"></a>Azure Communication Services に対する認証

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

この記事では、" *アクセス キー* " と " *ユーザー アクセス トークン* " を使用してクライアントを Azure Communication Services に対して認証する方法について説明します。 クライアントと Azure Communication Services とのやり取りはすべて認証される必要があります。

次の表に、Azure Communication Services クライアント ライブラリでサポートされる認証オプションを示します。

| クライアント ライブラリ | アクセス キー    | ユーザー アクセス トークン |
| -------------- | ------------- | ------------------ |
| 管理 | サポートされています     | サポートされていません      |
| SMS            | サポートされています     | サポートされていません      |
| チャット           | サポートされていません | サポートされています          |
| 呼び出し        | サポートされていません | サポートされています          |

ここでは、各認証オプションについて簡単に説明します。

- SMS および管理操作用の **アクセス キー** 認証。 アクセス キー認証は、信頼できるサービス環境で実行されているアプリケーションに適しています。 アクセス キーを使用して認証するために、クライアントによって[ハッシュベースのメッセージ認証コード (HMAC)](https://en.wikipedia.org/wiki/HMAC) が生成され、各 HTTP 要求の `Authorization` ヘッダーに含まれます。 詳細については、「[アクセス キーを使用して認証する](#authenticate-with-an-access-key)」を参照してください。
- チャットおよび通話用の **ユーザー アクセス トークン** 認証。 ユーザー アクセス トークンを使用すると、クライアント アプリケーションが Azure Communication Services に対して直接認証を行うことができます。 これらのトークンは、作成するサーバー側のトークン プロビジョニング サービス上で生成されます。 その後、クライアント デバイスに提供され、クライアント デバイスはそのトークンを使用して、チャットおよび通話のクライアント ライブラリを初期化します。 詳細については、「[ユーザー アクセス トークンを使用して認証する](#authenticate-with-a-user-access-token)」を参照してください。

## <a name="authenticate-with-an-access-key"></a>アクセス キーを使用して認証する

アクセス キー認証では、共有の秘密鍵を使用して、各 HTTP 要求に対して SHA256 アルゴリズムを使用して計算された HMAC を生成し、それを `HMAC-SHA256` スキームを使用して `Authorization` ヘッダーに送信します。

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

アクセス キー認証を使用する Azure Communication Services クライアント ライブラリは、リソースの接続文字列を使用して初期化される必要があります。 クライアント ライブラリを使用しない場合は、リソースのアクセス キーを使用して HMAC をプログラムで生成できます。 接続文字列の詳細については、[リソースのプロビジョニングに関するクイックスタート](../quickstarts/create-communication-resource.md)を参照してください。

### <a name="sign-an-http-request"></a>HTTP 要求に署名する

クライアント ライブラリを使用して Azure Communication Services REST API への HTTP 要求を行わない場合は、各 HTTP 要求に対して HMAC をプログラムで作成する必要があります。 以下の手順で、Authorization ヘッダーを作成する方法を示します。

1. `x-ms-date` ヘッダー内、または標準の HTTP ヘッダー `Date` 内で、要求の協定世界時 (UTC) タイムスタンプを指定します。 サービスはこれを検証することで、再生攻撃などの特定のセキュリティ攻撃を防ぎます。
1. SHA256 アルゴリズムを使用して HTTP 要求本文をハッシュし、それを `x-ms-content-sha256` ヘッダーを介して要求と共に渡します。
1. HTTP 動詞 (`GET` や `PUT` など)、HTTP 要求のパス、HTTP ヘッダー `Date`、`Host`、および `x-ms-content-sha256` の値を次の形式で連結して、署名対象文字列を作成します。
    ```
    VERB + "\n"
    URLPathAndQuery + "\n"
    DateHeaderValue + ";" + HostHeaderValue + ";" + ContentHashHeaderValue
    ```
1. 前の手順で作成した UTF-8 エンコード文字列の HMAC-256 署名を生成します。 次に、結果を Base64 としてエンコードします。 ストレージ アカウント キーを Base64 デコードする必要もあることに注意してください。 次の形式を使用します (疑似コードとして示しています)。
    ```
    Signature=Base64(HMAC-SHA256(UTF8(StringToSign), Base64.decode(<your_azure_storage_account_shared_key>)))
    ```
1. 次のように Authorization ヘッダーを指定します。
    ```
    Authorization="HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"  
    ```
    ここで、`<hmac-sha256-signature>` は、前の手順で計算された HMAC です。

## <a name="authenticate-with-a-user-access-token"></a>ユーザー アクセス トークンを使用して認証する

ユーザー アクセス トークンを使用すると、クライアント アプリケーションが Azure Communication Services に対して直接認証を行うことができます。 そのためには、管理クライアント ライブラリを使用してアプリケーション ユーザーを認証しユーザー アクセス トークンを発行する、信頼できるサービスを設定する必要があります。 アーキテクチャに関する考慮事項の詳細については、[クライアントとサーバーのアーキテクチャ](./client-and-server-architecture.md)の概念説明ドキュメントを参照してください。

`CommunicationClientCredential` クラスには、クライアント ライブラリにユーザー アクセス トークンの資格情報を提供し、そのライフサイクルを管理するためのロジックが含まれます。

### <a name="initialize-the-client-libraries"></a>クライアント ライブラリを初期化する

ユーザー アクセス トークン認証を必要とする Azure Communication Services クライアント ライブラリを初期化するには、まず `CommunicationClientCredential` クラスのインスタンスを作成し、それを使用して API クライアントを初期化します。

次のスニペットは、ユーザー アクセス トークンを使用してチャット クライアント ライブラリを初期化する方法を示しています。

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
// user access tokens should be created by a trusted service using the Administration client library
var token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
var userCredential = new CommunicationUserCredential(token);

// initialize the chat client library with the credential
var chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// user access tokens should be created by a trusted service using the Administration client library
const token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance with the AzureCommunicationUserCredential class
const userCredential = new AzureCommunicationUserCredential(token);

// initialize the chat client library with the credential
let chatClient = new ChatClient(ENDPOINT_URL, userCredential);
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
// user access tokens should be created by a trusted service using the Administration client library
let token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
let userCredential = try CommunicationUserCredential(token: token)

// initialize the chat client library with the credential
let chatClient = try CommunicationChatClient(credential: userCredential, endpoint: ENDPOINT_URL)
```

#### <a name="java"></a>[Java](#tab/java)

```java
// user access tokens should be created by a trusted service using the Administration client library
String token = "<valid-user-access-token>";

// create a CommunicationUserCredential instance
CommunicationUserCredential userCredential = new CommunicationUserCredential(token);

// Initialize the chat client
final ChatClientBuilder builder = new ChatClientBuilder();
builder.endpoint(ENDPOINT_URL)
    .credential(userCredential)
    .httpClient(HTTP_CLIENT);
ChatClient chatClient = builder.buildClient();
```

---

### <a name="refreshing-user-access-tokens"></a>ユーザー アクセス トークンを更新する

ユーザー アクセス トークンは、ユーザーのサービス中断を防ぐために再発行する必要がある、有効期間の短い資格情報です。 `CommunicationUserCredential` コンストラクターは、有効期限が切れる前にユーザー アクセス トークンを更新できるようにする更新コールバック関数を受け入れます。 このコールバックを使用して、信頼できるサービスから新しいユーザー アクセス トークンをフェッチする必要があります。

#### <a name="c"></a>[C#](#tab/csharp)

```csharp
var userCredential = new CommunicationUserCredential(
    initialToken: token,
    refreshProactively: true,
    tokenRefresher: cancellationToken => fetchNewTokenForCurrentUser(cancellationToken)
);
```

#### <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const userCredential = new AzureCommunicationUserCredential({
  tokenRefresher: async () => fetchNewTokenForCurrentUser(),
  refreshProactively: true,
  initialToken: token
});
```

#### <a name="swift"></a>[Swift](#tab/swift)

```swift
 let userCredential = try CommunicationUserCredential(initialToken: token, refreshProactively: true) { |completionHandler|
   let updatedToken = fetchTokenForCurrentUser()
   completionHandler(updatedToken, nil)
 }
```

#### <a name="java"></a>[Java](#tab/java)

```java
TokenRefresher tokenRefresher = new TokenRefresher() {
    @Override
    Future<String> getFetchTokenFuture() {
        return fetchNewTokenForCurrentUser();
    }
}

CommunicationUserCredential credential = new CommunicationUserCredential(tokenRefresher, token, true);
```
---

`refreshProactively` オプションを使用すると、トークンのライフサイクルの管理方法を指定できます。 既定では、トークンが古くなるとコールバックは API 要求をブロックし、更新を試みます。 `refreshProactively` が `true` に設定されている場合、コールバックはスケジュール設定され、トークンの有効期限が切れる前に非同期で実行されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ユーザー アクセス トークンの作成](../quickstarts/access-tokens.md)

詳細については、次の記事を参照してください。
- [クライアントとサーバーのアーキテクチャについて学習する](../concepts/client-and-server-architecture.md)
