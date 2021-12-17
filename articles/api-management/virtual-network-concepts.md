---
title: Azure API Management と Azure 仮想ネットワーク
description: API Management インスタンスを Azure 仮想ネットワークに接続するためのシナリオと要件について説明します。
author: dlepow
ms.service: api-management
ms.topic: conceptual
ms.date: 08/19/2021
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 364487d697aee69215e9ca9f080e4aa6a1a83468
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130253366"
---
# <a name="use-a-virtual-network-with-azure-api-management"></a>仮想ネットワークの Azure API Management との使用

Azure Virtual Network (VNET) では、Azure リソースをインターネット以外のルーティング可能なネットワークに配置し、アクセスを制御できます。 その後、さまざまな VPN テクノロジを使用して VNET をオンプレミスのネットワークに接続できます。 Azure VNET の詳細については、最初に [Azure Virtual Network の概要](../virtual-network/virtual-networks-overview.md)に関するページの情報をご覧ください。

この記事では、API Management インスタンスの VNET 接続オプション、要件、考慮事項について説明します。 Azure portal、Azure CLI、Azure Resource Manager テンプレート、またはその他のツールをデプロイに使用できます。 API Management がデプロイされるサブネットへの受信トラフィックと送信トラフィックは、[ネットワーク セキュリティ グループ][NetworkSecurityGroups]を使用して制御します。

デプロイの詳細な手順とネットワーク構成については、以下を参照してください。

* [Azure API Management を使用して外部仮想ネットワークに接続する](./api-management-using-with-vnet.md)。
* [Azure API Management を使用して内部仮想ネットワークに接続する](./api-management-using-with-internal-vnet.md)。

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="access-options"></a>アクセス オプション

既定では、API Management インスタンスはインターネットからアクセス可能である必要があります。 仮想ネットワークを使用すると、開発者ポータル、API ゲートウェイ、およびその他の API Management エンドポイントを、インターネット (外部モード) または VNET 内 (内部モード) のみのいずれかからアクセスできるように構成できます。 

* **外部**: API Management エンドポイントは、パブリック インターネットから外部ロード バランサーを使用してアクセスできます。 ゲートウェイでは、VNET 内のリソースにアクセスできます。

    :::image type="content" source="media/virtual-network-concepts/api-management-vnet-external.png" alt-text="外部 VNET に接続する":::

    仮想ネットワークにデプロイされているバックエンド サービスにアクセスするには、外部モードで API Management を使用します。

* **内部**: API Management のエンドポイントは、VNET 内から内部ロード バランサーを使用してのみアクセスできます。 ゲートウェイでは、VNET 内のリソースにアクセスできます。

    :::image type="content" source="media/virtual-network-concepts/api-management-vnet-internal.png" alt-text="内部 VNET に接続する":::

    API Management を内部モードで使用するのは、次のようなケースです。

    * Azure VPN 接続または Azure ExpressRoute を使用することで、プライベート データセンターでホストされている API へのアクセスを、サード パーティが安全に行うことができるようにします。
    * 一般的なゲートウェイを通じてクラウド ベースの API とオンプレミスの API を公開することによって、ハイブリッド クラウドのシナリオを有効にします。
    * 単一のゲートウェイ エンドポイントを使用して複数の地理的な場所でホストされている API を管理します。


## <a name="network-resource-requirements"></a>ネットワーク リソースの要件

API Management の仮想ネットワーク リソース要件は次のとおりです。 一部の要件は、API Management インスタンスをホストする[コンピューティング プラットフォーム](compute-infrastructure.md)のバージョン (`stv2` または `stv1`) によって異なります。

### <a name="stv2"></a>[stv2](#tab/stv2)

