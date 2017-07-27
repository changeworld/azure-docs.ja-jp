---
title: "Azure App Service Environment の概要"
description: "Azure App Service Environment の概要"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 3c7eaefa-1850-4643-8540-428e8982b7cb
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 0d078aefbf73a45298f397d02ab24b2c8232ecef
ms.contentlocale: ja-jp
ms.lasthandoff: 06/26/2017

---
# <a name="introduction-to-the-app-service-environment"></a>App Service Environment の概要 #
 
## <a name="overview"></a>概要 ##

App Service Environment は、Azure App Service アプリを大規模かつ安全に実行するために完全に分離された専用の環境を提供する、Azure App Service の機能です。 この機能は、[Web Apps][webapps]、[Mobile Apps][mobileapps]、[API Apps][APIapps]、および [Functions][Functions] をホストすることができます。

App Service Environment は、以下を必要とするアプリケーション ワークロードに最適です。

- 高スケール
- 分離およびセキュリティで保護されたネットワーク アクセス
- 高いメモリ使用率

顧客は、複数の App Service Environment を 1 つの Azure リージョン内に作成することも、複数の Azure リージョンにわたって作成することもできます。 そのため、App Service 環境は、高 RPS のワークロードをサポートするステートレス アプリケーション層の水平方向のスケーリングに最適です。

App Service 環境は、単一の顧客のアプリケーションだけを実行するために分離され、常に仮想ネットワークにデプロイされます。 顧客は、受信および送信アプリケーション ネットワーク トラフィックをきめ細かく制御できます。また、アプリケーションは、オンプレミスの企業リソースへの VPN 経由のセキュリティで保護された高速接続を確立できます。

App Service Environment に関するすべての記事と作業方法は [Application Service Environments の README][ASEReadme] を参照してください。

App Service Environment で高スケールおよびセキュリティで保護されたネットワーク アクセスを有効にする方法の概要については、App Service Environment の [AzureCon の詳細](https://azure.microsoft.com/documentation/videos/azurecon-2015-deploying-highly-scalable-and-secure-web-and-mobile-apps/)に関する記述を参照してください。

複数の App Service Environment を使用する水平方向のスケーリングの詳細については、[地理的に分散されたアプリのフットプリント](https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-geo-distributed-scale/)のセットアップ方法に関する記事を参照してください。

AzureCon Deep Dive に示されたセキュリティ アーキテクチャがどのように構成されたかを確認するには、App Service 環境での [レイヤード セキュリティ アーキテクチャ](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-layered-security) のインプリメントに関する記事を参照してください。

App Service 環境で実行されるアプリへのアクセスは、Web アプリケーション ファイアウォール (WAF) などのアップ ストリーム デバイスによって制限できます。 このシナリオについては、記事「 [App Service 環境の Web アプリケーション ファイアウォール (WAF) を構成する](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-app-service-environment-web-application-firewall) 」で説明します。

## <a name="dedicated-environment"></a>専用の環境 ##

App Service Environment は、1 つのサブスクリプション専用であり、100 のインスタンスをホストできます。 1 つの App Service プラン内の 100 のインスタンスから、100 の単一インスタンス App Service プラン、およびその間のすべてのプランがあります。

App Service Environment は、フロントエンドとワーカーで構成されます。 フロントエンドは、HTTP/HTTPS ターミネーションと、App Service Environment 内でのアプリ要求の自動負荷分散を担当します。 App Service Environment で App Service プランをスケール アウトすると、フロントエンドが自動的に追加されます。

ワーカーは、お客様のアプリをホストする役割です。 ワーカーは、3 つの固定サイズで使用できます。
* 1 コア/3.5 GB Ram
* 2 コア/7GB Ram
* 4 コア/14GB Ram

お客様は、フロントエンドやワーカーを管理する必要はありません。 すべてのインフラストラクチャは、お客様が App Service プランをスケール アウトしたときに自動的に追加されます。 App Service プランが App Service Environment で作成、拡大/縮小されるときに、必要なインフラストラクチャが適宜追加または削除されます。

インフラストラクチャの支払いを行うための App Service Environment の一定の月額料金があり、App Service Environment のサイズが変化しても料金は変わりません。 それに加えて、App Service プランのコアあたりのコストがあります。 App Service Environment でホストされているすべてのアプリは、分離された料金 SKU に含まれます。 App Service Environment の料金の詳細については、[App Service の料金][Pricing]のページで、App Service Environment で使用できるオプションを確認してください。

## <a name="virtual-network-support"></a>Virtual Network のサポート ##

App Service Environment は、Azure リソース マネージャーの仮想ネットワークでのみ作成できます。 Azure の仮想ネットワークの詳細については、[Azure 仮想ネットワークの FAQ](https://azure.microsoft.com/documentation/articles/virtual-networks-faq/) を参照してください。 App Service Environment は常に仮想ネットワーク (もっと正確に言えば、仮想ネットワークのサブネット内) に存在するため、仮想ネットワークのセキュリティ機能を使用して、アプリの受信と送信の両方のネットワーク通信を制御できます。

App Service Environment は、パブリック IP アドレスでインターネットに接続することも、Azure 内部ロード バランサー (ILB) アドレスだけで内部接続することもできます。

[ネットワーク セキュリティ グループ][NSGs]を使用して、受信ネットワーク通信を、App Service Environment が存在するサブネットに制限できます。 これによって、Web アプリケーション ファイアウォールやネットワーク SaaS プロバイダーなど、アップストリーム デバイスおよびサービスの背後でアプリを実行できます。

また、アプリは、内部データベースや Web サービスなどの企業リソースに頻繁にアクセスする必要があります。 オンプレミス ネットワークに VPN 接続されている Azure 仮想ネットワーク内に App Service Environment が展開された場合、App Service Environment 内のアプリは、オンプレミスのリソースにアクセスすることができます。 これは、VPN が[サイト対サイト](https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/)か [Azure ExpressRoute](http://azure.microsoft.com/services/expressroute/) VPN かに関係なく当てはまります。

App Service Environment と仮想ネットワークおよびオンプレミス ネットワークとの連携方法の詳細については、「[App Service Environment network consideration][ASENetwork]」(App Service Environment のネットワークに関する考慮事項) を参照してください。

## <a name="asev1"></a>ASEv1 ##

App Service Environment には、ASEv1 と ASEv2 の 2 つのバージョンがあります。 前述の情報は ASEv2 を中心としていました。 このセクションでは、ASEv1 と ASEv2 の違いについて説明します。 

ASEv1 では、すべてのリソースを手動で管理する必要があります。 これには、フロントエンド、worker、IP ベースの SSL に使用する IP アドレスが含まれます。 App Service プランをスケールアウトするには、そのプランをホストする worker プールを先にスケールアウトしておく必要があります。

ASEv1 では、ASEv2 とは異なる料金モデルを使用します。 ASEv1 では、割り当てられたコアごとに料金を支払う必要があります。 これには、フロントエンドまたはどのワークロードもホストしていない worker に使用されるコアが含まれます。 ASEv1 では、App Service Environment の既定の最大スケール サイズは合計で 55 ホストです。 これには、ワーカーとフロントエンドが含まれます。 ASEv1 の利点の 1 つは、クラシック仮想ネットワークと Resource Manager 仮想ネットワークにデプロイできることです。 ASEv1 の詳細については、「[App Service Environment の概要][ASEv1Intro]」を参照してください。

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md

