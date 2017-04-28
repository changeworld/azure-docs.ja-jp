---
title: "Azure へのレプリケートに関する Azure Site Recovery のサポート マトリックス | Microsoft Docs"
description: "Azure Site Recovery でサポートされているオペレーティング システムとコンポーネントの概要について説明します"
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/25/2017
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: 711fb0715b7f12e12a742136f75af8069cbc83d8
ms.lasthandoff: 04/18/2017


---
# <a name="azure-site-recovery-support-matrix-for-replicating-to-azure"></a>Azure へのレプリケートに関する Azure Site Recovery のサポート マトリックス

> [!div class="op_single_selector"]
> * [Azure へのレプリケート](site-recovery-support-matrix-to-azure.md)
> * [顧客が所有するセカンダリ サイトへのレプリケート](site-recovery-support-matrix-to-sec-site.md)


この記事では、Azure へのレプリケートおよび復旧時にサポートされる Azure Site Recovery の構成とコンポーネントをまとめます。 Azure Site Recovery の要件の詳細については、[前提条件](site-recovery-prereq.md)に関する記事をご覧ください。


## <a name="support-for-deployment-options"></a>デプロイ オプションのサポート

**デプロイ** | **VMware/物理サーバー** | **Hyper-V (Virtual Machine Manager あり/なし)** |
--- | --- | ---
**Azure ポータル** | Azure Resource Manager または従来のストレージとネットワークを使用して、オンプレミスの VMware VM を Azure Storage にレプリケートします。<br/><br/> Resource Manager ベースの VM または従来の VM にフェールオーバーします。 | Resource Manager または従来のストレージとネットワークを使用して、オンプレミスの Hyper-V VM を Azure Storage にレプリケートします。<br/><br/> Resource Manager ベースの VM または従来の VM にフェールオーバーします。
**クラシック ポータル** | メンテナンス モードのみ。 新しい資格情報コンテナーを作成することはできません。 | メンテナンス モードのみ。
**PowerShell** | 現在、サポートされていません。 | サポートされています


## <a name="support-for-datacenter-management-servers"></a>データセンター管理サーバーのサポート

### <a name="virtualization-management-entities"></a>仮想化管理エンティティ

**デプロイ** | **サポート**
--- | ---
**VMware VM/物理サーバー** | vSphere 6.0、5.5、または 5.1 (最新の更新プログラムをインストール済み)
**Hyper-V (Virtual Machine Manager あり)** | System Center Virtual Machine Manager 2016 と System Center Virtual Machine Manager 2012 R2

  >[!Note]
  > Windows Server 2016 ホストと 2012 R2 ホストが混在する System Center Virtual Machine Manager 2016 クラウドは、現在サポートされていません。

### <a name="host-servers"></a>ホスト サーバー

**デプロイ** | **サポート**
--- | ---
**VMware VM/物理サーバー** | vCenter 5.5 または 6.0 (5.5 の機能のみをサポート) 
**Hyper-V (Virtual Machine Manager あり/なし)** | Windows Server 2016、最新の更新プログラムが適用された Windows Server 2012 R2。<br></br>SCVMM が使用されている場合、Windows Server 2016 ホストは、SCVMM 2016 によって管理されている必要があります。


  >[!Note]
  >Windows Server 2016 を実行しているホストと 2012 R2 を実行しているホストが混在する Hyper-V サイトは、現在サポートされていません。 Windows Server 2016 ホスト上の VM は、現時点では別の場所に復元することはできません。

## <a name="support-for-replicated-machine-os-versions"></a>レプリケートされるマシンの OS バージョンのサポート

