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
    ms.date="09/30/2016"
    ms.author="dastrock"/>


# <a name="should-i-use-the-v2.0-endpoint?"></a>v2.0 エンドポイントの使用が適しているかどうかを判断するには

Azure Active Directory と統合されるアプリケーションを構築する場合は、v2.0 エンドポイントと認証プロトコルがニーズを満たすかどうかを判断する必要があります。  元の Azure AD エンドポイントは引き続き完全にサポートされ、いくつかの点では v2.0 よりも機能が豊富です。  ただし、v2.0 エンドポイントには、ユーザーに新しいプログラミング モデルを使用してもらおうと考える開発者にとって [大きな利点](active-directory-v2-compare.md) があります。

現時点では、Microsoft は次のような v2.0 エンドポイントの使用を推奨します:

- アプリケーションで個人の Microsoft アカウントをサポートする場合は、v2.0 エンドポイントを使用する必要があります。  ただし、この記事に記載される制限事項のリスト、特に職場または学校のアカウントに関連する制限事項をご確認ください。
- アプリケーションで職場または学校のアカウントのサポートのみが必要な場合は、[元の Azure AD エンドポイント](active-directory-developers-guide.md)を使用する必要があります。

今後、v2.0 エンドポイントはこの一覧に記載された制限事項をなくすように拡張され、v2.0 エンドポイントのみを使用すればよくなる予定です。  この記事には、それまでの間も v2.0 エンドポイントがニーズを満たすかを判断する際に役立つ情報が記載されています。  記事の内容は、v2.0 エンドポイントの最新状態を反映するために今後継続的に更新されるため、定期的にこのページをチェックし、v2.0 の機能に照らして要件を再評価してください。

v2.0 エンドポイントを使用していない Azure AD と既存のアプリケーションが統合されている場合も、ゼロから始める必要はありません。  将来的に、既存の Azure AD アプリケーションを v2.0 エンドポイントと共に使用する方法が提供される予定です。

## <a name="restrictions-on-apps"></a>アプリに関する制限事項
次の種類のアプリは、現時点では、v2.0 エンドポイントでサポートされていません。  サポートされているアプリのタイプについては、 [こちらの記事](active-directory-v2-flows.md)を参照してください。

