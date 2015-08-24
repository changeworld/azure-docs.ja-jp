<properties
	pageTitle="アプリ モデル v2.0 | Microsoft Azure"
	description="一般公開の Azure AD と v2.0 アプリ モデル パブリック プレユーの違いを比較しています。"
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

# アプリ モデル v2.0 プレビュー: 相違点

一般公開されている Azure AD サービスを使い慣れている場合、または以前にアプリを Azure AD に統合した場合は、v2.0 アプリ モデルで予想外の違いを体験する可能性があります。このドキュメントでは、それらの違いについて説明します。

> [AZURE.NOTE]この情報は、v2.0 アプリ モデルのパブリック プレビューに関するものです。一般公開されている Azure AD サービスと連携する手順については、「[Azure Active Directory 開発者ガイド](active-directory-developers-guide.md)」を参照してください。


## Microsoft アカウントと Azure AD アカウント
v2.0 アプリ モデルを使用すると、開発者は、1 つのエンドポイントを使用して Microsoft アカウントと Azure AD アカウントの両方からのサインインを受け付けるアプリを作成できます。これにより、まったくアカウントに依存しないアプリを作成できます。ユーザーがサインインにどのアカウントを使用するかを考慮する必要がありません。もちろん、特定のセッションで使用されるアカウントの種類を確認することは*できます*が、そうする必要はありません。

たとえば、アプリで [Office 365 の REST API](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) を呼び出す場合、エンタープライズ環境のユーザーは、SharePoint サイトや Directory データなどの追加の機能とデータを使用できます。ただし、[ユーザーのメールを読む](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)などの多くの操作について、Microsoft アカウントと Azure AD アカウントの両方に対して完全に同じコードを作成できます。

Microsoft アカウントと Azure AD アカウントのアプリを 1 つの単純なプロセスで統合できるようになりました。1 組のエンドポイント、1 つのライブラリ、1 回のアプリ登録によって、コンシューマー向けおよびエンタープライズ向けの両方の環境にアクセスできます。v2.0 アプリ モデル プレビューの詳細については、[概要](active-directory-appmodel-v2-overview.md)のページを参照してください。


## 新しいアプリ登録ポータル
v2.0 アプリ モデルでは、お使いの Microsoft アプリを新しい場所 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) で登録する必要があります。このポータルでは、アプリケーション ID の取得、アプリのサインイン ページの外観のカスタマイズなどを行うことができます。主にプレビュー期間中にお寄せいただくフィードバックに基づいて、このアプリ登録ポータルに多くの機能を継続的に追加していく予定です。このポータルは、Microsoft アプリに関連したあらゆる情報を一元管理できる新たな場所になります。

最大のメリットは、使用に Azure または Office のサブスクリプションが不要なことです。必要なものは、個人の Microsoft アカウントまたは職場や学校のアカウントだけです。

現時点では、新しいアプリ登録ポータルで登録したアプリは、アプリ モデル v2.0 でのみ動作します。また、新しいアプリ登録ポータルで登録したアプリ*のみ*が、アプリ モデル v2.0 で動作します。一般公開されている Microsoft アカウントまたは Azure AD サービスで、これらのアプリのいずれかを使用しようとしたとき、または v2.0 アプリ モデルで既存のアプリを使用しようとしたときに、非互換性の問題が発生する可能性があります。


## 1 つのアプリ ID ですべての製品に対応
GA の Azure AD サービスでは、1 つのプロジェクトに対して異なる複数のアプリを登録している場合があります。その場合、ネイティブ クライアントと Web アプリで別々にアプリ登録する必要がありました。

![古いアプリケーションの登録 UI](../media/active-directory-v2-flows/old_app_registration.PNG)

たとえば、Web サイトと iOS アプリの両方を構築した場合は、2 つの異なるアプリケーション ID を使用して、それぞれを別々に登録する必要がありました。Web サイトとバックエンド Web API を使用していた場合、Azure AD に別々のアプリとして登録していました。また、iOS アプリと Android アプリを使用していた場合は、2 つの異なるアプリを登録していました。

