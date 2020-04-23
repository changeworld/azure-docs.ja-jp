---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c3e5beaef7fcc9d407103834e2040957ff32984c
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008548"
---
Azure 共有ディスク (プレビュー) は、マネージド ディスクを複数の仮想マシン (VM) に同時に接続できるようにする Azure マネージド ディスクの新機能です。 マネージド ディスクを複数の VM に接続すると、新規にデプロイするか、既存のクラスター化されたアプリケーションを Azure に移行することができます。

## <a name="how-it-works"></a>しくみ

クラスター内の VM は、[SCSI 永続的な予約](https://www.t10.org/members/w_spc3.htm) (SCSI PR) を使用するクラスター化アプリケーションによって選択された予約に基づいて、接続されたディスクに対して読み取りまたは書き込みを行うことができます。 SCSI PR は、オンプレミスの記憶域ネットワーク (SAN) 上で実行されているアプリケーションによって利用される業界標準です。 マネージド ディスクで SCSI PR を有効にすると、これらのアプリケーションを Azure にそのまま移行することができます。

マネージド ディスクを共有することで、共有ブロック ストレージに複数の VM からアクセスできます。VM は論理ユニット番号 (LUN) として公開されます。 その後 LUN は、ターゲット (ディスク) からイニシエーター (VM) に提示されます。 これらの LUN は、VM からは直接接続ストレージ (DAS) やローカル ドライブのように見えます。

共有されたマネージド ディスクでは、SMB または NFS を使用してアクセスできるフル マネージドのファイルシステムをネイティブに提供していません。 Windows Server フェールオーバー クラスター (WSFC) や Pacemaker のような、クラスター ノードの通信と書き込みロックを処理するクラスター マネージャーを使用する必要があります。

## <a name="limitations"></a>制限事項

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>ディスク サイズ

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>サンプル ワークロード

### <a name="windows"></a>Windows

WSFC (クラスター ノード通信のすべてのコア インフラストラクチャを処理する) 上に構築されているほとんどの Windows ベースのクラスタリングでは、ことで、アプリケーションが並列アクセス パターンを活用できるようにしています。 WSFC では、ご使用の Windows Server のバージョンに応じて、CSV と非 CSV ベースのオプションの両方が有効になります。 詳細については、「[フェールオーバー クラスターを作成する](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster)」を参照してください。

WSFC で実行される一般的なアプリケーションには、次のようなものがあります。

- SQL Server フェールオーバー クラスター インスタンス (FCI)
- スケールアウト ファイル サーバー (SoFS)
- 汎用のファイル サーバー (IW ワークロード)
- リモート デスクトップ サーバー ユーザー プロファイル ディスク (RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Linux クラスターでは、[Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker) などのクラスター マネージャーを利用できます。 Pacemaker は [Corosync](http://corosync.github.io/corosync/) 上に構築され、高可用性環境にデプロイされたアプリケーションのクラスター通信を可能にします。 一般的なクラスター化されたファイルシステムには、[ocfs2](https://oss.oracle.com/projects/ocfs2/) と [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2) があります。 [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) や [sg_persist](https://linux.die.net/man/8/sg_persist) などのユーティリティを使用して、予約と登録を操作できます。

## <a name="persistent-reservation-flow"></a>永続的な予約フロー

次の図は、SCSI PR を利用してノード間のフェールオーバーを有効にする、2 ノードのクラスター化されたデータベース アプリケーションのサンプルを示しています。

![2 ノード クラスター。 クラスターで実行されているアプリケーションがディスクへのアクセスを処理している](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

フローは次のとおりです。

1. Azure VM1 と VM2 の両方で実行されているクラスター化アプリケーションは、ディスクに対して読み取りまたは書き込みするその意図を登録します。
1. その後、VM1 上のアプリケーション インスタンスが、そのディスクに書き込むための排他的な予約を受け取ります。
1. この予約はご使用の Azure ディスクに適用され、データベースがディスクに排他的に書き込めるようになります。 VM2 上のアプリケーション インスタンスからの書き込みは成功しません。
1. VM1 上のアプリケーション インスタンスがダウンすると、VM2 上のインスタンスがデータベースのフェールオーバーを開始し、ディスクを引き継げるようになります。
1. この予約は現在 Azure ディスクに適用されているため、このディスクでは VM1 からの書き込みを受け付けなくなります。 VM2 からの書き込みのみを受け付けます。
1. クラスター化アプリケーションは、データベースのフェールオーバーを完了し、VM2 からの要求を処理できます。

次の図は、機械学習モデルのトレーニングなど、並列プロセスを実行するためにディスクからデータを読み取る複数のノードで構成される、別の一般的なクラスター化されたワークロードを示しています。

![4 ノード VM クラスター。各ノードでは書き込みの意図が登録され、アプリケーションでは書き込み結果を適切に処理するため、排他的な予約を受け取る](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

フローは次のとおりです。

1. すべての VM で実行されているクラスター化アプリケーションは、ディスクに対して読み取りまたは書き込みするための意図を登録します。
1. VM1 上のアプリケーション インスタンスは、他の VM からのディスクへの読み取りを開いている間、ディスクへの書き込みの排他的な予約を受け取ります。
1. この予約は、Azure ディスクに適用されます。
1. これで、クラスター内のすべてのノードがディスクから読み取るれようになります。 クラスター内のすべてのノードに代わって、1 つのノードだけが結果をディスクに書き戻します。

### <a name="ultra-disks-reservation-flow"></a>Ultra ディスク予約フロー

Ultra ディスクから追加のスロットルが与えられ、スロットルが合計で 2 つになります。 このため、Ultra ディスク予約フローは前セクションの説明のとおりに動作できます。あるいは、パフォーマンスをさらに細かく調整したり、分配したりできます。

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text=" ":::

## <a name="ultra-disk-performance-throttles"></a>Ultra ディスク パフォーマンス スロットル

Ultra ディスクには、変更可能な属性を公開して変更を許可するという方法でパフォーマンス設定をユーザーに許可する独特の機能があります。 既定では変更可能な属性は 2 つだけですが、共有 Ultra ディスクには 2 つの属性が追加されています。


|属性  |説明  |
|---------|---------|
|DiskIOPSReadWrite     |書き込みアクセス権のある共有ディスクをマウントするすべての VM で許可される IOPS の合計数。         |
|DiskMBpsReadWrite     |書き込みアクセス権のある共有ディスクをマウントするすべての VM で許可される合計スループット (毎秒 MB)。         |
|DiskIOPSReadOnly*     |読み取り専用として共有ディスクをマウントするすべての VM で許可される IOPS の合計数。         |
|DiskMBpsReadOnly*     |読み取り専用として共有ディスクをマウントするすべての VM で許可される合計スループット (毎秒 MB)。         |

\* 共有 Ultra ディスクのみに適用

パフォーマンス属性はユーザーによる変更が可能であり、設定できます。そのしくみは次のように決められています。

- DiskIOPSReadWrite/DiskIOPSReadOnly: 
    - IOPS の上限は GiB あたり 300 IOPS であり、ディスクあたり最大 160K IOPS となる
    - 最小 100 IOPS
    - DiskIOPSReadWrite  + DiskIOPSReadOnly は少なくとも GiB あたり 2 IOPS
- DiskMBpsRead    Write/DiskMBpsReadOnly:
    - ディスク 1 つのスループット上限はプロビジョニングされた IOPS ごとに毎秒 256 KiB であり、ディスクあたり最大 2,000 MBps となる
    - ディスクあたりで保証される最小スループットはプロビジョニングされた IOPS ごとに毎秒 4 KiB であり、全体的ベースラインは最小 1 MBps となる

### <a name="examples"></a>例

次の例で取り上げるシナリオからは、共有 Ultra ディスクを具体的にどのように調整できるかわかります。

#### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>クラスター共有ボリュームを使用する 2 つのノード クラスター

次は、クラスター化された共有ボリュームを使用する 2 ノード WSFC の例です。 この構成では、両方の VM でディスクに同時に書き込みアクセスできます。その結果、2 つの VM 間で ReadWrite スロットルが分割され、ReadOnly スロットルは使用されません。

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="CSV 2 ノード Ultra の例":::

:::image-end:::

#### <a name="two-node-cluster-without-cluster-share-volumes"></a>クラスター共有ボリュームのない 2 つのノード クラスター

次は、クラスター化された共有ボリュームを使用しない 2 ノード WSFC の例です。 この構成では、1 つだけの VM がディスクに書き込みアクセスできます。 結果として、プライマリ VM に独占的に ReadWrite スロットルが使用され、ReadOnly スロットルはセカンダリでのみ使用されます。

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="CSV 2 ノードで CSV Ultra ディスクなしの例":::

:::image-end:::

#### <a name="four-node-linux-cluster"></a>4 ノード Linux クラスター

次は、シングル ライターが 1 つ、スケールアウト リーダーが 3 つ与えられた 4 ノード Linux クラスターの例です。 この構成では、1 つだけの VM がディスクに書き込みアクセスできます。 結果として、プライマリ VM に独占的に ReadWrite スロットルが使用され、ReadOnly スロットルはセカンダリ VM によって分割されます。

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="4 ノード Ultra の調整例":::

:::image-end:::