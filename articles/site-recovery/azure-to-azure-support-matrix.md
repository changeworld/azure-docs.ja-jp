---
title: Azure Site Recovery を使用した Azure リージョン間での Azure IaaS VM のディザスター リカバリーに関する Azure Site Recovery サポート マトリックス | Microsoft Docs
description: ディザスター リカバリー (DR) のニーズに応じて、リージョン間で Azure 仮想マシン (VM) を Azure Site Recovery でレプリケートするときのサポートされるオペレーティング システムと構成についてまとめます。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/12/2018
ms.author: raynew
ms.openlocfilehash: 50f9027301e4a04cd5624deee084429c803e04f4
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53342716"
---
# <a name="support-matrix-for-replicating-from-one-azure-region-to-another"></a>Azure リージョン間でレプリケートするためのマトリックスのサポート

この記事は、[Azure Site Recovery](site-recovery-overview.md) サービスを使用して、ある Azure リージョンから別の Azure リージョンへの Azure VM のレプリケート、フェールオーバー、および復旧を行うディザスター リカバリーをデプロイするときにサポートされる構成とコンポーネントをまとめたものです。


## <a name="deployment-method-support"></a>デプロイ方法のサポート

**デプロイ方法** |  **サポートされるかどうか**
--- | ---
**Azure Portal** | サポートされています
**PowerShell** | [PowerShell を使用した Azure から Azure へのレプリケーション](azure-to-azure-powershell.md)
**REST API** | 現在、サポートされていません
**CLI** | 現在、サポートされていません


## <a name="resource-support"></a>リソースのサポート

**リソースのアクション** | **詳細**
--- | --- | ---
**リソース グループ間の資格情報コンテナーの移動** | サポートされていません
**リソース グループ間のコンピューティング、ストレージ、およびネットワークの移動** | サポートされていません。<br/><br/> VM のレプリケート後に VM や関連コンポーネント (ストレージやネットワークなど) を移動する場合は、その VM のレプリケーションを無効にしてから、再度有効にする必要があります。
**ディザスター リカバリーのためにあるサブスクリプションから別のサブスクリプションに Azure VM をレプリケートする** | 同じ Azure Active Directory テナント内でサポートされます。
**サポートされている地理的なクラスター内 (サブスクリプション内外) のさまざまなリージョンにわたって VM を移行する** | 同じ Azure Active Directory テナント内でサポートされます。
**同じリージョン内の VM の移行** | サポートされていません。

## <a name="region-support"></a>リージョンのサポート

同じ地理クラスター内の 2 つのリージョン間で VM をレプリケートして、復旧できます。 地理クラスターは、データの遅延と主権を念頭に置きながら定義されます。

**地理クラスター** | **Azure リージョン**
-- | --
アメリカ | カナダ東部、カナダ中部、米国中南部、米国中西部、米国東部、米国東部 2、米国西部、米国西部 2、米国中部、米国中北部
ヨーロッパ | 英国西部、英国南部、北ヨーロッパ、西ヨーロッパ、フランス中部、フランス南部
アジア | インド南部、インド中部、東南アジア、東アジア、東日本、西日本、韓国中部、韓国南部
オーストラリア   | オーストラリア東部、オーストラリア南東部、オーストラリア中部、オーストラリア中部 2
Azure Government    | 米国政府バージニア、US Gov アイオワ、米国政府アリゾナ、米国政府テキサス、米国防総省東部、米国防総省中部
ドイツ | ドイツ中部、ドイツ北東部
中国 | 中国東部、中国北部、中国北部 2、中国東部 2

>[!NOTE]
>
> ブラジル南部リージョンについては、次のいずれかのリージョンに対してレプリケートとフェールオーバーが可能です: 米国中南部、米国中西部、米国東部、米国東部 2、米国西部、米国西部 2、米国中北部。</br>
> Site Recovery で、ソース リージョン (VM をそこから保護可能) として使用できるリージョンがブラジル南部だけになっていることに注意してください。 米国中南部のような Azure リージョンのいずれも、ターゲット DR リージョンとして使用することはできません。 なぜなら地理的な距離が原因で遅延が発生しているからであり、ブラジル南部以外のアメリカのリージョンを選択することをお勧めします。  

## <a name="cache-storage"></a>キャッシュ ストレージ

この表は、レプリケーション中に Site Recovery によって使用されるキャッシュ ストレージ アカウントのサポートをまとめたものです。

