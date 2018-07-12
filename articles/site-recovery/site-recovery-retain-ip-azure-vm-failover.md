---
title: Azure 仮想マシンを別の Azure リージョンにフェールオーバーする際に IP アドレスを保持する | Microsoft Docs
description: Azure Site Recovery を使用した Azure フェールオーバーのシナリオで Azure の IP アドレスを保持する方法について説明します。
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/06/2018
author: mayanknayar
ms.topic: conceptual
ms.author: manayar
ms.openlocfilehash: d9753f4359e1123ec9051dc303416a74e7aee847
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920418"
---
# <a name="ip-address-retention-for-azure-virtual-machine-failover"></a>Azure 仮想マシンのフェールオーバーでの IP アドレスの保持

Azure Site Recovery を使用すると、Azure VM のディザスター リカバリーを実行できます。 ある Azure リージョンから別の Azure リージョンへフェールオーバーする際、多くの状況で、IP 構成を維持することが必要になります。 既定では、ターゲット リージョンにリソースを作成する際に、Azure Site Recovery によってソース仮想ネットワークとサブネットの構造が模倣されます。 静的プライベート IP アドレスで構成された Azure VM の場合、Site Recovery は、その IP アドレスが Azure リソースやレプリケートされた VM によって事前にブロックされていなければ、できる限り同じプライベート IP アドレスをターゲット VM にプロビジョニングしようとします。

シンプルなアプリケーションの場合、必要となるのは、上記の既定の構成だけです。 複雑なエンタープライズ アプリケーションの場合は、フェールオーバー後にインフラストラクチャの他のコンポーネントと接続できるように、お客様が追加のネットワーク リソースをプロビジョニングすることが必要になる場合があります。 この記事では、Azure VM をあるリージョンから別のリージョンにフェールオーバーする際に VM の IP アドレスを維持するためのネットワーク要件について説明します。

## <a name="azure-to-azure-connectivity"></a>Azure 間の接続

1 つ目のシナリオでは、アプリケーション インフラストラクチャがすべて Azure にある **A 社**について考えます。 ビジネス継続性とコンプライアンス上の理由から、**A 社**は Azure Site Recovery を使用してアプリケーションを保護することに決めました。

IP アドレス保持の要件 (アプリケーションのバインディングのためなど) を踏まえて、A 社はターゲット リージョンに同じ仮想ネットワークとサブネットの構造を確保しています。 目標復旧時間 (RTO) をさらに短縮するために、**A 社**は、SQL Always ON やドメイン コントローラーなどのレプリカ ノードを使用しています。これらのレプリカ ノードはターゲット リージョンの別の仮想ネットワークに配置されています。 **A 社**はレプリカ ノードに対して別のアドレス空間を使用することで、ソース リージョンとターゲット リージョンの間の VPN サイト間接続を確立しています。両方のリージョンで同じアドレス空間が使用されていると接続を確立できないためです。

フェールオーバー前のネットワーク アーキテクチャは、次のとおりです。
- アプリケーション VM は Azure 東アジアにホストされ、アドレス空間 10.1.0.0/16 で Azure 仮想ネットワークを利用しています。 この仮想ネットワークの名前は **Source VNet** です。
- アプリケーション ワークロードは 3 つのサブネット、10.1.1.0/24、10.1.2.0/24、10.1.3.0/24 に分散されます。サブネットの名前はそれぞれ、**Subnet 1**、**Subnet 2**、**Subnet 3** です。
- ターゲット リージョンは Azure 東南アジアで、ソースのアドレス空間とサブネット構成を模倣した復旧用仮想ネットワークがあります。 この仮想ネットワークの名前は **Recovery VNet** です。
- Always On やドメイン コントローラーなどに必要なレプリカ ノードは、アドレスが 10.2.4.0/24 の Subnet 4 内にあるアドレス空間が 10.2.0.0/16 の仮想ネットワークに配置されています。 この仮想ネットワークの名前は **Azure VNet** で、Azure 東南アジアにあります。
- **Source VNet** と **Azure VNet** は VPN サイト間接続を介して接続されています。
- **Recovery VNet** は他の仮想ネットワークには接続されていません。
- **A 社**は、レプリケートされたアイテムのターゲット IP アドレスの割り当てと確認を行いました。 この例では、ターゲット IP アドレスは各 VM のソース IP アドレスと同じです。

