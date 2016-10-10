
<properties
	pageTitle="Azure AD v2.0 OAuth クライアントの資格情報フロー | Microsoft Azure"
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
	ms.topic="article"
	ms.date="09/26/2016"
	ms.author="dastrock"/>

# v2.0 プロトコル - OAuth 2.0 クライアント資格情報のフロー

[OAuth 2.0 クライアント資格情報の許可](http://tools.ietf.org/html/rfc6749#section-4.4)は、"2 本足のOAuth" とも呼ばれ、アプリケーションの ID を使用する Web ホストのリソースへのアクセスに使用できます。一般的には、エンドユーザーの即時 precense を使用せずに、バック グラウンドで実行されるサーバー間の相互作用に使用されます。これらのアプリケーションは、**"デーモン"** または **"サービス アカウント"** と呼ばれます。

> [AZURE.NOTE]
	Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。v2.0 エンドポイントを使用する必要があるかどうかを判断するには、[v2.0 の制限事項](active-directory-v2-limitations.md)に関するページをお読みください。

より一般的な 3 種類の "三本足の OAuth" では、特定のユーザーに代わり、クライアント アプリケーションにリソースへのアクセス許可が付与されます。通常、[同意](active-directory-v2-scopes.md)プロセス中に、アクセス許可はユーザーからアプリケーションに**委任**されます。ただし、クライアントの資格情報のフローでは、アクセス許可はアプリケーション自体に**直接**付与されます。アプリケーションがリソースにトークンを提示する場合、リソースはアプリ自体がアクションの一部を実行する承認を行います。一部のユーザーが承認を行うわけではありません。

## プロトコルのダイアグラム
クライアント資格情報フローの全体像は次のようになります。各手順についてはこの後詳しく説明します。

![クライアントの資格情報フロー](../media/active-directory-v2-flows/convergence_scenarios_client_creds.png)

## 直接承認を取得する 
通常、アプリからリソースへアクセスするために直接承認を受けるには 2 つの方法があり、リソースのアクセス制御リスト、または Azure AD でのアプリケーション アクセス許可の割り当てを介します。その他にもリソースがクライアントを承認する方法がいくつかあり、各リソース サーバーはアプリケーションにとって最適な方法を選択します。これらの 2 つの方法は、Azure AD で最も一般的であり、クライアント資格情報フローを実行するクライアントとリソースリソースに推奨されています。

### アクセス制御リスト
指定されたリソースプロバイダーは、一定レベルのアクセス許可を付与するアプリケーション ID 一覧に基づいて承認チェックを適用します。リソースが v2.0 エンドポイントからトークンを受け取るときに、トークンをデコードし、`appid` と `iss` の要求からクライアント アプリケーション ID を抽出できます。そして、アプリケーションと管理するアクセス制御リスト (ACL) を比較することができます。アクセス制御リストの粒度と方法は、リソースごとに大幅に異なります。

一般的な ACL の使用例は、Web アプリケーションまたは Web API のテスト ランナーです。Web API は、さまざまなクライアントに完全なアクセス許可のサブセットのみを付与できます。API でエンド ツー エンド テストを実行するために作成されるテスト クライアントは、v2.0 エンドポイントからトークンを取得して、API に送信します。API は、ACL で API のすべての機能にアクセスするテスト クライアントのアプリケーション ID を設定します。サービスにこのようなリストがある場合、呼び出し元の `appid` を検証するだけでなく、トークンの `iss` が信頼されているかについても検証する必要があります。

この種類の承認は、デーモンおよび個人の Microsoft アカウントを持つコンシューマー ユーザーが所有するデータにアクセスするサービス アカウントに共通しています。組織が所有するデータでは、アプリケーションのアクセス許可を介して必要な承認を取得することを推奨します。

### アプリケーションのアクセス許可
ACL を使用する代わりに、API はアプリケーションに付与される **アプリケーションのアクセス許可** を公開できます。アプリケーションのアクセス許可は、組織の管理者によってアプリケーションに付与され、その組織と従業員が所有するデータにアクセスする場合にのみ使用されます。たとえば、Microsoft Graph は次のようなアクセス許可を公開しています:

- すべてのメールボックスのメールの読み取り
- すべてのメールボックスのメールの読み書き
- 任意のユーザーとしてのメールの送信
- ディレクトリ データの読み取り
- [その他](https://graph.microsoft.io)

アプリケーションでこれらのアクセス許可を取得するには、次の手順を実行します。

#### アプリケーション登録ポータルでアクセス許可を要求する

- [apps.dev.microsoft.com](https://apps.dev.microsoft.com) に移動して使用しているアプリケーションを選択するか、または、アプリを作成していない場合は [アプリを作成します](active-directory-v2-app-registration.md)。アプリケーションで少なくとも 1 つのアプリケーション シークレットが作成されていることを確認する必要があります。
- **アプリケーションの直接アクセス許可** セクションを検索し、アプリに必要なアクセス許可を追加します。
- アプリの登録を **保存**してください

#### アプリで、ユーザーのサインインを行うことを推奨します

通常、アプリケーションのアクセス許可を使用するアプリケーションを構築する場合は、アプリ側に管理者がアプリのアクセス許可を承認するページ/表示が必要となります。このページは、アプリケーションのサインアップ フロー、アプリの設定、専用の "接続" フローに関する内容を含みます。多くの場合、職場または学校の Microsoft アカウントでユーザーがサインインした後にのみ、”接続" ビューが表示されます。

ユーザーがアプリケーションにサインインすると、アプリケーションのアクセス許可の承認を依頼する前に、ユーザーが所属する組織を識別できます。必須ではありませんが、組織のユーザーに向けたより直観的なエクスペリエンスの作成に役立ちます。ユーザーのサインインを行うには、[v2.0 プロトコル チュートリアル](active-directory-v2-protocols.md)に従ってください。

#### ディレクトリ管理者にアクセス許可を要求する

会社の管理者にアクセス許可を要求する準備ができたら、ユーザーを v2.0 **管理者の同意エンドポイント**にリダイレクトできます。

```
// Line breaks for legibility only

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissons
```

```
// Pro Tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| パラメーター | | Description |
| ----------------------- | ------------------------------- | --------------- |
| テナント | 必須 | アクセス許可を要求するディレクトリ テナント。Guid またはフレンドリ名の形式で指定できます。ユーザーが所属するテナントがわからず、任意のテナントでサインインを行う場合は、`common` を使用します。 |
| client\_id | 必須 | 登録ポータル ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) によってアプリに割り当てられたアプリケーション ID。 |
| redirect\_uri | 必須 | 処理するアプリのレスポンスの送信先となる redirect\_uri。ポータルで登録した redirect\_uri のいずれかと完全に一致させる必要があります (ただし、URL エンコードが必要であり、またその他のパスのセグメントがある場合があります)。 |
| state | 推奨 | 要求に含まれ、かつトークンの応答として返される値。任意の文字列を指定することができます。この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的に使用されます。 |

現時点で、Azure AD では、テナント管理者のみがサインインして、要求を完了することができます。管理者は、登録ポータルでユーザーが要求したすべての直接のアプリケーション アクセス許可への承認を求められます。

##### 成功応答
管理者がアプリケーション アクセス許可を承認すると、成功した応答は次のようになります:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| パラメーター | Description |
| ----------------------- | ------------------------------- | --------------- |
| テナント | アプリケーションが要求したアクセス許可を Guid 形式で付与するディレクトリ テナント。 |
| state | 要求に含まれ、かつトークンの応答として返される値。任意の文字列を指定することができます。この状態は、認証要求の前にアプリ内でユーザーの状態 (表示中のページやビューなど) に関する情報をエンコードする目的に使用されます。 |
| admin\_consent | `True` に設定されます。 |


##### エラー応答
管理者がアプリケーション アクセス許可を承認しない場合、失敗した応答は次のようになります:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| パラメーター | Description |
| ----------------------- | ------------------------------- | --------------- |
| error | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error\_description | エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |

エンドポイントをプロビジョニングしたアプリケーションから正常な応答を受信した場合、アプリは要求した直接のアプリケーション アクセス許可を獲得しています。目的のリソースのトークンの要求に移行できます。

## トークンを取得する
アプリケーションに必要な承認を獲得後、API のアクセス トークンの取得を開始できます。クライアント資格情報の付与を使用してトークンを取得するには、`/token` v2.0 エンドポイントに POST 要求を送信します:

```
POST /common/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| パラメーター | | Description |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | 必須 | 登録ポータル ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) によってアプリに割り当てられたアプリケーション ID。 |
| scope | 必須 | この要求の `scope` パラメーターに渡された値は、 `.default` サフィックスが付いた目的のリソースのリソース識別子 (アプリ ID URI) である必要があります。Microsoft Graph の例では、値は `https://graph.microsoft.com/.default` である必要があります。この値は、アプリ用に構成した直接のアプリケーション アクセス許可を v2.0 エンドポイントに通知し、目的のリソースに関連するトークンを発行します。 |
| client\_secret | 必須 | アプリの登録ポータルで作成した、アプリケーションのシークレット。 |
| grant\_type | 必須 | `client_credentials` である必要があります。 | 

#### 成功応答
成功応答は、次のような形式になります:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| パラメーター | Description |
| ----------------------- | ------------------------------- |
| access\_token | 要求されたアクセス トークン。アプリはこのトークンを使用して、保護されたリソース (Web API など) に対し、本人性を証明することができます。 |
| token\_type | トークン タイプ値を指定します。Azure AD でサポートされるのは `Bearer` のみです。 |
| expires\_in | アクセス トークンの有効期間 (秒)。 |

#### エラー応答
エラー応答は、次のような形式になります:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| パラメーター | Description |
| ----------------------- | ------------------------------- |
| error | 発生したエラーの種類を分類したりエラーに対処したりする際に使用するエラー コード文字列。 |
| error\_description | 認証エラーの根本的な原因を開発者が特定しやすいように記述した具体的なエラー メッセージ。 |
| error\_codes | 診断に役立つ STS 固有のエラー コードの一覧。 |
| timestamp | エラーが発生した時刻。 |
| trace\_id | 診断に役立つ、要求の一意の識別子。 |
| correlation\_id | コンポーネント間での診断に役立つ、要求の一意の識別子。 |

## トークンを使用する
トークンを獲得したら、そのトークンを使用してリソースへの要求を作成できます。トークンの有効期限が切れたときは、`/token` エンドポイントへの要求を繰り返し、新しいアクセス トークンを取得します。

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro Tip: Try the below command out! (but replace the token with your own)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## サンプル コード
管理者の同意エンドポイントを使用する client\_credentials 付与を実装するアプリケーションの例を参照するには、[v2.0 デーモンのコード サンプル](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)を参照してください。

<!---HONumber=AcomDC_0928_2016-->