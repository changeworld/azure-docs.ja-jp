---
title: クレーム チャレンジ、クレーム要求、およびクライアントの機能
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォームのクレーム チャレンジ、クレーム要求、およびクライアントの機能についての説明。
services: active-directory
author: knicholasa
manager: martinco
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 05/11/2021
ms.author: nichola
ms.reviewer: kkrishna, kylemar
ms.openlocfilehash: 6d1998673b3295129c494b4568e1ba6313984898
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067255"
---
# <a name="claims-challenges-claims-requests-and-client-capabilities"></a>クレーム チャレンジ、クレーム要求、およびクライアントの機能

*クレーム チャレンジ* とは、API から送信される応答であり、クライアント アプリケーションによって送信されたアクセス トークンのクレームが不十分であることを示します。 これは、トークンがその API に設定された条件付きアクセス ポリシーを満たしていないか、アクセス トークンが取り消されていることが原因である可能性があります。

*クレーム要求* はクライアント アプリケーションによって行われ、ユーザーを ID プロバイダーにリダイレクトして、満たされていない追加の要件を満たすクレームを含む新しいトークンを取得します。

[継続的アクセス評価 (CAE)](../conditional-access/concept-continuous-access-evaluation.md) や[条件付きアクセス認証コンテキスト](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/granular-conditional-access-for-sensitive-data-and-actions/ba-p/1751775)などの強化されたセキュリティ機能を使用するアプリケーションは、クレーム チャレンジを処理できるように準備する必要があります。

