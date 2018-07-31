---
title: Web Apps の概要 | Microsoft Docs
description: Azure App Service が Web アプリケーションの開発およびホストにどのように役立つかについて説明します
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/04/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 68c3306656ade6ce95a3f18fec19de32bd9cf319
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170839"
---
# <a name="web-apps-overview"></a>Web Apps の概要

*App Service Web Apps* (Web Apps) は、Web アプリケーション、REST API、およびモバイル バックエンドをホストするためのサービスです。 開発には、.NET、.NET Core、Java、Ruby、Node.js、PHP、Python のうち、お気に入りの言語をご利用いただけます。 アプリケーションの実行とスケーリングは、Windows ベースの環境で容易に行えます。 Linux ベースの環境については、[App Service on Linux](containers/app-service-linux-intro.md) に関するページを参照してください。 

Web Apps は、セキュリティ、負荷分散、自動スケール、自動管理などの Microsoft Azure の機能を、アプリケーションに追加するだけではありません。 VSTS、GitHub、Docker Hub およびその他のソースからの継続的なデプロイ、パッケージ管理、ステージング環境、カスタム ドメイン、SSL 証明書など、DevOps 機能を利用することもできます。 

App Service では、使用した Azure コンピューティング リソースに応じて課金されます。 コンピューティング リソースの使用は、Web Apps が実行されている "_App Service プラン_" によって決まります。 詳細については、[Azure Web Apps の App Service プラン](azure-web-sites-web-hosting-plans-in-depth-overview.md)に関するページをご覧ください。

## <a name="why-use-web-apps"></a>Web Apps を使用する理由

App Service Web Apps の主な機能をいくつか次に示します。

* **複数の言語とフレームワーク** - Web Apps は、ASP.NET、ASP.NET Core、Java、Ruby、Node.js、PHP、または Python を最高レベルでサポートしています。 また、[PowerShell などのスクリプトや実行可能ファイル](web-sites-create-web-jobs.md)をバックグラウンド サービスとして実行することもできます。
* **DevOps の最適化** - [継続的インテグレーションとデプロイ](app-service-continuous-deployment.md)を、Visual Studio Team Services、GitHub、BitBucket、Docker Hub、または Azure Container Registry で設定できます。 [テスト環境やステージング環境](web-sites-staged-publishing.md)を介して更新を反映できます。 Web Apps でのアプリの管理には、[Azure PowerShell](/powershell/azureps-cmdlets-docs) または[クロスプラットフォーム コマンド ライン インターフェイス (CLI)](/cli/azure/install-azure-cli) を使用します。
* **高可用性を備えたグローバルなスケール** - 手動または自動で[スケールアップ](web-sites-scale.md)または[スケールアウト](../monitoring-and-diagnostics/insights-how-to-scale.md)を実行できます。 Microsoft のグローバルなデータセンター インフラストラクチャのどこででもアプリをホストでき、App Service の [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) によって高可用性が保証されます。
* **SaaS プラットフォームおよびオンプレミス データへの接続** - エンタープライズ システム (SAP など)、SaaS サービス (Salesforce など)、インターネット サービス (Facebook など) 向けに用意された 50 を超える[コネクタ](../connectors/apis-list.md)から選択できます。 また、[ハイブリッド接続](../biztalk-services/integration-hybrid-connection-overview.md)と [Azure Virtual Networks](web-sites-integrate-with-vnet.md) を利用して、オンプレミスのデータにアクセスできます。
* **セキュリティとコンプライアンス** - App Service は [ISO、SOC、および PCI に準拠](https://www.microsoft.com/en-us/trustcenter)しています。 [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md) またはソーシャル ログイン ([Google](app-service-mobile-how-to-configure-google-authentication.md)、[Facebook](app-service-mobile-how-to-configure-facebook-authentication.md)、[Twitter](app-service-mobile-how-to-configure-twitter-authentication.md)、および[Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)) でユーザーを認証します。 [IP アドレス制限](app-service-ip-restrictions.md)を作成し、[サービス ID を管理](app-service-managed-service-identity.md)します。
* **アプリケーション テンプレート** - WordPress、Joomla、Drupal など、[Azure Marketplace](https://azure.microsoft.com/marketplace/) にある詳細な一覧からアプリケーション テンプレートを選択します。
* **Visual Studio の統合** - Visual Studio の専用ツールを使えば、作成、デプロイ、デバッグの作業が効率的になります。
* **API とモバイル機能** - Web Apps は、RESTful API シナリオに対してターンキー CORS サポートを提供するほか、認証、オフライン データ同期、プッシュ通知などを有効にして、モバイル アプリのシナリオを簡素化します。
* **サーバーレス コード** - コード スニペットまたはスクリプトをオンデマンドで実行します。その際、インフラストラクチャを明示的にプロビジョニングまたは管理する必要はありません。また、コードによって実際に使用されたコンピューティング時間のみが課金対象となります ([Azure Functions](/azure/azure-functions/) に関するページを参照)。

App Service の Web Apps に加え、Azure では Web サイトと Web アプリケーションをホストするために利用できるサービスを他にも提供しています。 ほとんどの場合は、Web Apps が最適な方法になります。  マイクロサービス アーキテクチャについては、[Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric) を検討してください。 また、コードの実行に使用する VM をより細かく制御する必要がある場合は、[Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/) の利用をご検討ください。 これらの Azure サービスから適切なサービスを選択する方法の詳細については、「[Azure App Service、Virtual Machines、Service Fabric、Cloud Services の比較](choose-web-site-cloud-service-vm.md)」を参照してください。

## <a name="next-steps"></a>次の手順

最初の Web アプリを作成する。

> [!div class="nextstepaction"]
> [ASP.NET Core](app-service-web-get-started-dotnet.md)

> [!div class="nextstepaction"]
> [ASP.NET](app-service-web-get-started-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP](app-service-web-get-started-php.md)

> [!div class="nextstepaction"]
> [Ruby (Linux の場合)](containers/quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js](app-service-web-get-started-nodejs.md)

> [!div class="nextstepaction"]
> [Java](app-service-web-get-started-java.md)

> [!div class="nextstepaction"]
> [Python (Linux 版)](containers/quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML](app-service-web-get-started-html.md)
