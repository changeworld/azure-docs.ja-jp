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
	ms.date="05/16/2016"
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

Web Apps の主要な機能を次に示します。

- **扱いやすく、高速** - 好みの言語、フレームワーク、統合開発環境 (IDE) で、既に持っているスキルを活用してコーディングします。数秒で新しい Web アプリをプロビジョニングし、それらにコードをデプロイできます。

- **エンタープライズ レベル** - Web Apps は、安全でミッション クリティカルなアプリケーションの構築およびホスト向けに設計されています。オンプレミス リソースに安全に接続する Active Directory 統合のビジネス アプリを構築し、それを [ISO](https://www.microsoft.com/TrustCenter/Compliance/ISO-IEC-27001)、[SOC](https://www.microsoft.com/TrustCenter/Compliance/SOC)、および [PCI](https://www.microsoft.com/TrustCenter/Compliance/pci) 準拠の、セキュリティで保護されたクラウド プラットフォーム上でホストします。すべてがエンタープライズ レベルの [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) を提供します。

- **グローバルなスケール** - Web Apps では、将来の顧客負荷に対処するため、スケール[アップ](../app-service/app-service-scale.md)やスケール[アウト](../azure-portal/insights-how-to-scale.md)をすばやく実行できます。VM の数とサイズを手動で選択するか、負荷またはスケジュールに応じた自動スケールを設定します。Microsoft のグローバル データセンター インフラストラクチャは、複数の場所で Web アプリをホストし、データとホスティング サービスを簡単にレプリケートできるようにします。

- **Azure Marketplace** - 常に増え続ける [Web アプリケーション テンプレートのリスト](https://azure.microsoft.com/marketplace/)から選択します。Wordpress、Joomla、Drupal など、OSS アプリ コミュニティのベスト アプリを、ワンクリック インストールで利用できます。

- **継続的インテグレーション** - [継続的インテグレーションとデプロイ](app-service-continous-deployment.md)のワークフローを Visual Studio Team Services、GitHub、または BitBucket でセットアップできます。コードのチェックインまたは統合テストが成功するごとに、Web アプリが自動的にビルド、テスト、およびデプロイされます。

- **ステージング環境とテスト環境** - [ステージングされたデプロイ](web-sites-staged-publishing.md)を実装して、運用環境と同じ運用前環境でコードを検証できます。準備ができたら、スワップ操作を実行することにより、ダウンタイムなしで新しいバージョンのアプリをリリースします。

- **運用環境でのテスト** - ステージングされたデプロイメントを次のレベルに移行し、[A/B テストを実行](app-service-web-test-in-production-get-start.md)して、構成可能な一部のライブ トラフィックで新しいコードを検証します。

- **WebJobs** - Web Apps VM で[任意のプログラムまたはスクリプトを実行](web-sites-create-web-jobs.md)できます。スケジュールに従って、またはイベントにトリガーされて、継続的にジョブを実行します。Azure [WebJobs SDK](websites-dotnet-webjobs-sdk-get-started.md) は、ストレージ キュー、BLOB、テーブルなどの他の Azure サービスや、Service Bus キューおよびトピックとの統合のために記述するコードを簡略化します。

- **ハイブリッド接続** - [ハイブリッド接続](../biztalk-services/integration-hybrid-connection-overview.md)と [VNET](../app-service-web/web-sites-integrate-with-vnet.md) を使用して、オンプレミスのデータにアクセスします。

- **Visual Studio の統合** - Visual Studio の専用ツールを使えば、Web アプリを作成、デプロイ、使用、デバッグ、管理する作業が効率的になります。詳細については、[Azure SDK 2.8.1 for .NET の発表に関するページ](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-1-for-net/)を参照してください。

さらに、Web アプリでは、[API Apps](../app-service-api/app-service-api-apps-why-best-platform.md) で提供される機能 (CORS のサポートなど) と [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) で提供される機能 (プッシュ通知など) を利用できます。その逆も当てはまります。つまり、API アプリまたはモバイル アプリを使用して、Web アプリケーションをホストし、自動スケーリング、ステージング デプロイなどの Web Apps 機能を利用することもできます。これらの 3 つの種類 (API、Web、モバイル) の異なる点は、Azure ポータルでこれらに対して使用する名前とアイコンのみです。App Service でのアプリの種類の詳細については、[Azure App Service の概要](../app-service/app-service-value-prop-what-is.md)に関するページを参照してください。

## 使用の開始

まず App Service で新しい Web アプリにサンプル コードをデプロイするには、「[5 分で初めての Web アプリを Azure にデプロイする](app-service-web-get-started.md)」チュートリアルに従ってください。無料の Azure アカウントが必要になります。

Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページにアクセスしてください。App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

<!---HONumber=AcomDC_0525_2016-->