* Azure Resource Manager 仮想ネットワークが必要です。
* 仮想ネットワークとサブネットを指定するだけでなく、Standard SKU [パブリック IPv4 アドレス](../virtual-network/ip-services/public-ip-addresses.md#standard)を指定する必要があります。
* API Management インスタンスへの接続に使用されるサブネットには、他の Azure リソースの種類が含まれる場合があります。
* API Management サービス、仮想ネットワークとサブネット、およびパブリック IP アドレス リソースは、同じリージョンとサブスクリプション内に存在する必要があります。
* 複数リージョンの API Management デプロイの場合、場所ごとに仮想ネットワーク リソースを個別に構成します。

### <a name="stv1"></a>[stv1](#tab/stv1)

* Azure Resource Manager 仮想ネットワークが必要です。
* API Management インスタンスへの接続に使用するサブネットは、API Management 専用である必要があります。 他の Azure リソースの種類を含めることはできません。
* API Management サービス、仮想ネットワーク、およびサブネット リソースは、同じリージョンとサブスクリプション内に存在する必要があります。
* 複数リージョンの API Management デプロイの場合、場所ごとに仮想ネットワーク リソースを個別に構成します。

---

## <a name="subnet-size"></a>サブネットのサイズ

API Management でデプロイできるサブネットの最小サイズは /29 で、3 つの使用可能な IP アドレスが提供されます。 API Management の追加スケール ユニットごとに、さらに 2 つの IP アドレスが必要になります。 最小サイズ要件は、次の考慮事項に基づきます。

* Azure では、各サブネット内で一部の IP アドレスが予約されており、これらを使用することはできません。 サブネットの最初と最後の IP アドレスは、プロトコルに準拠するために予約されています。 さらに 3 つのアドレスが Azure サービスのために使用されます。 詳細については、「[これらのサブネット内の IP アドレスの使用に関する制限はありますか](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)」をご覧ください。

* Azure VNET インフラストラクチャによって使用される IP アドレスに加えて、サブネットの各 API Management インスタンスでは、以下が使用されます。
    * Premium SKU のユニットごとに 2 つの IP アドレス、または 
    * Developer SKU 用に 1 つの IP アドレス。 

* 各インスタンスでは、外部ロード バランサー用に追加の IP アドレスが予約されます。 [内部 VNET](./api-management-using-with-internal-vnet.md) にデプロイする場合は、このインスタンスは、内部ロード バランサー用に追加の IP アドレスが必要です。

## <a name="routing"></a>ルーティング

API Management インスタンスを[外部 VNET](./api-management-using-with-vnet.md#routing) または[内部 VNET](./api-management-using-with-internal-vnet.md#routing) にデプロイする場合は、ルーティングに関するガイダンスを参照してください。

詳細については、[Azure API Management の IP アドレス](api-management-howto-ip-addresses.md)に関するページを参照してください。

## <a name="dns"></a>DNS

外部モードでは、VNET によって、API Management エンドポイントとその他の Azure リソースに対して [Azure で提供される名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution)が可能になります。 オンプレミスのリソースの名前解決は提供されません。 

内部モードでは、API Management エンドポイントおよびその他の必要な Azure リソースに対する名前解決を保証するために、独自の DNS ソリューションを提供する必要があります。 Azure [プライベート DNS ゾーン](../dns/private-dns-overview.md)を構成することをお勧めします。

詳細については次を参照してください: 
* [Azure 仮想ネットワーク内のリソースの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)。  
* [Azure プライベート DNS ゾーンを作成する](../dns/private-dns-getstarted-portal.md)

> [!IMPORTANT]
> VNET でカスタム DNS ソリューションを使用する予定の場合は、そのソリューションは、API Management サービスをデプロイする **前** に設定します。 それ以外の場合は、DNS サーバーを変更するたびに [[ネットワーク構成の適用] 操作](/rest/api/apimanagement/2020-12-01/api-management-service/apply-network-configuration-updates)を実行して API Management サービスを更新する必要があります。

## <a name="limitations"></a>制限事項

一部の制限は、API Management インスタンスをホストする[コンピューティング プラットフォーム](compute-infrastructure.md)のバージョン (`stv2` または `stv1`) によって異なります。

### <a name="stv2"></a>[stv2](#tab/stv2)

* API Management インスタンスが含まれるサブネットは、サブスクリプション間で移動できません。
* 内部 VNET モードで構成された複数リージョンの API Management デプロイでは、ユーザーがルーティングを所有し、複数のリージョン間の負荷分散を管理する責任を負います。
* API を [OpenAPI 仕様](import-and-publish.md)から API Management にインポートするには、仕様 URL が、パブリックにアクセス可能なインターネット アドレスでホストされている必要があります。

### <a name="stv1"></a>[stv1](#tab/stv1)

* API Management インスタンスが含まれるサブネットは、サブスクリプション間で移動できません。
* 内部 VNET モードで構成された複数リージョンの API Management デプロイでは、ユーザーがルーティングを所有し、複数のリージョン間の負荷分散を管理する責任を負います。
* API を [OpenAPI 仕様](import-and-publish.md)から API Management にインポートするには、仕様 URL が、パブリックにアクセス可能なインターネット アドレスでホストされている必要があります。
* プラットフォームの制限により、別のリージョンのグローバルにピアリングされた VNET 内のリソースと内部モードの API Management サービスの間の接続は機能しません。 詳細については、[仮想ネットワークのドキュメント](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)を参照してください。

---

## <a name="next-steps"></a>次のステップ

各項目の詳細情報

* [VPN Gateway を使用した仮想ネットワークのバックエンドへの接続](../vpn-gateway/design.md#s2smulti)
* [異なるデプロイ モデルの Virtual Network を PowerShell を使用して接続する](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Virtual Network についてよく寄せられる質問](../virtual-network/virtual-networks-faq.md)

仮想ネットワークに接続します。
* [Azure API Management を使用して外部仮想ネットワークに接続する](./api-management-using-with-vnet.md)。
* [Azure API Management を使用して内部仮想ネットワークに接続する](./api-management-using-with-internal-vnet.md)。

次のトピックの参照

* [VPN Gateway を使用して Virtual Network をバックエンドに接続する](../vpn-gateway/design.md#s2smulti)
* [異なるデプロイ モデルの Virtual Network を PowerShell を使用して接続する](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Azure API Management で API Inspector を使用して呼び出しをトレースする方法](api-management-howto-api-inspector.md)
* [Virtual Network についてよく寄せられる質問](../virtual-network/virtual-networks-faq.md)
* [サービス タグ](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/virtual-network-concepts/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/virtual-network-concepts/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[NetworkSecurityGroups]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags