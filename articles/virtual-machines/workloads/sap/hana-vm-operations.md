---
title: "SAP HANA on Azure の運用 | Microsoft Docs"
description: "Azure 仮想マシンにデプロイされている SAP HANA システムの運用ガイドです。"
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
ms.openlocfilehash: 1d6991d40b9bb8543898bbbdc9d7c905dfe11536
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2018
---
# <a name="sap-hana-on-azure-operations-guide"></a>SAP HANA on Azure 運用ガイド
このドキュメントは、Azure のネイティブ仮想マシン (VM) にデプロイされている SAP HANA システムの運用に関するガイダンスを提供します。 このドキュメントは、以下の内容を含む標準の SAP ドキュメントを代替するものではありません。

- [SAP 管理ガイド](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP インストール ガイド](https://service.sap.com/instguides)
- [SAP ノート](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>前提条件
このガイドを使用するには、次の Azure コンポーネントに関する基本的な知識が必要です。

- [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure ネットワーキングおよび仮想ネットワーク](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Azure 上の SAP NetWeaver や他の SAP コンポーネントについて詳しくは、[Azure のドキュメント](https://docs.microsoft.com/azure/) に関するページの [Azure 上の SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) に関するセクションをご覧ください。

## <a name="basic-setup-considerations"></a>基本設定の考慮事項
次のセクションでは、Azure VM に SAP HANA システムをデプロイする際の基本的なセットアップの考慮事項について説明します。

### <a name="connect-into-azure-virtual-machines"></a>Azure 仮想マシンに接続する
[Azure 仮想マシンの計画ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)のページで説明しているように、Azure VM に接続するには 2 つの基本的な方法があります。

- ジャンプ VM または SAP HANA を実行する VM 上で、インターネットとパブリック エンドポイントを介して接続します。
- [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) または Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 経由で接続します。

VPN または ExpressRoute 経由でのサイト対サイト接続は運用環境シナリオでは必須です。 このタイプの接続は、SAP ソフトウェアが使用されている場合に運用環境シナリオにフィードする非運用環境シナリオでも必要です。 次の図に、サイト間接続の例を示します。

![サイト間接続](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Azure VM の種類を選択する
運用環境シナリオで使用できる Azure VM の種類は、[IAAS の SAP マニュアル](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)に記載されています。 運用環境以外のシナリオでは、さまざまなネイティブの Azure VM の種類が使用できます。

>[!NOTE]
>運用環境以外のシナリオでは、[SAP ノート #1928533](https://launchpad.support.sap.com/#/notes/1928533) に記載されている VM の種類を使用します。

以下を使用して VM を Azure にデプロイします。

- Azure Portal
- Azure PowerShell コマンドレット
- Azure CLI

[SAP Cloud platform](https://cal.sap.com/) を使って Azure VM サービスに完全にインストールされた SAP HANA プラットフォームをデプロイすることもできます。 インストール プロセスについては、[Azure での SAP S/4HANA または BW/4HANA のデプロイ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)に関するページをご覧ください。

### <a name="choose-azure-storage-type"></a>Azure ストレージの種類を選択する
Azure は、SAP HANA を実行する Azure VM に適した 2 種類のストレージを提供します。

- [Azure Standard Storage](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure では、Azure Standard と Premium Storage の 2 つの VHD 展開方法を提供します。 シナリオ全体で可能な場合は、[Azure 管理ディスク](https://azure.microsoft.com/services/managed-disks/) デプロイを利用します。

ストレージの種類や SLA の一覧については、[管理ディスクに関する Azure ドキュメント](https://azure.microsoft.com/pricing/details/managed-disks/)をご覧ください。

Azure Premium ディスクは、/hana/data ボリュームおよび /hana/log ボリュームにお勧めします。 Azure Premium ディスクでは、複数の Premium Storage ディスクに LVM RAID を構築し、その RAID ボリュームを /hana/data および /hana/log ボリュームとして使用できます。

次の表に、Azure VM 上で SAP HANA をホストするために顧客がよく使用する VM の種類ごとの構成を示します。

| VM の SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data and /hana/log<br /> LVM または MDADM によるストライピング | /hana/shared | /root ボリューム | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| E16v3 | 128 GiB | 384 MB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 GiB | 1200 GB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2000 GB | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M64s | 1000 GiB | 1000 GB | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 GiB | 1000 GB | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 GiB | 2000 GB |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 GiB | 2000 GB | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


> [!NOTE]
> 比較的小さい VM に推奨されるディスク数 (3 x P20) は、[SAP TDI ストレージに関するホワイトペーパー](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)の領域の推奨事項によればボリュームにとって大きすぎます。 ただし、この表に示す選択肢は、SAP HANA に対して十分なディスク スループットを提供することができます。 必要な I/O スループットが低い場合は、/hana/data と /hana/log に対する Premium Storage ディスクの選択を調整できます。 /hana/backup ボリュームのサイズ設定についても同様です。メモリ ボリュームの 2 倍に相当するバックアップを保持するようにサイズ設定を行いました。 必要な領域が少ない場合には調整できます。 また、VM のサイズ設定や VM の決定を行うときには全体的な VM I/O スループットも考慮してください。 全体的な VM スループットについて詳しくは、「[メモリ最適化済み仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)」をご覧ください。  

> [!NOTE]
> [Azure 仮想マシンの単一 VM の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) のメリットを享受するには、Standard Storage (Sxx) として示されているすべての VHD を Premium Storage (Pxx) に変更する必要があります。 


### <a name="set-up-azure-virtual-networks"></a>Azure 仮想ネットワークをセットアップする
VPN または ExpressRoute を介して Azure に対するサイト対サイト接続がある場合は、最低 1 つの [Azure 仮想ネットワーク](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)が仮想ゲートウェイ経由で VPN または ExpressRoute 回線に接続されている必要があります。 仮想ゲートウェイは、Azure 仮想ネットワークのサブネットにあります。 SAP HANA をインストールするには、仮想ネットワーク内に 2 つのサブネットを追加作成します。 1 つのサブネットは、SAP HANA インスタンスを実行する VM をホストします。 もう 1 つのサブネットは、ジャンプボックスまたは管理 VM を実行し、SAP HANA Studio または他の管理ソフトウェアをホストします。

SAP HANA を実行するために VM をインストールするとき、VM には以下が必要です。

- 2 つの仮想 NIC がインストールされていること。1 つの NIC は管理サブネットに接続し、もう 1 つの NIC はオンプレミス ネットワークまたは他のネットワークから Azure VM 内の SAP HANA インスタンスに接続します。
- 両方の仮想 NIC に対してデプロイされる静的プライベート IP アドレス。

IP アドレスを割り当てるさまざまな方法の概要については、「[Azure における IP アドレスの種類と割り当て方法](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)」をご覧ください。 

[Azure ネットワーク セキュリティ グループ (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) を使用して、SAP HANA インスタンスまたはジャンプボックスにルーティングされたトラフィックの送信先を示します。 NSG は SAP HANA サブネットと管理サブネットに関連付けられています。

次の図は、SAP HANA の大まかなデプロイ スキーマの概要です。

![SAP HANA の展開スキーマの概要](media/hana-vm-operations/hana-simple-networking.PNG)


サイト対サイト接続なしで Azure に SAP HANA をデプロイするには、パブリック IP アドレスを介して SAP HANA インスタンスにアクセスします。 IP アドレスは、ジャンプボックス VM を実行している Azure VM に割り当てる必要があります。 この基本のシナリオでは、デプロイは Azure の組み込み DNS サービスに依存して、ホスト名を解決します。 公開 IP アドレスが使用されるより複雑なデプロイの場合には、Azure 組み込み DNS サーバーが特に重要です。 Azure NSG を使用して、公開 IP アドレスを持つアセットを含む Azure サブネットに接続できるオープンのポートまたは IP アドレス範囲を制限します。 次の図は、サイト対サイト接続なしで SAP HANA をデプロイする場合の大まかなスキーマです。
  
![サイト対サイト接続を使用しない SAP HANA のデプロイ スキーマの概要](media/hana-vm-operations/hana-simple-networking2.PNG)
 


## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Azure VM 上に SAP HANA をデプロイするための操作
次のセクションでは、Azure VM 上での SAP HANA システムのデプロイに関連する操作の一部について説明します。

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Azure VM でのバックアップおよび復元操作
次のドキュメントで、SAP HANA デプロイをバックアップおよび復元する方法を説明しています。

- [SAP HANA のバックアップの概要](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA のファイルレベルのバックアップ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [SAP HANA のストレージ スナップショットのベンチマーク](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>SAP HANA を含む VM の起動および再起動
Azure パブリック クラウドの特長は、コンピューティングを利用した時間分しか課金されないということです。 たとえば、SAP HANA を実行している VM をシャットダウンすると、その間はストレージのコストのみが請求されます。 もう 1 つの特長は、初期デプロイで VM の静的な IP アドレスを指定すると使用できます。 SAP HANA を含む VM を再起動すると、その VM は以前の IP アドレスを使用して再起動します。 


### <a name="use-saprouter-for-sap-remote-support"></a>SAP リモート サポートのために SAProuter を使用する
オンプレミスの場所と Azure の間にサイト対サイト接続があり、SAP コンポーネントを実行している場合は、既に SAProuter を実行している可能性があります。 その場合は、リモート サポートのために次の条件を満たしてください。

- SAP HANA をホストする VM のプライベート IP アドレスと静的 IP アドレスを SAProuter 構成で管理します。
- TCP/IP ポート 3299 を介したトラフィックを許可するように、HANA VM をホストするサブネットの NSG を構成します。

インターネットを介して Azure に接続しているとき、SAP HANA を含む VM 用の SAProuter がない場合は、そのコンポーネントをインストールする必要があります。 管理サブネット内の別の VM に SAProuter をインストールします。 次の図は、SAP HANA をデプロイする場合の大まかなスキーマです。サイト対サイト接続はなく、SAProuter があります。

![サイト対サイト接続と SAProuter を使用しない SAP HANA のデプロイ スキーマの概要](media/hana-vm-operations/hana-simple-networking3.PNG)

SAProuter はジャンプボックス VM ではなく、個別の VM にインストールする必要があります。 個別の VM には静的 IP アドレスが必要です。 ご使用の SAProuter を SAP でホストされる SAProuter に接続するには、IP アドレスについて SAP にお問い合わせください。 (SAP でホストされる SAProuter は、VM にインストールした SAProuter インスタンスの相手側になります。)SAP に問い合わせた IP アドレスを使用して、ご使用の SAProuter インスタンスを構成します。 構成設定で必要なポートは TCP ポート 3299 のみです。

SAPRouter 経由でリモート サポート接続をセットアップし維持する方法について詳しくは、[SAP ドキュメント](https://support.sap.com/en/tools/connectivity-tools/remote-support.html)をご覧ください。

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Azure のネイティブ VM での SAP HANA の高可用性
SUSE Linux 12 SP1 以上を実行している場合は、STONITH デバイスを使用して Pacemaker クラスターを確立できます。 このデバイスを使用して、HANA システム レプリケーションとの同期レプリケーションと自動フェールオーバーを使用する SAP HANA 構成をセットアップできます。 セットアップの手順について詳しくは、[Azure 仮想マシン上の SAP HANA の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)に関するページをご覧ください。
