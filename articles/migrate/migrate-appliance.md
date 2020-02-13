---
title: Azure Migrate アプライアンス
description: サーバーの評価と移行に使用される Azure Migrate アプライアンスの概要について説明します。
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 652fe9d379d6e2ba50e9e282f384905e154368d8
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031665"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate アプライアンス

この記事では、Azure Migrate アプライアンスについて説明します。 アプライアンスは、Microsoft Azure への移行のために [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) ツールを使用してアプリ、インフラストラクチャ、およびワークロードを検出および評価する場合にデプロイします。 また、アプライアンスは、[Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-migration-tool) と[エージェントレスの移行](server-migrate-overview.md)を使用して VMware VM を Azure に移行する場合にも使用します。

## <a name="appliance-overview"></a>アプライアンスの概要

Azure Migrate アプライアンスは、次のシナリオで使用します。

**シナリオ** | **ツール** | **用途** 
--- | --- | ---
VMware VM | Azure Migrate: Server Assessment<br/><br/> Azure Migrate: Server Migration | VMware VM を検出する<br/><br/> マシンのアプリと依存関係を検出する<br/><br/> 評価のためにマシン メタデータとパフォーマンス メタデータを収集する。<br/><br/> エージェントレス移行で VMware VM を移行する。
Hyper-V VM | Azure Migrate: Server Assessment | Hyper-V VM を検出する<br/><br/> 評価のためにマシン メタデータとパフォーマンス メタデータを収集する。
物理マシン |  Azure Migrate: Server Assessment |  物理サーバーを検出する<br/><br/> 評価のためにマシン メタデータとパフォーマンス メタデータを収集する。

## <a name="appliance---vmware"></a>アプライアンス - VMware 

**要件** | **VMware** 
--- | ---
**ダウンロード形式** | .OVA 
**ダウンロード リンク** | https://aka.ms/migrate/appliance/vmware 
**ダウンロード サイズ** | 11.2 GB
**License** | ダウンロードしたアプライアンス テンプレートには、180 日間有効な Windows Server 2016 評価版ライセンスが付属します。 評価期間が期限切れ間近の場合は、新しいアプライアンスをダウンロードしてデプロイするか、アプライアンス VM のオペレーティング システムのライセンスをアクティブ化することをお勧めします。
**デプロイ** | アプライアンスは VMware VM としてデプロイします。 32 GB の RAM、8 つの vCPU、約 80 GB のディスク記憶域、外部仮想スイッチを備えた VM を割り当てるには、vCenter Server に十分なリソースが必要です。<br/> アプライアンスは、直接またはプロキシを介してインターネットにアクセスできる必要があります。<br/> アプライアンスは、1 つの vCenter Server に接続できます。
**ハードウェア** | 32 GB の RAM、8 つの vCPU、約 80 GB のディスク記憶域、外部仮想スイッチを備えた VM を割り当てるための、vCenter 上のリソース。 
**ハッシュ値** | MD5: c06ac2a2c0f870d3b274a0b7a73b78b1<br/><br/> SHA256: 4ce4faa3a78189a09a26bfa5b817c7afcf5b555eb46999c2fad9d2ebc808540c
**vCenter サーバー/ホスト** | バージョン 5.5 以降が実行されている ESXi ホストにアプライアンス VM をデプロイする必要があります。<br/><br/> 5\.5、6.0、6.5、または 6.7 を実行する vCenter Server。
**Azure Migrate プロジェクト** | 単一のプロジェクトにアプライアンスを関連付けることができます。 <br/> 任意の数のアプライアンスを 1 つのプロジェクトに関連付けることができます。<br/> 
**検出** | 1 つのアプライアンスで、vCenter Server 上の VMware VM を最大 10,000 台検出できます。<br/> 1 つのアプライアンスは、1 つの vCenter Server に接続できます。
**アプライアンスのコンポーネント** | 管理アプリ: デプロイ時のユーザー入力用のアプライアンスの Web アプリ。<br/> 検出エージェント: マシン構成データを収集します。<br/> 評価エージェント: パフォーマンス データを収集します。<br/> DRA: VM のレプリケーションを調整し、マシンと Azure 間の通信を調整します。<br/> ゲートウェイ:レプリケートされたデータを Azure に送信します。<br/> 自動更新サービス: コンポーネントを更新します (24 時間ごとに実行されます)。
**VDDK (エージェントレス移行)** | Azure Migrate Server Migration を使用してエージェントレス移行を実行する場合は、VMware vSphere VDDK がアプライアンス VM にインストールされている必要があります。


