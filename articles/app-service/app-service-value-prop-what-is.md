<properties 
	pageTitle="Azure App Service とは | Microsoft Azure" 
	description="Azure App Service を活用して Web アプリとモバイル アプリを開発、デプロイ、管理する方法について説明します。" 
	keywords="App Service, Azure App Service, App Service のコスト, スケール, スケーラブル, アプリのデプロイ, Azure アプリのデプロイ, PaaS, サービスとしてのプラットフォーム"
	services="app-service" 
	documentationCenter="" 
	authors="omarkmsft" 
	manager="dwrede" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/25/2016" 
	ms.author="omark"/>

# Azure App Service とは

*App Service* は、Microsoft Azure が提供する[サービスとしてのプラットフォーム](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) であり、これを利用すると、Web アプリとモバイル アプリを任意のプラットフォームやデバイス向けに作成することができます。セキュリティ、信頼性、拡張性の厳しいニーズに対応しながら、アプリの SaaS ソリューション (Office 365、Dynamics CRM、Salesforce、Twilio など) との統合やオンプレミスのアプリケーション (SAP、Oracle、Siebel など) との接続、ビジネス プロセスの自動化が簡単に実現できます。

App Service には、以前は Azure Websites および Azure Mobile Services として個別に提供されていた Web 機能とモバイル機能が含まれています。さらに、ビジネス プロセスの自動化やクラウド API のホストに利用できる新しい機能も備えています。

## App Service でのアプリの種類

App Service では、アプリケーション コードまたはワークフロー プロセスの実行用に、次のようなアプリの種類を用意しています。

- [**Web Apps**](../app-service-web/app-service-web-overview.md) - Web サイトと Web アプリケーションをホストするためのアプリの種類。

- [**Mobile Apps**](../app-service-mobile/app-service-mobile-value-prop.md) - モバイル アプリのバック エンドをホストするためのアプリの種類。
   
- [**API Apps**](../app-service-api/app-service-api-apps-why-best-platform.md) - クラウド API をホストするためのアプリの種類。
 
- [**Logic Apps**](../app-service-logic/app-service-logic-what-are-logic-apps.md) - コードを記述することなく、クラウド全体のデータへのアクセスと使用を自動化するためのアプリの種類。

単一の統合サービスである App Service では、複数のアプリの種類を 1 つのソリューションへと簡単にまとめることができます。

## App Service プランと環境

[App Service プラン](azure-web-sites-web-hosting-plans-in-depth-overview.md)は、アプリを実行するために使用するコンピューティング リソースを表しています。下位の価格レベルでは、共有の仮想マシン (VM) でアプリを実行します。上位の価格レベルでは、専用の VM でアプリを実行します。複数の VM サイズから選ぶことができ、ダウンタイムなしで価格レベルを変更することもできます。より高い拡張性とネットワークの分離性が必要な場合は、[App Service Environment](../app-service-web/app-service-app-service-environment-intro.md) でアプリを実行することができます。

## App Service を使用する理由

App Service のいくつかの主要機能を次に示します。

- **完全に管理されたプラットフォーム** - OS とフレームワークへの修正プログラムの自動適用、バックアップと障害復旧のためのビルトイン サポート。 

- **ユーザーの既存スキルを活用** - 好みの言語、フレームワーク、開発環境でコーディングできます。App Service では、.NET、Node.js、Java、PHP、Python をサポートしています。

- **高速デプロイ** - 新しいアプリのプロビジョニングと、そのアプリへのコードのデプロイが数秒で完了します。

- **継続的インテグレーション** - [継続的インテグレーションとデプロイ](../app-service-web/app-service-continous-deployment.md)を、Visual Studio Team Services、GitHub、または BitBucket でセットアップできます。

- **ステージング環境とテスト環境** - [ステージングされたデプロイ](../app-service-web/web-sites-staged-publishing.md)を実装して、運用環境と同じ運用前環境でコードを検証できます。準備ができたら、スワップ操作を実行することにより、ダウンタイムなしで新しいバージョンのアプリをリリースします。

