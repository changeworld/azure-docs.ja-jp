---
title: "Azure Site Recovery による Azure レプリケーションのための Hyper-V (System Center VMM なし) のネットワークを計画する | Microsoft Docs"
description: "この記事では、Hyper-V VM (VMM なし) を Azure にレプリケートする場合に必要なネットワーク計画について説明します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5ca12254-975d-48e8-a84d-422825dac9b2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 4cacf3ca47b215bc23545d8a0b5c9b6fbd66a8df
ms.contentlocale: ja-jp
ms.lasthandoff: 06/23/2017


---

# <a name="step-4-plan-networking-for-hyper-v-to-azure-replication"></a>手順 4: Hyper-V から Azure へのレプリケーションのネットワークを計画する

この記事では、[Azure Site Recovery](site-recovery-overview.md) サービスを使用してオンプレミスの Hyper-V VM (System Center VMM なし) を Azure にレプリケートする場合のネットワーク計画の考慮事項について説明します。

この記事の末尾にあるコメント欄でご意見をお送りください。または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) に質問を投稿してください。


## <a name="connecting-to-replica-vms-after-failover"></a>フェールオーバー後のレプリカ VM への接続

レプリケーションとフェールオーバー戦略を計画する場合に重要なことの 1 つが、フェールオーバー後に Azure VM にどのように接続するかという問題です。 レプリカ Azure VM のネットワーク戦略を設計する場合は、いくつかの選択肢があります。

