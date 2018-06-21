---
title: SAP HANA on Azure の運用 | Microsoft Docs
description: Azure 仮想マシンにデプロイされている SAP HANA システムの運用ガイドです。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: juergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 61369fbf864db28ee0a9415bbb87dca2a185ed43
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809677"
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
> 運用環境以外のシナリオでは、[SAP ノート #1928533](https://launchpad.support.sap.com/#/notes/1928533) に記載されている VM の種類を使用します。 運用環境シナリオで Azure VM を使用する場合は、SAP が公開している[認定 IaaS プラットフォームの一覧](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)で SAP HANA 認定 VM を確認してください。

以下を使用して VM を Azure にデプロイします。

- Azure Portal
- Azure PowerShell コマンドレット
- Azure CLI

[SAP Cloud platform](https://cal.sap.com/) を使って Azure VM サービスに完全にインストールされた SAP HANA プラットフォームをデプロイすることもできます。 インストール プロセスについては、「[Azure に SAP S/4HANA または BW/4HANA をデプロイする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)」をご覧いただくか、[こちら](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)でリリースされている自動化スクリプトを参照してください。

### <a name="choose-azure-storage-type"></a>Azure ストレージの種類を選択する
Azure は、SAP HANA を実行する Azure VM に適した 2 種類のストレージを提供します。

- [Azure Standard Storage](https://docs.microsoft.com/azure/virtual-machines/windows/standard-storage)
- [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)

Azure では、Azure Standard と Premium Storage の 2 つの VHD 展開方法を提供します。 シナリオ全体で可能な場合は、[Azure 管理ディスク](https://azure.microsoft.com/services/managed-disks/) デプロイを利用します。

ストレージの種類と、IOPS およびストレージ スループットの SLA の一覧については、[管理ディスクに関する Azure ドキュメント](https://azure.microsoft.com/pricing/details/managed-disks/)をご覧ください。

### <a name="configuring-the-storage-for-azure-virtual-machines"></a>Azure 仮想マシンのストレージの構成

アプライアンス ベンダーは、SAP HANA の最小限のストレージ要件を満たしていることを確認する必要があるため、購入した SAP HANA アプライアンスがオンプレミス用であれば、I/O サブシステムとその機能を気にする必要はありませんでした。 Azure インフラストラクチャを自分で構築するときは、以下のセクションで推奨する構成要件を理解するためにも、これらの要件の一部を認識しておく必要があります。 SAP HANA を実行する仮想マシンを構成する場合も同様です。 求められる一部の特性により、次のことを行う必要があります。

- I/O サイズが 1 MB で、少なくとも 250 MB/秒の /hana/log で読み取り/書き込みボリュームを有効にする
- I/O サイズが 16 MB および 64 MB の /hana/data で、少なくとも 400 MB/秒の読み取りアクティビティを有効にする
- I/O サイズが 16 MB および 64 MB の /hana/data で、少なくとも 250 MB/秒の書き込みアクティビティを有効にする

DBMS システムではストレージの待機時間が短いことが重要であるため、SAP HANA のようなシステムでもデータをメモリ内に保持します。 通常、ストレージのクリティカル パスは、DBMS システムのトランザクション ログの書き込みの周辺にあります。 しかし、クラッシュ復旧後のセーブポイントの書き込みやメモリ内のデータの読み込みなどの操作も重要である場合があります。 そのため、/hana/data ボリュームと /hana/log ボリュームには、Azure Premium ディスクを利用することが必須となります。 SAP が要求する /hana/log と /hana/data の最小スループットを実現するには、複数の Azure Premium Storage ディスクで MDADM または LVM を使用して RAID 0 を構築し、RAID ボリュームを /hana/data ボリュームおよび /hana/log ボリュームとして使用する必要があります。 RAID 0 のストライプ サイズとして、次のサイズを使用することをお勧めします。

- /hana/data では 64K または 128K
- /hana/log では 32K

> [!NOTE]
> Azure Premium Storage と Standard Storage では VHD の 3 つのイメージを保持するので、RAID ボリュームを使用して冗長レベルを構成する必要はありません。 RAID ボリュームを使用するのは、十分な I/O スループットを提供するボリュームを構成するためです。

RAID の下の Azure VHD の数を増やすと、IOPS とストレージ スループットが累積されます。 そのため、3 つの Azure Premium Storage P30 ディスクで RAID 0 を構成した場合、1 つの Azure Premium Storage P30 ディスクの 3 倍の IOPS と 3 倍のストレージ スループットが得られます。

/hana/data および /hana/log に使用するディスクで Premium Storage のキャッシュを構成しないでください。 これらのボリュームを構築するすべてのディスクで、ディスクのキャッシュを "None" に設定する必要があります。

また、VM のサイズ設定や VM の決定を行うときには全体的な VM I/O スループットも考慮してください。 VM の全体的なストレージ スループットについては、「[メモリ最適化済み仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)」をご覧ください。

#### <a name="cost-conscious-azure-storage-configuration"></a>コストを意識した Azure Storage 構成
次の表に、Azure VM 上で SAP HANA をホストする際によく使用される VM の種類ごとの構成を示します。 SAP HANA の最低基準をすべて満たしているわけではない VM の種類もあります。 ただし、これまでのところ、これらの VM は、運用環境以外のシナリオで問題なく動作しているように思われました。 

> [!NOTE]
> 運用環境シナリオでは、[IAAS に関する SAP ドキュメント](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)で、特定の VM の種類が SAP HANA でサポートされているかどうかを確認してください。


| VM の SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data and /hana/log<br /> LVM または MDADM によるストライピング | /hana/shared | /root ボリューム | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GiB | 768 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 443 GiB | 1200 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2000 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 GiB | 1000 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1000 GiB | 1000 MB/秒 | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1750 GiB | 1000 MB/秒 | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2000 GiB | 2000 MB/秒 |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3800 GiB | 2000 MB/秒 | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


比較的小さい VM に推奨されるディスク数 (3 x P20) は、[SAP TDI ストレージに関するホワイトペーパー](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)の領域の推奨事項によればボリュームにとって大きすぎます。 ただし、この表に示す選択肢は、SAP HANA に対して十分なディスク スループットを提供することができます。 メモリ ボリュームの 2 倍に相当するバックアップを保持するようにサイズ設定された /hana/backup ボリュームを変更する必要がある場合は、自由に調整してください。   
推奨されるさまざまなボリュームのストレージ スループットが、実行するワークロードに対応できるかどうかを確認します。 ワークロードに /hana/data および /hana/log のより大きなボリュームが必要な場合は、Azure Premium Storage VHD の数を増やす必要があります。 表に記載されている数よりも多くの VHD でボリュームのサイズを設定すると、その Azure 仮想マシンの種類の制限内で IOPS と I/O スループットが向上します。 

> [!NOTE]
> 上記の構成では、Azure Premium Storage と Azure Standard Storage を組み合わせて使用しているため、[Azure 仮想マシンの単一 VM の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) のメリットは得られません。 ただし、各構成はコストを最適化するために選ばれています。


#### <a name="azure-storage-configuration-to-benefit-for-meeting-single-vm-sla"></a>単一 VM の SLA のメリットを得るための Azure Storage 構成
[Azure 仮想マシンの単一 VM の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) のメリットを得るには、Azure Premium Storage VHD だけを使用する必要があります。

> [!NOTE]
> 運用環境シナリオでは、[IAAS に関する SAP ドキュメント](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)で、特定の VM の種類が SAP HANA でサポートされているかどうかを確認してください。

| VM の SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data and /hana/log<br /> LVM または MDADM によるストライピング | /hana/shared | /root ボリューム | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 128 GiB | 768 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E16v3 | 128 GiB | 384 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P15 |
| E32v3 | 256 GiB | 768 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| E64v3 | 443 GiB | 1200 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| GS5 | 448 GiB | 2000 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P20 |
| M64ls | 512 GiB | 1000 MB/秒 | 3 x P20 | 1 x P20 | 1 x P6 | 1 x P6 | 1 x P30 |
| M64s | 1000 GiB | 1000 MB/秒 | 2 x P30 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/秒 | 3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/秒 |3 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/秒 | 5 x P30 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |


比較的小さい VM に推奨されるディスク数 (3 x P20) は、[SAP TDI ストレージに関するホワイトペーパー](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)の領域の推奨事項によればボリュームにとって大きすぎます。 ただし、この表に示す選択肢は、SAP HANA に対して十分なディスク スループットを提供することができます。 メモリ ボリュームの 2 倍に相当するバックアップを保持するようにサイズ設定された /hana/backup ボリュームを変更する必要がある場合は、自由に調整してください。  
推奨されるさまざまなボリュームのストレージ スループットが、実行するワークロードに対応できるかどうかを確認します。 ワークロードに /hana/data および /hana/log のより大きなボリュームが必要な場合は、Azure Premium Storage VHD の数を増やす必要があります。 表に記載されている数よりも多くの VHD でボリュームのサイズを設定すると、その Azure 仮想マシンの種類の制限内で IOPS と I/O スループットが向上します。 



#### <a name="storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Azure M シリーズ仮想マシンの Azure 書き込みアクセラレータを使用するストレージ ソリューション
Azure 書き込みアクセラレータは、M シリーズ VM 専用にロールアウトされる機能です。 名前が示すように、この機能の目的は、Azure Premium Storage に対する書き込みの I/O 待機時間を短縮することです。 SAP HANA の場合、書き込みアクセラレータは /hana/log ボリュームに対してのみ使用する必要があります。 そのため、これまでに示した構成を変更する必要があります。 主な変更として、/hana/log ボリュームに対してのみ Azure 書き込みアクセラレータを使用するために、/hana/data と /hana/log を分離します。 

> [!IMPORTANT]
> Azure M シリーズ仮想マシンの SAP HANA 認定は、/hana/log ボリュームに Azure 書き込みアクセラレータを使用している場合に限定されます。 そのため、運用環境シナリオでの Azure M シリーズ仮想マシンへの SAP HANA のデプロイは、/hana/log ボリュームに Azure 書き込みアクセラレータを使用して構成することが求められます。  

> [!NOTE]
> 運用環境シナリオでは、[IAAS に関する SAP ドキュメント](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)で、特定の VM の種類が SAP HANA でサポートされているかどうかを確認してください。

推奨される構成は次のようになります。

| VM の SKU | RAM | 最大 VM I/O<br /> Throughput | /hana/data | hana/log | /hana/shared | /root ボリューム | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/秒 | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/秒 | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/秒 |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/秒 | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

推奨されるさまざまなボリュームのストレージ スループットが、実行するワークロードに対応できるかどうかを確認します。 ワークロードに /hana/data および /hana/log のより大きなボリュームが必要な場合は、Azure Premium Storage VHD の数を増やす必要があります。 表に記載されている数よりも多くの VHD でボリュームのサイズを設定すると、その Azure 仮想マシンの種類の制限内で IOPS と I/O スループットが向上します。

Azure 書き込みアクセラレータは、[Azure 管理ディスク](https://azure.microsoft.com/services/managed-disks/)との連携でのみ動作します。 そのため、少なくとも、/hana/log ボリュームを形成する Azure Premium Storage ディスクは管理ディスクとしてデプロイする必要があります。

Azure 書き込みアクセラレータでサポートできる VM ごとに Azure Premium Storage VHD の制限があります。 現在の制限は次のとおりです。

- M128xx VM では 16 個の VHD
- M64xx VM では 8 個の VHD
- M32xx VM では 4 個の VHD

Azure 書き込みアクセラレータを有効にする方法の詳細については、[書き込みアクセラレータ](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)に関する記事をご覧ください。

Azure 書き込みアクセラレータの詳細と制限事項についても、同じドキュメントに記載されています。


### <a name="set-up-azure-virtual-networks"></a>Azure 仮想ネットワークをセットアップする
VPN または ExpressRoute を介して Azure に対するサイト対サイト接続がある場合は、最低 1 つの [Azure 仮想ネットワーク](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)が仮想ゲートウェイ経由で VPN または ExpressRoute 回線に接続されている必要があります。 仮想ゲートウェイは、Azure 仮想ネットワークのサブネットにあります。 SAP HANA をインストールするには、仮想ネットワーク内に 2 つのサブネットを追加作成します。 1 つのサブネットは、SAP HANA インスタンスを実行する VM をホストします。 もう 1 つのサブネットは、ジャンプボックスまたは管理 VM を実行し、SAP HANA Studio または他の管理ソフトウェアをホストします。

SAP HANA を実行するために VM をインストールするとき、VM には以下が必要です。

- 2 つの仮想 NIC がインストールされていること。1 つの NIC は管理サブネットに接続し、もう 1 つの NIC はオンプレミス ネットワークまたは他のネットワークから Azure VM 内の SAP HANA インスタンスに接続します。
- 両方の仮想 NIC に対してデプロイされる静的プライベート IP アドレス。

IP アドレスを割り当てるさまざまな方法の概要については、「[Azure における IP アドレスの種類と割り当て方法](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)」をご覧ください。 

SAP HANA を実行している VM では、割り当てられた静的 IP アドレスを使用する必要があります。 これは、HANA の一部の構成属性が IP アドレスを参照するためです。

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

インターネット経由で Azure に接続しているときに、SAP HANA を実行する VM 用の SAP ルーターがない場合は、このコンポーネントをインストールする必要があります。 管理サブネット内の別の VM に SAProuter をインストールします。 次の図は、SAP HANA をデプロイする場合の大まかなスキーマです。サイト対サイト接続はなく、SAProuter があります。

![サイト対サイト接続と SAProuter を使用しない SAP HANA のデプロイ スキーマの概要](media/hana-vm-operations/hana-simple-networking3.PNG)

SAProuter はジャンプボックス VM ではなく、個別の VM にインストールする必要があります。 個別の VM には静的 IP アドレスが必要です。 ご使用の SAProuter を SAP でホストされる SAProuter に接続するには、IP アドレスについて SAP にお問い合わせください。 (SAP でホストされる SAProuter は、VM にインストールした SAProuter インスタンスの相手側になります。)SAP に問い合わせた IP アドレスを使用して、ご使用の SAProuter インスタンスを構成します。 構成設定で必要なポートは TCP ポート 3299 のみです。

SAPRouter 経由でリモート サポート接続をセットアップし維持する方法について詳しくは、[SAP ドキュメント](https://support.sap.com/en/tools/connectivity-tools/remote-support.html)をご覧ください。

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Azure のネイティブ VM での SAP HANA の高可用性
SUSE Linux 12 SP1 以上を実行している場合は、STONITH デバイスを使用して Pacemaker クラスターを確立できます。 このデバイスを使用して、HANA システム レプリケーションによる同期レプリケーションと自動フェールオーバーを使用する SAP HANA 構成をセットアップできます。 セットアップ手順の詳細については、「[SAP HANA High Availability guide for Azure virtual machines (Azure 仮想マシンの SAP HANA 高可用性ガイド)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)」をご覧ください。
