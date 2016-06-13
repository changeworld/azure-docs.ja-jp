<properties
	pageTitle="Azure AD .NET プロトコルの概要 |Microsoft Azure"
	description="この記事では、Azure Active Directory と OpenID Connect を利用してテナントの Web アプリケーションと Web API へのアクセスを承認するために HTTP メッセージを使用する方法について説明します。"
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="priyamo"/>


# OpenID Connect と Azure Active Directory を使用する Web アプリケーションへのアクセスの承認

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) は、OAuth 2.0 プロトコル上に構築された単純な ID 層です。OAuth 2.0 では保護されたリソースにアクセスするための**アクセス トークン**を取得して使用するためのメカニズムを定義しますが、ID 情報を提供するための標準的な方法は定義しません。OpenID Connect は OAuth 2.0 承認プロセスの拡張機能として認証を実装します。これにより、エンド ユーザーに関する基本的なプロファイル情報を提供するだけでなく、ユーザーの ID を検証する `id_token` 形式のユーザーに関する情報を提供します。

OpenID Connect は、サーバーでホストされ、ブラウザーでアクセスされる Web アプリケーションを構築している場合に推奨されます。

## OpenID Connect を使用する認証フロー

最も基本的なサインイン フローには次の手順が含まれています。各手順についてはこの後詳しく説明します。

![OpenID Connect 認証フロー](media/active-directory-protocols-openid-connect-code/active-directory-oauth-code-flow-web-app.png)


## サインイン要求を送信する

Web アプリケーションでユーザーを認証する必要がある場合、ユーザーを `/authorize` エンドポイントにリダイレクトさせる必要があります。この要求は、[OAuth 2.0 承認コード フロー](active-directory-protocols-oauth-code.md)の最初の部分に似ていますが、いくつかの重要な違いがあります。

- 要求の `scope` パラメーターにはスコープ `openid` が含まれる必要があります。
- `response_type` パラメーターには `id_token` が含まれる必要があります。
- 要求には `nonce` パラメーターが含まれる必要があります。

したがって、要求の例は次のようになります。

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=id_token
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=form_post
&scope=openid
&state=12345
&nonce=7362CAEA-9CA5-4B43-9BA3-34D7C303EBA7
```

| パラメーター | | 説明 |
| ----------------------- | ------------------------------- | --------------- |
| テナント | 必須 | 要求パスの `{tenant}` 値を使用して、アプリケーションにサインインできるユーザーを制御できます。使用できる値はテナント ID です。たとえば、`8eaef023-2b34-4da1-9baa-8bc8c9d6a490`、`contoso.onmicrosoft.com` または `common` (テナント独立のトークンの場合) です。 |
| client\_id | 必須 | Azure AD への登録時にアプリに割り当てられたアプリケーション ID。これは Azure クラシック ポータルで見つけることができます。**[Active Directory]** をクリックしてディレクトリをクリックし、アプリケーションをクリックしてから **[構成]** をクリックします。 |
| response\_type | 必須 | OpenID Connect サインインでは、`id_token` を指定する必要があります。`code` などの他の response\_types が含まれていてもかまいません。 |
| scope | 必須 | スコープのスペース区切りリスト。OpenID Connect では、スコープ `openid` を指定する必要があります。このスコープは、承認 UI で "サインイン" アクセス許可に変換されます。同意を求めるこの要求には他のスコープが含まれていてもかまいません。 |
| nonce | 必須 | アプリによって生成された、要求に含まれる値。この値が、最終的な `id_token` に要求として含まれます。アプリでこの値を確認することにより、トークン再生攻撃を緩和することができます。通常、この値はランダム化された一意の文字列または GUID になっており、要求の送信元を特定する際に使用できます。 |
| redirect\_uri | 推奨 | アプリ の redirect\_uri。アプリは、この URI で認証応答を送受信することができます。ポータルで登録したいずれかの redirect\_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| response\_mode | 推奨 | 結果として得られた authorization\_code をアプリに返す際に使用するメソッドを指定します。サポートされる値は `form_post` (*HTTP フォーム ポスト*の場合) または `fragment` (*URL フラグメント*の場合) です。Web アプリケーションでは、トークンをアプリケーションに最も安全に転送できるように、`response_mode=form_post` を使用することをお勧めします。  
| state | 推奨 | 要求に含まれ、かつトークンの応答として返される値。任意の文字列を指定することができます。[クロスサイト リクエスト フォージェリ攻撃](http://tools.ietf.org/html/rfc6749#section-10.12)を防ぐために通常、ランダムに生成された一意の値が使用されます。この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的にも使用されます。 |
| prompt | 省略可能 | ユーザーとの必要な対話の種類を指定します。現時点で有効な値は 'login'、'none'、'consent' だけです。`prompt=login` は、その要求でユーザーに資格情報の入力を強制させ、シングル サインオンを無効にします。`prompt=none` は反対に、ユーザーに対して対話形式のプロンプトを表示しません。シングル サインオンによって自動的に要求を完了できない場合、エンドポイントはエラーを返します。`prompt=consent` では、ユーザーがサインインした後で OAuth 同意ダイアログが表示され、アプリへのアクセス許可の付与をユーザーに求めます。 |
| login\_hint | 省略可能 | ユーザー名が事前にわかっている場合、ユーザーに代わって事前に、サインイン ページのユーザー名/電子メール アドレス フィールドに入力ができます。多くの場合、アプリでは、`preferred_username` 要求を使用して前回のサインインからユーザー名を抽出しておくことで、再認証時にこのパラメーターを使用します。 |


現時点では、ユーザーに資格情報の入力と認証が求められます。

### 応答のサンプル

ユーザー認証後の応答は以下のサンプルのようになります。

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&state=12345
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| id\_token | アプリが要求した `id_token`。この `id_token` を使用してユーザーの身元を確認し、そのユーザーとのセッションを開始することができます。 |
| state | 要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

### エラー応答
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


単に `id_token` を受け取るだけでは、ユーザーを認証するには不十分です。`id_token` の署名を検証し、要求をアプリの要件に従って確認する必要があります。Azure AD エンドポイントは、JSON Web トークン (JWT) と公開キー暗号を使用してトークンに署名し、それらが有効であることを確認します。

クライアント コードで `id_token` を検証することもできますが、`id_token` をバックエンド サーバーに送信して検証を実行するのが一般的な方法です。`id_token` の署名を検証した後に、確認の必要な要求がいくつか存在します。

シナリオに応じてその他の要求も検証することができます。以下に一般的な検証の例をいくつか挙げます。

- ユーザー/組織がアプリにサインアップ済みであることを確認する。
- 適切な承認/特権がユーザーにあることを確認する。
- 多要素認証など特定の強度の認証が行われたことを確認する。

`id_token` を十分検証したら、ユーザーとのセッションを開始し、`id_token` の要求を使用してそのユーザーに関する情報をアプリで取得することができます。取得した情報は、表示、記録、承認などに利用することができます。トークンの種類と要求の詳細については、「[サポートされているトークンとクレームの種類](active-directory-token-and-claims.md)」を参照してください。

## サインアウト要求を送信する

ユーザーをアプリからサインアウトさせるとき、アプリの Cookie を消去する、あるいはユーザーとのセッションを終了するだけでは十分ではありません。サインアウトするには、ユーザーを `end_session_endpoint` にリダイレクトする必要もあります。そうしないと、ユーザーが資格情報を再入力しなくてもアプリで再認証できます。Azure AD エンドポイントのシングル サインオン セッションが有効であるためです。

OpenID Connect メタデータ ドキュメントの一覧にある `end_session_endpoint` にはユーザーを簡単にリダイレクトできます。

```
GET https://login.microsoftonline.com/common/oauth2/logout?
post_logout_redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F

