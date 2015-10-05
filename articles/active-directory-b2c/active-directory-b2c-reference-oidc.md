<properties
	pageTitle="Azure AD B2C プレビュー | Microsoft Azure"
	description="Azure AD で導入された OpenID Connect 認証プロトコルを利用し、Web アプリケーションを構築します。"
	services="active-directory-b2c"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="dastrock"/>

# Azure AD B2C プレビュー: OpenID Connect による Web サインイン

OpenID Connect は OAuth 2.0 を基盤として開発された認証プロトコルであり、ユーザーを Web アプリケーションに安全にサインインさせるために利用されます。Azure AD B2C で導入された OpenID Connect を利用することで、Web アプリケーションのサインアップ、サインイン、その他の ID 管理を Azure AD に外注できます。本ガイドでは、この外注を言語に依存せずに行う方法について説明します。オープンソース ライブラリを利用せず、HTTP メッセージを送受信する方法について説明します。

<!-- TODO: Need link to libraries -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

[OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) は、OAuth 2.0 の*承認*プロトコルを*認証*プロトコルとして使用できるように拡張したものです。OpenID Connect を使うことで OAuth を使用したシングル サインオンを行うことができます。OpenID Connect には、`id_token` の概念が導入されています。クライアントは、このセキュリティ トークンによってユーザーの本人性を確認し、そのユーザーに関する基本的なプロファイル情報を取得することができます。OAuth 2.0 を拡張強化したものであり、アプリはまた、[認証サーバー](active-directory-b2c-reference-protocols.md#the-basics)で保護されるリソースにアクセスするための **access\_tokens** を安全に取得できます。OpenID Connect は、サーバーでホストされ、ブラウザーでアクセスされる Web アプリケーションを構築している場合に推奨されます。Azure AD B2C を利用し、モバイルまたはデスクトップ アプリケーションに ID 管理を追加する場合、OpenID Connect ではなく、[OAuth 2.0](active-directory-b2c-reference-oauth-code.md) をご利用ください。

Azure AD B2C は、単純な認証と権限付与以上のことができるように標準の OpenID Connect プロトコルを拡張したものです。[**ポリシー パラメーター**](active-directory-b2c-reference-poliices.md)を導入しており、このパラメーターにより、OpenID Connect を利用し、サインアップ、サインイン、プロファイル管理などのユーザー操作をアプリに追加できます。ここでは、OpenID Connect とポリシーを利用し、Web アプリケーションに各種のユーザー操作を導入し、Web API にアクセスするための access\_tokens を取得する方法について説明します。

下の HTTP 要求例では、サンプル B2C ディレクトリの **fabrikamb2c.onmicrosoft.com**、サンプル アプリケーションの ****https://aadb2cplayground.azurewebsites.net**、ポリシーを利用します。これらの値を利用し、要求を自由にお試しいただけます。あるいは、独自の値で置換できます。[独自の B2C ディレクトリ、アプリケーション、ポリシーの取得](#use-your-own-b2c-directory)方法について学習してください。

## 認証要求を送信する
Web アプリでユーザーを認証し、ポリシーを実行する必要があるときは、ユーザーを `/authorize` エンドポイントにリダイレクトさせます。これはフローの対話部分であり、実際、ユーザーはポリシーに基づいて操作します。この要求では、クライアントはユーザーから取得する必要があるアクセス許可を `scope` パラメーターで示し、実行するポリシーを `p` パラメーターで示します。3 つの例を以下に示します (読みやすいように改行してあります)。それぞれ異なるポリシーが使用されています。各要求の動作を感覚的に理解するために、要求をブラウザーに貼り付け、実行してみてください。

#### サインイン ポリシーを使用する

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_in
```

#### サインアップ ポリシーを使用する

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_sign_up
```

#### プロファイル ポリシー編集を使用する

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&p=b2c_1_edit_profile
```

| パラメーター | | 説明 |
| ----------------------- | ------------------------------- | ----------------------- |
| client\_id | 必須 | [Azure ポータル](https://portal.azure.com)からアプリに割り当てられたアプリケーション ID。 |
| response\_type | 必須 | OpenID Connect には `id_token` を指定する必要があります。Web アプリで Web API を呼び出すためのトークンも必要になる場合、ここで行ったように、`code+id_token` を利用できます。 |
| redirect\_uri | 必須 | アプリ の redirect\_uri。アプリは、この URI で認証応答を送受信することができます。ポータルで登録したいずれかの redirect\_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。 |
| scope | 必須 | スコープのスペース区切りリスト。1 つのスコープ値は、要求されている両方のアクセス許可を Azure AD を示します。`openid` スコープは、ユーザーをサインインさせ、**id\_tokens** の形式でユーザーに関するデータを取得するためのアクセス許可を示します (これについては後に詳しく説明します)。Web アプリの場合、`offline_access` スコープは任意です。リソースに長期アクセスするためにアプリは **refresh\_token** を必要とすることを示します。 |
| response\_mode | 推奨 | 結果として得られた authorization\_code をアプリに返す際に使用するメソッドを指定します。'query'、'form\_post'、'fragment' のいずれかを指定できます。 |
| state | 推奨 | 要求に含まれ、かつトークンの応答として返される値。任意の文字列を指定することができます。クロスサイト リクエスト フォージェリ攻撃を防ぐために通常、ランダムに生成された一意の値が使用されます。この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページなど) に関する情報をエンコードする目的にも使用されます。 |
| nonce | 必須 | 要求に追加する (アプリによって生成された) 値。この値が、最終的な id\_token に要求として追加されます。アプリでこの値を確認することにより、トークン再生攻撃を緩和することができます。通常この値はランダム化された一意の文字列になっており、要求の送信元を特定する際に使用できます。 |
| p | 必須 | 実行するポリシーを示します。B2C ディレクトリで作成されたポリシーの名前であり、その値は必ず「b2c\_1\_」で始まります。ポリシーに関する詳細は、[ここ](active-directory-b2c-reference-policies.md)にあります。 |
| prompt | 省略可能 | ユーザーとの必要な対話の種類を指定します。現時点で有効な値は 'login' のみです。この場合ユーザーは要求時に、その資格情報を入力する必要があります。シングル サインオンは作用しません。 |

この時点で、ユーザーにポリシーのワークフローの完了が求められます。たとえば、ユーザー名とパスワードを入力したり、ソーシャル ID でサインインしたり、ディレクトリにサインアップしたりする必要があります。他にも、ポリシーの定義に基づき、多数の手順があります。ユーザーがポリシーを完了すると、Azure AD は `response_mode` パラメーターに指定されたメソッドを使い、指定された `redirect_uri` でアプリに応答を返します。実行されたポリシーに関係なく、上記のすべての例で応答はまったく同じになります。

`response_mode=query` を使用した場合の正常な応答は次のようになります。

```
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| id\_token | アプリが要求した id\_token。この id\_token を使用してユーザーの本人性を確認し、そのユーザーとのセッションを開始することができます。id\_token とその内容の詳細については、[Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)を参照してください。 |
| code | `response_type=code+id_token` を使用した場合、アプリが要求した authorization\_code。アプリは承認コードを使用して、対象リソースのアクセス トークンを要求します。承認コードは有効期間が非常に短く、通常 10 分後には期限切れとなります。 |
| state | 要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |

アプリ側でエラーを適切に処理できるよう、`redirect_uri` にはエラー応答も送信されます。

```
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| error | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error\_description | 認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |
| state | 要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。要求と応答に含まれる状態値が同一であることをアプリ側で確認する必要があります。 |


## id\_token を検証する
単に id\_token を受け取るだけでは、ユーザーを認証するには不十分です。id\_token の署名を検証し、そのトークンに含まれる要求をアプリの要件に従って確認する必要があります。Azure AD B2C は、[JSON Web トークン (JWT)](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) と公開キー暗号を使用してトークンに署名し、それらが有効であることを証明します。優先言語にもよりますが、JWT を検証するためのさまざまなオープンソース ライブラリを入手できます。独自の検証ロジックを実装するより、オープンソース ライブラリを試してみることをお勧めします。ライブラリを正しく利用する方法を模索するとき、ここにある情報が役に立つでしょう。

Azure AD B2C には、OpenID Connect メタデータ エンドポイントがあって、アプリは、このエンドポイントを使用することで、Azure AD B2C に関する情報を実行時にフェッチすることができます。この情報には、エンドポイント、トークンの内容、トークンの署名キーが含まれます。B2C ディレクトリにはポリシー別の JSON メタデータ ドキュメントがあります。たとえば、`fabrikamb2c.onmicrosoft.com` の `b2c_1_sign_in` ポリシーのメタデータ ドキュメントは次の場所にあります。

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

この構成ドキュメントのプロパティの 1 つに `jwks_uri` があります。この値は同じポリシーで次のようになります。

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

id\_token の署名で使用されたポリシー (とメタデータのフェッチ場所) は 2 つの方法で判断できます。最初の方法です。ポリシー名が id\_token の `acr` 要求に含まれています。id\_token の要求を解析する方法については、「[Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)」を参照してください。もう 1 つの方法です。要求を発行するとき、`state` パラメーターの値に含まれるポリシーを符号化し、それから復号化して使用されたポリシーを判断します。いずれの方法も完全に有効です。

OpenID Connect メタデータ エンドポイントからメタデータ ドキュメントを取得したら、このエンドポイントにある RSA256 公開鍵を利用し、id\_token の署名を検証できます。このエンドポイントには常時、複数のキーがリストされており、それぞれのキーは `kid` で識別されます。id\_token のヘッダーにはさらに `kid` 要求が格納されていて、複数のキーのうち、どのキーが id\_token の署名に使用されたかが、この要求によって示されます。[トークンの検証](active-directory-b2c-reference-tokens.md#validating-tokens)と[署名キーのロールオーバーに関する重要な情報](active-directory-b2c-reference-tokens.md#validating-tokens)を含む詳細については、[Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)を参照してください。
<!--TODO: Improve the information on this-->

id\_token の署名を検証した後に、確認の必要な要求がいくつか存在します。

- トークン再生攻撃を防止するために、`nonce` 要求を検証する必要があります。その値が、サインイン要求で指定した値と一致していることが必要です。
- id\_token が確実に自分のアプリに対して発行されたものであることを確認するために `aud` 要求を検証する必要があります。その値がアプリのアプリケーション ID と一致している必要があります。
- id\_token の有効期限が切れていないことを確認するために、`iat` 要求と `exp` 要求を検証する必要があります。

シナリオに応じてその他の要求も検証することができます。以下に一般的な検証の例をいくつか挙げます。

- ユーザー/組織がアプリにサインアップ済みであることを確認する。
- 適切な承認/特権がユーザーにあることを確認する。
- 多要素認証など特定の強度の認証が行われたことを確認する。

id\_token に含まれる要求の詳細については、[Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)を参照してください。

id\_token を十分検証したら、ユーザーとのセッションを開始し、id\_token に含まれる要求を使ってそのユーザーに関する情報をアプリの中で取得することができます。取得した情報は、表示、記録、承認などに利用することができます。

## トークンを取得する
Web アプリに必要なことがポリシーの実行だけであれば、以降のセクションを一部省略できます。以降のセクションは、Web アプリで Web API を認証呼び出しする必要があり、その呼び出しも Azure AD B2C で保護する場合にのみ適用されます。

`POST` 要求を `/token` エンドポイントに送信することで、(`response_type=code+id_token` を利用し) トークンに取得した authorization\_code を任意のリソースに適用できます。Azure AD B2C プレビューでは、トークンを要求できる唯一のリソースはアプリの独自のバックエンド Web API です。トークンの要求には一般的にスコープ `openid` が使用されます。

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob",
	"client_secret": "<your-application-secret>"
}
```

| パラメーター | | 説明 |
| ----------------------- | ------------------------------- | --------------------- |
| p | 必須 | 認証コードの取得に使用されたポリシー。この要求に別のポリシーを使用することはできません。**このパラメーターはクエリ文字列に追加されることに注意してください。**POST 本文ではありません。 |
| client\_id | 必須 | [Azure ポータル](https://portal.azure.com)からアプリに割り当てられたアプリケーション ID。 |
| grant\_type | 必須 | 承認コード フローでは `authorization_code` を指定する必要があります。 |
| scope | 必須 | スコープのスペース区切りリスト。1 つのスコープ値は、要求されている両方のアクセス許可を Azure AD を示します。`openid` スコープは、ユーザーをサインインさせ、**id\_tokens** の形式でユーザーに関するデータを取得するためのアクセス許可を示します。クライアントと同じアプリケーション ID で表され、アプリの独自のバックエンド Web API にトークンを届けるために利用できます。`offline_access` 範囲は、リソースに長期アクセスするためにアプリは **refresh\_token** を必要とすることを示します。 |
| code | 必須 | フローの最初の段階で取得した authorization\_code。 |
| redirect\_uri | 必須 | authorization\_code を受け取った、アプリケーションの redirect\_uri。 |
| client\_secret | 必須 | [Azure ポータル](https://portal.azure.com)で生成したアプリケーション シークレット。このアプリケーション シークレットは重要なセキュリティ アーティファクトであり、サーバーに安全に保管する必要があります。また、慎重を期して、このクライアント シークレットを定期的に変更してください。 |

正常なトークン応答は次のようになります。

```
{
	"not_before": "1442340812",
	"token_type": "Bearer",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"scope": "openid offline_access",
	"id_token_expires_in": "3600",
	"profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI3NzU1MjdmZi05YTM3LTQzMDctOGIzZC1jY...",
	"refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
	"refresh_token_expires_in": "1209600"
}
```
| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| not\_before | トークンが有効と見なされる時間 (エポック時間)。 |
| token\_type | トークン タイプ値を指定します。Azure AD でサポートされるのは Bearer タイプのみです。 |
| id\_token | 要求した署名付きの JWT トークン。 |
| scope | トークンの有効スコープ。後の利用のためにトークンのキャッシュに利用できます。 |
| id\_token\_expires\_in | id\_token の有効期間 (秒)。 |
| profile\_info | base-64 エンコードの JSON 文字列。ネイティブ アプリケーションに表示するユーザーに関する役に立つ情報が含まれる場合があります。その厳密なコンテンツは、ポリシーで構成したアプリケーション要求に基づきます。 |
| refresh\_token | OAuth 2.0 更新トークン。現在のトークンの有効期限が切れた後、アプリはこのトークンを使用して、追加のトークンを取得します。Refresh\_token は有効期間が長く、リソースへのアクセスを長時間保持するときに利用できます。詳細については、[B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)を参照してください。更新トークンを受け取るには、認証要求とトークン要求の両方でスコープ `offline_access` を使用していなければなりません。 |
| refresh\_token\_expires\_in | 更新トークンの最大有効期間 (秒)。ただし、更新トークンは任意の時点で無効になる場合があります。 |

> [AZURE.NOTE]この時点で access\_token の場所がわからなければ、次の点を考慮してください。`openid` スコープを要求すると、Azure AD は応答で JWT `id_token` を発行します。この `id_token` は厳密には OAuth 2.0 access\_token ではなく、クライアントと同じ client\_id で表され、アプリの独自のバックエンド サービスと通信するときなどに利用できます。`id_token` は署名付きの JWT ベアラー トークンであり、HTTP 認証ヘッダーでリソースに送信し、要求の認証に利用できます。違いは、`id_token` には、特定のクライアント アプリケーションに与えられるアクセスをスコープ ダウンするメカニズムがないことにあります。ただし、(現行の Azure AD B2C プレビューでそうであるように) クライアント アプリケーションがバックエンド サービスと通信できる唯一のクライアントの場合、そのようなスコープ メカニズムは必要ありません。Azure AD B2C プレビューで追加のファースト パーティ リソースとサード パーティ リソースと通信する機能がクライアントに与えられるとき、access\_tokens が導入されます。ただし、そのときであっても、アプリの独自のバックエンド サーバスには `id_tokens` で通信することが推奨されます。Azure AD B2C プレビューで構築できるアプリケーションの種類については、[この記事](active-directory-b2c-apps.md)を参照してください。

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

## トークンを使用する
`id_token` を無事取得したら、そのトークンを `Authorization` ヘッダーに追加することによって、バックエンド Web API への要求に使用することができます。

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## トークンを更新する
Id\_tokens は有効期間が短く、期限が切れた後もリソースにアクセスし続けるためにはトークンを更新する必要があります。アクセス トークンを更新するには、もう一度 `POST` 要求を `/token` エンドポイントに送信します。このとき、`code` の代わりに `refresh_token` を指定します。

```
POST fabrikamb2c.onmicrosoft.com/v2.0/oauth2/token?p=b2c_1_sign_in HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "refresh_token",
	"client_id": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6",
	"scope": "openid offline_access",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...",
	"redirect_uri": "urn:ietf:wg:oauth:2.0:oob",
	"client_secret": "<your-application-secret>"	
}
```

| パラメーター | | 説明 |
| ----------------------- | ------------------------------- | -------- |
| p | 必須 | 元の更新トークンの取得に使用されたポリシー。この要求に別のポリシーを使用することはできません。**このパラメーターはクエリ文字列に追加されることに注意してください。**POST 本文ではありません。 |
| client\_id | 必須 | [Azure ポータル](https://portal.azure.com)からアプリに割り当てられたアプリケーション ID。 |
| grant\_type | 必須 | この段階の承認コード フローでは `refresh_token` を指定する必要があります。 |
| scope | 必須 | スコープのスペース区切りリスト。1 つのスコープ値は、要求されている両方のアクセス許可を Azure AD を示します。`openid` スコープは、ユーザーをサインインさせ、**id\_tokens** の形式でユーザーに関するデータを取得するためのアクセス許可を示します。クライアントと同じアプリケーション ID で表され、アプリの独自のバックエンド Web API にトークンを届けるために利用できます。`offline_access` 範囲は、リソースに長期アクセスするためにアプリは **refresh\_token** を必要とすることを示します。 |
| redirect\_uri | 必須 | authorization\_code を受け取った、アプリケーションの redirect\_uri。 |
| refresh\_token | 必須 | フローの第 2 段階で取得した元の refresh\_token。更新トークンを受け取るには、認証要求とトークン要求の両方でスコープ `offline_access` を使用していなければなりません。 |
| client\_secret | 必須 | [Azure ポータル](https://portal.azure.com)で生成したアプリケーション シークレット。このアプリケーション シークレットは重要なセキュリティ アーティファクトであり、サーバーに安全に保管する必要があります。また、慎重を期して、このクライアント シークレットを定期的に変更してください。 |

正常なトークン応答は次のようになります。

```
{
	"not_before": "1442340812",
	"token_type": "Bearer",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"scope": "openid offline_access",
	"id_token_expires_in": "3600",
	"profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI3NzU1MjdmZi05YTM3LTQzMDctOGIzZC1jY...",
	"refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
	"refresh_token_expires_in": "1209600"
}
```
| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| not\_before | トークンが有効と見なされる時間 (エポック時間)。 |
| token\_type | トークン タイプ値を指定します。Azure AD でサポートされるのは Bearer タイプのみです。 |
| id\_token | 要求した署名付きの JWT トークン。 |
| scope | トークンの有効スコープ。後の利用のためにトークンのキャッシュに利用できます。 |
| id\_token\_expires\_in | id\_token の有効期間 (秒)。 |
| profile\_info | base-64 エンコードの JSON 文字列。ネイティブ アプリケーションに表示するユーザーに関する役に立つ情報が含まれる場合があります。その厳密なコンテンツは、ポリシーで構成したアプリケーション要求に基づきます。 |
| refresh\_token | OAuth 2.0 更新トークン。現在のトークンの有効期限が切れた後、アプリはこのトークンを使用して、追加のトークンを取得します。Refresh\_token は有効期間が長く、リソースへのアクセスを長時間保持するときに利用できます。詳細については、[B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)を参照してください。 |
| refresh\_token\_expires\_in | 更新トークンの最大有効期間 (秒)。ただし、更新トークンは任意の時点で無効になる場合があります。 |

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


<!-- 

Here is the entire flow for a native  app; each request is detailed in the sections below:

![OAuth Auth Code Flow](./media/active-directory-b2c-reference-oauth-code/convergence_scenarios_native.png) 

-->

## サインアウト要求を送信する

ユーザーをアプリからサインアウトさせるとき、アプリの Cookie を消去する、あるいはユーザーとのセッションを終了するだけでは十分ではありません。サインアウトするには、ユーザーを Azure AD にリダイレクトする必要もあります。そうしない場合、ユーザーは資格情報を再入力しなくてもアプリで再認証されることがあります。Azure AD のシングル サインオン セッションが有効であるためです。

[前述](#validate-the-id-token)の同じ OpenID Connect メタデータ ドキュメントの一覧にある `end_session_endpoint` にユーザーをリダイレクトできます。

```
GET https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/logout?
p=b2c_1_sign_in
&post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| パラメーター | | 説明 |
| ----------------------- | ------------------------------- | ------------ |
| p | 必須 | アプリケーションにサインインするためにユーザーが最近使用したポリシー。 |
| post\_logout\_redirect\_uri | 推奨 | ログアウトの完了後にユーザーをリダイレクトする URL。指定しない場合、Azure AD B2C はユーザーに汎用メッセージを表示します。 |

> [AZURE.NOTE]ユーザーを `end_session_endpoint` にリダイレクトすると、Azure AD のユーザーのシングル サインオン状態が一部消去されますが、現在のところ、これはサインアウトとして有効ではありません。それどころか、ユーザーがサインインに利用する IDP を選択すると、資格情報を入力しなくても再認証されます。ソーシャル IDP の場合、そのような行動が予想されます。ユーザーが B2C ディレクトリからサインアウトするとき、Facebook アカウントからの完全なサインアウトを望んでいるとは限りません。ただし、ローカル アカウントの場合、ユーザーのセッションを適切に終了できなければなりません。ローカル アカウントのサインアウトが適切に機能しないのは、Azure AD プレビューの既知の[制限事項](active-directory-b2c-limitations.md)です。短期的な回避策は認証要求ごとに `&prompt=login` パラメーターを送信することです。要求された動作のように見えますが、B2C ディレクトリのアプリケーション間のシングル サインオンが終了します。

## 独自の B2C ディレクトリを使用する

これらの要求を自分で試す場合、最初に次の 3 つの手順を実行し、上記のサンプルの値を独自の値に置換する必要があります。

- [B2C ディレクトリを作成し](active-directory-b2c-get-started.md)、要求でディレクトリの名前を使用します。
- [アプリケーションを作成し](active-directory-b2c-app-registration.md)、アプリケーション ID と redirect\_uri を取得します。アプリに **web app/web api** を追加し、必要に応じて、**アプリケーション シークレット**を作成します。
- [ポリシーを作成し](active-directory-b2c-reference-policies.md)、ポリシー名を取得します。

<!--

TODO

OpenID Connect for the v2.0 app model is the recommended way to implement sign-in for a [web  app](active-directory-v2-flows.md#web-apps).  The most basic sign-in flow contains the following steps:

image goes here

-->

<!---HONumber=Sept15_HO4-->