- **運用環境でのテスト** - ステージングされたデプロイを次のレベルに移行し、[A/B テストを実行](../app-service-web/app-service-web-test-in-production-get-start.md)して、構成可能な一部のライブ トラフィックで新しいコードを検証します。

- **認証と承認** - コードを変更することなく、アプリを認証されていないアクセスから保護できます。組み込みの認証サービスでは、ユーザー、ユーザーを表すクライアント、またはサービスからのアクセスに対してアプリをセキュリティで保護します。Azure Active Directory、Facebook、Twitter、Google、Microsoft アカウントなどが、ID プロバイダーとしてサポートされています。詳細については、「[Azure App Service での認証および承認](app-service-authentication-overview.md)」を参照してください。

- **任意のサービスへの接続** - 組み込みの[コネクタ](../connectors/apis-list.md)を使用して、アプリをエンタープライズ システムやサービスとしてのソフトウェア (SaaS) プラットフォームに数分で接続します。SAP、Siebel、Oracle などのエンタープライズ システムから Salesforce や Office 365 などの人気のエンタープライズ SaaS サービス、Facebook、Twitter、Dropbox などの人気のインターネット サービスまで、50 を超えるコネクタから選択できます。

- **グローバルなスケール** - 顧客からの受信負荷に対処するために、スケール[アップ](../app-service/app-service-scale.md)またはスケール[アウト](../azure-portal/insights-how-to-scale.md)を実行できます。VM の数とサイズを手動で選択するか、負荷またはスケジュールに応じた自動スケールを設定します。Microsoft のグローバル データセンター インフラストラクチャでアプリをホストするため、データやホスティング サービスを複数の場所で簡単にレプリケートできます。

- **エンタープライズ レベル** - App Service は、安全でミッション クリティカルなアプリケーションを構築およびホストすることを目的に設計されています。オンプレミスのリソースに安全に接続できる Active Directory 統合のビジネス アプリを構築し、それを [ISO、SOC、および PCI 準拠](https://www.microsoft.com/TrustCenter/)の、セキュリティで保護されたクラウド プラットフォーム上でホストします。また、すべてにおいてエンタープライズ レベルの [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) が提供されます。

- **Azure Marketplace** - 増え続ける[アプリケーション テンプレートのリスト](https://azure.microsoft.com/marketplace/)から選択できます。WordPress、Joomla、Drupal など、OSS アプリ コミュニティのベスト アプリを、ワンクリック インストールで利用できます。

- **WebJobs** - App Service VM で[任意のプログラムまたはスクリプトを実行](../app-service-web/web-sites-create-web-jobs.md)できます。スケジュールに従って、またはイベントにトリガーされて、継続的にジョブを実行します。Azure [WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md) を利用すると、他の Azure サービスやサードパーティ サービスと統合するために、記述するコードを単純化できます。

- **ハイブリッド接続** - [ハイブリッド接続](../biztalk-services/integration-hybrid-connection-overview.md)と [Azure Virtual Network](../app-service-web/web-sites-integrate-with-vnet.md) を利用して、オンプレミスのデータにアクセスできます。

- **Visual Studio の統合** - Visual Studio の専用ツールを使うと、Web アプリ、モバイル アプリ、API アプリを作成、デプロイ、使用、デバッグ、管理する作業が効率的になります。

## App Service の使用開始

今すぐ無料で[一時 Web アプリ、モバイル アプリ、またはロジック アプリを作成](http://go.microsoft.com/fwlink/?LinkId=523751)できます。クレジット カードや契約は不要で、手間もかかりません。

または、[無料 Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成し、次のいずれかの入門チュートリアルを試すこともできます。

* [Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/)
* [Mobile Apps](https://azure.microsoft.com/documentation/services/app-service/mobile/)
* [API Apps](https://azure.microsoft.com/documentation/services/app-service/api/)
* [Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/)

<!---HONumber=AcomDC_0601_2016-->