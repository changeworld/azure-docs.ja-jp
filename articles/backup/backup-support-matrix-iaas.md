---
title: Azure VM のバックアップに関する Azure Backup のサポート マトリックス
description: Azure Backup サービスを使用して Azure VM をバックアップする場合のサポート設定と制限事項について概説します。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: b99d6285942bafe5467827c30b5ba2e42094fdf3
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56431056"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Azure VM バックアップのサポート マトリックス
[Azure Backup サービス](backup-overview.md)を使用して、オンプレミスのマシンとワークロード、および Azure VM をバックアップできます。 この記事では、Azure Backup を使用して Azure 仮想マシン (VM) をバックアップする場合のサポート設定と制限事項について概説します。

その他のサポート マトリックス:

- Azure Backup の[一般的なサポート マトリックス](backup-support-matrix.md)
- Microsoft Azure Backup サーバー/DOM バックアップの[サポート マトリックス](backup-support-matrix-mabs-dpm.md)
- MARS エージェントを使用したバックアップの[サポート マトリックス](backup-support-matrix-mars-agent.md)



## <a name="supported-scenarios"></a>サポートされるシナリオ

Azure Backup サービスを使用して Azure VM をどのようにバックアップおよび復元できるかを次に示します。


**シナリオ** | **Backup** | **エージェント** |**Restore** 
--- | --- | --- | ---
**Azure VM の直接バックアップ** | VM 全体をバックアップします。  | Azure VM では、エージェントは必要ありません。 Azure Backup によって、VM で実行されている [Azure VM エージェント](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)に対して拡張機能がインストールされ、使用されます。 | 次のように復元します。<br/><br/> - **基本的な VM を作成する**。 これは、VM に複数の IP アドレスなどの特別な構成がない場合に便利です。<br/><br/> - **VM ディスクを復元する**。 ディスクを復元します。 次にそれを既存の VM にアタッチするか、PowerShell を使用してディスクから新しい VM を作成します。<br/><br/> - **VM ディスクを交換する**。 VM が存在し、マネージド ディスク (未暗号化) を使用している場合、ディスクを復元し、それを使用して VM 上の既存のディスクを交換することができます。<br/><br/> - **特定のファイル/フォルダーを復元する**。 VM 全体ではなく、VM からファイル/フォルダーを復元できます。  
**Azure VM の直接バックアップ (Windowsのみ)** | 特定のファイル/フォルダー/ボリュームをバックアップします。 | [Microsoft Azure Recovery Services (MARS) エージェント](backup-azure-file-folder-backup-faq.md)をインストールします。<br/><br/> Azure VM エージェントのバックアップ拡張機能と共に MARS エージェントを実行して、ファイル/フォルダー レベルで VM をバックアップできます。 | 特定のフォルダー/ファイルを復元します。
**バックアップ サーバーに Azure VM をバックアップする** |  ファイル/フォルダー/ボリューム、システム状態/ベア メタル ファイル、アプリ データを System Center DPM または Microsoft Azure Backup Server (MAB サーバー) にバックアップします。<br/><br/> その後、DPM/MABS はバックアップ コンテナーにバックアップします。 | VM に MABS/DPM 保護エージェントをインストールします。 MARS エージェントは DPM/MABS にインストールされます。| ファイル/フォルダー/ボリューム、システム状態/ベア メタル ファイル、アプリ データを復元します。 

