<properties
	pageTitle="Azure AD .NET プロトコルの概要 |Microsoft Azure"
	description="この記事では、Azure Active Directory と OAuth 2.0 を利用してテナントの Web アプリケーションと Web API へのアクセスを承認するために HTTP メッセージを使用する方法について説明します。"
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
	ms.date="05/31/2016"
	ms.author="priyamo"/>


# OAuth 2.0 と Azure Active Directory を使用した Web アプリケーションへのアクセスの承認

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

Azure Active Directory (Azure AD) が OAuth 2.0 を使用することにより、ユーザーは Azure AD テナントの Web アプリケーションと Web API へのアクセスを承認することができます。本ガイドでは、オープンソース ライブラリを利用せず、HTTP メッセージを送受信する方法について説明します。本ガイドは言語非依存です。

OAuth 2.0 承認コード フローは、[OAuth 2.0 仕様のセクション 4.1](https://tools.ietf.org/html/rfc6749#section-4.1) で規定されています。Web アプリやネイティブにインストールされるアプリを含め、大半のアプリ タイプで認証と承認を行う際にこのフローが使用されます。

[AZURE.INCLUDE [active-directory-protocols-getting-started](../../includes/active-directory-protocols-getting-started.md)]


## OAuth 2.0 承認フロー

まとめると、アプリケーションの全体的な承認フローは次のようになります。

![OAuth Auth Code Flow](media/active-directory-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)


## 承認コードを要求する

承認コード フローは、クライアントがユーザーを `/authorize` エンドポイントにリダイレクトさせることから始まります。この要求で、クライアントは、ユーザーから取得する必要のあるアクセス許可を指定します。Azure クラシック ポータルのアプリケーションのページ下部のドロアーにある **[エンドポイントの表示]** ボタンから OAuth 2.0 エンドポイントを取得できます。

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

| パラメーター | | 説明 |
| ----------------------- | ------------------------------- | --------------- |
| テナント | 必須 | 要求パスの `{tenant}` 値を使用して、アプリケーションにサインインできるユーザーを制御できます。使用できる値はテナント ID です。たとえば、`8eaef023-2b34-4da1-9baa-8bc8c9d6a490`、`contoso.onmicrosoft.com` または `common` (テナント非依存のトークンの場合) です。 |
| client\_id | 必須 | Azure AD への登録時にアプリに割り当てられたアプリケーション ID。値は Azure の管理ポータルにあります。**[Active Directory]** をクリックしてディレクトリをクリックし、アプリケーションをクリックしてから **[構成]** をクリックします。 |
| response\_type | 必須 | 承認コード フローでは `code` を指定する必要があります。 |
| redirect\_uri | 推奨 | アプリ の redirect\_uri。アプリは、この URI で認証応答を送受信することができます。ポータルで登録したいずれかの redirect\_uri と完全に一致させる必要があります (ただし、URL エンコードが必要)。ネイティブ アプリとモバイル アプリでは、`urn:ietf:wg:oauth:2.0:oob` の既定値を使用します。 |
| scope | 必須 | ユーザーに同意を求める[スコープ](active-directory-v2-scopes.md)の、スペースで区切られたリスト。 |
| response\_mode | 推奨 | 結果として得られたトークンをアプリに返す際に使用するメソッドを指定します。`query` または `form_post` を指定できます。 |
| state | 推奨 | 要求に含まれ、かつトークンの応答として返される値。任意の文字列を指定することができます。[クロスサイト リクエスト フォージェリ攻撃を防ぐために](http://tools.ietf.org/html/rfc6749#section-10.12)通常、ランダムに生成された一意の値が使用されます。この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的にも使用されます。 |
| prompt | 省略可能 | ユーザーとの必要な対話の種類を指定します。現時点で有効な値は 'login'、'none'、'consent' だけです。`prompt=login` は、その要求でユーザーに資格情報の入力を強制させ、シングル サインオンを無効にします。`prompt=none` は反対に、ユーザーに対して対話形式のプロンプトを表示しません。シングル サインオンによって自動的に要求を完了できない場合、v2.0 エンドポイントはエラーを返します。`prompt=consent` では、ユーザーがサインインした後で OAuth 同意ダイアログが表示され、アプリへのアクセス許可の付与をユーザーに求めます。 |
| login\_hint | 省略可能 | ユーザー名が事前にわかっている場合、ユーザーに代わって事前に、サインイン ページのユーザー名/電子メール アドレス フィールドに入力ができます。多くの場合、アプリでは、`preferred_username` 要求を使用して前回のサインインからユーザー名を抽出しておくことで、再認証時にこのパラメーターを使用します。 |
| domain\_hint | 省略可能 | `consumers` か `organizations` のいずれかを指定できます。これが含まれる場合、v2.0 のサインイン ページでユーザーが行う電子メール ベースの検出プロセスがスキップされ、ユーザー エクスペリエンスは若干簡素化されたものになります。多くの場合、アプリでは、前回のサインインから `tid` を抽出することで、再認証時にこのパラメーターを使用します。`tid` 要求の値が `9188040d-6c67-4c5b-b112-36a304b66dad` の場合、`domain_hint=consumers` を使用する必要があります。それ以外の場合は、`domain_hint=organizations` を指定します。 |

> [AZURE.NOTE] ユーザーが組織に所属している場合は、組織の管理者がユーザーに代わって同意または却下するか、あるいはユーザーに同意を許可することができます。そのユーザーは、管理者が許可した場合のみ承認を行うことができます。

この時点でユーザーは、資格情報を入力し、`scope` クエリ パラメーターで指定されたアクセス許可に同意するよう要求されます。ユーザーが認証を行い、同意の許可を与えると、Azure AD は要求で指定されたアプリの `redirect_uri` アドレスに応答を返します。

### 成功応答

正常な応答は次のようになります。

```
http://localhost:12345/?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&state=12345
&session_state=733ad279-b681-49c3-9215-951abf94d2c5
```

| パラメーター | 説明 |
| -----------------------| --------------- |
| admin\_consent | 管理者が同意要求プロンプトに同意した場合、値は True です。|
| code | アプリケーションが要求した承認コード。アプリケーションは承認コードを使用して、対象リソースのアクセス トークンを要求します。 |
| session\_state | 現在のユーザー セッションを識別する一意の値。この値は GUID ですが、検査なしで渡される不透明な値として扱う必要があります。 |
| state | 要求に state パラメーターが含まれている場合、同じ値が応答にも含まれることになります。要求に含まれる state の値をアプリケーション側で確認する必要があります。
要求に state パラメーターが含まれていた場合、同じ値が応答にも含まれます。要求と応答に含まれる state の値が同一であることをアプリケーション側で確認することをお勧めします。

### エラー応答

アプリケーション側でエラーを適切に処理できるよう、`redirect_uri` にもエラー応答が送信される場合があります。

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

## 承認コードを使用してアクセス トークンを要求する

承認コードを取得しユーザーからアクセス許可を得たら、POST 要求を `/token` エンドポイントに送信することで、目的のリソースのアクセス トークンのためにコードを使うことができます。

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: client_secret only required for web apps
```

| パラメーター | | 説明 |
| ----------------------- | ------------------------------- | --------------------- |
| テナント | 必須 | 要求パスの `{tenant}` 値を使用して、アプリケーションにサインインできるユーザーを制御できます。使用できる値はテナント ID です。たとえば、`8eaef023-2b34-4da1-9baa-8bc8c9d6a490`、`contoso.onmicrosoft.com` または `common` (テナント非依存のトークンの場合) です。 |
| client\_id | 必須 | Azure AD への登録時にアプリに割り当てられたアプリケーション ID。値は Azure クラシック ポータルにあります。**[Active Directory]** をクリックしてディレクトリをクリックし、アプリケーションをクリックしてから **[構成]** をクリックします。 |
| grant\_type | 必須 | 承認コード フローでは `authorization_code` を指定する必要があります。 |
| code | 必須 | 前のセクションで取得した `authorization_code` 。 |
| redirect\_uri | 必須 | `authorization_code` を取得するために使用したのと同じ `redirect_uri` 値。 |
| client\_secret | Web アプリの場合は必須 | アプリ登録ポータルで作成した、アプリケーションのシークレット。ネイティブ アプリでは使用しないでください。デバイスに client\_secret を確実に保存することができません。Web アプリや Web API では `client_secret` をサーバー側で安全に保存する機能が備わっており、指定する必要があります。 |


### 成功応答

正常な応答は次のようになります。

```
{
  "access_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
"id_token": " eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.”
}

```

| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| access\_token | 要求されたアクセス トークン。アプリはこのトークンを使用して、保護されたリソース (Web API など) に対し、本人性を証明することができます。 |
| token\_type | トークン タイプ値を指定します。Azure AD でサポートされるのは Bearer タイプのみです。ベアラ― トークンに関する詳細については、「[OAuth2.0 Authorization Framework: Bearer Token Usage (RFC 6750) (OAuth2.0 承認フレームワーク: ベアラ―トークンの使用について (RFC 6750))](http://www.rfc-editor.org/rfc/rfc6750.txt)」を参照してください。 |
| expires\_in | アクセス トークンの有効期間 (秒)。 |
| scope | クライアント アプリケーションに付与される偽装アクセス許可。既定のアクセス許可は `user_impersonation` です。保護されたリソースの所有者は、別の値を Azure AD に登録できます。|
| refresh\_token | OAuth 2.0 更新トークン。現在のアクセス トークンの有効期限が切れた後、アプリはこのトークンを使用して、追加のアクセス トークンを取得することができます。更新トークンは有効期間が長く、リソースへのアクセスを長時間保持するときに利用できます。 |
| id\_token | 無署名の JSON Web トークン (JWT)。このトークンのセグメントを base64Url でデコードすることによって、サインインしたユーザーに関する情報を要求することができます。この値をキャッシュして表示することはできますが、承認やセキュリティ境界の用途でこの値に依存することは避けてください。 |

### JWT トークン要求
`id_token` パラメーターの値にある JWT トークンは次の要求にデコードすることができます。

```
{
 "typ": "JWT",
 "alg": "none"
}.
{
 "aud": "2d4d11a2-f814-46a7-890a-274a72a7309e",
 "iss": "https://sts.windows.net/7fe81447-da57-4385-becb-6de57f21477e/",
 "iat": 1388440863,
 "nbf": 1388440863,
 "exp": 1388444763,
 "ver": "1.0",
 "tid": "7fe81447-da57-4385-becb-6de57f21477e",
 "oid": "68389ae2-62fa-4b18-91fe-53dd109d74f5",
 "upn": "frank@contoso.com",
 "unique_name": "frank@contoso.com",
 "sub": "JWvYdCWPhhlpS1Zsf7yYUxShUwtUm5yzPmw_-jX3fHY",
 "family_name": "Miller",
 "given_name": "Frank"
}.
```

`id_token` パラメーターには、以下の要求の種類があります。JSON Web トークンに関する詳細については、「[JWT IETF draft specification (JWT IETF ドラフト仕様) ](http://go.microsoft.com/fwlink/?LinkId=392344)」を参照してください 。トークンの種類と要求の詳細については、「[サポートされているトークンとクレームの種類](active-directory-token-and-claims.md)」を参照してください。

| 要求の種類 | 説明 |
|------------|-------------|
| aud | トークンの対象ユーザー。クライアント アプリケーションにトークンが発行される場合、対象ユーザーは、クライアントの `client_id` です。
| exp | 期限切れ日時。トークンの有効期限が切れる日時。トークンを有効にするには、現在の日付/時刻が `exp` の値以前である必要があります。日時は UTC 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) から、トークンが発行された日時までの秒数で表されます。 |
| family\_name | ユーザーの姓。アプリケーションでは、この値を表示できます。 |
| given\_name | ユーザーの名。アプリケーションでは、この値を表示できます。 |
| iat | 発行日時。JWT が発行された日時。日時は UTC 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) から、トークンが発行された日時までの秒数で表されます。 |
| iss | トークン発行者を識別します。 |
| nbf | 期間の開始日時。トークンが有効になる日時。トークンを有効にするには、現在の日付/時刻が Nbf 値以降である必要があります。日時は UTC 1970 年 1 月 1 日 (1970-01-01T0:0:0Z) から、トークンが発行された日時までの秒数で表されます。 |
| oid | Azure AD におけるユーザー オブジェクトのオブジェクト識別子 (ID)。 |
| sub | トークンのサブジェクト識別子。これは、トークンが示すユーザーの永続的で不変の識別子です。キャッシュ ロジックでは、この値を使用します。 |
| tid | トークンを発行した Azure AD テナントのテナント識別子 (ID) です。 |
| unique\_name | ユーザーに表示される一意識別子。これは、通常ユーザー プリンシパル名 (UPN) です。 |
| upn | ユーザーのユーザー プリンシパル名。 |
| ver | バージョン。JWT トークンのバージョンで、通常は 1.0 です。 |

### エラー応答

エラー応答の例は次のようになります。

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| パラメーター | 説明 |
| ----------------------- | ------------------------------- |
| error | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error\_description | 認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |
| error\_codes | 診断に役立つ STS 固有のエラー コードの一覧。 |
| timestamp | エラーが発生した時刻。 |
| trace\_id | 診断に役立つ、要求の一意の識別子。 |
| correlation\_id | コンポーネント間での診断に役立つ、要求の一意の識別子。|

## リソースにアクセスするためにアクセス トークンを使用します。

`access_token` を無事取得したら、そのトークンを `Authorization` ヘッダーに追加することによって、Web API への要求に使用することができます。 [RFC 6750](http://www.rfc-editor.org/rfc/rfc6750.txt) 仕様では、HTTP 要求でベアラー トークンを使用して、保護されたリソースにアクセスする方法について説明されています。

### 要求のサンプル

```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

## アクセス トークンの更新

アクセス トークンは有効期間が短く、期限が切れた後もリソースにアクセスし続けるためにはトークンを更新する必要があります。`access_token` を更新するには、もう一度 `POST` 要求を `/token` エンドポイントに送信します。このとき、`code` の代わりに `refresh_token` を指定します。

更新トークンの有効期間は指定されておらず、ポリシー設定や、Azure AD が承認コードの付与を取り消す日時によって変動します。アプリケーションで新しいアクセス トークンの要求が失敗した場合を予想し、対応する必要があります。この場合、新しいアクセス トークンを要求するコードに戻ります。

更新トークンを使って新しいアクセス トークンを取得する**テナント固有**のエンドポイント (**共通**エンドポイントの利用も可能) への要求の例は次のようになります。

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```
| パラメーター | 説明 |
|-----------|-------------|
| access\_token | 要求された新しいアクセス トークン。|
| expires\_in | トークンの残りの有効期間を秒単位で表したもの。標準的な値は 3600 (1 時間) です。 |
| expires\_on | トークンの有効期限が切れる日付と時刻。日時は 1970-01-01T0:0:0Z UTC から期限切れ日時までの秒数として表されます。 |
| refresh\_token | 新しい OAuth 2.0 の refresh\_token で、応答中にアクセス トークンの有効期限が切れた時に、新しいアクセス トークンを要求するために使用します。 |
| resource | アクセス トークンを使ってアクセスできる保護されたリソースを識別します。 |
| scope | ネイティブ クライアント アプリケーションに付与される偽装アクセス許可。既定のアクセス許可は **user\_impersonation** です。ターゲット リソースの所有者は、代替の値を Azure AD に登録できます。 |
| token\_type | トークンのタイプ。サポートされている値は **bearer** のみです。 |

### 成功応答

正常なトークン応答は次のようになります。

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```

### エラー応答

エラー応答の例は次のようになります。

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872.  This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant.  You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

<!---HONumber=AcomDC_0608_2016-->