```

| パラメーター | | 説明 |
| ----------------------- | ------------------------------- | ------------ |
| post\_logout\_redirect\_uri | 推奨 | ログアウトの完了後にユーザーをリダイレクトする URL。指定しない場合、ユーザーに汎用メッセージが表示されます。 |

## トークンの取得

多くの Web アプリは、ユーザーをサインインさせるだけでなく、OAuth を使用してそのユーザーの代わりに Web サービスにアクセスする必要もあります。このシナリオでは、OpenID Connect を使ってユーザー認証を行うと同時に、OAuth 承認コード フローを使って、`access_tokens` を取得するための `authorization_code` を取得します。

## アクセス トークンの取得

アクセス トークンを取得するには、前に示したサインイン要求を変更する必要があります。

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e		// Your registered Application Id
&response_type=id_token+code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F 	  // Your registered Redirect Uri, url encoded
&response_mode=form_post						      // form_post', or 'fragment'
&scope=openid
&resource=https%3A%2F%2Fservice.contoso.com%2F									 
&state=12345						 				 // Any value, provided by your app
&nonce=678910										 // Any value, provided by your app
```

アクセス許可スコープを要求に組み込み、`response_type=code+id_token` を使うことによって、`authorize` エンドポイントはユーザーが `scope` クエリ パラメーターで示されているアクセス許可に同意したことを確認し、アクセス トークンと引き替えに承認コードをアプリに返します。

### 成功応答

`response_mode=form_post` を使用した場合の正常な応答は次のようになります。

```
POST /myapp/ HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded

id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNB...&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&state=12345
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| id\_token | アプリが要求した `id_token`。この `id_token` を使用してユーザーの身元を確認し、そのユーザーとのセッションを開始することができます。 |
| code | アプリが要求した authorization\_code。アプリは承認コードを使用して、対象リソースのアクセス トークンを要求します。承認コードは有効期間が非常に短く、通常 10 分後には期限切れとなります。 |
| state | 要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

### エラー応答

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

承認の `code` と `id_token` を取得した後は、ユーザーをサインインさせ、代わりにアクセス トークンを取得できます。ユーザーをサインインさせるには、前に説明したように `id_token` を厳密に検証する必要があります。アクセス トークンは、[OAuth プロトコルのドキュメント](active-directory-protocols-oauth-code.md#Use-the-Authorization-Code-to-Request-an-Access-Token)に記載されている手順に従って取得できます。

<!---HONumber=AcomDC_0601_2016-->