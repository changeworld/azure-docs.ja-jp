<properties 
	pageTitle="API Apps の概要 | Microsoft Azure" 
	description="Azure App Service が RESTful API を開発、発行、ホストするために最適なプラットフォームである理由について説明します。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2016" 
	ms.author="tdykstra"/>

# API Apps の概要

API Apps は、[Azure App Service](../app-service/app-service-value-prop-what-is.md) によって提供される 4 種類のアプリの中の 1 つです。

![Azure App Service でのアプリの種類](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

[App Service](../app-service/app-service-value-prop-what-is.md) は、Web、モバイル、および統合シナリオのための完全に管理されたプラットフォームです。App Service の API Apps は、クラウドとオンプレミスでの API のビルド、ホスト、および使用を容易にする機能を提供します。API を App Service の API アプリとしてデプロイすると、エンタープライズ レベルのセキュリティ、シンプルなアクセス制御、ハイブリッド接続、SDK の自動生成、および [Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) とのシームレスな統合を利用できます。

## API Apps を使う理由

API Apps には、次の機能が用意されています。

- **簡単な利用** - [Swagger API メタデータ](#concepts)に対するサポートの統合によって、さまざまなクライアントで API を簡単に利用できます。C#、Java、Javascript などのさまざまな言語で API のクライアント コードを自動的に生成します。コードの変更なしで [CORS](#concepts) を簡単に構成します。詳細については、「[API の検出とコードの生成を行うための App Service API Apps のメタデータ](app-service-api-metadata.md)」と「[CORS を使用して JavaScript から API アプリを使用する](app-service-api-cors-consume-javascript.md)」を参照してください。 

- **シンプルなアクセス制御** - コードを変更せずに、API アプリを不正なアクセスから保護します。組み込みの認証サービスでは、他のサービスからのアクセスまたはユーザーを表すクライアントからのアクセスから API をセキュリティで保護します。サポートされている ID プロバイダーには、Azure Active Directory やサード パーティ プロバイダー ( Facebook、Twitter など) が含まれます。クライアントでは、Active Directory 認証ライブラリ (ADAL) または Mobile Apps SDK を使用できます。詳細については、「[App Service 認証/承認の展開](/blog/announcing-app-service-authentication-authorization/)」と「[App Service API Apps - 変更内容](app-service-api-whats-changed.md)」を参照してください。

- **Visual Studio の統合** - Visual Studio の専用ツールを使えば、API アプリを作成、デプロイ、使用、デバッグ、管理する作業が効率的になります。詳細については、[Azure SDK 2.8.1 for .NET の発表に関するページ](/blog/announcing-azure-sdk-2-8-1-for-net/)を参照してください。

- **Logic Apps との統合** - 作成した API アプリは、[App Service Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) で使用できます。詳細については、「[App Service でホストされたカスタム API のロジック アプリでの使用](../app-service-logic/app-service-logic-custom-hosted-api.md)」と「[新しいスキーマ バージョン 2015-08-01-preview](../app-service-logic/app-service-logic-schema-2015-08-01.md)」を参照してください。

- **既存の API をそのまま使用できる** - API Apps 機能を利用するために既存の API のコードを変更する必要はありません。お持ちのコードを API アプリにデプロイするだけです。API では、App Service でサポートされている任意の言語またはフレームワークを使用できます。たとえば、ASP.NET、C#、Java、PHP、Node.js、Python などを使用できます。

さらに、API アプリでは、[Web Apps](../app-service-web/app-service-web-overview.md) および [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) によって提供される機能を活用することができます。この逆も当てはまります。Web アプリまたはモバイル アプリを使用して API をホストする場合は、Swagger メタデータなどの API Apps の機能を活用してクライアント コードを生成し、CORS を活用してブラウザーのクロス ドメイン アクセスを実行できます。3 つの種類 (API、Web、モバイル) の異なる点は、Azure ポータルでこれらに対して使用する名前とアイコンのみです。すべてに同じ機能があるため、必要な機能を得るためにアプリの種類を変える必要はありません。ただし、既存のアプリの種類を変更する必要がある場合には、簡単に行うことができます。詳細については、「[Azure App Service で API Apps と ASP.NET を使用する](app-service-api-dotnet-get-started.md#optional-changing-an-app-type)」の「**アプリの種類を変更する**」を参照してください。

## Azure API Management を使用して API Apps を強化する 

API Apps と [Azure API Management](../api-management/api-management-key-concepts.md) は補完的なサービスです。

* API Management は API 管理関連のサービスです。API Management フロントエンドを API に配置し、使用率を監視し、調整したり、入力と出力を操作したり、複数の API を 1 つに統合したりします。管理対象の API は任意の場所でホストできます。
* API Apps は API のホスト関連のサービスです。このサービスには API の開発と使用を支援する機能がありますが、API Management で行うような監視、調整、操作、統合は行いません。 

API Management を利用し、API Apps がホストする API を管理するか、API Management なしで API Apps を使用できます。

API Management と API Apps の一部の機能には同じような働きがあります。たとえば、両方とも CORS サポートを自動化できます。2 つのサービスを共に使用する場合、CORS には API Management を使用します。これが API アプリのフロントエンドとして機能するためです。

## <a id="concepts"></a> API Apps の概念

- **Swagger** - REST ベースの API のドキュメント生成と検出のためのフレームワークです。API Apps で既定で使用されます。詳細については、[http://swagger.io/](http://swagger.io/) を参照してください。
- **クロス オリジン リソース共有 (CORS)** - ブラウザーで実行されている JavaScript が、Web ページが読み出されたドメインとは別のドメインでホストされている API を呼び出すことができるメカニズムです。詳細については、「[CORS を使用して JavaScript から API アプリを使用する](app-service-api-cors-consume-javascript.md)」を参照してください。 
- **トリガー** - [ロジック アプリ](../app-service-logic/app-service-logic-what-are-logic-apps.md)から呼び出すことができる REST API です。特定の条件が満たされたときにワークフロー プロセスを開始できます。たとえば、API アプリは、Twitter フィード内の特定の語句を検索するためにロジック アプリから定期的に呼び出すメソッドを提供できます。詳細については、[API アプリ トリガー](app-service-api-dotnet-triggers.md)に関するページを参照してください。
- **アクション** - ワークフローがトリガーによって開始された後に、[ロジック アプリ](../app-service-logic/app-service-logic-what-are-logic-apps.md)がデータを処理するために呼び出すことができる REST API です。たとえば、API アプリは、Twitter のトリガーによって検出されたツイートに応答するためにロジック アプリから呼び出されるメソッドを提供できます。アクションは、Swagger API の定義によって公開される API のメソッドです。

## 使用の開始

API Apps の使用を開始する場合は、[API Apps の使用に関するチュートリアル](app-service-api-dotnet-get-started.md)のいずれかに従ってください。

API アプリに関する質問がある場合は、[API Apps フォーラム](https://social.msdn.microsoft.com/Forums/ja-JP/home?forum=AzureAPIApps)でスレッドを開始します。

<!---HONumber=AcomDC_0330_2016-->