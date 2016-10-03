<properties
	pageTitle="Azure AD v2.0 のスコープ、アクセス許可、および同意 | Microsoft Azure"
	description="スコープ、アクセス許可、同意など、Azure AD v2.0 エンドポイントでの承認の説明。"
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
	ms.date="09/16/2016"
	ms.author="dastrock"/>

# v2.0 エンドポイントのスコープ、アクセス許可、および同意

Azure AD と統合されるアプリでは、アプリがどのようにデータにアクセスできるかをユーザーが制御できるようにする、特定の承認モデルが使用されます。この承認モデルの v2.0 実装が更新され、アプリが Azure AD とやり取りする方法が変わりました。このトピックでは、スコープ、アクセス許可、同意など、この承認モデルの基本的な概念について説明します。

> [AZURE.NOTE]
	Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。v2.0 エンドポイントを使用する必要があるかどうかを判断するには、[v2.0 の制限事項](active-directory-v2-limitations.md)に関するページをお読みください。

## スコープとアクセス許可

Azure AD は、[OAuth 2.0](active-directory-v2-protocols.md) 承認プロトコルを実装しています。このプロトコルによって、ユーザーに代わってサード パーティのアプリが、Web でホストされるリソースにアクセスできるようになります。Azure AD と統合される、Web でホストされるすべてのリソースは、リソース識別子、つまり**アプリ ID URI** を持ちます。Microsoft の Web でホストされるリソースには、次のようなものがあります。

- Office 365 統合メール API: `https://outlook.office.com`
- Azure AD Graph API: `https://graph.windows.net`
- Microsoft Graph: `https://graph.microsoft.com`

Azure AD と統合されたサード パーティのリソースも同様です。これらのリソースのいずれでも、機能をより小さいまとまりに分割するために使用できるアクセス許可のセットを定義できます。たとえば、Microsoft Graph では次のようなアクセス許可が定義されています。