バックアップ サーバーを使用したバックアップ (backup-architecture.md#architecture-back-up-to-dpmmabs)、および[サポート要件](backup-support-matrix-mabs-dpm.md)に関する詳細を確認してください。


## <a name="supported-backup-actions"></a>サポートされているバックアップ アクション

**アクション** | **サポート**
--- | ---
Windows Azure VM の作成時にバックアップを有効にする | サポート対象:Windows Server 2016 (Datacenter/Datacenter Core)、Windows Server 2012 R2 DataCenter、Windows Server 2008 R2 SP1
Linux VM の作成時にバックアップを有効にする | サポート対象:<br/><br/> - Ubuntu Server: 1710、1704、1604 (LTS)、1404 (LTS)<br/><br/> - Red Hat: RHEL 6.7、6.8、6.9、7.2、7.3、7.4<br/><br/> - SUSE Linux Enterprise Server: 11 SP4、12 SP2、12 SP3<br/><br/> - Debian: 8、9<br/><br/> - CentOS: 6.9、7.3<br/><br/> Oracle Linux: 6.7、6.8、6.9、7.2、7.3
シャットダウン状態/オフライン状態/VM をシーク中の VM をバックアップする | サポートされています。<br/><br/> スナップショットは、アプリ整合性ではなく、クラッシュ整合性のみです。
マネージド ディスクへの移行後にディスクをバックアップします。 | サポートされています。<br/><br/> バックアップは引き続き機能します。 操作は必要ありません。
リソース グループのロックを有効にした後、マネージド ディスクをバックアップする | サポートされていません。<br/><br/> バックアップは古いリソース ポイントを削除できず、復元ポイントの上限に達すると開始に失敗します。
VM のバックアップ ポリシーを変更する | サポートされています。<br/><br/> VM は、新しいポリシーのスケジュールおよびリテンション期間の設定を使用してバックアップされます。 リテンション期間の設定が延長されている場合、既存の復旧ポイントがマークされ、保持されます。 短縮されている場合、既存の復旧ポイントは次のクリーンアップ ジョブで取り除かれ、最終的に削除されます。
バックアップ ジョブを取り消す | スナップショットの処理中にサポートされます。<br/><br/> スナップショットがコンテナーに転送されているときはサポートされません。 
別のリージョンまたはサブスクリプションに VM をバックアップする |  サポートされていません。
1 日あたりのバックアップ回数 (Azure VM 拡張機能を使用した場合) | 1 日あたり 1 回のスケジュール済みバックアップ。<br/><br/> 1 日あたり最大で 4 回のオンデマンド バックアップを実行できます。
1 日あたりのバックアップ回数 (MARS エージェントを使用した場合) | 1 日あたり 3 回のスケジュール済みバックアップ。 
1 日あたりのバックアップ回数 (DPM/MABS を使用した場合) | 1 日あたり 2 回のスケジュール済みバックアップ。
毎月/毎年のバックアップ   | Azure VM 拡張機能を使用してバックアップする場合、サポートされません。 毎日および毎週のみサポートされます。<br/><br/> 毎月/毎年のリテンション期間の間、毎日/毎週のバックアップを保持するようにポリシーを設定できます。
クロックの自動調整 | サポートされていません。<br/><br/> Azure Backup では、VM をバックアップするとき、夏時間変更を考慮したクロックの自動調整は行われません。<br/><br/>  必要に応じて手動でポリシーを変更してください。
[ハイブリッド バックアップのセキュリティ機能](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |  これらの機能の無効化はサポートされていません。



## <a name="operating-system-support-windows"></a>オペレーティング システムのサポート (Windows)

Windows Azure VM をバックアップする場合にサポートされるオペレーティング システムについて次の表にまとめます。

**シナリオ** | **OS のサポート** 
--- | ---
Azure VM エージェント拡張機能を使用したバックアップ | Windows クライアント: サポートされていません<br/><br/> Windows Server: Windows Server 2008 R2 以降 
MARS エージェントを使用したバックアップ | [サポートされている](backup-support-matrix-mars-agent.md#support-for-direct-backups)オペレーティング システム。
DPM/MABS を使用したバックアップ | [MABS](backup-mabs-protection-matrix.md) および [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) を使用したバックアップでサポートされるオペレーティング システム。

## <a name="support-for-linux-backup"></a>Linux バックアップのサポート

Linux マシンをバックアップをしたい場合に何がサポートされるかを以下に示します。

**アクション** | **サポート**
--- | --- 
Linux Azure VM エージェントを使用した Linux Azure VM のバックアップ | ファイル整合性バックアップ。<br/><br/> [カスタム スクリプト](backup-azure-linux-app-consistent.md)を使用したアプリ整合性バックアップ。<br/><br/> 復元中、新しい VM を作成すること、ディスクを復元し、それを使用して VM を作成すること、またはディスクを復元し、それを使用して既存の VM 上のディスクを交換することができます。 個々のファイルとフォルダーも復元できます。
MARS エージェントを使用した Linux Azure VM のバックアップ | サポートされていません。<br/><br/> MARS エージェントをインストールできるのは Windows マシンだけです。
DPM/MABS を使用した Linux Azure VM のバックアップ | サポートされていません。

## <a name="operating-system-support-linux"></a>オペレーティング システムのサポート (Linux)

Azure VM Linux のバックアップでは、Azure Backup は、[Azure で承認されている一連の Linux ディストリビューション](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)をサポートしています。 以下の点に注意してください。

- Azure Backup は Core OS Linux をサポートしていません。
- Azure Backup は 32 ビットのオペレーティング システムをサポートしていません。
- 他の個人所有の Linux ディストリビューションは、VM 上で [Linux 用の Azure VM エージェント](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)が動作し、かつ Python がサポートされていれば使用できます。



## <a name="backup-frequency-and-retention"></a>バックアップの頻度とリテンション期間

**設定** | **制限** 
--- | --- 
保護されたインスタンス (マシン/ワークロード) あたりの最大復旧ポイント数 | 9999
復旧ポイントの最大有効期限 | 制限なし
コンテナーへのバックアップの最大頻度 (Azure VM 拡張機能) | 1 回 (1 日あたり)
コンテナーへのバックアップの最大頻度 (MARS エージェント) | 1 日あたり 3 回のバックアップ。
DPM または MABS へのバックアップの最大頻度 | SQL Server の場合は 15 分ごと<br/><br/> 他のワークロードの場合は 1 時間に 1 回。
復旧ポイントの保持期間 | 日、週、月、年。
最大保有期間 | バックアップ頻度による。
DPM または MABS ディスクの復旧ポイント数 | ファイル サーバーの場合 64 個、アプリ サーバーの場合 448 個。<br/><br/> テープの復旧ポイント数は、オンプレミス DPM に対しては無制限。


## <a name="supported-restore-methods"></a>サポートされる復元方法

**復元方法** | **詳細**
--- | ---
**新しい VM を作成する** | 復元処理中に VM を作成できます。 <br/><br/> このオプションは、基本 VM を起動し実行します。 VM の名前、リソース グループ、仮想ネットワーク、サブネット、およびストレージを指定できます。  
**ディスクを復元する** | ディスクを復元し、それを使用して VM を作成できます。<br/><br/> このオプションを選択すると、Azure Backup によって、選択したストレージ アカウントにコンテナーからデータがコピーされます。 復元ジョブによってテンプレートが生成されます。 このテンプレートをダウンロードして使用することで、VM のカスタム設定を指定して、VM を作成できます。<br/><br/> このオプションを使用すると、VM を作成するための以前のオプションよりも多くの設定を指定できます。<br/><br/>
**既存のディスクを交換する** | ディスクを復元し、その復元されたディスクを使用して、現在 VM 上にあるディスクを交換できます。
**ファイルを復元する** | 選択した復旧ポイントからファイルを復旧できます。 復旧ポイントから VM ディスクをマウントするためのスクリプトをダウンロードします。 次に、ディスク ボリュームをブラウズして復旧するファイル/フォルダーを見つけ、完了したらディスクをマウント解除します。

## <a name="support-for-file-level-restore"></a>ファイル レベルの復元のサポート

**Restore** | **サポートされています**
--- | --- 
オペレーティング システム間でファイルを復元する | バックアップ VM と同じ (または互換性のある) OS を使用する任意のマシンでファイルを復元できます。 [互換性のある OS の表](backup-azure-restore-files-from-vm.md#system-requirements)を参照してください。
クラシック VM でファイルを復元する | サポートされていません。 
暗号化された VM からファイルを復元する | サポートされていません。
ネットワーク制限付きのストレージ アカウントからファイルを復元する | サポートされていません。
Windows 記憶域スペースを使用して VM でファイルを復元する | 同じ VM での復元はサポートされていません。<br/><br/> 代わりに、互換性のある VM でファイルを復元します。
LVM/RAID アレイを使用して Linux VM でファイルを復元する | 同じ VM での復元はサポートされていません。<br/><br/> 互換性のある VM で復元を行います。
特別なネットワーク設定でファイルを復元する | 同じ VM での復元はサポートされていません。 <br/><br/> 互換性のある VM で復元を行います。

## <a name="support-for-vm-management"></a>VM 管理のサポート

VM ディスクの追加、交換など、VM 管理タスク中のバックアップのサポートについて次の表にまとめます。

**Restore** | **サポートされています** 
--- | --- 
サブスクリプション/リージョン/ゾーン間で復元する | サポートされていません 
既存の VM に復元する | ディスクの交換オプションを使用します。
Azure Storage Service Encryption (SSE) に対して有効になっているストレージ アカウントを使用してディスクを復元する | サポートされていません。<br/><br/> SSE が有効になっていないアカウントに復元します。
混合ストレージ アカウントに復元する | サポートされていません。<br/><br/> ストレージ アカウントの種類に基づいて、復元されるすべてのディスクは Premium または Standard になり、混合することはありません。 
ZRS を使用してストレージ アカウントに復元する | サポートされていません
VM を可用性セットに直接復元する | マネージド ディスクでは、ディスクを復元し、テンプレートで可用性セット オプションを使用できます。<br/><br/> アンマネージド ディスクでは、サポートされていません。 アンマネージド ディスクでは、ディスクを復元し、可用性セットで VM を作成します。
マネージドにアップグレードした後、アンマネージド VM のバックアップを復元する | サポートされています。<br/><br/> ディスクを復元し、その後、マネージド VM を作成できます。
VM がマネージド ディスクに移行されたときよりも前の復元ポイントにその VM を復元する | サポートされています。<br/><br/> アンマネージド ディスク (既定) に復元し、復元したディスクをマネージド ディスクに変換し、そのマネージド ディスクを使用して VM を作成します。
削除された VM を復元する | サポートされています。<br/><br/> 復旧ポイントから VM を復元できます。
マルチ DC 構成の一部であるドメイン コントローラー (DC) VM をポータルを通じて復元する | ディスクを復元し、PowerShell を使用して VM を作成する場合にサポートされます。
別の VNet で VM を復元する |  サポートされています。<br/><br/> VNet は同じサブスクリプションとリージョン内にある必要があります。




## <a name="vm-compute-support"></a>VM コンピューティングのサポート

**Compute** | **サポート** 
--- | --- 
VM サイズ |   少なくとも 2 つの CPU コアと 1 GB の RAM を備えた任意の Azure VM サイズ。<br/><br/> [詳細情報](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) 
[可用性セット](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets)で VM をバックアップする | サポートされています。<br/><br/> VM をすばやく作成するオプションを使用して、可用性セットで VM を復元することはできません。 代わりに、VM を復元する場合、ディスクを復元し、それを使用して VM をデプロイするか、ディスクを復元し、それを使用して既存のディスクを交換する必要があります。 
[可用性ゾーン](https://docs.microsoft.com/azure/availability-zones/az-overview)で VM をバックアップする |  サポートされていません。  
[Hybrid Use Benefit (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) を使用してデプロイされた VM をバックアップする | サポートされています。
[スケール セット](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)でデプロイされた VM をバックアップする |   サポートされていません   
[Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=virtual-machine-images) からデプロイされた VM をバックアップする<br/><br/> (Microsoft、サード パーティによって公開) |   サポートされています。<br/><br/> VM はサポートされているオペレーティング システムを実行している必要があります。<br/><br/> VM でファイルを復元する場合、(古いまたは新しい OS ではなく) 互換性のある OS に対してのみ復元できます。
カスタム イメージ (サード パーティ) からデプロイされた VM をバックアップする |    サポートされています。<br/><br/> VM はサポートされているオペレーティング システムを実行している必要があります。<br/><br/> VM でファイルを復元する場合、(古いまたは新しい OS ではなく) 互換性のある OS に対してのみ復元できます。
Azure に移行された VM をバックアップする   | サポートされています。<br/><br/> VM をバックアップするには、移行済みマシンに VM エージェントをインストールする必要があります。 



## <a name="vm-storage-support"></a>VM ストレージのサポート

**コンポーネント** | **サポート**
--- | ---
Azure VM のデータ ディスク数 | 16 個以下のデータ ディスクを備えた VM をバックアップします。
データ ディスク サイズ | ディスクごとに最大で 4,095 GB。<br/><br/> 最新バージョンの Azure VM バックアップ (別名、インスタント リストア) を実行している場合、最大 4TB のディスク サイズがサポートされます。 [詳細情報](backup-instant-restore-capability.md)。
ストレージの種類 | Standard HDD、Standard SSD、Premium SSD <br/><br/> 最新バージョンの Azure VM バックアップ (別名、インスタント リストア) を実行している場合、Standard SSD がサポートされます。 [詳細情報](backup-instant-restore-capability.md)。
マネージド ディスク | サポートされています
暗号化されたディスク | サポートされています。<br/><br/> Azure Disk Encryption (ADE) が有効になっている Azure VM を (Azure AD アプリを使用して、または使用せずに) バックアップできます。<br/><br/> 暗号化された VM は、ファイル/フォルダー レベルでは復旧できません。 VM 全体を復旧する必要があります。<br/><br/> Azure Backup によって既に保護されている VM で暗号化を有効にできます。
書き込みアクセラレータが有効になっているディスク | サポートされていません。<br/><br/> 最新バージョンの Azure VM バックアップ (別名、[インスタント リストア](backup-instant-restore-capability.md)) を実行している場合、書き込みアクセラレータが有効になっているディスクをバックアップから除外できます。
重複除去されたディスクをバックアップする | サポートされていません。
保護された VM にディスクを追加する | サポートされています。
保護された VM でディスクのサイズを変更する | サポートされています。

## <a name="vm-network-support"></a>VM ネットワークのサポート


**コンポーネント** | **サポート**
--- | ---
NIC の数 | 特定の Azure VM サイズでサポートされている NIC の最大数まで。<br/><br/> 復元処理中に VM が作成されると、NIC が作成されます。<br/><br/> 復元された VM の NIC の数は、保護を有効にしたときの VM の NIC の数を反映します。 有効にした後に NIC を削除しても、数には影響しません。
外部/内部ロード バランサー |   サポートされています。 <br/></br> 特別なネットワーク設定での VM の復元について[詳細を確認](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)してください。
複数の予約済み IP アドレス |  サポートされています。 <br/></br> 特別なネットワーク設定での VM の復元について[詳細を確認](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)してください。
複数のネットワーク アダプターを持つ VM  | サポートされています。 <br/></br> 特別なネットワーク設定での VM の復元について[詳細を確認](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)してください。
パブリック IP アドレスを持つ VM    | サポートされています。<br/><br/> 既存のパブリック IP アドレスを NIC に関連付けるか、復元後にアドレスを作成して NIC に関連付ける必要があります。
NIC/サブネットでのネットワーク セキュリティ グループ (NSG) |   サポートされています。
予約済み IP アドレス (静的) | サポートされていません。<br/><br/> 予約済み IP アドレスを持ち、エンドポイントが定義されていない VM はバックアップできません。
動的 IP アドレス |    サポートされています。<br/><br/> ソース VM の NIC が動的 IP アドレスを使用する場合、既定では、復元された VM の NIC も動的 IP アドレスを使用します。
Traffic Manager | サポートされています<br/><br/>。 バックアップされた VM が Traffic Manager にある場合、復元された VM を同じ Traffic Manager に手動で追加する必要があります。 
Azure DNS | サポートされています。
[カスタム DNS] |    サポートされています。
HTTP プロキシを介したアウトバウンド接続 | サポートされています。<br/><br/> 認証済みプロキシはサポートされていません。 
VNet サービス エンドポイント  | サポートされています。<br/><br/> ファイアウォールと VNet のストレージ アカウントの設定で、すべてのネットワークからのアクセスを許可する必要があります。 



## <a name="vm-securityencryption-support"></a>VM のセキュリティ/暗号化のサポート

Azure Backup では、転送中と保存時のデータの暗号化をサポートしています。

Azure へのネットワーク トラフィック:
- サーバーから Recovery Services コンテナーへのバックアップ トラフィックは、Advanced Encryption Standard 256 を使用して暗号化されます。
- バックアップ データは、セキュリティで保護された HTTPS リンク経由で送信されます。
- バックアップ データは、暗号化された形式で Recovery Services コンテナーに格納されます。
- このデータのロックを解除するパスフレーズを持っているのはお客様だけです。 Microsoft は、どの時点でも、バックアップ データの暗号化を解除できません。
    > [!WARNING]
    > コンテナーの設定後、お客様だけが暗号化キーにアクセスできます。 Microsoft がコピーを保持することはなく、キーにアクセスすることもできません。 キーを紛失した場合、Microsoft はバックアップ データを復旧できません。
データのセキュリティ:
- Azure VM のバックアップの場合は、仮想マシン "*内*" で暗号化を設定する必要があります。
- Azure Backup は Azure Disk Encryption をサポートしており、Windows 仮想マシンでは BitLocker が、Linux 仮想マシンでは **dm-crypt** が使用されます。
- Azure Backup のバックエンドでは [Azure Storage Service Encryption](../storage/common/storage-service-encryption.md) が使用されており、これによって保存データが保護されます。


**マシン** | **転送中** | **保存時**
--- | --- | ---
オンプレミスの Windows マシン (DPM または MABS なし) | ![はい][green] | ![はい][green]
Azure VM | ![はい][green] | ![はい][green] 
オンプレミス VM または Azure VM (DPM あり) | ![はい][green] | ![はい][green]
オンプレミス VM または Azure VM (MABS あり) | ![はい][green] | ![はい][green]



## <a name="vm-compression-support"></a>VM の圧縮のサポート

Backup では、次の表にまとめたように、バックアップ トラフィックの圧縮をサポートしています。 以下の点に注意してください。

- Azure VM では、ストレージ ネットワーク経由で Azure ストレージ アカウントから直接データが VM 拡張機能によって読み取られます。そのため、このトラフィックを圧縮する必要はありません。
- DPM または MABS を使用する場合は、帯域幅を節約するために、データを DPM または MABS にバックアップする前に圧縮できます。 

**マシン** | **MABS または DPM に圧縮 (TCP)** | **コンテナーに圧縮 (HTTPS)**
--- | --- | ---
オンプレミスの Windows マシン (DPM または MABS なし) | NA | はい
Azure VM | NA | NA
オンプレミス VM または Azure VM (DPM あり) | ![はい][green] | ![はい][green]
オンプレミス VM または Azure VM (MABS あり) | ![はい][green] | ![はい][green]




## <a name="next-steps"></a>次の手順

- [Azure VM のバックアップ](backup-azure-arm-vms-prepare.md)
- バックアップ サーバーなしで、[Windows マシンを直接バックアップ](tutorial-backup-windows-server-to-azure.md)します。
- Azure へのバックアップのために [MABS を設定](backup-azure-microsoft-azure-backup.md)してから、MABS にワークロードをバックアップします。
- Azure へのバックアップのために [DPM を設定](backup-azure-dpm-introduction.md)してから、DPM にワークロードをバックアップします。

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png

