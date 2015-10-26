<properties
	pageTitle="アプリ モデル v2.0 のプロトコル | Microsoft Azure"
	description="Azure AD v2.0 アプリ モデル パブリック プレビューでサポートされているプロトコル。"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# アプリ モデル v2.0 プレビュー: プロトコル - OAuth 2.0 と OpenID Connect

v2.0 アプリ モデルは、業界標準のプロトコルである OpenID Connect と OAuth 2.0 をサポートすることによって Identity-as-a-Service (サービスとしての ID) 機能をアプリに提供します。このサービスは標準に準拠していますが、これらのプロトコルには、実装によって微妙な違いが存在する場合があります。ここでは、いずれかのオープン ソース ライブラリを使うのではなく、コードから直接 HTTP 要求を送信して処理する場合に役立つ情報を紹介しています。<!-- TODO: Need link to libraries above -->

> [AZURE.NOTE]この情報は、v2.0 アプリ モデルのパブリック プレビューに関するものです。一般公開されている Azure AD サービスとの統合手順については、「[Azure Active Directory 開発者ガイド](active-directory-developers-guide.md)」を参照してください。

## Tokens
v2.0 アプリ モデルで実装している OAuth 2.0 と OpenID Connect では、ベアラー トークンが広範囲に使われています (JWT として表現されたベアラー トークンなど)。ベアラー トークンは、保護されたリソースへの "ベアラー" アクセスを許可する簡易セキュリティ トークンです。この意味で、"ベアラー" はトークンを提示できる任意の利用者を表します。利用者がベアラー トークンを受信するには、まず Azure AD による認証が必要となりますが、転送中や保存時にトークンを保護するために必要な対策を講じていない場合、意図しない利用者によって傍受され、使用されるおそれがあります。一部のセキュリティ トークンには、許可されていない利用者がトークンを使用できないようにするための組み込みメカニズムがありますが、ベアラー トークンにはこのメカニズムがないため、トランスポート層セキュリティ (HTTPS) などのセキュリティで保護されたチャネルで転送する必要があります。ベアラー トークンが暗号化されずに転送された場合、悪意のある利用者が中間者攻撃によってトークンを取得し、保護されたリソースへの未承認のアクセスに使用する可能性があります。後で使用するためにベアラー トークンを保存またはキャッシュするときにも、同じセキュリティ原則が適用されます。アプリケーションでは、常に安全な方法でベアラー トークンを転送および保存してください。ベアラー トークンのセキュリティに関する考慮事項の詳細については、[RFC 6750 セクション 5](http://tools.ietf.org/html/rfc6750) をご覧ください。

v2.0 アプリ モデルで使われている各種トークンの詳細については、[v2.0 アプリ モデルのトークン リファレンス](active-directory-v2-tokens.md)を参照してください。

## 基本操作
v2.0 アプリ モデルを使うアプリは、すべて [apps.dev.microsoft.com](https://apps.dev.microsoft.com) で登録する必要があります。アプリの登録プロセスでは、いくつかの値が収集され、対象のアプリに割り当てられます。

- アプリを一意に識別する**アプリケーション ID**。
- **リダイレクト URI** または**パッケージ識別子** (アプリに戻す応答をリダイレクトする際に使用)。
- その他シナリオに応じた値。詳細については、[アプリの登録](active-directory-v2-app-registration.md)方法を参照してください。

登録済みのアプリは、v2.0 エンドポイントに要求を送ることによって、Azure AD と通信を行います。

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

OAuth と OpenID Connect におけるフローはほぼすべて、情報のやり取りに 4 つの当事者が関係します。

![OAuth 2.0 Roles](../media/active-directory-v2-flows/protocols_roles.png)

- **承認サーバー**は v2.0 エンドポイントです。ユーザーの本人性確認、リソースへのアクセス権の付与と取り消し、トークンの発行という役割を担います。ID プロバイダーと呼ばれることもあります。ユーザーの情報とそのアクセス、そしてフロー内の当事者間の信頼関係に関するすべてのことは、承認サーバーによって安全に処理されます。
- **リソース所有者**とは通常、エンド ユーザーを指します。データを所有し、そのデータ (またはリソース) へのアクセスをサード パーティに許可する権限を持つ当事者です。
- **OAuth クライアント**は、皆さんが開発するアプリです。対応するアプリケーション ID で識別されます。通常はこの当事者がエンド ユーザーと情報をやり取りし、承認サーバーにトークンを要求します。クライアントには、リソース所有者がリソースへのアクセス権を付与する必要があります。
- **リソース サーバー**は、リソースまたはデータが存在する場所です。承認サーバーを信頼し、OAuth クライアントを安全に認証、承認します。リソースへのアクセスを許可するためにベアラー トークン (access\_tokens) が使用されます。

## OAuth2 承認コード フロー
OAuth 2.0 承認コード フローは、[OAuth 2.0 仕様のセクション 4.1](http://tools.ietf.org/html/rfc6749) で規定されています。[Web アプリ](active-directory-v2-flows.md#web-apps)や[ネイティブにインストールされるアプリ](active-directory-v2-flows.md#mobile-and-native-apps)を含め、大半のアプリ タイプで認証と承認を行う際にこのフローが使用されます。アプリは、このフローによって安全に access\_tokens を取得し、v2.0 アプリ モデルを使用して保護されているリソースにアクセスすることができます。

以下に示したのは、ネイティブ アプリに使用されるフローの全体図です。それぞれの要求については、以降のセクションで詳しく説明します。 ![OAuth Auth Code Flow](../media/active-directory-v2-flows/convergence_scenarios_native.png)

#### 承認コードを要求する
承認コード フローは、クライアントがユーザーを `/authorize` エンドポイントにリダイレクトさせることから始まります。この要求で、クライアントは、ユーザーから取得する必要のあるアクセス許可を指定します。

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=											   // See table below for scope parameter details.
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				 // Any value provided by your app
```

| パラメーター | | 説明 |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | 必須 | 登録ポータル ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) によってアプリに割り当てられたアプリケーション ID。 |
| response\_type | 必須 | 承認コード フローでは `code` を指定する必要があります。 |
| redirect\_uri | 必須 | アプリ の redirect\_uri。アプリは、この URI で認証応答を送受信することができます。ポータルで登録したいずれかの redirect\_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| scope | 必須 | スコープのスペース区切りリスト。v2.0 エンドポイントには、リソースとその (要求されているリソースへの) アクセス許可の両方が 1 つのスコープ値で指定されます。スコープは `<app identifier URI>/<scope value>` という形式で指定します。上の例では、Azure AD Graph API のアプリ識別子を使用し (`https://graph.windows.net`)、2 つのアクセス許可を要求しています (`directory.read` と `directory.write`)。スコープの詳細については、c を参照してください。 |
| response\_mode | 推奨 | 結果として得られた authorization\_code をアプリに返す際に使用するメソッドを指定します。'query'、'form\_post'、'fragment' のいずれかを指定できます。
| state | 推奨 | 要求に含まれ、かつトークンの応答として返される値。任意の文字列を指定することができます。クロスサイト リクエスト フォージェリ攻撃を防ぐために通常、ランダムに生成された一意の値が使用されます。この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的にも使用されます。 |
| prompt | 省略可能 | ユーザーとの必要な対話の種類を指定します。現時点で有効な値は 'login' のみです。この場合ユーザーは要求時に、その資格情報を入力する必要があります。シングル サインオンは作用しません。 |

現時点では、ユーザーに資格情報の入力と認証が求められます。v2.0 エンドポイントは、`scope` クエリ パラメーターで指定されたアクセス許可にユーザーが同意済みであることの保証も行います。いずれのアクセス許可にもユーザーが同意しなかった場合、必要なアクセス許可に同意するようユーザーに求めます。アクセス許可、同意、マルチテナント アプリの詳細については、[こちら](active-directory-v2-scopes.md)を参照してください。

ユーザーが本人であることを証明し、同意の許可を与えると、v2.0 エンドポイントは、`response_mode` パラメーターに指定されたメソッドを使い、指定された `redirect_uri` でアプリに応答を返します。

`response_mode=query` を使用した場合の正常な応答は次のようになります。

```
GET https://localhost/myapp/?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  							// the value provided in the request
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| code | アプリが要求した authorization\_code。アプリは承認コードを使用して、対象リソースのアクセス トークンを要求します。承認コードは有効期間が非常に短く、通常 10 分後には期限切れとなります。 |
| session\_state | 現在のユーザー セッションを識別する一意の値。この値は GUID ですが、検査なしで渡される不透明な値として扱う必要があります。 |
| state | 要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

アプリ側でエラーを適切に処理できるよう、`redirect_uri` にはエラー応答も送信されます。

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| error | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error\_description | 認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

#### アクセス トークンを要求する
ユーザーから authorization\_code を取得し、アクセス許可を得たら、`POST` 要求を `/token` エンドポイントに送信することで `access_token` の `code` を目的のリソースに対して使うことができます。

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

| パラメーター | | 説明 |
| ----------------------- | ------------------------------- | --------------------- |
| client\_id | 必須 | 登録ポータル ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) によってアプリに割り当てられたアプリケーション ID。 |
| grant\_type | 必須 | 承認コード フローでは `authorization_code` を指定する必要があります。 |
| scope | 必須 | スコープのスペース区切りリスト。この段階で要求するスコープは、最初の段階で要求したスコープと同じか、またはそのサブセットである必要があります。この要求で指定したスコープが複数のリソース サーバーにまたがる場合、v2.0 エンドポイントからは、最初のスコープで指定したリソースのトークンが返されます。スコープの詳細については、[アクセス許可、同意、スコープ](active-directory-v2-scopes.md)に関するページを参照してください。 |
| code | 必須 | フローの最初の段階で取得した authorization\_code。 |
| client\_secret | Web アプリの場合は必須 | アプリ登録ポータルで作成した、アプリケーションのシークレット。ネイティブ アプリでは使用しないでください。デバイスに client\_secret を確実に保存することができません。Web アプリや Web API では client\_secret をサーバー側で安全に保存する機能が備わっており、必ず指定する必要があります。 |

正常なトークン応答は次のようになります。

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"expires_on": "1388444763",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```
| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| access\_token | 要求されたアクセス トークン。アプリはこのトークンを使用して、保護されたリソース (Web API など) に対し、本人性を証明することができます。 |
| token\_type | トークン タイプ値を指定します。Azure AD でサポートされるのは Bearer タイプのみです。 |
| expires\_in | アクセス トークンの有効期間 (秒)。 |
| expires\_on | アクセス トークンの有効期限が切れる日時。日付は、エポック時からの秒数で表されます。 |
| scope | access\_token が有効である範囲。 |
| refresh\_token | OAuth 2.0 更新トークン。現在のアクセス トークンの有効期限が切れた後、アプリはこのトークンを使用して、追加のアクセス トークンを取得します。Refresh\_token は有効期間が長く、リソースへのアクセスを長時間保持するときに利用できます。詳細については、[v2.0 トークン リファレンス](active-directory-v2-tokens.md)を参照してください。 |
| id\_token | 無署名の JSON Web トークン (JWT)。このトークンのセグメントを base64Url でデコードすることによって、サインインしたユーザーに関する情報を要求することができます。この値をキャッシュして表示することはできますが、承認やセキュリティ境界の用途でこの値に依存することは避けてください。id\_token の詳細については、[v2.0 アプリ モデル トークン リファレンス](active-directory-v2-tokens.md)を参照してください。 |

エラー応答は次のようになります。

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| error | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error\_description | 認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

#### アクセス トークンを使用する
`access_token` を無事取得したら、そのトークンを `Authorization` ヘッダーに追加することによって、Web API への要求に使用することができます。

```
GET /contoso.onmicrosoft.com/users
Host: https://graph.windows.net
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

#### アクセス トークンを更新する
アクセス トークンは有効期間が短く、期限が切れた後もリソースにアクセスし続けるためにはトークンを更新する必要があります。アクセス トークンを更新するには、もう一度 `POST` 要求を `/authorize` エンドポイントに送信します。このとき、`code` の代わりに `refresh_token` を指定します。

```
POST common/v2.0/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "refresh_token",
	"client_id": "2d4d11a2-f814-46a7-890a-274a72a7309e",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

| パラメーター | | 説明 |
| ----------------------- | ------------------------------- | -------- |
| client\_id | 必須 | 登録ポータル ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) によってアプリに割り当てられたアプリケーション ID。 |
| grant\_type | 必須 | この段階の承認コード フローでは `refresh_token` を指定する必要があります。 |
| scope | 必須 | スコープのスペース区切りリスト。この段階で要求するスコープは、最初の承認コード要求段階で要求したスコープと同じか、またはそのサブセットである必要があります。この要求で指定したスコープが複数のリソース サーバーにまたがる場合、v2.0 エンドポイントからは、最初のスコープで指定したリソースのトークンが返されます。スコープの詳細については、[アクセス許可、同意、スコープ](active-directory-v2-scopes.md)に関するページを参照してください。 |
| refresh\_token | 必須 | フローの第 2 段階で取得した refresh\_token。 |
| client\_secret | Web アプリの場合は必須 | アプリ登録ポータルで作成した、アプリケーションのシークレット。ネイティブ アプリでは使用しないでください。デバイスに client\_secret を確実に保存することができません。Web アプリや Web API では client\_secret をサーバー側で安全に保存する機能が備わっており、必ず指定する必要があります。 |

正常なトークン応答は次のようになります。

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"expires_on": "1388444763",
	"scope": "https://graph.windows.net/directory.read https://graph.windows.net/directory.write",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```
| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| access\_token | 要求されたアクセス トークン。アプリはこのトークンを使用して、保護されたリソース (Web API など) に対し、本人性を証明することができます。 |
| token\_type | トークン タイプ値を指定します。Azure AD でサポートされるのは Bearer タイプのみです。 |
| expires\_in | アクセス トークンの有効期間 (秒)。 |
| expires\_on | アクセス トークンの有効期限が切れる日時。日付は、エポック時からの秒数で表されます。 |
| scope | access\_token が有効である範囲。 |
| refresh\_token | 新しい OAuth 2.0 更新トークン。できるだけ長い時間、更新トークンを有効な状態に維持するためには、この新しく取得した更新トークンで古い更新トークンを置き換える必要があります。 |
| id\_token | 無署名の JSON Web トークン (JWT)。このトークンのセグメントを base64Url でデコードすることによって、サインインしたユーザーに関する情報を要求することができます。この値をキャッシュして表示することはできますが、承認やセキュリティ境界の用途でこの値に依存することは避けてください。id\_token の詳細については、[v2.0 アプリ モデル トークン リファレンス](active-directory-v2-tokens.md)を参照してください。 |

エラー応答は次のようになります。

```
{
	"error": "access_denied",
	"error_description": "The user revoked access to the app.",
}
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| error | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error\_description | 認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |






## OpenID Connect サインイン フロー
[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) は、OAuth 2.0 の*承認*プロトコルを*認証*プロトコルとして使用できるように拡張したものです。OpenID Connect を使うことで OAuth を使用したシングル サインオンを行うことができます。OpenID Connect には、`id_token` の概念が導入されています。クライアントは、このセキュリティ トークンによってユーザーの本人性を確認し、そのユーザーに関する基本的なプロファイル情報を取得することができます。

v2.0 アプリ モデルでは、[Web アプリ](active-directory-v2-flows.md#web-apps)のサインインを実装する方法として、OpenID Connect が推奨されます。ごく基本的なサインイン フローは、以下のステップから成ります。

![OpenId Connect Swimlanes](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

#### サインイン要求を送信する
Web アプリでユーザーを認証する必要があるときは、ユーザーを `/authorize` エンドポイントにリダイレクトさせます。この要求は、[OAuth 2.0 承認コード フロー](#oauth2-authorization-code-flow)の最初の段階と似ていますが、いくつかの重要な違いがあります。- 要求の `scope` パラメーターにはスコープ `openid` を指定する必要があります。- `response_type` パラメーターには `id_token` を指定する必要があります。- この要求には `nonce` パラメーターを指定する必要があります。

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=openid										 // Translates to the 'Read your profile' permission
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

| パラメーター | | 説明 |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | 必須 | 登録ポータル ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) によってアプリに割り当てられたアプリケーション ID。 |
| response\_type | 必須 | OpenID Connect サインインでは、`id_token` を指定する必要があります。また、他の response\_type を指定することもできます (「[OpenID Connect と OAuth Code を組み合わせたフロー](#OpenID-Connect-with-OAuth-Code-Flow)」セクションを参照)。 |
| redirect\_uri | 必須 | アプリ の redirect\_uri。アプリは、この URI で認証応答を送受信することができます。ポータルで登録したいずれかの redirect\_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| scope | 必須 | スコープのスペース区切りリスト。OpenID Connect では、スコープ `openid` を指定する必要があります。このスコープは、承認 UI で "サインインとプロファイルの読み取り" アクセス許可に変換されます。また、この要求で他のスコープを指定して同意を要求することもできます (「[OpenID Connect と OAuth Code を組み合わせたフロー](#OpenID-Connect-with-OAuth-Code-Flow)」セクションを参照)。 |
| nonce | 必須 | 要求に追加する (アプリによって生成された) 値。この値が、最終的な id\_token に要求として追加されます。アプリでこの値を確認することにより、トークン再生攻撃を緩和することができます。通常この値はランダム化された一意の文字列になっており、要求の送信元を特定する際に使用できます。 |
| response\_mode | 推奨 | 結果として得られた authorization\_code をアプリに返す際に使用するメソッドを指定します。'query'、'form\_post'、'fragment' のいずれかを指定できます。  
| state | 推奨 | 要求に含まれ、かつトークンの応答として返される値。任意の文字列を指定することができます。クロスサイト リクエスト フォージェリ攻撃を防ぐために通常、ランダムに生成された一意の値が使用されます。この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的にも使用されます。 |
| prompt | 省略可能 | ユーザーとの必要な対話の種類を指定します。現時点で有効な値は 'login' のみです。この場合ユーザーは要求時に、その資格情報を入力する必要があります。シングル サインオンは作用しません。 |

現時点では、ユーザーに資格情報の入力と認証が求められます。v2.0 エンドポイントは、`scope` クエリ パラメーターで指定されたアクセス許可にユーザーが同意済みであることの保証も行います。いずれのアクセス許可にもユーザーが同意しなかった場合、必要なアクセス許可に同意するようユーザーに求めます。アクセス許可、同意、マルチテナント アプリの詳細については、[こちら](active-directory-v2-scopes.md)を参照してください。

ユーザーが本人であることを証明し、同意の許可を与えると、v2.0 エンドポイントは、`response_mode` パラメーターに指定されたメソッドを使い、指定された `redirect_uri` でアプリに応答を返します。

`response_mode=query` を使用した場合の正常な応答は次のようになります。

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request

```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| id\_token | アプリが要求した id\_token。この id\_token を使用してユーザーの本人性を確認し、そのユーザーとのセッションを開始することができます。id\_token とその内容の詳細については、[v2.0 エンドポイント トークン リファレンス](active-directory-v2-tokens.md)を参照してください。 |
| session\_state | 現在のユーザー セッションを識別する一意の値。この値は GUID ですが、検査なしで渡される不透明な値として扱う必要があります。 |
| state | 要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

アプリ側でエラーを適切に処理できるよう、`redirect_uri` にはエラー応答も送信されます。

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| error | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error\_description | 認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

#### id\_token を検証する
単に id\_token を受け取るだけでは、ユーザーを認証するには不十分です。id\_token の署名を検証し、そのトークンに含まれる要求をアプリの要件に従って確認する必要があります。v2.0 エンドポイントは、[JSON Web トークン (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) と公開キー暗号を使用してトークンに署名し、それらが有効であることを証明します。

v2.0 アプリ モデルには、OpenID Connect メタデータ エンドポイントがあって、アプリは、このエンドポイントを使用することで、v2.0 アプリ モデルに関する情報を実行時にフェッチすることができます。この情報には、エンドポイント、トークンの内容、トークンの署名キーが含まれます。メタデータ エンドポイントは、以下の場所に JSON ドキュメントを格納しています。

`https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`

この構成ドキュメントのプロパティの 1 つに `jwks_uri` があります。v2.0 アプリ モデルでは、この値が次のようになります。

`https://login.microsoftonline.com/common/discovery/v2.0/keys`

id\_token の署名は、このエンドポイントに置かれている RSA256 公開キーを使用して検証できます。このエンドポイントには常時、複数のキーがリストされており、それぞれのキーは `kid` で識別されます。id\_token のヘッダーにはさらに `kid` 要求が格納されていて、複数のキーのうち、どのキーが id\_token の署名に使用されたかが、この要求によって示されます。

[トークンの検証](active-directory-v2-tokens.md#validating-tokens)と[署名キーのロールオーバーに関する重要な情報](active-directory-v2-tokens.md#validating-tokens)を含む詳細については、[v2.0 アプリ モデル トークン リファレンス](active-directory-v2-tokens.md)を参照してください。 <!--TODO: Improve the information on this-->

id\_token の署名を検証した後に、確認の必要な要求がいくつか存在します。

- トークン再生攻撃を防止するために、`nonce` 要求を検証する必要があります。その値が、サインイン要求で指定した値と一致していることが必要です。
- id\_token が確実に自分のアプリに対して発行されたものであることを確認するために `aud` 要求を検証する必要があります。その値が、アプリの `client_id` と一致していることが必要です。
- id\_token の有効期限が切れていないことを確認するために、`iat` 要求と `exp` 要求を検証する必要があります。

シナリオに応じてその他の要求も検証することができます。以下に一般的な検証の例をいくつか挙げます。

- ユーザー/組織がアプリにサインアップ済みであることを確認する。
- 適切な承認/特権がユーザーにあることを確認する。
- 多要素認証など特定の強度の認証が行われたことを確認する。

id\_token に含まれる要求の詳細については、[v2.0 アプリ モデル トークン リファレンス](active-directory-v2-tokens.md)を参照してください。

id\_token を十分検証したら、ユーザーとのセッションを開始し、id\_token に含まれる要求を使ってそのユーザーに関する情報をアプリの中で取得することができます。取得した情報は、表示、記録、承認などに利用することができます。

#### サインアウト要求を送信する

OpenIdConnect `end_session_endpoint` は現在、v2.0 アプリ モデル プレビューではサポートされていません。したがって、v2.0 エンドポイントに対しアプリから、ユーザーのセッションを終了し、v2.0 エンドポイントによって設定された Cookie をクリアする要求を送信することはできません。ユーザーをサインアウトさせるには、単にアプリ側でユーザーとのセッションを終了し、ユーザー側の v2.0 エンドポイントとのセッションは放置してください。次回ユーザーがサインインしようとすると "アカウントの選択" ページが表示され、実際にサインインしているアカウントが一覧表示されます。そのページでユーザーは任意のアカウントを選んでサインアウトし、v2.0 エンドポイントとのセッションを終了することができます。

<!--

When you wish to sign the user out of the  app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->

## OpenID Connect と OAuth Code を組み合わせたフロー
多くの Web アプリは、ユーザーをサインインさせたうえで、OAuth を使い、そのユーザーに代わって Web サービスにアクセスする必要があります。このセクションでは、前の 2 つのセクションを組み合わせたシナリオを紹介します。OpenID Connect を使ってユーザー認証を行うと同時に、OAuth 承認コード フローを使って、access\_token を取得するための authorization\_code を取得します。

![OpenId Connect Swimlanes](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

このフローは、サインイン要求の送信方法が、前のセクションとはわずかに異なります。

#### サインイン要求を送信する
Web アプリでユーザーを認証し、リソースへのアクセス許可を取得する必要があるときは、ユーザーを `/authorize` エンドポイントにリダイレクトさせます。この場合、アプリは、`id_token` と `code` の両方を応答として返すように要求する必要があります。

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e		// Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=query							      // 'query', 'form_post', or 'fragment'
&scope=openid%20										 // Include both 'openid' and scopes your app needs  
https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20
https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

| パラメーター | | 説明 |
| ----------------------- | ------------------------------- | ----------------- |
| client\_id | 必須 | 登録ポータル ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) によってアプリに割り当てられたアプリケーション ID。 |
| response\_type | 必須 | このシナリオでは、`id_token` と `code` を指定する必要があります。 |
| redirect\_uri | 必須 | アプリ の redirect\_uri。アプリは、この URI で認証応答を送受信することができます。ポータルで登録したいずれかの redirect\_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| scope | 必須 | スコープのスペース区切りリスト。OpenID Connect では、スコープ `openid` を指定する必要があります。このスコープは、承認 UI で "サインインとプロファイルの読み取り" アクセス許可に変換されます。また、アプリがアクセスする必要のあるリソースのスコープも指定する必要があります。スコープの詳細については、[アクセス許可、同意、スコープ](active-directory-v2-scopes.md)に関するページを参照してください。 |
| nonce | 必須 | 要求に追加する (アプリによって生成された) 値。この値が、最終的な id\_token に要求として追加されます。アプリでこの値を確認することにより、トークン再生攻撃を緩和することができます。通常この値はランダム化された一意の文字列になっており、要求の送信元を特定する際に使用できます。 |
| response\_mode | 推奨 | 結果として得られた authorization\_code をアプリに返す際に使用するメソッドを指定します。'query'、'form\_post'、'fragment' のいずれかを指定できます。  
| state | 推奨 | 要求に含まれ、かつトークンの応答として返される値。任意の文字列を指定することができます。クロスサイト リクエスト フォージェリ攻撃を防ぐために通常、ランダムに生成された一意の値が使用されます。この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的にも使用されます。 |
| prompt | 省略可能 | ユーザーとの必要な対話の種類を指定します。現時点で有効な値は 'login' のみです。この場合ユーザーは要求時に、その資格情報を入力する必要があります。シングル サインオンは作用しません。 |

現時点では、ユーザーに資格情報の入力と認証が求められます。v2.0 エンドポイントは、`scope` クエリ パラメーターで指定されたアクセス許可にユーザーが同意済みであることの保証も行います。いずれのアクセス許可にもユーザーが同意しなかった場合、必要なアクセス許可に同意するようユーザーに求めます。アクセス許可、同意、マルチテナント アプリの詳細については、[こちら](active-directory-v2-scopes.md)を参照してください。

ユーザーが本人であることを証明し、同意の許可を与えると、v2.0 エンドポイントは、`response_mode` パラメーターに指定されたメソッドを使い、指定された `redirect_uri` でアプリに応答を返します。

`response_mode=query` を使用した場合の正常な応答は次のようになります。

```
GET https://localhost/myapp/?
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q... 	// the id_token, truncated
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq... 	// the authorization_code, truncated
&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF			   // a value generated by the v2.0 endpoint
&state=12345												  	// the value provided in the request

```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| id\_token | アプリが要求した id\_token。この id\_token を使用してユーザーの本人性を確認し、そのユーザーとのセッションを開始することができます。id\_token とその内容の詳細については、[v2.0 アプリ モデル トークン リファレンス](active-directory-v2-tokens.md)を参照してください。 |
| code | アプリが要求した authorization\_code。アプリは承認コードを使用して、対象リソースのアクセス トークンを要求します。承認コードは有効期間が非常に短く、通常 10 分後には期限切れとなります。 |
| session\_state | 現在のユーザー セッションを識別する一意の値。この値は GUID ですが、検査なしで渡される不透明な値として扱う必要があります。 |
| state | 要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

アプリ側でエラーを適切に処理できるよう、`redirect_uri` にはエラー応答も送信されます。

```
GET https://localhost/myapp/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| error | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error\_description | 認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

#### id\_token を検証する
このプロセスは、「[OpenID Connect サインイン フロー](#OpenID-Connect-Sign-In-Flow)」とまったく同じです。

#### サインアウト要求を送信する
このプロセスは、「[OpenID Connect サインイン フロー](#OpenID-Connect-Sign-In-Flow)」とまったく同じです。

#### アクセス トークンを要求する
このプロセスは、「[OAuth 2.0 承認コード フロー](#oauth2-authorization-code-flow)」とまったく同じです。

#### アクセス トークンを使用する
このプロセスは、「[OAuth 2.0 承認コード フロー](#oauth2-authorization-code-flow)」とまったく同じです。

#### アクセス トークンを更新する
このプロセスは、「[OAuth 2.0 承認コード フロー](#oauth2-authorization-code-flow)」とまったく同じです。





## OAuth2 クライアント資格情報付与フロー
OAuth 2.0 クライアント資格情報付与は、[OAuth 2.0 仕様](http://tools.ietf.org/html/rfc6749#section-4.4)で規定されています。長時間実行されるプロセスなど、ユーザーから委任された ID ではなくアプリがそれ自身の "アプリケーション ID" を使ってリソースに身元を証明することができるサーバー間のシナリオで利用できます。

このフローは現在、v2.0 アプリ モデル プレビューではサポートされていません。一般提供版の Azure AD サービスにおける動作については、[こちらの Azure AD コード サンプル](https://github/com/AzureADSamples/Daemon-DotNet)を参照してください。

## OAuth2 暗黙的フロー
OAuth 2.0 暗黙的フローは、[OAuth 2.0 仕様](http://tools.ietf.org/html/rfc6749#section-4.2)で規定されています。ブラウザーで動作するシングル ページ/JavaScript アプリで多く使用されます。

このフローは現在、v2.0 アプリ モデル プレビューではサポートされていません。一般提供版の Azure AD サービスにおける動作については、[こちらの Azure AD コード サンプル](active-directory-devquickstarts-angular.md)を参照してください。

## OAuth2 リソース所有者パスワード資格情報付与
OAuth 2.0 リソース所有者パスワード資格情報付与は、[OAuth 2.0 仕様](http://tools.ietf.org/html/rfc6749#section-4.3)で規定されています。アプリでトークン要求にユーザーの名前とパスワードを与えることによって access\_token を取得することができます。

このフローは現在、v2.0 アプリ モデル プレビューではサポートされていません。一般提供版の Azure AD サービスにおける動作については、[こちらの Azure AD コード サンプル](https://github.com/AzureADSamples/NativeClient-Headless-DotNet)を参照してください。

## OAuth2 On Behalf Of フロー
On Behalf Of フロー (JWT ベアラー資格情報付与) は、[OAuth 2.0 Extension Grant ドラフト](https://tools.ietf.org/html/draft-ietf-oauth-jwt-bearer-12)で規定されています。このフローによって、access\_token を受け取る Web API は、その access\_token を資格情報として他のリソースの access\_token を取得することができます。これによって Web API が、下流にある別の Web API を安全に呼び出すことができます。

このフローは現在、v2.0 アプリ モデル プレビューではサポートされていません。一般提供版の Azure AD サービスにおける動作については、[こちらの Azure AD コード サンプル](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet)を参照してください。

<!---HONumber=Oct15_HO3-->