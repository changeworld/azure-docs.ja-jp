<properties
	pageTitle="v2.0 エンドポイントの制限事項と制約事項 |Microsoft Azure"
	description="Azure AD v2.0 エンドポイントに関する制限事項と制約事項のリスト"
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

# v2.0 エンドポイントの使用が適しているかどうかを判断するには

Azure Active Directory と統合されるアプリケーションを構築する場合は、v2.0 エンドポイントと認証プロトコルがニーズを満たすかどうかを判断する必要があります。元の Azure AD アプリ モデルは引き続き完全にサポートされ、いくつかの点では v2.0 よりも機能が豊富です。ただし、v2.0 エンドポイントには、ユーザーに新しいプログラミング モデルを使用してもらおうと考える開発者にとって[大きな利点](active-directory-v2-compare.md)があります。v2.0 は今後、Azure AD のすべての機能を網羅するように拡張され、v2.0 エンドポイントのみを使用すればよくなる予定です。

現時点では、v2.0 エンドポイントを使用して、次の 2 つの主要機能を実現できます。

- ユーザーが個人アカウントや職場アカウントでサインインできるようにする。
- [集約された Outlook API](https://dev.outlook.com) を呼び出す。

どちらの機能も、Web アプリケーション、モバイル アプリケーション、PC アプリケーションに実装できます。このような比較的限られた機能がアプリケーションにとって有用であれば、v2.0 エンドポイントを使用することをお勧めします。Microsoft サービスの他の機能がアプリケーションに必要な場合は、Azure AD の実証済みのエンドポイントと Microsoft アカウントを引き続き使用することをお勧めします。将来的には、v2.0 エンドポイントが Azure AD と Microsoft アカウントの両方の上位セットになり、すべての開発者が v2.0 エンドポイントに移行できるようにサポートが提供される予定です。

この記事には、それまでの間も v2.0 エンドポイントがニーズを満たすかを判断する際に役立つ情報が記載されています。記事の内容は、v2.0 エンドポイントの最新状態を反映するために今後継続的に更新されるため、定期的にこのページをチェックし、v2.0 の機能に照らして要件を再評価してください。

v2.0 エンドポイントを使用していない Azure AD と既存のアプリケーションが統合されている場合も、ゼロから始める必要はありません。将来的に、既存の Azure AD アプリケーションを v2.0 エンドポイントと共に使用する方法が提供される予定です。

## アプリに関する制限事項
次の種類のアプリは、現時点では、v2.0 エンドポイントでサポートされていません。サポートされているアプリのタイプについては、[こちらの記事](active-directory-v2-flows.md)を参照してください。

##### スタンドアロン Web API
v2.0 エンドポイントでは、[OAuth 2.0 を使用して保護された Web API を作成](active-directory-v2-flows.md#web-apis)できます。ただし、その Web API は、同じアプリケーション ID を共有するアプリケーションからしかトークンを受け取ることができません。アプリケーション ID が異なるクライアントからアクセスされる Web API を作成することはできません。そのようなクライアントは、Web API へのアクセス許可を要求することも取得することもできません。

同じアプリ ID を持つクライアントからのトークンを受け付ける Web API の作成方法については、[こちら](active-directory-appmodel-v2-overview.md#getting-started)のセクションに掲載されている v2.0 エンドポイントの Web API サンプルを参照してください。

##### デーモン/サーバー サイド アプリ
長時間実行されるプロセスを含んだアプリや、ユーザーの介入なしで動作するアプリも、セキュリティで保護されたリソース (Web API など) にアクセスする必要があります。これらのアプリは、OAuth 2.0 Client Credentials Flow で (ユーザーの委任 ID ではなく) アプリの ID を使用して認証を行い、トークンを取得することができます。

v2.0 エンドポイントでは、このフローが現在サポートされていません。つまり、アプリがトークンを取得できるのは、対話的なユーザー サインイン フローの後に限られます。近い将来、クライアント資格情報フローが追加されます。元の Azure AD エンドポイントを使用したクライアント資格情報フローについては、[GitHub のデーモン サンプル](https://github.com/AzureADSamples/Daemon-DotNet)を参照してください。

##### Web API の On-Behalf-Of フロー
v2.0 エンドポイントによって保護された Web API から、同じように保護された別の下流の Web API を呼び出す手法は、多くのアーキテクチャで使われています。このシナリオは、Web API バックエンドを持つネイティブ クライアントでよく見られます。この Web API から Microsoft オンライン サービスや、Azure AD をサポートしている別のカスタム ビルド Web API が呼び出されます。

このようなシナリオは、OAuth 2.0 Jwt Bearer Credential Grant (On-Behalf-Of フロー) を使用してサポートすることができますが、現時点では、v2.0 エンドポイントでは On-Behalf-Of フローをサポートしていません。一般提供版 Azure AD サービスにおけるこのフローの動作については、[GitHub の On-Behalf-Of コード サンプル](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet)を参照してください。

## アプリの登録に関する制限事項
現時点では、v2.0 エンドポイントに統合するアプリはすべて、[apps.dev.microsoft.com](https://apps.dev.microsoft.com) で新しいアプリ登録を作成する必要があります。既存の Azure AD アプリまたは Microsoft アカウント アプリは、v2.0 エンドポイントとの互換性がありません。新しいアプリケーション登録ポータル以外のポータルで登録されたアプリも同様です。今後、既存のアプリを v2.0 アプリとして使用する方法を提供する予定ですが、現時点では、アプリを v2.0 エンドポイントに移行する方法はありません。

同様に、新しいアプリケーション登録ポータルで登録されたアプリは、元の Azure AD 認証エンドポイントに対して機能しません。ただし、アプリケーション登録ポータルで作成されたアプリを使用して、Microsoft アカウント認証エンドポイント `https://login.live.com` と統合することができます。

現在、新しいアプリケーション登録ポータルで登録されるアプリは、ごく限られた redirect\_uri 値に限定されています。Web アプリと Web サービスの redirect\_uri はスキーム (`https`) で始める必要があります。それ以外のすべてのプラットフォームの redirect\_uri は、`urn:ietf:oauth:2.0:oob` という値をハードコーディングする必要があります。

## リダイレクト URI に関する制限事項
Web アプリでは、すべての redirect\_uri 値で 1 つの DNS ドメインを共有する必要があります。たとえば、以下の redirect\_uri を持つ Web アプリを登録することはできません。

`https://login-east.contoso.com` `https://login-west.contoso.com`

登録システムによって、既存の redirect\_uri の DNS 名全体と、追加する redirect\_uri の DNS 名が比較されます。新しい redirect\_uri の DNS 名全体が既存の redirect\_uri の DNS 名と完全に一致せず、既存の redirect\_uri のサブドメインでもない場合、追加要求は失敗します。たとえば、現在アプリには次の redirect\_uri が含まれているとします。

`https://login.contoso.com`

この場合、DNS 名が完全に一致する

`https://login.contoso.com/new`

または、login.contoso.com の DNS サブドメインである

`https://new.login.contoso.com`

の追加は可能です。redirect\_uri として login-east.contoso.com と login-west.contoso.com を持つアプリが必要な場合は、以下の redirect\_uri を上から順番に追加する必要があります。

`https://contoso.com` `https://login-east.contoso.com` `https://login-west.contoso.com`

後の 2 つの redirect\_uri を追加できるのは、それらが 1 つ目の contoso.com のサブドメインであるためです。この制限は、今後のリリースで解消される予定です。

新しいアプリケーション登録ポータルでアプリを登録する方法については、[こちらの記事](active-directory-v2-app-registration.md)を参照してください。

## サービスと API に関する制限事項
v2.0 エンドポイントは現在、[サポートされる認証フロー](active-directory-v2-flows.md)のリストに該当することを条件に、新しいアプリケーション登録ポータルで登録されたアプリのサインインをサポートしています。ただしこれらのアプリで取得できるのは、ごく限られたリソースの OAuth 2.0 アクセス トークンだけです。v2.0 エンドポイントから発行される access\_tokens の対象は次のいずれかに限定されます。

- トークンを要求したアプリ。論理的に複数の異なるコンポーネントまたは階層で構成されている場合にアプリが取得できるのはそれ自身の access\_token となります。その実際のシナリオについては、[こちら](active-directory-appmodel-v2-overview.md#getting-started)のチュートリアルを参照してください。
- Outlook メール、カレンダー、連絡先の REST API (いずれも https://outlook.office.com に存在)。これらの API にアクセスするアプリの作成方法については、[Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) に関するチュートリアルを参照してください。
- Microsoft Graph API。Microsoft Graph と使用可能なすべてのデータの詳細については、[https://graph.microsoft.io](https://graph.microsoft.io) を参照してください。

現時点では、上記以外のサービスはサポートされていません。その他の Microsoft オンライン サービスと、独自のカスタム ビルド Web API やサービスについても、将来サポートが追加される予定です。

## ライブラリと SDK に関する制限事項
さまざまな方法を試すことができるように、v2.0 エンドポイントと互換性のある Active Directory 認証ライブラリ (ADAL) の試験版が用意されています。ただし、このバージョンの ADAL はプレビュー段階のためサポート対象外で、今後数か月の間に大幅に変更される予定です。v2.0 エンドポイントで動作するアプリがすぐに必要な場合は、.NET、iOS、Android、および Javascript 向けの ADAL を使用したコード サンプルが「[Getting Started (概要)](active-directory-appmodel-v2-overview.md#getting-started)」セクションに掲載されています。

運用環境のアプリケーションで v2.0 エンドポイントを使用する場合、次のような選択肢があります。

- Web アプリケーションを構築する場合は、マイクロソフトが一般提供しているサーバー側のミドルウェアを使用して、サインインとトークンの検証を安全に実行できます。このようなミドルウェアには、OWIN の ASP.NET 用 OpenID Connect ミドルウェアやマイクロソフトの NodeJS 用 Passport プラグインなどがあります。これらのミドルウェアを使用したコード サンプルについては、「[Getting Started (概要)](active-directory-appmodel-v2-overview.md#getting-started)」セクションを参照してください。
- 他のプラットフォームやネイティブ アプリケーションとモバイル アプリケーションでは、アプリケーション コードで直接プロトコル メッセージを送受信することで v2.0 エンドポイントと統合することもできます。v2.0 の OpenID Connect プロトコルと OAuth プロトコルについては、このような統合の実行に役立つように、[明確に文書化](active-directory-v2-protocols.md)されています。
- オープン ソースの Open ID Connect および OAuth のライブラリを使用して、v2.0 エンドポイントと統合できます。v2.0 のプロトコルは通常、大幅な変更を加えなくても、多数のオープン ソース プロトコル ライブラリと互換性があります。このようなライブラリを使用できるかどうかは、言語やプラットフォームごとに異なります。[Open ID Connect](http://openid.net/connect/) および [OAuth 2.0](http://oauth.net/2/) の Web サイトに、一般的な実装のリストが掲載されています。以下に、v2.0 エンドポイントでテスト済みのオープン ソース クライアント ライブラリとサンプルを示します。

  - [Java WSO2 ID サーバー](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server)
  - [Java Gluu フェデレーション](https://github.com/GluuFederation/oxAuth)
  - [Node.Js Passport OpenID Connect](https://www.npmjs.com/package/passport-openidconnect)
  - [PHP OpenID Connect の基本的なクライアント](https://github.com/jumbojett/OpenID-Connect-PHP)
  - [iOS OAuth2 クライアント](https://github.com/nxtbgthng/OAuth2Client)
  - [Android OAuth2 クライアント](https://github.com/wuman/android-oauth-client)
  - [Android OpenID Connect クライアント](https://github.com/kalemontes/OIDCAndroidLib)

## プロトコルに関する制限事項
v2.0 エンドポイントは、Open ID Connect と OAuth 2.0 のみをサポートしています。ただし、各プロトコルの一部の機能がまだ v2.0 エンドポイントには組み込まれていません。次に例をいくつか示します。

- OpenID Connect `end_sesssion_endpoint`
- OAuth 2.0 クライアント資格情報付与

v2.0 エンドポイントでサポートされているプロトコル機能の範囲について詳しく理解するには、[OpenID Connect および OAuth 2.0 プロトコルに関するリファレンス](active-directory-v2-protocols.md)を参照してください。

## Azure AD 開発者向けの高度な機能
Azure Active Directory サービスで利用できる開発者向け機能の中には、まだ v2.0 エンドポイントではサポートされていないものがあります。具体的には、次のような機能です。

- Azure AD ユーザーのグループ要求
- アプリケーション ロールとロール要求

<!---HONumber=AcomDC_0921_2016-->