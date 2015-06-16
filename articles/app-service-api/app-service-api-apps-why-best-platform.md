<properties 
	pageTitle="API Apps とは" 
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
	ms.date="05/05/2015" 
	ms.author="tdykstra"/>

# API Apps とは

API Apps は、Web Apps、Moblue Apps、Logic Apps と同様に、[Azure App Service](../app-service/app-service-value-prop-what-is.md) スイートを構成する要素の 1 つです。

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

このスイートで、API Apps は、クラウドとオンプレミスの API を構築、使用、配布するための豊富なプラットフォームとエコシステムを提供します。

>[AZURE.NOTE]API Apps は現在、パブリック プレビューの段階です。API Apps は、セキュリティで保護されたミッションクリティカルなアプリケーションを世界規模で構築およびホストするための一般公開 (GA) サービスである [App Service Web Apps](../app-service-web/app-service-web-overview.md) をベースに構築されています。現在、API を構築するために GA サービスをお探しなら、Web Apps をお勧めします。API Apps が一般公開されたら、既存の Web アプリを使用しつつ API Apps の機能を活用する手順をご案内します。

## API Apps を使う理由

API アプリは、REST ベースの Web API の開発、デプロイ、発行、使用、管理、収益化を支援する追加機能を備えた [App Service の Web アプリ](../app-service-web/app-service-web-overview.md)です。

つまり、API アプリ は、次のような Azure App Service プラットフォームの Web ホスティング機能すべてを Web アプリと共有します。

- OS の修正プログラムの自動適用
- エンタープライズ グレードのセキュリティ
- 高可用性
- 自動スケーリングと負荷分散
- バックグラウンド処理用の [Web ジョブ](../app-service-web/websites-webjobs-resources.md)
- 迅速かつ簡単なデプロイメントと多くの継続的な配信オプション - API アプリで使用できるデプロイメント オプションの範囲については、「[Azure App Service での Web アプリのデプロイ](../app-service-web/web-sites-deploy.md)」を参照してください。 

API Apps プラットフォームが提供する次の追加機能によって、API の開発、ホスト、使用が簡単になります。

- **API をそのまま使用** - API には、ASP.NET、Java、PHP、Node.js、または Python を使用できます。そのままの API から API Apps プラットフォームを利用できます。

- **簡単なアクセス制御** - コードを変更せずに、Azure Active Directory や、Facebook、Twitter などのサード パーティのサービスによる認証のために API を構成できます。認証コード用の簡略化された構文を使用できます。詳細については、[API アプリの保護](app-service-api-dotnet-add-authentication.md)に関するページを参照してください。

- **SaaS プラットフォームへの簡単な接続** - Azure Marketplace で提供されているマイクロソフトとサード パーティの[コネクタ API アプリ](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md)を利用すれば、SalesForce、Office 365、Twitter、Facebook、Dropbox などの操作のために記述するコードを簡略化できます。

- **オンプレミスのデータへのアクセス** - API Apps では、[ハイブリッド接続](../integration-hybrid-connection-overview.md)と [VNET](../app-service-web/web-sites-integrate-with-vnet.md) を使用して、自社のデータ センター内のデータを使用する API を公開することができます。

- **簡単な利用** - 統合されている [Swagger](http://swagger.io/) のサポートを利用すれば、さまざまなクライアントで API を簡単に利用できます。API Apps SDK を使用して、C#、Java、Javascript などのさまざまな言語で API のクライアント コードを生成することもできます。

- **ロジック アプリとの統合** - 作成した API アプリは、App Service のロジック アプリで使用できます。

- **Visual Studio の統合** - Visual Studio の専用ツールを使えば、API アプリを[作成](app-service-dotnet-create-api-app.md)、[デプロイ](app-service-dotnet-deploy-api-app.md)、[デバッグ](app-service-dotnet-remotely-debug-api-app)、管理する作業が効率的になります。

近い将来、API Apps プラットフォームには、コードの共有を簡単にすることで、次のようなリッチな API のエコシステムが生まれます。

- **パブリックおよびプライベートのマーケットプレース** - [Azure Marketplace](http://azure.microsoft.com/marketplace/) は、マイクロソフトやサード パーティによって開発された事前にパッケージ済みの API アプリを簡単に見つけて、自分の Azure サブスクリプションにデプロイできるようになります。また、独自に開発した API アプリをパッケージ化して発行できるようになるため、他の開発者はこの API を自らの Azure サブスクリプションにデプロイできます。Azure Marketplace に独自の API を発行するときに、組織の他のメンバーにのみ表示されるようにできるようになります。 

- **依存関係の自動デプロイメント** - Marketplace から Azure サブスクリプションに API アプリをデプロイすると、Azure によって依存する API アプリが自動的にデプロイされ、必要なリソースが作成されるようになる予定です。依存する API アプリと必要な Azure のリソースは、API アプリのパッケージによって指定されます。

- **自動更新** - 共有したいずれかの API アプリ パッケージのコードを更新するときに、API アプリをインストールしたユーザーと実行しているユーザー全員に更新プログラムをプッシュできるようになります。これは、重大でない変更の場合に、更新プログラムの受信をオプトインしているユーザーに機能します。

パブリック マーケットプレースや自動更新など、これらの機能の多くは、マイクロソフトが提供する API アプリに既に使用できる状態になっています。

## API Apps の概念 ##

- **ゲートウェイ** - リソース グループのすべての API アプリの API の管理機能と認証を処理する Web アプリです。 
- **Swagger** - REST ベースの API の対話型のドキュメント生成と検出のためのフレームワークです。API アプリで既定で使用されます。詳細については、[http://swagger.io/](http://swagger.io/) を参照してください。
- **コネクタ** - API アプリの一種で、Salesforce、Office 365 などの SaaS プラットフォームへの接続を容易にします。詳細については、「[コネクタと BizTalk API Apps とは](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md)」を参照してください。
- **トリガー** - [ロジック アプリ](../app-service-logic/app-service-logic-what-are-logic-apps.md)から呼び出すことができる REST API です。特定の条件が満たされたときにワークフロー プロセスを開始できます。たとえば、API アプリは、Twitter フィード内の特定の語句を検索するためにロジック アプリから定期的に呼び出すメソッドを提供できます。詳細については、[API アプリ トリガー](app-service-api-dotnet-triggers.md)に関するページを参照してください。
- **アクション** - ワークフローがトリガーによって開始された後に、[ロジック アプリ](../app-service-logic/app-service-logic-what-are-logic-apps.md)がデータを処理するために呼び出すことができる REST API です。たとえば、API アプリは、Twitter のトリガーによって検出されたツイートに応答するためにロジック アプリから呼び出されるメソッドを提供できます。アクションは、Swagger API の定義によって公開される API のメソッドです。

## 使用の開始

API アプリの使用を開始するには、[API アプリの作成に関するチュートリアル](app-service-dotnet-create-api-app.md)に従ってください。

API のアプリに関する既知の問題の一覧については、[MSDN フォーラムのこの投稿](https://social.msdn.microsoft.com/Forums/en-US/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps)を参照してください。

Azure App Service プラットフォームの詳細については、[Azure App Service](../app-service/app-service-value-prop-what-is.md) に関するページを参照してください。


<!--HONumber=54--> 