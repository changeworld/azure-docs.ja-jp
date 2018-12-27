---
title: Azure 上での IBM Db2 pureScale
description: この記事では、Azure 上で IBM Db2 pureScale 環境を実行するためのアーキテクチャを紹介します。
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: 5a7ae96a3730df2d0dae04dde6d7609ce69cdee5
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2018
ms.locfileid: "51978114"
---
# <a name="ibm-db2-purescale-on-azure"></a>Azure 上での IBM Db2 pureScale

IBM Db2 pureScale 環境は、Linux オペレーティング システム上で高可用性とスケーラビリティを備えた Azure 向けデータベース クラスター ソリューションを提供します。 この記事では、Azure 上で Db2 pureScale を実行するためのアーキテクチャを紹介します。

## <a name="overview"></a>概要

企業では長い間、オンライン トランザクション処理 (OLTP) のニーズに応えるために、従来のリレーショナル データベース管理システム (RDBMS) プラットフォームを使用してきました。 今日、容量を拡大し、コストを削減し、一定した運用コスト構造を維持するために、多くの企業がメインフレーム ベースのデータベース環境を Azure に移行しています。

多くの場合、移行はレガシープラットフォームを最新化する最初のステップとなります。 たとえば、ある企業では最近、z/OS 上で実行される IBM Db2 環境を Azure 上の IBM Db2 pureScale に再ホストしました。 元の環境と同一ではないものの、Linux 上の IBM Db2 pureScale は、メインフレーム上の Parallel Sysplex 構成で実行される z/OS 向けの IBM Db2 とほぼ同等の高可用性とスケーラビリティの特性を提供します。

