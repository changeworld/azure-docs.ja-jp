---
title: Azure プライベート エンドポイントを使用して非公開で Web アプリに接続する
description: Azure プライベート エンドポイントを使用して非公開で Web アプリに接続する
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 893a7a2c7483fccc3bbc7bd198929f65917457b3
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2020
ms.locfileid: "79036947"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Azure Web アプリでのプライベート エンドポイントの使用 (プレビュー)

Azure Web アプリにプライベート エンドポイントを使用して、プライベート ネットワーク上のクライアントが Private Link 経由で安全にアプリにアクセスできるようにすることができます。 プライベート エンドポイントは、Azure VNet アドレス空間からの IP アドレスを使用します。 プライベート ネットワーク上のクライアントと Web アプリ間のネットワーク トラフィックは、Vnet および Microsoft バックボーン ネットワーク上の Private Link を経由することで、パブリック インターネットにさらされないようにします。

Web アプリにプライベート エンドポイントを使用することで、次のことが可能になります。

- サービス エンドポイントを構成することにより Web アプリをセキュリティで保護し、公開されないようにする
- VPN または ExpressRoute のプライベート ピアリングを使用して VNet に接続するオンプレミス ネットワークから Web アプリに安全に接続する。

単に、Vnet と Web アプリ間のセキュリティで保護された接続が必要な場合、サービス エンドポイントが最も簡単なソリューションです。 さらに、Azure ゲートウェイ、リージョンでピアリングされた Vnet、またはグローバルにピアリングされた Vnet を介してオンプレミスから Web アプリに接続する必要がある場合は、プライベート エンドポイントがソリューションになります。  

[サービス エンドポイント][serviceendpoint]の詳細情報

## <a name="conceptual-overview"></a>概念の概要

プライベート エンドポイントは、仮想ネットワーク (VNet) 内のサブネットにある Azure Web アプリ用の特別なネットワーク インターフェイス (NIC) です。
Web アプリのプライベート エンドポイントを作成すると、プライベート ネットワーク上のクライアントと Web アプリ間の安全な接続が提供されます。 プライベート エンドポイントには、VNet の IP アドレス範囲から IP アドレスが割り当てられます。
プライベート エンドポイントと Web アプリ間の接続には、セキュリティで保護された [Private Link][privatelink] が使用されます。 プライベート エンドポイントは、Web アプリへの受信フローにのみ使用されます。 送信フローではこのプライベート エンドポイントは使用されませんが、[Vnet 統合機能][vnetintegrationfeature]を使用して、別のサブネット内のネットワークに送信フローを挿入できます。

プライベート エンドポイントを接続するサブネットには他のリソースを含めることができます。専用の空のサブネットは必要ありません。
プライベート エンドポイントは、Web アプリとは別のリージョンにデプロイできます。 

> [!Note]
>Vnet 統合機能は、プライベート エンドポイントと同じサブネットを使用することはできません。これは、Vnet 統合機能の制限です。

セキュリティの観点から:

- Web アプリに対してサービス エンドポイントを有効にすると、すべてのパブリック アクセスは無効になります
- 他のリージョン内の Vnet を含め、他の Vnet およびサブネットで複数のプライベート エンドポイントを有効にすることができます
- プライベート エンドポイントの NIC の IP アドレスは動的である必要がありますが、そのプライベート エンドポイントを削除するまでは変わりません
- プライベート エンドポイントの NIC に NSG を関連付けることはできません
- プライベート エンドポイントをホストするサブネットには NSG を関連付けることができますが、プライベート エンドポイントに対するネットワーク ポリシーの適用を無効にする必要があります。[こちらの記事][disablesecuritype]を参照してください。 このため、プライベート エンドポイントへのアクセスを NSG でフィルタに掛けることはできません。
- Web アプリに対してプライベート エンドポイントを有効にすると、Web アプリの[アクセス制限][accessrestrictions]構成は評価されません。
- 宛先が "インターネット" または "Azure サービス" になっているすべての NSG ルールを削除することによって、Vnet からのデータ流出リスクを減らすことができます。 しかし、サブネットに Web アプリのサービス エンドポイントを追加すると、同じスタンプでホストされ、インターネットに公開されているどの Web アプリにもアクセスできます。

Web アプリのプライベート エンドポイントは PremiumV2 レベルで使用でき、外部 ASE で分離されています。

Web アプリの Web HTTP ログには、クライアントのソース IP が含まれています。 TCP プロキシ プロトコルを実装しており、Web アプリにクライアント IP プロパティを転送します。 詳細については、 [こちらの記事][tcpproxy]を参照してください。

![全体的な概要][1]


## <a name="dns"></a>DNS

この機能はプレビュー段階であるため、プレビュー期間中は DNS エントリを変更しません。 DNS エントリは、プライベート DNS サーバーまたは Azure DNS プライベート ゾーンでご自身で管理する必要があります。 カスタム DNS 名を使用する必要がある場合は、カスタム名を Web アプリに追加する必要があります。 プレビュー期間中は、すべてのカスタム名と同様に、パブリック DNS 解決を使用してカスタム名を検証する必要があります。 [カスタム DNS 検証のテクニカル リファレンス][dnsvalidation]

## <a name="pricing"></a>価格

料金の詳細については、「[Azure Private Link の料金][pricing]」をご覧ください。

## <a name="limitations"></a>制限事項

定期的に Private Link 機能とプライベート エンドポイントの改善を行っています。制限に関する最新情報については、[こちらの記事][pllimitations]を確認してください。

## <a name="next-steps"></a>次のステップ

ポータルを使用して Web アプリのプライベート エンドポイントをデプロイするには、[Web アプリに非公開で接続する方法][howtoguide]に関する記事を参照してください。


<!--Image references-->
[1]: ./media/private-endpoint/schemaglobaloverview.png

<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: https://docs.microsoft.com/azure/private-link/rivate-link-service-overview#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