## <a name="appliance---hyper-v"></a>アプライアンス - Hyper-V

**要件** | **Hyper-V** 
--- | ---
**ダウンロード形式** | zip 形式のフォルダー (VHD を含む)
**ダウンロード リンク** | https://aka.ms/migrate/appliance/hyperv 
**ダウンロード サイズ** | 10 GB
**License** | ダウンロードしたアプライアンス テンプレートには、180 日間有効な Windows Server 2016 評価版ライセンスが付属します。 評価期間が期限切れ間近の場合は、新しいアプライアンスをダウンロードしてデプロイするか、アプライアンス VM のオペレーティング システムのライセンスをアクティブ化することをお勧めします。
**アプライアンスのデプロイ**   |  アプライアンスを Hyper-V VM としてデプロイします。<br/> Azure Migrate によって提供されるアプライアンス VM は、Hyper-V VM バージョン 5.0 です。<br/> Hyper-V ホストで Windows Server 2012 R2 以降が実行されている必要があります。<br/> ホストには、アプライアンス VM に 16 GB の RAM、8 つの vCPU、約 80 GB のストレージ スペース、1 つの外部スイッチを割り当てることができる十分な領域が必要です。<br/> アプライアンスには、静的または動的 IP アドレス、およびインターネット アクセスが必要です。
**ハードウェア** | アプライアンス VM 用の 16 GB の RAM、8 つの vCPU、約 80 GB の記憶域スペース、外部スイッチを割り当てるための、Hyper-V ホスト上のリソース。
**ハッシュ値** | MD5: 29a7531f32bcf69f32d964fa5ae950bc<br/><br/> SHA256: 37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31
**Hyper-V ホスト** | Windows Server 2012 R2 以降を実行します。
**Azure Migrate プロジェクト** | 単一のプロジェクトにアプライアンスを関連付けることができます。 <br/> 任意の数のアプライアンスを 1 つのプロジェクトに関連付けることができます。<br/> 
**検出** | 1 つのアプライアンスで、vCenter Server 上の VMware VM を最大 5,000 台検出できます。<br/> アプライアンスは、最大 300 個の Hyper-V ホストに接続できます。
**アプライアンスのコンポーネント** | 管理アプリ: デプロイ時のユーザー入力用のアプライアンスの Web アプリ。<br/> 検出エージェント: マシン構成データを収集します。<br/> 評価エージェント: パフォーマンス データを収集します。<br/>  自動更新サービス: コンポーネントを更新します (24 時間ごとに実行されます)。


## <a name="appliance---physical"></a>アプライアンス - 物理

