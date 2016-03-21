
<properties
	pageTitle="Azure AD v2.0 OAuth 承認コード フロー | Microsoft Azure"
	description="Azure AD で導入された OAuth 2.0 認証プロトコルを利用し、Web アプリケーションを構築します。"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# v2.0 プロトコル: OAuth 2.0 承認コード フロー

デバイスにインストールされているアプリに、Web API など、保護されているリソースにアクセスする権利を与えるために OAuth 2.0 認証コード付与を利用できます。アプリ モデル v2.0 による OAuth 2.0 の実装を使用すると、サインインおよび API アクセスをモバイル アプリやデスクトップ アプリに追加できます。本ガイドでは、オープンソース ライブラリを利用せず、HTTP メッセージを送受信する方法について説明します。本ガイドは言語非依存です。

<!-- TODO: Need link to libraries -->	

> [AZURE.NOTE]
	Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。v2.0 エンドポイントを使用する必要があるかどうかを判断するには、[v2.0 の制限事項](active-directory-v2-limitations.md)に関するページをお読みください。

OAuth 2.0 承認コード フローは、[OAuth 2.0 仕様のセクション 4.1](http://tools.ietf.org/html/rfc6749) で規定されています。[Web アプリ](active-directory-v2-flows.md#web-apps)や[ネイティブにインストールされるアプリ](active-directory-v2-flows.md#mobile-and-native-apps)を含め、大半のアプリ タイプで認証と承認を行う際にこのフローが使用されます。アプリは、このフローによって安全に access\_tokens を取得し、v2.0 エンドポイントを使用して保護されているリソースにアクセスすることができます。

## プロトコルのダイアグラム
まとめると、ネイティブ/モバイル アプリケーションの全体的な認証フローは次のようになります。

![OAuth Auth Code Flow](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## 承認コードを要求する
承認コード フローは、クライアントがユーザーを `/authorize` エンドポイントにリダイレクトさせることから始まります。この要求で、クライアントは、ユーザーから取得する必要のあるアクセス許可を指定します。

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [AZURE.TIP] 以下の要求をブラウザーに貼り付けてみてください!

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345
```

| パラメーター | | 説明 |
| ----------------------- | ------------------------------- | --------------- |
| テナント | 必須 | 要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。使用できる値は、`common`、`organizations`、`consumers` およびテナント識別子です。詳細については、[プロトコルの基礎](active-directory-v2-protocols.md#endpoints)に関するページを参照してください。 |
| client\_id | 必須 | 登録ポータル ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) によってアプリに割り当てられたアプリケーション ID。 |
| response\_type | 必須 | 承認コード フローでは `code` を指定する必要があります。 |
| redirect\_uri | 推奨 | アプリ の redirect\_uri。アプリは、この URI で認証応答を送受信することができます。ポータルで登録したいずれかの redirect\_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。ネイティブ アプリとモバイル アプリでは、`urn:ietf:wg:oauth:2.0:oob` の既定値を使用します。 |
| scope | 必須 | ユーザーに同意を求める、スペースで区切られた[スコープ](active-directory-v2-scopes.md)の一覧。 |
| response\_mode | 推奨 | 結果として得られたトークンをアプリに返す際に使用するメソッドを指定します。`query` または `form_post` が可能です。 |
| state | 推奨 | 要求に含まれ、かつトークンの応答として返される値。任意の文字列を指定することができます。[クロスサイト リクエスト フォージェリ攻撃を防ぐ](http://tools.ietf.org/html/rfc6749#section-10.12)ために通常、ランダムに生成された一意の値が使用されます。この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的にも使用されます。 |
| prompt | 省略可能 | ユーザーとの必要な対話の種類を指定します。現時点で有効な値は 'login'、'none'、'consent' だけです。`prompt=login` は、その要求でユーザーに資格情報の入力を強制させ、シングル サインオンを無効にします。`prompt=none` は反対に、ユーザーに対して対話形式のプロンプトを表示しません。シングル サインオンによって自動的に要求を完了できない場合、v2.0 エンドポイントはエラーを返します。`prompt=consent` では、ユーザーがサインインした後で OAuth 同意ダイアログが表示され、アプリへのアクセス許可の付与をユーザーに求めます。 |
| login\_hint | 省略可能 | ユーザー名が事前にわかっている場合、ユーザーに代わって事前に、サインイン ページのユーザー名/電子メール アドレス フィールドに入力ができます。アプリはしばしば前回のサインインから `preferred_username` 要求を抽出して再認証時にこのパラメーターを使用します。 |
| domain\_hint | 省略可能 | `consumers` か `organizations` のいずれかを指定できます。これが含まれる場合、v2.0 のサインイン ページでユーザーが行う電子メール ベースの検出プロセスがスキップされ、ユーザー エクスペリエンスは若干簡素化されたものになります。アプリはしばしば前回のサインインから `tid` を抽出して再認証時にこのパラメーターを使用します。`tid` 要求の値が `9188040d-6c67-4c5b-b112-36a304b66dad` の場合、`domain_hint=consumers` を使用する必要があります。それ以外の場合は、`domain_hint=organizations` を指定します。 |

現時点では、ユーザーに資格情報の入力と認証が求められます。v2.0 エンドポイントは、`scope` クエリ パラメーターで指定されたアクセス許可にユーザーが同意済みであることの保証も行います。いずれのアクセス許可にもユーザーが同意しなかった場合、必要なアクセス許可に同意するようユーザーに求めます。アクセス許可、同意、マルチテナント アプリの詳細については、[こちら](active-directory-v2-scopes.md)を参照してください。

ユーザーが本人であることを証明し、同意の許可を与えると、v2.0 エンドポイントは、`response_mode` パラメーターに指定されたメソッドを使い、指定された `redirect_uri` でアプリに応答を返します。

#### 成功応答
`response_mode=query` を使用した場合の正常な応答は次のようになります。

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| code | アプリが要求した authorization\_code。アプリは承認コードを使用して、対象リソースのアクセス トークンを要求します。承認コードは有効期間が非常に短く、通常 10 分後には期限切れとなります。 |
| state | 要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

#### エラー応答
アプリ側でエラーを適切に処理できるよう、`redirect_uri` にはエラー応答も送信されます。

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| error | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error\_description | 認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

## アクセス トークンを要求する
ユーザーから authorization\_code を取得し、アクセス許可を得たら、`POST` 要求を `/token` エンドポイントに送信することで `access_token` の `code` を目的のリソースに対して使うことができます。

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] 次の curl コマンドを Postman にインポートしてみてください (成功させるには、`code` を独自のものに置き換える必要があります)。

```
curl -X POST -H "Cache-Control: no-cache" -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=6731de76-14a6-49ae-97bc-6eba6914391e&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXrFgnryzZvcDbKTvyz36ono600tLhxSdnoOe50zSgxiIQhD36sIPLln7lNOMrUi1ralV_hOfZItjuwqeTOTFgXRG_rhkIzBfKmudQHD1KUodPD84a308LAfJ5ciLak9nlNVyVOL7gViWADpdZv_KrBXgaJXkxKZ4qxeYT_wf6yajHP2Gt4LPijuhqJIsqId7Xo8FkNIsmlvZkdArZDLgpZdunDmnis_623fu4vMeuWyVhrAoesilIqbwP_bKWNhGO_fcQ1Spsa-TDgfqUyrXnk3UYc-B3m6Npvkx3bYv3NrUSNxqdMONxR-3HowU3Uke-jM3Z8GR25HE4YAdfTqVxHtd6DEP9aamMIRH0LwuM4uxUrgeALqpbPenabekOZkkZ5-KKY4AyJKMOWxvMmqJRz9gYHnGUxqKcl2-F7250rHNGZTbJPurie_3WzNrRKFOQAF84mbsGoeYvSXlbI5uiH3Bw9kpOw302r26K4j-IKoMpw2BXU0mNxoGEL_wC0oTkVqRNg_sTTcsAPU1giW0hj-LONWc0ZgcKNI00fXaC5l6V8i2ERWyBy4Ys8gKIc7mynZnCpf2tgrxMBH5sloZ1Lf6P63CiAA&client_secret=JqQX2PNo9bpM0uEihUPzyrh&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&grant_type=authorization_code' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```


| パラメーター | | 説明 |
| ----------------------- | ------------------------------- | --------------------- |
| テナント | 必須 | 要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。使用できる値は、`common`、`organizations`、`consumers` およびテナント識別子です。詳細については、[プロトコルの基礎](active-directory-v2-protocols.md#endpoints)に関するページを参照してください。 |
| client\_id | 必須 | 登録ポータル ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) によってアプリに割り当てられたアプリケーション ID。 |
| grant\_type | 必須 | 承認コード フローでは `authorization_code` を指定する必要があります。 |
| scope | 必須 | スコープのスペース区切りリスト。この段階で要求するスコープは、最初の段階で要求したスコープと同じか、またはそのサブセットである必要があります。この要求で指定したスコープが複数のリソース サーバーにまたがる場合、v2.0 エンドポイントからは、最初のスコープで指定したリソースのトークンが返されます。スコープの詳細については、[アクセス許可、同意、スコープ](active-directory-v2-scopes.md)に関するページを参照してください。 |
| code | 必須 | フローの最初の段階で取得した authorization\_code。 |
| redirect\_uri | 必須 | authorization\_code の取得に使用された同じ redirect\_uri 値。 |
| client\_secret | Web アプリの場合は必須 | アプリ登録ポータルで作成した、アプリケーションのシークレット。ネイティブ アプリでは使用しないでください。デバイスに client\_secret を確実に保存することができません。Web アプリや Web API では client\_secret をサーバー側で安全に保存する機能が備わっており、必ず指定する必要があります。 |

#### 成功応答
正常なトークン応答は次のようになります。

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": 3599,
	"scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| access\_token | 要求されたアクセス トークン。アプリはこのトークンを使用して、保護されたリソース (Web API など) に対し、本人性を証明することができます。 |
| token\_type | トークン タイプ値を指定します。Azure AD でサポートされるのは Bearer タイプのみです。 |
| expires\_in | アクセス トークンの有効期間 (秒)。 |
| scope | access\_token が有効である範囲。 |
| refresh\_token | OAuth 2.0 更新トークン。現在のアクセス トークンの有効期限が切れた後、アプリはこのトークンを使用して、追加のアクセス トークンを取得します。Refresh\_token は有効期間が長く、リソースへのアクセスを長時間保持するときに利用できます。詳細については、[v2.0 トークン リファレンス](active-directory-v2-tokens.md)を参照してください。 |
| id\_token | 無署名の JSON Web トークン (JWT)。このトークンのセグメントを base64Url でデコードすることによって、サインインしたユーザーに関する情報を要求することができます。この値をキャッシュして表示することはできますが、承認やセキュリティ境界の用途でこの値に依存することは避けてください。id\_token の詳細については、[v2.0 エンドポイント トークン リファレンス](active-directory-v2-tokens.md)を参照してください。 |

#### エラー応答
エラー応答は次のようになります。

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| error | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error\_description | 認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |
| error\_codes | 診断に役立つ STS 固有のエラー コードの一覧。 |
| timestamp | エラーが発生した時刻。 |
| trace\_id | 診断に役立つ、要求の一意の識別子。 |
| correlation\_id | コンポーネント間での診断に役立つ、要求の一意の識別子。 |

## アクセス トークンを使用する
`access_token` を無事取得したら、そのトークンを `Authorization` ヘッダーに追加することによって、Web API への要求に使用することができます。

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

> [AZURE.TIP] 次のコマンドを試してください! (ただし、トークンは独自のものに置き換えてください)。

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## アクセス トークンを更新する
アクセス トークンは有効期間が短く、期限が切れた後もリソースにアクセスし続けるためにはトークンを更新する必要があります。アクセス トークンを更新するには、もう一度 `POST` 要求を `/token` エンドポイントに送信します。このとき、`code` の代わりに `refresh_token` を指定します。

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh	  // NOTE: Only required for web apps
```

> [AZURE.TIP] 次の curl コマンドを Postman にインポートしてみてください! (成功するには、refresh\_token を独自のものに置き換える必要があります)。

```
curl -X POST -H "Cache-Control: no-cache" -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=6731de76-14a6-49ae-97bc-6eba6914391e&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXrFgnryzZvcDbKTvyz36ono600tLhxSdnoOe50zSgxiIQhD36sIPLln7lNOMrUi1ralV_hOfZItjuwqeTOTFgXRG_rhkIzBfKmudQHD1KUodPD84a308LAfJ5ciLak9nlNVyVOL7gViWADpdZv_KrBXgaJXkxKZ4qxeYT_wf6yajHP2Gt4LPijuhqJIsqId7Xo8FkNIsmlvZkdArZDLgpZdunDmnis_623fu4vMeuWyVhrAoesilIqbwP_bKWNhGO_fcQ1Spsa-TDgfqUyrXnk3UYc-B3m6Npvkx3bYv3NrUSNxqdMONxR-3HowU3Uke-jM3Z8GR25HE4YAdfTqVxHtd6DEP9aamMIRH0LwuM4uxUrgeALqpbPenabekOZkkZ5-KKY4AyJKMOWxvMmqJRz9gYHnGUxqKcl2-F7250rHNGZTbJPurie_3WzNrRKFOQAF84mbsGoeYvSXlbI5uiH3Bw9kpOw302r26K4j-IKoMpw2BXU0mNxoGEL_wC0oTkVqRNg_sTTcsAPU1giW0hj-LONWc0ZgcKNI00fXaC5l6V8i2ERWyBy4Ys8gKIc7mynZnCpf2tgrxMBH5sloZ1Lf6P63CiAA&client_secret=JqQX2PNo9bpM0uEihUPzyrh&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&grant_type=refresh_token' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| パラメーター | | 説明 |
| ----------------------- | ------------------------------- | -------- |
| テナント | 必須 | 要求パスの `{tenant}` の値を使用して、アプリケーションにサインインできるユーザーを制御します。使用できる値は、`common`、`organizations`、`consumers` およびテナント識別子です。詳細については、[プロトコルの基礎](active-directory-v2-protocols.md#endpoints)に関するページを参照してください。 |
| client\_id | 必須 | 登録ポータル ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) によってアプリに割り当てられたアプリケーション ID。 |
| grant\_type | 必須 | この段階の承認コード フローでは `refresh_token` を指定する必要があります。 |
| scope | 必須 | スコープのスペース区切りリスト。この段階で要求するスコープは、最初の承認コード要求段階で要求したスコープと同じか、またはそのサブセットである必要があります。この要求で指定したスコープが複数のリソース サーバーにまたがる場合、v2.0 エンドポイントからは、最初のスコープで指定したリソースのトークンが返されます。スコープの詳細については、[アクセス許可、同意、スコープ](active-directory-v2-scopes.md)に関するページを参照してください。 |
| refresh\_token | 必須 | フローの第 2 段階で取得した refresh\_token。 |
| redirect\_uri | 必須 | authorization\_code の取得に使用された同じ redirect\_uri 値。 |
| client\_secret | Web アプリの場合は必須 | アプリ登録ポータルで作成した、アプリケーションのシークレット。ネイティブ アプリでは使用しないでください。デバイスに client\_secret を確実に保存することができません。Web アプリや Web API では client\_secret をサーバー側で安全に保存する機能が備わっており、必ず指定する必要があります。 |

#### 成功応答
正常なトークン応答は次のようになります。

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": 3599,
	"scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| access\_token | 要求されたアクセス トークン。アプリはこのトークンを使用して、保護されたリソース (Web API など) に対し、本人性を証明することができます。 |
| token\_type | トークン タイプ値を指定します。Azure AD でサポートされるのは Bearer タイプのみです。 |
| expires\_in | アクセス トークンの有効期間 (秒)。 |
| scope | access\_token が有効である範囲。 |
| refresh\_token | 新しい OAuth 2.0 更新トークン。できるだけ長い時間、更新トークンを有効な状態に維持するためには、この新しく取得した更新トークンで古い更新トークンを置き換える必要があります。 |
| id\_token | 無署名の JSON Web トークン (JWT)。このトークンのセグメントを base64Url でデコードすることによって、サインインしたユーザーに関する情報を要求することができます。この値をキャッシュして表示することはできますが、承認やセキュリティ境界の用途でこの値に依存することは避けてください。id\_token の詳細については、[v2.0 エンドポイント トークン リファレンス](active-directory-v2-tokens.md)を参照してください。 |

#### エラー応答
```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| error | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error\_description | 認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |
| error\_codes | 診断に役立つ STS 固有のエラー コードの一覧。 |
| timestamp | エラーが発生した時刻。 |
| trace\_id | 診断に役立つ、要求の一意の識別子。 |
| correlation\_id | コンポーネント間での診断に役立つ、要求の一意の識別子。 |

<!---HONumber=AcomDC_0302_2016-->