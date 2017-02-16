---
title: "セカンダリ サイトへのレプリケートに関する Azure Site Recovery のサポート マトリックス | Microsoft Docs"
description: "Azure Site Recovery でサポートされているオペレーティング システムとコンポーネントの概要について説明します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/24/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: e858a70f2ce173eb6b99cbe7a130abb5fdc265e1
ms.openlocfilehash: 0a2c3bec5fc6fb44b4baddc393d6f9387bbb5b94


---
# <a name="azure-site-recovery-support-matrix-for-replicating-to-customer-owned-secondary-site"></a>顧客が所有するセカンダリ サイトへのレプリケートに関する Azure Site Recovery のサポート マトリックス

> [!div class="op_single_selector"]
> * [Azure へのレプリケート](site-recovery-support-matrix-to-azure.md)
> * [顧客が所有するセカンダリ サイトへのレプリケート](site-recovery-support-matrix-to-sec-site.md)

Azure Site Recovery の前提条件の一覧は[こちら](site-recovery-best-practices.md)に記載されており、以下の記事では、顧客が所有するセカンダリ サイトにレプリケートおよび復旧するときに Azure Site Recovery に対してサポートされる構成とコンポーネントをまとめます。

## <a name="support-for-deployment-options"></a>デプロイ オプションのサポート

**デプロイ** | **VMware/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
**Azure ポータル** | オンプレミスの VMware VM をセカンダリ VMware サイトにレプリケートします。<br/><br/> InMage Scout ユーザー ガイド (http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) をダウンロードしてください。 Azure Portal では使用できません | サポートされていません | VMM クラウドのオンプレミスの Hyper-V VM をセカンダリ VMM クラウドにレプリケートします。<br/><br/> 標準の Hyper-V レプリケーションのみ。SAN はサポートされていません
**クラシック ポータル** | メンテナンス モードのみ。 新しい資格情報コンテナーを作成することはできません。 | サポートされていません | メンテナンス モードのみ。
**PowerShell** | サポートされていません。 | サポートされていません | サポートされています



## <a name="support-for-datacenter-management-servers"></a>データセンター管理サーバーのサポート

### <a name="virtualization-management-entities"></a>仮想化管理エンティティ

**デプロイ** | **サポート**
--- | ---
**VMware VM/物理サーバー** | vSphere 6.0、5.5、または 5.1 (最新の更新プログラムをインストール済み)
**Hyper-V (VMM あり)** | SCVMM 2016、SCVMM 2012 R2

  >[!Note]
  > Windows Server 2016 ホストと 2012 R2 ホストが混在する SCVMM 2016 クラウドは、現在サポートされていません。

### <a name="host-servers"></a>ホスト サーバー

**デプロイ** | **サポート**
--- | ---
**VMware VM/物理サーバー** | vCenter 5.5 または 6.0 (5.5 の機能のみをサポート) 
**Hyper-V (VMM なし)** | セカンダリ サイトへのレプリケートの場合は、サポートされる構成ではありません
**Hyper-V (VMM あり)** | Windows Server 2016、最新の更新プログラムが適用された Windows Server 2012 R2<br/><br/> Windows Server 2016 ホストは、SCVMM 2016 によって管理されている必要があります

## <a name="support-for-replicated-machine-machine-os-versions"></a>レプリケートされるマシンの OS バージョンのサポート
次の表は、Azure Site Recovery 使用時の、さまざまなデプロイ シナリオにおけるオペレーティング システムのサポートをまとめたものです。 このサポートは、示された OS で実行される**任意のワークロードに適用可能**です。

