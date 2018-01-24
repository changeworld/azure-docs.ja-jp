---
title: "Azure への Hyper-V レプリケーションのサポート マトリックス | Microsoft Docs"
description: "Azure Site Recovery を使用して、Azure への Hyper-V レプリケーションを行う場合にサポートされるコンポーネントと要件の概要について説明します。"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 12/31/2017
ms.author: raynew
ms.openlocfilehash: 5918c56c2b7d01c884bf846e3a7d621b3393bb96
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2018
---
# <a name="support-matrix-for-hyper-v-replication-to-azure"></a>Azure への Hyper-V レプリケーションのサポート マトリックス


この記事では、[Azure Site Recovery](site-recovery-overview.md) サービスを使用して、オンプレミス Hyper-V VM の Azure へのディザスター リカバリーを行う場合にサポートされるコンポーネントと設定の概要について説明します。


## <a name="supported-scenarios"></a>サポートされるシナリオ

**シナリオ** | **詳細**
--- | --- 
**Hyper-V (VMM あり)** | System Center Virtual Machine Manager (VMM) ファブリックで管理されている Hyper-V ホスト上で実行されている VM の場合、Azure へのディザスター リカバリーを実行できます<br/><br/> このシナリオは、Azure Portal または PowerShell を使用して展開できます。<br/><br/> Hyper-V ホストが VMM で管理されている場合は、セカンダリ オンプレミス サイトへのディザスター リカバリーを実行することもできます。 このシナリオの詳細については、[こちらの記事](tutorial-vmm-to-vmm.md)を参照してください。
**Hyper-V (VMM なし)** | VMM によって管理されていない Hyper-V ホスト上で実行されている VM の場合、Azure へのディザスター リカバリーを実行できます。<br/><br/> このシナリオは、Azure Portal または PowerShell を使用して展開できます。 


## <a name="on-premises-servers"></a>オンプレミスのサーバー

**サーバー** | **要件** | **詳細**
--- | --- | ---
**Hyper-V (VMM なしで実行)** | Windows Server 2016、最新の更新プログラムが適用された Windows Server 2012 R2。 | Site Recovery で Hyper-V サイトを構成する場合、Windows Server 2016 と 2012 R2 を実行するホストの混在はサポートされません。<br/><br/> Windows Server 2016 を実行しているホスト上にある VM の場合、別の場所への回復はサポートされていません。
**Hyper-V (VMM を使用して実行)** | VMM 2016、VMM 2012 R2 | VMM が使用されている場合、Windows Server 2016 ホストは、VMM 2016 で管理されている必要があります。<br/><br/> Windows Server 2016 上および 2012 R2 上で実行されている Hyper-V ホストが混在する VMM クラウドは、現在サポートされていません。<br/><br/> 既存の VMM 2012 R2 サーバーから 2016 へのアップグレードを含む環境はサポートされていません。


## <a name="replicated-vms"></a>レプリケートされた VM


次の表は、VM のサポートをまとめた一覧です。 Site Recovery は、サポートされているオペレーティング システム上で実行されているすべてのワークロードをサポートします。 

 **コンポーネント** | **詳細**