- ユーザーの予定表の読み取り
- ユーザーの予定表への書き込み
- ユーザーとしてのメールの送信
- [その他](https://graph.microsoft.io)

これらのアクセス許可を定義することで、リソースはデータを外部にどのように公開するかをきめ細かく制御できます。サード パーティのアプリは、エンド ユーザーにこれらのアクセス許可を要求します。アプリがユーザーに代わって動作するには、エンド ユーザーがアクセス許可を承認する必要があります。リソースの機能を細かいアクセス許可セットにまとめることによって、サード パーティ アプリは、機能を実行するために必要な特定のアクセス許可のみを要求するように構築できます。また、アプリが正確にはどのようにデータを使用するかをエンド ユーザーに知らせ、アプリが悪意のある動作をしないことを確信できるようにすることもできます。

Azure AD と OAuth では、これらのアクセス許可は**スコープ**と呼ばれます。**oAuth2Permissions** と呼ばれる場合もあります。スコープは、Azure AD では文字列値として表されます。Microsoft Graph の例では、各アクセス許可のスコープ値は次のとおりです。

- ユーザーの予定表の読み取り: `Calendar.Read`
- ユーザーの予定表への書き込み: `Mail.ReadWrite`
- ユーザーとしてのメールの送信: `Mail.Send`

アプリは、以下に示すように、v2.0 エンドポイントへの要求でスコープを指定することによって、これらのアクセス許可を要求できます。


## 同意

[OpenID Connect または OAuth 2.0](active-directory-v2-protocols.md) 承認要求では、アプリは `scope` クエリ パラメーターを使用して、必要なアクセス許可を要求できます。たとえば、ユーザーがアプリにサインインするときに、アプリは次のような要求を送信します (読みやすくするために、改行を入れています)。

```
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendar.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

`scope` パラメーターは、アプリが要求しているスコープの、空白文字で区切られた一覧です。個々のスコープは、リソースの識別子 (アプリ ID URI) にスコープ値を追加することによって示されます。上の要求では、ユーザーの予定表を読み取り、ユーザーとしてメールを送信するためのアクセス許可をアプリが必要としていることが示されています。

ユーザーが資格情報を入力すると、v2.0 エンドポイントが**ユーザーの同意**の一致するレコードを確認します。要求されたアクセス許可にユーザーがまだ同意したことがない場合、v2.0 エンドポイントは要求されたアクセス許可を付与するようにユーザーに求めます。

![職場アカウントの同意のスクリーン ショット](../media/active-directory-v2-flows/work_account_consent.png)

ユーザーがアクセス許可を承認すると、同意が記録され、ユーザーが以降のサインインで再度同意しなくても済むようになります。

## アクセス許可の使用

ユーザーがアプリのアクセス許可に同意したら、アプリは一定範囲でリソースにアクセスするためのアプリのアクセス許可を表すアクセス トークンを取得できます。特定のアクセス トークンは、1 つのリソースだけで使用できますが、その内部には、そのリソースに関してアプリに付与されたすべてのアクセス許可がエンコードされます。アクセス トークンを取得するために、アプリは v2.0 トークン エンドポイントへの次のような要求を作成できます。

```
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
	"grant_type": "authorization_code",
	"client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
	"scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
	"code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
	"redirect_uri": "https://localhost/myapp",
	"client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

結果として得られるアクセス トークンは、リソースへの HTTP 要求で使用され、アプリが特定のタスクを実行するための適切なアクセス許可を持っていることをリソースに示します。

OAuth 2.0 プロトコルとアクセス トークンの取得方法の詳細については、[v2.0 エンドポイント プロトコルのリファレンス](active-directory-v2-protocols.md)を参照してください。

## OpenID Connect のスコープ

OpenID Connect の v2.0 実装には、特定のリソースには適用されない、適切に定義されたスコープ `openid`、`email`、`profile`、`offline_access` があります。

#### OpenId

アプリは、[OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow) を使用してサインインを行う場合、`openid` スコープを要求する必要があります。`openid` スコープは、職場アカウントの同意画面では "サインイン" アクセス許可として表示され、個人用 Microsoft アカウントの同意画面では "プロフィールの表示と、Microsoft アカウントを使うアプリとサービスへの接続" アクセス許可として表示されます。このアクセス許可により、`sub` 要求の形式でユーザーの一意の識別子をアプリが受け取ることができるようになります。アプリがユーザー情報エンドポイントにアクセスすることも可能にします。`openid` スコープは、v2.0 トークン エンドポイントで id\_tokens を取得するためにも使用できます。このトークンは、アプリ内の異なるコンポーネント間の HTTP 呼び出しをセキュリティで保護するために使用できます。

#### 電子メール

`email` スコープは `openid` やその他のスコープに含めることができます。これにより、アプリがユーザーのプライマリ電子メール アドレスに `email` 要求の形式でアクセスできるようにします。電子メール アドレスがユーザー アカウントと関連付けられている場合のみ (常にではないが)、`email` 要求はトークンに含まれます。`email` スコープを使用する場合、トークンに `email` 要求が存在しないケースにも対応できるよう、アプリを準備する必要があります。

#### プロファイル

`profile` スコープは `openid` やその他のスコープに含めることができます。これで、アプリはユーザーのさまざまな情報にアクセスできるようになります。これらは、ユーザーの名前、姓、推奨ユーザー名、オブジェクト ID などですが、これに限定されるものではありません。id\_tokens で使用できる特定のユーザーに対するプロファイル要求の完全な一覧については、「[v2.0 トークンのリファレンス](active-directory-v2-tokens.md)」を参照してください。

#### Offline\_Access

[`offline_access` スコープ](http://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess)を使用すると、アプリはユーザーの代わりに、長期間にわたってリソースにアクセスできます。このスコープは、職場アカウントの同意画面には、"いつでもデータにアクセス" アクセス許可として表示されます。個人用 Microsoft アカウントの同意画面には、"いつでも情報にアクセス" アクセス許可として表示されます。ユーザーが `offline_access` スコープを承認すると、アプリは v2.0 トークン エンドポイントから更新トークンを取得できるようになります。更新トークンは有効期間が長いので、古いアクセス トークンの有効期限が切れたときに、アプリは新しいアクセス トークンを取得できます。

アプリが `offline_access` スコープを要求しない場合、refresh\_tokens を受け取ることはありません。つまり、[OAuth 2.0 承認コード フロー](active-directory-v2-protocols.md#oauth2-authorization-code-flow)の authorization\_code を使用すると、`/token` エンドポイントから access\_token だけが取得されます。その access\_token は、短時間 (通常は 1 時間) 有効ですが、最終的には期限が切れます。その時点で、アプリはユーザーを `/authorize` エンドポイントにリダイレクトして、新しい authorization\_code を取得する必要があります。このリダイレクト中に、アプリの種類によっては、ユーザーが資格情報を再入力したり、アクセス許可に再同意したりする必要がある場合もあります。

更新トークンの取得方法と使用方法の詳細については、「[v2.0 プロトコルのリファレンス](active-directory-v2-protocols.md)」を参照してください。

<!---HONumber=AcomDC_0921_2016-->