---
title: Azure Site Recovery を使用した VMware VM および物理サーバーの Azure へのレプリケートに関するサポート マトリックス | Microsoft Docs
description: Azure Site Recovery を使用して VMware VM および物理サーバーを Azure にレプリケートする処理をサポートしているオペレーティング システムとコンポーネントの概要について説明します。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/09/2018
ms.author: raynew
ms.openlocfilehash: d19aa4c3765beecc853a1b800a7ba1d3ebd74e9c
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40004329"
---
# <a name="support-matrix-for-vmware-and-physical-server-replication-to-azure"></a>VMware および物理サーバーの Azure へのレプリケーションのサポート マトリックス

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して、VMware VM の Azure へのディザスター リカバリーを行う場合にサポートされるコンポーネントと設定の概要について説明します。

最も簡単なデプロイ シナリオで Azure Site Recovery の使用を開始するには、こちらの[チュートリアル](tutorial-prepare-azure.md)を参照してください。 Azure Site Recovery アーキテクチャの詳細については、[こちら](vmware-azure-architecture.md)をご覧ください。

## <a name="replication-scenario"></a>レプリケーション シナリオ

**シナリオ** | **詳細**
--- | ---
VMware VM | オンプレミス VMware VM の Azure へのレプリケーション。 このシナリオは、Azure portal または [PowerShell](vmware-azure-disaster-recovery-powershell.md) を使用してデプロイできます。
物理サーバー | オンプレミスの Windows または Linux の物理サーバーから Azure へのレプリケーション。 このシナリオは、Azure Portal で展開できます。

## <a name="on-premises-virtualization-servers"></a>オンプレミスの仮想化サーバー

**サーバー** | **要件** | **詳細**
--- | --- | ---
VMware | vCenter Server 6.7、6.5、6.0、5.5、または vSphere 6.7、6.5、6.0、5.5 | vCenter サーバーを使用することをお勧めします。<br/><br/> vSphere ホストと vCenter サーバーはプロセス サーバーと同じネットワーク内に存在することが推奨されます。 既定では、プロセス サーバー コンポーネントは構成サーバーで実行されるため、専用のプロセス サーバーを設定していなければ、これがその中に構成サーバーを設定するネットワークになります。
物理 | 該当なし

## <a name="site-recovery-configuration-server"></a>Site Recovery 構成サーバー

構成サーバーはオンプレミスのマシンで、構成サーバー、プロセス サーバー、マスター ターゲット サーバーを含む Site Recovery のコンポーネントを実行します。 VMware のレプリケーションの場合は、VMware VM を作成する OVF テンプレートを使用して、すべての要件を含む構成サーバーを設定します。 物理サーバーのレプリケーションの場合は、構成サーバーのマシンを手動で設定します。

