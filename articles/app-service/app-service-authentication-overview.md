<properties 
	pageTitle="Azure App Service API と Mobile Apps での認証" 
	description="Azure App Service での API Apps と Mobile Apps の認証の構成と使用について説明します。" 
	services="app-service" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/22/2015" 
	ms.author="tdykstra"/>

# Azure App Service での API Apps と Mobile Apps の認証

## 概要

この記事では、[API Apps](../app-service-api/app-service-api-apps-why-best-platform.md) と [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop-preview.md)の組み込み認証機能について説明します。

この記事の末尾にある[次のステップ](#next-steps)セクションでは、関連の操作方法に関するドキュメントへのリンクを提供しています。

## Azure App Service ゲートウェイ

Azure App Service は、[OAuth 2.0](#oauth) と [OpenID Connect](#oauth) を実装し、複数の *ID プロバイダー*を処理する組み込み認証機能を提供します。ID プロバイダーは、Azure App Service で信頼される、アプリケーションのユーザーを認証する外部のサービスです。App Service は、最も一般的な ID プロバイダーをサポートします。

* Azure Active Directory
* Microsoft アカウント
* Google
* Twitter
* Facebook

### ゲートウェイのアーキテクチャ

API Apps や Mobile Apps を含む任意の Azure リソース グループには、*ゲートウェイ*が含まれています。ゲートウェイとは、Web アプリで実行される Azure のリソースで、リソース グループ内の API Apps と Mobile Apps の認証のなどの管理タスクを処理します。

ゲートウェイはログイン手順を処理し、トークンを管理し、認証されていない呼び出しが、[認証済みアクセスを必要とするように構成](../app-service-api/app-service-api-dotnet-add-authentication.md#protect-the-api-app)されている API Apps に到達するのを防止します。リソース グループ内の API Apps 宛てに受信されるすべての HTTP 要求はゲートウェイを経由してルーティングされるため、ゲートウェイは、API Apps へのアクセスを制御できます。

次の図は、これらのゲートウェイの機能を示しています。

![](./media/app-service-authentication-overview/gateway.png)

### ゲートウェイの機能

ゲートウェイの認証サービスには、独自の OAuth 2.0 の実装を実行するにあたって、いくつかの利点があります。

* Microsoft では、簡略化された構文を使用して認証と承認のタスクを実行できる SDK を提供します。

* App Service は、さまざまな認証タスクを処理するため、開発とテストにかかる時間が最小化され、OAuth をプロバイダーに実装する際の変更への影響をほとんど、あるいはすべて回避できます。

* 保護する複数のアプリケーションがあり、それらを 1 つのリソース グループ内に保持する場合、ゲートウェイのリダイレクト URL が 1 つしかないため、各認証プロバイダーに必要なものは 1 つのクライアント ID とクライアント シークレットだけです。

* ゲートウェイを監視することによって、リソース グループ全体の認証に関連するトラフィックを監視できるため、監視とトラブルシューティングが簡単になりました。

* ローカルでデバッグ モードを実行中に、プログラムを構成してゲートウェイを使用できるため、デバッグは簡単です。ID プロバイダーのアカウントのリダイレクト URL を変更する必要はありません。

## サーバー フローとクライアント フローの比較

App Service ゲートウェイは、2 つの方法でクライアントを認証します。*クライアント フロー*と*サーバー フロー*です。両方のフローで、クライアント アプリケーションは、ユーザーの資格情報 (通常は、ユーザー名とパスワード) を、ID プロバイダーに直接送信します。ゲートウェイもアプリケーションも、どちらかのフローでユーザーの資格情報を受け取ることはありません。

### クライアント フロー

クライアント フローでは、クライアント アプリケーションは、ID プロバイダーと直接通信して、プロバイダーのアクセス トークンを取得します。クライアント アプリケーションは、ゲートウェイにプロバイダーのアクセス トークンを送信します。ゲートウェイは、ユーザーのコンテキスト トークンを作成し、クライアントに送信します。このユーザーのコンテキスト トークンは、[Azure Mobile Services](/documentation/services/mobile-services/) の元のコード ネームから Zumo トークンとも呼ばれます。( API Apps と Mobile Apps の認証サービスは、本来 Mobile Services 向けに開発された同じアーキテクチャに構築されます)。

次の図にこのフローを示します。

![](./media/app-service-authentication-overview/clientflow.png)

クライアントは、保護対象の API Apps か Mobile Apps を呼び出すときに、HTTP 要求内の Zumo トークンを提供します。ゲートウェイは、プロバイダーのトークンを格納し、どのトークンが Zumo トークンに関連付けられるかを追跡します。

### サーバー フロー

サーバー フローでは、クライアント アプリケーションはゲートウェイに依存して ID プロバイダーと通信し、ログインを開始します。クライアントのブラウザーは、ゲートウェイの URL に移動し、ゲートウェイは要求を ID プロバイダーのログイン ページにリダイレクトします。ユーザーがログインした後、ゲートウェイは ID プロバイダーのトークンを取得し、Zumo トークンを作成し、Zumo トークンをクライアントに送信します。

![](./media/app-service-authentication-overview/serverflow.png)

クライアント アプリケーションと保護された API アプリやモバイルアプリの間のそれ以降の相互作用は、クライアント フローの場合と同様に処理されます。つまり、クライアントは、HTTP 要求内の Zumo トークンを提供します。

### クライアント フローとサーバー フローの選択方法

クライアント フローは、通常、使用する ID プロバイダーにサポートするクライアント プラットフォーム用の SDK がある場合に最適です。クライアント フローは、ユーザーが資格情報を入力する回数が少なくなるため、最適なユーザー エクスペリエンスを提供します。たとえば、ユーザーが Android デバイスを使用しており、そのデバイスに Google アカウントが関連付けられている場合、ユーザーはユーザー名とパスワードを再入力することなくそのアカウントを使用することができます。このことは、優れたユーザー エクスペリエンスを実現していると言えます。Android、iOS、Windows Phone デバイスの Facebook アカウントや、Windows のデスクトップ、Windows Phone での Microsoft アカウントも同様です。

他のシナリオでは、サーバー フローのほうが優れた選択肢となる場合があります。

- ID プロバイダーとサポートするクライアントのプラットフォーム用のネイティブ クライアント SDK はありません。

- 運用環境に迅速に何か取得し、後で時間があれば、ユーザー エクスペリエンスを向上させます。サーバー フローは、Azure App Service で認証以外の作業を行うことができます。つまり、必要な開発とテストの時間を最小限に抑えます。

## SaaS プラットフォームへ代理呼び出しを送信

ログインしているユーザーの代理で、サービスとしてのソフトウェア (SaaS) プラットフォームへの呼び出しを送信できます。また、[コネクタ API アプリ](../app-service-mobile/app-service-logic-what-are-biztalk-api-apps.md) を使用できます。たとえば、[Twitter の SDK](https://dev.twitter.com/overview/api/twitter-libraries) を使用できる Twitter アカウント からツイートを投稿したり、Azure サブスクリプションの [Twitter コネクタ](../app-service-mobile/app-service-logic-connector-twitter.md)をプロビジョニングしたり、それを呼び出したりできます。このセクションでは、API アプリ か モバイル アプリで実行されているコードから SaaS プラットフォームにアクセスする方法について説明します。

### <a id="obotoidprovider"></a> ID プロバイダー トークンの使用 

ゲートウェイは、Zumo トークンを複数の ID プロバイダー アクセス トークンと更新トークンに関連付ける*トークン ストア* を保持します。有効な Zumo トークンを持った HTTP要求を受信した場合、ゲートウェイはどの ID プロバイダー トークンがそのユーザーに関連しているのかを認識します。
  
API アプリやモバイル アプリで実行されているコードが、ログオン ユーザーの代わりに保護されたリソースに呼び出しを行う必要がある場合は、次の図に示すように、ゲートウェイのトークン ストアから、ID プロバイダーのトークンを取得して使用できます。この図は、クライアントが既にゲートウェイで認証されていて Zumo トークンがあることを前提としています。

![](./media/app-service-authentication-overview/idprovidertoken.png)

たとえば、ID プロバイダーが Azure Active Directory (AAD) であり、API アプリで AAD アクセス トークンを使用して AAD Graph API を呼び出すか、ユーザーのアクセス許可がある SharePoint サイトへのアクセスを要求すると仮定します。ゲートウェイに要求を送信して AAD トークンを取得し、その AAD トークンを使用して Graph API を呼び出すか、SharePoint サイトのアクセス トークンを取得できます。

### <a id="obotosaas"></a>その他のリソースにアクセスするためにユーザーの同意を取得する

ゲートウェイには、元の ID プロバイダー以外のプロバイダーによってセキュリティ保護されたリソースにアクセスするときに、ユーザーの同意を取得する組み込みの機能もあります。たとえば、Azure Active Directory を使用してサインインするユーザーは、ユーザーの Dropbox アカウント内のファイルにアクセスできます。

アプリ サービス ゲートウェイには、次のプロバイダーからのアクセス同意を取得するための組み込みのサポートが含まれています。

* Box
* DropBox
* Facebook
* Google
* Office 365
* OneDrive
* QuickBooks
* Salesforce
* SharePointOnline
* Twitter
* Yammer
* Azure Active Directory
* Microsoft アカウント

これらのプロバイダーでは、ゲートウェイはアクセス トークンを保持し、ID プロバイダー アクセス トークンと同様に、Zumo トークンと関連付けます。次の図に、ユーザーの同意を取得し、SaaS プラットフォームを呼び出すプロセスを示します。この図は、クライアントが既にゲートウェイで認証されていて Zumo トークンがあることを前提としています。

![](./media/app-service-authentication-overview/saastoken.png)

App Service のランタイム サポートと SDK では、これらのプロバイダーのいずれかによってセキュリティで保護されたリソースにアクセスするコードを記述することは比較的簡単です。図には表示されていない準備手順があります。プロバイダーを持つアカウントを設定し、Azure App Service のプロバイダーのクライアント ID とクライアント シークレットの設定を構成する必要があります。

これらとその他の多くのプロバイダーで、事前にパッケージされた[コネクタ API アプリ](../app-service-mobile/app-service-logic-what-are-biztalk-api-apps.md)を使用してセキュリティで保護されたリソースにアクセスできます。

## SDK の可用性

API Appsに対して、SDK for .NET は認証機能を提供します。

* [Microsoft.Azure.AppService](http://www.nuget.org/packages/Microsoft.Azure.AppService) - API アプリのクライアントで使用します。  
* [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/) - API アプリで実行されている Web API プロジェクトで使用します。
 
また、Visual Studio では、SDK for .NET と動作するコードを自動的に生成し、API アプリを呼び出すために記述するコードを簡素化します。詳細については、「[Azure App Service で .NET クライアントから API アプリ使用する](../app-service-api/app-service-api-dotnet-consume.md)」をご覧ください。

Mobile Apps の場合は、SDK は次のプラットフォームで利用できます。

- [.NET のサーバー](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) 
- [iOS](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-preview.md)
- [Xamarin iOS](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md)
- [Xamarin Android](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview.md)
- [Windows](../app-service-mobile/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md)
- JavaScript (開発中のチュートリアル)

## 代替の認証方法

ゲートウェイの認証サービスが、アプリのニーズを満たしていない場合は、自分で認証を処理するか、Azure API Management サービスを使用できます。

### <a id="doityourself"></a> 手動による認証

Azure の [ASP.NET Identity](http://www.asp.net/identity) や [Thinktecture](http://www.thinktecture.com/identityAndAccessControl) などの認証フレームワークを使用できます。これにより、すべての処理を制御できますが、認証機能の開発とテストに少し時間がかかります。また、複数のリダイレクト URL で保護する複数のアプリがある場合、Facebook、Google、Twitter などのサード パーティの認証プロバイダーで、複数のクライアント ID とクライアント シークレットを構成する必要があります。

現時点で、[Mobile Services](mobile-services-dotnet-backend-get-started-custom-authentication.md) では可能ですが、App Service では、ゲートウェイ認証と手動による認証ソリューションの併用はサポートできません。

### <a id="apim"></a>Azure API Management

認証を使用して保護する既存の API がある場合、Azure API Management サービスを使用して行うことができます。API アプリを使用した API Management の使用方法の詳細については、Panos Kefalidis が投稿した次のブログをご覧ください。「[API アプリ用 API Management の利用](http://www.kefalidis.me/2015/06/taking-advantage-of-api-management-for-api-apps/)」。

## 次のステップ

この記事には、API Apps と Mobile Apps に提供される Azure App Service の認証サービスについて説明されています。次に、基になる認証プロトコルと App Service の認証機能を使用する方法についてドキュメントについてのラーニング リソースへのリンクを示します。

* [OAuth 2.0、OpenID Connect、JSON Web トークン (JWT)](#oauth)
* API Apps のリソース
	* [API Apps のクライアント フロー](#apiaclient)
	* [API Apps のサーバー フロー](#apiaserver)
	* [API Apps の代理呼び出し](#apiaobo)
* Mobile Apps のリソース
	* [Mobile Apps のクライアント フロー](#maclient)
	* [Mobile Apps のサーバー フロー](#maserver)
	* [Mobile Apps の代理呼び出し](#maobo)

### <a id="oauth"></a>OAuth 2.0、OpenID Connect、JSON Web トークン (JWT)

* [OAuth 2.0 の概要](http://shop.oreilly.com/product/0636920021810.do "OAuth 2.0 の概要") 
* [OAuth2、OpenID Connect と JSON Web トークン (JWT) の概要 - PluralSight コース](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction) 
* [ASP.NET での複数のクライアント用 RESTful API の構築とセキュリティ保護 - PluralSight コース](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)

### <a id="apiaclient"></a>API Apps のクライアントのフロー

* [API アプリの保護](../app-service-api/app-service-api-dotnet-add-authentication.md) - API アプリの構成 パーツは、クライアント フローとサーバー フローの両方に適用されますが、ブラウザー内テスト パーツは、サーバー フローを示します。
* [.NET クライアントから Azure App Service で API アプリを使用する](../app-service-api/app-service-api-dotnet-consume.md) - 認証された呼び出しのサンプル アプリはサーバー フローを示していますが、その後にサンプル コードによる[クライアント フロー](../app-service-api/app-service-api-dotnet-consume.md#client-flow)のセクションが続きます。

### <a id="apiaserver"></a>API Apps サーバー フロー

* [API アプリの保護](../app-service-api/app-service-api-dotnet-add-authentication.md) - API アプリの構成 パーツは、クライアント フローとサーバー フローの両方に適用されます。またブラウザー内テスト パーツは、サーバー フローを示します。
* [.NET クライアントから Azure App Service で API アプリを使用する](../app-service-api/app-service-api-dotnet-consume.md) - 認証された呼び出しのサンプル コードはサーバー フローを示しています。 

### <a id="apiaobo"></a>API Apps の代理呼び出し

* [Azure App Service の SaaS コネクタ API アプリのデプロイと構成](../app-service-api/app-service-api-connnect-your-app-to-saas-connector.md) - 事前にパッケージされたコネクタ API アプリのプロビジョニング、構成、ブラウザー ツールを使用した呼び出し方法を示します。
* [Azure App Service での ASP.NET API アプリから SaaS のプラットフォームへの接続](../app-service-api/app-service-api-dotnet-connect-to-saas.md) - 独自のコネクタ、つまり、SaaS プロバイダーに対する代理呼び出しを実行するカスタム API アプリのコードをプロビジョニング、構成、記述する方法を示しています。

### <a id="maclient"></a>Mobile Apps のクライアント フロー

* [iOS アプリに Azure Active Directory シングル サインオンを追加する](../app-service-mobile/app-service-mobile-dotnet-backend-ios-aad-sso-preview.md)

### <a id="maserver"></a>Mobile Apps のサーバーのフロー

* [iOS アプリに認証を追加する](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-users-preview.md)
* [Xamarin.iOS アプリに認証を追加する](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md)
* [Xamarin.Android アプリに認証を追加する](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-android-get-started-users-preview.md)
* [Windows アプリに認証を追加する](../app-service-mobile/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-users-preview.md)

### <a id="maobo"></a>セキュリティで保護されたリソースへの Mobile Apps の代理呼び出し

* [アクセス トークンを取得し、Mobile Apps の SharePoint API を呼び出す](../app-service-mobile/app-service-mobile-dotnet-backend-get-started-connect-to-enterprise.md#obtain-token)

<!---HONumber=Sept15_HO4-->