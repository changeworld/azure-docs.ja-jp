
<properties
	pageTitle="Azure AD v2.0 の OpenID Connect プロトコル | Microsoft Azure"
	description="Azure AD の v2.0 で導入された OpenID Connect 認証プロトコルを利用し、Web アプリケーションを構築します。"
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
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="dastrock"/>

# v2.0 プロトコル: OpenID Connect
OpenID Connect は OAuth 2.0 を基盤として開発された認証プロトコルであり、ユーザーを Web アプリケーションに安全にサインインさせるために利用されます。v2.0 エンドポイントによる OpenID Connect の実装を使用すると、サインインおよび API アクセスを Web ベースのアプリケーションに追加できます。本ガイドでは、この外注を言語に依存せずに行う方法について説明します。オープンソース ライブラリを利用せず、HTTP メッセージを送受信する方法について説明します。

> [AZURE.NOTE]
	Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。v2.0 エンドポイントを使用する必要があるかどうかを判断するには、[v2.0 の制限事項](active-directory-v2-limitations.md)に関するページをお読みください。
    
[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) は、OAuth 2.0 の*承認*プロトコルを*認証*プロトコルとして使用できるように拡張したものです。OpenID Connect を使うことで OAuth を使用したシングル サインオンを行うことができます。OpenID Connect には、`id_token` の概念が導入されています。クライアントは、このセキュリティ トークンによってユーザーの本人性を確認し、そのユーザーに関する基本的なプロファイル情報を取得することができます。OAuth 2.0 を拡張強化したものであり、アプリはまた、[認証サーバー](active-directory-v2-protocols.md#the-basics)で保護されるリソースにアクセスするための **access\_tokens** を安全に取得できます。OpenID Connect は、サーバーでホストされ、ブラウザーでアクセスされる [Web アプリケーション](active-directory-v2-flows.md#web-apps)を構築している場合に推奨されます。

## プロトコルのダイアグラム - サインイン
最も基本的なサインイン フローには次の手順が含まれています。各手順についてはこの後詳しく説明します。

![OpenId Connect Swimlanes](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

## サインイン要求を送信する
Web アプリでユーザーを認証する必要があるときは、ユーザーを `/authorize` エンドポイントにリダイレクトさせます。この要求は、[OAuth 2.0 承認コード フロー](active-directory-v2-protocols-oauth-code.md)の最初の部分に似ていますが、いくつかの重要な違いがあります。

- 要求の `scope` パラメーターにはスコープ `openid` が含まれる必要があります。
- `response_type` パラメーターには、`id_token` が設定されている必要があります。
- 要求には `nonce` パラメーターが含まれる必要があります。

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=678910
```

> [AZURE.TIP] この要求を実行するには、以下のリンクをクリックしてください。 サインイン後、ブラウザーは `https://localhost/myapp/` にリダイレクトされ、アドレス バーに `id_token` が含まれた状態になります。この要求では `response_mode=query` を使用していることに注意してください (チュートリアルでのみ使用)。`response_mode=form_post` を使用することをお勧めします。<a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&scope=openid&response_mode=query&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

| パラメーター | | 説明 |
| ----------------------- | ------------------------------- | --------------- |
| テナント | 必須 | 要求パスの `{tenant}` 値を使用して、アプリケーションにサインインできるユーザーを制御できます。使用できる値は、`common`、`organizations`、`consumers`、およびテナント識別子です。詳細については、「[v2.0 プロトコル - OAuth 2.0 と OpenID Connect](active-directory-v2-protocols.md#endpoints)」をご覧ください。 |
| client\_id | 必須 | 登録ポータル ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) によってアプリに割り当てられたアプリケーション ID。 |
| response\_type | 必須 | OpenID Connect サインインでは、`id_token` を指定する必要があります。`code` などの他の response\_types が含まれていてもかまいません。 |
| redirect\_uri | 推奨 | アプリ の redirect\_uri。アプリは、この URI で認証応答を送受信することができます。ポータルで登録したいずれかの redirect\_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| scope | 必須 | スコープのスペース区切りリスト。OpenID Connect では、スコープとして `openid` を指定する必要があります。このスコープは、承認 UI で "サインイン" アクセス許可に変換されます。同意を求めるこの要求には他のスコープが含まれていてもかまいません。 |
| nonce | 必須 | 要求に追加する (アプリによって生成された) 値。この値が、最終的な id\_token に要求として追加されます。アプリでこの値を確認することにより、トークン再生攻撃を緩和することができます。通常この値はランダム化された一意の文字列になっており、要求の送信元を特定する際に使用できます。 |
| response\_mode | 推奨 | 結果として得られた authorization\_code をアプリに返す際に使用するメソッドを指定します。'query'、'form\_post'、'fragment' のいずれかを指定できます。Web アプリケーションでは、トークンをアプリケーションに最も安全に転送できるように、`response_mode=form_post` を使用することをお勧めします。  
| state | 推奨 | 要求に含まれ、かつトークンの応答として返される値。任意の文字列を指定することができます。[クロスサイト リクエスト フォージェリ攻撃を防ぐ](http://tools.ietf.org/html/rfc6749#section-10.12)ために、通常はランダムに生成された一意の値が使用されます。この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的にも使用されます。 |
| prompt | 省略可能 | ユーザーとの必要な対話の種類を指定します。現時点で有効な値は 'login'、'none'、'consent' だけです。`prompt=login` は、その要求でユーザーに資格情報の入力を強制させ、シングル サインオンを無効にします。`prompt=none` は反対に、ユーザーに対して対話形式のプロンプトを表示しません。シングル サインオンによって自動的に要求を完了できない場合、v2.0 エンドポイントはエラーを返します。`prompt=consent` では、ユーザーがサインインした後で OAuth 同意ダイアログが表示され、アプリへのアクセス許可の付与をユーザーに求めます。 |
| login\_hint | 省略可能 | ユーザー名が事前にわかっている場合、ユーザーに代わって事前に、サインイン ページのユーザー名/電子メール アドレス フィールドに入力ができます。アプリはしばしば前回のサインインから `preferred_username` 要求を抽出して再認証時にこのパラメーターを使用します。 |
| domain\_hint | 省略可能 | `consumers` か `organizations` のいずれかを指定できます。これが含まれる場合、v2.0 のサインイン ページでユーザーが行う電子メール ベースの検出プロセスがスキップされ、ユーザー エクスペリエンスは若干簡素化されたものになります。アプリはしばしば id\_token から `tid` 要求を抽出して再認証時にこのパラメーターを使用します。`tid` 要求の値が `9188040d-6c67-4c5b-b112-36a304b66dad` の場合、`domain_hint=consumers` を使用する必要があります。それ以外の場合は、`domain_hint=organizations` を指定します。 |
現時点では、ユーザーに資格情報の入力と認証が求められます。v2.0 エンドポイントは、`scope` クエリ パラメーターで指定されたアクセス許可にユーザーが同意済みであることの保証も行います。いずれのアクセス許可にもユーザーが同意しなかった場合、必要なアクセス許可に同意するようユーザーに求めます。アクセス許可、同意、マルチテナント アプリの詳細については、[こちら](active-directory-v2-scopes.md)を参照してください。

ユーザーが本人であることを証明し、同意の許可を与えると、v2.0 エンドポイントは、`response_mode` パラメーターに指定されたメソッドを使い、指定された `redirect_uri` でアプリに応答を返します。

#### 成功応答
`response_mode=form_post` を使用した場合の正常な応答は次のようになります。

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| id\_token | アプリが要求した id\_token。この id\_token を使用してユーザーの本人性を確認し、そのユーザーとのセッションを開始することができます。id\_token とその内容の詳細については、[v2.0 エンドポイント トークン リファレンス](active-directory-v2-tokens.md)を参照してください。 |
| state | 要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

#### エラー応答
アプリ側でエラーを適切に処理できるよう、`redirect_uri` にはエラー応答も送信されます。

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| error | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error\_description | 認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

## id\_token を検証する
単に id\_token を受け取るだけでは、ユーザーを認証するには不十分です。id\_token の署名を検証し、そのトークンに含まれる要求をアプリの要件に従って確認する必要があります。v2.0 エンドポイントは、[JSON Web トークン (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) と公開キー暗号を使用してトークンに署名し、それらが有効であることを証明します。

クライアント コードで `id_token` を検証することもできますが、`id_token` をバックエンド サーバーに送信して検証を実行するのが一般的な方法です。id\_token の署名を検証した後に、確認の必要な要求がいくつか存在します。[トークンの検証](active-directory-v2-tokens.md#validating-tokens)や[署名キーのロールオーバーに関する重要な情報](active-directory-v2-tokens.md#validating-tokens)など、詳細については、「[v2.0 トークンのリファレンス](active-directory-v2-tokens.md)」をご覧ください。トークンの解析および検証には、ほとんどの言語とプラットフォームに少なくとも 1 つは用意されているライブラリを活用することをお勧めします。
<!--TODO: Improve the information on this-->

シナリオに応じてその他の要求も検証することができます。以下に一般的な検証の例をいくつか挙げます。

- ユーザー/組織がアプリにサインアップ済みであることを確認する。
- 適切な承認/特権がユーザーにあることを確認する。
- 多要素認証など特定の強度の認証が行われたことを確認する。

id\_token に含まれる要求の詳細については、「[v2.0 トークンのリファレンス](active-directory-v2-tokens.md)」をご覧ください。

id\_token を十分検証したら、ユーザーとのセッションを開始し、id\_token に含まれる要求を使ってそのユーザーに関する情報をアプリの中で取得することができます。取得した情報は、表示、記録、承認などに利用することができます。

## サインアウト要求を送信する

OpenIdConnect の `end_session_endpoint` は、v2.0 エンドポイントでは現在サポートされていません。したがって、v2.0 エンドポイントに対しアプリから、ユーザーのセッションを終了し、v2.0 エンドポイントによって設定された Cookie をクリアする要求を送信することはできません。ユーザーをサインアウトさせるには、単にアプリ側でユーザーとのセッションを終了し、ユーザー側の v2.0 エンドポイントとのセッションは放置してください。次回ユーザーがサインインしようとすると "アカウントの選択" ページが表示され、実際にサインインしているアカウントが一覧表示されます。そのページでユーザーは任意のアカウントを選んでサインアウトし、v2.0 エンドポイントとのセッションを終了することができます。

<!--

When you wish to sign the user out of the app, it is not sufficient to clear your app's cookies or otherwise end the session with the user.  You must also redirect the user to the v2.0 endpoint for sign out.  If you fail to do so, the user will be able to re-authenticate to your app without entering their credentials again, because they will have a valid single sign-on session with the v2.0 endpoint.

You can simply redirect the user to the `end_session_endpoint` listed in the OpenID Connect metadata document:

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
```

| Parameter | | Description |
| ----------------------- | ------------------------------- | ------------ |
| post_logout_redirect_uri | recommended | The URL which the user should be redirected to after successful logout.  If not included, the user will be shown a generic message by the v2.0 endpoint.  |

-->

## プロトコルのダイアグラム - トークンの取得
多くの Web アプリは、ユーザーをサインインさせるだけでなく、OAuth を使用してそのユーザーの代わりに Web サービスにアクセスする必要もあります。このセクションでは、OpenID Connect を使ってユーザー認証を行うと同時に、OAuth 承認コード フローを使って、access\_token を取得するための authorization\_code を取得します。

OpenID Connect によるサインインとトークン取得の完全なフローは次のようになります。各手順についてはこの後詳しく説明します。

![OpenId Connect Swimlanes](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

## アクセス トークンを取得する
アクセス トークンを取得するには、前に示したサインイン要求を少し変更する必要があります。

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e		// Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=form_post						      // 'query', 'form_post', or 'fragment'
&scope=openid%20                                      // Include both 'openid' and scopes your app needs  
offline_access%20										 
https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

> [AZURE.TIP] この要求を実行するには、以下のリンクをクリックしてください。 サインイン後、ブラウザーは `https://localhost/myapp/` にリダイレクトされ、アドレス バーに `id_token` と `code` が含まれた状態になります。この要求では `response_mode=query` を使用していることに注意してください (チュートリアルでのみ使用)。`response_mode=form_post` を使用することをお勧めします。<a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=id_token+code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345&nonce=678910" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>

アクセス許可スコープを要求に組み込み、`response_type=code+id_token` を使うことによって、v2.0 エンドポイントはユーザーが `scope` クエリ パラメーターで示されているアクセス許可に同意したことを確認し、アクセス トークンと引き替えに承認コードをアプリに返します。

#### 成功応答
`response_mode=form_post` を使用した場合の正常な応答は次のようになります。

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| id\_token | アプリが要求した id\_token。この id\_token を使用してユーザーの本人性を確認し、そのユーザーとのセッションを開始することができます。id\_token とその内容の詳細については、[v2.0 エンドポイント トークン リファレンス](active-directory-v2-tokens.md)を参照してください。 |
| code | アプリが要求した authorization\_code。アプリは承認コードを使用して、対象リソースのアクセス トークンを要求します。承認コードは有効期間が非常に短く、通常 10 分後には期限切れとなります。 |
| state | 要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

#### エラー応答
アプリ側でエラーを適切に処理できるよう、`redirect_uri` にはエラー応答も送信されます。

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

error=access_denied&error_description=the+user+canceled+the+authentication
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| error | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error\_description | 認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

承認の `code` と `id_token` を取得した後は、ユーザーをサインインさせ、代わりにアクセス トークンを取得できます。ユーザーをサインインさせるには、[前](#validating-the-id-token)に説明したように `id_token` を厳密に検証する必要があります。アクセス トークンは、[OAuth プロトコルのドキュメント](active-directory-v2-protocols-oauth-code.md#request-an-access-token)に記載されている手順に従って取得できます。

<!---HONumber=AcomDC_0330_2016------>