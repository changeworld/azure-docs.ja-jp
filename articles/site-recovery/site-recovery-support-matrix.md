---
title: "Azure Site Recovery のサポート マトリックス | Microsoft Docs"
description: "Azure Site Recovery でサポートされているオペレーティング システムとコンポーネントの概要について説明します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 1bbcc13c-ea21-4349-9ddf-0d7dfdcdcbfb
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/20/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 0c89357b2d6e2ab92d6a4a1b771650d77edb5e66
ms.openlocfilehash: 78716196cb677c95dae5082face6c8ea6499df4b


---
# <a name="azure-site-recovery-support-matrix"></a>Azure Site Recovery のサポート マトリックス

この記事では、Azure Site Recovery でサポートされているオペレーティング システムとコンポーネントの概要について説明します。 サポートされているコンポーネントと前提条件の一覧は、対応するデプロイに関する各記事のデプロイ シナリオごとに使用でき、このトピックではその要約を示しています。

## <a name="support-for-virtualization-server-operating-systems"></a>仮想化サーバーのオペレーティング システムのサポート

### <a name="host-servers-replicate-to-azure"></a>ホスト サーバー (Azure へのレプリケート)

**VMware VM/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | ---
vCenter 5.5 または 6.0 (5.5 の機能のみをサポート)  <br/><br/> vSphere 6.0、5.5、または 5.1 (最新の更新プログラムをインストール済み) | Windows Server 2012 R2 (最新の更新プログラムをインストール済み) | Windows Server 2012 R2 (最新の更新プログラムをインストール済み)

### <a name="host-servers-replicate-to-secondary-site"></a>ホスト サーバー (セカンダリ サイトへのレプリケート)

**VMware VM/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
vCenter 5.5 または 6.0 (5.5 の機能のみをサポート)  <br/><br/> vSphere 6.0、5.5、または 5.1 (最新の更新プログラムをインストール済み) | Windows Server 2012 以上 (最新の更新プログラムをインストール済み)


## <a name="support-for-replicated-machines"></a>レプリケートされたマシンのサポート

### <a name="machines-replicate-to-azure"></a>マシン (Azure へのレプリケート)