<!-- You may have even registered different apps for each of your build environments - one for dev, one for test, and one for production. -->

v2.0 では、各プロジェクトに対して 1 つのアプリケーション ID を使用して 1 回のアプリ登録を行うだけでかまいません。それぞれのプロジェクトに複数の "プラットフォーム" を追加し、追加した各プラットフォームに対して適切なデータを提供できます。もちろんアプリは要件に応じて必要な数だけ作成できますが、大半の場合、必要なアプリケーション ID は 1 つだけです。

<!-- You can also label a particular platform as "production-ready" when it is ready to be published to the outside world, and use that same Application Id safely in your development environments. -->

マイクロソフトが目指しているのは、アプリの管理と開発のエクスペリエンスをいっそう簡素化し、より高度に統合されたビューで作業中の各プロジェクトの状況を把握できるようにすることです。


## リソースではなくスコープ
Azure AD の一般公開サービスの場合、アプリは**リソース**として、またはトークンの受信者として動作できます。リソースには、リソースで識別できる多数の**スコープ**または **oAuth2Permissions** を定義できます。それによりクライアント アプリは、そのリソースに特定範囲のトークンを要求できます。リソースの例として、Azure AD Graph API があります。

- リソース識別子、または `AppID URI`:`https://graph.windows.net/`
- スコープ、または `OAuth2Permissions`:`Directory.Read`、`Directory.Write` などです。  

このすべてが v2.0 アプリ モデルに当てはまります。アプリはリソースとして動作し、スコープを定義することができ、URI によって識別できます。また、クライアント アプリは、スコープへのアクセスを要求できます。ただし、クライアントがアクセス許可を要求する方法が変更されました。以前は、Azure AD への OAuth 2.0 承認要求は、次のようになっていました。

```
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https%3A%2F%2Fgraph.windows.net%2F
...
```

ここで、**resource** パラメーターは、クライアント アプリが認証を求めている対象のリソースを示します。Azure AD は、Azure ポータルでの静的な構成に基づいて、アプリによって要求されたアクセス許可を計算し、適切なトークンを発行していました。現在は、同じ OAuth 2.0 承認要求が次のようになっています。

