<properties 
	pageTitle="Web アプリとモバイル アプリのための Azure App Service | Microsoft Azure" 
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

*App Service* は、Microsoft Azure が提供する[サービスとしてのプラットフォーム](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) です。任意のプラットフォームまたはデバイスを対象として Web アプリとモバイル アプリを作成できます。作成したアプリと SaaS ソリューションの統合、オンプレミス アプリケーションへの接続、ビジネス プロセスの自動化を実行できます。Azure では、指定した共有仮想マシン (VM) リソースまたは専用 VM を使用して、完全に管理された VM 上で、アプリが実行されます。

App Service には、以前は Azure Websites および Azure Mobile Services として個別に提供されていた Web 機能とモバイル機能が含まれています。さらに、ビジネス プロセスの自動化やクラウド API のホストに利用できる新しい機能も備えています。単一の統合サービスである App Service では、さまざまなコンポーネント (Web サイト、モバイル アプリのバック エンド、RESTful API、ビジネス プロセス) を 1 つのソリューションにまとめることができます。

次の 4 分間のビデオでは、App Service と以前の Azure サービスとの関連と App Service の新機能について簡単に説明しています。

\+[AZURE.VIDEO app-service-history-lesson] 

## App Service を使用する理由

App Service のいくつかの主要機能を次に示します。

- **複数の言語とフレームワーク** - App Service は、ASP.NET、Node.js、Java、PHP、Python を最高レベルでサポートしています。また、App Service VM では、[Windows PowerShell などのスクリプトや実行可能ファイル](../app-service-web/web-sites-create-web-jobs.md)を実行することもできます。

- **DevOps の最適化** - [継続的インテグレーションとデプロイ](../app-service-web/app-service-continuous-deployment.md)を、Visual Studio Team Services、GitHub、または BitBucket でセットアップできます。[テスト環境やステージング環境](../app-service-web/web-sites-staged-publishing.md)を介して更新を反映できます。また、[A/B テスト](../app-service-web/app-service-web-test-in-production-get-start.md)を実行できます。App Service でのアプリの管理には、[Azure PowerShell](../powershell-install-configure.md) または[クロスプラットフォーム コマンド ライン インターフェイス (CLI)](../xplat-cli-install.md) を使用します。
 
- **高可用性を備えたグローバルなスケール** - 手動または自動で[スケールアップ](../app-service-web/web-sites-scale.md)または[スケールアウト](../azure-portal/insights-how-to-scale.md)を実行できます。Microsoft のグローバルなデータセンター インフラストラクチャのどこででもアプリをホストでき、App Service の [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) によって高可用性が保証されます。

- **SaaS プラットフォームおよびオンプレミス データへの接続** - エンタープライズ システム (SAP、Siebel、Oracle など)、SaaS サービス (Salesforce や Office 365 など)、インターネット サービス (Facebook や Twitter など) 向けに用意された 50 を超える[コネクタ](../connectors/apis-list.md)から選択できます。また、[ハイブリッド接続](../biztalk-services/integration-hybrid-connection-overview.md)と [Azure Virtual Network](../app-service-web/web-sites-integrate-with-vnet.md) を利用して、オンプレミスのデータにアクセスできます。

- **セキュリティとコンプライアンス** - App Service は [ISO、SOC、および PCI に準拠](https://www.microsoft.com/TrustCenter/)しています。

- **アプリケーション テンプレート** - [Azure Marketplace](https://azure.microsoft.com/marketplace/) にある詳細な一覧からアプリケーション テンプレートを選択し、ウィザードを通じて、WordPress、Joomla、Drupal などの広く普及しているオープン ソース ソフトウェアをインストールできます。

- **Visual Studio の統合** - Visual Studio の専用ツールを使えば、作成、デプロイ、デバッグの作業が効率的になります。

## App Service でのアプリの種類

App Service には、*アプリの種類*が複数用意されており、それぞれ特定のタイプのワークロードをホストするよう設計されています。

- [**Web Apps**](../app-service-web/app-service-web-overview.md) - Web サイトと Web アプリケーションをホストするためのアプリの種類。

- [**Mobile Apps**](../app-service-mobile/app-service-mobile-value-prop.md) - モバイル アプリのバック エンドをホストするためのアプリの種類。
   
- [**API Apps**](../app-service-api/app-service-api-apps-why-best-platform.md) - クラウド API をホストするためのアプリの種類。
 
- [**Logic Apps**](../app-service-logic/app-service-logic-what-are-logic-apps.md) - コードを記述することなく、クラウド全体のデータへのアクセスと使用を自動化するためのアプリの種類。

ここでの*アプリ*という言葉は、ワークロードの実行専用のホスティング リソースを指しています。"Web アプリ" を例にとると、Web アプリをコンピューティング リソースとアプリケーション コードの両方の観点から捉えることに皆さんおそらく慣れていることでしょう。この両者が合わさって、ブラウザーに機能が提供されます。しかし、App Service の *Web アプリ*とは、アプリケーション コードをホストするために Azure が提供するコンピューティング リソースです。アプリケーションが Web フロント エンドと RESTful API バック エンドで構成されている場合は、両方を Web アプリにデプロイすることも、フロント エンドのコードを Web アプリに、バック エンドのコードを API アプリにデプロイすることもできます。アプリケーションは、さまざまな種類の複数の App Service アプリで構成することもできます。

## App Service プラン

[App Service プラン](azure-web-sites-web-hosting-plans-in-depth-overview.md)には、アプリを実行するために使用するコンピューティング リソースの種類が指定されています。トラフィックの負荷が軽いと予想される場合は、共有仮想マシン (VM) を使用できます。負荷が高い場合は、複数のサイズが用意された専用 VM から選択できます。複数の App Service アプリで同じプランを共有できます。スケールアップやスケールダウンはプランを維持したまま実行できます。

より高い拡張性とネットワークの分離性が必要な場合は、[App Service Environment](../app-service-web/app-service-app-service-environment-intro.md) でアプリを実行することができます。

## 価格

App Service の価格の詳細については、「[App Service の価格](https://azure.microsoft.com/pricing/details/app-service/)」を参照してください。

## App Service の使用開始

今すぐ無料で[一時 Web アプリ、モバイル アプリ、またはロジック アプリを作成](http://go.microsoft.com/fwlink/?LinkId=523751)できます。クレジット カードや契約は不要で、手間もかかりません。

または、[無料 Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成し、次のいずれかの入門チュートリアルを試すこともできます。

* [チュートリアル: Web アプリの作成](../app-service-web/app-service-web-get-started.md)
* [チュートリアル: モバイル アプリの作成](../app-service-mobile/app-service-mobile-android-get-started.md)
* [チュートリアル: API アプリの作成](../app-service-api/app-service-api-dotnet-get-started.md)
* [チュートリアル: ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0803_2016-->