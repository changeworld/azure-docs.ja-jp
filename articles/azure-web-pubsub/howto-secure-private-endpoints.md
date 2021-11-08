---
title: Azure プライベート エンドポイント経由で VNet と Azure Web PubSub サービス間のトラフィックをセキュリティで保護する方法
description: 仮想ネットワークから Azure Web PubSub サービスへのセキュアなアクセスのための、プライベート エンドポイントの概要。
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: c05112ec35197f8a17220b31e5032ae5d2469cf8
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995050"
---
# <a name="use-private-endpoints-for-azure-web-pubsub-service"></a>Azure Web PubSub サービスでプライベート エンドポイントを使用する

Azure Web PubSub サービスの[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用すると、仮想ネットワーク (VNet) 内のクライアントは [Private Link](../private-link/private-link-overview.md) を介してデータに安全にアクセスできるようになります。 プライベート エンドポイントでは、Azure Web PubSub サービスの VNet アドレス空間からの IP アドレスが使用されます。 VNet 上のクライアントと Azure Web PubSub サービス間のネットワーク トラフィックが、Microsoft バックボーン ネットワーク上のプライベート リンク経由で送受信され、パブリック インターネットへの露出を避けることができます。

Azure Web PubSub サービスのプライベート エンドポイントを使用すると、次のことが可能になります。

- ネットワーク アクセス制御を使用して Azure Web PubSub サービスをセキュリティで保護し、Azure Web PubSub サービスに対するすべての接続をパブリック エンドポイント上でブロックします。
- VNnet からのデータの流出をブロックできるようにすることで、仮想ネットワーク (VNet) のセキュリティを強化します。
- [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) または [ExpressRoutes](../expressroute/expressroute-locations.md) をプライベート ピアリングと共に使用して、VNet に接続するオンプレミス ネットワークから Azure Web PubSub サービスに安全に接続します。

## <a name="conceptual-overview"></a>概念の概要

:::image type="content" source="./media/howto-secure-private-endpoints/private-endpoint-overview.png" alt-text="Azure Web PubSub サービスのプライベート エンドポイントの概要。":::

プライベート エンドポイントは、[仮想ネットワーク](../virtual-network/virtual-networks-overview.md) (VNet) 内の Azure サービス用の特別なネットワーク インターフェイスです。 Azure Web PubSub サービスのプライベート エンドポイントを作成すると、VNet 上のクライアントとサービス間の安全な接続が提供されます。 プライベート エンドポイントには、VNet の IP アドレス範囲から IP アドレスが割り当てられます。 プライベート エンドポイントと Azure Web PubSub サービス間の接続には、セキュリティで保護されたプライベート リンクが使用されます。

VNet 内のアプリケーションは、プライベート エンドポイント経由で Azure Web PubSub サービスにシームレスに接続できます。その際、**使用される接続文字列と承認メカニズムは、それを経由しない場合と同じになります**。 プライベート エンドポイントは、Azure Web PubSub サービスでサポートされているすべてのプロトコル (REST API を含む) で使用できます。

お使いの VNet で Azure Web PubSub サービスのプライベート エンドポイントを作成すると、承認を得るために同意要求が Azure Web PubSub サービスの所有者に送信されます。 プライベート エンドポイントの作成を要求しているユーザーが Azure Web PubSub サービスの所有者でもある場合、この同意要求は自動的に承認されます。

Azure Web PubSub サービスの所有者は、[Azure portal](https://portal.azure.com) で Azure Web PubSub サービスの *[プライベート エンドポイント]* タブを使用して、同意要求とプライベート エンドポイントを管理できます。

> [!TIP]
> Azure Web PubSub サービスへのアクセスを、プライベート エンドポイント経由のみに制限する場合は、パブリック エンドポイント経由のアクセスを拒否または制御するように[ネットワーク アクセス制御を構成](howto-secure-network-access-control.md)します。

### <a name="connecting-to-private-endpoints"></a>プライベート エンドポイントへの接続

プライベート エンドポイントを使用する VNet 上のクライアントは、パブリック エンドポイントに接続するクライアントと同じ接続文字列を、Azure Web PubSub サービスに対して使用する必要があります。 プライベート リンク経由で VNet から Azure Web PubSub サービスへの接続を自動的にルーティングするために、DNS 解決に依存しています。

> [!IMPORTANT]
> プライベート エンドポイントを利用しない場合と同じ接続文字列を使用して、Azure Web PubSub サービスに接続します。 `privatelink` サブドメイン URL を使用して、Azure Web PubSub サービスに接続しないでください。

既定では、VNet に接続されている[プライベート DNS ゾーン](../dns/private-dns-overview.md)が作成され、プライベート エンドポイントに必要な更新も行われます。 ただし、独自の DNS サーバーを使用している場合は、DNS 構成に他の変更が必要になることがあります。 以下の [DNS の変更](#dns-changes-for-private-endpoints)に関するセクションで、プライベート エンドポイントに必要な更新について説明しています。

## <a name="dns-changes-for-private-endpoints"></a>プライベート エンドポイントの DNS の変更

プライベート エンドポイントを作成すると、Azure Web PubSub サービスの DNS CNAME リソース レコードは、プレフィックス `privatelink` を含むサブドメイン内のエイリアスに更新されます。 既定で、`privatelink` サブドメインに対応する[プライベート DNS ゾーン](../dns/private-dns-overview.md)も作成されます。これには、プライベート エンドポイントの DNS A リソース レコードが含まれます。

プライベート エンドポイントを含む VNet の外部から Azure Web PubSub サービス ドメイン名を解決すると、Azure Web PubSub サービスのパブリック エンドポイントに解決されます。 プライベート エンドポイントをホストしている VNet から解決されると、ドメイン名はプライベート エンドポイントの IP アドレスに解決されます。

上の図の例のように、プライベート エンドポイントをホストしている VNet の外部から解決されると、Azure Web PubSub サービスの 'foobar' の DNS リソース レコードは次のようになります。

| 名前                                                  | 種類  | 値                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.webpubsub.azure.com``                        | CNAME | ``foobar.privatelink.webpubsub.azure.com``            |
| ``foobar.privatelink.webpubsub.azure.com``            | A     | \<Azure Web PubSub service public IP address\>           |

既に説明したように、ネットワーク アクセス制御を使用して、VNet の外部のクライアントによるパブリック エンドポイント経由のアクセスを拒否または制御することができます。

'foobar' の DNS リソース レコードは、プライベート エンドポイントをホストしている VNet 内のクライアントによって解決されると、次のようになります。

| 名前                                                  | 種類  | 値                                                 |
| :---------------------------------------------------- | :---: | :---------------------------------------------------- |
| ``foobar.webpubsub.azure.com``                        | CNAME | ``foobar.privatelink.webpubsub.azure.com``            |
| ``foobar.privatelink.webpubsub.azure.com``            | A     | 10.1.1.5                                              |

この方法を使用すると、プライベート エンドポイントをホストしている VNet 上のクライアントと、VNet の外部のクライアントから **同じ接続文字列を使用して** Azure Web PubSub サービスにアクセスできます。

ネットワーク上でカスタム DNS サーバーを使用している場合、クライアントでは、Azure Web PubSub サービス エンドポイントの FQDN をプライベート エンドポイントの IP アドレスに解決できる必要があります。 プライベート リンク サブドメインを VNet のプライベート DNS ゾーンに委任するように DNS サーバーを構成するか、プライベート エンドポイントの IP アドレスを使用して `foobar.privatelink.webpubsub.azure.com` の A レコードを構成する必要があります。

> [!TIP]
> カスタムまたはオンプレミスの DNS サーバーを使用している場合は、`privatelink` サブドメインの Azure Web PubSub サービス名をプライベート エンドポイントの IP アドレスに解決するように、DNS サーバーを構成する必要があります。 これを行うには、VNet のプライベート DNS ゾーンに `privatelink` サブドメインを委任するか、DNS サーバー上で DNS ゾーンを構成し、DNS A レコードを追加します。

Azure Web PubSub サービスのプライベート エンドポイントに推奨される DNS ゾーン名は、`privatelink.webpubsub.azure.com` です。

プライベート エンドポイントをサポートするように独自の DNS サーバーを構成する方法の詳細については、次の記事を参照してください。

- [Azure 仮想ネットワーク内のリソースの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [プライベート エンドポイントの DNS 構成](../private-link/private-endpoint-overview.md#dns-configuration)

## <a name="create-a-private-endpoint"></a>プライベート エンドポイントの作成

### <a name="create-a-private-endpoint-along-with-a-new-azure-web-pubsub-service-in-the-azure-portal"></a>Azure portal で新しい Azure Web PubSub サービスと共にプライベート エンドポイントを作成する

1. 新しい Azure Web PubSub サービスを作成する場合は、 **[ネットワーク]** タブを選択します。接続方法として **[プライベート エンドポイント]** を選択します。

    :::image type="content" source="./media/howto-secure-private-endpoints/portal-create-blade-networking-tab.png" alt-text="Azure Web PubSub サービスの作成 - [ネットワーク] タブ。":::

1. **[追加]** を選択します。 新しいプライベート エンドポイントのサブスクリプション、リソース グループ、場所、名前を入力します。 仮想ネットワークとサブネットを選択します。

1. **[Review + create]\(レビュー + 作成\)** を選択します。

### <a name="create-a-private-endpoint-for-an-existing-azure-web-pubsub-service-in-the-azure-portal"></a>Azure portal で既存の Azure Web PubSub サービスのプライベート エンドポイントを作成する

1. Azure Web PubSub サービスにアクセスします。

1. **[プライベート エンドポイント接続]** という設定メニューを選択します。

1. 上部にある **[+ プライベート エンドポイント]** ボタンを選択します。

1. 新しいプライベート エンドポイントのサブスクリプション、リソース グループ、リソース名、およびリージョンを入力します。

1. ターゲットの Azure Web PubSub サービス リソースを選択します。

1. ターゲットの仮想ネットワークを選択します

1. **[Review + create]\(レビュー + 作成\)** を選択します。

## <a name="pricing"></a>価格

料金の詳細については、「[Azure Private Link の料金](https://azure.microsoft.com/pricing/details/private-link)」をご覧ください。

## <a name="known-issues"></a>既知の問題

Azure Web PubSub サービスのプライベート エンドポイントに関する以下の既知の問題に注意してください。

### <a name="free-tier"></a>Free レベル

Azure Web PubSub サービスの Free レベル インスタンスはプライベート エンドポイントと統合できません。

### <a name="access-constraints-for-clients-in-vnets-with-private-endpoints"></a>プライベート エンドポイントを含む VNet 内のクライアントに対するアクセス制約

既存のプライベート エンドポイントを含む VNet 内のクライアントには、プライベート エンドポイントを持つ他の Azure Web PubSub サービス インスタンスにアクセスするときに、制約があります。 たとえば、VNet N1 に、Azure Web PubSub サービス インスタンス W1 のプライベート エンドポイントがあるとします。 Azure Web PubSub サービス W2 に VNet N2 でのプライベート エンドポイントがある場合、VNet N1 のクライアントでは、プライベート エンドポイントを使用して Azure Web PubSub サービス W2 にもアクセスする必要があります。 Azure Web PubSub サービス W2 にプライベート エンドポイントがない場合、VNet N1 のクライアントは、プライベート エンドポイントを使用せずにそのアカウントで Azure Web PubSub サービスにアクセスできます。

この制約は、Azure Web PubSub サービス W2 によってプライベート エンドポイントが作成されるときに行われた DNS 変更の結果です。

### <a name="network-security-group-rules-for-subnets-with-private-endpoints"></a>プライベート エンドポイントがあるサブネットのネットワーク セキュリティ グループ規則

現在、プライベート エンドポイントの[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md) (NSG) 規則とユーザー定義ルートを構成することはできません。 プライベート エンドポイントをホストするサブネットに適用される NSG 規則は、プライベート エンドポイントに適用されます。 この問題の限定的な回避策として、ソース サブネットでプライベート エンドポイントのアクセス規則を実装できます。ただし、この方法では、管理オーバーヘッドが高くなる可能性があります。