##### <a name="standalone-web-apis"></a>スタンドアロン Web API
v2.0 エンドポイントでは、 [OAuth 2.0 を使用して保護された Web API を作成](active-directory-v2-flows.md#web-apis)できます。  ただし、その Web API は、同じアプリケーション ID を共有するアプリケーションからしかトークンを受け取ることができません。  アプリケーション ID が異なるクライアントからアクセスされる Web API を作成することはできません。  そのようなクライアントは、Web API へのアクセス許可を要求することも取得することもできません。

同じアプリ ID を持つクライアントからのトークンを受け付ける Web API の作成方法については、 [こちら](active-directory-appmodel-v2-overview.md#getting-started)に掲載されている v2.0 エンドポイントの Web API サンプルを参照してください。

##### <a name="web-api-on-behalf-of-flow"></a>Web API の On-Behalf-Of フロー
v2.0 エンドポイントによって保護された Web API から、同じように保護された別の下流の Web API を呼び出す手法は、多くのアーキテクチャで使われています。  このシナリオは、Web API バックエンドを持つネイティブ クライアントでよく見られます。この Web API から Microsoft オンライン サービスや、Azure AD をサポートしている別のカスタム ビルド Web API が呼び出されます。

このようなシナリオは、OAuth 2.0 Jwt Bearer Credential Grant (On-Behalf-Of フロー) を使用してサポートすることができますが、  現時点では、v2.0 エンドポイントでは On-Behalf-Of フローをサポートしていません。  一般提供版 Azure AD サービスにおけるこのフローの動作については、 [GitHub の On-Behalf-Of コード サンプル](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet)を参照してください。

## <a name="restrictions-on-app-registrations"></a>アプリの登録に関する制限事項
現時点では、v2.0 エンドポイントに統合するアプリはすべて、[apps.dev.microsoft.com](https://apps.dev.microsoft.com) で新しいアプリ登録を作成する必要があります。  既存の Azure AD アプリまたは Microsoft アカウント アプリは、v2.0 エンドポイントとの互換性がありません。新しいアプリケーション登録ポータル以外のポータルで登録されたアプリも同様です。  今後、既存のアプリを v2.0 アプリとして使用する方法を提供する予定です。 ただし、現時点では、アプリを v2.0 エンドポイントに移行する方法はありません。

同様に、新しいアプリケーション登録ポータルで登録されたアプリは、元の Azure AD 認証エンドポイントに対して機能しません。  ただし、アプリケーション登録ポータルで作成されたアプリを使用して、Microsoft アカウント認証エンドポイント `https://login.live.com`と統合することができます。

さらに、 [apps.dev.microsoft.com](https://apps.dev.microsoft.com) で作成されたアプリケーション登録には、次の注意点があります:

- **サインオン URL** とも呼ばれる**ホームページ**プロパティは、サポートされていません。  ホーム ページがない場合、Office MyApps パネルにこれらのアプリケーションは表示されません。
- この時点で、アプリケーション ID ごとに許可されるアプリ シークレットは 2 種類です。
- アプリの登録を表示および管理できるのは、1 人の開発者アカウントのみです。  複数の開発者間で共有することはできません。
- 許可される redirect_uri の形式には、いくつかの制限事項があります。  詳細については、次のセクションを参照してください。

## <a name="restrictions-on-redirect-uris"></a>リダイレクト URI に関する制限事項
現在、新しいアプリケーション登録ポータルで登録されるアプリは、ごく限られた redirect_uri 値に限定されています。  Web アプリと Web サービスの redirect_uri はスキーム `https` で始める必要があり、すべての redirect_rui 値で 1 つの DNS ドメインを共有する必要があります。  たとえば、以下の redirect_uri を持つ Web アプリを登録することはできません。

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

登録システムによって、既存の redirect_uri の DNS 名全体と、追加する redirect_uri の DNS 名が比較されます。 次のいずれかの条件に一致すると、DNS 名を追加する要求は失敗します:  

- 新しい redirect_uri の DNS 名全体が、既存の redirect_uri の DNS 名と一致しない場合
- 新しい redirect_uri の DNS 名全体が、既存の redirect_uri のサブドメインではない場合

たとえば、現在アプリには次の redirect_uri が含まれているとします。

`https://login.contoso.com`

この場合、DNS 名が完全に一致する

`https://login.contoso.com/new`

または、login.contoso.com の DNS サブドメインである

`https://new.login.contoso.com`

の追加は可能です。  redirect_uri として login-east.contoso.com と login-west.contoso.com を持つアプリが必要な場合は、以下の redirect_uri を上から順番に追加する必要があります。

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

後の 2 つの redirect_uri を追加できるのは、それらが 1 つ目の contoso.com のサブドメインであるためです。 この制限は、今後のリリースで解消される予定です。

新しいアプリケーション登録ポータルでアプリを登録する方法については、 [こちらの記事](active-directory-v2-app-registration.md)を参照してください。

## <a name="restrictions-on-services-&-apis"></a>サービスと API に関する制限事項
v2.0 エンドポイントは現在、 [サポートされる認証フロー](active-directory-v2-flows.md)のリストに該当することを条件に、新しいアプリケーション登録ポータルで登録されたアプリのサインインをサポートしています。  ただしこれらのアプリで取得できるのは、ごく限られたリソースの OAuth 2.0 アクセス トークンだけです。  v2.0 エンドポイントから発行される access_tokens の対象は次のいずれかに限定されます。

- トークンを要求したアプリ。  論理的に複数の異なるコンポーネントまたは階層で構成されている場合にアプリが取得できるのはそれ自身の access_token となります。  その実際のシナリオについては、 [こちら](active-directory-appmodel-v2-overview.md#getting-started) のチュートリアルを参照してください。
- Outlook メール、カレンダー、連絡先の REST API (いずれも https://outlook.office.com に存在)。  これらの API にアクセスするアプリの作成方法については、 [Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) に関するチュートリアルを参照してください。
- Microsoft Graph API。  Microsoft Graph と使用可能なすべてのデータの詳細については、 [https://graph.microsoft.io](https://graph.microsoft.io)を参照してください。

現時点では、上記以外のサービスはサポートされていません。  その他の Microsoft オンライン サービスと、独自のカスタム ビルド Web API やサービスについても、将来サポートが追加される予定です。

## <a name="restrictions-on-libraries-&-sdks"></a>ライブラリと SDK に関する制限事項
現時点では、v2.0 エンドポイントのライブラリー サポートには多くの制限があります。  運用環境のアプリケーションで v2.0 エンドポイントを使用する場合、次のような選択肢があります。

- Web アプリケーションを構築する場合は、マイクロソフトが一般提供しているサーバー側のミドルウェアを使用して、サインインとトークンの検証を安全に実行できます。  このようなミドルウェアには、OWIN の ASP.NET 用 OpenID Connect ミドルウェアやマイクロソフトの NodeJS 用 Passport プラグインなどがあります。  Microsoft のミドルウェアを使用したコード サンプルについては、「 [概要](active-directory-appmodel-v2-overview.md#getting-started) 」セクションを参照してください。
- 他のプラットフォームやネイティブ アプリケーションとモバイル アプリケーションでは、アプリケーション コードで直接プロトコル メッセージを送受信することで v2.0 エンドポイントと統合することもできます。  v2.0 の OpenID Connect プロトコルと OAuth プロトコルについては、このような統合の実行に役立つように、 [明確に文書化](active-directory-v2-protocols.md) されています。
- オープン ソースの Open ID Connect および OAuth のライブラリを使用して、v2.0 エンドポイントと統合できます。  v2.0 のプロトコルは通常、大幅な変更を加えなくても、多数のオープン ソース プロトコル ライブラリと互換性があります。  このようなライブラリを使用できるかどうかは、言語やプラットフォームごとに異なります。[Open ID Connect](http://openid.net/connect/) および [OAuth 2.0](http://oauth.net/2/) の Web サイトに、一般的な実装のリストが掲載されています。 詳細については、「[Azure Active Directory (AD) v2.0 と認証ライブラリ](active-directory-v2-libraries.md)」、および v2.0 エンドポイントでテスト済みのオープン ソース クライアント ライブラリとサンプルの一覧を参照してください。

Microsoft は、.NET 用 [Microsoft 認証ライブラリ (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) の初期プレビューもリリースしました。  このライブラリは .NET クライアントとサーバー アプリケーションで試用いただけますが、プレビュー ライブラリのため GA 品質のサポートはありません。

## <a name="restrictions-on-protocols"></a>プロトコルに関する制限事項
v2.0 エンドポイントは、Open ID Connect と OAuth 2.0 のみをサポートしています。  ただし、v2.0 エンドポイントには、次を含む各プロトコルの一部の機能がまだ組み込まれていません:

- v2.0 エンドポイントで、ユーザー側のセッションの終了をアプリに許可する OpenID Connect `end_session_endpoint`。
- v2.0 エンドポイントで発行される id_token には、ユーザーのペアワイズ識別子のみが含まれます。  これは、同一ユーザーに対して異なる 2 つのアプリケーションが異なる ID を受け取ることを意味します。  Microsoft Graph `/me` エンドポイントにクエリを実行すると、アプリケーション間で使用可能なユーザーの相関 ID を取得できます。
- ユーザーから電子メールを表示する許可を取得していても、現時点では v2.0 エンドポイントが発行する id_token にはユーザーの `email` の要求は含まれません。
- OpenID Connect ユーザー情報エンドポイント。 現時点では、v2.0 エンドポイントにユーザー情報エンドポイントは実装されていません。  ただし、このエンドポイントで受け取る可能性のあるすべてのユーザー プロファイル データは、Microsoft Graph `/me` エンドポインから使用できます。
- ロールとグループの要求。  現時点では、v2.0 エンドポイントで id_tokens 内のロールまたはグループの要求の発行はサポートされていません。

v2.0 エンドポイントでサポートされているプロトコル機能の範囲について詳しく理解するには、[OpenID Connect および OAuth 2.0 プロトコルに関するリファレンス](active-directory-v2-protocols.md)を参照してください。

## <a name="restrictions-for-work-&-school-accounts"></a>職場と学校のアカウントの制限
Microsoft の組織/ビジネス ユーザー向けに、v2.0 エンドポイントでサポートされていない機能がいくつかあります。

##### <a name="device-based-conditional-access,-native-and-mobile-apps,-and-the-microsoft-graph"></a>デバイス ベースの条件付きアクセス、ネイティブとモバイルのアプリケーション、Microsoft Graph
iOS または Android で実行されるネイティブ アプリのようなモバイル アプリケーションとネイティブ アプリケーションのデバイス認証は、v2.0 エンドポイントではまだサポートされていません。  一部の組織では、ネイティブ アプリケーションが Microsoft Graph を呼び出せない可能性があります。  管理者がアプリケーションのデバイス ベースの条件付きアクセス ポリシーを設定するときに、デバイス認証が必要となります。  v2.0 のエンドポイントでデバイス ベースの条件付きアクセスをする最も可能性の高いシナリオは、Outlook API などの Microsoft Graph のリソースで、ポリシーを設定する管理者です。  管理者がこのポリシーを設定し、ネイティブ アプリケーションが Microsoft Graph にトークンを要求した場合、デバイス認証がまだサポートされていないため、要求は最終的に失敗します。  ただし、デバイス ベースのポリシーが構成された場合、Microsoft Graph にトークンを要求する Web アプリケーションはサポートされます。  Web アプリのシナリオでは、デバイス認証はユーザーの Web ブラウザーを介して実行されます。

Microsoft Graph のリソースにポリシーが設定されるとき、高い確率で開発者による制御はできず、また設定に気づきません。  職場または学校のユーザー向けにアプリケーションを構築している場合は、v2.0 エンドポイントでデバイス認証がサポートされるまで、 [元の Azure AD エンドポイント](active-directory-developers-guide.md) を使用する必要があります。  デバイス ベースの条件付きアクセスの詳細については、 [この記事](active-directory-conditional-access.md#device-based-conditional-access)を参照してください。

##### <a name="windows-integrated-authentication-for-federated-tenants"></a>フェデレーション テナントの統合 Windows 認証
Windows アプリケーションで ADAL (および元の Azure AD エンドポイント) を使用している場合は、SAML アサーションの付与と呼ばれるものを活用できます。  この付与により、フェデレーション Azure AD テナントのユーザーは資格情報を入力せずに、オンプレミスの Active Directory インスタンスで何も行わずに認証できます。  SAML アサーションの付与は、現在 v2.0 のエンドポイントではサポートされていません。



<!--HONumber=Oct16_HO2-->