**設定** | **サポート** | **詳細**
--- | --- | ---
汎用目的 V2 ストレージ アカウント (ホット層とクール層) | サポートされていません。 | V2 のトランザクション コストは V1 ストレージ アカウントより大幅に多いため、キャッシュ ストレージに制限があります。
仮想ネットワークの Azure Storage ファイアウォール  | サポートされています | ファイアウォールが有効なキャッシュ ストレージ アカウントまたはターゲット ストレージ アカウントを使用している場合は必ず、['信頼できる Microsoft サービスを許可'](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) してください。


## <a name="replicated-machine-operating-systems"></a>レプリケートされるマシンのオペレーティング システム

Site Recovery は、このセクションに示したオペレーティング システムを実行する Azure VM のレプリケーションをサポートしています。

### <a name="windows"></a> Windows

**オペレーティング システム** | **詳細**
--- | ---
Windows Server 2016  | サーバー コア、デスクトップ エクスペリエンス搭載サーバー
Windows Server 2012 R2 |
Windows Server 2012 |
Windows Server 2008 R2 | SP1 以降を実行

#### <a name="linux"></a> Linux

**オペレーティング システム** | **詳細**
--- | ---
Red Hat Enterprise Linux | 6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6  
CentOS | 6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6
Ubuntu 14.04 LTS Server | [サポートされるカーネル バージョン](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16.04 LTS Server | [サポートされるカーネル バージョン](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> パスワード ベースの認証とログインを使用しており、cloud-init パッケージを使用してクラウド VM を構成する Ubuntu サーバーでは、(cloudinit 構成に応じて) フェールオーバー時にパスワード ベースのログインが無効になっている場合があります。 パスワード ベースのログインは、Azure portal で、フェールオーバーされた VM の [サポート] > [トラブルシューティング] > [設定] メニューからパスワードをリセットすることで、その仮想マシンで再度有効にできます。
Debian 7 | [サポートされるカーネル バージョン](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [サポートされるカーネル バージョン](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1、SP2、SP3。 [サポートされるカーネル バージョン](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> レプリケートするマシンの SP3 から SP4 へのアップグレードはサポートされません。 レプリケートされたマシンがアップグレードされた場合は、レプリケーションを無効にして、アップグレード後にレプリケーションを再度有効にする必要があります。
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4、6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5 <br/><br/> Red Hat 互換カーネルまたは Unbreakable Enterprise カーネル リリース 3 (UEK3) のいずれかを実行している。


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Azure 仮想マシン用のサポートされる Ubuntu カーネル バージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
14.04 LTS | 9.20 | 3.13.0-24-generic から 3.13.0-161-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic から 4.4.0-138-generic、<br/>4.15.0-1023-azure から 4.15.0-1030-azure |
14.04 LTS | 9.19 | 3.13.0-24-generic から 3.13.0-153-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic ～ 4.4.0-131-generic |
14.04 LTS | 9.18 | 3.13.0-24-generic から 3.13.0-151-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic から 4.4.0-128-generic |
14.04 LTS | 9.17 | 3.13.0-24-generic から 3.13.0-147-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic から 4.4.0-124-generic |
|||
16.04 LTS | 9.20 | 4.4.0-21-generic から 4.4.0-138-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-38-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1030-azure|
16.04 LTS | 9.19 | 4.4.0-21-generic ～ 4.4.0-131-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic ～ 4.15.0-30-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure ～ 4.15.0-1019-azure|
16.04 LTS | 9.18 | 4.4.0-21-generic から 4.4.0-128-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure |
16.04 LTS | 9.17 | 4.4.0-21-generic から 4.4.0-124-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-41-generic、<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1016-azure |


#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Azure 仮想マシン用のサポートされる Debian カーネル バージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
Debian 7 | 9.17、9.18、9.19、9.20 | 3.2.0-4-amd64 から 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.20 | 3.16.0-4-amd64 から 3.16.0-7-amd64、4.9.0-0.bpo.4-amd64 から 4.9.0-0.bpo.8-amd64 |
Debian 8 | 9.19 | 3.16.0-4-amd64 から 3.16.0-6-amd64、4.9.0-0.bpo.4-amd64 から 4.9.0-0.bpo.7-amd64 |
Debian 8 | 9.17、9.18 | 3.16.0-4-amd64 から 3.16.0-6-amd64、4.9.0-0.bpo.4-amd64 から 4.9.0-0.bpo.6-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Azure 仮想マシン用のサポートされる SUSE Linux Enterprise Server 12 カーネル バージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3) | 9.20 | SP1 3.12.49-11-default から 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default から 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default から 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default から 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default から 4.4.162-94.69-default |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3) | 9.19 | SP1 3.12.49-11-default から 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default から 3.12.74-60.64.93-default</br></br> SP2 4.4.21-69-default から 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default から 4.4.121-92.80-default</br></br>SP3 4.4.73-5-default から 4.4.140-94.42-default |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3) | 9.18 | SP1 3.12.49-11-default から 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default から 3.12.74-60.64.93-default</br></br> SP2 4.4.21-69-default から 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default から 4.4.121-92.80-default</br></br>SP3 4.4.73-5-default から 4.4.138-94.39-default |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3) | 9.17 | SP1 3.12.49-11-default から 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default から 3.12.74-60.64.88-default</br></br> SP2 4.4.21-69-default から 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default</br></br>SP3 4.4.73-5-default から 4.4.126-94.22-default |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>レプリケートされるマシン - Linux ファイル システム/ゲスト ストレージ

* ファイル システム: ext3、ext4、ReiserFS (Suse Linux Enterprise Server のみ)、XFS、BTRFS
* ボリューム マネージャー:LVM2
* マルチパス ソフトウェア:デバイス マッパー


## <a name="replicated-machines---compute-settings"></a>レプリケートされるマシン - コンピューティングの設定

**設定** | **サポート** | **詳細**
--- | --- | ---
サイズ | 少なくとも 2 つの CPU コアと 1 GB の RAM を備えた任意の Azure VM サイズ | [Azure 仮想マシンのサイズ](../virtual-machines/windows/sizes.md)を確認してください。
可用性セット | サポートされています | 既定のオプションを使用して Azure VM のレプリケーションを有効にした場合は、ソース リージョンの設定に基づいて可用性セットが自動的に作成されます。 次の設定を変更できます。
可用性ゾーン | サポートされています |  
Hybrid Use Benefit (HUB) | サポートされています | ソース VM で HUB ライセンスが有効になっている場合、テスト フェールオーバー VM またはフェールオーバーした VM でも HUB ライセンスが使用されます。
VM スケール セット | サポートされていません |
Azure ギャラリー イメージ - Microsoft が公開 | サポートされています | サポート対象のオペレーティング システムで VM が実行されている場合にサポートされます。
Azure ギャラリー イメージ - サード パーティが公開 | サポートされています | サポート対象のオペレーティング システムで VM が実行されている場合にサポートされます。
カスタム イメージ - サード パーティが公開 | サポートされています | サポート対象のオペレーティング システムで VM が実行されている場合にサポートされます。
Site Recovery を使用して移行された VM | サポートされています | Site Recovery を使用して Azure に移行された VMware VM または物理マシンの場合は、マシンで実行されている古いバージョンのモビリティ サービスをアンインストールし、マシンを再起動してから、それを他の Azure リージョンにレプリケートする必要があります。

## <a name="replicated-machines---disk-actions"></a>レプリケートされるマシン - ディスクのアクション

**アクション** | **詳細**
-- | ---
レプリケートされた VM のディスク サイズの変更 | サポートされています
レプリケートされた VM にディスクを追加する | サポートされていません。<br/><br/> VM のレプリケーションを無効にし、ディスクを追加してから、レプリケーションを再び有効にする必要があります。

## <a name="replicated-machines---storage"></a>レプリケートされるマシン - ストレージ

この表は、Azure VM の OS ディスク、データ ディスク、一時ディスクに対するサポートをまとめたものです。

- [Linux](../virtual-machines/linux/disk-scalability-targets.md) VM と [Windows](../virtual-machines/windows/disk-scalability-targets.md) VM でのパフォーマンスの問題を回避するため、VM ディスクの制限とターゲットを守ることが重要です。
- 既定の設定でデプロイすると、Site Recovery によって、ソースの設定に基づいてディスクとストレージ アカウントが自動的に作成されます。
- カスタマイズする場合は、必ずガイドラインに従ってください。

**コンポーネント** | **サポート** | **詳細**
--- | --- | ---
OS ディスクの最大サイズ | 2048 GB | VM ディスクに関する[詳細を表示します](../virtual-machines/windows/about-disks-and-vhds.md#disks-used-by-vms)。
一時ディスク | サポートされていません | 一時ディスクは常にレプリケーションから除外されます。<br/><br/> 一時ディスクに永続的なデータを置かないでください。 [詳細情報](../virtual-machines/windows/about-disks-and-vhds.md#temporary-disk)。
データ ディスクの最大サイズ | 4095 GB |
データ ディスクの最大数 | 最大 64 (特定の Azure VM サイズでのサポートに従います) | VM サイズに関する[詳細を表示します](../virtual-machines/windows/sizes.md)。
データ ディスクの変更レート | Premium ストレージではディスクあたり最大 10 MBps。 Standard ストレージではディスクあたり最大 2 MBps。 | ディスクでの平均データ変更レートが継続的に最大値より高い場合、レプリケーションが追いつかなくなります。<br/><br/>  ただし、上限の超過が散発的である場合は、レプリケーションが追いつくことができます。しかし、復旧ポイントがわずかに遅延することもあります。
データ ディスク - Standard ストレージ アカウント | サポートされています |
データ ディスク - Premium ストレージ アカウント | サポートされています | VM のディスクが Premium ストレージ アカウントと Standard ストレージ アカウントに分散している場合は、ターゲット リージョンでのストレージ構成が確実に同じになるように、ディスクごとに異なるターゲット ストレージ アカウントを選択できます。
マネージド ディスク - Standard | Azure Site Recovery がサポートされている Azure リージョンでサポートされます。 |  
マネージド ディスク - Premium | Azure Site Recovery がサポートされている Azure リージョンでサポートされます。 |
Standard SSD | サポートされていません |
冗長性 | LRS と GRS がサポートされます。<br/><br/> ZRS はサポートされていません。
クールおよびホット ストレージ | サポートされていません | VM ディスクは、クールおよびホット ストレージではサポートされません
記憶域 | サポートされています |         
保存時の暗号化 (SSE) | サポートされています | SSE はストレージ アカウントでの既定の設定です。   
Windows OS 用 Azure Disk Encryption (ADE) | [Azure AD アプリでの暗号化](https://aka.ms/ade-aad-app)を有効になっているVMはサポートされています。 |
Linux OS 用 Azure Disk Encryption (ADE) | サポートされていません |
ディスクのホット アド/削除 | サポートされていません | VM 上でデータ ディスクを追加または削除する場合は、レプリケーションを無効にしてから、もう一度 VM に対してレプリケーションを有効にする必要があります。
ディスクの除外 | サポートされていません|   一時ディスクは既定で除外されます。
記憶域スペース ダイレクト  | サポートされていません|
スケールアウト ファイル サーバー  | サポートされていません|
LRS | サポートされています |
GRS | サポートされています |
RA-GRS | サポートされています |
ZRS | サポートされていません |  
クールおよびホット ストレージ | サポートされていません | 仮想マシン ディスクは、クールおよびホット ストレージではサポートされません
仮想ネットワークの Azure Storage ファイアウォール  | サポートされています | ストレージ アカウントへの仮想ネットワーク アクセスを制限している場合は、['信頼できる Microsoft サービスを許可'](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) していることを確認します。
汎用目的 V2 ストレージ アカウント (ホット層とクール層の両方) | いいえ  | 汎用目的 V1 のストレージ アカウントに比べて、トランザクション コストが非常に大きくなります

>[!IMPORTANT]
> パフォーマンスの問題を回避するために、[Linux](../virtual-machines/linux/disk-scalability-targets.md) または [Windows](../virtual-machines/windows/disk-scalability-targets.md) 仮想マシンの VM ディスクのスケーラビリティおよびパフォーマンスのターゲットを確認してください。 既定の設定に従うと、Site Recovery により、ソース構成に基づいて必要なディスクとストレージ アカウントが作成されます。 設定をカスタマイズして独自の設定を選択する場合は、ソース VM のディスクのスケーラビリティおよびパフォーマンスのターゲットに従ってください。

## <a name="azure-site-recovery-limits-to-replicate-data-change-rates"></a>データ変化率のレプリケートに対する Azure Site Recovery の制限
以下の表は、Azure Site Recovery の制限を示したものです。 上記の制限は、弊社のテストに基づいて公開されていますが、アプリケーション I/O として想定されるすべての組み合わせを網羅したものではありません。 実際の結果は、ご使用のアプリケーションで発生するさまざまな I/O によって異なることが考えられます。 また、ディスクごとのデータ チャーンと仮想マシンごとのデータ チャーンという考慮すべき 2 つの制限があることにも注意する必要があります。
たとえば、次の表の Premium P20 ディスクを見ると、Site Recovery はディスクごとに 5 MB/s のチャーンを処理できます。VM あたりのディスクの最大数が 5 であるため、VM ごとの合計チャーンの制限は 25 MB/s になります。

**レプリケーション先のストレージ** | **レプリケーション元の平均ディスク I/O サイズ** |**レプリケーション元ディスクの平均データ変更頻度** | **レプリケーション元ディスクの 1 日あたりのデータ変更頻度合計**
---|---|---|---
Standard Storage | 8 KB | 2 MB/秒 | (ディスクあたり) 168 GB
Premium P10 または P15 ディスク | 8 KB  | 2 MB/秒 | (ディスクあたり) 168 GB
Premium P10 または P15 ディスク | 16 KB | 4 MB/秒 |  (ディスクあたり) 336 GB
Premium P10 または P15 ディスク | 32 KB 以上 | 8 MB/秒 | (ディスクあたり) 672 GB
Premium P20、P30、P40、または P50 ディスク | 8 KB    | 5 MB/s | (ディスクあたり) 421 GB
Premium P20、P30、P40、または P50 ディスク | 16 KB 以上 |10 MB/s | (ディスクあたり) 842 GB
## <a name="replicated-machines---networking"></a>レプリケートされるマシン - ネットワーク
**構成** | **サポート** | **詳細**
--- | --- | ---
NIC | 特定の Azure VM サイズでサポートされる最大数 | フェールオーバー中に VM が作成されるときには NIC が作成されます。<br/><br/> フェールオーバー VM 上の NIC 数は、レプリケーションが有効にされたときのソース VM 上の NIC 数によって決まります。 レプリケーションを有効にした後に NIC を追加または削除しても、フェールオーバー後、レプリケートされた VM 上にある NIC の数は影響を受けません。
インターネット Load Balancer | サポートされています | 復旧計画の Azure Automation スクリプトを使用して、構成済みロード バランサーを関連付けます。
内部ロード バランサー | サポートされています | 復旧計画の Azure Automation スクリプトを使用して、構成済みロード バランサーを関連付けます。
パブリック IP アドレス | サポートされています | 既存のパブリック IP アドレスを NIC に関連付けます。 または、パブリック IP アドレスを作成し、復旧計画の Azure Automation スクリプトを使用して、それを NIC に関連付けます。
NIC 上の NSG | サポートされています | 復旧計画の Azure Automation スクリプトを使用して、NSG を NIC に関連付けます。  
サブネット上の NSG | サポートされています | 復旧計画の Azure Automation スクリプトを使用して、NSG を サブネットに関連付けます。
予約済みの (静的) IP アドレス | サポートされています | ソース VM で NIC が静的 IP を使用していて、ターゲット サブネットで同じ IP アドレスを使用できる場合、そのアドレスはフェールオーバーした VM に割り当てられます。<br/><br/> ターゲット サブネットで同じ IP アドレスを使用できない場合は、そのサブネットで使用できる IP アドレスの 1 つが、その VM 用に予約されます。<br/><br/> **[レプリケートされたアイテム]** > **[設定]** > **[コンピューティングとネットワーク]** > **[ネットワーク インターフェイス]** で、固定 IP アドレスとサブネットを指定することもできます。
動的 IP アドレス | サポートされています | ソース VM で NIC に動的 IP アドレス指定が設定されている場合、フェールオーバーした VM の NIC も既定で動的になります。<br/><br/> 必要な場合は、これを固定 IP アドレスに変更できます。
Traffic Manager     | サポートされています | トラフィックがソース リージョンのエンドポイントに定期的にルーティングされ、フェールオーバーの場合はターゲット リージョンのエンドポイントにルーティングされるように、Traffic Manager を事前に構成することができます。
Azure DNS | サポートされています |
[カスタム DNS]  | サポートされています |    
非認証プロキシ | サポートされています | [ネットワーク ガイダンスのドキュメント](site-recovery-azure-to-azure-networking-guidance.md)を参照してください。    
認証済みプロキシ | サポートされていません | VM が送信接続に認証済みプロキシを使用している場合は、Azure Site Recovery でレプリケートできません。    
オンプレミスでのサイト間 VPN (ExpressRoute あり/なし)| サポートされています | Site Recovery トラフィックがオンプレミスにルーティングされないように、UDR と NSG が構成されていることを確認します。 [ネットワーク ガイダンスのドキュメント](site-recovery-azure-to-azure-networking-guidance.md)を参照してください。  
VNet 間接続 | サポートされています | [ネットワーク ガイダンスのドキュメント](site-recovery-azure-to-azure-networking-guidance.md)を参照してください。  
仮想ネットワーク サービス エンドポイント | サポートされています | ストレージ アカウントへの仮想ネットワーク アクセスを制限している場合は、信頼された Microsoft サービスがストレージ アカウントへのアクセスを許可されることを確認します。
高速ネットワーク | サポートされています | 高速ネットワークは、ソース VM で有効にする必要があります。 [詳細情報](azure-vm-disaster-recovery-with-accelerated-networking.md)。



## <a name="next-steps"></a>次の手順
- [Azure VM のレプリケートに関するネットワーク面のガイダンス](site-recovery-azure-to-azure-networking-guidance.md)を確認します。
- [Azure VM をレプリケートして](site-recovery-azure-to-azure.md)ディザスター リカバリーを展開します。