保護対象の仮想マシンは、Azure にレプリケートされるときは、[Azure の要件](#failed-over-azure-vm-requirements)を満たしている必要があります。
次の表は、Azure Site Recovery 使用時の、さまざまなデプロイ シナリオにおけるレプリケートされるオペレーティング システムのサポートをまとめたものです。 このサポートは、示された OS で実行される任意のワークロードに適用可能です。

 **VMware/物理サーバー** | **Hyper-V (Virtual Machine Manager あり/なし)** |
--- | --- |
64 ビット Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 以降<br/><br/> Red Hat Enterprise Linux 6.7、6.8、7.1、7.2 <br/><br/> CentOS 6.5、6.6、6.7、6.8、7.0、7.1、7.2 <br/><br/> Red Hat 互換カーネルまたは Unbreakable Enterprise Kernel リリース 3 (UEK3) を実行している Oracle Enterprise Linux 6.4、6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3 <br/><br/> SUSE Linux Enterprise Server 11 SP4 <br/>(レプリケートするマシンの SLES 11 SP3 から SLES 11 SP4 へのアップグレードはサポートされていません。 レプリケートされるマシンが SLES 11SP3 から SLES 11 SP4 にアップグレードされた場合は、アップグレード後にレプリケーションを無効にし、再度マシンを保護する必要があります。) | [Azure がサポートする](https://technet.microsoft.com/library/cc794868.aspx)任意のゲスト OS


>[!IMPORTANT]
>(Azure にレプリケートする VMware/物理サーバーに適用されます)
>
> Red Hat Enterprise Linux Server 7 以降および CentOS 7 以降のサーバーでは、Azure Site Recovery モビリティ サービスのバージョン 9.8 以降、カーネル バージョン 3.10.0-514 がサポートされます。<br/><br/>
> モビリティ サービスのバージョンが 9.8 より前の 3.10.0-514 カーネルのお客様は、レプリケーションを無効にし、モビリティ サービスのバージョンを 9.8 に更新してから、再度レプリケーションを有効にする必要があります。  

## <a name="supported-file-systems-and-guest-storage-configurations-on-linux-vmwarephysical-servers"></a>Linux (VMware/物理サーバー) でサポートされるファイル システムおよびゲスト ストレージ構成

VMware または物理サーバーで実行される Linux サーバーでは、次のファイル システムおよびストレージ構成ソフトウェアがサポートされます。
* ファイル システム: ext3、ext4、ReiserFS (Suse Linux Enterprise Server のみ)、XFS (v4 までに限られます)
* ボリューム マネージャー: LVM2
* マルチパス ソフトウェア: デバイス マッパー

HP CCISS ストレージ コントローラーを使用する物理サーバーはサポートされていません。

>[!Note]
> Linux サーバーでは、/ (ルート)、/boot、/usr、/usr/local、/var、/etc の各ディレクトリ (個別のパーティション/ファイルシステムとしてセットアップされた場合) はすべて、ソース サーバーの同じディスク (OS ディスク) 上に存在する必要があります。<br/><br/>
> メタデータ チェックサムなど XFS v5 の機能は現在、XFS ファイルシステムの ASR でサポートされていません。 XFS ファイルシステムで v5 の機能を使用していないことを確認してください。 xfs_info ユーティリティを使用して、パーティションの XFS スーパーブロックを確認します。 ftype が 1 に設定されている場合は、XFSv5 の機能が使用されています。 
>


## <a name="support-for-network-configuration"></a>ネットワーク構成のサポート
次の表は、Azure へのレプリケートに Azure Site Recovery を使用するさまざまなデプロイ シナリオにおけるネットワーク構成のサポートをまとめたものです。

### <a name="host-network-configuration"></a>ホストのネットワーク構成

**構成** | **VMware/物理サーバー** | **Hyper-V (Virtual Machine Manager あり/なし)**
--- | --- | ---
NIC チーミング | はい<br/><br/>物理マシン上でサポートされていません| はい
VLAN | あり | あり
IPv4 | はい | はい
IPv6 | なし | なし

### <a name="guest-vm-network-configuration"></a>ゲスト VM のネットワーク構成

**構成** | **VMware/物理サーバー** | **Hyper-V (Virtual Machine Manager あり/なし)**
--- | --- | ---
NIC チーミング | なし | なし
IPv4 | はい | はい
IPv6 | なし | なし
静的 IP (Windows) | あり | あり
静的 IP (Linux) | なし | なし
マルチ NIC | はい | はい

### <a name="failed-over-azure-vm-network-configuration"></a>フェールオーバーされる Azure VM のネットワーク構成

**Azure のネットワーク** | **VMware/物理サーバー** | **Hyper-V (Virtual Machine Manager あり/なし)**
--- | --- | ---
ExpressRoute | あり | あり
ILB | はい | はい
ELB | はい | はい
Traffic Manager | はい | はい
マルチ NIC | はい | はい
予約済み IP | あり | あり
IPv4 | はい | はい
発信元アドレスを保持 | あり | はい


## <a name="support-for-storage"></a>ストレージのサポート
次の表は、Azure へのレプリケートに Azure Site Recovery を使用するさまざまなデプロイ シナリオにおけるストレージ構成のサポートをまとめたものです。

### <a name="host-storage-configuration"></a>ホストのストレージ構成

**構成** | **VMware/物理サーバー** | **Hyper-V (Virtual Machine Manager あり/なし)**
--- | --- | --- | ---
NFS | VMware = はい<br/><br/> 物理サーバー = いいえ | 該当なし
SMB 3.0 | 該当なし | はい
SAN (ISCSI) | あり | はい
マルチパス (MPIO)<br></br>テスト環境: Microsoft DSM、EMC PowerPath 5.7 SP4、EMC PowerPath DSM for CLARiiON | はい | はい

### <a name="guest-or-physical-server-storage-configuration"></a>ゲストまたは物理サーバーのストレージ構成

**構成** | **VMware/物理サーバー** | **Hyper-V (Virtual Machine Manager あり/なし)**
--- | --- | ---
VMDK | はい | 該当なし
VHD/VHDX | 該当なし | はい
第 2 世代 VM | 該当なし | はい
EFI/UEFI| なし | はい
共有クラスター ディスク | VMware = はい<br/><br/> 物理サーバー = 該当なし | いいえ
暗号化されたディスク | いいえ | なし
NFS | なし | 該当なし
SMB 3.0 | なし | なし
RDM | はい<br/><br/> 物理サーバー = 該当なし | 該当なし
1 TB より大きいディスク | いいえ | いいえ
ストライピングされたディスクのボリューム > 1 TB<br/><br/> LVM 論理ボリュームの管理 | はい | はい
記憶域 | なし | あり
ディスクのホット アド/削除 | なし | いいえ
ディスクの除外 | はい | はい
マルチパス (MPIO) | 該当なし | はい

**Azure Storage** | **VMware/物理サーバー** | **Hyper-V (Virtual Machine Manager あり/なし)**
--- | --- | ---
LRS | はい | はい
GRS | はい | はい
RA-GRS | はい | はい
クール ストレージ | なし | なし
ホット ストレージ| なし | なし
保存時の暗号化 (SSE)| はい | はい
Premium Storage | はい | はい
インポート/エクスポート サービス | なし | いいえ


## <a name="support-for-azure-compute-configuration"></a>Azure コンピューティング構成のサポート

**コンピューティング機能** | **VMware/物理サーバー** | **Hyper-V (Virtual Machine Manager あり/なし)**
--- | --- | --- | ---
可用性セット | はい | はい
ハブ | あり | はい  

## <a name="failed-over-azure-vm-requirements"></a>フェールオーバーされる Azure VM の要件

Site Recovery をデプロイすると、Azure でサポートされた任意のオペレーティング システムを実行している仮想マシンまたは物理サーバーをレプリケートできます。 これには、Windows と Linux のほとんどのバージョンが含まれます。 レプリケートするオンプレミスの VM は、Azure へのレプリケート時に、次の Azure の要件を満たしている必要があります。

**エンティティ** | **要件** | **詳細**
--- | --- | ---
**ゲスト オペレーティング システム** | Hyper-V から Azure へのレプリケーション: Site Recovery では、 [Azure でサポートされている](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)すべてのオペレーティング システムがサポートされます。 <br/><br/> VMware と物理サーバーのレプリケーション: Windows と Linux の [前提条件](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | サポートされていない場合、前提条件の確認は失敗します。
**ゲスト オペレーティング システムのアーキテクチャ** | 64 ビット | サポートされていない場合、前提条件の確認は失敗します。
**オペレーティング システムのディスク サイズ** | 最大 1023 GB | サポートされていない場合、前提条件の確認は失敗します。
**オペレーティング システムのディスク数** | 1 | サポートされていない場合、前提条件の確認は失敗します。
**データ ディスク数** | **VMware VM を Azure に**レプリケートする場合は 64 個以下、**HYPER-V VM を Azure に**レプリケートする場合は 16 個以下 | サポートされていない場合、前提条件の確認は失敗します。
**データ ディスク VHD のサイズ** | 最大 1023 GB | サポートされていない場合、前提条件の確認は失敗します。
**ネットワーク アダプター** | 複数のアダプターがサポートされます。 |
**共有 VHD** | サポートされていません | サポートされていない場合、前提条件の確認は失敗します。
**FC ディスク** | サポートされていません | サポートされていない場合、前提条件の確認は失敗します。
**ハード ディスク フォーマット** | VHD  <br/><br/> VHDX | VHDX は現在、Azure でサポートされていませんが、Azure にフェールオーバーするとき、Site Recovery が VHDX を VHD に自動的に変換します。 オンプレミスにフェールバックした場合、仮想マシンは引き続き VHDX 形式を使用します。
**Bitlocker** | サポートされていません | 仮想マシンを保護する前に BitLocker は無効にしてください。
**VM 名** | 1 ～ 63 文字で指定します。 名前に使用できるのは、英文字、数字、およびハイフンのみです。 VM 名の最初と最後は、文字か数字とする必要があります。 | Site Recovery で仮想マシンのプロパティの値を更新します。
**VM の種類** | 第 1 世代<br/><br/> 第 2 世代 - Windows | OS ディスクの種類が基本 (VHDX としてフォーマットされている 1 つまたは 2 つのデータ ボリュームが含まれる) でディスク容量が 300 GB 未満の第 2 世代の VM はサポートされています。<br></br>第 2 世代の Linux VM はサポートされていません。 [詳細情報](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="support-for-recovery-services-vault-actions"></a>Recovery Services コンテナーの操作のサポート

**アクション** | **VMware/物理サーバー** | **Hyper-V (Virtual Machine Manager なし)** | **Hyper-V (Virtual Machine Manager あり)**
--- | --- | --- | ---
リソース グループ間の資格情報コンテナーの移動<br/><br/> サブスクリプション内およびサブスクリプション間 | なし | いいえ | いいえ
リソース グループ間でストレージ、ネットワーク、Azure VM を移動<br/><br/> サブスクリプション内およびサブスクリプション間 | なし | いいえ | なし


## <a name="support-for-provider-and-agent"></a>プロバイダーとエージェントのサポート

**名前** | **説明** | **最新バージョン** | **詳細**
--- | --- | --- | --- | ---
**Azure Site Recovery プロバイダー** | オンプレミスのサーバーと Azure の間の通信を調整します <br/><br/> オンプレミスの Virtual Machine Manager サーバー、または Virtual Machine Manager サーバーが存在しない場合は HYPER-V サーバーにインストールされます | 5.1.19 ([ポータルから使用可能](http://aka.ms/downloaddra)) | [最新の機能と修正](https://support.microsoft.com/kb/3155002)
**Azure Site Recovery の統合セットアップ (VMware から Azure)** | オンプレミスの VMware サーバーと Azure の間の通信を調整します  <br/><br/> オンプレミスの VMware サーバーにインストールされます | 9.3.4246.1 (ポータルから使用可能) | [最新の機能と修正](https://support.microsoft.com/kb/3155002)
**モビリティ サービス** | オンプレミスの VMware サーバー/物理サーバーと Azure/セカンダリ サイトの間のレプリケーションを調整します<br/><br/> レプリケートする VMware VM または物理サーバーにインストールされます  | 該当なし (ポータルから使用可能) | 該当なし
**Microsoft Azure Recovery Services (MARS) エージェント** | Hyper-V VM と Azure の間のレプリケーションを調整します<br/><br/> オンプレミスの Hyper-V サーバーにインストールされます (Virtual Machine Manager サーバーの有無にかかわらず) | 最新のエージェント ([ポータルから使用可能](http://aka.ms/latestmarsagent)) |






## <a name="next-steps"></a>次のステップ
[前提条件を確認する](site-recovery-prereq.md)

