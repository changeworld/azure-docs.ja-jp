<properties
	pageTitle="Azure AD B2C プレビュー | Microsoft Azure"
	description="Azure AD B2C プレビューで発行されるトークンの種類。"
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
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Azure AD B2C Preview: トークン リファレンス

Azure AD B2C は、各[認証フロー](active-directory-b2c-apps.md)の処理において複数の種類のセキュリティ トークンを出力します。このドキュメントでは、各トークンの種類の形式、セキュリティ特性、内容について説明します。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## トークンの種類

Azure AD B2C は [OAuth 2.0 承認プロトコル](active-directory-b2c-reference-protocols.md)をサポートしており、access\_tokens と refresh\_tokens の両方を利用します。また、[OpenID Connect](active-directory-b2c-reference-protocols.md) による認証とサインインもサポートしており、これによって 3 番目のトークンの種類 id\_token が導入されます。これらの各トークンは、「ベアラー トークン」として表されます。

ベアラー トークンは、保護されたリソースへの "ベアラー" アクセスを許可する簡易セキュリティ トークンです。この意味で、"ベアラー" はトークンを提示できる任意の利用者を表します。利用者がベアラー トークンを受信するには、まず Azure AD による認証が必要となりますが、転送中や保存時にトークンを保護するために必要な対策を講じていない場合、意図しない利用者によって傍受され、使用されるおそれがあります。一部のセキュリティ トークンには、許可されていない利用者がトークンを使用できないようにするための組み込みメカニズムがありますが、ベアラー トークンにはこのメカニズムがないため、トランスポート層セキュリティ (HTTPS) などのセキュリティで保護されたチャネルで転送する必要があります。ベアラー トークンが暗号化されずに転送された場合、悪意のある利用者が中間者攻撃によってトークンを取得し、保護されたリソースへの未承認のアクセスに使用する可能性があります。後で使用するためにベアラー トークンを保存またはキャッシュするときにも、同じセキュリティ原則が適用されます。アプリケーションでは、常に安全な方法でベアラー トークンを転送および保存してください。ベアラー トークンのセキュリティに関する考慮事項の詳細については、[RFC 6750 セクション 5](http://tools.ietf.org/html/rfc6750) をご覧ください。

Azure AD B2C によって発行されるトークンの多くは、JSON Web トークン (JWT) として実装されます。JWT は、2 つのパーティ間で情報を転送する、コンパクトで URL の安全な手段です。JWT に含まれる情報は「要求」と呼ばれ、トークンのベアラーとサブジェクトに関する情報のアサーションです。JWT の要求は、伝送用にエンコードおよびシリアル化された JSON オブジェクトです。Azure AD B2C によって発行される JWT は署名されますが、暗号化されないため、デバッグの目的で JWT の内容を簡単に検査できます。そのためには、[calebb.net](https://calebb.net) などの複数のツールを利用できます。JWT の詳細については、[JWT の仕様](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html)を参照してください。

## id\_token

id\_token は、アプリが Azure AD B2C の `authorize` および `token` エンドポイントから受け取るセキュリティ トークンの形式です。これらは [JWT](#types-of-tokens) として表され、アプリにユーザーを示すために使用できるクレームが含まれます。`authorize` エンドポイントから取得した id\_token は、Web アプリケーションへのユーザーのサインインによく使用されます。`token` エンドポイントから取得した id\_token は、同じアプリケーションまたはサービスの 2 つのコンポーネント間で通信するときに HTTP 要求に入れて送信されます。必要に応じて id\_token でクレームを使用できます。一般には、アカウント情報の表示や、アプリ内でのアクセス制御の決定に使用されます。

id\_token は署名されますが、この時点では暗号化されません。アプリまたは API は、id\_token を受け取ったら、[署名を検証](#validating-tokens)してトークンの信頼性を確認し、クレームを検証してトークンの有効性を確認する必要があります。アプリで検証するクレームはシナリオの要件によって異なりますが、すべてのシナリオでアプリが実行する必要がある[共通のクレーム検証](#validating-tokens)がいくつかあります。

以下では、id\_token のクレームの詳細および id\_token のサンプルを示します。id\_token 内のクレームは特定の順序では返されないことに注意してください。さらに、随時、新しいクレームが id\_token に導入される可能性があります。アプリは、新しいクレームが導入されても問題ないようにする必要があります。次の一覧のクレームは、この記事の執筆時点で、アプリで解釈できることが保証されているものです。実際には、[calebb.net](https://calebb.net) に貼り付けることによって、サンプル id\_token 内の要求を調べてみてください。必要な場合は、[OpenID Connect の仕様](http://openid.net/specs/openid-connect-core-1_0.html)でさらに詳細な情報を参照できます。

#### id\_token のサンプル
```
// Line breaks for display purposes only

eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IklkVG9rZW5TaWduaW5nS2V5Q29udGFpbmVyIn0.
eyJleHAiOjE0NDIzNjAwMzQsIm5iZiI6MTQ0MjM1NjQzNCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly9s
b2dpbi5taWNyb3NvZnRvbmxpbmUuY29tLzc3NTUyN2ZmLTlhMzctNDMwNy04YjNkLWNjMzExZjU4ZDkyNS92
Mi4wLyIsImFjciI6ImIyY18xX3NpZ25faW5fc3RvY2siLCJzdWIiOiJOb3Qgc3VwcG9ydGVkIGN1cnJlbnRs
eS4gVXNlIG9pZCBjbGFpbS4iLCJhdWQiOiI5MGMwZmU2My1iY2YyLTQ0ZDUtOGZiNy1iOGJiYzBiMjlkYzYi
LCJpYXQiOjE0NDIzNTY0MzQsImF1dGhfdGltZSI6MTQ0MjM1NjQzNCwiaWRwIjoiZmFjZWJvb2suY29tIn0.
h-uiKcrT882pSKUtWCpj-_3b3vPs3bOWsESAhPMrL-iIIacKc6_uZrWxaWvIYkLra5czBcGKWrYwrAC8ZvQe
DJWZ50WXQrZYODEW1OUwzaD_I1f1HE0c2uvaWdGXBpDEVdsD3ExKaFlKGjFR2V7F-fPThkVDdKmkUDQX3bVc
yyj2V2nlCQ9jd7aGnokTPfLfpOjuIrTsAdPcGpe5hfSEuwYDmqOJjGs9Jp1f-eSNEiCDQOaTBSvr479L5ptP
XWeQZyX2SypN05Rjr05bjZh3j70ZUimiocfJzjibeoDCaQTz907yAg91WYuFOrQxb-5BaUoR7K-O7vxr2M-_
CQhoFA

```

#### id\_token 内のクレーム

Azure AD B2C では、トークンの内容を細かく制御できます。アプリの操作に必要な特定のユーザー データのセットをクレームに入れて送信するように、[ポリシー](active-directory-b2c-reference-policies.md)を構成できます。これらのクレームは、ユーザーの `displayName` や `emailAddress` などの標準プロパティだけでなく、B2C ディレクトリで定義できる[カスタム ユーザー属性](active-directory-b2c-reference-custom-attr)も含むことができます。ただし、受け取るすべての id\_token には特定のセキュリティ関連クレームのセットが格納されており、アプリケーションではそれを使用してユーザーと要求を安全に認証できます。以下で示すのは、Azure AD B2C によって発行されるすべての id\_token に存在するものと考えられるクレームです。これら以外のクレームはすべてポリシーによって決定されます。

| 名前 | クレーム | 値の例 | 説明 |
| ----------------------- | ------------------------------- | ------------ | --------------------------------- |
| 対象となる読者 | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | トークンの受信者を示します。id\_token では、受信者はアプリ登録ポータルでアプリに割り当てられるアプリのアプリケーション ID です。アプリでは、この値を検証し、一致しない場合はトークンを拒否する必要があります。 |
| Issuer | `iss` | `https://login.microsoftonline.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | トークンを作成して返した Security Token Service (STS)、およびユーザーが認証された Azure AD ディレクトリを示します。アプリでは、発行者クレームを検証し、トークンが v2.0 エンドポイントからのものであることを確認する必要があります。 |
| 発行時刻 | `iat` | `1438535543` | トークンが発行された日時です。エポック時間で表されます。 |
| 期限切れ日時 | `exp` | `1438539443` | トークンが無効になる日時です。エポック時間で表されます。アプリでは、このクレームを使用してトークンの有効期間の有効性を確認する必要があります。 |
| 期間の開始時刻 | `nbf` | `1438535543` | トークンが有効になる日時です。エポック時間で表されます。これは通常、発行日時と同じです。アプリでは、このクレームを使用してトークンの有効期間の有効性を確認する必要があります。 |
| バージョン | `ver` | `1.0` | id\_token のバージョンです。Azure AD で定義されています。 |
| コード ハッシュ | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | コード ハッシュは、id\_token が OAuth 2.0 認証コードと共に発行される場合にのみ、id\_token に含まれます。これを使用して、認証コードの信頼性を検証できます。この検証の実行の詳細については、[OpenID Connect の仕様](http://openid.net/specs/openid-connect-core-1_0.html)を参照してください。 |
| アクセス トークン ハッシュ | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | アクセス トークン ハッシュは、id\_token が OAuth 2.0 アクセス トークンと共に発行される場合にのみ、id\_token に含まれます。これを使用して、アクセス トークンの信頼性を検証できます。この検証の実行の詳細については、[OpenID Connect の仕様](http://openid.net/specs/openid-connect-core-1_0.html)を参照してください。 |
| nonce | `nonce` | `12345` | nonce は、トークンのリプレイ攻撃を緩和するための戦略です。アプリでは、`nonce` クエリ パラメーターを使用して、承認要求で nonce を指定できます。要求で指定した値は、id\_token の `nonce` 要求で変更されずに出力されます。これにより、アプリではこの値を要求で指定した値と比較して検証できます。この値は、アプリのセッションと特定の id\_token を関連付けます。アプリでは、id\_token 検証プロセスの間にこの検証を実行する必要があります。 |
| [件名] | `sub` | `Not supported currently. Use oid claim.` | トークンが情報をアサートするプリンシパルです (アプリのユーザーなど)。この値は変更不可で、再割り当ても再利用もできません。したがってこの値を使用すると、トークンを使用してリソースにアクセスする場合などに安全に承認チェックができます。ただし、サブジェクト クレームは Azure AD B2C プレビューではまだ実装されていません。サブジェクト クレームを使用して承認するのではなく、オブジェクト ID の `oid` クレームを含むようにポリシーを構成し、ユーザーの識別にはその値を使用する必要があります。 |
| 認証コンテキスト クラスの参照 | `acr` | `b2c_1_sign_in` | id\_token の取得に使用されたポリシーの名前です。 |
| 認証時刻 | `auth_time | `1438535543` | ユーザーが資格情報を最後に入力した時刻 (エポック時間) です。 |



## アクセス トークン

現時点では、Azure AD B2C はアクセス トークンを発行しません。この初期プレビュー段階では、2 つの異なるパーティ間の認証はサポートされていません。ただし、id\_token は同じアプリケーションのコンポーネント間の通信に使用できます。Azure AD B2C パブリック プレビューでサポートされているアプリケーションおよび認証シナリオについては、[B2C アプリに関する記事](active-directory-b2c-apps.md)をご覧ください。

## 更新トークン

更新トークンは、OAuth 2.0 のフローで新しい id\_token と access\_token を取得するためにアプリで使用できるセキュリティ トークンです。ユーザーが介入しなくても、アプリはユーザーに代わってリソースへの長期的なアクセスを実現できます。

トークン応答で更新を受け取るためには、アプリは `offline_acesss` スコープを要求する必要があります。`offline_access` スコープの詳細については、[Azure AD B2C のプロトコル リファレンス](active-directory-b2c-reference-protocols.md)をご覧ください。

更新トークンは、現在も将来的にも常に、アプリに対して完全に非透過的です。Azure AD によって発行され、Azure AD によってのみ検査および解釈できます。有効期間は長いですが、アプリを作成するときに更新トークンが一定期間残っているものと期待することはできません。更新トークンは、いつでもさまざまな理由で無効になる可能性があります。アプリで更新トークンが有効かどうかを知る唯一の方法は、Azure AD に対してトークン要求を行って更新トークンを利用することです。

新しいトークンに対して更新トークンを利用すると (そして、アプリが `offline_access` スコープを許可されている場合は)、トークン応答で新しい更新トークンを受け取ります。新しく発行された更新トークンを保存し、要求で前に使用したものと置き換える必要があります。これにより、可能な限り長く更新トークンが有効であることが保証されます。

## トークンの検証

現時点で、アプリにおいて実行する必要があるトークンの検証は、id\_token の検証だけです。id\_token を検証するには、アプリは id\_token の署名と id\_token 内のクレームの両方を検証する必要があります。

<!-- TODO: Link -->
優先言語にもよりますが、JWT を検証するためのさまざまなオープンソース ライブラリを入手できます。独自の検証ロジックを実装するより、オープンソース ライブラリを試してみることをお勧めします。ライブラリを正しく利用する方法を模索するとき、ここにある情報が役に立つでしょう。

#### 署名の検証
JWT には 3 つのセグメントがあり、`.` 文字で区切られています。1 番目のセグメントは**ヘッダー**、2 番目は**本文**、3 番目は**署名**と呼ばれます。署名セグメントを使用して id\_token の信頼性を検証し、アプリで信頼できることを確認できます。

id\_token は、RSA 256 などの業界標準の非対称暗号アルゴリズムを使用して署名されます。id\_token のヘッダーには、トークンの署名に使用されたキーと暗号方法に関する情報が含まれます。

```
{
		"typ": "JWT",
		"alg": "RS256",
		"kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

`alg` クレームはトークンの署名に使用されたアルゴリズムを示し、`kid` または `x5t` クレームはトークンの署名に使用された特定の公開キーを示します。

いつでも、Azure AD は公開/秘密キー ペアの特定セットのいずれかを使用して、id\_token に署名できます。Azure AD は定期的に使用可能なキー セットをローテーションするので、このキー変更を自動的に処理するようにアプリを作成する必要があります。Azure AD によって使用される公開キーの更新を確認する適切な頻度は、約 24 時間です。

Azure AD B2C には、OpenID Connect メタデータ エンドポイントがあって、アプリは、このエンドポイントを使用することで、Azure AD B2C に関する情報を実行時にフェッチすることができます。この情報には、エンドポイント、トークンの内容、トークンの署名キーが含まれます。B2C ディレクトリにはポリシー別の JSON メタデータ ドキュメントがあります。たとえば、`fabrikamb2c.onmicrosoft.com` の `b2c_1_sign_in` ポリシーのメタデータ ドキュメントは次の場所にあります。

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in`

署名の検証に必要な署名キー データは、次の場所にある OpenID Connect メタデータのドキュメントを使用して入手できます。

```
https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_sign_in
```

`fabrikamb2c.onmicrosoft.com` はユーザーの認証に使用される B2C ディレクトリ、`b2c_1_sign_in` は id\_token の取得に使用されるポリシーです。id\_token の署名で使用されたポリシー (とメタデータのフェッチ場所) は 2 つの方法で判断できます。最初の方法です。ポリシー名が id\_token の `acr` クレームに含まれています。本文を Base 64 でデコードし、結果の JSON 文字列を逆シリアル化することによって、JWT の本文からクレームを解析できます。`acr` クレームは、id\_token の発行に使用されたポリシーの名前です。もう 1 つの方法です。要求を発行するとき、`state` パラメーターの値に含まれるポリシーを符号化し、それから復号化して使用されたポリシーを判断します。いずれの方法も完全に有効です。

メタデータ ドキュメントは、OpenID Connect 認証の実行に必要なさまざまなエンドポイントの場所など、役に立つ情報を含む JSON オブジェクトです。また、トークンの署名に使用される公開キーのセットの場所を示す `jwks_uri` も含まれます。次に示すのがその場所ですが、メタデータ ドキュメントを使用して `jwks_uri` を解析することにより動的にその場所をフェッチするのが最善の方法です。

`https://login.microsoftonline.com/fabrikamb2c.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_sign_in`

この URL に存在する JSON ドキュメントには、特定の時点で使用されているすべての公開キー情報が含まれます。アプリでは、`kid` または `x5t` 要求を JWT ヘッダーで使用して、特定のトークンの署名に使用されたこのドキュメント内の公開キーを選択できます。その後、正しい公開キーと指定されたアルゴリズムを使用して、署名の検証を実行できます。

署名の検証の実行は、このドキュメントの対象範囲外です。必要な場合は、役に立つオープン ソース ライブラリが数多く提供されています。

#### クレームの検証
アプリまたは API は、id\_token を受け取ったら、id\_token 内のクレームに対していくつかのチェックを実行する必要があります。学習した内容は次のとおりです。

- **受信者**クレーム - id\_token がそのアプリに対するものであることを検証します。
- **期間の開始時刻**および**期限切れ日時**クレーム - id\_token が期限切れでないことを検証します。
- **発行者**クレーム - トークンが Azure AD によってそのアプリに対して発行されたことを検証します。
- **Nonce** - トークン リプレイ攻撃を緩和するために検証します。

これらの要求に対して予期される値の詳細については、前記の [id\_token セクション](#id_tokens)を参照してください。

## トークンの有効期間

以下で示すトークンの有効期間は、アプリの開発およびデバッグで役に立つので、理解を深めるのためにのみ提供されています。これらの有効期間が一定で変わらないものと想定してアプリを作成しないでください。随時、変更される可能性があります。

| トークン | 有効期間 | 説明 |
| ----------------------- | ------------------------------- | ------------ |
| id\_token | 1 時間 | 通常、id\_token の有効期間は 1 時間です。Web アプリでは、この同じ有効期間を使用してユーザーとの独自のセッションを維持することも (推奨)、完全に異なるセッション有効期間を選択することもできます。アプリで新しい id\_token を取得する必要がある場合は、Azure AD に対して新しいサインイン要求を行うことだけが必要です。ユーザーに Azure AD との有効なブラウザー セッションがある場合は、資格情報を再度入力する必要はない場合があります。 |
| 更新トークン | 最大 14 日 | 1 つの更新トークンは、最大 14 日間有効です。ただし、更新トークンはさまざまな理由でいつでも無効になる可能性があるので、アプリでは、失敗するまで、または新しい更新トークンに置き換えるまで、更新トークンの使用を続行する必要がありますに。更新トークンは、ユーザーが自分の資格情報を入力してから 90 日経過した場合も無効になります。 |
| 承認コード | 5 分 | 承認コードは有効期間が意図的に短くされており、受け取ったらすぐに access\_token、id\_token、refresh\_token に利用する必要があります。 |

<!---HONumber=AcomDC_0128_2016-->