この記事では、この Azure への移行に使用されるアーキテクチャについて説明します。 Red Hat Linux 7.4 は、構成をテストするために使用されていました。 このバージョンは、Azure Marketplace から入手できます。 Linux ディストリビューションを選択する前に、現在サポートされているバージョンを必ず確認してください。 詳細については、[IBM Db2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) および [GlusterFS](https://docs.gluster.org/en/latest/) に関するドキュメントをご覧ください。

この記事は単に、Db2 実装計画の出発点です。 業務要件は違っても、同じ基本パターンが適用されます。 また、このアーキテクチャのパターンは、Azure 上でのオンライン分析処理 (OLAP) アプリケーションにも使用できます。

この記事では、z/OS 対応の IBM Db2 データベースから Linux 上で実行される IBM Db2 pureScale へ移行する際の相違点や想定される移行タスクについては取り上げていません。 また、Db2 z/OS から Db2 pureScale へ移行する際の、同等サイズの見積りやワークロード分析についても取り上げません。 お使いの環境に最適な Db2 pureScale アーキテクチャを決定できるように、完全なサイズ見積りの演習を完了して、仮説を立てることを強くお勧めします。 他の要素の中では、ソース システム上で、Data Sharing Architecture、Coupling Facility 構成、および DDF 利用状況統計を使った Db2 z/OS Parallel Sysplex について必ず検討してください。

> [!NOTE]
> この記事では、Db2 移行の 1 つの方法を取り上げていますが、方法は他にもあります。 たとえば、Db2 pureScale は仮想化されたオンプレミス環境で実行することもできます。 IBM ではさまざまな構成で、Microsoft Hyper-V 上での Db2 をサポートしています。 詳細については、IBM Knowledge Center の[Db2 pureScale 仮想化アーキテクチャ](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html)に関するページをご覧ください。

## <a name="architecture"></a>アーキテクチャ

Azure 上での高可用性とスケーラビリティをサポートするために、スケールアウトの共有データ アーキテクチャを Db2 pureScale に使用できます。 次のアーキテクチャの例は、当社の顧客の移行に使用されたものです。

![](media/db2-purescale-on-azure/pureScaleArchitecture.png "ストレージとネットワークを示した Azure 仮想マシン上の Db2 pureScale")


アーキテクチャの図は、Db2 pureScale クラスターに必要な論理層を示しています。 これらには、クライアント用、管理、キャッシング用、データベース エンジン用、および共有ストレージ用の仮想マシンが含まれます。 データベース エンジン ノードに加えて、図には、クラスター キャッシング機能 (CF) と呼ばれるものに使用される 2 つのノードも含まれています。 データベース エンジン自体には、最低 2 つのノードが使用されます。pureScale クラスターに属する Db2 サーバーはメンバーと呼ばれます。 クラスターは、スケールアウト ストレージと高可用性を提供するために、iSCSI 経由で 3 ノード GlusterFS 共有ストレージ クラスターに接続されています。 Db2 pureScale は、Linux を実行している Azure 仮想マシンにインストールされます。

この方法は単に、組織で必要とされるサイズとスケールに適合するように修正可能なテンプレートであり、次の条件に基づいています。

-   2 つ以上のデータベース メンバーが、少なくとも 2 つの CF ノードと連結されます。これにより、共有メモリのグローバル バッファー プール (GBP) とグローバル ロック マネージャー (GLM) サービスを管理して、複数のアクティブなメンバーからの共有アクセスとロックの競合を制御します。 1 つの CF ノードはプライマリとして、もう 1 つはセカンダリのフェールオーバー CF ノードとして機能します。 単一障害点がある環境を作成しないように、Db2 pureScale クラスターには最低 4 つのノードが必要です。

-   高パフォーマンスの共有ストレージ (図 1 の P30 サイズに表示)。各 Gluster FS ノードで使用されます。

-   データ メンバーと共有ストレージの高パフォーマンス ネットワーク。

### <a name="compute-considerations"></a>コンピューティングに関する考慮事項

このアーキテクチャは、Azure 仮想マシン上でアプリケーション、ストレージ、およびデータ層を実行します。 [展開セットアップ スクリプト](http://aka.ms/db2onazure)では、以下を作成します。

-   Db2 pureScale クラスター。 Azure 上で必要なコンピューティング リソースの種類は、設定に依存します。 一般的には、次の 2 つの方法を使用できます。

    -   マルチノードの、高パフォーマンス コンピューティング (HPC) 形式のネットワークを使用する。複数の小規模から中規模サイズのインスタンスから共有ストレージにアクセスします。 この HPC タイプの構成では、Azure のメモリ最適化 E シリーズまたはストレージ最適化 L シリーズの[仮想マシン](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)によって、必要なコンピューティング性能が提供されます。

    -   データ エンジンに使用する大規模な仮想マシン インスタンスの数を減らす。 大規模インスタンスの場合、大量のメモリ内ワークロードには最大メモリ最適化 [M シリーズ](https://azure.microsoft.com/pricing/details/virtual-machines/series/)の仮想マシンが理想的ですが、Db2 の実行に使用される Logical Partition (LPAR) のサイズに応じて、専用のインスタンスが必要になる可能性があります。

-   Db2 CF では、E シリーズや L シリーズなど、メモリ最適化の仮想マシンを使用します。

-   GlusterFS ストレージでは、Linux を実行している Standard\_DS4\_v2 仮想マシンを使用します。

-   GlusterFS ジャンプボックスは、Linux を実行している Standard\_DS2\_v2 仮想マシンです。

-   クライアントは、Windows を実行している Standard\_DS3\_v2 仮想マシンです (テストに使用されます)。

-   監視サーバーは、Linux を実行している Standard\_DS3\_v2 仮想マシンです (Db2 pureScale に使用されます)。

> [!NOTE]
> Db2 pureScale クラスターでは、最低 2 つの Db2 インスタンスが必要になります。 また、キャッシュ インスタンスとロック マネージャー インスタンスも必要になります。

### <a name="storage-considerations"></a>ストレージに関する考慮事項

Oracle RAC と同様に、Db2 pureScale は、高パフォーマンス ブロック I/O のスケールアウト データベースです。 ニーズに適した入手可能な最大の [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) を使用することをお勧めします。 たとえば、開発およびテスト環境には、より小規模なストレージ オプションが適しているかもしれませんが、実稼働環境では多くの場合、より大規模なストレージ容量が必要になります。 例のアーキテクチャでは、サイズと価格に対する IOPS の比率から、[P30](https://azure.microsoft.com/pricing/details/managed-disks/) を使用しています。 最適なパフォーマンスのためには、サイズに関係なく Premium Storage を使用します。

Db2 pureScale では、あらゆるものを共有するアーキテクチャを使用します。全クラスタ ノードからすべてのデータにアクセスできます。 オンデマンドまたは専用インスタンスのどちらであっても、複数のインスタンス間で Premium Storage を共有する必要があります。

大規模な Db2 pureScale クラスターでは、100,000 の IOPS と共に、200 テラバイト (TB) 以上の Premium 共有ストレージを必要とする場合があります。 Db2 pureScale では、Azure 上で使用できる iSCSI ブロック インターフェイスがサポートされています。 ISCSI インターフェイスには、GlusterFS、S2D、または別のツールを使って実装できる共有ストレージ クラスターが必要になります。 このタイプのソリューションでは、Azure 上に仮想ストレージ エリア ネットワーク (vSAN) デバイスを作成します。 Db2 pureScale では vSAN を使って、複数の仮想マシン間でのデータ共有に使用されるクラスター化されたファイル システムをインストールします。

このアーキテクチャには、特にクラウド ストレージに対して最適化された無料のスケーラブルなオープン ソース分散ファイル システムとして、GlusterFS ファイル システムを使用しました。

### <a name="networking-considerations"></a>ネットワークに関する考慮事項

IBM では、Db2 pureScale クラスターのすべてのメンバーに InfiniBand ネットワークをお勧めします。また、Db2 pureScale では、使用される CF に対して、利用可能な場合はリモート ダイレクト メモリ アクセス (RDMA) を使用します。

セットアップの間に、すべての仮想マシンを含む 1 つの Azure [リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)が作成されます。 リソースは一般に、有効期間や管理者に基づいてグループ化されます。 このアーキテクチャの仮想マシンでは、[高速ネットワーク](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)が必要です。これは、仮想マシンに対して単一のルート I/O 仮想化 (SR-IOV) 経由で、一貫性のある超低ネットワーク待機時間を提供する Azure 機能です。

どの Azure 仮想マシンも、メイン、Gluster FS フロント エンド (gfsfe)、Gluster FS バック エンド (bfsbe)、Db2 pureScale (db2be)、および Db2 purescale フロント エンド (db2fe) という複数のサブネットにセグメント化された仮想ネットワークに展開されます。 また、インストール スクリプトは、メイン サブネットの仮想マシン上にプライマリ [NIC](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) を作成します。

[ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) は、仮想ネットワーク内でネットワーク トラフィックを制限し、サブネットを分離するために使用されます。

Azure では、Db2 pureScale は、ストレージのネットワーク接続として TCP/IP を使用する必要があります。

## <a name="next-steps"></a>次の手順

-   [Azure 上にこのアーキテクチャを展開する](deploy-ibm-db2-purescale-azure.md)
