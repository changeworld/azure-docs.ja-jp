---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a14ae76e15c1adb59917e61fbcbdaa34a7efa2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77472020"
---
Azure 共有ディスク (プレビュー) は、Azure マネージド ディスクを複数の仮想マシン (VM) に同時に接続できるようにする Azure マネージド ディスクの新機能です。 マネージド ディスクを複数の VM に接続すると、新規にデプロイするか、既存のクラスター化されたアプリケーションを Azure に移行することができます。

## <a name="how-it-works"></a>しくみ

クラスター内の VM は、[SCSI 永続的な予約](https://www.t10.org/members/w_spc3.htm) (SCSI PR) を使用するクラスター化アプリケーションによって選択された予約に基づいて、接続されたディスクに対して読み取りまたは書き込みを行うことができます。 SCSI PR は、オンプレミスの記憶域ネットワーク (SAN) 上で実行されているアプリケーションによって利用される、よく知られている業界標準です。 マネージド ディスクで SCSI PR を有効にすると、これらのアプリケーションを Azure にそのまま移行することができます。

共有ディスクが有効になっているマネージド ディスクでは、複数の VM からアクセスできる共有ブロック ストレージが提供されます。これは、論理ユニット番号 (LUN) として公開されます。 その後 LUN は、ターゲット (ディスク) からイニシエーター (VM) に提示されます。 これらの LUN は、VM からは直接接続ストレージ (DAS) やローカル ドライブのように見えます。

共有ディスクが有効になっているマネージド ディスクでは、SMB/NFS を使用してアクセスできる完全に管理されたファイル システムをネイティブで提供していません。 Windows Server フェールオーバー クラスター (WSFC) や Pacemaker のような、クラスター ノードの通信と書き込みロックを処理するクラスター マネージャーを使用する必要があります。

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