**コンポーネント** | **要件**
--- |---
CPU コア数 | 8
RAM | 16 GB
ディスクの数 | ディスク 3 台<br/><br/> ディスクには、OS ディスク、プロセス サーバーのキャッシュ ディスク、フェールバック用リテンション ドライブが含まれます。
ディスクの空き領域 | プロセス サーバーのキャッシュのために必要な 600 GB の領域。
ディスクの空き領域 | リテンション ドライブのために必要な 600 GB の領域。
オペレーティング システム  | Windows Server 2012 R2 または Windows Server 2016 |
オペレーティング システムのロケール | 英語 (en-us)
PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") をインストールする必要があります。
Windows Server の役割 | 以下は有効にしません: <br> - Active Directory Domain Services <br>- インターネット インフォメーション サービス <br> - Hyper-V |
グループ ポリシー| 以下は有効にしません: <br> - コマンド プロンプトへのアクセス禁止。 <br> - レジストリ編集ツールへのアクセス禁止。 <br> - ファイル添付の信頼ロジック。 <br> - スクリプト実行の有効化。 <br> [詳細情報](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | 以下を実行します。<br/><br/> - 既定の Web サイトが事前に存在しないようにする <br> - [匿名認証](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx)を有効にする <br> - [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) 設定を有効にする  <br> - ポート 443 でリッスンしている既存の Web サイト/アプリがないようにする<br>
NIC の種類 | VMXNET3 (VMware VM としてデプロイされている場合)
IP アドレスの種類 | 静的
ポート | コントロール チャネルのオーケストレーションに使用される 443<br>データ転送に使用される 9443

## <a name="replicated-machines"></a>レプリケートされるマシン

Site Recovery は、サポートされているマシンで実行されているすべてのワークロードのレプリケーションをサポートします。

**コンポーネント** | **詳細**
--- | ---
マシンの設定 | Azure にレプリケートするマシンは、[Azure の要件](#azure-vm-requirements)を満たしている必要があります。
Windows オペレーティング システム | 64 ビット Windows Server 2016 (Server Core、サーバーおよびデスクトップ エクスペリエンス)、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 以降。 </br></br>  [Windows Server 2008 SP2 以降 - 32 ビットおよび 64 ビット](migrate-tutorial-windows-server-2008.md) (移行のみ)。 </br></br> Windows 2016 の Nano Server はサポートされていません。
Linux オペレーティング システム | Red Hat Enterprise Linux: 5.2 から 5.11<b>\*\*</b>、6.1 から 6.9<b>\*\*</b>、7.0 から 7.5 <br/><br/>CentOS: 5.2 から 5.11<b>\*\*</b>、6.1 から 6.9<b>\*\*</b>、7.0 から 7.5 <br/><br/>Ubuntu 14.04 LTS サーバー[ (サポートされるカーネルのバージョン)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS サーバー[ (サポートされるカーネルのバージョン)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8[ (サポートされるカーネルのバージョン)](#debian-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 12 SP1、SP2、SP3 [ (サポートされるカーネルのバージョン)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/><br/>SUSE Linux Enterprise Server 11 SP3<b>\*\*</b> または SUSE Linux Enterprise Server 11 SP4 * </br></br>Red Hat 互換カーネルまたは Unbreakable Enterprise Kernel リリース 3 (UEK3) を実行している Oracle Enterprise Linux 6.4、6.5 <br/><br/></br>* *レプリケートされたマシンの SUSE Linux Enterprise Server 11 SP3 から SP4 へのアップグレードはサポートされていません。アップグレードするには、いったんレプリケーションを無効にし、アップグレードの後に再び有効にします。*</br></br><b>\*\*</b> *Azure の Linux およびオープン ソース テクノロジのサポートについては、[Azure の Linux 仮想マシンのサポート](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)に関するページを参照してください。Azure Site Recovery を使用すると、Azure で Linux サーバーをフェールオーバーして実行できますが、Linux ベンダーはサポートが終了していないバージョンのディストリビューションのみにサポートを制限している場合があります。*


>[!NOTE]
>
> - Linux ディストリビューションでは、ディストリビューションのマイナー バージョン リリース/更新の一部である stock カーネルのみがサポートされます。
>
> - 保護されているマシンの Linux ディストリビューションのメジャー バージョン間のアップグレードはサポートされていません。 アップグレードするには、いったんレプリケーションを無効にしてオペレーティング システムをアップグレードしてから、レプリケーションを再び有効にします。
>
> - Azure でマシンが起動するには、Red Hat Enterprise Linux 5.2 から 5.11 または CentOS 5.2 から 5.11 を実行しているサーバーに Linux Integration Services (LIS) コンポーネントがインストールされている必要があります。

### <a name="ubuntu-kernel-versions"></a>Ubuntu カーネルのバージョン


**サポートされているリリース** | **Azure Site Recovery モビリティ サービスのバージョン** | **カーネル バージョン** |
--- | --- | --- |
14.04 LTS | 9.18 | 3.13.0-24-generic から 3.13.0-153-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic から 4.4.0-130-generic |
14.04 LTS | 9.17 | 3.13.0-24-generic から 3.13.0-149-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic から 4.4.0-127-generic |
14.04 LTS | 9.16 | 3.13.0-24-generic ～ 3.13.0-144-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic ～ 4.4.0-119-generic、 |
14.04 LTS | 9.15 | 3.13.0-24-generic ～ 3.13.0-144-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic ～ 4.4.0-119-generic、 |
|||
16.04 LTS | 9.18 | 4.4.0-21-generic から 4.4.0-130-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic |
16.04 LTS | 9.17 | 4.4.0-21-generic から 4.4.0-127-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-43-generic |
16.04 LTS | 9.16 | 4.4.0-21-generic ～ 4.4.0-119-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-38-generic |
16.04 LTS | 9.15 | 4.4.0-21-generic ～ 4.4.0-119-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-38-generic |


### <a name="debian-kernel-versions"></a>Debian カーネルのバージョン


**サポートされているリリース** | **Azure Site Recovery モビリティ サービスのバージョン** | **カーネル バージョン** |
--- | --- | --- |
Debian 7 | 9.17、9.18 | 3.2.0-4-amd64 から 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
Debian 7 | 9.15、9.16 | 3.2.0-4-amd64 ～ 3.2.0-5-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.17、9.18 | 3.16.0-4-amd64 から 3.16.0-6-amd64、4.9.0-0.bpo.4-amd64 から 4.9.0-0.bpo.6-amd64 |
Debian 8 | 9.16 | 3.16.0-4-amd64 から 3.16.0-5-amd64、4.9.0-0.bpo.4-amd64 から 4.9.0-0.bpo.6-amd64 |
Debian 8 | 9.15 | 3.16.0-4-amd64 ～ 3.16.0-5-amd64、4.9.0-0.bpo.4-amd64 ～ 4.9.0-0.bpo.5-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 のサポートされるカーネルのバージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3) | 9.18 | SP1 3.12.49-11-default から 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default から 3.12.74-60.64.96-default</br></br> SP2 4.4.21-69-default から 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default から 4.4.121-92.85-default</br></br>SP3 4.4.73-5-default から 4.4.138-94.39-default |

## <a name="linux-file-systemsguest-storage"></a>Linux ファイル システム/ゲストのストレージ

**コンポーネント** | **サポートされています**
--- | ---
ファイル システム | ext3、ext4、XFS。
ボリューム マネージャー | LVM2。 LVM は、データ ディスクでのみサポートされています。 Azure VM には、OS ディスクが 1 つだけあります。
準仮想化ストレージ デバイス | 準仮想化ドライバーによってエクスポートされたデバイスはサポートされません。
マルチ キュー ブロック IO デバイス | サポートされていません。
HP CCISS ストレージ コントローラーを使用する物理サーバー | サポートされていません。
ディレクトリ | / (ルート)、/boot、/usr、/usr/local、/var、/etc の各ディレクトリ (個別のパーティション/ファイルシステムとしてセットアップされた場合) はすべて、ソース サーバーの同じ OS ディスク上に存在する必要があります。</br></br> /boot はディスク パーティション上にあり、LVM ボリュームではないことが必要です。<br/><br/>
空き領域要件| /rootパーティションで 2GB <br/><br/> インストール フォルダーで 250MB
XFSv5 | メタデータ チェックサムなど、XFS ファイル システム上の XFSv5 機能は、モビリティ サービスのバージョン 9.10 以降でサポートされます。 xfs_info ユーティリティを使用して、パーティションの XFS スーパーブロックを確認します。 ftype が 1 に設定されている場合は、XFSv5 の機能が使用されます。

## <a name="vmdisk-management"></a>VM/ディスク管理

**アクション** | **詳細**
--- | ---
レプリケートされた VM のディスク サイズの変更 | サポートされています。
レプリケートされた VM のディスクの追加 | VM のレプリケーションを無効にし、ディスクを追加してから、レプリケーションを再び有効にします。 レプリケート VM へのディスクの追加は現在サポートされていません。

## <a name="network"></a>ネットワーク

**コンポーネント** | **サポートされています**
--- | ---
ホスト ネットワークの NIC チーミング | VMware VM でサポートされています。 <br/><br/>物理マシンのレプリケーションではサポートされません。
ホスト ネットワークの VLAN | はい。
ホスト ネットワークの IPv4 | はい。
ホスト ネットワークの IPv6 | いいえ。
ゲスト/サーバー ネットワークの NIC チーミング | いいえ。
ゲスト/サーバー ネットワークの IPv4 | はい。
ゲスト/サーバー ネットワークの IPv6 | いいえ。
ゲスト/サーバー ネットワークの静的 IP (Windows) | はい。
ゲスト/サーバー ネットワークの静的 IP (Linux) | はい。 <br/><br/>フェールバックで DHCP を使用するように VM が構成されます。
ゲスト/サーバー ネットワークのマルチ NIC | はい。


## <a name="azure-vm-network-after-failover"></a>Azure VM ネットワーク (フェールオーバー後)

**コンポーネント** | **サポートされています**
--- | ---
Azure ExpressRoute | [はい]
ILB | [はい]
ELB | [はい]
Azure の Traffic Manager | [はい]
マルチ NIC | [はい]
予約済み IP アドレス | [はい]
IPv4 | [はい]
送信元 IP アドレスを保持する | [はい]
Azure Virtual Network サービス エンドポイント<br/> (Azure Storage ファイアウォールなし) | [はい]
高速ネットワーク | いいえ 

## <a name="storage"></a>Storage
**コンポーネント** | **サポートされています**
--- | ---
ホスト NFS | VMware = はい<br/><br/> 物理サーバー = いいえ
ホスト SAN (iSCSI/FC) | [はい]
ホスト vSAN | VMware = はい<br/><br/> 物理サーバー = 該当なし
ホスト マルチパス (MPIO) | はい - テスト環境: Microsoft DSM、EMC PowerPath 5.7 SP4、EMC PowerPath DSM for CLARiiON
ホストの仮想ボリューム (VVols) | VMware = はい<br/><br/> 物理サーバー = 該当なし
ゲスト/サーバー VMDK | [はい]
ゲスト/サーバー EFI/UEFI| ポータル (Azure for Windows Server 2012 およびそれ以降の VMware 仮想マシンへの移行のみ) </br></br> この表の下部の注意事項をご覧ください
ゲスト/サーバー共有クラスター ディスク | いいえ 
ゲスト/サーバー暗号化ディスク | いいえ 
ゲスト/サーバー NFS | いいえ 
ゲスト/サーバー SMB 3.0 | いいえ 
ゲスト/サーバー RDM | [はい]<br/><br/> 物理サーバー = 該当なし
ゲスト/サーバー ディスク > 1 TB | [はい]<br/><br/>最大 4,095 GB
4K 論理および 4K 物理セクター サイズのゲスト/サーバー ディスク | [はい]
4K 論理および 512 バイト物理セクター サイズのゲスト/サーバー ディスク | [はい]
ストライピングされたディスクのゲスト/サーバー ボリューム > 4 TB <br><br/>論理ボリューム管理 (LVM)| [はい]
ゲスト/サーバー - 記憶域スペース | いいえ 
ゲスト/サーバー ディスクのホット アド/削除 | いいえ 
ゲスト/サーバー - ディスクの除外 | [はい]
ゲスト/サーバー マルチパス (MPIO) | いいえ 

> [!NOTE]
> Windows Server 2012 以降を実行する VMware 仮想マシンの UEFI ブートは、Azure に移行できます。 次の制限事項が適用されます。

> - Azure への移行のみがサポートされています。 オンプレミスの VMware サイトへのフェールバックはサポートされていません。
> - サーバーには、OS ディスクに 4 個を超えるパーティションを持たせないでください。
> - バージョン 9.13 以降のモビリティ サービスが必要です。
> - 物理サーバーについてはサポートされません。

## <a name="azure-storage"></a>Azure Storage

**コンポーネント** | **サポートされています**
--- | ---
ローカル冗長ストレージ | [はい]
geo 冗長ストレージ | [はい]
読み取りアクセス geo 冗長ストレージ | [はい]
クール ストレージ | いいえ 
ホット ストレージ| いいえ 
ブロック blob | いいえ 
保存時の暗号化 (Storage サービスの暗号化)| [はい]
Premium Storage | [はい]
インポート/エクスポート サービス | いいえ 
ターゲット ストレージ/キャッシュ ストレージ アカウント (レプリケーション データの保存に使用) で構成された仮想ネットワークの Azure Storage ファイアウォール | いいえ 
汎用目的 V2 ストレージ アカウント (ホット層とクール層の両方) | いいえ 

## <a name="azure-compute"></a>Azure コンピューティング

**機能** | **サポートされています**
--- | ---
可用性セット | [はい]
ハブ | [はい]
マネージド ディスク | [はい]

## <a name="azure-vm-requirements"></a>Azure VM の要件

Azure にレプリケートするオンプレミス VM は、この表にまとめられている Azure VM の要件を満たしている必要があります。 Site Recovery が前提条件確認を実行するとき、満たされていない要件がある場合には確認が失敗します。

**コンポーネント** | **要件** | **詳細**
--- | --- | ---
ゲスト オペレーティング システム | レプリケートするマシンの[サポート対象のオペレーティング システム](#replicated-machines)を確認します。 | サポートされていない場合、確認は失敗します。
ゲスト オペレーティング システムのアーキテクチャ | 64 ビット。 | サポートされていない場合、確認は失敗します。
オペレーティング システムのディスク サイズ | 最大 2,048 GB。 | サポートされていない場合、確認は失敗します。
オペレーティング システムのディスク数 | 1 | サポートされていない場合、確認は失敗します。  
データ ディスク数 | 64 以下。 | サポートされていない場合、確認は失敗します。  
データ ディスク サイズ | 最大 4,095 GB | サポートされていない場合、確認は失敗します。
ネットワーク アダプター | 複数のアダプターがサポートされます。 |
共有 VHD | サポートされていません。 | サポートされていない場合、確認は失敗します。
FC ディスク | サポートされていません。 | サポートされていない場合、確認は失敗します。
BitLocker | サポートされていません。 | マシンのレプリケーションを有効にする前に、BitLocker を無効にする必要があります。 |
VM 名 | 1 から 63 文字。<br/><br/> 名前に使用できるのは、英文字、数字、およびハイフンのみです。<br/><br/> マシン名の最初と最後は、文字か数字とする必要があります。 |  Site Recovery でマシンのプロパティの値を更新します。


## <a name="vault-tasks"></a>資格情報コンテナーのタスク

**アクション** | **サポートされています**
--- | ---
リソース グループ間の資格情報コンテナーの移動<br/><br/> サブスクリプション内およびサブスクリプション間 | いいえ 
リソース グループ間でストレージ、ネットワーク、Azure VM を移動<br/><br/> サブスクリプション内およびサブスクリプション間 | いいえ 


## <a name="download-latest-azure-site-recovery-components"></a>Azure Site Recovery コンポーネントのダウンロード

**名前** | **説明** | **最新バージョンのダウンロード手順** 
--- | --- | --- | --- | ---
構成サーバー | オンプレミスの VMware サーバーと Azure の間の通信を調整します  <br/><br/> オンプレミスの VMware サーバーにインストールされます | 新規インストールについては、[こちら](vmware-azure-deploy-configuration-server.md)をクリックしてください。 最新バージョンへの既存のコンポーネントのアップグレードについては、[こちら](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server)をクリックしてください。
プロセス サーバー|構成サーバーに既定でインストールされます。 レプリケーション データを受信し、そのデータをキャッシュ、圧縮、暗号化によって最適化して、Azure Storage に送信します。 デプロイの拡大に合わせて、増大するレプリケーション トラフィックの処理を実行する独立したプロセス サーバーを追加できます。| 新規インストールについては、[こちら](vmware-azure-set-up-process-server-scale.md)をクリックしてください。 最新バージョンへの既存のコンポーネントのアップグレードについては、[こちら](vmware-azure-manage-process-server.md#upgrade-a-process-server)をクリックしてください。
モビリティ サービス | オンプレミスの VMware サーバー/物理サーバーと Azure/セカンダリ サイトの間のレプリケーションを調整します<br/><br/> レプリケートする VMware VM または物理サーバーにインストールされます | 新規インストールについては、[こちら](vmware-azure-install-mobility-service.md)をクリックしてください。 最新バージョンへの既存のコンポーネントのアップグレードについては、[こちら](vmware-azure-install-mobility-service.md#update-mobility-service)をクリックしてください。

最新の機能と修正プログラムについては、[こちら](https://aka.ms/latest_asr_updates)をクリックしてください。


## <a name="next-steps"></a>次の手順
VMware VM のディザスター リカバリーのために Azure を準備する[方法を学びます](tutorial-prepare-azure.md)。