- **異なる IP アドレス**: レプリケートされた Azure VM ネットワークに対して異なる IP アドレス範囲を使用することを選択できます。 このシナリオでは、VM はフェールオーバー後に新しい IP アドレスを取得するため、DNS の更新が必要になります。 [詳細情報](site-recovery-test-failover-vmm-to-vmm.md#prepare-the-infrastructure-for-test-failover)
- **同じ IP アドレス**: フェールオーバー後も、プライマリ オンプレミス サイトと同じ IP アドレス範囲を Azure で使うことができます。 通常は、IP アドレスの新しい場所でルートを更新する必要があります。 ただし、プライマリ サイトと Azure の間に拡張 VLAN がデプロイされている場合は、仮想マシンの IP アドレスを保持するのは有効なオプションになります。 同じ IP アドレスを維持することで、フェールオーバー後のネットワーク関連の問題が少なくなるため、復旧が単純化されます。


## <a name="retain-ip-addresses"></a>IP アドレスを維持する

ディザスター リカバリーの観点からは、固定 IP アドレスを使うのが最も簡単な方法のように見えますが、可能性のある課題がいくつかあります。 Site Recovery は、サブネットのフェールオーバーを使用して、Azure にフェールオーバーするときに IP アドレスを保持する機能を提供します。


### <a name="subnet-failover"></a>Subnet failover

このシナリオでは、特定のサブネットはサイト 1 とサイト 2 のどちらかにのみ存在し、両方のサイトに同時に存在することはありません。 フェールオーバーの発生時に IP アドレス空間を維持するには、サイトからサイトへサブネットを移動するよう、ルーター インフラストラクチャをプログラムで制御します。 フェールオーバー時、サブネットは、関連する保護対象 VM と一緒に移動します。 この方法の大きな欠点は、フェールオーバー時にサブネット全体を移動する必要があるということであり、フェールオーバーの粒度に影響する可能性があります。


### <a name="failover-example"></a>フェールオーバーの例

Azure へのフェールオーバーの例を見てみましょう。

- Woodgrove Bank (架空の会社) には、自社のビジネス アプリをホストするオンプレミスのインフラストラクチャがあります。 この会社のモバイル アプリケーションは Azure でホストされています。
- Woodgrove Bank が Azure 内でホストしている VM とオンプレミス サーバーとの接続は、オンプレミス エッジ ネットワークと Azure 仮想ネットワーク間のサイト間 (VPN) 接続によって提供されます。
- この VPN は、Azure 内の会社の仮想ネットワークがオンプレミス ネットワークの拡張機能として表示されることを意味します。
- Woodgrove では、Site Recovery を使用して、オンプレミスのワークロードを Azure にレプリケートしたいと考えています。
 - Woodgrove が取り組むべき課題は、ハードコーディングされた IP アドレスに依存するアプリケーションと構成です。そこで、Azure へのフェールオーバー後もアプリケーションの IP アドレスを維持することが必須となります。
 - Woodgrove では、IP アドレス範囲 (172.16.1.0/24、172.16.2.0/24) からの IP アドレスを、Azure で実行されるリソースに割り当てています。


IP アドレスを維持した状態で VM を Azure にレプリケートするには、Woodgrove は次の操作を実行する必要があります。

1. Azure 仮想ネットワークを作成します。 これは、アプリケーションがシームレスにフェールオーバーできるように、オンプレミス ネットワークの拡張機能である必要があります。
2. Azure に作成された仮想ネットワークには、ポイント対サイト接続だけでなく、サイト間 VPN 接続を追加することができます。
3. サイト間接続をセットアップするとき、Azure ネットワークでトラフィックをオンプレミスの場所 (ローカル ネットワーク) にルーティングできるのは、IP アドレス範囲がオンプレミスの IP アドレス範囲と異なる場合だけです。
    - これは、Azure でサブネットの拡張がサポートされていないためです。 すなわち、オンプレミス上にサブネット 192.168.1.0/24 がある場合、Azure ネットワークにローカル ネットワーク 192.168.1.0/24 を追加することはできません。
    - これは予想される状況です。その理由は、Azure が、サブネットにアクティブな VM がないこと、およびサブネットがディザスター リカバリーの目的でのみ作成されることを認識しないことにあります。
    - Azure ネットワークからネットワーク トラフィックを正しくルーティングできるようにするには、ネットワーク内のサブネットとローカル ネットワークが競合してはなりません。

![サブネットのフェールオーバー前](./media/hyper-v-site-walkthrough-network/network-design7.png)

### <a name="before-failover"></a>フェールオーバー前

1. 追加のネットワークを作成します (復旧ネットワークなど)。 これは、フェールオーバーされた VM が作成されるネットワークです。
2. フェールオーバー後も VM の IP アドレスを確実に維持するために、VM のプロパティの **[構成]** でオンプレミス側の VM と同じ IP アドレスを指定し、**[保存]** をクリックします。
3. VM がフェールオーバーされると、Azure Site Recovery によって、指定した IP アドレスが VM に割り当てられます。

    ![Network properties](./media/hyper-v-site-walkthrough-network/network-design8.png)

4. フェールオーバーがトリガーされ、必要な IP アドレスが割り当てられた VM が Azure に作成された後は、[Vnet 間接続](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)を使用して目的のネットワークに接続することができます。 このアクションはスクリプト化することができます。
5. 192.168.1.0/24 が Azure に移動されたことを反映するために、ルートを適切に変更する必要があります。

    ![サブネットのフェールオーバー後](./media/hyper-v-site-walkthrough-network/network-design9.png)

### <a name="after-failover"></a>フェールオーバー後

上記のような Azure ネットワークがない場合は、フェールオーバー後にプライマリ サイトと Azure の間でサイト間 VPN 接続を作成できます。

## <a name="change-ip-addresses"></a>IP アドレスを変更する

フェールオーバー後に IP アドレスを保持する必要がない場合に Azure ネットワーク インフラストラクチャを設定する方法については、この[ブログ記事](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/)を参照してください。 アプリケーションの説明から始まり、オンプレミスと Azure でネットワークを設定する方法が示され、最後にフェールオーバーの実行に関する情報が示されています。  

## <a name="next-steps"></a>次のステップ

[手順 5: Azure を準備する](hyper-v-site-walkthrough-prepare-azure.md)方法に関するページに進む

