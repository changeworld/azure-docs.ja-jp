---
title: プライベート ネットワーク アクセスの概要 - Azure Database for MySQL フレキシブル サーバー
description: Azure Database for MySQL のフレキシブル サーバー デプロイ オプションのプライベート アクセス ネットワークのオプションについて説明します
author: Madhusoodanan
ms.author: dimadhus
ms.service: mysql
ms.topic: conceptual
ms.date: 8/6/2021
ms.openlocfilehash: 961e1fbf4bc87355ce036fb9a5af7a1cd85c4e76
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472715"
---
# <a name="private-network-access-for-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL のプライベート ネットワーク アクセス - フレキシブル サーバー

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

この記事では、Azure MySQL フレキシブル サーバーのプライベート接続オプションについて説明します。 Azure でサーバーを安全に作成するための、Azure Database for MySQL フレキシブル サーバーの仮想ネットワークの概念について詳しく説明します。


## <a name="private-access-vnet-integration"></a>プライベート アクセス (VNet 統合)

[Azure Virtual Network (VNet)](../../virtual-network/virtual-networks-overview.md) は、Azure 内のプライベート ネットワークの基本的な構成要素です。 Virtual Network (VNet) を Azure Database for MySQL - フレキシブル サーバーと統合すると、Azure のネットワーク セキュリティと分離の利点が得られます。

Azure Database for MySQL - フレキシブル サーバーの Virtual Network (VNet) 統合を使用すると、サービスへのアクセスを、仮想ネットワーク インフラストラクチャのみに制限できます。 Virtual Network (VNet) では、すべてのアプリケーションとデータベースのリソースを 1 つの仮想ネットワークに含めることができます。また、同じリージョン内または異なるリージョン間の異なる VNet にわたって拡張することもできます。 さまざまな仮想ネットワーク間のシームレスな接続は、Microsoft の低待機時間、高帯域幅のプライベート バックボーン インフラストラクチャを使用する[ピアリング](../../virtual-network/virtual-network-peering-overview.md)によって確立できます。 仮想ネットワークは、接続において、見かけ上 1 つのネットワークとして機能します。

Azure Database for MySQL - フレキシブル サーバーは、次のクライアントからの接続に対応しています。

* 同じ Azure リージョン内の仮想ネットワーク。 (ローカル ピアリング VNet)
* 複数の Azure リージョン間の仮想ネットワーク。 (グローバル ピアリング VNet)

サブネットにより、仮想ネットワークを 1 つ以上のサブネットワークにセグメント分割して、仮想ネットワークのアドレス空間の一部を割り当て、そこに Azure リソースをデプロイできます。 Azure Database for MySQL - フレキシブル サーバーには、[委任されたサブネット](../../virtual-network/subnet-delegation-overview.md)が必要です。 委任されたサブネットは、あるサブネットで Azure Database for MySQL - フレキシブル サーバーのみをホスト可能であることを示す、明示的な識別子です。 サブネットを委任することにより、そのサブネットに Azure Database for MySQL - フレキシブル サーバーをシームレスに管理する、サービス固有のリソースを作成するための、明示的なアクセス許可がサービスに与えられます。

Azure Database for MySQL - フレキシブル サーバーは、Azure [プライベート DNS ゾーン](../../dns/private-dns-privatednszone.md)と統合されます。これにより、信頼性が高くセキュリティで保護された DNS サービスが提供され、仮想ネットワークでドメイン名を管理および解決するのに、カスタムの DNS ソリューションを追加する必要がなくなります。 プライベート DNS ゾーンは、[仮想ネットワーク リンク](../../dns/private-dns-virtual-network-links.md)を作成することで、1 つ以上の仮想ネットワークにリンクできます。


:::image type="content" source="./media/concepts-networking/vnet-diagram.png" alt-text="フレキシブル サーバー MySQL VNET":::

上の図では、

