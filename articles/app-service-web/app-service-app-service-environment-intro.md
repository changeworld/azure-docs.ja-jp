<properties 
	pageTitle="App Service 環境の概要" 
	description="すべてのアプリを実行するためのセキュリティで保護され VNet に参加している専用のスケール ユニットを提供する、App Service 環境の機能について説明します。" 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="stefsh"/>

# App Service 環境の概要

## 概要 ##
App Service 環境は、すべてのアプリを安全に実行するために完全に分離された専用の環境を提供する、Azure App Service の [Premium][PremiumTier] サービス プラン オプションです。これには、拡張されたスケーリング オプションと共に [Web Apps][WebApps]、[Mobile Apps][MobileApps]、[API Apps][APIApps]、[Logic Apps][LogicApps] が含まれています。

App Service 環境のコンピューティング リソースは、お使いのアプリを実行するためだけに使用されます。App Service 環境は常に地域仮想ネットワーク内に作成されるため、アプリにはネットワーク分離用の新しいオプションが用意されます。さらに、App Service 環境は追加のスケーリング オプションをサポートしており、アプリの実行に最大 50 個のコンピューティング リソースを使用できます。App Service 環境の外部では、アプリをホストするコンピューティング リソースは 20 個に制限されています。

## 仮想ネットワークのサポート ##
App Service 環境は、既存の地域仮想ネットワーク内でも、新しい地域仮想ネットワーム内でも作成できます ([仮想ネットワークの詳細][MoreInfoOnVirtualNetworks])。App Service 環境は常に地域仮想ネットワーク (もっと正確に言えば、地域仮想ネットワークのサブネット内) に存在するため、仮想ネットワークのセキュリティ機能を使用して、受信と送信の両方のネットワーク通信を制御できます。

[ネットワーク セキュリティ グループ][NetworkSecurityGroups]を使用して、受信ネットワーク通信を、App Service 環境が存在するサブネットに制限できます。これによって、Web アプリケーション ファイアウォールやネットワーク SaaS プロバイダーなど、アップストリーム デバイスおよびサービスの背後でアプリを実行できます。

また、アプリは、内部データベースや Web サービスなどの企業リソースに頻繁にアクセスする必要があります。一般的な方法では、これらのエンドポイントを、Azure 仮想ネットワーク内を通過する内部ネットワーク トラフィックのみが使用できるようにします。App Service 環境が内部サービスと同じ仮想ネットワークに参加すると、その環境内で実行されているアプリは、[サイト間][SiteToSite]接続や [Azure ExpressRoute][ExpressRoute] 接続で到達可能なエンドポイントなど、内部サービスにアクセスできます。

## 専用のコンピューティング リソース ##
App Service 環境内のすべてのコンピューティング リソースは、1 つのサブスクリプション専用です。App Service 環境は、1 つのフロントエンド コンピューティング リソース プールと、1 ～ 3 個のワーカー コンピューティング リソース プールで構成されます。

フロントエンド プールには、SSL 終了と、App Service 環境内でのアプリ要求の自動負荷分散を担当するコンピューティング リソースが含まれます。

各ワーカー プールには、[App Service プラン][AppServicePlan]に割り当てられたコンピューティング リソースが含まれ、コンピューティング リソースには 1 つ以上の Azure App Service アプリが含まれます。App Service 環境には、異なるワーカー プールを 3 つまで配置できるため、ワーカー プールごとに異なるコンピューティング リソースを柔軟に選択できます。

たとえば、これにより、開発アプリまたはテスト アプリ向けの App Service プランでは、性能の低いコンピューティング リソースを含むワーカー プールを 1 つ作成できます。2 番目 (または 3 番目) のワーカー プールでは、実稼働アプリを実行する App Service プラン向けにより性能の高いコンピューティング リソースを使用することができます。

App Service 環境は、1 つのワーカー プールに最大 50 個のコンピューティング リソースを使用して構成できます。フロントエンドとワーカー プールで使用可能なコンピューティング リソースの量の詳細については、[App Service 環境の構成方法][HowToConfigureanAppServiceEnvironment]に関するページを参照してください。

App Service 環境でサポートされる使用可能なコンピューティング リソースのサイズの詳細については、[App Service の料金][AppServicePricing]のページで、Premium 価格レベルの App Service 環境で使用できるオプションを確認してください。


## 使用の開始

App Service 環境の使用を開始するには、[App Service 環境の作成方法][HowToCreateAnAppServiceEnvironment]に関するページを参照してください。

Azure App Service プラットフォームの詳細については、[Azure App Service][AzureAppService] に関するページを参照してください。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[MoreInfoOnVirtualNetworks]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[AppServicePlan]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[Azure preview portal]: http://portal.azure.com
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[LogicApps]: http://azure.microsoft.com/documentation/articles/app-service-logic-what-are-logic-apps/
[NetworkSecurityGroups]: https://msdn.microsoft.com/library/azure/dn848316.aspx
[SiteToSite]: https://msdn.microsoft.com/library/azure/dn133795.aspx
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[HowToConfigureanAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/

<!-- IMAGES -->

<!---HONumber=62-->