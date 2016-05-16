<properties
	pageTitle="Azure App Service での認証と承認 | Microsoft Azure"
	description="Azure App Service の認証/承認の機能の概念リファレンスと概要"
	services="app-service"
	documentationCenter=""
	authors="mattchenderson"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="04/25/2016"
	ms.author="mahender"/>
    
# Azure App Service での認証および承認

## App Service の認証および承認とは

App Service の認証および承認とは、アプリのバックエンドのコードを変更せずに、アプリケーションがユーザーのログインを許可する機能です。これにより、アプリケーションの保護が容易になり、またユーザーごとのデータにも対応できるようになります。

App Service では、サード パーティの ID プロバイダーがアカウントを格納しユーザーを認証する、フェデレーション ID を使用しています。アプリケーションは、プロバイダーの ID 情報を、自身には格納せずに使用します。App Service では、標準で _Azure Active Directory_、_Facebook_、_Google_、_Microsoft アカウント_、および _Twitter_ の 5 つの ID プロバイダーをサポートしています。アプリで利用可能な ID プロバイダーには制限がないため、ユーザーのログイン方法に合わせたオプションを提供できます。他の ID プロバイダーや[独自のカスタム ID ソリューション][custom-auth]を統合して、組み込みのサポートを拡張することもできます。

すぐに開始する場合は、以下のチュートリアルのいずれかを参照してください。

- [iOS アプリに認証を追加する][iOS] (または [Android]、[Windows]、[Xamarin.iOS]、[Xamarin.Android]、[Xamarin.Forms]、[Cordova])
- [Azure App Service の API Apps でのユーザー認証][apia-user]

## App Service の認証のしくみ

いずれかの ID プロバイダーで認証を行うには、最初にその ID プロバイダーを構成してアプリケーションを把握させる必要があります。すると、ID プロバイダーから ID とシークレットが提供されるので、それらを App Service に戻します。これにより信頼関係が完成し、App Service は、認証トークンなどの ID プロバイダーからのユーザー アサーションを検証できるようになります。

こうしたプロバイダーのいずれかを使用してユーザーをサインインさせるには、ユーザーをそのプロバイダーのログイン エンドポイントにリダイレクトする必要があります。顧客が Web ブラウザーを使用している場合は、App Service を使用して、認証されていないすべてのユーザーをログイン エンドポイントに自動的に転送できます。それ以外の場合は、顧客を `{your App Service base URL}/.auth/login/<provider>` に転送する必要があります。ここで、`<provider>` は、_aad_、_facebook_、_google_、_microsoft_、_twitter_ のいずれかになります。モバイルおよび API のシナリオについては、以下のセクションで説明します。