--- | ---
VM 構成 | Azure にレプリケートする VM は、[Azure の要件](#failed-over-azure-vm-requirements)を満たしている必要があります。
ゲスト オペレーティング システム | [Azure がサポートする](https://technet.microsoft.com/library/cc794868.aspx)任意のゲスト OS<br/><br/> Windows Server 2016 の Nano Server はサポートされていません。




## <a name="hyper-v-network-configuration"></a>Hyper-V のネットワーク構成

**コンポーネント** | **Hyper-V (VMM あり)** | **Hyper-V (VMM なし)**
--- | --- | ---
ホスト ネットワーク: NIC チーミング | [はい]
ホスト ネットワーク: VLAN | [はい]
ホスト ネットワーク: IPv4 | [はい]
ホスト ネットワーク: IPv6 | いいえ 
ゲスト VM ネットワーク: NIC チーミング | いいえ 
ゲスト VM ネットワーク: IPv4 | [はい]
ゲスト VM ネットワーク: IPv6 | いいえ 
ゲスト VM ネットワーク: 静的 IP (Windows) | [はい]
ゲスト VM ネットワーク: 静的 IP (Linux) | いいえ 
ゲスト VM ネットワーク: マルチ NIC | [はい]



## <a name="azure-vm-network-configuration-after-failover"></a>Azure VM ネットワーク構成 (フェールオーバー後)

**コンポーネント** | **Hyper-V (VMM あり)** | **Hyper-V (VMM なし)**
--- | --- | ---
ExpressRoute | [はい] | [はい]
ILB | [はい] | [はい]
ELB | [はい] | [はい]
Traffic Manager | [はい] | [はい]
マルチ NIC | [はい] | [はい]
予約済み IP | [はい] | [はい]
IPv4 | [はい] | [はい]
送信元 IP アドレスを保持する | [はい] | [はい]
VNET サービス エンドポイント<br/><br/> (Azure Storage ファイアウォールと仮想ネットワーク) | いいえ  | いいえ 


## <a name="hyper-v-host-storage"></a>Hyper-V ホスト ストレージ

**Storage** | **Hyper-V (VMM あり)** | Hyper-V (VMM なし)
--- | --- | --- | ---
NFS | 該当なし | 該当なし
SMB 3.0 | [はい] | [はい]
SAN (ISCSI) | [はい] | [はい]
マルチパス (MPIO) 以下でテスト済み:<br></br> Microsoft DSM、EMC PowerPath 5.7 SP4<br/><br/> EMC PowerPath DSM for CLARiiON | [はい] | [はい]

## <a name="hyper-v-vm-guest-storage"></a>Hyper-V VM ゲスト ストレージ

**Storage** | **Hyper-V (VMM あり)** | Hyper-V (VMM なし)
--- | --- | ---
VMDK | 該当なし | 該当なし
VHD/VHDX | [はい] | [はい]
Generation 2 VM | [はい] | [はい]
EFI/UEFI| [はい] | [はい]
共有クラスター ディスク | いいえ  | いいえ 
暗号化されたディスク | いいえ  | いいえ 
NFS | 該当なし | 該当なし
SMB 3.0 | いいえ  | いいえ 
RDM | 該当なし | 該当なし
1 TB より大きいディスク | はい、最大 4095 GB | はい、最大 4095 GB
ディスク: 4K 論理および物理セクター | サポートされない: Gen 1/Gen 2 | サポートされない: Gen 1/Gen 2
ディスク: 4K 論理および 512 バイトの物理セクター | [はい] |  [はい]
ストライピングされたディスクのボリューム > 1 TB<br/><br/> LVM 論理ボリュームの管理 | [はい] | [はい]
記憶域 | [はい] | [はい]
ディスクのホット アド/削除 | いいえ  | いいえ 
ディスクの除外 | [はい] | [はい]
マルチパス (MPIO) | [はい] | [はい]

## <a name="azure-storage"></a>Azure Storage

**コンポーネント** | **Hyper-V (VMM あり)** | **Hyper-V (VMM なし)**
--- | --- | ---
LRS | [はい] | [はい]
GRS | [はい] | [はい]
RA-GRS | [はい] | [はい]
クール ストレージ | いいえ  | いいえ 
ホット ストレージ| いいえ  | いいえ 
ブロック BLOB | いいえ  | いいえ 
保存時の暗号化 (SSE)| [はい] | [はい]
Premium Storage | [はい] | [はい]
インポート/エクスポート サービス | いいえ  | いいえ 
VNET サービス エンドポイント (Azure ストレージ ファイアウォールと VNET)、レプリケーション データに使用されるストレージ アカウントをキャッシュするターゲット上 | いいえ  | いいえ 


## <a name="azure-compute-features"></a>Azure コンピューティング機能

**機能** | **Hyper-V (VMM あり)** | **Hyper-V (VMM なし)**
--- | --- | ---
可用性セット | [はい] | [はい]
ハブ | [はい] | [はい]  
管理ディスク | はい、フェールオーバー用<br/><br/> 管理ディスクのフェールバックはサポートされません | はい、フェールオーバー用<br/><br/> 管理ディスクのフェールバックはサポートされません

## <a name="azure-vm-requirements"></a>Azure VM の要件

Azure にレプリケートするオンプレミス VM は、この表にまとめられている Azure VM の要件を満たしている必要があります。

**コンポーネント** | **要件** | **詳細**
--- | --- | ---
**ゲスト オペレーティング システム** | Site Recovery では、[Azure でサポートされている](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx)すべてのオペレーティング システムがサポートされます。  | サポートされていない場合、前提条件の確認は失敗します。
**ゲスト オペレーティング システムのアーキテクチャ** | 64 ビット | サポートされていない場合、前提条件の確認は失敗します。
**オペレーティング システムのディスク サイズ** | 第 1 世代の VM では最大 2048 GB です。<br/><br/> 第 2 世代の VM では最大 300 GB です。  | サポートされていない場合、前提条件の確認は失敗します。
**オペレーティング システムのディスク数** | 1 | サポートされていない場合、前提条件の確認は失敗します。
**データ ディスク数** | 16 以下  | サポートされていない場合、前提条件の確認は失敗します。
**データ ディスク VHD のサイズ** | 最大 4095 GB | サポートされていない場合、前提条件の確認は失敗します。
**ネットワーク アダプター** | 複数のアダプターがサポートされます。 |
**共有 VHD** | サポートされていません | サポートされていない場合、前提条件の確認は失敗します。
**FC ディスク** | サポートされていません | サポートされていない場合、前提条件の確認は失敗します。
**ハード ディスク フォーマット** | VHD  <br/><br/> VHDX | Azure にフェールオーバーすると、Site Recovery によって自動的に VHDX が VHD に変換されます。 オンプレミスにフェールバックした場合、仮想マシンは引き続き VHDX 形式を使用します。
**Bitlocker** | サポートされていません | VM のレプリケーションを有効にする前に、Bitlocker を無効にする必要があります。
**VM 名** | 1 ～ 63 文字で指定します。 名前に使用できるのは、英文字、数字、およびハイフンのみです。 VM 名の最初と最後は、文字か数字とする必要があります。 | Site Recovery の VM プロパティ値を更新します。
**VM の種類** | 第 1 世代<br/><br/> 第 2 世代 - Windows | OS ディスクの種類が基本 (VHDX としてフォーマットされている 1 つまたは 2 つのデータ ボリュームが含まれる) でディスク容量が 300 GB 未満の第 2 世代の VM はサポートされています。<br></br>第 2 世代の Linux VM はサポートされていません。 [詳細情報](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)|

## <a name="recovery-services-vault-actions"></a>Recovery Services コンテナーのアクション

**アクション** |  **Hyper-V (VMM あり)** | **Hyper-V (VMM なし)**
--- | --- | --- 
リソース グループ間の資格情報コンテナーの移動<br/><br/> サブスクリプション内およびサブスクリプション間 | いいえ  | いいえ  
リソース グループ間でストレージ、ネットワーク、Azure VM を移動<br/><br/> サブスクリプション内およびサブスクリプション間 | いいえ  | いいえ  


## <a name="provider-and-agent"></a>プロバイダーとエージェント

デプロイがこの記事の設定と互換性を持たせるには、最新のプロバイダーとエージェントのバージョンを実行してください。

**名前** | **説明** | **詳細**
--- | --- | --- | --- | ---
**Azure Site Recovery プロバイダー** | オンプレミスのサーバーと Azure の間の通信を調整します <br/><br/> Hyper-V (VMM あり): VMM サーバーにインストールします<br/><br/> Hyper-V (VMM なし): Hyper-V ホストにインストールします| 最新バージョン: 5.1.2700.1 (ポータルから入手可能)<br/><br/> [最新の機能と修正](https://aka.ms/latest_asr_updates)
**Microsoft Azure Recovery Services (MARS) エージェント** | Hyper-V VM と Azure の間のレプリケーションを調整します<br/><br/> (VMM の有無にかかわらず) オンプレミスの Hyper-V サーバーにインストールされます | ポータルから入手可能な最新のエージェント






## <a name="next-steps"></a>次の手順
オンプレミス Hyper-V VM のディザスター リカバリーのために [Azure を準備する](tutorial-prepare-azure.md)方法を学びます。 