```
GET https://login.microsoftonline.com/common/v2.0/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

ここで、**scope** パラメーターは、アプリが承認を要求している対象のリソースとアクセス許可を示します。要求されているリソースはまだ要求内に多く存在し、scope パラメーターの各値に含まれています。このように scope パラメーターを使用すると、v2.0 アプリ モデルの OAuth 2.0 仕様への準拠を高め、一般的な業界の慣行に近づけることができます。また、アプリで[増分同意](#incremental-and-dynamic-consent)を実行できます。次のセクションでこれについて説明します。

## 増分および動的な同意
一般公開の Azure AD サービスに登録されたアプリは、作成時に Azure ポータルで、必要な OAuth 2.0 のアクセス許可を指定する必要がありました。

![アクセス許可の登録 UI](../media/active-directory-v2-flows/app_reg_permissions.PNG)

アプリが要求したアクセス許可は**静的に**構成されていました。これにより Azure ポータルにアプリの構成を存在させることができ、コードを適切に維持できる一方、開発者にとっては問題もいくつかあります。

- アプリの作成時に、そのアプリで必要になる可能性のあるすべてのアクセス許可がわかっている必要があります。後からアクセス許可を追加することは困難なプロセスでした。
- また、アプリでアクセスする可能性のあるすべてのリソースが事前にわかっている必要があります。任意の数のリソースにアクセスするアプリを作成することは、困難でした。
- アプリは、ユーザーの初回サインインの時点で、そのアプリで必要になる可能性があるすべてのアクセス許可を要求する必要があります。アクセス許可のリストは非常に長くなる場合があり、エンドユーザーが初回サインイン時にアプリのアクセスを承認する阻害要因となっていました。

v2.0 アプリ モデルでは、アプリの通常の使用状況で、アプリに必要なアクセス許可を実行時に**動的に**指定できます。これを行うには、任意の時点でアプリに必要なスコープを承認要求の `scope` パラメーターで指定できます。

```
GET https://login.microsoftonline.com/common/v2.0/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https%3A%2F%2Fgraph.windows.net%2Fdirectory.read%20https%3A%2F%2Fgraph.windows.net%2Fdirectory.write
...
```

この要求は、アプリで Azure AD ユーザーのディレクトリ データの読み取りおよび書き込みのためのアクセス許可を求めています。ユーザーが過去にこの特定のアプリでこれらのアクセス許可に同意した場合、資格情報を入力してアプリにサインインできます。ユーザーがこれらのアクセス許可のいずれにも同意していない場合、v2.0 エンドポイントはこれらのアクセス許可に対する同意をユーザーに求めます。詳細については、[アクセス許可、同意、スコープ](active-directory-v2-scopes.md)の説明を参照してください。

アプリで `scope` パラメーターを使用してアクセス許可を動的に要求できるため、ユーザーの操作を完全に制御できます。必要に応じて、同意の操作を初期段階に組み込み、1 回の初期認証要求ですべてのアクセス許可を求めます。または、アプリで多数のアクセス許可が必要な場合は、時間の経過と共に、ユーザーがアプリの特定の機能を使用するときにアクセス許可を集めることができます。

## オフライン アクセス
v2.0 アプリ モデルでは、アプリのために、`offline_access` スコープというよく知られた新しいアクセス許可を採用しています。すべてのアプリは、ユーザーがアプリを積極的に利用しない可能性がある場合でも、長期間ユーザーに代わってリソースにアクセスするために必要な、このアクセス許可を要求する必要があります。`offline_access` スコープは、コンテンツ ダイアログで "データにオフラインでアクセスします" と表示されます。`offline_access` アクセス許可を要求すると、Web アプリで v2.0 エンドポイントから OAuth 2.0 の refresh\_tokens を受け取ることができます。refresh\_tokens は、長期間維持され、アクセスの期間を延長するために新しい OAuth 2.0 の access\_tokens と交換することができます。

`offline_access` スコープを要求しないアプリは refresh\_tokens を受け取りません。つまり、[OAuth 2.0 承認コード フロー](active-directory-v2-protocols.md#oauth2-authorization-code-flow)の authorization\_code を使用すると、`/oauth2/token` エンドポイントから access\_token だけが取得されます。その access\_token は、短時間 (通常は 1 時間) 有効ですが、最終的には期限が切れます。その時点で、アプリはユーザーを `/oauth2/authorize` エンドポイントにリダイレクトして、新しい authorization\_code を取得する必要があります。このリダイレクト中に、アプリの種類によっては、ユーザーが資格情報を再入力したり、アクセス許可に再同意したりする必要がある場合もあります。

OAuth 2.0、refresh\_tokens、および access\_tokens の詳細については、「[v2.0 アプリ モデル プロトコル リファレンス](active-directory-v2-protocols.md)」を参照してください。

## トークン要求
v2.0 エンドポイントによって発行されたトークンでの要求は、一般公開の Azure AD エンドポイントによって発行されるトークンと同一にはなりません。新しいサービスに移行するアプリでは、id\_tokens または access\_tokens 内に特定の要求の存在を想定しないでください。v2.0 エンドポイントによって発行されたトークンは OAuth 2.0 および OpenID Connect 仕様に準拠していますが、一般公開の Azure AD サービスとは異なるセマンティクスに従っている可能性があります。

v2.0 アプリ モデル トークンによって発行される特定の要求の詳細については、[v2.0 トークン リファレンス](active-directory-v2-tokens.md)を参照してください。


## プレビューの制限事項
パブリック プレビュー期間中に v2.0 アプリ モデルを使用してアプリをビルドする際、注意が必要な制限事項がいくつかあります。それらの制限事項が実際のシナリオに当てはまるかどうかについては、「[v2.0 アプリ モデルの制限事項に関するドキュメント](active-directory-v2-limitations.md)を参照してください。

<!---HONumber=August15_HO7-->