1. 委任されたサブネット、VNET **VNet-1** の 10.0.1.0/24 にフレキシブル サーバーを導入しています。
2. 同じ VNET の異なるサブネットに展開したどのアプリケーションからでも、そのフレキシブル サーバーに直接アクセスできます。
3. 別の VNET である **VNet-2** に展開したアプリケーションからは、フレキシブル サーバーに直接アクセスできません。 それらからフレキシブル サーバーに直接アクセスするためには、[プライベート DNS ゾーン VNET ピアリング](#private-dns-zone-and-vnet-peering) を実行する必要があります。

## <a name="virtual-network-concepts"></a>仮想ネットワークの概念

以下に、MySQL フレキシブル サーバーと共に仮想ネットワークを使用する際に知っておく必要があるいくつかの概念を示します。

* **仮想ネットワーク** -

   Azure Virtual Network (VNet) には、実際の使用のために構成されたプライベート IP アドレス空間が含まれます。 Azure の仮想ネットワークの詳細については、[Azure Virtual Network の概要](../../virtual-network/virtual-networks-overview.md)に関するページを参照してください。

    ご利用の仮想ネットワークは、フレキシブル サーバーと同じ Azure リージョンに存在する必要があります。

* **委任されたサブネット** -

   仮想ネットワークには、サブネット (サブネットワーク) が含まれます。 サブネットを使用すると、仮想ネットワークをより小さなアドレス空間に分割できます。 Azure リソースは、仮想ネットワーク内の特定のサブネットにデプロイされます。

   MySQL フレキシブル サーバーは、MySQL フレキシブル サーバーでの使用のためにのみ **委任された** サブネット内に存在する必要があります。 この委任は、Azure Database for MySQL フレキシブル サーバーのみがそのサブネットを使用できることを意味します。 委任されたサブネットに他の Azure リソースの種類を含めることはできません。 その委任プロパティを Microsoft.DBforMySQL/flexibleServers として割り当てて、サブネットを委任します。

* **ネットワーク セキュリティ グループ (NSG)**

   ネットワーク セキュリティ グループのセキュリティ規則を使用して、仮想ネットワーク サブネットとネットワーク インターフェイスに出入りできるネットワーク トラフィックの種類をフィルター処理できます。 詳細については、[ネットワーク セキュリティ グループの概要](../../virtual-network/network-security-groups-overview.md)に関するページを参照してください。

* **プライベート DNS ゾーンの統合** -

   Azure プライベート DNS ゾーンの統合により、現在の VNET 内、またはそのプライベート DNS ゾーンがリンクされたリージョン内のピアリングされた VNET のプライベート DNS を解決できます。

* **仮想ネットワーク ピアリング**

   仮想ネットワーク ピアリングを使用すると、Azure で 2 つ以上の Virtual Network をシームレスに接続できます。 ピアリングされた仮想ネットワークは、接続において、見かけ上 1 つのネットワークとして機能します。 ピアリングされた仮想ネットワーク内の仮想マシン間のトラフィックには、Microsoft のバックボーンインフラストラクチャが使用されます。 ピアリングされた VNet 内のクライアント アプリケーションとフレキシブル サーバーの間のトラフィックは、Microsoft のプライベート ネットワークのみを介してルーティングされ、そのネットワークだけに分離されます。

## <a name="using-private-dns-zone"></a>プライベート DNS ゾーンを使用する

* Azure portal または Azure CLI を使用して VNET にフレキシブル サーバーを作成する場合は、指定されたサーバー名を使用して、サブスクリプション内で新しいプライベート DNS ゾーンがサーバーごとに自動プロビジョニングされます。 代わりに、フレキシブル サーバーで使用する専用のプライベート DNS ゾーンを設定する必要がある場合は、[プライベート DNS の概要](../../dns/private-dns-overview.md)のドキュメントを参照してください。
* Azure API、Azure Resource Manager テンプレート (ARM テンプレート)、または Terraform を使用する場合は、`mysql.database.azure.com` で終わるプライベート DNS ゾーンを作成し、フレキシブル サーバーにプライベート アクセスを構成する間はそれらを使用してください。 詳細については、[プライベート DNS ゾーンの概要](../../dns/private-dns-overview.md)に関するページを参照してください。

   > [!IMPORTANT]
   > プライベート DNS ゾーン名は、`mysql.database.azure.com` で終わる必要があります。

プライベート アクセス (VNet 統合) を使用するフレキシブル サーバーを [Azure portal](how-to-manage-virtual-network-portal.md) または [Azure CLI](how-to-manage-virtual-network-cli.md) で作成する方法を参照してください。

## <a name="integration-with-custom-dns-server"></a>カスタム DNS サーバーとの統合

カスタム DNS サーバーを使用する場合は、Azure Database for MySQL - フレキシブル サーバーの FQDN を解決する DNS フォワーダーを使用する必要があります。 フォワーダーの IP アドレスは、[168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md) である必要があります。 カスタム DNS サーバーは、VNet 内にあるか、VNET の DNS サーバー設定を使用して到達可能である必要があります。 「[独自の DNS サーバーを使用する名前解決](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」をご覧ください。

## <a name="private-dns-zone-and-vnet-peering"></a>プライベート DNS ゾーンと VNET ピアリング

プライベート DNS ゾーンの設定と VNET ピアリングはそれぞれ独立しています。 プライベート DNS ゾーンを作成して使用する方法の詳細については、前述の「[プライベート DNS ゾーンを使用する](concepts-networking-vnet.md#using-private-dns-zone)」セクションを参照してください。

同じリージョンまたは異なるリージョンの別の VNET でプロビジョニングされたクライアントからフレキシブル サーバーに接続する必要がある場合は、プライベート DNS ゾーンをその VNET にリンクする必要があります。 [仮想ネットワークのリンク方法](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)のドキュメントをご覧ください。

> [!NOTE]
> 末尾が `mysql.database.azure.com` のプライベート DNS ゾーン名のみリンク可能です。

## <a name="connecting-from-on-premises-to-flexible-server-in-virtual-network-using-expressroute-or-vpn"></a>ExpressRoute または VPN を使用したオンプレミスから仮想ネットワーク内のフレキシブル サーバーへの接続

オンプレミス ネットワークから仮想ネットワーク内のフレキシブル サーバーにアクセスする必要があるワークロードの場合は、[ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute/) または [VPN](/azure/architecture/reference-architectures/hybrid-networking/vpn/) と、[オンプレミスに接続された](/azure/architecture/reference-architectures/hybrid-networking/)仮想ネットワークが必要です。 このセットアップを使用すると、オンプレミスの仮想ネットワーク上で実行されているクライアント アプリケーション (MySQL Workbench など) から接続する場合は、フレキシブル サーバーの名前を解決するための DNS フォワーダーが必要になります。 この DNS フォワーダーには、サーバーレベル フォワーダー経由のすべての DNS クエリを、Azure 提供の DNS サービス [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md) に解決する役割があります。

適切に構成するには、次のリソースが必要です。

* オンプレミス ネットワーク
* プライベート アクセスを使用してプロビジョニングされた MySQL フレキシブル サーバー (VNet 統合)
* [オンプレミスに接続された](/azure/architecture/reference-architectures/hybrid-networking/)仮想ネットワーク
* Azure にデプロイされた DNS フォワーダー [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md) を使用する

その後、フレキシブル サーバーの名前 (FQDN) を使用して、ピアリングされた仮想ネットワークまたはオンプレミス ネットワークのクライアント アプリケーションから、フレキシブル サーバーに接続できます。

## <a name="unsupported-virtual-network-scenarios"></a>サポートされない仮想ネットワークのシナリオ

* パブリック エンドポイント (またはパブリック IP あるいは DNS) - 仮想ネットワークにデプロイされたフレキシブル サーバーに、パブリック エンドポイントを設けることはできません
* フレキシブル サーバーを仮想ネットワークおよびサブネットにデプロイした後、それを別の仮想ネットワークまたはサブネットに移動することはできません。 仮想ネットワークを別のリソース グループまたはサブスクリプションに移動することはできません。
* サブネットにリソースを配置すると、そのサブネットのサイズ (アドレス空間) を増やすことはできません
* フレキシブル サーバーは、Private Link には対応していません。 代わりに、VNET インジェクションにより、VNet 内でフレキシブル サーバーを利用できるようにしています。

> [!NOTE]
> カスタム DNS サーバーを使用する場合は、Azure Database for MySQL - フレキシブル サーバーの FQDN を解決する DNS フォワーダーを使用する必要があります。 「[独自の DNS サーバーを使用する名前解決](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」をご覧ください。

## <a name="next-steps"></a>次のステップ

* [Azure portal](how-to-manage-virtual-network-portal.md) または [Azure CLI](how-to-manage-virtual-network-cli.md) を使用してプライベート アクセス (VNet 統合) を有効にする方法を確認します
* [TLS を使用する](how-to-connect-tls-ssl.md)方法を確認します