**VMware/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
64 ビット Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 以降<br/><br/> Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> CentOS 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Red Hat 互換カーネルまたは Unbreakable Enterprise Kernel リリース 3 (UEK3) を実行している Oracle Enterprise Linux 6.4、6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3 | [Hyper-V がサポートする](https://technet.microsoft.com/library/mt126277.aspx)

>[!Note]
>**Linux バージョンに対するストレージのサポート** ファイル システム (EXT3、ETX4、ReiserFS、XFS) マルチパス ソフトウェア デバイス マッパー (multipath) ボリューム マネージャー: (LVM2) HP CCISS コントローラー ストレージを使用する物理サーバーはサポートされて**いません**。
>ReiserFS ファイルシステムは、SUSE Linux Enterprise Server 11 SP3 でのみサポートされています。

## <a name="support-for-network"></a>ネットワークのサポート
次の表は、Azure へのレプリケートに Azure Site Recovery を使うときの、さまざまなデプロイ シナリオにおけるネットワーク構成のサポートをまとめたものです。

### <a name="host-network-configuration"></a>ホストのネットワーク構成

**構成** | **VMware/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
NIC チーミング | あり | はい
VLAN | あり | あり
IPv4 | はい | はい
IPv6 | なし | なし

### <a name="guest-vm-network-configuration"></a>ゲスト VM のネットワーク構成

**構成** | **VMware/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
NIC チーミング | なし | なし
IPv4 | はい | はい
IPv6 | なし | なし
静的 IP (Windows) | あり | あり
静的 IP (Linux) | はい | はい
マルチ NIC | はい | はい


## <a name="support-for-storage"></a>ストレージのサポート
次の表は、Azure へのレプリケートに Azure Site Recovery を使うときの、さまざまなデプロイ シナリオにおけるストレージ構成のサポートをまとめたものです。

### <a name="host-storage-configuration"></a>ホストのストレージ構成

**ストレージ (ホスト)** | **VMware/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
NFS | あり | 該当なし
SMB 3.0 | 該当なし | はい
SAN (ISCSI) | あり | はい
マルチパス (MPIO) | あり | はい

### <a name="guest-physical-server-storage-configuration"></a>ゲスト/物理サーバーのストレージ構成

**構成** | **VMware/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
VMDK | はい | 該当なし
VHD/VHDX | 該当なし | はい (最大 16 個のディスク)
第 2 世代 VM | 該当なし | あり
共有クラスター ディスク | はい  | なし
暗号化されたディスク | いいえ | いいえ
UEFI| なし | 該当なし
NFS | なし | いいえ
SMB 3.0 | なし | なし
RDM | はい | 該当なし
1 TB より大きいディスク | なし | はい
ストライピングされたディスクのボリューム > 1 TB<br/><br/> LVM | はい | はい
記憶域 | なし | あり
ディスクのホット アド/削除 | なし | いいえ
ディスクの除外 | なし | なし
マルチパス (MPIO) | 該当なし | あり

## <a name="support-for-recovery-services-vault-actions"></a>Recovery Services コンテナーの操作のサポート

**アクション** | **VMware/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
リソース グループ間の資格情報コンテナーの移動<br/><br/> サブスクリプション内およびサブスクリプション間 | なし | いいえ
リソース グループ間でストレージ、ネットワーク、Azure VM を移動<br/><br/> サブスクリプション内およびサブスクリプション間 | なし | なし

## <a name="support-for-provider-and-agent"></a>プロバイダーとエージェントのサポート

**名前** | **説明** | **最新バージョン** | **詳細**
--- | --- | --- | --- | ---
**Azure Site Recovery プロバイダー** | オンプレミスのサーバーと Azure の間の通信を調整します <br/><br/> オンプレミス VMM サーバー、または Hyper-V サーバー (VMM サーバーがない場合) にインストールされます | 5.1.19 ([ポータルから使用可能](http://aka.ms/downloaddra)) | [最新の機能と修正](https://support.microsoft.com/kb/3155002)
**モビリティ サービス** | オンプレミスの VMware サーバー/物理サーバーとセカンダリ サイトの間のレプリケーションを調整します<br/><br/> レプリケートする VMware VM または物理サーバーにインストールされます  | 該当なし (ポータルから使用可能) | 該当なし


## <a name="next-steps"></a>次のステップ
[デプロイメントの準備をする](site-recovery-best-practices.md)



<!--HONumber=Jan17_HO4-->


