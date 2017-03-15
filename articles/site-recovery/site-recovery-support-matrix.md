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
ms.date: 12/04/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 3b9d269a780e9a4c61263208f26f440b1121c682
ms.openlocfilehash: f437598b612a145c5dd8b46a1ba340d298a76981
ms.lasthandoff: 03/01/2017


---
# <a name="azure-site-recovery-support-matrix"></a>Azure Site Recovery のサポート マトリックス

この記事では、Azure Site Recovery でサポートされているオペレーティング システムとコンポーネントの概要について説明します。 サポートされているコンポーネントと前提条件の一覧は、対応するデプロイに関する各記事のデプロイ シナリオごとに使用でき、このトピックではその要約を示しています。

## <a name="support-for-azure-replication-scenarios"></a>Azure のレプリケーション シナリオのサポート

**デプロイ** | **VMware/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
**Azure ポータル** | Resource Manager または従来のストレージとネットワークを使用して、オンプレミスの VMware VM を Azure Storage にレプリケートします。<br/><br/> リソース マネージャー ベースの VM または従来の VM にフェールオーバーします。 | Resource Manager または従来のストレージとネットワークを使用して、(VMM クラウドにない) オンプレミスの Hyper-V VM を Azure Storage にレプリケートします。<br/><br/> リソース マネージャー ベースの VM または従来の VM にフェールオーバーします。 | Resource Manager または従来のストレージとネットワークを使用して、(VMM クラウドの) オンプレミスの Hyper-V VM を Azure Storage にレプリケートします。<br/><br/> リソース マネージャー ベースの VM または従来の VM にフェールオーバーします。
**クラシック ポータル** | メンテナンス モードのみ。 新しい資格情報コンテナーを作成することはできません。 | メンテナンス モードのみ。 | メンテナンス モードのみ。
**PowerShell** | 現在、サポートされていません。 | サポートされています | サポートされています


## <a name="support-for-secondary-site-replication-scenarios"></a>セカンダリ サイトのレプリケーション シナリオのサポート

**デプロイ** | **VMware/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
**Azure ポータル** | オンプレミスの VMware VM をセカンダリ VMware サイトにレプリケートします。<br/><br/> InMage Scout ユーザー ガイド (http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) をダウンロードしてください。 Azure Portal では使用できません | サポートされていません | VMM クラウドのオンプレミスの Hyper-V VM をセカンダリ VMM クラウドにレプリケートします。<br/><br/> 標準の Hyper-V レプリケーションのみ。SAN はサポートされていません
**クラシック ポータル** | メンテナンス モードのみ。 新しい資格情報コンテナーを作成することはできません。 | メンテナンス モードのみ。 | メンテナンス モードのみ。
**PowerShell** | サポートされていません。 | 該当なし | サポートされています



## <a name="support-for-virtualization-server-operating-systems"></a>仮想化サーバーのオペレーティング システムのサポート

### <a name="host-servers-replicate-to-azure"></a>ホスト サーバー (Azure へのレプリケート)

**VMware VM/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | ---
vCenter 5.5 または 6.0 (5.5 の機能のみをサポート)  <br/><br/> vSphere 6.0、5.5、または 5.1 (最新の更新プログラムをインストール済み) | Windows Server 2016、最新の更新プログラムが適用された Windows Server 2012 R2<br/><br/> Windows Server 2016 を実行しているホストと 2012 R2 を実行しているホストが混在する Hyper-V サイトは、現在サポートされていません。 | Windows Server 2016、最新の更新プログラムが適用された Windows Server 2012 R2<br/><br/> Windows Server 2016 ホストは、System Center 2016 を実行する VMM によって管理されている必要があります。<br/><br/> Windows Server 2016 ホストと 2012 R2 ホストが混在する VMM 2016 クラウドは、現在サポートされていません。

### <a name="host-servers-replicate-to-secondary-site"></a>ホスト サーバー (セカンダリ サイトへのレプリケート)