アプリケーションは、サービスの呼び出しで[クライアントの機能](#client-capabilities)を宣言する場合にのみ、[Microsoft Graph](/graph/overview) などの一般的なサービスからのクレーム チャレンジを受け取ります。

## <a name="claims-challenge-header-format"></a>クレーム チャレンジ ヘッダーの書式

クレーム チャレンジは、提示された[アクセス トークン](access-tokens.md)が承認されない場合に API によって返される `www-authenticate` ヘッダーとしてのディレクティブであり、代わりに適切な機能がある新しいアクセス トークンが必要です。 クレーム チャレンジは複数の部分 (応答の HTTP 状態コードと `www-authenticate` ヘッダー) で構成されています。このヘッダー自体にも複数の部分があり、クレーム ディレクティブが含まれている必要があります。

次に例を示します。

```https
HTTP 401; Unauthorized

www-authenticate =Bearer realm="", authorization_uri="https://login.microsoftonline.com/common/oauth2/authorize", error="insufficient_claims", claims="eyJhY2Nlc3NfdG9rZW4iOnsiYWNycyI6eyJlc3NlbnRpYWwiOnRydWUsInZhbHVlIjoiYzEifX19"
```

 **HTTP 状態コード**: **401 Unauthorized** である必要があります。

次のものを含む **www-authenticate 応答ヘッダー**。

| パラメーター    | 必須/省略可能 | 説明 |
|--------------|-------------|--------------|
| 認証の種類 | 必須 | **ベアラー** である必要があります。|
| Realm | 省略可能 | アクセスされるテナント ID またはテナント ドメイン名 (たとえば、microsoft.com)。 認証が[共通エンドポイント](howto-convert-app-to-be-multi-tenant.md#update-your-code-to-send-requests-to-common)を通過する場合は、空の文字列である必要があります。 |
| `authorization_uri` | 必須 | 必要に応じて対話型認証を実行できる承認エンドポイントの URI。 領域で指定する場合、テナント情報を authorization_uri に含める必要があります。 領域が空の文字列である場合、authorization_uri は[共通エンドポイント](howto-convert-app-to-be-multi-tenant.md#update-your-code-to-send-requests-to-common)に対して存在する必要があります。 |
| `error` | 必須 | クレーム チャレンジを生成する必要がある場合は、"insufficient_claims" である必要があります。 | 
| `claims` | エラーが "insufficient_claims" の場合は必須です。 | Base 64 でエンコードされた[クレーム要求](https://openid.net/specs/openid-connect-core-1_0.html#ClaimsParameter)を含む、引用符で囲まれた文字列。 クレーム要求は、JSON オブジェクトのトップ レベルにある"access_token" のクレームを要求する必要があります。 値 (要求されたクレーム) はコンテキストに依存します。このドキュメントで後ほど指定します。 サイズ上の理由から、証明書利用者アプリケーションは、base64 エンコードの前に JSON のミニファイ処理を行う必要があります。 上記の例の未加工の JSON は、{"access_token":{"acrs":{"essential":true,"value":"cp1"}}} です。 |

**401** 応答には、複数の `www-authenticate` ヘッダーが含まれる場合があります。 前の表のすべてのフィールドが、同じ `www-authenticate` ヘッダー内に含まれている必要があります。 クレーム チャレンジを含む `www-authenticate` ヘッダーには、他のフィールドを含めることが "*できます*"。 ヘッダー内のフィールドは順序指定されません。 RFC 7235 に従って、各パラメーター名は、認証スキームのチャレンジごとに 1 回だけ出現する必要があります。

## <a name="claims-request"></a>クレーム要求

アプリケーションがクレーム チャレンジを受け取った場合は、以前のアクセス トークンが有効とは見なされなくなったことを示しています。 このシナリオでは、アプリケーションがすべてのローカル キャッシュまたはユーザー セッションでトークンをクリアする必要があります。 その後、サインインしたユーザーを Azure Active Directory (Azure AD) にリダイレクトし、[OAuth 2.0 認証コード フロー](v2-oauth2-auth-code-flow.md)と *claims* パラメーターを使用して、満たされなかった追加の要件を満たす新しいトークンを取得する必要があります。

次に例を示します。

```https
GET https://login.microsoftonline.com/14c2f153-90a7-4689-9db7-9543bf084dad/oauth2/v2.0/authorize
?client_id=2810aca2-a927-4d26-8bca-5b32c1ef5ea9
&redirect_uri=https%3A%2F%contoso.com%3A44321%2Fsignin-oidc
&response_type=code
&scope=openid%20profile%20offline_access%20user.read%20Sites.Read.All
&response_mode=form_post
&login_hint=kalyan%ccontoso.onmicrosoft.com
&domain_hint=organizations
claims=%7B%22access_token%22%3A%7B%22acrs%22%3A%7B%22essential%22%3Atrue%2C%22value%22%3A%22c1%22%7D%7D%7D
```

クレーム チャレンジは、トークンが正常に取得され、チャレンジが必要なくなるまで、Azure AD の [/authorize](v2-oauth2-auth-code-flow.md#request-an-authorization-code) エンドポイントに対するすべての呼び出しの一部として渡される必要があります。

クレーム パラメーターを設定するには、開発者は次のことを行う必要があります。

1. 以前に受信した Base64 文字列をデコードします。
2. 文字列を URL エンコードし、claims パラメーターに **もう一度 を追加** します。

このフローが完了すると、アプリケーションは、ユーザーが必要な条件を満たしていることを証明する追加のクレームを含むアクセス トークンを受け取ります。

## <a name="client-capabilities"></a>クライアントの機能

クライアントの機能は、呼び出し元のクライアント アプリケーションがクレーム チャレンジを認識し、それに応じて応答をカスタマイズできるかどうかを Web API のようなリソース プロバイダーが検出するために役立ちます。 この機能は、すべての API クライアントがクレーム チャレンジを処理できるわけではなく、一部の旧バージョンでは別の応答が期待される場合に便利であることがあります。

[Microsoft Graph](/graph/overview) などの一般的なアプリケーションでは、呼び出し元のクライアント アプリが "*クライアントの機能*" を使用してクレーム チャレンジを処理できると宣言している場合にのみ、クレーム チャレンジが送信されます。

余分なトラフィックやユーザー エクスペリエンスへの影響を回避するために、Azure AD では、明示的にオプトインしない限り、チャレンジされたクレームをアプリで処理できるとは想定していません。 アプリケーションは、"cp1" 機能を使用して、それらを処理する準備ができていると宣言しない限り、クレーム チャレンジを受け取りません (また、CAE トークンなどの関連機能を使用できません)。

### <a name="how-to-communicate-client-capabilities-to-azure-ad"></a>クライアント機能を Azure AD に通信する方法

次のクレーム パラメーターの例は、クライアント アプリケーションがその機能を [OAuth 2.0 認証コード フロー](v2-oauth2-auth-code-flow.md)で Azure AD に通信する方法を示しています。

```json
Claims: {"access_token":{"xms_cc":{"values":["cp1"]}}}
```

MSAL ライブラリを使用する場合は、次のコードを使用します。

```c#
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
 .WithDefaultRedirectUri()
 .WithAuthority(authority)
 .WithClientCapabilities(new [] {"cp1"})
 .Build();*
```

Microsoft.Identity.Web を使用する場合は、次のコードを構成ファイルに追加できます。

```c#
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    // the remaining settings
    // ... 
    "ClientCapabilities": [ "cp1" ]
},
```

Azure AD に対する要求の内容の例を次に示します。

```https
GET https://login.microsoftonline.com/14c2f153-90a7-4689-9db7-9543bf084dad/oauth2/v2.0/authorize
?client_id=2810aca2-a927-4d26-8bca-5b32c1ef5ea9
&redirect_uri=https%3A%2F%contoso.com%3A44321%2Fsignin-oidc
&response_type=code
&scope=openid%20profile%20offline_access%20user.read%20Sites.Read.All
&response_mode=form_post
&login_hint=kalyan%ccontoso.onmicrosoft.com
&domain_hint=organizations
&claims=%7B%22access_token%22%3A%7B%22xms_cc%22%3A%7B%22values%22%3A%5B%22cp1%22%5D%7D%7D%7D
```

クレーム パラメーターのペイロードが既にある場合は、これを既存のセットに追加します。

たとえば、条件アクセス認証コンテキスト操作から次の応答を既に取得している場合、

```json
{"access_token":{"acrs":{"essential":true,"value":"c25"}}}
```

既存の **claims** ペイロードにクライアント機能を追加します。

```json
{"access_token":{"xms_cc":{"values":["cp1"]},"acrs":{"essential":true,"value":"c25"}}}
```

## <a name="receiving-xms_cc-claim-in-an-access-token"></a>アクセス トークンで xms_cc クレームを受け取る

クライアント アプリケーションがクレーム チャレンジを処理できるかどうかに関する情報を受け取るには、API 実装者は、アプリケーション マニフェストのオプションのクレームとして **xms_cc** を要求する必要があります。

アクセス トークン内の値が "cp1" の **xms_cc** クレームは、クライアント アプリケーションがクレーム チャレンジを処理できることを識別するための信頼できる方法です。 **xms_cc** は、省略可能なクレームであり、クライアントが "xms_cc" を使用してクレーム要求を送信した場合でも、アクセス トークンで常に発行されるとは限りません。 アクセス トークンに **xms_cc** クレームを含めるには、リソース アプリケーション (つまり、API 実装者) がアプリケーション マニフェストで [省略可能なクレーム](active-directory-optional-claims.md)として xms_cc を要求する必要があります。 **xms_cc** は、省略可能なクレームとして要求された場合、クライアント アプリケーションがクレーム要求で **xms_cc** を送信した場合にのみアクセス トークンに追加されます。 **xms_cc** クレーム要求の値は、既知の値である場合、アクセス トークンの **xms_cc** クレームの値として含まれます。 現在の既知の値は、**cp1** のみです。

値は、大文字と小文字が区別されず、順序指定もされません。 **xms_cc** クレーム要求で複数の値が指定されている場合、それらの値は、 **xms_cc** クレームの値として複数値のコレクションになります。

次の要求は、

```json
{ "access_token": { "xms_cc":{"values":["cp1","foo", "bar"] } }}
```

アクセス トークン内に次のクレームを生成します

```json
"xms_cc": ["cp1", "foo", "bar"]
```

(**cp1**、**foo**、**bar** が既知の機能の場合)。

[省略可能なクレーム](active-directory-optional-claims.md) **xms_cc** が要求された後のアプリのマニフェストの内容を次に示します。

```c#
"optionalClaims":
{
    "accessToken": [
    {
        "additionalProperties": [],
        "essential": false,
        "name": "xms_cc",
        "source": null
    }],
    "idToken": [],
    "saml2Token": []
}
```

この後、API では、クライアントがクレーム チャレンジを処理できるかどうかに基づいて、応答をカスタマイズできます。

C# の例

```c#
Claim ccClaim = context.User.FindAll(clientCapabilitiesClaim).FirstOrDefault(x => x.Type == "xms_cc");
if (ccClaim != null && ccClaim.Value == "cp1")
{
    // Return formatted claims challenge as this client understands this
}
else
{
    // Throw generic exception
    throw new UnauthorizedAccessException("The caller does not meet the authentication bar to carry our this operation. The service cannot allow this operation");
}
```

## <a name="next-steps"></a>次のステップ

- [Microsoft ID プラットフォームと OAuth 2.0 認証コード フロー](v2-oauth2-auth-code-flow.md#request-an-authorization-code)
- [継続的アクセス評価が有効になった API をアプリケーションで使用する方法](app-resilience-continuous-access-evaluation.md)
- [機密データとアクションに対するきめ細かな条件付きアクセス](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/granular-conditional-access-for-sensitive-data-and-actions/ba-p/1751775)
