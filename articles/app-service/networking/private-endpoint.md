---
title: Azure プライベート エンドポイントを使用して非公開で Web アプリに接続する
description: Azure プライベート エンドポイントを使用して非公開で Web アプリに接続する
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 05/25/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: 4c48a2fad927812cc45543243b48a2df81acf73b
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846955"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Azure Web アプリでのプライベート エンドポイントの使用 (プレビュー)

> [!Note]
> プレビューの更新により、データ窃盗からの保護機能がリリースされました。
> このプレビューは、PremiumV2 の Windows および Linux の Web アプリとエラスティック Premium 関数すべてに対して、米国東部と米国西部 2 のリージョンでご利用いただけます。 

Azure Web アプリにプライベート エンドポイントを使用すると、プライベート ネットワーク内のクライアントが Private Link 経由で安全にアプリにアクセスできるようになります。 プライベート エンドポイントは、Azure VNet アドレス空間からの IP アドレスを使用します。 プライベート ネットワーク上のクライアントと Web アプリ間のネットワーク トラフィックは、VNet および Microsoft バックボーン ネットワーク上の Private Link を経由することで、パブリック インターネットにさらされないようにします。

Web アプリにプライベート エンドポイントを使用することで、次のことが可能になります。

- プライベート エンドポイントを構成することにより Web アプリをセキュリティで保護し、公開されないようにする。
- VPN または ExpressRoute のプライベート ピアリングを使用して VNet に接続するオンプレミス ネットワークから Web アプリに安全に接続する。
- VNet からデータが一切流出しないようにしてください。 

単に、VNet と Web アプリ間のセキュリティで保護された接続が必要な場合、サービス エンドポイントが最も簡単なソリューションです。 さらに、Azure ゲートウェイ、リージョンでピアリングされた VNet、またはグローバルにピアリングされた VNet を介してオンプレミスから Web アプリに接続する必要がある場合は、プライベート エンドポイントがソリューションになります。  

詳細については、 [サービス エンドポイント][serviceendpoint]に関するページを参照してください。

## <a name="conceptual-overview"></a>概念の概要

プライベート エンドポイントは、仮想ネットワーク (VNet) 内のサブネットにある Azure Web アプリ用の特別なネットワーク インターフェイス (NIC) です。
Web アプリのプライベート エンドポイントを作成すると、プライベート ネットワーク上のクライアントと Web アプリ間の安全な接続が提供されます。 プライベート エンドポイントには、VNet の IP アドレス範囲から IP アドレスが割り当てられます。
プライベート エンドポイントと Web アプリ間の接続には、セキュリティで保護された [Private Link][privatelink] が使用されます。 プライベート エンドポイントは、Web アプリへの受信フローにのみ使用されます。 送信フローではこのプライベート エンドポイントは使用されませんが、[VNet 統合機能][vnetintegrationfeature]を使用して、別のサブネット内のネットワークに送信フローを挿入できます。

プライベート エンドポイントを接続するサブネットには他のリソースを含めることができます。専用の空のサブネットは必要ありません。
また、プライベート エンドポイントを Web アプリとは別のリージョンにデプロイすることもできます。 

> [!Note]
>VNet 統合機能は、プライベート エンドポイントと同じサブネットを使用することはできません。これは、VNet 統合機能の制限です。

セキュリティの観点から:

- Web アプリに対してプライベート エンドポイントを有効にすると、すべてのパブリック アクセスは無効になります。
- 他のリージョン内の VNet を含め、他の VNet およびサブネットで複数のプライベート エンドポイントを有効にすることができます。
- プライベート エンドポイントの NIC の IP アドレスは動的である必要がありますが、そのプライベート エンドポイントを削除するまでは変わりません。
- プライベート エンドポイントの NIC に NSG を関連付けることはできません。
- プライベート エンドポイントをホストするサブネットには NSG を関連付けることができますが、プライベート エンドポイントに対するネットワーク ポリシーの適用を無効にする必要があります。「[プライベート エンドポイントのネットワーク ポリシーを無効にする][disablesecuritype]」を参照してください。 このため、プライベート エンドポイントへのアクセスを NSG でフィルタに掛けることはできません。
- Web アプリに対してプライベート エンドポイントを有効にすると、Web アプリの[アクセス制限][accessrestrictions]構成は評価されません。
- 宛先が "インターネット" または "Azure サービス" になっているすべての NSG ルールを削除することによって、VNet からのデータ流出リスクをなくすことができます。 Web アプリのプライベート エンドポイントをデプロイするときに、この特定の Web アプリには、このプライベート エンドポイントからのみできます。 別の Web アプリがある場合は、この別の Web アプリの別の専用プライベート エンドポイントをデプロイする必要があります。

Web アプリの Web HTTP ログには、クライアントのソース IP が含まれています。 これは、TCP プロキシ プロトコルを使用して実装されており、Web アプリにクライアント IP プロパティを転送します。 詳細については、「[TCP プロキシ v2 を使用した接続情報の取得][tcpproxy]」を参照してください。


  > [!div class="mx-imgBorder"]
  > ![Web アプリのプライベート エンドポイントの全体像](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

この機能はプレビュー段階であるため、プレビュー期間中は DNS エントリを変更しません。 DNS エントリは、プライベート DNS サーバーまたは Azure DNS プライベート ゾーンでご自身で管理する必要があります。
カスタム DNS 名を使用する必要がある場合は、カスタム名を Web アプリに追加する必要があります。 プレビュー期間中は、すべてのカスタム名と同様に、パブリック DNS 解決を使用してカスタム名を検証する必要があります。 詳細については、[カスタム DNS 検証][dnsvalidation]に関するページをご覧ください。

Kudu コンソールまたは Kudu REST API (Azure DevOps セルフホステッド エージェントを使用したデプロイなど) を使用する必要がある場合は、Azure DNS プライベート ゾーンまたはカスタム DNS サーバーに 2 つのレコードを作成する必要があります。 
- PrivateEndpointIP yourwebappname.azurewebsites.net 
- PrivateEndpointIP yourwebappname.scm.azurewebsites.net 

## <a name="pricing"></a>価格

料金の詳細については、「[Azure Private Link の料金][pricing]」をご覧ください。

## <a name="limitations"></a>制限事項

Elastic Premium プランの Azure Function をプライベート エンドポイントとともに使用する場合、Azure Web ポータルで関数を実行するには、直接ネットワークにアクセスする必要があります。そうでないと、HTTP 403 エラーが発生します。 つまり、Azure Web ポータルから関数を実行するには、お使いのブラウザーで、プライベート エンドポイントに到達できる必要があります。 

プレビュー期間中は、運用スロットのみがプライベート エンドポイントの背後で公開され、他のスロットにはパブリック エンドポイントから接続する必要があります。

定期的に Private Link 機能とプライベート エンドポイントの改善を行っています。制限に関する最新情報については、[こちらの記事][pllimitations]を確認してください。

## <a name="next-steps"></a>次のステップ

ポータルを使用して Web アプリのプライベート エンドポイントをデプロイするには、[Web アプリに非公開で接続する方法][howtoguide]に関する記事を参照してください。




<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