Web ブラウザーを使用してアプリケーションとやりとりするユーザーは、Cookie を設定することになるため、アプリケーションをナビゲートするときに認証された状態を維持できます。モバイルなどの他のタイプのクライアントでは、クライアントに対して、`X-ZUMO-AUTH` ヘッダーに表示する必要がある JSON Web トークン (JWT) が発行されます。これは、Mobile Apps クライアント SDK によって処理されます。または、Azure Active Directory の ID トークンまたはアクセス トークンを、`Authorization` ヘッダーに[ベアラー トークン](https://tools.ietf.org/html/rfc6750)として直接含めることができます。

App Service は、アプリケーションが発行したすべての Cookie とトークンを検証して、ユーザーが認証されるようにします。アプリケーションにアクセスできるユーザーを制限するには、以下の[認証](#authorization)に関するセクションを参照してください。

### プロバイダー SDK を使用したモバイル認証

バックエンドですべてを構成したら、App Service にログインできるようにモバイル クライアントを変更できます。これには 2 つの方法はあります。

- 特定の ID プロバイダーが発行している SDK を利用し、ID を確立し、App Service にアクセスできるようにします。
- 1 行のコードを使用して、Mobile Apps クライアント SDK でユーザーがサインインできるようにします。

>[AZURE.TIP] 多くのアプリケーションでは、よりネイティブに感じられるログイン エクスペリエンスを得たり、更新のサポートを利用したり、その他のプロバイダーに固有のメリットを得るために、プロバイダーの SDK を使用した方がよいでしょう。

プロバイダー SDK を使用すると、アプリが実行されているプラットフォーム OS とログイン エクスペリエンスがより緊密に通信するようになります。これにより、プロバイダーのトークンとクライアント上のユーザー情報の一部が得られるので、グラフ API が使用しやすくなったり、ユーザー エクスペリエンスをカスタマイズしやすくなったりします。クライアント コードがログインを処理し、クライアント コードがプロバイダーのトークンにアクセスするため、これは "クライアント フロー" または "クライアント主導のフロー" と、ブログやフォーラムではときどき呼ばれています。

プロバイダーのトークンが取得されたら、App Service に送信して検証する必要があります。App Service では、トークンの検証後、クライアントに返される新しい App Service トークンが作成されます。Mobile Apps クライアント SDK には、この交換を管理してアプリケーション バックエンドへのすべての要求にトークンを自動的に添付するヘルパー メソッドがあります。希望する場合、開発者はプロバイダー トークンへの参照を保持できます。

### プロバイダー SDK を使用しないモバイル認証

プロバイダー SDK を設定しない場合は、App Service Mobile Apps でのログインを許可できます。Mobile Apps クライアント SDK が、選択したプロバイダーの Web ビューを開き、サインインを完了させます。サーバーがログイン管理を行い、クライアント SDK がプロバイダーのトークンを受け取ることはないため、これは "サーバー フロー" または "サーバー主導のフロー" と、ブログやフォーラムではときどき呼ばれています。

このフローを開始するために必要なコードは、各プラットフォームの認証のチュートリアルで説明されています。フローの最後では、クライアント SDK が App Service トークンを取得し、そのトークンがアプリケーション バックエンドへのすべての要求に自動的に添付されます。

### サービス間認証

ユーザーにアプリケーションへのアクセス権を付与する以外に、別の信頼されたアプリケーションに独自 API の呼び出しを許可することもできます。たとえば、ある App Service アプリケーションから別の App Service アプリケーションの API を呼び出すことができます。このシナリオでは、エンド ユーザーの資格情報ではなく、サービス アカウントの資格情報を使用して、トークンを取得します。サービス アカウントは、Azure Active Directory の用語では*サービス プリンシパル*とも呼ばれ、このようなアカウントでの認証は、サービス間シナリオとも呼ばれます。

>[AZURE.IMPORTANT] モバイル アプリケーションは、お客様のデバイスで実行されるので、信頼されたアプリケーションとは_みなされません_。また、サービス プリンシパルのフローも使用できません。代わりに、上記のユーザー フローのいずれかを使用してください。

サービス間シナリオでは、App Service は Azure Active Directory を使用してアプリケーションを保護できます。呼び出し元のアプリケーションは、AAD からクライアント ID とクライアント シークレットを提供することで取得した AAD サービスのプリンシパル認証トークンを提供するだけで済みます。このシナリオについて、ASP.NET API アプリを使った例が、[API Apps のサービス プリンシパル認証][apia-service]に関するチュートリアルで紹介されています。

App Service 認証を使わずにサービス間の認証を行う場合、クライアント証明書または基本認証を利用することができます。Azure のクライアント証明書の詳細については、「[Web Apps の TLS 相互認証を構成する方法](../app-service-web/app-service-web-configure-tls-mutual-auth.md)」を参照してください。ASP.NET での基本認証の詳細については、「[Authentication Filters in ASP.NET Web API 2 (ASP.NET Web API 2 の認証フィルター)](http://www.asp.net/web-api/overview/security/authentication-filters)」を参照してください。

App Service ロジック アプリから API アプリへのサービス アカウント認証は特殊なケースであり、「[App Service でホストされたカスタム API のロジック アプリでの使用](../app-service-logic/app-service-logic-custom-hosted-api.md)」で説明されています。

## <a name="authorization"></a>App Service の認証のしくみ

どの要求にアプリケーションへのアクセスを許可するかを完全に制御することができます。App Service の認証/承認は、次の動作のいずれかになるように構成できます。

- 認証済みの要求のみアプリケーションへの到達を許可する。

	ブラウザーから匿名要求を受信した場合、App Service は、選択した ID プロバイダーのログオン ページにリダイレクトされます。モバイル デバイスから要求を受信した場合は、HTTP _401 Unauthorized_ 応答が返されます。

	このオプションを使用すると、アプリで認証コードを記述する必要はまったくありません。細かく設定した承認を行う必要がある場合は、ユーザーに関する情報をコードで使用できます。

- すべての要求にアプリケーションへの到達を許可したうえで、認証済みの要求を検証し、HTTP ヘッダー内の認証情報を受け渡しする。

	このオプションでは、承認に関する決定をアプリケーション コードに委ねます。匿名要求処理の柔軟性は高まりますが、コードを記述する必要があります。
	
- すべての要求にアプリケーションへの到達を許可し、要求に含まれる認証情報に対して何も実行しない。

	この場合、認証/承認の機能は無効になります。認証と承認に伴う一切の処理をアプリケーション コードに委ねることになります。

上記の動作は、ポータルの [**要求が認証されない場合に実行するアクション**] オプションによって制御します。プロバイダーのいずれかについて "...へのログイン" を選択する場合は、すべての要求が認証される必要があります。[要求の許可 (操作不要)] では、認証に関する決定がコードに委ねられますが、認証情報も提供されます。コードですべてを処理する場合は、認証/承認の機能を無効にすることができます。

## アプリケーションでのユーザー ID の使用

App Service では、特殊なヘッダーを使用して、アプリケーションにユーザー情報の一部を渡します。これらのヘッダーは、外部要求からは禁じられています。App Service の認証/承認によって設定された場合にのみ、使用できます。いくつかのヘッダーの例は次のとおりです。

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON

任意の言語またはフレームワークで記述されたコードでは、これらのヘッダーから必要な情報を取得できます。ASP.NET 4.6 アプリの場合は、ClaimsPrincipal が自動的に適切な値に設定されます。

アプリケーションでは、HTTP GET を介してアプリケーションの `/.auth/me` エンドポイントで、その他のユーザー詳細も取得できます。有効なトークンが要求に含まれている場合は、このトークンにより、使用されているプロバイダー、基になっているプロバイダー トークン、およびその他のユーザー情報に関する詳細を含む JSON ペイロードが返されます。Mobile Apps サーバー SDK には、このデータを操作するためのヘルパー メソッド ([Node.JS](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md/#howto-tables-getidentity)、[.NET](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md/#user-info)) が用意されています。

## ドキュメントおよびその他のリソース

### ID プロバイダー
以下のチュートリアルは、さまざまな認証プロバイダーを使用する、App Service の構成方法について説明しています。

- [Azure Active Directory ログインを使用するようにアプリを構成する方法][AAD]
- [Facebook ログインを使用するようにアプリを構成する方法][Facebook]
- [Google ログインを使用するようにアプリを構成する方法][Google]
- [Microsoft アカウント ログインを使用するようにアプリを構成する方法][MSA]
- [Twitter ログインを使用するようにアプリを構成する方法][Twitter]

ここで示す以外の ID システムを使用する場合は、[Mobile Apps .NET サーバー SDK でのカスタム認証のサポートのプレビュー][custom-auth]も利用できます。このプレビューは、Web Apps、Mobile Apps、または API Apps で使用できます。

### モバイル アプリケーション
以下のチュートリアルで、サーバー主導のフローを使用し、モバイル クライアントに認証を追加する方法について説明しています。

- [iOS アプリに認証を追加する][iOS]
- [Android アプリに認証を追加する][Android]
- [Windows アプリに認証を追加する][Windows]
- [Xamarin.iOS アプリに認証を追加する][Xamarin.iOS]
- [Xamarin.Android アプリに認証を追加する][Xamarin.Android]
- [Xamarin.Forms アプリに認証を追加する][Xamarin.Forms]
- [Cordova アプリに認証を追加する][Cordova]

AAD にクライアント主導のフローを使用する場合は以下を参照してください。

- [iOS 向け Active Directory 認証ライブラリを使用する][ADAL-iOS]
- [Android 向け Active Directory 認証ライブラリを使用する][ADAL-Android]
- [Windows および Xamarin 向け Active Directory 認証ライブラリを使用する][ADAL-dotnet]

### API アプリケーション
次のチュートリアルで、API Apps を保護する方法を示しています。

- [Azure App Service の API Apps でのユーザー認証][apia-user]
- [Azure App Service での API Apps のサービス プリンシパル認証][apia-service]









[apia-user]: ../app-service-api/app-service-api-dotnet-user-principal-auth.md
[apia-service]: ../app-service-api/app-service-api-dotnet-service-principal-auth.md

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: ../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: ../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md
[MSA]: ../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: ../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md/#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md/#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md/#adal

<!---HONumber=AcomDC_0504_2016-->