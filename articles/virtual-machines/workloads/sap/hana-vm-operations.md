---
title: Azure における SAP HANA インフラストラクチャの構成と運用 | Microsoft Docs
description: Azure 仮想マシンにデプロイされている SAP HANA システムの運用ガイドです。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: acccc553c5b63b2acd0f9793b0397b25145449dd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699340"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Azure における SAP HANA インフラストラクチャの構成と運用
この記事では、Azure インフラストラクチャを構成し、Azure のネイティブ仮想マシン (VM) にデプロイされている SAP HANA システムを運用する方法について説明します。 また、この記事には、M128s VM SKU 向けの SAP HANA スケールアウトの構成情報も含まれています。 この記事は、以下の内容を含む標準の SAP ドキュメントを代替するものではありません。

- [SAP 管理ガイド](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [SAP インストール ガイド](https://service.sap.com/instguides)
- [SAP Note](https://sservice.sap.com/notes)

## <a name="prerequisites"></a>前提条件
このガイドを使用するには、次の Azure コンポーネントに関する基本的な知識が必要です。

- [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Azure ネットワーキングおよび仮想ネットワーク](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Azure Storage](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Azure 上の SAP NetWeaver や他の SAP コンポーネントについて詳しくは、[Azure のドキュメント](https://docs.microsoft.com/azure/) に関するページの [Azure 上の SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) に関するセクションをご覧ください。

## <a name="basic-setup-considerations"></a>基本設定の考慮事項
次のセクションでは、Azure VM に SAP HANA システムをデプロイする際の基本的なセットアップの考慮事項について説明します。

### <a name="connect-to-azure-virtual-machines"></a>Azure 仮想マシンに接続する
[Azure 仮想マシンの計画ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)で説明しているように、Azure VM に接続するには 2 つの基本的な方法が使用されます。

- JumpBox VM または SAP HANA を実行している VM 上で、インターネットとパブリック エンドポイントを介して接続します。
- [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) または Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 経由で接続します。

VPN または ExpressRoute 経由でのサイト対サイト接続は運用環境シナリオでは必須です。 この種類の接続は、SAP ソフトウェアが使用されている運用環境シナリオにフィードする、運用環境以外のシナリオでも必要です。 次の図に、サイト間接続の例を示します。

![サイト間接続](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Azure VM の種類を選択する
運用環境シナリオに使用する Azure VM の種類は、[IaaS に関する SAP のドキュメント](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)に記載されています。 運用環境以外のシナリオでは、さまざまなネイティブの Azure VM の種類が使用できます。

>[!NOTE]
> 運用環境以外のシナリオでは、[SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533) に記載されている VM の種類を使用します。 運用環境シナリオに Azure VM を使用する場合は、SAP が公開している[認定 IaaS プラットフォームの一覧](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)で SAP HANA 認定 VM を確認してください。

Azure に VM をデプロイするには、以下を使用します。

- Azure Portal
- Azure PowerShell コマンドレット
- Azure CLI

[SAP Cloud Platform](https://cal.sap.com/) を使って Azure VM サービスに完全にインストールされた SAP HANA プラットフォームをデプロイすることもできます。 このインストール プロセスについては、「[Azure に SAP S/4HANA または BW/4HANA をデプロイする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)」を参照してください。 リリースされている自動化については、[この GitHub の記事](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)を参照してください。

### <a name="choose-an-azure-storage-type"></a>Azure ストレージの種類を選択する
Azure は、SAP HANA を実行する Azure VM に適した 2 種類のストレージを提供します。  

- Standard ハード ディスク ドライブ (HDD)
- Premium ソリッドステート ドライブ (SSD)

これらのディスクの種類の詳細については、[ディスクの種類の選択](../../windows/disks-types.md)に関する記事を参照してください。

Azure では、Azure Standard Storage および Premium Storage 上の VHD について 2 つのデプロイ方法を提供します。 シナリオ全体で可能な場合は、[Azure マネージド ディスク](https://azure.microsoft.com/services/managed-disks/) デプロイを利用します。

ストレージの種類と、IOPS およびストレージ スループットの SLA の一覧については、[マネージド ディスクに関する Azure ドキュメント](https://azure.microsoft.com/pricing/details/managed-disks/)を参照してください。

### <a name="configure-the-storage-for-azure-virtual-machines"></a>Azure 仮想マシンのストレージを構成する

SAP HANA の最小ストレージ要件が満たされていることはアプライアンス ベンダーによって確認されているため、これまで I/O サブシステムとその機能について気にしたことはおそらくないでしょう。 Azure インフラストラクチャを自身で構築する場合は、これらの要件の一部にも注意する必要があります。 また、次のセクションに示された構成要件も理解しておく必要があります。 SAP HANA を実行する仮想マシンを構成する場合は、次のことが必要になる可能性があります。

- I/O サイズが 1 MB で、少なくとも 250 MB/秒の /hana/log で読み取り/書き込みボリュームを有効にする。
- I/O サイズが 16 MB および 64 MB の /hana/data で、少なくとも 400 MB/秒の読み取りアクティビティを有効にする。
- I/O サイズが 16 MB および 64 MB の /hana/data で、少なくとも 250 MB/秒の書き込みアクティビティを有効にする。

SAP HANA のように DBMS がデータをメモリ内に保持する場合でも、DBMS システムではストレージの待ち時間を短くすることが重要です。 通常、ストレージのクリティカル パスは、DBMS システムのトランザクション ログの書き込みの周辺にあります。 しかし、セーブポイントの書き込みやクラッシュ復旧後のメモリ内データの読み込みなどの操作も非常に重要である場合があります。 

/hana/data および /hana/log ボリュームへの Azure Premium ディスクの使用は必須です。 SAP が求める /hana/log および /hana/data の最小スループットを実現するには、複数の Azure Premium Storage ディスク上で mdadm または論理ボリューム マネージャー (LVM) を使用して RAID 0 を構築します。 さらに、RAID ボリュームを /hana/data および /hana/log ボリュームとして使用します。 RAID 0 には、次のストライプ サイズを使用することをお勧めします。

- /hana/data の場合は 64 KB または 128 KB
- /hana/log の場合は 32 KB

> [!NOTE]
> Azure Premium Storage と Standard Storage では VHD の 3 つのイメージが保持されるので、RAID ボリュームを使用して冗長レベルを構成する必要はありません。 RAID ボリュームを使用するのは、単に十分な I/O スループットを提供するボリュームを構成するためです。

RAID の下に多数の Azure VHD が累積される場合は、IOPS とストレージ スループット側からの累積になります。 3 つの Azure Premium Storage P30 ディスクで RAID 0 を構成した場合、1 つの Azure Premium Storage P30 ディスクの 3 倍の IOPS と 3 倍のストレージ スループットが得られます。

以下に示すキャッシュに関する推奨事項は、SAP HANA の I/O 特性を前提にしています。

- HANA データ ファイルに対する読み取りワークロードはほとんどありません。 例外は、HANA インスタンスの再起動後や HANA へのデータ読み込み時の大きなサイズの I/O です。 データ ファイルに対する大きな読み取り I/O の別のケースとして、HANA データベースのバックアップがあり得ます。 結果として、ほとんどの場合はすべてのデータ ファイル ボリュームを完全に読み取る必要があるため、読み取りキャッシュは意味がありません。
- データ ファイルに対する書き込みは、HANA セーブポイントと HANA クラッシュ復旧に基づいてバーストで発生します。 セーブポイントの書き込みは非同期であり、どのユーザー トランザクションも保持されません。 クラッシュ復旧中のデータの書き込みは、システムの応答性を再び向上させるためにパフォーマンス クリティカルです。 クラッシュ復旧はどちらかといえば例外的な状況です。
- HANA redo ファイルからの読み取りはほとんどありません。 例外は、トランザクション ログ バックアップ、クラッシュ復旧、または HANA インスタンスの再起動フェーズを実行するときの大きなサイズの I/O です。  
- SAP HANA redo ログ ファイルに対する主な負荷は書き込みです。 ワークロードの性質に応じて、I/O サイズを 4 KB まで小さくしたり、1 MB 以上の I/O サイズにしたりできます。 SAP HANA redo ログに対する書き込み待機時間はパフォーマンス クリティカルです。
- すべての書き込みを信頼性の高い方法でディスク上に保存する必要があります。

これらの観察された SAP HANA の I/O パターンの結果として、Azure Premium Storage を使用する各種ボリュームのキャッシュを次のように設定します。

- **/hana/data**: キャッシュなし。
- **/hana/log**: M シリーズの VM を除いてキャッシュなし (この記事の後半を参照してください)。
- **/hana/shared**: 読み取りキャッシュ。


また、VM のサイズ設定や VM の決定を行うときには全体的な VM I/O スループットにも留意してください。 VM の全体的なストレージ スループットについては、「[メモリ最適化済み仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)」を参照してください。

#### <a name="linux-io-scheduler-mode"></a>Linux I/O スケジューラ モード
Linux には、数種類の I/O スケジューリング モードがあります。 Linux ベンダーおよび SAP からの一般的な推奨事項は、ディスク ボリュームの I/O スケジューラ モードを **cfq** モードから **noop** モードに設定することです。 詳細については、[SAP Note #1984798](https://launchpad.support.sap.com/#/notes/1984787) をご覧ください。 


#### <a name="storage-solution-with-write-accelerator-for-azure-m-series-virtual-machines"></a>Azure M シリーズ仮想マシンの書き込みアクセラレータを使用するストレージ ソリューション
 書き込みアクセラレータは、Azure M シリーズ VM 専用にロールアウトされる Azure 機能です。 この機能の目的は、Azure Premium Storage に対する書き込みの I/O 待ち時間を短縮することです。 SAP HANA の場合、書き込みアクセラレータは /hana/log ボリュームに対してのみ使用する必要があります。 そのため、これまでに示した構成を変更する必要があります。 主な変更として、/hana/log ボリュームに対してのみ書き込みアクセラレータを使用するために、/hana/data と /hana/log を分離します。 

> [!IMPORTANT]
> Azure M シリーズ仮想マシンの SAP HANA 認定は、/hana/log ボリューム用の書き込みアクセラレータ専用です。 そのため、運用環境シナリオでの Azure M シリーズ仮想マシンへの SAP HANA のデプロイは、/hana/log ボリュームに対して書き込みアクセラレータを構成することが求められます。

> [!NOTE]
> 運用環境シナリオでは、[IAAS に関する SAP ドキュメント](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)で、特定の VM の種類が SAP HANA でサポートされているかどうかを確認してください。

推奨される構成を次の表に示します。

| VM の SKU | RAM | 最大 VM I/O<br /> throughput | /hana/data | hana/log | /hana/shared | /root ボリューム | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1,000 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1,000 GiB | 1,000 MB/秒 | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1,750 GiB | 1,000 MB/秒 | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2,000 GiB | 2,000 MB/秒 |3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P40 |
| M128ms | 3,800 GiB | 2,000 MB/秒 | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 2 x P50 |

推奨されるさまざまなボリュームのストレージ スループットが、実行するワークロードに対応できるかどうかを確認します。 /hana/data および /hana/log でワークロードが必要とするボリュームが大きくなると、Azure Premium Storage VHD の数も増加します。 記載されている数よりも多くの VHD でボリュームのサイズを設定すると、その Azure 仮想マシンの種類の制限内で IOPS と I/O スループットが向上します。

書き込みアクセラレータは、[Azure マネージド ディスク](https://azure.microsoft.com/services/managed-disks/)との連携でのみ動作します。 少なくとも、/hana/log ボリュームを形成する Azure Premium Storage ディスクはマネージド ディスクとしてデプロイする必要があります。

書き込みアクセラレータでサポートできる VM ごとの Azure Premium Storage VHD には制限があります。 現在の制限は次のとおりです。

- M128xx VM の場合は 16 個の VHD。
- M64xx VM の場合は 8 個の VHD。
- M32xx VM の場合は 4 個の VHD。

書き込みアクセラレータを有効にする方法の詳細については、[書き込みアクセラレータ](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)に関する記事を参照してください。

書き込みアクセラレータの詳細と制限事項については、同ドキュメントを参照してください。


#### <a name="cost-conscious-azure-storage-configuration"></a>コストを意識した Azure Storage 構成
次の表に、Azure VM 上で SAP HANA をホストする際によく使用される VM の種類ごとの構成を示します。 VM の種類によっては、SAP HANA の最低基準を必ずしもすべて満たしていない場合があります。 また、VM の種類によっては、SAP の SAP HANA で正式にサポートされていない場合もあります。 これまでのところ、これらの VM は、運用環境以外のシナリオで問題なく動作していました。 

> [!NOTE]
> 運用環境シナリオでは、[IAAS に関する SAP ドキュメント](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)で、特定の VM の種類が SAP HANA でサポートされているかどうかを確認してください。


| VM の SKU | RAM | 最大 VM I/O<br /> throughput | /hana/data and /hana/log<br /> LVM または mdadm によるストライピング | /hana/shared | /root ボリューム | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E16v3 | 128 GiB | 384 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S15 |
| E32v3 | 256 GiB | 768 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| E64v3 | 432 GiB | 1,200 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| GS5 | 448 GiB | 2,000 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S30 |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 | 1 x S20 |
| M64ls | 512 GiB | 1,000 MB/秒 | 3 x P20 | 1 x S20 | 1 x S6 | 1 x S6 |1 x S30 |
| M64s | 1,000 GiB | 1,000 MB/秒 | 2 x P30 | 1 x S30 | 1 x S6 | 1 x S6 |2 x S30 |
| M64ms | 1,750 GiB | 1,000 MB/秒 | 3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 3 x S30 |
| M128s | 2,000 GiB | 2,000 MB/秒 |3 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S40 |
| M128ms | 3,800 GiB | 2,000 MB/秒 | 5 x P30 | 1 x S30 | 1 x S6 | 1 x S6 | 2 x S50 |


比較的小さい VM に推奨されるディスク (3 x P20) は、[SAP TDI ストレージに関するホワイトペーパー](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)に記載されている領域の推奨値に対してボリュームのサイズが大きすぎます。 この表に示す選択肢は、SAP HANA に十分なディスク スループットを提供するためのものです。 メモリ ボリュームの 2 倍に相当するバックアップを保持するようにサイズ設定された **/hana/backup** ボリュームを変更する必要がある場合は、自由に調整してください。 

推奨されるさまざまなボリュームのストレージ スループットが、実行するワークロードに対応できるかどうかを確認します。 /hana/data および /hana/log でワークロードが必要とするボリュームが大きくなると、Azure Premium Storage VHD の数も増加します。 記載されている数よりも多くの VHD でボリュームのサイズを設定すると、その Azure 仮想マシンの種類の制限内で IOPS と I/O スループットが向上します。 

> [!NOTE]
> 上記の構成では、Azure Premium Storage と Azure Standard Storage を組み合わせて使用しているため、[Azure 仮想マシンの単一 VM の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) のメリットは得られません。 各構成はコストを最適化するために選ばれています。 VM の構成を Azure の単一 VM の SLA に準拠させるには、この表で Azure Standard Storage (Sxx) として掲載されているすべてのディスクに Premium Storage を選択してください。


> [!NOTE]
> ディスク構成の推奨事項は、SAP がそのインフラストラクチャ プロバイダーに提供する最小要件を対象としています。 実際の顧客のデプロイおよびワークロードのシナリオにおいては、状況によってこれらの推奨事項では十分な機能が提供されませんでした。 たとえば、顧客が HANA の再起動後により速いデータの再読み込みを必要とする場合や、バックアップ構成でストレージへのより高い帯域幅が必要になる場合がありました。 /hana/log が関連するケースもありました。特定のワークロードでは、5,000 IOPS では不十分でした。 これらの推奨事項を出発点として使用し、ワークロードの要件に合わせて調整してください。
>  

### <a name="set-up-azure-virtual-networks"></a>Azure 仮想ネットワークをセットアップする
VPN または Azure ExpressRoute を介した Azure へのサイト間接続がある場合は、仮想ゲートウェイ経由で VPN または ExpressRoute 回線に接続されている Azure 仮想ネットワークが 1 つ以上必要です。 シンプルなデプロイでは、仮想ゲートウェイは、SAP HANA インスタンスもホストする Azure 仮想ネットワークのサブネットにデプロイできます。 

SAP HANA をインストールするには、Azure 仮想ネットワーク内に 2 つのサブネットを追加作成します。 1 つのサブネットは、SAP HANA インスタンスを実行する VM をホストします。 もう 1 つのサブネットは、JumpBox または管理 VM を実行して、SAP HANA Studio、他の管理ソフトウェア、またはアプリケーション ソフトウェアをホストします。

> [!IMPORTANT]
> SAP NetWeaver、Hybris、または S/4HANA ベースの SAP システムの DBMS レイヤーと SAP アプリケーションの間の通信パスに [Azure ネットワーク仮想アプライアンス](https://azure.microsoft.com/solutions/network-appliances/)を構成することはサポートされていません。 この制限は、機能およびパフォーマンス上の理由からです。 SAP アプリケーション レイヤーと DBMS レイヤーの間の通信パスは、直接的なものである必要があります。 [アプリケーション セキュリティ グループ (ASG) およびネットワーク セキュリティ グループ (NSG) ルール](https://docs.microsoft.com/azure/virtual-network/security-overview)で直接通信パスが許可されている場合、この制限にこれらの Azure ASG および NSG ルールは含まれません。 
>
> ネットワーク仮想アプライアンスがサポートされないその他のシナリオは次のとおりです。 
>
> - 「[SUSE Linux Enterprise Server for SAP Applications 上の Azure VM での SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)」で説明されている、Linux Pacemaker クラスター ノードを表す Azure VM と SBD デバイスの間の通信パス。
> - 「[Azure のファイル共有を使用して Windows フェールオーバー クラスター上の SAP ASCS/SCS インスタンスをクラスター化する](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)」に従って設定された、Azure VM と Windows Server スケールアウト ファイル サーバーの間の通信パス。 
>
> 通信パスにネットワーク仮想アプライアンスがあると、2 つの通信パートナー間のネットワーク待ち時間が容易に倍増する可能性があります。 また、SAP アプリケーション レイヤーと DBMS レイヤーの間のクリティカル パスのスループットが制限される場合もあります。 一部の顧客シナリオでは、ネットワーク仮想アプライアンスが原因で Pacemaker Linux クラスターに障害が発生することがあります。 Linux Pacemaker クラスター ノード間の通信において、ネットワーク仮想アプライアンス経由で SBD デバイスと通信する場合があります。  
> 

> [!IMPORTANT]
> サポートされて "*いない*" もう 1 つの設計は、SAP アプリケーション レイヤーと DBMS レイヤーを、相互に[ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)されていない異なる Azure 仮想ネットワークに分離することです。 異なる Azure 仮想ネットワークを使用するのではなく、Azure 仮想ネットワーク内のサブネットを使用して SAP アプリケーション レイヤーと DBMS レイヤーを分離することをお勧めします。 
>
>推奨事項に従わず、代わりに 2 つのレイヤーを異なる仮想ネットワークに分離する場合は、2 つの仮想ネットワークを[ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)する必要があります。 
>
> 2 つの[ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)された Azure 仮想ネットワーク間のネットワーク トラフィックは転送コストの影響を受ける点に注意してください。 SAP アプリケーション レイヤーと DBMS レイヤーの間で、数テラバイトもの膨大な量のデータが交換されます。 SAP アプリケーション レイヤーと DBMS レイヤーが 2 つのピアリングされた Azure 仮想ネットワーク間で分離されている場合、相当なコストが蓄積される可能性があります。 

SAP HANA を実行するために VM をインストールするとき、VM には以下が必要です。

- 2 つの仮想 NIC がインストールされていること。 1 つの NIC は、管理サブネットに接続します。 もう 1 つの NIC は、オンプレミス ネットワークまたは他のネットワークから Azure VM 内の SAP HANA インスタンスに接続します。
- 両方の仮想 NIC に対してデプロイされる静的プライベート IP アドレス。

> [!NOTE]
> Azure を介して静的 IP アドレスを割り当てるということは、それらを個々の仮想 NIC に割り当てるということです。 ゲスト OS 内の静的 IP アドレスを仮想 NIC に割り当てないでください。 Azure Backup のような一部の Azure サービスは、少なくともプライマリ仮想 NIC が静的 IP アドレスではなく DHCP に設定されているという事実に依存します。 詳細については、「[Azure 仮想マシンのバックアップのトラブルシューティング](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)」を参照してください。 複数の静的 IP アドレスを 1 つの VM に割り当てるには、複数の仮想 NIC を VM に割り当てます。
>
>

永続的なデプロイの場合は、Azure に仮想データセンター ネットワーク アーキテクチャを作成します。 このアーキテクチャでは、オンプレミスに接続する Azure 仮想ネットワーク ゲートウェイを別個の Azure 仮想ネットワークに分離することをお勧めします。 この別個の仮想ネットワークでは、オンプレミスまたはイントラネットのどちらかに流れるすべてのトラフィックがホストされます。 このアプローチを使用することにより、この別個のハブ仮想ネットワークで Azure の仮想データセンターに流入するトラフィックの監査とログ記録を行うソフトウェアをデプロイすることが可能になります。 このように、1 つの仮想ネットワークで、Azure のデプロイに送受信されるトラフィックに関連するすべてのソフトウェアと構成がホストされます。


仮想データセンターのアプローチと関連する Azure 仮想ネットワーク設計の詳細については、以下を参照してください。 

- [Azure 仮想データセンター: ネットワーク パースペクティブ](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)。
- [Azure 仮想データセンターとエンタープライズ コントロール プレーン](https://docs.microsoft.com/azure/architecture/vdc/)。


>[!NOTE]
>[Azure 仮想ネットワーク ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)を使用してハブ仮想ネットワークとスポーク仮想ネットワーク間を流れるトラフィックは、追加[コスト](https://azure.microsoft.com/pricing/details/virtual-network/)の対象になります。 これらのコストを基に、仮想ネットワーク ピアリングをバイパスするために、状況に応じて、厳格なハブとスポークのネットワーク設計の実行と、スポークに接続する複数の [Azure ExpressRoute ゲートウェイ](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)の実行の間で妥協点を見出す必要があります。 
>
> Azure ExpressRoute ゲートウェイでも、追加の[コスト](https://azure.microsoft.com/pricing/details/vpn-gateway/)が発生します。 また、ネットワーク トラフィックのログ記録、監査、および監視に使用するサードパーティ製のソフトウェアでも、追加のコストが発生する場合があります。 仮想ネットワーク ピアリングを介したデータ交換のコストと、追加の ExpressRoute ゲートウェイおよびソフトウェア ライセンスによって生じるコストを検討してください。 仮想ネットワークの代わりにサブネットを分離単位として使用して、1 つの仮想ネットワーク内でマイクロセグメンテーションについて決定することができます。


IP アドレスの割り当てに使用できるさまざまな方法の概要については、「[Azure における IP アドレスの種類と割り当て方法](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)」を参照してください。 

SAP HANA を実行している VM では、割り当てられている静的 IP アドレスを操作します。 その理由は、HANA の一部の構成属性が IP アドレスを参照するためです。

[Azure NSG](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) は、SAP HANA インスタンスまたは JumpBox にルーティングされるトラフィックの送信に使用されます。 NSG と、最終的に[アプリケーション セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)は、SAP HANA サブネットと管理サブネットに関連付けられます。

次の図は、ハブとスポークの仮想ネットワーク アーキテクチャに従う SAP HANA の大まかなデプロイ スキーマの概要を示しています。

![SAP HANA の展開スキーマの概要](media/hana-vm-operations/hana-simple-networking.PNG)

サイト間接続を利用せずに Azure に SAP HANA をデプロイするには、パブリック インターネットから SAP HANA インスタンスをシールドして、転送プロキシの背後に隠します。 この基本のシナリオでは、ホスト名を解決するためにデプロイで Azure の組み込み DNS サービスに利用しています。 公開 IP アドレスが使用されるより複雑なデプロイの場合には、Azure 組み込み DNS サーバーが特に重要です。 Azure NSG と [Azure ネットワーク仮想アプライアンス](https://azure.microsoft.com/solutions/network-appliances/)を使用して、インターネットから Azure の Azure 仮想ネットワーク アーキテクチャへのルーティングを監視します。 

次の図は、ハブとスポークの仮想ネットワーク アーキテクチャでサイト間接続なしで SAP HANA をデプロイする方法の大まかなスキーマを示しています。
  
![サイト対サイト接続を使用しない SAP HANA のデプロイ スキーマの概要](media/hana-vm-operations/hana-simple-networking2.PNG)
 

ハブとスポークの仮想ネットワーク アーキテクチャを使用せずに Azure ネットワーク仮想アプライアンスを使用してインターネットからのアクセスを制御および監視する方法の別の説明については、「[Deploy highly available network virtual appliances (高可用性のネットワーク仮想アプライアンスをデプロイする)](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha)」を参照してください。


## <a name="configure-azure-infrastructure-for-sap-hana-scale-out"></a>SAP HANA スケールアウト用に Azure インフラストラクチャを構成する
Microsoft では、SAP HANA スケールアウト構成で認定されている M シリーズの VM SKU を 1 つ用意しています。 VM の種類である M128s は、最大 16 ノードのスケールアウトについて認定されています。 Azure VM における SAP HANA スケールアウト認定の変更については、[認定 IaaS プラットフォームの一覧](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)を参照してください。

Azure VM でスケールアウト構成をデプロイするために使用される最小の OS リリースは次のとおりです。

- SUSE Linux 12 SP3。
- Red Hat Linux 7.4。

16 ノードのスケールアウト認定の場合:

- 1 個のノードがマスター ノード。
- 最大 15 個のノードがワーカー ノード。

>[!NOTE]
>Azure VM のスケールアウト デプロイでは、スタンバイ ノードを使用することはできません。
>

スタンバイ ノードを構成できない理由は 2 つあります。

- Azure には、このポイントにネイティブな NFS サービスがありません。 その結果、NFS 共有はサードパーティの機能を利用して構成する必要があります。
- サードパーティの NFS 構成には、Azure にデプロイされたソリューションを使って SAP HANA のストレージ待ち時間の条件を達成できるものはありません。

その結果、/hana/data および /hana/log ボリュームは共有できません。 単一ノードのこれらのボリュームを共有しないので、スケールアウト構成で SAP HANA スタンバイ ノードは使用できなくなります。

次の図は、スケールアウト構成の単一ノードの基本設計を示しています。

![単一ノードのスケールアウトの基本設計](media/hana-vm-operations/scale-out-basics.PNG)

SAP HANA スケールアウトの VM ノードの基本構成は、次のようになります。

- /hana/shared の場合、SUSE Linux 12 SP3 に基づいて高可用性 NFS クラスターを構築します。 このクラスターでは、実際のスケールアウト構成および SAP NetWeaver または BW/4HANA Central Services の /hana/shared NFS 共有をホストします。 このような構成を構築する方法については、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)」を参照してください。
- その他のディスク ボリュームはすべて、異なるノード間では共有されず、NFS に基づきません。 非共有の /hana/data および /hana/log を使用したスケールアウト HANA インストールのインストール構成と手順については、この記事で後述します。

>[!NOTE]
>これまでの各図に示した高可用性 NFS クラスターは、SUSE Linux のみでサポートされています。 Red Hat に基づく高可用性 NFS ソリューションについては、後で説明します。

ノードのボリュームのサイズ設定は、/hana/shared を除き、スケールアップの場合と同じです。 次の表に、M128s VM SKU に推奨されるサイズと種類を示します。

| VM の SKU | RAM | 最大 VM I/O<br /> throughput | /hana/data | hana/log | /root ボリューム | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- |
| M128s | 2,000 GiB | 2,000 MB/秒 |3 x P30 | 2 x P20 | 1 x P6 | 1 x P6 | 2 x P40 |


推奨されるさまざまなボリュームのストレージ スループットが、実行するワークロードに対応できるかどうかを確認します。 /hana/data および /hana/log でワークロードが必要とするボリュームが大きくなると、Azure Premium Storage VHD の数も増加します。 記載されている数よりも多くの VHD でボリュームのサイズを設定すると、その Azure 仮想マシンの種類の制限内で IOPS と I/O スループットが向上します。 また、書き込みアクセラレータを、/hana/log ボリュームを形成するディスクに適用します。
 

スケールアウト用の /hana/shared ボリュームのサイズを 4 つのワーカー ノードごとに 1 つのワーカー ノードのメモリ サイズとして定義する式については、「[SAP HANA TDI Storage Requirements (SAP HANA TDI のストレージ要件)](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)を参照してください。

約 2 TB のメモリを搭載した SAP HANA スケールアウトの認定 M128s Azure VM を利用していると仮定した場合、SAP の推奨事項は次のようにまとめることができます。

- 1 つのマスター ノードと最大 4 つのワーカー ノードの場合、/hana/shared ボリュームのサイズは 2 TB です。
- 1 つのマスター ノードと 5 つおよび 8 つのワーカー ノードの場合、/hana/shared ボリュームのサイズは 4 TB です。
- 1 つのマスター ノードと 9 から 12 個のワーカー ノードの場合、/hana/shared ボリュームのサイズは 6 TB です。
- 1 つのマスター ノードと 12 から 15 個のワーカー ノードの場合、/hana/shared ボリュームのサイズは 8 TB です。

スケールアウト SAP HANA VM の単一ノード構成の図に示されているその他の重要な設計は、サブネット構成を使用した仮想ネットワークです。 SAP では、HANA ノード間の通信から、クライアントおよびアプリケーションへのトラフィックを分離することを強く推奨しています。 

この目標を達成するには、図に示すように、2 つの異なる仮想 NIC を VM に接続します。 2 つの仮想 NIC は別々のサブネットにあり、2 つの異なる IP アドレスを備えています。 その後、NSG またはユーザー定義ルートを使用して、ルーティング規則でトラフィックのフローを制御します。

特に Azure には、特定の仮想 NIC にサービスおよびクオータの品質を適用する手段や方法がありません。 その結果、クライアントやアプリケーションに対する通信とイントラノード通信を分離することで、一方のトラフィック ストリームがもう一方よりも優先されることはありません。 その代わり、分離によってスケールアウト構成のイントラノード通信がシールドされる点で、依然としてセキュリティ対策にはなります。  

>[!IMPORTANT]
>SAP では、このドキュメントで説明しているように、クライアントやアプリケーション側へのネットワーク トラフィックとイントラノードのトラフィックを分離することを強く推奨しています。 ここでは、前の図に示すように、アーキテクチャを導入することをお勧めします。
>

次の図では、ネットワークの観点からの最低限必要なネットワーク アーキテクチャを示しています。

![単一ノードのスケールアウトの基本設計](media/hana-vm-operations/scale-out-networking-overview.PNG)

これまでサポートされてきた上限は、1 個のマスター ノードに加えて 15 個のワーカー ノードです。

次の図は、ストレージの観点からのストレージ アーキテクチャを示しています。


![単一ノードのスケールアウトの基本設計](media/hana-vm-operations/scale-out-storage-overview.PNG)

/hana/shared ボリュームは、高可用性 NFS 共有の構成に配置されています。 その他のドライブはすべて、個々の VM のローカルにマウントされています。 

### <a name="highly-available-nfs-share"></a>高可用性 NFS 共有
これまで、高可用性 NFS クラスターは、SUSE Linux でのみ動作していました。 設定情報については、「[SUSE Linux Enterprise Server 上の Azure VM での NFS の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)」を参照してください。 NFS クラスターを、SAP HANA インスタンスを実行する Azure 仮想ネットワークの外部にある他の HANA 構成と共有しない場合は、同じ仮想ネットワークにインストールします。 それを独自のサブネットにインストールし、任意のトラフィックすべてがそのサブネットにアクセスできるわけではないことを確認します。 代わりに、そのサブネットへのトラフィックを、/hana/shared ボリュームへのトラフィックを実行する VM の IP アドレスに制限します。

/hana/shared トラフィックをルーティングする HANA スケールアウト VM の仮想 NIC に関連する推奨事項を次に示します。

- /hana/shared へのトラフィック量は中程度なので、最小構成でクライアント ネットワークに割り当てられている仮想 NIC を介してルーティングします。
- 最終的に、/hana/shared へのトラフィックのために、SAP HANA スケールアウト構成をデプロイする仮想ネットワーク内に 3 番目のサブネットをデプロイします。 そのサブネットでホストされる 3 番目の仮想 NIC を割り当てます。 NFS 共有へのトラフィックには、その 3 番目の仮想 NIC と、関連付けられている IP アドレスを使用します。 これで、別個のアクセス規則とルーティング規則を適用できるようになります。

>[!IMPORTANT]
>スケールアウトの手法で SAP HANA がデプロイされている VM と高可用性 NFS の間のネットワーク トラフィックは、どのような状況でも、[ネットワーク仮想アプライアンス](https://azure.microsoft.com/solutions/network-appliances/)または類似の仮想アプライアンス経由ではルーティングされない可能性があります。 Azure NSG は、このようなデバイスではありません。 ルーティング規則を確認して、SAP HANA を実行している VM から高可用性 NFS 共有にアクセスするときにネットワーク仮想アプライアンスまたは類似の仮想アプライアンスが確実に迂回されることを確認します。
> 

SAP HANA 構成間で高可用性 NFS クラスターを共有したい場合は、これらすべての HANA 構成を同じ仮想ネットワーク内に移動します。 
 

### <a name="install-an-sap-hana-scale-out-in-azure"></a>SAP HANA スケールアウトを Azure にインストールする
スケールアウト SAP 構成をインストールするには、次の一般的な手順に従います。

- 新しい Azure 仮想ネットワーク インフラストラクチャをデプロイするか調整します。
- Azure マネージド Premium Storage ボリュームを使用して、新しい VM をデプロイします。
- 高可用性 NFS クラスターを新しくデプロイするか、既存のクラスターを調整します。
- ネットワーク ルーティングを調整して、たとえば VM 間のイントラノード通信が[ネットワーク仮想アプライアンス](https://azure.microsoft.com/solutions/network-appliances/)経由でルーティングされないようにします。 VM と高可用性 NFS クラスター間のトラフィックについても同じことが当てはまります。
- SAP HANA マスター ノードをインストールする。
- SAP HANA マスター ノードの構成パラメーターを調整します。
- SAP HANA ワーカー ノードのインストールを続行します。

#### <a name="install-sap-hana-in-a-scale-out-configuration"></a>スケールアウト構成で SAP HANA をインストールする
Azure VM インフラストラクチャがデプロイされ、その他すべての準備が完了しました。 ここで、SAP HANA スケールアウト構成をインストールするには、次の手順に従います。

- SAP のドキュメントに従って SAP HANA マスター ノードをインストールします。
- "*インストール後、global.ini ファイルを変更し、パラメーター "basepath_shared = no" を global.ini に追加します*"。 このパラメーターを使用すると、ノード間で共有される /hana/data および /hana/log ボリュームがなくてもスケールアウトで SAP HANA を実行できます。 詳細については、[SAP Note #2080991](https://launchpad.support.sap.com/#/notes/2080991) を参照してください。
- global.ini のパラメーターを変更した後、SAP HANA インスタンスを再起動します。
- その他のワーカー ノードを追加します。 詳細については、[SAP HANA 管理ガイド](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html)を参照してください。 インストール中またはインストール後にローカルの hdblcm などを使用して、SAP HANA ノード間通信用の内部ネットワークを指定します。 詳細については、[SAP Note #2183363](https://launchpad.support.sap.com/#/notes/2183363) を参照してください。 

この設定ルーチンに従って、インストールしたスケールアウト構成では、非共有ディスクを使用して /hana/data および /hana/log が実行されます。 /hana/shared ボリュームは、高可用性 NFS 共有に配置されます。


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>Azure 仮想マシン用の SAP HANA Dynamic Tiering 2.0

Microsoft Azure では、Azure M シリーズ VM の SAP HANA 認定だけでなく、SAP HANA Dynamic Tiering 2.0 (DT 2.0) もサポートされています。 SAP HANA Dynamic Tiering に関するドキュメントへのリンクについては、この記事で後述する「DT 2.0 ドキュメントへのリンク」を参照してください。 Azure VM 内での SAP HANA Cockpit の使用など、製品のインストールや操作に違いはありませんが、Azure での公式サポートのために必須である、重要な点がいくつかあります。 これらの重要な点については、以降のセクションで説明します。 

SAP HANA DT 2.0 は、SAP BW または S4HANA ではサポートされていません。 現在の主なユース ケースは、ネイティブの HANA アプリケーションです。


### <a name="overview"></a>概要

次の図に、Microsoft Azure での DT 2.0 サポートの概要を示します。 公式認定に準拠するには、これらの必須要件に従ってください。

- DT 2.0 は専用の Azure VM にインストールする必要があります。 SAP HANA が実行されているのと同じ VM では動作しない可能性があります。
- SAP HANA および DT 2.0 VM は同じ Azure 仮想ネットワーク内にデプロイする必要があります。
- SAP HANA および DT 2.0 VM は、Azure 高速ネットワークを有効にしてデプロイする必要があります。
- DT 2.0 VM のストレージの種類は Azure Premium Storage である必要があります。
- DT 2.0 VM には複数の Azure ディスクを接続する必要があります。
- Azure ディスク全体でストライピングを使用することで、LVM または mdadm を使用してソフトウェア RAID またはストライプ ボリュームを作成する必要があります。

以降のセクションではさらに詳しく説明します。

![SAP HANA DT 2.0 のアーキテクチャの概要](media/hana-vm-operations/hana-dt-20.PNG)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>SAP HANA DT 2.0 専用の Azure VM

Azure IaaS では、専用の VM でのみ DT 2.0 がサポートされます。 HANA インスタンスが実行されているのと同じ Azure VM では DT 2.0 を実行できません。 最初は以下の 2 つの VM の種類を使用して、SAP HANA DT 2.0 を実行できます。

- M64-32ms 
- E32sv3 

VM の種類の説明については、「[メモリ最適化済み仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)」を参照してください。

コストを節約するためにウォーム データをオフロードするという DT 2.0 の基本的な考え方を考慮すると、対応する VM のサイズを使用するのが妥当です。 可能な組み合わせについて厳密な規則はありません。 特定の顧客のワークロードによって異なります。

推奨される構成を次の表に示します。

| SAP HANA VM の種類 | DT 2.0 VM の種類 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


SAP HANA 認定の M シリーズ VM とサポート対象の DT 2.0 VM (M64-32ms、E32sv3 など) のすべての組み合わせが可能です。


### <a name="azure-networking-and-sap-hana-dt-20"></a>Azure のネットワークと SAP HANA DT 2.0

専用の VM 上に DT 2.0 をインストールするには、DT 2.0 VM と SAP HANA VM の間のネットワーク スループットが 10 GB 以上である必要があります。 そのため、同じ Azure 仮想ネットワーク内にすべての VM を配置し、Azure 高速ネットワークを有効にすることが必須です。

Azure 高速ネットワークの詳細については、「[高速ネットワークを使った Linux 仮想マシンの作成](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)」を参照してください。

### <a name="vm-storage-for-sap-hana-dt-20"></a>SAP HANA DT 2.0 用の VM ストレージ

DT 2.0 ベスト プラクティス ガイダンスによると、最小ディスク IO スループットは、物理コアあたり 50 MB/秒です。 DT 2.0 でサポートされている 2 つの Azure VM の種類の仕様によると、VM の最大ディスク IO スループット制限は次のとおりです。

- **E32sv3**:  768 MB/秒。キャッシュなし。これは、物理コアあたり 48 MB/秒という比率を意味します
- **M64-32ms**: 1,000 MB/秒。キャッシュなし。これは、物理コアあたり 62.5 MB/秒という比率を意味します

VM あたりのディスク スループットの上限に達するように DT 2.0 VM に複数の Azure ディスクを接続し、OS レベルでのストライピングを使用してソフトウェア RAID を作成する必要があります。 単一の Azure ディスクでは、VM の上限に達するスループットを提供することはできません。 DT 2.0 を実行するには、Azure Premium Storage が必須です。 

- 使用可能な Azure ディスクの種類の詳細については、[Azure IaaS Windows VM 用のディスクの種類の選択](../../windows/disks-types.md)に関するページを参照してください。
- mdadm を使用してソフトウェア RAID を作成する方法の詳細については、「[Linux でのソフトウェア RAID の構成](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)」を参照してください。
- LVM を構成して最大スループット向けにストライプ ボリュームを作成する方法の詳細については、「[Azure で Linux VM の LVM を構成する](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)」を参照してください。

サイズ要件に応じて、VM の最大スループットを達成するためのさまざまなオプションがあります。 ここでは、VM スループットの上限に達するように DT 2.0 VM の種類ごとに可能なデータ ボリューム ディスク構成を示します。 E32sv3 VM は、より小規模なワークロードのエントリ レベルと見なされます。 速度が十分でない場合は、VM のサイズを M64-32ms に変更する必要がある可能性があります。

M64-32ms VM には大量のメモリがあるため、特に読み取り集中型のワークロードでは、IO の負荷が上限に達しない可能性があります。 顧客固有のワークロードによっては、ストライプ セット内のディスクが少なくても十分な場合があります。 安全策として、最大スループットを保証するために次のディスク構成が選択されています。


| VM の SKU | ディスク構成 1 | ディスク構成 2 | ディスク構成 3 | ディスク構成 4 | ディスク構成 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3.5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2.5 TB | 6 x P15 -> 1.5 TB | 


特にワークロードが読み取り集中型である場合は、データベース ソフトウェアのデータ ボリュームに推奨されるように Azure ホスト キャッシュの "読み取り専用" 設定をオンにすると、IO パフォーマンスが向上する可能性があります。 トランザクション ログの場合は、Azure ホスト ディスク キャッシュを "なし" にする必要があります。 

ログ ボリュームのサイズについては、データ サイズの 15% のヒューリスティックを開始点とすることをお勧めします。 ログ ボリュームを作成するには、コストとスループットの要件に応じてさまざまな Azure ディスクの種類を使用してください。 ログ ボリュームの場合、高い I/O スループットが必要です。 

VM の種類として M64-32ms を使用する場合は、[書き込みアクセラレータ](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)を有効にすることをお勧めします。 書き込みアクセラレータは、トランザクション ログに最適なディスク書き込み待ち時間を提供する Azure の機能です。 これは、M シリーズでのみ使用できます。 ただし、VM の種類ごとのディスクの最大数など、検討すべき項目がいくつかあります。 書き込みアクセラレータの詳細については、「[書き込みアクセラレータを有効にする](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)」を参照してください。


次の表に、ログ ボリュームのサイズを設定する際に役立ついくつかの例を示します。

| データ ボリュームのサイズとディスクの種類 | ログ ボリュームとディスクの種類の構成 1 | ログ ボリュームとディスクの種類の構成 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2.5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1.5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


SAP HANA スケールアウトの場合と同様に、SAP HANA VM と DT 2.0 VM の間で /hana/shared ディレクトリを共有する必要があります。 高可用性 NFS サーバーとして機能する専用の VM を使用して、SAP HANA スケールアウトと同じアーキテクチャを使用することをお勧めします。 共有バックアップ ボリュームを提供するには、同じ設計を使用します。 ただし、高可用性が必要であるか、バックアップ サーバーとして機能するために十分なストレージ容量がある専用の VM を使用するだけで十分であるかは、ユーザーが判断する必要があります。



### <a name="links-to-dt-20-documentation"></a>DT 2.0 ドキュメントへのリンク 

- [SAP HANA Dynamic Tiering のインストールと更新に関するガイド](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US)
- [SAP HANA Dynamic Tiering のチュートリアルとリソース](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US)
- [SAP HANA Dynamic Tiering の概念実証](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/)
- [SAP HANA 2.0 SPS 02 Dynamic Tiering の拡張](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/)




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
オンプレミスの場所と Azure の間にサイト間接続があり、SAP コンポーネントを実行している場合は、既に SAProuter を実行している可能性があります。 この場合は、リモート サポートを利用するには次の手順に従ってください。

- SAP HANA をホストする VM のプライベート IP アドレスと静的 IP アドレスを SAProuter 構成で管理します。
- TCP/IP ポート 3299 を介したトラフィックを許可するように、HANA VM をホストするサブネットの NSG を構成します。

インターネット経由で Azure に接続し、SAP HANA がインストールされた VM 用の SAP ルーターがない場合は、このコンポーネントをインストールします。 管理サブネット内の別の VM に SAProuter をインストールします。 

次の図は、SAP HANA をデプロイする場合の大まかなスキーマです。サイト対サイト接続はなく、SAProuter があります。

![サイト対サイト接続と SAProuter を使用しない SAP HANA のデプロイ スキーマの概要](media/hana-vm-operations/hana-simple-networking3.PNG)

SAProuter は、JumpBox VM ではなく、必ず別の VM にインストールしてください。 個別の VM には静的 IP アドレスが必要です。 ご使用の SAProuter を SAP でホストされている SAProuter に接続するには、IP アドレスについて SAP にお問い合わせください。 SAP でホストされている SAProuter は、VM にインストールした SAProuter インスタンスの相手側になります。 SAP に問い合わせた IP アドレスを使用して、ご使用の SAProuter インスタンスを構成します。 構成設定で必要なポートは TCP ポート 3299 のみです。

SAProuter 経由でリモート サポート接続を設定して維持する方法の詳細については、[SAP ドキュメント](https://support.sap.com/en/tools/connectivity-tools/remote-support.html)を参照してください。

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Azure のネイティブ VM での SAP HANA による高可用性
SUSE Linux Enterprise Server for SAP Applications 12 SP1 以降を実行している場合は、STONITH デバイスを使用して Pacemaker クラスターを確立できます。 このデバイスを使用して、HANA システム レプリケーションによる同期レプリケーションと自動フェールオーバーを使用する SAP HANA 構成を設定します。 設定手順の詳細については、[Azure 仮想マシンの SAP HANA 高可用性ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)を参照してください。
