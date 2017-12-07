---
title: "SAP HANA on Azure の運用 | Microsoft Docs"
description: "SAP HANA on Azure ネイティブ VM の運用"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: juergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2017
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0328bdc40429e1e82a76f290f5bde39089db0a9d
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2017
---
# <a name="sap-hana-on-azure-operations-guide"></a>SAP HANA on Azure 運用ガイド
このガイドでは、Azure の仮想マシンに展開されている SAP HANA システムの運用に関するガイダンスを提供します。 このドキュメントは、いかなる標準の SAP ドキュメントも代替するものではありません。 SAP のガイドおよびノートは次の場所にあります。

- [SAP 管理ガイド](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP インストール ガイド](https://service.sap.com/instguides)
- [SAP Note](https://sservice.sap.com/notes)

以下のような Azure コンポーネントの基礎知識を持っていることが前提条件です。

- [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure のネットワークおよび VNet](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Azure 上の SAP NetWeaver や他の SAP コンポーネントについてのその他のドキュメントは、[[Azure ドキュメント]](https://docs.microsoft.com/azure/) の [[SAP on Azure]](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) セクションにあります。

## <a name="basic-setup-considerations"></a>基本設定の考慮事項
### <a name="connecting-into-azure"></a>Azure への接続
[Azure の仮想マシンの計画と SAP NetWeaver の実装] の [計画ガイド] で説明されているとおり、Azure 仮想マシンに接続するには 2 つの基本的な方法があります。 

- インターネットとジャンプ VM のパブリック エンドポイント経由で接続するか、または SAP HANA を実行している VM から接続します。
- [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) または Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 経由で接続する

運用環境シナリオまたは、SAP ソフトウェアと組み合わせて運用環境シナリオにフィードする非運用環境のシナリオでは、この図に示すように、VPN または ExpressRoute 経由のサイト対サイト接続が必要です。

![サイト間接続](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choice-of-azure-vm-types"></a>Azure VM の種類の選択肢
運用環境シナリオに使用できる Azure VM の種類は[ここ](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)から検索できます。 非運用環境シナリオについては、さまざまなネイティブの Azure VM から選択できます。 ただし、[SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533) に記述されている種類の VM に限定することをお勧めします。 以下の方法を使用したこれらの VM の Azure へのデプロイ

- Azure ポータル
- Azure PowerShell コマンドレット
- Azure CLI

また、[ここ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)で説明されているように、[SAP Cloud platform](https://cal.sap.com/) を使って Azure 仮想マシン サービスに完全にインストールされた SAP HANA プラットフォームをデプロイすることもできます。

### <a name="choice-of-azure-storage"></a>Azure ストレージの選択肢
Azure は、SAP HANA を実行する Azure VM に適した主に 2 つの種類のストレージを提供します。

- [Azure Standard Storage](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure では、Azure Standard と Premium Storage の 2 つの VHD 展開方法を提供します。 全体的なシナリオで可能な場合は、[Azure Managed Disk](https://azure.microsoft.com/services/managed-disks/) デプロイを使用することをお勧めします。

正確なストレージ タイプと、そのストレージ タイプの SLA については、[このドキュメント](https://azure.microsoft.com/pricing/details/managed-disks/)を参照してください。

/hana/data and /hana/log ボリュームには Azure Premium ディスクを使用することをお勧めします。 Azure Premium ディスクでは、複数の Premium Storage ディスクに LVM RAID を構築し、その RAID ボリュームを /hana/data および /hana/log ボリュームとして使用することがサポートされています。

Azure VM 上で SAP HANA をホストするためにお客様がこれまで使用した各種の一般的な VM タイプの構成には以下が挙げられます。

| VM の SKU | RAM | /hana/data and /hana/log<br /> LVM または MDADM によるストライピング | /hana/shared | /root ボリューム | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | -- |
| E16v3 | 128 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S10 |
| E32v3 | 256GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GB | 2 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1 TB | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1.7 TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2 TB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128ms | 3.8 TB | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 5 x S30 |


### <a name="azure-networking"></a>Azure のネットワーク
VPN または ExpressRoute サイト対サイト接続を使用して Azure に接続している場合は、最低 1 つの [Azure VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) が仮想ゲートウェイ経由で VPN または ExpressRoute 回線に接続されている必要があります。 仮想ゲートウェイは、Azure Vnet のサブネットにあります。 HANA をインストールするには、VNet 内に追加のサブネットを 2 つ作成します。 サブネットの 1 つは、SAP HANA インスタンスを実行する VM をホストし、もう 1 つのサブネットは、SAP HANA Studio または他の管理ソフトウェアをホストできる最終的な Jumpbox または Management VM を実行します。
HANA を実行する VM をインストールする場合は、VM には以下が必要です。

- 管理サブネットに接続された仮想 NIC 1 つと、Azure VM 内の SAP HANA インスタンスへオンプレミスまたは他のネットワークから接続するために使用される NIC 1 つの、計 2 つの仮想 NIC がインストールされている。
- 2 つの NIC の両方に静的プライベート IP アドレスが展開されている

考えられるさまざまな IP アドレスの割り当てについては、[ここ](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)をご覧ください。 

SAP HANA インスタンスまたは Jumpbox へ直接転送されるトラフィックは、HANA サブネットおよび管理サブネットに関連づけられた [Azure ネットワークのセキュリティ グループ](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg)によって転送されます。

展開スキーマの概要は次のようになります。

![SAP HANA の展開スキーマの概要](media/hana-vm-operations/hana-simple-networking.PNG)


サイト対サイト (Azure への VPN または ExpressRoute) 接続なしに Azure に SAP HANA を配置する場合は、Jumpbox VM を実行する Azure VM に割り当てられているパブリック IP アドレスを使って SAP HANA インスタンスにアクセスします。 また、単純な構造の場合、Azure に組み込まれた DNS サービスを使用してホスト名を解決します。 特に公開 IP アドレスを使用している場合は、Azure Network Security Groups を使用して、公開 IP アドレスを使用してアセットを実行している Azure サブネットへの接続が許可されるポートまたは IPアドレス範囲を制限します。 このような展開のスキーマは以下のようになります。
  
![サイト対サイト接続を使用しない SAP HANA の展開スキーマの概要](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations"></a>操作
### <a name="backup-and-restore-operations-on-azure-vms"></a>Azure VM のバックアップおよび復元操作
SAP HANA のバックアップおよび復元の可能性については、これらのドキュメントで説明されています。

- [SAP HANA のバックアップの概要](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA のファイル レベルのバックアップ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA のストレージ スナップショットのベンチマーク](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)



### <a name="start-and-restart-of-vms-containing-sap-hana"></a>SAP HANA を含む VM の起動と再起動
Azure パブリック クラウドの長所の 1 つは、消費しているコンピューティング分数だけ料金が発生することです。 つまり、SAP HANA を実行している VM をシャット ダウンした場合、その間のストレージ料金のみが請求されます。 SAP HANA の展開されている VM を再起動された場合、同じ IP アドレス (静的 IP アドレスで展開した場合) が使用されます。 


### <a name="saprouter-enabling-sap-remote-support"></a>SAPRouter を有効にする SAP のリモート サポート
オンプレミスの場所と Azure の間にサイト対サイト接続があり SAP コンポーネントが既に実行されている場合は、既に SAProuter が実行されている可能性が高くなります。 この場合、Azure に展開されている SAP HANA インスタンスに何も作業する必要はありません。 SAPRouter 構成で HANA をホスティングしている VM のプライベートおよび静的 IP アドレスを維持し、HANA VM をホスティングしているサブネットの NSG を適用するだけです (許可された TCP/IP ポート 3299 を通過するトラフィック)。

SAP HANA を展開し、インターネットから Azure に接続して、VM を実行する Vnet に SAPRouter を SAP HANA を使って インストールしていない場合は、ここに表示されている管理サブネット内の別の VM に SAPRouter をインストールする必要があります。


![サイト対サイト接続と SAPRouter を使用しない SAP HANA の展開スキーマの概要](media/hana-vm-operations/hana-simple-networking3.PNG)

SAPRouter は Jumpbox VM ではなく、個別の VM にインストールする必要があります。 VM を分離するには、静的 IP アドレスが必要です。 SAP がホストする SAPRouter に SAPRouter を接続するには (VM をインストールした SAPRouter に対応する)、SAP に連絡して、SAP SAPRouter インスタンスを構成する必要のある SAP から IP アドレスを取得する必要があります。 必要なポートは、TCP ポート 3299 のみです。
SAPRouter 経由でリモート サポート接続をセットアップし維持する方法の詳細については、この [SAP ソース](https://support.sap.com/en/tools/connectivity-tools/remote-support.html)を参照してください。

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Azure のネイティブ VM での SAP HANA の高可用性
SUSE Linux 12 SP1 以上を実行することにより、STONITH デバイスで Pacemaker クラスターを構築し、HANA システム レプリケーションおよび自動フェールオーバーとの同期レプリケーションを使用する SAP HANA 構成をセットアップできます。 セットアップの手順については、「[Azure Virtual Machines (VM) 上の SAP HANA の高可用性](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-high-availability)」の記事を参照してください。

 










