---
title: SAP HANA on Azure (L インスタンス) でのディザスター リカバリーの原則と準備 | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) でのディザスター リカバリーの原則と準備について理解します。
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: gwallace
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 7/01/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 79991bcdd5174d2655f3c917d0898b0b87b0ca50
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113217073"
---
# <a name="disaster-recovery-principles-and-preparation"></a>ディザスター リカバリーの原則と準備

この記事では、HANA Large Instances (BareMetal インフラストラクチャとも呼ばれる) の重要なディザスター リカバリー (DR) の原則について説明します。 ディザスター リカバリーに備えて実行する必要がある手順について説明します。 また、障害時の目標復旧時間 (RTO) と目標復旧時点 (RPO) を達成する方法も確認します。 

## <a name="dr-principles-for-hana-large-instances"></a>HANA Large Instances の DR の原則

HANA Large Instances では、さまざまな Azure リージョンの HANA Large Instance スタンプ間でのディザスター リカバリー機能が提供されます。 たとえば、Azure の米国西部リージョンに HANA Large Instances をデプロイするとします。 その後、米国東部リージョンの HANA Large Instances をディザスター リカバリー ユニットとして使用できます。 ディザスター リカバリーでは DR リージョンの別の HANA Large Instance の料金を支払う必要があるため、ディザスター リカバリーは自動的には構成されません。 ディザスター リカバリー セットアップは、スケールアップおよびスケールアウト セットアップに対応しています。 

ほとんどのお客様は DR リージョンのユニットを使って、インストール済みの HANA インスタンスを使用する非運用システムを実行していました。 HANA Large Instance は、運用環境で使用される SKU と同じ SKU である必要があります。 次の図は、Azure 運用リージョンとディザスター リカバリー リージョンのサーバー ユニット間のディスク構成を示したものです。

