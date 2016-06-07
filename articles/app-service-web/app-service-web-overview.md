<properties
	pageTitle="Web アプリの概要 | Microsoft Azure"
	description="Azure App Service が Web アプリケーションの開発およびホストにどのように役立つかについて説明します"
	services="app-service\web"
	documentationCenter=""
	authors="jaime-espinosa"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/25/2016"
	ms.author="tdykstra"/>

# Web Apps の概要

*App Service Web Apps* は、Web サイトと Web アプリケーションをホストするために最適化された、完全に管理されたコンピューティング プラットフォームです。Microsoft Azure のこの[サービスとしてのプラットフォーム](https://en.wikipedia.org/wiki/Platform_as_a_service) (PaaS) サービスを使用すると、アプリの実行とスケーリングのためのインフラストラクチャは Azure で管理されるため、お客様はビジネス ロジックに集中できます。

「[Azure App Service Web Apps with Yochay Kiriaty (Yochay Kiriaty による Azure App Service Web Apps)](https://azure.microsoft.com/documentation/videos/azure-app-service-web-apps-with-yochay-kiriaty/)」で、5 分間の概要ビデオをご覧いただけます。

## App Service の Web アプリとは

App Service の *Web アプリ*とは、Web サイトまたは Web アプリケーションをホストするために Azure が提供するコンピューティング リソースです。

コンピューティング リソースは、選択した価格レベルに応じて、共有または専用の仮想マシン (VM) 上に配置されます。アプリケーション コードは、他のお客様から分離された管理 VM で実行されます。

コードは、ASP.NET、Node.js、Java、PHP、Python など、[Azure App Service](../app-service/app-service-value-prop-what-is.md) でサポートされている任意の言語またはフレームワークで作成できます。また、Web アプリでは、[PowerShell やその他のスクリプト言語](web-sites-create-web-jobs.md#acceptablefiles)を使用するスクリプトを実行することもできます。

Web Apps を使用できる一般的なアプリケーション シナリオの例については、「[Web アプリのシナリオ](https://azure.microsoft.com/documentation/scenarios/web-app/)」を参照してください。

## Web Apps を使用する理由

Web Apps に適用されるいくつかの App Service の主要機能を次に示します。

- **完全に管理されたプラットフォーム** - OS とフレームワークへの修正プログラムの自動適用、バックアップと障害復旧のためのビルトイン サポート。 

- **ユーザーの既存スキルを活用** - 好みの言語、フレームワーク、開発環境でコーディングできます。App Service では、.NET、Node.js、Java、PHP、Python をサポートしています。

- **高速デプロイ** - 新しいアプリのプロビジョニングと、そのアプリへのコードのデプロイが数秒で完了します。

- **継続的インテグレーション** - [継続的インテグレーションとデプロイ](../app-service-web/app-service-continous-deployment.md)を、Visual Studio Team Services、GitHub、または BitBucket でセットアップできます。

- **ステージング環境とテスト環境** - [ステージングされたデプロイ](../app-service-web/web-sites-staged-publishing.md)を実装して、運用環境と同じ運用前環境でコードを検証できます。準備ができたら、スワップ操作を実行することにより、ダウンタイムなしで新しいバージョンのアプリをリリースします。

- **運用環境でのテスト** - ステージングされたデプロイを次のレベルに移行し、[A/B テストを実行](../app-service-web/app-service-web-test-in-production-get-start.md)して、構成可能な一部のライブ トラフィックで新しいコードを検証します。

- **認証と承認** - コードを変更することなく、アプリを認証されていないアクセスから保護できます。組み込みの認証サービスでは、ユーザー、ユーザーを表すクライアント、またはサービスからのアクセスに対してアプリをセキュリティで保護します。Azure Active Directory、Facebook、Twitter、Google、Microsoft アカウントなどが、ID プロバイダーとしてサポートされています。詳細については、「[Azure App Service での認証および承認](../app-service/app-service-authentication-overview.md)」を参照してください。

- **任意のサービスへの接続** - 組み込みの[コネクタ](../connectors/apis-list.md)を使用して、アプリをエンタープライズ システムやサービスとしてのソフトウェア (SaaS) プラットフォームに数分で接続します。SAP、Siebel、Oracle などのエンタープライズ システムから Salesforce や Office 365 などの人気のエンタープライズ SaaS サービス、Facebook、Twitter、Dropbox などの人気のインターネット サービスまで、50 を超えるコネクタから選択できます。

- **グローバルなスケール** - 顧客からの受信負荷に対処するために、スケール[アップ](../app-service/app-service-scale.md)またはスケール[アウト](../azure-portal/insights-how-to-scale.md)を実行できます。VM の数とサイズを手動で選択するか、負荷またはスケジュールに応じた自動スケールを設定します。Microsoft のグローバル データセンター インフラストラクチャでアプリをホストするため、データやホスティング サービスを複数の場所で簡単にレプリケートできます。

- **エンタープライズ レベル** - App Service は、安全でミッション クリティカルなアプリケーションを構築およびホストすることを目的に設計されています。オンプレミスのリソースに安全に接続できる Active Directory 統合のビジネス アプリを構築し、それを [ISO、SOC、および PCI 準拠](https://www.microsoft.com/TrustCenter/)の、セキュリティで保護されたクラウド プラットフォーム上でホストします。また、すべてにおいてエンタープライズ レベルの [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) が提供されます。

- **Azure Marketplace** - 増え続ける[アプリケーション テンプレートのリスト](https://azure.microsoft.com/marketplace/)から選択できます。WordPress、Joomla、Drupal など、OSS アプリ コミュニティのベスト アプリを、ワンクリック インストールで利用できます。

- **WebJobs** - App Service VM で[任意のプログラムまたはスクリプトを実行](../app-service-web/web-sites-create-web-jobs.md)できます。スケジュールに従って、またはイベントにトリガーされて、継続的にジョブを実行します。Azure [WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk.md) を利用すると、他の Azure サービスやサードパーティ サービスと統合するために、記述するコードを単純化できます。

- **ハイブリッド接続** - [ハイブリッド接続](../biztalk-services/integration-hybrid-connection-overview.md)と [Azure Virtual Network](../app-service-web/web-sites-integrate-with-vnet.md) を利用して、オンプレミスのデータにアクセスできます。

- **Visual Studio の統合** - Visual Studio の専用ツールを使うと、Web アプリ、モバイル アプリ、API アプリを作成、デプロイ、使用、デバッグ、管理する作業が効率的になります。

さらに、Web アプリでは、[API Apps](../app-service-api/app-service-api-apps-why-best-platform.md) で提供される機能 (CORS のサポートなど) と [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) で提供される機能 (プッシュ通知など) を利用できます。これらの 3 つの種類 (API、Web、モバイル) の異なる点は、Azure ポータルでこれらに対して使用する名前とアイコンのみです。App Service でのアプリの種類の詳細については、[Azure App Service の概要](../app-service/app-service-value-prop-what-is.md)に関するページを参照してください。

App Service の Web Apps に加え、Azure では Web サイトと Web アプリケーションをホストするために利用できるサービスを他にも提供しています。ほとんどの場合は、Web Apps が最適な方法になります。マイクロサービス アーキテクチャ向けには、[Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric) を検討してください。また、コードの実行に使用する VM をより細かく制御する必要がある場合は、[Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) をご検討ください。これらの Azure サービスから適切なサービスを選択する方法の詳細については、「[Azure App Service、Cloud Services、Virtual Machines、および Service Fabric の比較](choose-web-site-cloud-service-vm.md)」を参照してください。

## 使用の開始

まず App Service で新しい Web アプリにサンプル コードをデプロイするには、チュートリアル「[5 分で初めての Web アプリを Azure にデプロイする](app-service-web-get-started.md)」に従ってください。無料の Azure アカウントが必要になります。

Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

<!---HONumber=AcomDC_0601_2016-->