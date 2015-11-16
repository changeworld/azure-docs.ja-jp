<properties 
	pageTitle="API Apps の概要" 
	description="Azure App Service が RESTful API を開発、発行、ホストするために最適なプラットフォームである理由について説明します。" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2015" 
	ms.author="tdykstra"/>

# API Apps の概要

API Apps は、[Azure App Service](../app-service/app-service-value-prop-what-is.md) によって提供される 4 種類のアプリの中の 1 つです。

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

API Apps は、クラウドとオンプレミスで API をビルド、ホスト、および使用するためのリッチなプラットフォームです。App Service の API アプリとして API をデプロイすると、エンタープライズ レベルのセキュリティ、簡単なアクセス制御、ハイブリッド接続、SDK の自動生成、[Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) とのシームレスな統合などを活用できます。

## API Apps を使う理由

API Apps は、現在、パブリック プレビューで次の機能を提供しています。

- **簡単な利用** - [Swagger](#concepts) のサポートが統合されたため、さまざまなクライアントで API を簡単に利用できます。[CORS](#concepts) を簡単に構成し、C#、Java、Javascript などのさまざまな言語で API のクライアント コードを自動的に生成します。

- **簡単なアクセス制御** - コードを変更せずに、API アプリを不正なアクセスから保護できます。組み込みの認証サービスでは、他のサービスからのアクセスまたはユーザーを表すクライアントからのアクセスについて API をセキュリティで保護します。サポートされている ID プロバイダーには、Azure Active Directory やサード パーティ プロバイダー ( Facebook、Twitter など) が含まれます。クライアントでは、Active Directory 認証ライブラリ (ADAL) または Mobile Apps SDK を使用できます。詳細については、「[Azure App Service での API Apps と Mobile Apps の認証](../app-service/app-service-authentication-overview.md)」を参照してください。

- **Visual Studio の統合** - Visual Studio の専用ツールを使えば、API アプリを作成、デプロイ、使用、デバッグ、管理する作業が効率的になります。

- **Logic Apps との統合** - 作成した API アプリは、[App Service Logic Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) で使用できます。

- **既存の API をそのまま使用できる** - API Apps 機能を利用するために既存の API のコードを変更する必要はありません。お持ちのコードを API アプリにデプロイするだけです。API では、App Service でサポートされている任意の言語またはフレームワークを使用できます。たとえば、ASP.NET、C#、Java、PHP、Node.js、Python などを使用できます。

これらの機能は、ほんの一例にすぎません。API Apps で活用できるその他の機能については、「[Web Apps の概要](../app-service-web/app-service-web-overview.md)」を参照してください。

>[AZURE.NOTE][Azure API Management](../api-management/api-management-key-concepts.md) を使用することで、App Service API Apps でホストされる API へのクライアント アクセスを制御できます。API Apps では認証サービスを提供します。その他に、API Apps ではなく API Management が提供するアクセス管理機能もあります。たとえば、エンドポイント統合やスロットルなどです。
>
>API Apps は現在、パブリック プレビューの段階です。[App Service Web Apps](../app-service-web/app-service-web-overview.md) は、セキュリティで保護されたミッションクリティカルなアプリケーションを世界規模で構築およびホストするための一般公開 (GA) サービスです。現在、API を構築するために GA サービスをお探しなら、Web Apps をお勧めします。API Apps が一般公開されたら、既存の Web Apps を使用しつつ API Apps の追加機能を活用する手順をご案内します。

## API Apps の概念 ##

- **Swagger** - REST ベースの API のドキュメント生成と検出のためのフレームワークです。API アプリで既定で使用されます。詳細については、[http://swagger.io/](http://swagger.io/) を参照してください。
- **クロス オリジン リソース共有 (CORS)** - ブラウザーで実行されている JavaScript が、Web ページが読み出されたドメインとは別のドメインでホストされている API を呼び出すことができるメカニズムです。
- **トリガー** - [ロジック アプリ](../app-service-logic/app-service-logic-what-are-logic-apps.md)から呼び出すことができる REST API です。特定の条件が満たされたときにワークフロー プロセスを開始できます。たとえば、API アプリは、Twitter フィード内の特定の語句を検索するためにロジック アプリから定期的に呼び出すメソッドを提供できます。詳細については、[API アプリ トリガー](app-service-api-dotnet-triggers.md)に関するページを参照してください。
- **アクション** - ワークフローがトリガーによって開始された後に、[ロジック アプリ](../app-service-logic/app-service-logic-what-are-logic-apps.md)がデータを処理するために呼び出すことができる REST API です。たとえば、API アプリは、Twitter のトリガーによって検出されたツイートに応答するためにロジック アプリから呼び出されるメソッドを提供できます。アクションは、Swagger API の定義によって公開される API のメソッドです。
- **ゲートウェイ** - リソース グループのすべての API アプリの API の管理機能と認証を処理する Web アプリです。

## 使用の開始

API アプリの使用を開始するには、[API アプリの作成に関するチュートリアル](app-service-dotnet-create-api-app.md)に従ってください。

API Apps に関する既知の問題の一覧については、[API Apps の既知の問題に関するフォーラムの投稿](https://social.msdn.microsoft.com/Forums/ja-JP/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps)を参照してください。

Azure App Service プラットフォームの詳細については、[Azure App Service](../app-service/app-service-value-prop-what-is.md) に関するページを参照してください。

 

<!---HONumber=Nov15_HO2-->