![フェールオーバー前の Azure 間接続](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="full-region-failover"></a>リージョン全体のフェールオーバー

リージョン障害が発生した場合、**A 社**は、Azure Site Recovery の強力な[復旧計画](site-recovery-create-recovery-plans.md)を利用して、デプロイ全体を迅速かつ簡単に復旧できます。 フェールオーバー前に各 VM のターゲット IP アドレスが既に設定されているため、**A 社**は、フェールオーバーを調整し、下図に示すように、Recovery VNet と Azure Vnet の間の接続を自動的に確立できます。

![Azure 間接続のリージョン全体のフェールオーバー](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)

ターゲット リージョンにある 2 つの VNet 間の接続は、アプリケーションの要件に応じて、フェールオーバー前、フェールオーバー中 (中間ステップとして)、またはフェールオーバー後に確立できます。 [復旧計画](site-recovery-create-recovery-plans.md)を使用して、スクリプトを追加し、フェールオーバーの順序を定義できます。

A 社は、Recovery VNet と Azure VNet の接続の確立に、VNET ピアリングかサイト間 VPN を使用できます。 VNET ピアリングは VPN ゲートウェイを使用せず、さまざまな制約があります。 さらに、[VNET ピアリングの料金](https://azure.microsoft.com/pricing/details/virtual-network)は、[VNet 間 VPN Gateway の料金](https://azure.microsoft.com/pricing/details/vpn-gateway)と計算方法が異なります。 フェールオーバーの場合、一般的には、ネットワークの変更によって生じる予期しないインシデントを最小限に抑えるために、接続の種類など、ソースの接続を模倣することが推奨されます。

### <a name="isolated-application-failover"></a>分離されたアプリケーションのフェールオーバー

特定の条件下では、ユーザーがアプリケーション インフラストラクチャの一部をフェールオーバーする必要が生じることがあります。 このようなケースの例には、専用サブネット内に配置された特定のアプリケーションや階層のフェールオーバーがあります。 IP アドレスを維持しながらサブネットをフェールオーバーすることは可能ですが、接続の整合性が著しく失われるため、ほとんどの場合は推奨されません。 また、同じ Azure 仮想ネットワーク内の他のサブネットとの接続も失われてしまいます。

より適切にサブネット レベルのアプリケーション フェールオーバー要件に対応するには、フェールオーバーに別のターゲット IP アドレスを使用する (ソース仮想ネットワークの他のサブネットに接続する必要がある場合) か、各アプリケーションをソースの専用仮想ネットワークに分離します。 後者の方法では、ソースでネットワーク間接続を確立し、ターゲット リージョンにフェールオーバーする際に同じ接続をエミュレートできます。

回復性を考慮して各アプリケーションを設計するには、アプリケーションを専用の仮想ネットワークに配置し、必要に応じて仮想ネットワーク間の接続を確立する方法をお勧めします。 これにより、元のプライベート IP アドレスを維持しながら、分離されたアプリケーションをフェールオーバーできます。

フェールオーバー前の構成は、次のとおりです。
- アプリケーション VM は Azure アジア太平洋にホストされ、1 つ目のアプリケーションはアドレス空間 10.1.0.0/16 で、2 つ目のアプリケーションはアドレス空間 10.2.0.0/16 で Azure 仮想ネットワークを使用しています。 仮想ネットワークの名前は、1 つ目のアプリケーションが **Source VNet1**、2 つ目のアプリケーションが **Source VNet2** です。
- 各 VNet は、さらに 2 つのサブネットに分割されています。
- ターゲット リージョンは Azure 東南アジアで、Recovery VNet1 と Recovery VNet2 という名前の復旧用の仮想ネットワークがあります。
- Always On やドメイン コントローラーなどに必要なレプリカ ノードは、アドレスが 10.3.4.0/24 の **Subnet 4** 内にあるアドレス空間が 10.3.0.0/16 の仮想ネットワークに配置されています。 この仮想ネットワークの名前は Azure VNet で、Azure 東南アジアにあります。
- **Source VNet1** と **Azure VNet** は VPN サイト間接続を介して接続されています。 同様に、**Source VNet2** と **Azure VNet** も VPN サイト間接続を介して接続されています。
- この例では、**Source VNet1** と **Source VNet2** も、S2S VPN を介して接続されています。 2 つの VNet が同じリージョンにあるため、S2S VPN の代わりに VNET ピアリングを使用することもできます。
- **Recovery VNet1** と **Recovery VNet2** は他の仮想ネットワークには接続されていません。
- 目標復旧時間 (RTO) を短縮するために、フェールオーバー前に **Recovery VNet1** と **Recovery VNet2** に VPN Gateway が構成されています。

![フェールオーバー前の Azure 間接続の分離されたアプリケーション](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

1 つのアプリケーションのみ (この例では Source VNet2 に配置されているアプリケーション) に影響を与える障害状況が発生した場合、A 社は影響を受けるアプリケーションを次のようにして復旧できます。
- **Source VNet1** と **Source VNet2** の VPN 接続と、**Source VNet2** と **Azure VNet** の VPN 接続を切断する。
- **Source VNet1** と **Recovery VNet2** の VPN 接続と、**Recovery VNet2** と **Azure VNet** の VPN 接続を確立する。
- **Source VNet2** の VM を **Recovery VNet2** にフェールオーバーする。

![フェールオーバー後の Azure 間接続の分離されたアプリケーション](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)

上記の分離されたアプリケーションのフェールオーバーを拡張して、含めるアプリケーションとネットワーク接続を増やすことができます。 ソースからターゲットにフェールオーバーする際は、できるだけ似た構成の接続を使用することをお勧めします。

### <a name="further-considerations"></a>その他の考慮事項

VPN Gateway ではパブリック IP アドレスとゲートウェイ ホップを使用して接続を確立します。 パブリック IP アドレスを使用しない場合や、ホップが増えることを避けたい場合は、Azure [仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)を使って、[サポートされる Azure リージョン](../virtual-network/virtual-network-manage-peering.md#cross-region)間で仮想ネットワークをピアリングできます。

## <a name="on-premises-to-azure-connectivity"></a>オンプレミスと Azure の間の接続

2 つ目のシナリオでは、アプリケーション インフラストラクチャの一部を Azure で実行し、残りをオンプレミスで実行している **B 社**について考えます。 ビジネス継続性とコンプライアンス上の理由から、**B 社**は、Azure Site Recovery を使用して、Azure で実行されているアプリケーションを保護することに決めました。

フェールオーバー前のネットワーク アーキテクチャは、次のとおりです。
- アプリケーション VM は Azure 東アジアにホストされ、アドレス空間 10.1.0.0/16 で Azure 仮想ネットワークを利用しています。 この仮想ネットワークの名前は **Source VNet** です。
- アプリケーション ワークロードは 3 つのサブネット、10.1.1.0/24、10.1.2.0/24、10.1.3.0/24 に分散されます。サブネットの名前はそれぞれ、**Subnet 1**、**Subnet 2**、**Subnet 3** です。
- ターゲット リージョンは Azure 東南アジアで、ソースのアドレス空間とサブネット構成を模倣した復旧用仮想ネットワークがあります。 この仮想ネットワークの名前は **Recovery VNet** です。
- Azure 東アジアの VM は、ExpressRoute またはサイト間 VPN を介してオンプレミス データセンターに接続されています。
- 目標復旧時間 (RTO) を短縮するために、B 社は、フェールオーバー前に Azure 東南アジアにある Recovery VNet にゲートウェイをプロビジョニングしました。
- **B 社**は、レプリケートされたアイテムのターゲット IP アドレスの割り当てと確認を行いました。 この例では、ターゲット IP アドレスは各 VM のソース IP アドレスと同じです。

![フェールオーバー前のオンプレミスと Azure の接続](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="full-region-failover"></a>リージョン全体のフェールオーバー

リージョン障害が発生した場合、**B 社**は、Azure Site Recovery の強力な[復旧計画](site-recovery-create-recovery-plans.md)を利用して、デプロイ全体を迅速かつ簡単に復旧できます。 フェールオーバー前に各 VM のターゲット IP アドレスが既に設定されているため、**B 社**は、フェールオーバーを調整し、下図に示すように、Recovery VNet とオンプレミス データセンターの間の接続を自動的に確立できます。

Azure 東南アジアとオンプレミス データセンターの接続を確立する前に、Azure 東アジアとオンプレミス データセンターの元の接続を切断する必要があります。 オンプレミスのルーティングも、フェールオーバー後にターゲット リージョンとゲートウェイをポイントするように再構成しました。

![フェールオーバー後のオンプレミスと Azure の接続](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

### <a name="subnet-failover"></a>Subnet failover

**A 社**で説明した Azure 間接続のシナリオとは異なり、**B 社**のこのケースでは、サブネット レベルのフェールオーバーは実行できません。これは、ソース仮想ネットワークと復旧用仮想ネットワークのアドレス空間が同じであり、ソースとオンプレミスの間の元の接続がアクティブであるためです。

アプリケーションの回復性を確保するために、各アプリケーションは専用の Azure 仮想ネットワークに配置することをお勧めします。 そうすることで、上記のように、アプリケーションを分離してフェールオーバーし、必要なオンプレミスとソースの間の接続をターゲット リージョンにルーティングできます。

## <a name="next-steps"></a>次の手順
- 復旧計画の詳細については、[こちら](site-recovery-create-recovery-plans.md)をご覧ください。
