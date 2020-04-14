---
title: Azure Migrate アプライアンス
description: サーバーの評価と移行に使用される Azure Migrate アプライアンスの概要について説明します。
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: bccf4738d46b65f2d149eafc8e69591141d7d073
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437585"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate アプライアンス

この記事では、Azure Migrate アプライアンスの前提条件とサポート要件の概要を説明します。 

## <a name="deployment-scenarios"></a>デプロイメント シナリオ

Azure Migrate アプライアンスは、次のシナリオで使用します。

**シナリオ** | **ツール** | **用途** 
--- | --- | ---
**VMware VM の評価** | Azure Migrate:Server Assessment | VMware VM を検出する<br/><br/> マシンのアプリと依存関係を検出する<br/><br/> 評価のためにマシン メタデータとパフォーマンス メタデータを収集する。
**VMware VM のエージェントレス移行** | Azure Migrate:Server Migration | VMware VM を検出する <br/><br/> エージェントレス移行で VMware VM を移行する。
**Hyper-V VM の評価** | Azure Migrate:Server Assessment | Hyper-V VM を検出する<br/><br/> 評価のためにマシン メタデータとパフォーマンス メタデータを収集する。
**物理マシンの評価** |  Azure Migrate:Server Assessment |  物理サーバー (または物理サーバーとして扱う VM) を検出します。<br/><br/> 評価のためにマシン メタデータとパフォーマンス メタデータを収集する。

## <a name="appliance---vmware"></a>アプライアンス - VMware 

次の表は、VMware の Azure Migrate アプライアンス要件をまとめたものです。

