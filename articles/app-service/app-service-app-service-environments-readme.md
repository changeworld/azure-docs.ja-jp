<properties 
	pageTitle="Azure App Service Environment" 
	description="App Service の特長について説明します。" 
	keywords="App Service Environment, Azure App Service Environment"
	services="app-service" 
	documentationCenter="" 
	authors="yochay" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2016" 
	ms.author="stefsch"/>

# 概要

App Service 環境は、Azure App Service アプリを高スケールで安全に実行するために完全に分離された専用の環境を提供する、Azure App Service の [Premium][PremiumTier] サービス プラン オプションです。App Service 環境には、[Web Apps][WebApps]、[Mobile Apps][MobileApps]、[API Apps][APIApps] が含まれています。

App Service 環境は、以下を必要とするアプリケーション ワークロードに最適です。

- 高スケール
- 分離およびセキュリティで保護されたネットワーク アクセス

顧客は、複数の App Service 環境を 1 つの Azure リージョン内に作成することも、複数の Azure リージョンにわたって作成することもできます。そのため、App Service 環境は、高 RPS のワークロードをサポートするステートレス アプリケーション層の水平方向のスケーリングに最適です。

App Service 環境は、単一の顧客のアプリケーションだけを実行するために分離され、常に仮想ネットワークにデプロイされます。顧客は、[ネットワーク セキュリティ グループ][NetworkSecurityGroups]を使用することで、受信と送信の両方のアプリケーション ネットワーク トラフィックをきめ細かく制御することができます。また、アプリケーションは仮想ネットワークを介した、オンプレミスの企業リソースへのセキュリティで保護された高速な接続を確立することができます。

アプリは、内部データベースや Web サービスなどの企業リソースに頻繁にアクセスする必要があります。App Service Environment で動作するアプリは、[サイト間][SiteToSite] VPN 接続や [Azure ExpressRoute][ExpressRoute] 接続を介してリソースにアクセスすることができます。

* [App Service 環境とは](../app-service-web/app-service-app-service-environment-intro.md)
* [App Service 環境の作成](../app-service-web/app-service-web-how-to-create-an-app-service-environment.md)
* [App Service 環境でのアプリの作成](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [App Service 環境の構成](../app-service-web/app-service-web-configure-an-app-service-environment.md) 
* [App Service 環境でのアプリのスケーリング](../app-service-web/app-service-web-scale-a-web-app-in-an-app-service-environment.md)
* [ネットワークのセキュリティとアーキテクチャ](../app-service-web/app-service-app-service-environment-network-architecture-overview.md)

## 方法

[AZURE.INCLUDE [app-service-blueprint-app-service-environment](../../includes/app-service-blueprint-app-service-environment.md)]


## ビデオ
[AZURE.VIDEO azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps]

[AZURE.VIDEO microsoft-ignite-2015-running-enterprise-web-and-mobile-apps-on-azure-app-service]


<!-- LINKS -->
[PremiumTier]: http://azure.microsoft.com/pricing/details/app-service/
[WebApps]: http://azure.microsoft.com/documentation/articles/app-service-web-overview/
[MobileApps]: http://azure.microsoft.com/documentation/articles/app-service-mobile-value-prop-preview/
[APIApps]: http://azure.microsoft.com/documentation/articles/app-service-api-apps-why-best-platform/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/

<!---HONumber=AcomDC_0224_2016-->