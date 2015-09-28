<properties
   pageTitle="Azure Virtual Network (VNet) の概要"
   description="Azure 仮想ネットワーク (VNet) について"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2015"
   ms.author="telmos" />

# Virtual Network の概要

Azure 仮想ネットワーク (VNet) は、クラウド内のユーザー独自のネットワークを表すものです。Azure ネットワーク設定を制御して、アドレス ブロックの DHCP、DNS 設定、セキュリティ ポリシー、ルーティングを定義できます。また、VNet をさらにサブネットに分割し、物理マシンと仮想マシンをオンプレミスのデータセンターにデプロイできるのと同じように、Azure IaaS Virtual Machines (VM) と PaaS ロール インスタンスもデプロイできます。基本的には、独自の IP アドレス ブロックを取り込んで、ネットワークを Azure に拡張できます。

VNet をより詳しく説明するために、次の図に簡略化されたオンプレミスのネットワークを示します。

![オンプレミス ネットワーク](./media/virtual-networks-overview/figure01.png)

上の図は、ルーターを経由してパブリック インターネットに接続されたオンプレミスのネットワークを示します。また、ルーターと、DNS サーバーおよび Web サーバー ファームをホストしている DMZ との間にファイアウォールがあります。Web サーバー ファームは、インターネットに公開されるハードウェア ロード バランサーを使用して負荷分散され、内部サブネットからリソースを消費します。内部サブネットは、別のファイアウォールによって DMZ から分離され、Active Directory ドメイン コント ローラー サーバー、データベース サーバー、アプリケーション サーバーをホストします。

次の図のように、同じネットワークを Azure でホストできます。

![Azure 仮想ネットワーク](./media/virtual-networks-overview/figure02.png)

Azure インフラストラクチャが VNet からパブリック インターネットへのアクセスを有効にするルーターのロールを担っていることがわかります。構成を行う必要はありません。ファイアウォールは、個々のサブネットに適用されたネットワーク セキュリティ グループ (NSG) に置き換えることができます。すべての物理ロード バランサーは Azure でインターネットに接続する内部ロード バランサーに置き換えられます。

## Virtual Networks

VNet は、IaaS VM と、PaaS ロール インスタンスがデプロイしたロールに次のサービスを提供します。

- **分離**: VNet は、相互に完全に分離されているため、同じ CIDR アドレス ブロックを使用する、開発、テスト、実稼働環境用に個別の VNet を作成できます。

- **コンテインメイト**: VNet を複数の Azure リージョンに割り当てることはできません。

    >[AZURE.NOTE]Azure には、クラシック (サービス管理とも呼ばれます) と Azure リソース マネージャー (ARM) の 2 つのデプロイ モードがあります。クラシック VNet はアフィニティ グループに追加するか、リージョン VNet として作成できます。アフィニティ グループに VNet がある場合は、[リージョン VNet に移行する方法](./virtual-networks-migrate-to-regional-vnet.md)がお勧めです。

- **パブリック インターネットへのアクセス**: VNet 内のすべての IaaS VM と PaaS ロール インスタンスは、既定でパブリック インターネットにアクセスできます。ネットワーク セキュリティ グループ (NSG) を使用してアクセスを制御することもできます。

- **VNet 内の VM へのアクセス**: IaaS VM と PaaS ロール インスタンスは、異なるサブネットに配置されている場合でも、ゲートウェイを構成したり Public IP Addresses を使用したりせずに、自分の PaaS と IaaS 環境を共に適用して VNet で相互に接続できます。

- **名前解決**: Azure には、VNet にデプロイされた IaaS VM と PaaS ロール インスタンス用に内部の名前解決が用意されています。独自の DNS サーバーをデプロイし、それを使用するよう VNet を構成することもできます。