**要件** | **VMware** 
--- | ---
**アプライアンスのコンポーネント** | アプライアンスには、次のコンポーネントがあります。<br/><br/> - **管理アプリ**:これは、アプライアンスのデプロイ時にユーザーが入力する Web アプリです。 Azure への移行用にマシンを評価するときに使用されます。<br/> - **検出エージェント**:このエージェントは、マシン構成データを収集します。 Azure への移行用にマシンを評価するときに使用されます。<br/>- **評価エージェント**:このエージェントはパフォーマンス データを収集します。 Azure への移行用にマシンを評価するときに使用されます。<br/>- **自動更新サービス**:アプライアンス コンポーネントを更新します (24 時間ごとに実行されます)。<br/>- **DRA エージェント**:VM のレプリケーションを調整し、レプリケートされたマシンと Azure 間の通信を調整します。 エージェントレスの移行を使用して VMware VM を Azure にレプリケートする場合にのみ使用されます。<br/>- **ゲートウェイ**:レプリケートされたデータを Azure に送信します。 エージェントレスの移行を使用して VMware VM を Azure にレプリケートする場合にのみ使用されます。
**サポートされるデプロイ** | OVA テンプレートを使用して VMware VM としてデプロイする。<br/><br/> PowerShell インストール スクリプトを使用して、VMware VM または物理マシンとしてデプロイする。
**プロジェクトのサポート** |  単一のプロジェクトにアプライアンスを関連付けることができます。 <br/> 任意の数のアプライアンスを 1 つのプロジェクトに関連付けることができます。<br/> 
**検出の制限** | 1 つのアプライアンスで、vCenter Server 上の VMware VM を最大 10,000 台検出できます。<br/> 1 つのアプライアンスは、1 つの vCenter Server に接続できます。
**OVA テンプレート** | ポータルから、または https://aka.ms/migrate/appliance/vmware からダウンロードします。<br/><br/> ダウンロード サイズは 11.2 GB です。<br/><br/> ダウンロードしたアプライアンス テンプレートには、180 日間有効な Windows Server 2016 評価版ライセンスが付属します。 評価期間が期限切れ間近の場合は、新しいアプライアンスをダウンロードしてデプロイするか、アプライアンス VM のオペレーティング システムのライセンスをアクティブ化することをお勧めします。
**PowerShell スクリプト** | スクリプト [ダウンロード](https://go.microsoft.com/fwlink/?linkid=2105112)。<br/><br/> 
**ソフトウェア/ハードウェア** |  アプライアンスは、Windows Server 2016、32 GB の RAM、8 つの vCPU、約 80 GB のディスク記憶域、外部仮想スイッチを搭載したコンピューターで実行する必要があります。<br/> アプライアンスは、直接またはプロキシを介してインターネットにアクセスできる必要があります。<br/><br/> アプライアンスを VMware VM で実行する場合、vCenter Server に、要件を満たす VM を割り当てるのに十分なリソースが必要です。<br/><br/> 物理マシンでアプライアンスを実行する場合は、Windows Server 2016 を実行しており、ハードウェアの要件を満たしていることを確認します。 
**VMware の要件** | アプライアンスを VMware VM としてデプロイする場合は、バージョン 5.5 以降を実行している ESXi ホストにデプロイする必要があります。<br/><br/> 5\.5、6.0、6.5、または 6.7 を実行する vCenter Server。
**VDDK (エージェントレス移行)** | アプライアンスを VMware VM としてデプロイし、エージェントレスの移行を実行している場合は、VMware vSphere VDDK をアプライアンス VM にインストールする必要があります。
**ハッシュ値 - OVA** | OVA テンプレートのハッシュ値を[確認](tutorial-assess-vmware.md#verify-security)します。
**ハッシュ値 - PowerShell スクリプト** | PowerShell スクリプトのハッシュ値を[確認](deploy-appliance-script.md#verify-file-security)します。




## <a name="appliance---hyper-v"></a>アプライアンス - Hyper-V

**要件** | **Hyper-V** 
--- | ---
**アプライアンスのコンポーネント** | アプライアンスには、次のコンポーネントがあります。<br/><br/>- **管理アプリ**:これは、アプライアンスのデプロイ時にユーザーが入力する Web アプリです。 Azure への移行用にマシンを評価するときに使用されます。<br/> - **検出エージェント**:このエージェントは、マシン構成データを収集します。 Azure への移行用にマシンを評価するときに使用されます。<br/>- **評価エージェント**:このエージェントはパフォーマンス データを収集します。 Azure への移行用にマシンを評価するときに使用されます。<br/>- **自動更新サービス**:アプライアンス コンポーネントを更新します (24 時間ごとに実行されます)。
**サポートされるデプロイ** | VHD テンプレートを使用して Hyper-V VM としてデプロイする。<br/><br/> PowerShell インストール スクリプトを使用して、Hyper-V VM または物理マシンとしてデプロイする。
**プロジェクトのサポート** |  単一のプロジェクトにアプライアンスを関連付けることができます。 <br/> 任意の数のアプライアンスを 1 つのプロジェクトに関連付けることができます。<br/> 
**検出の制限** | アプライアンスは、最大 5000 個の Hyper-V ホストを検出できます。<br/> アプライアンスは、最大 300 個の Hyper-V ホストに接続できます。
**VHD テンプレート** | VHD を含む zip 形式のフォルダー。 ポータルから、または https://aka.ms/migrate/appliance/hyperv からダウンロードします。<br/><br/> ダウンロード サイズは 10 GB です。<br/><br/> ダウンロードしたアプライアンス テンプレートには、180 日間有効な Windows Server 2016 評価版ライセンスが付属します。 評価期間が期限切れ間近の場合は、新しいアプライアンスをダウンロードしてデプロイするか、アプライアンス VM のオペレーティング システムのライセンスをアクティブ化することをお勧めします。
**PowerShell スクリプト** | スクリプト [ダウンロード](https://go.microsoft.com/fwlink/?linkid=2105112)。<br/><br/> 
**ソフトウェア/ハードウェア***   |  アプライアンスは、Windows Server 2016、32 GB の RAM、8 つの vCPU、約 80 GB のディスク記憶域、外部仮想スイッチを搭載したコンピューターで実行する必要があります。<br/> アプライアンスには、静的または動的 IP アドレスが必要であり、直接またはプロキシを介したインターネット アクセスが必要です。<br/><br/> アプライアンスを Hyper-V VM として実行する場合、Hyper-V ホスト上に、アプライアンス VM 用の 16 GB の RAM、8 つの vCPU、約 80 GB の記憶域スペース、外部スイッチを割り当てるための十分なリソースが必要です。<br/><br/> 物理マシンでアプライアンスを実行する場合は、Windows Server 2016 を実行しており、ハードウェアの要件を満たしていることを確認します。 
**Hyper-V の要件** | VHD テンプレートを使用してアプライアンスをデプロイする場合、Azure Migrate によって提供されるアプライアンス VM は、Hyper-V VM バージョン 5.0 です。<br/><br/> Hyper-V ホストで Windows Server 2012 R2 以降が実行されている必要があります。 
**ハッシュ値 - VHD** | VHD テンプレートのハッシュ値を[確認](tutorial-assess-hyper-v.md#verify-security)します。
**ハッシュ値 - PowerShell スクリプト** | PowerShell スクリプトのハッシュ値を[確認](deploy-appliance-script.md#verify-file-security)します。


## <a name="appliance---physical"></a>アプライアンス - 物理

**要件** | **物理** 
--- | ---
**アプライアンスのコンポーネント** | アプライアンスには、次のコンポーネントがあります。 <br/><br/> - **管理アプリ**:これは、アプライアンスのデプロイ時にユーザーが入力する Web アプリです。 Azure への移行用にマシンを評価するときに使用されます。<br/> - **検出エージェント**:このエージェントは、マシン構成データを収集します。 Azure への移行用にマシンを評価するときに使用されます。<br/>- **評価エージェント**:このエージェントはパフォーマンス データを収集します。 Azure への移行用にマシンを評価するときに使用されます。<br/>- **自動更新サービス**:アプライアンス コンポーネントを更新します (24 時間ごとに実行されます)。
**サポートされるデプロイ** | PowerShell インストール スクリプトを使用して、専用物理マシンまたは VM としてデプロイする。
**プロジェクトのサポート** |  単一のプロジェクトにアプライアンスを関連付けることができます。 <br/> 任意の数のアプライアンスを 1 つのプロジェクトに関連付けることができます。<br/> 
**検出の制限** | 1 つのアプライアンスで最大 250 台の物理サーバーを検出できます。
**PowerShell スクリプト** | ポータルから zip 形式のフォルダーでスクリプト (AzureMigrateInstaller.ps1) をダウンロードします。 [詳細については、こちらを参照してください](tutorial-assess-physical.md#set-up-the-appliance)。 または、[直接ダウンロード](https://go.microsoft.com/fwlink/?linkid=2105112)します。<br/><br/> ダウンロード サイズは 59.7 MB です。
**ソフトウェア/ハードウェア** |  アプライアンスは、Windows Server 2016、32 GB の RAM、8 つの vCPU、約 80 GB のディスク記憶域、外部仮想スイッチを搭載したコンピューターで実行する必要があります。<br/> アプライアンスには、静的または動的 IP アドレスが必要であり、直接またはプロキシを介したインターネット アクセスが必要です。<br/><br/> 物理マシンでアプライアンスを実行する場合は、Windows Server 2016 を実行しており、ハードウェアの要件を満たしていることを確認します。 
**ハッシュ値** | PowerShell スクリプトのハッシュ値を[確認](deploy-appliance-script.md#verify-file-security)します。

## <a name="url-access"></a>URL アクセス

Azure Migrate アプライアンスには、インターネットへの接続が必要です。

- アプライアンスをデプロイすると、下の表にまとめた URL への接続チェックが Azure Migrate によって実行されます。
- URL ベースのプロキシを使用してインターネットに接続している場合は、それらの URL へのアクセスを許可して、URL の探索中に受信されたすべての CNAME レコードがプロキシによって解決されるようにする必要があります。

**URL** | **詳細**  
--- | --- |
*.portal.azure.com  | Azure Portal に移動します。
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com | Azure サブスクリプションにサインインします。
*.microsoftonline.com <br/> *.microsoftonline-p.com | アプライアンスで Azure Migrate と通信するための Azure Active Directory (AD) アプリを作成します。
management.azure.com | アプライアンスで Azure Migrate サービスと通信するための Azure AD アプリを作成します。
dc.services.visualstudio.com | 内部監視に使用するアプリ ログをアップロードします。
*.vault.azure.net | Azure Key Vault でシークレットを管理します。
aka.ms/* | aka リンクへのアクセスを許可します。 Azure Migrate アプライアンスの更新に使用されます。
download.microsoft.com/download | Microsoft ダウンロードからのダウンロードを許可します。
*.servicebus.windows.net | アプライアンスと Azure Migrate サービスの間の通信。
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Azure Migrate サービスの URL に接続します。
*.hypervrecoverymanager.windowsazure.com | **VMware のエージェントレス移行のために使用**<br/><br/> Azure Migrate サービスの URL に接続します。
*.blob.core.windows.net |  **VMware のエージェントレス移行のために使用**<br/><br/>移行のためにストレージにデータをアップロードします。




## <a name="collected-data---vmware"></a>収集されるデータ - VMware

アプライアンスでは、メタ―データ、パフォーマンス データ、依存関係分析データ (エージェントレスの[依存関係分析](concepts-dependency-visualization.md)が使用されている場合) が収集されます。

### <a name="metadata"></a>Metadata

Azure Migrate アプライアンスによって検出されたメタデータは、コンピューターとアプリを Azure に移行する準備ができているかどうかを判断し、コンピューターとアプリを適切にサイズ設定して、コストを計画し、アプリケーションの依存関係を分析するために役立ちます。 Microsoft では、ライセンスのコンプライアンス監査でこのデータを使用しません。

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
名前 | container.GetType().Name
子オブジェクトの型 | container.ChildType
参照の詳細 | container.MoRef
親の詳細 | Container.Parent
各 VM のフォルダーの詳細 | ((Folder)container).ChildEntity.Type
各 VM のデータセンターの詳細 | ((Datacenter)container).VmFolder
各ホスト フォルダーのデータセンターの詳細 | ((Datacenter)container).HostFolder
各ホストのクラスターの詳細 | ((ClusterComputeResource)container).Host
各 VM のホストの詳細 | ((HostSystem)container).VM

### <a name="performance-data"></a>パフォーマンス データ


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

### <a name="app-dependencies-metadata"></a>アプリの依存関係メタデータ

エージェントレスの依存関係分析では、接続データとプロセス データを収集します。

#### <a name="connection-data"></a>接続データ

エージェントレスの依存関係分析が有効になっている各 VM から、アプライアンスが収集する接続データを次に示します。 このデータは Azure に送信されます。

**データ** | **使用されるコマンド** 
--- | --- 
ローカル ポート | netstat
ローカル IP アドレス | netstat
リモート ポート | netstat
リモート IP アドレス | netstat
TCP 接続の状態 | netstat
プロセス ID | netstat
いいえ。 アクティブな接続の | netstat

#### <a name="process-data"></a>データを処理する
エージェントレスの依存関係分析が有効になっている各 VM から、アプライアンスが収集するプロセス データを次に示します。 このデータは Azure に送信されます。

**データ** | **WMI クラス** | **WMI クラスのプロパティ**
--- | --- | ---
[処理名] | Win32_Process | ExecutablePath
プロセスの引数 | Win32_Process | CommandLine
アプリケーション名 | Win32_Process | ExecutablePath プロパティの VersionInfo.ProductName パラメーター

#### <a name="linux-vm-data"></a>Linux VM データ

エージェントレスの依存関係分析が有効になっている各 Linux VM から、アプライアンスが収集する接続データとプロセス データを次に示します。 このデータは Azure に送信されます。

**データ** | **使用されるコマンド** 
--- | ---
ローカル ポート | netstat 
ローカル IP アドレス | netstat 
リモート ポート | netstat 
リモート IP アドレス | netstat 
TCP 接続の状態 | netstat 
いいえ。 アクティブな接続の | netstat
プロセス ID  | netstat 
[処理名] | ps
プロセスの引数 | ps
アプリケーション名 | dpkg または rpm



## <a name="collected-data---hyper-v"></a>収集されるデータ - Hyper-V

アプライアンスでは、メタ―データ、パフォーマンス データ、依存関係分析データ (エージェントレスの[依存関係分析](concepts-dependency-visualization.md)が使用されている場合) が収集されます。

### <a name="metadata"></a>Metadata
Azure Migrate アプライアンスによって検出されたメタデータは、コンピューターとアプリを Azure に移行する準備ができているかどうかを判断し、コンピューターとアプリを適切にサイズ設定して、コストを計画し、アプリケーションの依存関係を分析するために役立ちます。 Microsoft では、ライセンスのコンプライアンス監査でこのデータを使用しません。

アプライアンスが収集して Azure に送信する Hyper-V VM のメタデータの全一覧を次に示します。

**データ* | **WMI クラス** | **WMI クラスのプロパティ**
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

### <a name="performance-data"></a>パフォーマンス データ

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

## <a name="appliance-upgrades"></a>アプライアンスのアップグレード

アプライアンスで実行されている Azure Migrate エージェントが更新されると、アプライアンスがアップグレードされます。 アプライアンスでは既定で自動更新が有効になっているため、これは自動的に行われます。 エージェントを手動で更新するために、この既定の設定を変更できます。

- **自動更新をオフにする**:レジストリで自動更新をオフにするには、HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance の "AutoUpdate" キーを 0 (ダブルワード) に設定します。 手動更新を使用する場合は、アプライアンスの古いエージェントごとに **[更新]** ボタンを使用して、アプライアンスのすべてのエージェントを同時に更新することが重要です。
- **手動で更新する**:手動で更新する場合は、アプライアンスの古いエージェントごとに **[更新]** ボタンを使用して、アプライアンスのすべてのエージェントを更新してください。 更新設定は、いつでも自動更新に戻すことができます。

![アプライアンスを自動的に更新する](./media/migrate-appliance/autoupdate.png)

## <a name="next-steps"></a>次のステップ

- VMware 用にアプライアンスを設定する[方法を確認する](how-to-set-up-appliance-vmware.md)。
- Hyper-V 用にアプライアンスを設定する[方法を確認する](how-to-set-up-appliance-hyper-v.md)。
- 物理サーバーのためのアプライアンスを設定する[方法を確認](how-to-set-up-appliance-physical.md)します。