**VMware VM/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
vCenter 5.5 または 6.0 (5.5 の機能のみをサポート)  <br/><br/> vSphere 6.0、5.5、または 5.1 (最新の更新プログラムをインストール済み) | Windows Server 2016、Windows Server 2012 R2、または最新の更新プログラムが適用された Windows Server 2012。<br/><br/> Windows Server 2016 ホストは、System Center 2016 を実行する VMM によって管理されている必要があります。<br/><br/> Windows Server 2016 ホストとそれ以前のホストが混在する VMM 2016 クラウドは、現在サポートされていません。


## <a name="support-for-replicated-machines"></a>レプリケートされたマシンのサポート

### <a name="machines-replicate-to-azure"></a>マシン (Azure へのレプリケート)

仮想マシンは [Azure 要件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)を満たしている必要があります。

**要件** | **VMware/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
レプリケート対象 | Windows または Linux VM 上のすべてのワークロード | すべてのワークロード | すべてのワークロード
ホスト OS | 64 ビット Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 以降<br/><br/> Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> CentOS 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Red Hat 互換カーネルまたは Unbreakable Enterprise Kernel リリース 3 (UEK3) を実行している Oracle Enterprise Linux 6.4、6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3<bbr/><br/> 必要なストレージ: ファイル システム (EXT3、ETX4、ReiserFS、XFS)、マルチパス ソフトウェア デバイス マッパー (multipath)、ボリューム マネージャー (LVM2)。 HP CCISS コントローラー ストレージを使用する物理サーバーはサポートされていません。 ReiserFS ファイルシステムは、SUSE Linux Enterprise Server 11 SP3 でのみサポートされています。 | [Azure がサポートする](https://technet.microsoft.com/library/cc794868.aspx)任意のゲスト OS | [Azure がサポートする](https://technet.microsoft.com/library/cc794868.aspx)任意のゲスト OS


### <a name="machines-replicate-to-secondary-site"></a>マシン (セカンダリ サイトへのレプリケート)

**要件** | **VMware/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
レプリケート対象 | Windows または Linux VM 上のすべてのワークロード | すべてのワークロード | すべてのワークロード
ホスト OS | 64 ビット Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 以降<br/><br/> Red Hat Enterprise Linux 6.7、7.1、7.2 <br/><br/> CentOS 6.5、6.6、6.7、7.0、7.1、7.2 <br/><br/> Red Hat 互換カーネルまたは Unbreakable Enterprise Kernel リリース 3 (UEK3) を実行している Oracle Enterprise Linux 6.4、6.5 <br/><br/> SUSE Linux Enterprise Server 11 SP3<bbr/><br/> 必要なストレージ: ファイル システム (EXT3、ETX4、ReiserFS、XFS)、マルチパス ソフトウェア デバイス マッパー (multipath)、ボリューム マネージャー (LVM2)。<br/><br/> HP CCISS コントローラー ストレージを使用する物理サーバーはサポートされていません。 ReiserFS ファイルシステムは、SUSE Linux Enterprise Server 11 SP3 でのみサポートされています。 | Hyper-V でサポートされているすべてのゲスト OS (https://technet.microsoft.com/library/mt126277.aspx)


## <a name="support-for-provider-and-agent"></a>プロバイダーとエージェントのサポート

**名前** | **説明** | **最新バージョン** | **詳細**
--- | --- | --- | --- | ---
**Azure Site Recovery プロバイダー** | オンプレミスのサーバーと Azure/セカンダリ サイトの間の通信を調整します  <br/><br/> オンプレミス VMM サーバー、または Hyper-V サーバー (VMM サーバーがない場合) にインストールされます | 5.1.1700 (ポータルから使用可能) | [最新の機能と修正](https://support.microsoft.com/kb/3155002)
**Azure Site Recovery の統合セットアップ (VMware から Azure)** | オンプレミスの VMware サーバーと Azure の間の通信を調整します  <br/><br/> オンプレミスの VMware サーバーにインストールされます | 9.3.4246.1 (ポータルから使用可能) | [最新の機能と修正](https://support.microsoft.com/kb/3155002)
**モビリティ サービス** | オンプレミスの VMware サーバー/物理サーバーと Azure/セカンダリ サイトの間のレプリケーションを調整します<br/><br/> レプリケートする VMware VM または物理サーバーにインストールされます  | 該当なし (ポータルから使用可能) | に関するページを参照してください。
**Microsoft Azure Recovery Services (MARS) エージェント** | Hyper-V VM と Azure の間のレプリケーションを調整します<br/><br/> オンプレミスの Hyper-V サーバーにインストールされます (VMM サーバーの有無にかかわらず) | |

## <a name="support-for-networking"></a>ネットワークのサポート

### <a name="networking-replicate-to-azure"></a>ネットワーク (Azure へのレプリケート)

**ホスト ネットワーク** | **VMware/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
NIC チーミング | はい<br/><br/>物理マシン上でサポートされていません| はい | はい
VLAN | あり | あり | あり
IPv4 | はい | あり | はい
IPv6 | なし | いいえ | なし

**ゲスト VM ネットワーク** | **VMware/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
NIC チーミング | なし | いいえ | なし
IPv4 | はい | あり | はい
IPv6 | なし | いいえ | なし
静的 IP (Windows) | あり | あり | あり
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
VLAN | あり | あり
IPv4 | はい | はい
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
SAN (ISCSI) | あり | あり | はい
マルチパス (MPIO) | VMware = はい<br/><br/> 物理サーバー = 該当なし | あり | はい

**ストレージ (ゲスト VM/物理サーバー)** | **VMware/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
VMDK | はい | 該当なし | 該当なし
VHD/VHDX | 該当なし | あり | はい
第 2 世代 VM | 該当なし | あり | あり
共有クラスター ディスク | VMware = はい<br/><br/> 物理サーバー = 該当なし | なし | なし
暗号化されたディスク | いいえ | いいえ | なし
EFI/UEFI| なし | はい | はい
NFS | なし | いいえ | いいえ
SMB 3.0 | なし | いいえ | なし
RDM | はい<br/><br/> 物理サーバー = 該当なし | 該当なし | 該当なし
1 TB より大きいディスク | いいえ | いいえ | いいえ
ストライピングされたディスクのボリューム > 1 TB<br/><br/> LVM | はい | あり | はい
記憶域 | なし | はい | はい
ディスクのホット アド/削除 | なし | いいえ | いいえ
ディスクの除外 | はい | あり | はい
マルチパス (MPIO) | 該当なし | あり | はい

**Azure Storage** | **VMware/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
LRS | はい | あり | はい
GRS (Standard Storage のみ) | はい | あり | はい
クール ストレージ | なし | いいえ | なし
ホット ストレージ| なし | いいえ | いいえ
保存時の暗号化 | あり | あり | はい
Premium Storage | はい | あり | はい
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

### <a name="vaults-replicate-to-azure"></a>資格情報コンテナー (Azure へのレプリケート)

**アクション** | **VMware/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
リソース グループ間の資格情報コンテナーの移動<br/><br/> サブスクリプション内およびサブスクリプション間 | なし | いいえ | いいえ
リソース グループ間でストレージ、ネットワーク、Azure VM を移動<br/><br/> サブスクリプション内およびサブスクリプション間 | なし | いいえ | なし

### <a name="vaults-replicate-to-secondary-site"></a>資格情報コンテナー (セカンダリ サイトへのレプリケート）

**アクション** | **VMware/物理サーバー** | **Hyper-V (VMM あり)**
--- | --- | ---
リソース グループ間の資格情報コンテナーの移動<br/><br/> サブスクリプション内およびサブスクリプション間 | なし | いいえ
リソース グループ間でストレージ、ネットワーク、Azure VM を移動<br/><br/> サブスクリプション内およびサブスクリプション間 | なし | いいえ


## <a name="support-for-azure-compute-replicate-to-azure"></a>Azure コンピューティングのサポート (Azure にレプリケート)

**コンピューティング機能** | **VMware/物理サーバー** | **Hyper-V (VMM なし)** | **Hyper-V (VMM あり)**
--- | --- | --- | ---
共有ディスク ゲスト クラスター | なし | いいえ | なし
可用性セット | いいえ | いいえ | なし
ハブ | あり | あり | はい

## <a name="support-for-azure-vms"></a>Azure VM のサポート

Site Recovery をデプロイすると、Azure でサポートされた任意のオペレーティング システムを実行している仮想マシンまたは物理サーバーをレプリケートできます。 これには、Windows と Linux のほとんどのバージョンが含まれます。 レプリケートするオンプレミスの VM は、Azure の要件を満たしている必要があります。

**機能** | **要件** | **詳細**
--- | --- | ---
**Hyper-V ホスト** | Windows Server 2012 R2 以降が実行されている必要があります。 | オペレーティング システムがサポートされていない場合は、前提条件の確認は失敗します。
**VMware ハイパーバイザー** | サポートされるオペレーティング システム | [要件の確認](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
**ゲスト オペレーティング システム** | Hyper-V から Azure へのレプリケーション: Site Recovery では、 [Azure でサポートされている](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)すべてのオペレーティング システムがサポートされます。 <br/><br/> VMware と物理サーバーのレプリケーション: Windows と Linux の [前提条件](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | サポートされていない場合、前提条件の確認は失敗します。
**ゲスト オペレーティング システムのアーキテクチャ** | 64 ビット | サポートされていない場合、前提条件の確認は失敗します。
**オペレーティング システムのディスク サイズ** | 最大 1023 GB | サポートされていない場合、前提条件の確認は失敗します。
**オペレーティング システムのディスク数** | 1 | サポートされていない場合、前提条件の確認は失敗します。
**データ ディスク数** | **VMware VM を Azure に**レプリケートする場合は 64 個以下、**HYPER-V VM を Azure に**レプリケートする場合は 16 個以下 | サポートされていない場合、前提条件の確認は失敗します。
**データ ディスク VHD のサイズ** | 最大 1023 GB | サポートされていない場合、前提条件の確認は失敗します。
**ネットワーク アダプター** | 複数のアダプターがサポートされます。 |
**静的 IP アドレス** | サポートされています | プライマリ仮想マシンが静的 IP アドレスを使用している場合、Azure で作成される仮想マシンに静的 IP アドレスを指定できます。<br/><br/> **Hyper-V で実行されている Linux VM** の静的な IP アドレスはサポートされていません。
**iSCSI ディスク** | サポートされていません | サポートされていない場合、前提条件の確認は失敗します。
**共有 VHD** | サポートされていません | サポートされていない場合、前提条件の確認は失敗します。
**FC ディスク** | サポートされていません | サポートされていない場合、前提条件の確認は失敗します。
**ハード ディスク フォーマット** | VHD  <br/><br/> VHDX | VHDX は現在、Azure でサポートされていませんが、Azure にフェールオーバーするとき、Site Recovery が VHDX を VHD に自動的に変換します。 オンプレミスにフェールバックした場合、仮想マシンは引き続き VHDX 形式を使用します。
**Bitlocker** | サポートされていません | 仮想マシンを保護する前に BitLocker は無効にしてください。
**VM 名** | 1 ～ 63 文字で指定します。 名前に使用できるのは、英文字、数字、およびハイフンのみです。 名前の先頭と末尾は、英文字または数字である必要があります。 | Site Recovery で仮想マシンのプロパティの値を更新します。
**VM の種類** | 第 1 世代<br/><br/> 第 2 世代 - Windows | VHDX としてフォーマットされている 1 つまたは 2 つのデータ ボリュームが含まれる、300 GB 未満で OS ディスクタイプが基本の第 2 世代の VM はサポートされています。<br/><br/>に関するページを参照してください。 第 2 世代の Linux VM はサポートされていません。 [詳細情報](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) |








## <a name="next-steps"></a>次のステップ
[前提条件を確認する](site-recovery-prereq.md)