仮想マシンは [Azure 要件](site-recovery-best-practices.md#azure-virtual-machine-requirements)を満たしている必要があります。

**要件** | **VMware/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
レプリケート対象 | Windows または Linux VM 上のすべてのワークロード | すべてのワークロード | すべてのワークロード
ホスト OS | 64 ビット Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 以降<br/><br/> Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> CentOS 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Red Hat 互換カーネルまたは Unbreakable Enterprise Kernel リリース 3 (UEK3) を実行している Oracle Enterprise Linux 6.4、6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3<bbr/><br/>  必要なストレージ: ファイル システム (EXT3、ETX4、ReiserFS、XFS)、マルチパス ソフトウェア デバイス マッパー (multipath)、ボリューム マネージャー (LVM2)。 HP CCISS コントローラー ストレージを使用する物理サーバーはサポートされていません。 ReiserFS ファイルシステムは、SUSE Linux Enterprise Server 11 SP3 でのみサポートされています。 | [Azure がサポートする](https://technet.microsoft.com/library/cc794868.aspx)任意のゲスト OS | [Azure がサポートする](https://technet.microsoft.com/library/cc794868.aspx)任意のゲスト OS


### <a name="machines-replicate-to-secondary-site"></a>マシン (セカンダリ サイトへのレプリケート)

**要件** | **VMware/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
レプリケート対象 | Windows または Linux VM 上のすべてのワークロード | すべてのワークロード | すべてのワークロード
ホスト OS | 64 ビット Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 以降<br/><br/> Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> CentOS 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Red Hat 互換カーネルまたは Unbreakable Enterprise Kernel リリース 3 (UEK3) を実行している Oracle Enterprise Linux 6.4、6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3<bbr/><br/>  必要なストレージ: ファイル システム (EXT3、ETX4、ReiserFS、XFS)、マルチパス ソフトウェア デバイス マッパー (multipath)、ボリューム マネージャー (LVM2)。<br/><br/> HP CCISS コントローラー ストレージを使用する物理サーバーはサポートされていません。 ReiserFS ファイルシステムは、SUSE Linux Enterprise Server 11 SP3 でのみサポートされています。 | Hyper-V でサポートされているすべてのゲスト OS (https://technet.microsoft.com/library/mt126277.aspx)


## <a name="support-for-provider-and-agent"></a>プロバイダーとエージェントのサポート

**名前** | **説明** | **最新バージョン** | **詳細**
--- | --- | --- | --- | ---
**Azure Site Recovery プロバイダー** | オンプレミスのサーバーと Azure/セカンダリ サイトの間の通信を調整します  <br/><br/>  オンプレミスの VMM サーバー、または Hyper-V サーバー (VMM サーバーがない場合) にインストールされます | 5.1.1700 (ポータルから使用可能) | [最新の機能と修正](https://support.microsoft.com/kb/3155002)
**Azure Site Recovery の統合セットアップ (VMware から Azure)** | オンプレミスの VMware サーバーと Azure の間の通信を調整します  <br/><br/>  オンプレミスの VMware サーバーにインストールされます | 9.3.4246.1 (ポータルから使用可能) | [最新の機能と修正](https://support.microsoft.com/kb/3155002)
**モビリティ サービス** | オンプレミスの VMware サーバー/物理サーバーと Azure/セカンダリ サイトの間のレプリケーションを調整します<br/><br/> VMware VM またはレプリケートする物理サーバーにインストールされます  | 該当なし (ポータルから使用可能) | に関するページを参照してください。
**Microsoft Azure Recovery Services (MARS) エージェント** | Hyper-V VM と Azure の間のレプリケーションを調整します<br/><br/>  オンプレミスの Hyper-V サーバーにインストールされます (VMM サーバーの有無にかかわらず) | |

## <a name="support-for-networking"></a>ネットワークのサポート

### <a name="networking-replicate-to-azure"></a>ネットワーク (Azure へのレプリケート)

**ホスト ネットワーク** | **VMware/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
NIC チーミング | はい<br/><br/>物理マシン上でサポートされていません| はい | あり
VLAN | はい | あり | あり
IPv4 | あり | あり | はい
IPv6 | なし | いいえ | なし

**ゲスト VM ネットワーク** | **VMware/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
NIC チーミング | なし | いいえ | なし
IPv4 | あり | あり | はい
IPv6 | なし | いいえ | いいえ
静的 IP (Windows) | はい | あり | はい
静的 IP (Linux) | なし | いいえ | なし
マルチ NIC | はい | あり | はい

**Azure のネットワーク** | **VMware/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
ExpressRoute | あり | あり | あり
ILB | はい | あり | はい
ELB | あり |  |
Traffic Manager | はい | あり | はい
マルチ NIC | はい | あり | はい
予約済み IP | あり | あり | あり
IPv4 | はい | あり | はい
発信元アドレスを保持 | あり | あり | あり

### <a name="networking-replicate-to-secondary-site"></a>ネットワーク (セカンダリ サイトへのレプリケート)

**ホスト ネットワーク** | **VMware/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
NIC チーミング | あり | はい
VLAN | あり | はい
IPv4 | あり | はい
IPv6 | なし | なし

**ゲスト VM ネットワーク** | **VMware/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
NIC チーミング | なし | なし
IPv4 | はい | はい
IPv6 | なし | なし
静的 IP (Windows) | あり | あり
静的 IP (Linux) | はい | はい
マルチ NIC | はい | はい


## <a name="support-for-storage"></a>ストレージのサポート

### <a name="storage-replicate-to-azure"></a>ストレージ (Azure へのレプリケート)

**ストレージ (ホスト)** | **VMware/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
NFS | VMware = はい<br/><br/> 物理サーバー = いいえ | 該当なし | 該当なし
SMB 3.0 | 該当なし | あり | はい
SAN (ISCSI) | はい | あり | はい
マルチパス (MPIO) | VMware = はい<br/><br/> 物理サーバー = 該当なし | あり | あり

**ストレージ (ゲスト VM/物理サーバー)** | **VMware/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
VMDK | はい | 該当なし | 該当なし
VHD/VHDX | 該当なし | はい | はい
第 2 世代 VM | 該当なし | はい | あり
共有クラスター ディスク | VMware = はい<br/><br/> 物理サーバー = 該当なし | なし | なし
暗号化されたディスク | なし | いいえ | なし
EFI/UEFI| なし | はい | はい
NFS | なし | いいえ | いいえ
SMB 3.0 | なし | いいえ | なし
RDM | はい<br/><br/> 物理サーバー = 該当なし | 該当なし | 該当なし
1 TB より大きいディスク | いいえ | いいえ | いいえ
ストライピングされたディスクのボリューム > 1 TB<br/><br/> LVM | あり | あり | はい
記憶域 | なし | はい | はい
ディスクのホット アド/削除 | なし | いいえ | いいえ
ディスクの除外 | あり | なし | いいえ
マルチパス (MPIO) | 該当なし | あり | はい

**Azure Storage** | **VMware/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
LRS | はい | あり | はい
GRS | はい | あり | はい
クール ストレージ | なし | いいえ | なし
ホット ストレージ| なし | いいえ | いいえ
保存時の暗号化 | あり | あり | はい
Premium Storage | はい | なし | なし
インポート/エクスポート サービス | なし | いいえ | いいえ


### <a name="storage-replicate-to-secondary-site"></a>ストレージ (セカンダリ サイトへのレプリケート)

**ストレージ (ホスト)** | **VMware/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
NFS | あり | 該当なし
SMB 3.0 | 該当なし | はい
SAN (ISCSI) | あり | はい
マルチパス (MPIO) | あり | はい

**ストレージ (ゲスト VM/物理サーバー)** | **VMware/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
VMDK | はい | 該当なし
VHD/VHDX | 該当なし | はい (最大 64 個のディスク)
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

### <a name="vaults-replicate-to-azure"></a>資格情報コンテナー (Azure へのレプリケート)

**アクション** | **VMware/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
リソース グループ間の資格情報コンテナーの移動<br/><br/> サブスクリプション内およびサブスクリプション間 | なし | いいえ | いいえ
リソース グループ間でストレージ、ネットワーク、Azure VM を移動<br/><br/> サブスクリプション内およびサブスクリプション間 | なし | いいえ | なし

### <a name="vaults-replicate-to-secondary-site"></a>資格情報コンテナー (セカンダリ サイトへのレプリケート）

**アクション** | **VMware/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
リソース グループ間の資格情報コンテナーの移動<br/><br/> サブスクリプション内およびサブスクリプション間 | なし | なし
リソース グループ間でストレージ、ネットワーク、Azure VM を移動<br/><br/> サブスクリプション内およびサブスクリプション間 | いいえ | いいえ


## <a name="support-for-azure-compute-replicate-to-azure"></a>Azure コンピューティングのサポート (Azure にレプリケート)

**コンピューティング機能** | **VMware/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
共有ディスク ゲスト クラスター | なし | いいえ | なし
可用性セット | いいえ | いいえ | なし
ハブ | あり | あり | あり









## <a name="next-steps"></a>次のステップ
[デプロイメントの準備をする](site-recovery-best-practices.md)



<!--HONumber=Nov16_HO5-->