**要件** | **物理** 
--- | ---
**ダウンロード形式** | zip 形式のフォルダー (PowerShell ベースのインストーラ スクリプトを含む)
**ダウンロード リンク** | [ダウンロード リンク](https://go.microsoft.com/fwlink/?linkid=2105112)
**ダウンロード サイズ** | 59.7 MB
**ハードウェア** | 専用の物理マシン、または仮想マシンを使用します。 アプライアンスを実行するマシンには、16 GB の RAM、8 つの vCPU、約 80 GB の記憶域スペース、および外部スイッチが必要です。<br/> アプライアンスには、静的または動的 IP アドレス、およびインターネット アクセスが必要です。
**ハッシュ値** | MD5: 1e92ede3e87c03bd148e56a708cdd33f<br/><br/> SHA256: a3fa78edc8ff8aff9ab5ae66be1b64e66de7b9f475b6542beef114b20bfdac3c
**オペレーティング システム** | アプライアンス マシンでは、Windows Server 2016 が実行されている必要があります。 
**アプライアンスのデプロイ**   |  アプライアンス インストーラー スクリプトは、ポータルからダウンロードします (zip 形式のフォルダー)。 <br/> フォルダーを解凍し、PowerShell スクリプト (AzureMigrateInstaller.ps1) を実行します。
**検出** | 1 つのアプライアンスで最大 250 台の物理サーバーを検出できます。
**アプライアンスのコンポーネント** | 管理アプリ: デプロイ時のユーザー入力用のアプライアンスの Web アプリ。<br/> 検出エージェント: マシン構成データを収集します。<br/> 評価エージェント: パフォーマンス データを収集します。<br/>  自動更新サービス: コンポーネントを更新します (24 時間ごとに実行されます)。


## <a name="url-access"></a>URL アクセス

Azure Migrate アプライアンスには、インターネットへの接続が必要です。

- アプライアンスをデプロイすると、下の表にまとめた URL への接続チェックが Azure Migrate によって実行されます。
- URL ベースのプロキシを使用してインターネットに接続している場合は、それらの URL へのアクセスを許可して、URL の探索中に受信されたすべての CNAME レコードがプロキシによって解決されるようにします。

**[URL]** | **詳細**  
--- | --- |
*.portal.azure.com  | Azure Portal に移動します。
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Azure サブスクリプションにサインインします。
*.microsoftonline.com <br/> *.microsoftonline-p.com | アプライアンスが Azure Migrate と通信するための Active Directory アプリを作成します。
management.azure.com | アプライアンスが Azure Migrate サービスと通信するための Active Directory アプリを作成します。
dc.services.visualstudio.com | 内部監視に使用するアプリ ログをアップロードします。
*.vault.azure.net | Azure Key Vault でシークレットを管理します。
aka.ms/* | aka リンクへのアクセスを許可します。 Azure Migrate アプライアンスの更新に使用されます。
download.microsoft.com/download | Microsoft ダウンロードからのダウンロードを許可します。
*.servicebus.windows.net | アプライアンスと Azure Migrate サービスの間の通信。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Azure Migrate サービスの URL に接続します。
*.hypervrecoverymanager.windowsazure.com | **VMware のエージェントレス移行のために使用**<br/><br/> Azure Migrate サービスの URL に接続します。
*.blob.core.windows.net |  **VMware のエージェントレス移行のために使用**<br/><br/>移行のためにストレージにデータをアップロードします。




## <a name="collected-data---vmware"></a>収集されるデータ - VMware

### <a name="collected-performance-data-vmware"></a>収集対象のパフォーマンス データ - VMware

アプライアンスが収集して Azure に送信する VMware VM のパフォーマンス データを次に示します。

**データ** | **カウンター** | **評価の影響**
--- | --- | ---
CPU 使用率 | cpu.usage.average | 推奨される VM サイズ/コスト
メモリ使用率 | mem.usage.average | 推奨される VM サイズ/コスト
ディスク読み取りのスループット (MB/秒) | virtualDisk.read.average | ディスク サイズ、ストレージ コスト、VM サイズの計算
ディスク書き込みのスループット (MB/秒) | virtualDisk.write.average | ディスク サイズ、ストレージ コスト、VM サイズの計算
1 秒あたりのディスク読み取り操作 | virtualDisk.numberReadAveraged.average | ディスク サイズ、ストレージ コスト、VM サイズの計算
1 秒あたりのディスク書き込み操作 | virtualDisk.numberWriteAveraged.average  | ディスク サイズ、ストレージ コスト、VM サイズの計算
NIC 読み取りのスループット (MB/秒) | net.received.average | VM サイズの計算
NIC 書き込みのスループット (MB/秒) | net.transmitted.average  |VM サイズの計算


### <a name="collected-metadata-vmware"></a>収集対象のメタデータ - VMware

> [!NOTE]
> Azure Migrate アプライアンスによって検出されたメタデータは、アプリケーションを Azure に移行し、Azure 適合性分析、アプリケーション依存関係分析、およびコスト計画を実行するときに、アプリケーションを適切なサイズにするのに役立ちます。 Microsoft では、ライセンスのコンプライアンス監査に関してこのデータを使用しません。

アプライアンスが収集して Azure に送信する VMware VM のメタデータの全一覧を次に示します。

**データ** | **カウンター**
--- | --- 
**マシンの詳細** | 
VM ID | vm.Config.InstanceUuid 
VM 名 | vm.Config.Name
vCenter Server ID | VMwareClient.Instance.Uuid
VM の説明 | vm.Summary.Config.Annotation
ライセンス プロダクト名 | vm.Client.ServiceContent.About.LicenseProductName
オペレーティング システムの種類 | vm.SummaryConfig.GuestFullName
ブートの種類 | vm.Config.Firmware
コア数 | vm.Config.Hardware.NumCPU
メモリ (MB) | vm.Config.Hardware.MemoryMB
ディスクの数 | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk).count
ディスク サイズのリスト | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
ネットワーク アダプターのリスト | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualEthernet).count
CPU 使用率 | cpu.usage.average
メモリ使用率 |mem.usage.average
**各ディスクの詳細** | 
ディスク キーの値 | disk.Key
Dikunit 番号 | disk.UnitNumber
ディスク コントローラー キーの値 | disk.ControllerKey.Value
プロビジョニングされたギガバイト数 | virtualDisk.DeviceInfo.Summary
ディスク名 | disk.UnitNumber、disk.Key、disk.ControllerKey.VAlue を使用して生成される値
1 秒あたりの読み取り操作 | virtualDisk.numberReadAveraged.average
1 秒あたりの書き込み操作 | virtualDisk.numberWriteAveraged.average
読み取りのスループット (MB/秒) | virtualDisk.read.average
書き込みのスループット (MB/秒) | virtualDisk.write.average
**各 NIC の詳細** | 
ネットワーク アダプターの名前 | nic.Key
MAC アドレス | ((VirtualEthernetCard)nic).MacAddress
IPv4 アドレス | vm.Guest.Net
IPv6 アドレス | vm.Guest.Net
読み取りのスループット (MB/秒) | net.received.average
書き込みのスループット (MB/秒) | net.transmitted.average
**インベントリ パスの詳細** | 
Name | container.GetType().Name
子オブジェクトの型 | container.ChildType
参照の詳細 | container.MoRef
親の詳細 | Container.Parent
各 VM のフォルダーの詳細 | ((Folder)container).ChildEntity.Type
各 VM のデータセンターの詳細 | ((Datacenter)container).VmFolder
各ホスト フォルダーのデータセンターの詳細 | ((Datacenter)container).HostFolder
各ホストのクラスターの詳細 | ((ClusterComputeResource)container).Host
各 VM のホストの詳細 | ((HostSystem)container).VM

## <a name="collected-data---hyper-v"></a>収集されるデータ - Hyper-V

### <a name="collected-performance-data-hyper-v"></a>収集対象のパフォーマンス データ - Hyper-V

> [!NOTE]
> Azure Migrate アプライアンスによって検出されたメタデータは、アプリケーションを Azure に移行し、Azure 適合性分析、アプリケーション依存関係分析、およびコスト計画を実行するときに、アプリケーションを適切なサイズにするのに役立ちます。 Microsoft では、ライセンスのコンプライアンス監査に関してこのデータを使用しません。

アプライアンスが収集して Azure に送信する Hyper VM のパフォーマンス データを次に示します。

**パフォーマンス カウンター クラス** | **カウンター** | **評価の影響**
--- | --- | ---
Hyper-V のハイパーバイザー仮想プロセッサ | ゲスト実行時間 (%) | 推奨される VM サイズ/コスト
Hyper-V の動的メモリ VM | 現在の負荷 (%)<br/> ゲストの可視物理メモリ (MB) | 推奨される VM サイズ/コスト
Hyper-V の仮想ストレージ デバイス | 読み取りバイト数/秒 | ディスク サイズ、ストレージ コスト、VM サイズの計算
Hyper-V の仮想ストレージ デバイス | 書き込みバイト数/秒 | ディスク サイズ、ストレージ コスト、VM サイズの計算
Hyper-V の仮想ネットワーク アダプター | 受信バイト数/秒 | VM サイズの計算
Hyper-V の仮想ネットワーク アダプター | 送信バイト数/秒 | VM サイズの計算

- CPU 使用率は、VM にアタッチされているすべての仮想プロセッサのすべての使用量の合計です。
- メモリ使用率は、(現在の負荷 * ゲストの可視物理メモリ) / 100 です。
- ディスクとネットワークの使用率の値は、一覧にある Hyper-V パフォーマンス カウンターから収集されます。

### <a name="collected-metadata-hyper-v"></a>収集対象のメタデータ - Hyper-V

アプライアンスが収集して Azure に送信する Hyper-V VM のメタデータの全一覧を次に示します。

**データ** | **WMI クラス** | **WMI クラスのプロパティ**
--- | --- | ---
**マシンの詳細** | 
BIOS _ Msvm_BIOSElement のシリアル番号 | BIOSSerialNumber
VM の種類 (Gen 1 または 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
VM の表示名 | Msvm_VirtualSystemSettingData | ElementName
VM のバージョン | Msvm_ProcessorSettingData | VirtualQuantity
メモリ (バイト) | Msvm_MemorySettingData | VirtualQuantity
VM が使用できる最大メモリ | Msvm_MemorySettingData | 制限
動的メモリの有効化 | Msvm_MemorySettingData | DynamicMemoryEnabled
オペレーティング システムの名前/バージョン/FQDN | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems 名前データ
VM の電源状態 | Msvm_ComputerSystem | EnabledState
**各ディスクの詳細** | 
ディスク識別子 | Msvm_VirtualHardDiskSettingData | VirtualDiskId
仮想ハード ディスクの種類 | Msvm_VirtualHardDiskSettingData | Type
仮想ハード ディスクのサイズ | Msvm_VirtualHardDiskSettingData | MaxInternalSize
仮想ハード ディスクの親 | Msvm_VirtualHardDiskSettingData | ParentPath
**各 NIC の詳細** | 
IP アドレス (統合 NIC) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP の有効化 (統合 NIC) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC ID (統合 NIC) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC MAC アドレス (統合 NIC) | Msvm_SyntheticEthernetPortSettingData | Address
NIC ID (レガシ NIC) | MsvmEmulatedEthernetPortSetting データ | InstanceID
NIC MAC ID (レガシ NIC) | MsvmEmulatedEthernetPortSetting データ | Address




## <a name="discovery-and-collection-process"></a>検出と収集のプロセス

アプライアンスは、次のプロセスを使用して、vCenter サーバーおよび Hyper-V ホスト/クラスターと通信します。

1. **検出を開始する**:
    - Hyper-V アプライアンスで検出を開始すると、WinRM ポート 5985 (HTTP) および 5986 (HTTPS) で Hyper-V ホストとの通信が行われます。
    - VMware アプライアンスで検出を開始すると、既定では TCP ポート 443 で vCenter サーバーとの通信が行われます。 vCenter サーバーが別のポートでリッスンしている場合は、それをアプライアンス Web アプリで構成できます。
2. **メタデータとパフォーマンス データを収集する**:
    - アプライアンスは、Common Information Model (CIM) セッションを使用して、ポート 5985 および 5986 で Hyper-V ホストから Hyper-V VM のデータを収集します。
    - アプライアンスは、既定ではポート 443 を使用して通信を行い、vCenter サーバーから VMware VM のデータを収集します。
3. **データを送信する**:アプライアンスは、SSL ポート 443 を介して、収集したデータを Azure Migrate Server Assessment と Azure Migrate Server Migration に送信します。 アプライアンスはインターネット経由で Azure に接続できます。または、ExpressRoute をパブリックまたは Microsoft ピアリングで使用できます。
    - パフォーマンス データの場合、アプライアンスはリアルタイムの使用状況データを収集します。
        - パフォーマンス データは、各パフォーマンス メトリックについて、VMware では 20 秒ごとに収集され、Hyper-V では 30 秒ごとに収集されます。
        - 収集されたデータは、10 分間に 1 つのデータ ポイントを作成するためにロールアップされます。
        - ピーク時の使用率の値は、すべての 20 または 30 秒のデータ ポイントから選択され、評価計算のために Azure に送信されます。
        - 評価のプロパティで指定されたパーセンタイル値 (50/90/95/99) に基づいて、10 分間のポイントが昇順に並べ替えられ、適切なパーセンタイル値を使用して評価が計算されます。
    - Server Migration の場合、アプライアンスは VM データの収集を開始し、それを Azure にレプリケートします。
4. **評価および移行する**:これで、Azure Migrate Server Assessment を使用して、アプライアンスによって収集されたメタデータから評価を作成できます。 また、Azure Migrate Server Migration を使用して VMware VM の移行を開始し、エージェントレスの VM レプリケーションを調整することもできます。


![Architecture](./media/migrate-appliance/architecture.png)


## <a name="appliance-upgrades"></a>アプライアンスのアップグレード

アプライアンスで実行されている Azure Migrate エージェントが更新されると、アプライアンスがアップグレードされます。

- アプライアンスでは既定で自動更新が有効になっているため、これは自動的に行われます。
- エージェントを手動で更新するために、この既定の設定を変更できます。
- 自動更新を無効にするには、レジストリ エディターで HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance にアクセスし、レジストリ キー "AutoUpdate" を 0 (DWORD) に設定します。
 
### <a name="set-agent-updates-to-manual"></a>エージェントの更新を手動に設定する

手動で更新する場合は、アプライアンスの古いエージェントごとに **[更新]** ボタンを使用して、アプライアンスのすべてのエージェントを同時に更新してください。 更新設定は、いつでも自動更新に戻すことができます。

## <a name="next-steps"></a>次のステップ

VMware 用にアプライアンスを設定する[方法を確認する](tutorial-assess-vmware.md#set-up-the-appliance-vm)。
Hyper-V 用にアプライアンスを設定する[方法を確認する](tutorial-assess-hyper-v.md#set-up-the-appliance-vm)。