![ディスクの観点から見た DR セットアップ構成](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

この概要図からわかるように、2 つ目のディスク ボリューム セットを注文する必要があります。 DR サイトの HANA Large Instance サーバーに関連付けられているターゲット ディスク ボリュームは、実稼働ボリュームと同じサイズです。 

次のボリュームは、実稼働リージョンから DR サイトにレプリケートされます。

- /hana/data
- /hana/logbackups 
- /hana/shared (/usr/sap を含む)

/hana/log ボリュームはレプリケートされません。 これらのボリュームから復元するときに、SAP HANA トランザクション ログは必要ありません。

## <a name="hana-large-instance-storage-replication"></a>HANA Large Instance ストレージ レプリケーション 

HANA Large Instance インフラストラクチャの DR 機能の基盤はそのストレージ レプリケーションです。 ストレージ側で使用される機能は、ストレージ ボリュームに対する変更が発生したときに非同期で変更を絶えずレプリケートするものではありません。 そうではなく、これらのボリュームのスナップショットを定期的に作成する機能に依存するメカニズムです。 既にレプリケートされたスナップショットとまだレプリケートされていない新しいスナップショット間の差分が、DR サイトのターゲット ディスク ボリュームに転送されます。 これらのスナップショットはそのボリュームに格納されます。 ディザスター リカバリー フェールオーバーが発生した場合、それらのボリュームで復元する必要があります。  

ボリュームの完全なデータを初めて転送する場合、データのサイズがスナップショット間の差分よりも小さくなる前に転送を実行する必要があります。 そのため、DR サイトのボリュームには、運用サイトで実行されたすべてのボリューム スナップショットが含まれています。 これにより、運用システムをロールバックすることなく、その DR システムを使用して以前の状態に戻し、失われたデータを最終的に回復することができます。

1 つの HANA Large Instance に複数の独立した SAP HANA インスタンスによる MCOD 配備が存在する場合は、すべての SAP HANA インスタンスが DR 側のストレージにレプリケートされます。

運用サイトで高可用性実現のために HANA System Replication を使用し、DR サイトに対してストレージベースのレプリケーションを使用している場合、プライマリ サイトから DR インスタンスに対する両方のノードのボリュームがレプリケートされます。 プライマリ ノードとセカンダリ ノードの両方から DR に対するレプリケーションに対応するには、DR サイトに追加のストレージ (プライマリ ノードと同じサイズ) を購入します。 

>[!NOTE]
>HANA Large Instance のストレージ レプリケーション機能では、ストレージ スナップショットをミラーリングおよびレプリケートします。 「[バックアップと復元](hana-backup-restore.md)」で説明されているようにストレージ スナップショットを作成しないと、DR サイトにレプリケートできません。 ストレージ スナップショットの実行は、ディザスター リカバリー サイトへのストレージ レプリケーションの前提条件となります。

## <a name="preparation-of-the-disaster-recovery-scenario"></a>ディザスター リカバリー シナリオの準備
この DR シナリオでは、運用システムが運用 Azure リージョンの HANA Large Instances 上で実行されているものとします。 以下の手順では、その HANA システムの SID が「PRD」だと仮定します。 また、DR Azure リージョンの HANA Large Instances で、非運用システムも実行されているものとします。 その SID は「TST」です。 次の図は、この構成を示したものです。

![DR セットアップの開始](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

サーバー インスタンスについて追加のストレージ ボリューム セットをまだ指定していないものとします。 SAP HANA on Azure サービス管理によって、追加のボリューム セットがアタッチされます。 これらは、TST HANA インスタンスを実行している HANA Large Instance の実稼働レプリカのターゲットです。 実稼働 HANA インスタンスの SID を指定する必要があります。 SAP HANA on Azure サービス管理でボリュームのアタッチを確定した後は、それらのボリュームを HANA Large Instance にマウントする必要があります。

![DR セットアップの次の手順](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

次の手順は、TST HANA インスタンスを実行している DR Azure リージョンの HANA Large Instance に、2 つ目の SAP HANA インスタンスをインストールすることです。 新しくインストールする SAP HANA インスタンスの SID は同じにする必要があります。 作成したユーザーは、実稼働インスタンスと同じ UID とグループ ID を持っている必要があります。 詳細については、「[バックアップと復元](hana-backup-restore.md)」をご覧ください。 インストールが成功した場合、以下の手順を実行する必要があります。

- 「[バックアップと復元](hana-backup-restore.md)」に記述されている、ストレージ スナップショットの準備の手順 2 を実行します。
- まだ作成していない場合は、HANA Large Instance の DR ユニットに対する公開キーを作成します。 「[バックアップと復元](hana-backup-restore.md)」に記述されている、ストレージ スナップショットの準備の手順 3 を参照します。
- 新しい HANA インスタンスで *HANABackupCustomerDetails.txt* を更新し、ストレージへの接続が正常に動作することをテストします。  
- DR Azure リージョンの HANA Large Instance に新しくインストールした SAP HANA インスタンスを停止します。
- PRD ボリュームのマウントを解除し、SAP HANA on Azure サービス管理に連絡します。 ストレージ レプリケーション ターゲットとして機能している間はボリュームにアクセスできないため、ボリュームをユニットにマウントしたままにすることはできません。  

![運用 Azure リージョンの PRD ボリュームと、DR Azure リージョンの PRD ボリューム間のレプリケーション関係示す図。](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

オペレーション チームは、運用リージョンの PRD ボリュームと、DR リージョンの PRD ボリューム間にレプリケーション関係を確立します。

>[!IMPORTANT]
>レプリケートされた SAP HANA データベースをディザスター リカバリー サイトで一貫した状態に復元する必要がないため、/hana/log ボリュームはレプリケートされません。

次に、障害発生時に RTO と RPO を達成するために、ストレージ スナップショット バックアップ スケジュールを設定します。 RPO を最小限に抑えるために、HANA Large Instance サービスでは次のレプリケーション間隔を設定します。
- 結合スナップショット (スナップショットの種類は **hana**) の対象となるボリュームについては、ディザスター リカバリー サイトの同等のストレージ ボリューム ターゲットに 15 分ごとにレプリケートされるように設定します。
- トランザクション ログ バックアップ ボリューム (スナップショットの種類は **logs**) については、ディザスター リカバリー サイトの同等のストレージ ボリューム ターゲットに 3 分ごとにレプリケートされるように設定します。

RPO を最小化するには:
- 種類が **hana** のストレージ スナップショットを 30 分 ～ 1 時間ごとに実行する。 詳細については、「[Azure アプリケーション整合性スナップショット ツールを使用してバックアップする](../../../azure-netapp-files/azacsnap-cmd-ref-backup.md)」を参照してください。
- SAP HANA トランザクション ログ バックアップを 5 分ごとに実行する。
- 種類が **logs** のストレージ スナップショットを 5 ～ 15 分ごとに実行する。 この間隔で約 15 ～ 25 分の RPO を実現できます。

このセットアップでは、トランザクション ログ バックアップ、ストレージ スナップショット、HANA トランザクション ログ バックアップ ボリュームと /hana/data、/hana/shared (/usr/sap を含む) のレプリケーションのシーケンスは下図のデータのようになります。

 ![時間軸でのトランザクション ログ バックアップ スナップショットとスナップ ミラーの関係](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

ディザスター リカバリーの場合に RPO をさらに短縮するには、SAP HANA on Azure (L インスタンス) の HANA トランザクション ログ バックアップをもう一方の Azure リージョンにコピーします。 この RPO の短縮を実現するには、次の手順を実行します。

1. HANA トランザクション ログをできるだけ頻繁に /hana/logbackups にバックアップします。
1. rsync を使用して、NFS 共有でホストされている Azure 仮想マシンにトランザクション ログのバックアップをコピーします。 仮想マシン (VM) は、Azure 実稼働リージョンと DR リージョンの Azure 仮想ネットワーク内にあります。 実稼働 HANA Large Instances を Azure に接続する回路に、両方の Azure 仮想ネットワークを接続します。 詳細については、「[HANA Large Instances でのディザスター リカバリーのネットワークに関する考慮事項](hana-overview-high-availability-disaster-recovery.md#network-considerations-for-disaster-recovery-with-hana-large-instances)」を参照してください。 
1. そのリージョン内の VM に接続されている NFS エクスポートされたストレージにトランザクション ログ バックアップを保持します。
1. 障害時のフェールオーバーの場合、/hana/logbackups ボリューム上のトランザクション ログ バックアップを、DR サイトの NFS 共有上のつい最近作成されたトランザクション ログ バックアップで補完します。 
1. DR リージョンに保存されている可能性のある最新のバックアップへのトランザクション ログ バックアップの復元を開始します。

レプリケーション関係がセットアップされていることを HANA Large Instance のオペレーション チームが確認し、お客様が実行ストレージ スナップショット バックアップを開始したら、データのレプリケートが開始されます。

![レプリケーションを確立する前の DR セットアップ手順](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

レプリケーションの処理中は、DR Azure リージョンの PRD ボリューム上のスナップショットは復元されません。 スナップショットは格納されるのみです。 このような状態でボリュームがマウントされている場合、PRD SAP HANA インスタンスが DR Azure リージョンのサーバーにインストールされた後に、それらのボリュームのマウントは解除された状態と表示されます。 また、まだ復元されていないストレージ バックアップであると表示されます。

フェールオーバーが発生した場合、最新のストレージ スナップショットではなく、古いストレージ スナップショットへの復元を選択することもできます。

## <a name="next-steps"></a>次のステップ

ディザスター リカバリーのフェールオーバー手順について。

> [!div class="nextstepaction"]
> [ディザスター リカバリーのフェールオーバー手順](hana-failover-procedure.md)