- **接続**: VNet は相互に接続することも、サイト間 VPN 接続や ExpressRoute 接続を使用して、オンプレミスのデータセンターに接続することもできます。VPN ゲートウェイについては、「[VPN Gateway について](./vpn-gateway-about-vpngateways.md)」をご覧ください。ExpressRoute の詳細については、「[ExpressRoute の技術概要](./expressroute-introduction.md)」をご覧ください。

    >[AZURE.NOTE]Azure 環境に IaaS VM や PaaS ロール インスタンスをデプロイする前に、VNet を作成するようにしてください。ARM ベースの VM には VNet が必要です。既存の VNet を指定しない場合、Azure が既定の VNet を作成します。この VNet には、独自のオンプレミス ネットワークと競合する CIDR アドレス ブロックが含まれる場合があります。この場合、VNet はオンプレミスのネットワークに接続できなくなります。

## サブネット

VNet は、組織とセキュリティ用の複数のサブネットに分割できます。VNet 内のサブネットは、構成を追加しなくても相互に通信できます。サブネット レベルでのルーティング設定を変更し、サブネットに NSG を適用することもできます。

## IP アドレス

Azure のコンポーネントには、パブリックとプライベートの 2 種類の IP アドレスが割り当てられています。Azure サブネットにデプロイされた IaaS VM と PaaS ロール インスタンスには、サブネットに割り当てられている CIDR アドレス ブロックに基づいた各 NIC に、プライベート IP アドレスが自動的に割り当てられます。IaaS VM と PaaS ロール インスタンスにパブリック IP アドレスを割り当てることもできます。

これらの IP アドレスは動的であるため、いつでも変更できます。特定のサービスの IP アドレスが常に同じかどうかを確認する必要がある場合もあります。この場合、IP アドレスを予約することで、静的アドレスに変更できます。

## Azure ロード バランサー

Azure では、次の 2 種類のロード バランサーを使用できます。

- **外部ロード バランサー**: 外部ロード バランサーを使用すると、パブリック インターネットからアクセスする IaaS VM と PaaS ロール インスタンスに高可用性を提供できます。

- **内部ロード バランサー**: 内部ロード バランサーを使用すると、VNet の他のサービスからアクセスする IaaS VM と PaaS ロール インスタンスに高可用性を提供できます。

Azure の負荷分散の詳細については、「[ロード バランサーの概要](../load-balancer-overview.md)」をご覧ください。

## ネットワーク セキュリティ グループ (NSG)

NSG を作成すると、ネットワーク インターフェイス (NIC)、VM、サブネットへの着信と発信アクセスを制御できます。各 NSG には、発信元 IP アドレス、送信元ポート、接続先 IP アドレス、宛先ポートに基づいてトラフィックを承認するか、拒否するかを指定する 1 つ以上のルールが含まれます。NSG の詳細については、「[ネットワーク セキュリティ グループ (NSG) について](../virtual-networks-nsg.md)」をご覧ください。

## 仮想アプライアンス:

仮想アプライアンスは、ファイアウォール、WAN の最適化、不正侵入検出などのソフトウェア ベースのアプライアンス機能を実行する VNet 内の別の VM です。仮想アプライアンス経由で VNet トラフィックをルーティングしてこの機能を使用するためのルートを Azure で作成できます。

たとえば、NSG を使用して VNet にセキュリティを確保できます。ただし、NSG では、受信と送信パケットにレイヤー 4 のアクセス制御リスト (ACL) を提供します。レイヤー 7 のセキュリティ モデルが必要な場合は、ファイアウォール アプライアンスを使用する必要があります。

仮想アプライアンスは、[ユーザー定義のルートと IP 転送](../virtual-networks-udr-overview.md)によって異なります。

## 次のステップ

- サブネットと [VNet を作成する](../virtual-networks-create-vnet-arm-pportal.md)。
- [Windows を実行する仮想マシンを Azure プレビュー ポータルで作成する](../virtual-machines-windows-tutorial.md)。
- [NSG](../virtual-networks-nsg.md) について。
- [ロード バランサー](../load-balancer-overview.md)の概要。
- [静的内部プライベート IP アドレスの設定方法](../virtual-networks-reserved-private-ip.md)
- [予約済み IP の概要](../virtual-networks-reserved-public-ip.md)
- [ユーザー定義のルートと IP 転送](virtual-networks-udr-overview.md)の概要。

<!---HONumber=Sept15_HO3-->