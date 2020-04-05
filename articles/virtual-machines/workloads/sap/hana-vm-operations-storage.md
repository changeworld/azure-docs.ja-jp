---
title: SAP HANA Azure 仮想マシンのストレージ構成 | Microsoft Docs
description: SAP HANA がデプロイされている VM のストレージの推奨事項。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b469c098db4f8d90147b491bcb54bd55d326b03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79080310"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 仮想マシンのストレージ構成

Azure は、SAP HANA を実行する Azure VM に適した、異なる種類のストレージを提供します。 SAP HANA のデプロイのために検討できる **SAP HANA 認定の Azure ストレージの種類**は次のとおりです。 

- Azure Premium SSD  
- [Ultra Disk](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

これらのディスクの種類の詳細については、[ディスクの種類の選択](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types)に関する記事を参照してください。

Azure では、Azure Standard と Premium Storage の 2 つの VHD 展開方法を提供します。 シナリオ全体で可能な場合は、[Azure マネージド ディスク](https://azure.microsoft.com/services/managed-disks/) デプロイを利用します。 

ストレージの種類と、IOPS およびストレージ スループットの SLA の一覧については、[マネージド ディスクに関する Azure ドキュメント](https://azure.microsoft.com/pricing/details/managed-disks/)をご覧ください。

> [!IMPORTANT]
> 選択した Azure ストレージの種類に関係なく、そのストレージで使用されるファイル システムは、特定のオペレーティング システムと DBMS 向けに SAP でサポートされている必要があります。 [SAP サポート ノート #405827](https://launchpad.support.sap.com/#/notes/405827) には、SAP HANA を含むさまざまなオペレーティング システムとデータベースでサポートされるファイル システムの一覧が示されています。 これは、任意のタスクの読み取りと書き込みのために SAP HANA がアクセスする可能性があるすべてのボリュームに適用されます。 特に Azure for SAP HANA で NFS を使用する場合は、この記事で後述するように、NFS バージョンに関する追加の制限が適用されます 


各種ストレージにおける SAP HANA 認定の最低条件は次のとおりです。 

- Azure Premium SSD - Azure [書き込みアクセラレータ](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)を使用して /hana/log をキャッシュする必要があります。 /hana/data ボリュームは、Azure 書き込みアクセラレータなしで Premium SSD や Ultra ディスクに配置できます。
- /hana/log ボリュームには少なくとも Azure Ultra ディスクが必要です。 /hana/data ボリュームは、Azure 書き込みアクセラレータなしで Premium SSD に配置できるほか、再起動時間を短縮するために Ultra ディスクに配置することができます。
- /hana/log **および** /hana/data 用の Azure NetApp Files の上にある **NFS v4.1** のボリューム。 /hana/shared のボリュームには、NFS v3 または NFS v4.1 プロトコルを使用できます。 /hana/data ボリュームと /hana/log ボリュームには、NFS v4.1 プロトコルが必須です。

ストレージの種類によっては、組み合わせることができます。 たとえば、/hana/data を Premium Storage に配置できます。また、必須の低遅延を確保するために、/hana/log を Ultra Disk ストレージ上に配置することもできます。 ただし、/hana/data の ANF に基づいて NFS v4.1 ボリュームを使用している場合は、/hana/log の ANF に基づいて別の NFS v4.1 ボリュームを使用する必要があります。 1 つのボリューム (/hana/data など) に対して ANF 上で NFS を使用し、他のボリューム (/hana/log など) に対して Azure Premium Storage または Ultra ディスクを使用することは**サポートされていません**。

オンプレミスでは、I/O サブシステムとその機能を気にする必要はほとんどありませんでした。 SAP HANA に対する最低限のストレージ要件が満たされていることを、アプライアンスのベンダーが保証する必要があったためです。 Azure インフラストラクチャを自身でビルドする場合は、これらの SAP が発行した要件の一部に注意する必要があります。 SAP から求められる一部の最小スループット特性により、次のことを行う必要があります。

- I/O サイズが 1 MB で、250 MB/秒の **/hana/log** で読み取り/書き込みを有効にする
- I/O サイズが 16 MB および 64 MB の **/hana/data** で、少なくとも 400 MB/秒の読み取りアクティビティを有効にする
- I/O サイズが 16 MB および 64 MB の **/hana/data** で、少なくとも 250 MB/秒の書き込みアクティビティを有効にする

DBMS システムではストレージの待ち時間が短いことが重要であるため、SAP HANA のようなシステムでもデータをメモリ内に保持します。 通常、ストレージのクリティカル パスは、DBMS システムのトランザクション ログの書き込みの周辺にあります。 しかし、クラッシュ復旧後のセーブポイントの書き込みやメモリ内のデータの読み込みなどの操作も重要である場合があります。 そのため、 **/hana/data** ボリュームと **/hana/log** ボリュームには、Azure Premium ディスクを利用することが**必須**となります。 SAP が要求する **/hana/log** と **/hana/data** の最小スループットを実現するには、複数の Azure Premium Storage ディスクで MDADM または LVM を使用して RAID 0 を構築する必要があります。 また、RAID ボリュームを **/hana/data** および **/hana/log** ボリュームとして使用してください。 

**推奨事項:RAID 0 のストライプ サイズとして、次のサイズを使用することをお勧めします。**

- **/hana/data** の場合は 256 KB
- **/hana/log** の場合は 32 KB

> [!IMPORTANT]
> /Hana/data のストライプ サイズは、最新の Linux バージョンでのカスタマー エクスペリエンスに基づいて、64 KB または 128 KB を必要としていた以前の推奨事項から 256 KB に変更されました。 256 KB のサイズによって少しパフォーマンスが向上しています

> [!NOTE]
> Azure Premium Storage と Standard Storage では VHD の 3 つのイメージを保持するので、RAID ボリュームを使用して冗長レベルを構成する必要はありません。 RAID ボリュームを使用するのは、十分な I/O スループットを提供するボリュームを構成するためです。

RAID の下の Azure VHD の数を増やすと、IOPS とストレージ スループットが累積されます。 そのため、3 つの Azure Premium Storage P30 ディスクで RAID 0 を構成した場合、1 つの Azure Premium Storage P30 ディスクの 3 倍の IOPS と 3 倍のストレージ スループットが得られます。

また、VM のサイズ設定や VM の決定を行うときには全体的な VM I/O スループットも考慮してください。 VM の全体的なストレージ スループットについては、「[メモリ最適化済み仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)」をご覧ください。

## <a name="linux-io-scheduler-mode"></a>Linux I/O Scheduler モード
Linux には、数種類の I/O スケジューリング モードがあります。 Linux ベンダーおよび SAP が一般的に推奨しているのは、ディスク ボリュームの I/O スケジューラ モードを **cfq** モードから **noop** (マルチ キュー以外) モードまたは **none** (マルチ キューの場合) モードに再構成することです。 詳細については、[SAP ノートの #1984787](https://launchpad.support.sap.com/#/notes/1984787) を参照してください。 


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Azure M シリーズ仮想マシン用の Azure 書き込みアクセラレータおよび Premium Storage を使用するソリューション
Azure 書き込みアクセラレータは、Azure M シリーズ VM 専用に提供される機能です。 名前が示すように、この機能の目的は、Azure Premium Storage に対する書き込みの I/O 待機時間を短縮することです。 SAP HANA の場合、書き込みアクセラレータは **/hana/log** ボリュームに対してのみ使用する必要があります。 したがって、 **/hana/data** と **/hana/log** は別々のボリュームであり、Azure 書き込みアクセラレータは **/hana/log** ボリュームのみをサポートします。 

> [!IMPORTANT]
> Azure Premium Storage を使用する場合は、Azure [書き込みアクセラレータ](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)または /hana/log ボリュームの使用が必須です。 書き込みアクセラレータは、Premium Storage と、M シリーズおよび Mv2 シリーズの VM でのみ使用できます。

下に示すキャッシュの推奨事項は、次のような SAP HANA の I/O 特性を前提にしています。

- HANA データ ファイルに対する読み取りワークロードはほとんどありません。 例外は、HANA インスタンスの再開後や、データが HANA に読み込まれたときの、大きなサイズの I/O です。 データ ファイルに対する大きな読み取り I/O の別のケースとして、HANA データベースのバックアップがあり得ます。 結果として、大部分のケースではすべてのデータ ファイル ボリュームを完全に読み取る必要があるため、読み取りキャッシュはほとんど意味がありません。
- データ ファイルに対する書き込みは、HANA セーブポイントと HANA クラッシュ復旧に基づいてバーストで発生します。 セーブポイントの書き込みは非同期であり、どのユーザー トランザクションも保持されません。 クラッシュ復旧中のデータの書き込みは、システムの応答性を再び向上させるためにパフォーマンス クリティカルです。 ただし、クラッシュ復旧はどちらかといえば例外的な状況です。
- HANA redo ファイルからの読み取りはほとんどありません。 例外は、トランザクション ログ バックアップやクラッシュ復旧の実行時、または HANA インスタンスの再開フェーズでの大きなサイズの I/O です。  
- SAP HANA redo ログ ファイルに対する主な負荷は書き込みです。 ワークロードの性質に応じて、I/O サイズを 4 KB まで小さくしたり、1 MB 以上の I/O サイズにできます。 SAP HANA redo ログに対する書き込み待機時間はパフォーマンス クリティカルです。
- すべての書き込みを信頼性の高い方法でディスク上に保存する必要があります。

**推奨事項:これらの観察された SAP HANA の I/O パターンの結果として、Azure Premium Storage を使用したさまざまなボリュームに対するキャッシュを次のように設定する必要があります。**

- **/hana/data** - キャッシュなし
- **/hana/log** - キャッシュなし - M シリーズおよび Mv2 シリーズの場合は例外。これらのシリーズでは、読み取りキャッシュなしで書き込みアクセラレータを有効にする必要があります。 
- **/hana/shared** - 読み取りキャッシュ

### <a name="production-recommended-storage-solution"></a>運用環境で推奨されるストレージ ソリューション

> [!IMPORTANT]
> Azure M シリーズ仮想マシンの SAP HANA 認定は、 **/hana/log** ボリュームに Azure 書き込みアクセラレータを使用している場合に限定されます。 そのため、運用環境シナリオでの Azure M シリーズ仮想マシンへの SAP HANA のデプロイは、 **/hana/log** ボリュームに Azure 書き込みアクセラレータを使用して構成することが求められます。  

> [!NOTE]
> 運用環境シナリオでは、[IAAS に関する SAP ドキュメント](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)で、特定の VM の種類が SAP HANA でサポートされているかどうかを確認してください。

推奨事項は、この記事で既に説明したように、SAP の最小要件を超えていることがよくあります。 一覧表示される推奨事項は、SAP による推奨サイズと、さまざまな種類の VM で提供される最大ストレージ スループットの間の妥協点です。

**推奨事項:運用環境シナリオで推奨される構成は次のようになります。**

| VM の SKU | RAM | 最大 VM I/O<br /> スループット | /hana/data | hana/log | /hana/shared | /root ボリューム | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GiB | 1000 MB/秒 | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/秒 | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/秒 | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/秒 |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/秒 | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/秒 | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/秒 | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/秒 | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/秒 | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

推奨されるさまざまなボリュームのストレージ スループットが、実行するワークロードに対応できるかどうかを確認します。 ワークロードに **/hana/data** および **/hana/log** のより大きなボリュームが必要な場合は、Azure Premium Storage VHD の数を増やす必要があります。 記載されている数よりも多くの VHD でボリュームのサイズを設定すると、その Azure 仮想マシンの種類の制限内で IOPS と I/O スループットが向上します。

Azure 書き込みアクセラレータは、[Azure マネージド ディスク](https://azure.microsoft.com/services/managed-disks/)との連携でのみ動作します。 そのため、少なくとも、 **/hana/log** ボリュームを形成する Azure Premium Storage ディスクはマネージド ディスクとしてデプロイする必要があります。

Azure 書き込みアクセラレータでサポートできる VM ごとの Azure Premium Storage VHD には制限があります。 現在の制限は次のとおりです。

- M128xx と M416xx VM では 16 個の VHD
- M64xx と M208xx VM では 8 個の VHD
- M32xx VM では 4 個の VHD

Azure 書き込みアクセラレータを有効にする方法の詳細については、[書き込みアクセラレータ](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)に関する記事をご覧ください。

Azure 書き込みアクセラレータの詳細と制限事項についても、同じドキュメントに記載されています。

**推奨事項:/hana/log ボリュームを形成するディスクには、書き込みアクセラレータを使用する必要があります**


### <a name="cost-conscious-azure-storage-configuration"></a>コストを意識した Azure Storage 構成
次の表に、顧客が Azure VM 上で SAP HANA をホストする際にも使用する VM の種類ごとの構成を示します。 VM の種類によっては、SAP HANA のストレージの最低条件をすべて満たしていないものも、SAP HANA と一緒に使用することが SAP で公式にサポートされていないものもあります。 ただし、これまでのところ、これらの VM は、運用環境以外のシナリオで問題なく動作しているように思われました。 **/hana/data** と **/hana/log** は 1 つのボリュームに結合されます。 そのため、Azure 書き込みアクセラレータの使用は IOPS の観点から制限となる可能性があります。

> [!NOTE]
> SAP でサポートされるシナリオについては、[IaaS に関する SAP ドキュメント](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)で、特定の VM の種類が SAP HANA でサポートされているかどうかを確認してください。

> [!NOTE]
> コストを意識したソリューション向けの以前の推奨からの変更は、[Azure Standard HDD ディスク](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)から優れたパフォーマンスとより信頼性の高い [Azure Standard SSD ディスク](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd)に移動することです。

推奨事項は、この記事で既に説明したように、SAP の最小要件を超えていることがよくあります。 一覧表示される推奨事項は、SAP による推奨サイズと、さまざまな種類の VM で提供される最大ストレージ スループットの間の妥協点です。

| VM の SKU | RAM | 最大 VM I/O<br /> スループット | /hana/data and /hana/log<br /> LVM または MDADM によるストライピング | /hana/shared | /root ボリューム | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GiB | 384 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1,200 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2,000 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GiB | 1,000 MB/秒 | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1,000 GiB | 1,000 MB/秒 | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1,750 GiB | 1,000 MB/秒 | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2,000 GiB | 2,000 MB/秒 |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3,800 GiB | 2,000 MB/秒 | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2,000 MB/秒 | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


比較的小さい VM に推奨されるディスク数 (3 x P20) は、[SAP TDI ストレージに関するホワイトペーパー](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)の領域の推奨事項によればボリュームにとって大きすぎます。 ただし、この表に示す選択肢は、SAP HANA に対して十分なディスク スループットを提供することができます。 メモリ ボリュームの 2 倍に相当するバックアップを保持するようにサイズ設定された **/hana/backup** ボリュームを変更する必要がある場合は、自由に調整してください。   
推奨されるさまざまなボリュームのストレージ スループットが、実行するワークロードに対応できるかどうかを確認します。 ワークロードに **/hana/data** および **/hana/log** のより大きなボリュームが必要な場合は、Azure Premium Storage VHD の数を増やす必要があります。 記載されている数よりも多くの VHD でボリュームのサイズを設定すると、その Azure 仮想マシンの種類の制限内で IOPS と I/O スループットが向上します。 

> [!NOTE]
> 上記の構成では、Azure Premium Storage と Azure Standard Storage を組み合わせて使用しているため、[Azure 仮想マシンの単一 VM の SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) のメリットは得られません。 ただし、各構成はコストを最適化するために選ばれています。 VM の構成を Azure single VM SLA に準拠させるには、Azure Standard Storage (Sxx) として示されている上記のすべてのディスクに対して、Premium Storage を選択する必要があります。


> [!NOTE]
> 明記されているディスク構成推奨事項は、SAP がそのインフラストラクチャ プロバイダーに表明している最小要件を対象にしています。 お客様の実際のデプロイ シナリオ/ワークロード シナリオにおいては、この推奨事項で十分な機能が達成できない状況がありました。 お客様が HANA の再起動後にデータを再読み込みしたところ、読み込みが遅かった、バックアップ構成でストレージへの帯域幅が少なかったなどです。 **/hana/log** が関連するケースもありました。特定のワークロードでは、5000 IOPS では不十分でした。 そこで、この推奨事項は開始点として利用し、ワークロードの要件に合わせて調整してください。
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>SAP HANA 用の Azure Ultra Disk ストレージ構成
Microsoft は、[Azure Ultra Disk](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk) と呼ばれる新しい Azure ストレージの種類をロール アウトしています。 これまでに提供されていた Azure Storage と Ultra Disk との大きな違いは、ディスク機能がディスク サイズにバインドされなくなることです。 顧客として、Ultra Disk に次の機能を定義できます。

- 4 GiB から 65,536 GiB のディスク範囲のサイズ
- 100 IOPS から 160K IOPS の IOPS の範囲 (最大値は VM の種類によっても異なる)
- 300 MB/秒から 2,000 MB/秒のストレージのスループット

Ultra Disk によって、自分のサイズ、IOPS、ディスク スループットの範囲を実行する 1 つのディスクを定義することができます。 Azure Premium Storage 上の LVM または MDADM などの論理ボリュームを使用する代わりに、IOPS とストレージ スループットの要件を満たすボリュームを構築します。 Ultra Disk と Premium Storage との間の構成の組み合わせを実行できます。 その結果、Ultra Disk の使用をパフォーマンス クリティカルの /hana/data と /hana/log ボリュームに制限し、Azure Premium Storage を使用したその他のボリュームをカバーすることができます

Ultra Disk のその他の利点は、Premium Storage と比較して読み取り待機時間が短いことです。 読み取り待機時間が短いということは、HANA の起動時間とその後のメモリへのデータの読み込みを短縮する必要がある場合に利点があります。 Ultra Disk ストレージの利点は、HANA によってセーブポイントが書き込まれているときにも感じることができます。 /hana/data 用の Premium Storage ディスクは、通常、書き込みアクセラレータでキャッシュされないため、Ultra Disk と比較して Premium Storage 上の/hana/data への書き込み待機時間は長くなります。 Ultra Disk でのセーブポイントの書き込みにより、Ultra Disk でのパフォーマンスが向上することが期待できます。

> [!IMPORTANT]
> Ultra Disk は、まだ、すべての Azure リージョンには存在しておらず、すべての種類の VM がサポートされているわけでもありません。 使用可能な Ultra Disk、サポートされている VM ファミリの詳細については、「[Azure で利用できるディスクの種類](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk)」を参照してください。

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>純粋な Ultra Disk 構成を使用した運用環境で推奨されるストレージ ソリューション
この構成では、/hana/data ボリュームと /hana/log ボリュームを別々に保持します。 推奨値は、[SAP TDI ストレージに関するホワイトペーパー](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) で推奨されているように SAP HANA およびストレージ構成に対して VM の種類を認定するために SAP が用意している KPI から導出されます。

推奨事項は、この記事で既に説明したように、SAP の最小要件を超えていることがよくあります。 一覧表示される推奨事項は、SAP による推奨サイズと、さまざまな種類の VM で提供される最大ストレージ スループットの間の妥協点です。

| VM の SKU | RAM | 最大 VM I/O<br /> スループット | /hana/data volume | /hana/data I/O throughput | /hana/data IOPS | /hana/log volume | /hana/log I/O throughput | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1,200 MB/秒 | 600 GB | 700 MBps | 7,500 | 512 GB | 500 MBps  | 2,000 |
| M32ts | 192 GiB | 500 MB/秒 | 250 GB | 400 MBps | 7,500 | 256 GB | 250 MBps  | 2,000 |
| M32ls | 256 GiB | 500 MB/秒 | 300 GB | 400 MBps | 7,500 | 256 GB | 250 MBps  | 2,000 |
| M64ls | 512 GiB | 1,000 MB/秒 | 600 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M64s | 1,000 GiB | 1,000 MB/秒 |  1,200 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M64ms | 1,750 GiB | 1,000 MB/秒 | 2,100 GB | 600 MBps | 7,500 | 512 GB | 400 MBps  | 2,500 |
| M128s | 2,000 GiB | 2,000 MB/秒 |2,400 GB | 1,200 MBps |9,000 | 512 GB | 800 MBps  | 3,000 | 
| M128ms | 3,800 GiB | 2,000 MB/秒 | 4,800 GB | 1200 MBps |9,000 | 512 GB | 800 MBps  | 3,000 | 
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 3,500 GB | 1,000 MBps | 9,000 | 512 GB | 400 MBps  | 2,500 | 
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 7,200 GB | 1,000 MBps | 9,000 | 512 GB | 400 MBps  | 2,500 | 
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 7,200 GB | 1,500 MBps | 9,000 | 512 GB | 800 MBps  | 3,000 | 
| M416ms_v2 | 11,400 GiB | 2,000 MB/秒 | 14,400 GB | 1,500 MBps | 9,000 | 512 GB | 800 MBps  | 3,000 |   

一覧の値は開始位置になることが意図されており、実際の要求に対して評価される必要があります。 Azure Ultra Disk を使用する利点は、VM または停止しているシステムに適用されたワークロードをシャットダウンする必要なく、IOPS とスループットの値を適用できることです。   

> [!NOTE]
> これまでのところ、Ultra Disk ストレージでのストレージ スナップショットは利用できません。 これにより、Azure Backup Services での VM スナップショットの使用はブロックされます。

### <a name="cost-conscious-storage-solution-with-pure-ultra-disk-configuration"></a>純粋な Ultra Disk 構成を使用したコストを意識したストレージ ソリューション
この構成では、/hana/data ボリュームと /hana/log ボリュームを同じディスク上で使用します。 推奨値は、[SAP TDI ストレージに関するホワイトペーパー](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) で推奨されているように SAP HANA およびストレージ構成に対して VM の種類を認定するために SAP が用意している KPI から導出されます。 

推奨事項は、この記事で既に説明したように、SAP の最小要件を超えていることがよくあります。 一覧表示される推奨事項は、SAP による推奨サイズと、さまざまな種類の VM で提供される最大ストレージ スループットの間の妥協点です。

| VM の SKU | RAM | 最大 VM I/O<br /> スループット | /hana/data および hana/log 用のボリューム | /hana/data とログ I/O のスループット | /hana/data と log IOPS |
| --- | --- | --- | --- | --- | --- |
| E64s_v3 | 432 GiB | 1,200 MB/秒 | 1,200 GB | 1,200 MBps | 9,500 | 
| M32ts | 192 GiB | 500 MB/秒 | 512 GB | 400 MBps | 9,500 | 
| M32ls | 256 GiB | 500 MB/秒 | 600 GB | 400 MBps | 9,500 | 
| M64ls | 512 GiB | 1,000 MB/秒 | 1,100 GB | 900 MBps | 10,000 | 
| M64s | 1,000 GiB | 1,000 MB/秒 |  1,700 GB | 900 MBps | 10,000 | 
| M64ms | 1,750 GiB | 1,000 MB/秒 | 2,600 GB | 900 MBps | 10,000 | 
| M128s | 2,000 GiB | 2,000 MB/秒 |2,900 GB | 1,800 MBps |12,000 | 
| M128ms | 3,800 GiB | 2,000 MB/秒 | 5,300 GB | 1,800 MBps |12,000 |  
| M208s_v2 | 2,850 GiB | 1,000 MB/秒 | 4,000 GB | 900 MBps | 10,000 |  
| M208ms_v2 | 5,700 GiB | 1,000 MB/秒 | 7,700 GB | 900 MBps | 10,000 | 
| M416s_v2 | 5,700 GiB | 2,000 MB/秒 | 7,700 GB | 1,800 MBps | 12,000 |  
| M416ms_v2 | 11,400 GiB | 2,000 MB/秒 | 15,000 GB | 1,800 MBps | 12,000 |    

一覧の値は開始位置になることが意図されており、実際の要求に対して評価される必要があります。 Azure Ultra Disk を使用する利点は、VM または停止しているシステムに適用されたワークロードをシャットダウンする必要なく、IOPS とスループットの値を適用できることです。  

## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Azure NetApp Files 上の NFS v4.1 ボリューム
Azure NetApp Files には、/hana/shared、/hana/data、/hana/log の各ボリュームに使用できるネイティブ NFS 共有が用意されています。 /hana/data ボリュームと /hana/log ボリュームに ANF ベースの NFS 共有を使用するには、v4.1 NFS プロトコルを使用する必要があります。 ANF に基づいて共有を行う場合、/hana/data ボリュームと /hana/log ボリュームの使用に対して NFS プロトコル v3 はサポートされていません。 

> [!IMPORTANT]
> Azure NetApp Files に実装されている NFS v3 プロトコルは、/hana/data および /hana/log に対してはサポートされて**いない**ため使用できません。 /hana/data ボリュームと /hana/log ボリュームには、機能上の観点から NFS 4.1 の使用が必須です。 一方、/hana/shared ボリュームの場合、機能上の観点から NFS v3 または NFS v4.1 プロトコルを使用できます。

### <a name="important-considerations"></a>重要な考慮事項
SAP Netweaver および SAP HANA 用に Azure NetApp Files を検討するときは、以下の重要な考慮事項に注意してください。

- 最小容量プールは 4 TiB です。  
- 最小ボリューム サイズは 100 GiB です。
- Azure NetApp Files と、Azure NetApp Files のボリュームがマウントされるすべての仮想マシンは、同じ Azure 仮想ネットワーク内、または同じリージョン内の[ピアリングされた仮想ネットワーク](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)内に存在する必要があります。  
- 選択した仮想ネットワークには、Azure NetApp Files に委任されているサブネットがある必要があります。
- Azure NetApp ボリュームのスループットは、「[Azure NetApp Files のサービス レベル](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)」に記載されているように、ボリューム クォータとサービス レベルの機能です。 HANA Azure NetApp ボリュームのサイズを設定するときは、そのスループットが HANA システム要件を満たしていることを確認してください。  
- Azure NetApp Files の[エクスポート ポリシー](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)では、ユーザーが制御できるのは、許可されたクライアント、アクセスの種類 (読み取りおよび書き込み、読み取り専用など) です。 
- Azure NetApp Files 機能は、ゾーンにはまだ対応していません。 現在、Azure NetApp Files 機能は、Azure リージョン内のすべての可用性ゾーンにはデプロイされていません。 Azure リージョンによっては、待ち時間が発生する可能性があることに注意してください。  
- 待ち時間を短縮するには、仮想マシンを Azure NetApp ストレージに近い場所にデプロイすることが重要です。 
- 仮想マシン上の <b>sid</b>adm のユーザー ID と `sapsys` のグループ ID は、Azure NetApp Files の構成と一致している必要があります。 

> [!IMPORTANT]
> SAP HANA ワークロードにとって、待ち時間の短縮は重要です。 Microsoft の担当者と協力して、仮想マシンと Azure NetApp Files ボリュームが確実に近接してデプロイされるようにします。  

> [!IMPORTANT]
> 仮想マシンと Azure NetApp の構成の間で、<b>sid</b>adm のユーザー ID と、`sapsys` のグループ ID が一致しない場合は、仮想マシンにマウントされた Azure NetApp ボリューム上のファイルのアクセス許可が `nobody` として表示されます。 Azure NetApp Files に[新しいシステムをオンボード](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)するときに、正しい <b>sid</b>adm のユーザー ID と `sapsys` のグループ ID を指定していることを確認してください。

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Azure NetApp Files 上の HANA データベースのサイズ指定

Azure NetApp ボリュームのスループットは、「[Azure NetApp Files のサービス レベル](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)」に記載されているように、ボリューム サイズとサービス レベルの機能です。 

Azure で SAP のインフラストラクチャを設計する際には、SAP による最小ストレージ スループット要件に注意する必要があります。これは、最小スループット特性につながります。

- I/O サイズが 1 MB で、250 MB/秒の /hana/log で読み取り/書き込みを有効にする  
- I/O サイズが 16 MB および 64 MB の /hana/data で、少なくとも 400 MB/秒の読み取りアクティビティを有効にする  
- I/O サイズが 16 MB および 64 MB の /hana/data で、少なくとも 250 MB/秒の書き込みアクティビティを有効にする  

ボリューム クォータの 1 TiB あたりの [Azure NetApp Files スループットの上限](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)は次のとおりです。
- Premium ストレージ層 - 64 MiB/秒  
- Ultra ストレージ層 - 128 MiB/秒  

> [!IMPORTANT]
> 1 つの NFS ボリュームにデプロイする容量に関係なく、スループットは、仮想マシンでコンシューマーによって活用される毎秒 1.2 から 1.4 GB までの帯域幅範囲にとどまるものと予想されます。 これは、ANF プランの基礎アーキテクチャと NFS 関連の Linux セッション上限に関係があります。 「[Azure NetApp Files のパフォーマンス ベンチマークのテスト結果](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-performance-benchmarks)」という記事に記載されているパフォーマンスとスループットの数値は、1 つの共有 NFS ボリュームと複数のクライアント VM (結果として複数のセッション) に対して行われたものです。 このシナリオは、SAP で測定するシナリオとは異なります。 その場合、ANF でホストされている NFS ボリュームに対して 1 つの VM からの スループットを測定します。

データとログの SAP 最小スループット要件を満たすため、および `/hana/shared` のガイドラインに従うと、推奨されるサイズは次のようになります。

| ボリューム | サイズ<br /> Premium ストレージ層 | サイズ<br /> Ultra ストレージ層 | サポートされる NFS プロトコル |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/shared | 4 ワーカー ノードあたり最大 (512 GB、1xRAM) | 4 ワーカー ノードあたり最大 (512 GB、1xRAM) | v3 または v4.1 |


> [!NOTE]
> ここで説明されている Azure NetApp Files のサイズ設定の推奨事項は、SAP がそのインフラストラクチャ プロバイダーに表明している最小要件を満たすことを目標としています。 実際の顧客のデプロイとワークロードのシナリオでは、これらは十分ではない場合があります。 これらの推奨事項は開始点として利用し、ご自分の固有のワークロードの要件に合わせて調整してください。  

そのため、Ultra Disk Storage 用に記載されているのと同様の ANF ボリューム用のスループットをデプロイすることを検討してください。 また、Ultra ディスク テーブルの場合と同じように、さまざまな VM SKU のボリュームに対して記載されているサイズも考慮してください。

> [!TIP]
> Azure NetApp Files ボリュームは、ボリュームを `unmount` したり、仮想マシンを停止したり、SAP HANA を停止したりすることなく、動的にサイズ変更することができます。 これにより、予想されるスループット要求と予期しないスループット要求の両方を柔軟に満たすことができます。

ANF でホストされる NFS v4.1 ボリュームを使用して、スタンバイ ノードを含む SAP HANA スケールアウト構成をデプロイする方法については、[SUSE Linux Enterprise Server 上に Azure VM のスタンバイ ノードと Azure NetApp Files を使用して SAP HANA をスケールアウトする方法](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)に関するドキュメントを参照してください。


## <a name="next-steps"></a>次のステップ
詳細については、次を参照してください。

- [Azure 仮想マシンの SAP HANA 高可用性ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)。
