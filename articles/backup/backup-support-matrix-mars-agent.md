---
title: Azure Backup を使用した Microsoft Azure Recovery Services (MARS) エージェントを実行しているコンピューターのバックアップのサポート マトリックス
description: この記事では、Microsoft Azure Recovery Services (MARS) エージェントを実行しているコンピューターをバックアップする場合の Azure Backup のサポートを要約しています。
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 9d0f751e8d0bc0275cc5fd2c47aaba7a6058931c
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430936"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Microsoft Azure Recovery Services (MARS) エージェントを使用したバックアップのサポート マトリックス

[Azure Backup サービス](backup-overview.md)を使用すると、オンプレミスのコンピューター、アプリ、および Azure VM をバックアップできます。 この記事では、Microsoft Azure Recovery Services (MARS) エージェントを使用してコンピューターをバックアップするためのサポートの設定および制限を要約しています。

## <a name="about-the-mars-agent"></a>MARS エージェントについて

MARS エージェントは、オンプレミスのコンピューターや Azure VM のデータを Azure の Recovery Services バックアップ コンテナーにバックアップするために Azure Backup によって使用されます。 MARS エージェントは、次のように使用できます。
- Azure の Recovery Services バックアップ コンテナーに直接バックアップできるように、オンプレミスの Windows コンピューター上でエージェントを実行します。
- コンテナーに直接バックアップできるように、Windows Azure VM 上でエージェントを実行します。
- Microsoft Azure Backup Server (MABS) または System Center Data Protection Manager (DPM) サーバー上でエージェントを実行します。 このシナリオでは、コンピューターとワークロードを MABS/DPM にバックアップした後、MARS エージェントを使用して MABS/DPM が Azure のコンテナーにバックアップされます。 

バックアップできる内容は、エージェントがインストールされている場所によって異なります。

- MARS エージェントを使用したバックアップ アーキテクチャについては、[ここ](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders)を参照してください。
- MABS/DPM の[バックアップ アーキテクチャ](backup-architecture.md#architecture-back-up-to-dpmmabs)と[要件](backup-support-matrix-mabs-dpm.md)については、[ここ]()を参照してください。


## <a name="supported-installation"></a>サポートされるインストール

**インストール** | **詳細**
--- | ---
**最新の MARS エージェントをダウンロードする** | 最新バージョンのエージェントはコンテナーからダウンロードするか、または[直接ダウンロード](https://aka.ms/azurebackup_agent)できます。
**コンピューターに直接インストールする** | MARS エージェントは、オンプレミスの Windows サーバー、またはいずれかの[サポートされるオペレーティング システム]()を実行している Windows Azure VM に直接インストールできます。
**バックアップ サーバーにインストールする** | Azure にバックアップするように DPM または MABS を設定する場合は、そのサーバーに MARS エージェントをダウンロードしてインストールします。 このエージェントは、バックアップ サーバーのサポート マトリックス内の[サポートされるオペレーティング システム](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)に従ってインストールできます。

> [!NOTE]
> 既定では、バックアップが有効になっている Azure VM には Azure Backup 拡張機能がインストールされています。 この拡張機能は、VM 全体をバックアップします。 VM 全体ではなく、特定のフォルダーやファイルをバックアップする場合は、この拡張機能と共に MARS エージェントを Azure VM にインストールして実行できます。
> MARS エージェントは、Azure VM 上で実行されると、その VM 上の一時ストレージにあるファイルやフォルダーをバックアップします。 それらのファイルやフォルダーが一時ストレージから削除されたり、その一時ストレージが削除されたりすると、バックアップは失敗します。


## <a name="cache-folder-support"></a>キャッシュ フォルダーのサポート

MARS エージェントでバックアップする場合、このエージェントはデータのスナップショットを取得し、それを Azure に送信する前にローカル キャッシュ フォルダーに格納します。 キャッシュ (スクラッチ) フォルダーには、いくつかの要件があります。

**キャッシュ** | **詳細**
--- | ---
**キャッシュ サイズ** |  キャッシュ フォルダー サイズの空き領域は、バックアップ データの全体のサイズの少なくとも 5 ～ 10% である必要があります。 
**キャッシュの場所** | キャッシュ フォルダーは、バックアップされるコンピューターに対してローカルであり、かつオンラインである必要があります。<br/><br/> キャッシュ フォルダーがネットワーク共有、リムーバブル メディア、またはオフライン ボリュームに配置されていてはいけません。 
**キャッシュ フォルダー** | キャッシュ フォルダーは、重複除去されたボリューム上、または圧縮済み/スパース/再解析ポイントであるフォルダー上で暗号化されている必要があります。
**キャッシュの場所の変更** | キャッシュの場所は、バックアップ エンジンを停止し (net stop bengine)、キャッシュ フォルダーを新しいドライブ (十分な領域があることを確認してください) にコピーすることによって変更できます。 その後、HKLM\SOFTWARE\Microsoft\Windows Azure Backup にある 2 つのレジストリ エントリ (Config/ScratchLocation と Config/CloudBackupProvider/ScratchLocation) を新しい場所に更新し、エンジンを再起動します。

## <a name="networking-and-access-support"></a>ネットワークとアクセスのサポート

### <a name="url-access"></a>URL アクセス

MARS エージェントには、次の URL へのアクセス権が必要です。

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net

### <a name="throttling-support"></a>調整のサポート

**機能** | **詳細**
--- | ---
帯域幅の制御 | サポートされています<br/><br/> 帯域幅を調整するには、MARS エージェントで **[プロパティの変更]** を使用します。
Network throttling | Windows Server 2008 R2、Windows Server 2008 SP2、または Windows 7 を実行しているバックアップ済みのコンピューターには使用できません。

## <a name="support-for-direct-backups"></a>直接バックアップのサポート

次の表は、オンプレミスのコンピューターや Azure VM 上で実行されているどのオペレーティング システムを MARS エージェントで Azure に直接バックアップできるかをまとめたものです。

- すべてのオペレーティング システムが 64 ビットです。
- すべてのオペレーティング システムで最新の Service Pack および更新プログラムが実行されている必要があります。
- MARS エージェントでどの DPM および MABS サーバーをバックアップできるかの詳細については、[この表](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)を確認してください。

**オペレーティング システム** | **ファイル/フォルダー** | **システム状態**
--- | --- | ---
Windows 10 (Enterprise、Pro、Home) | はい | いいえ 
Windows 8.1 (Enterprise、Pro)| はい |いいえ 
Windows 8 (Enterprise、Pro) | はい | いいえ 
Windows 7 (Ultimate、Enterprise、Pro、Home Premium/Basic、Starter) | はい | いいえ 
Windows Server 2016 (Standard、Datacenter、Essentials) | はい | はい
Windows Server 2012 R2 (Standard、Datacenter、Foundation、Essentials) | はい | はい
Windows Server 2012 (Standard、Datacenter、Foundation) | はい | はい
Windows Server 2008 R2 (Standard、Enterprise、Datacenter、Foundation) | はい | はい
Windows Server 2008 SP2 (Standard、Datacenter、Foundation) | はい | いいえ 
Windows Storage Server 2016/2012 R2/2012 (Standard、Workgroup) | はい | いいえ 


## <a name="backup-limits"></a>Backup の制限

Azure Backup では、バックアップできるファイル/フォルダー データ ソースのサイズに制限があります。 1 つのボリュームからバックアップ用に選択された項目のサイズが、次の表に要約されているサイズを超えることはできません。

**オペレーティング システム** | **サイズの制限**
--- | ---
Windows Server 2012 またはそれ以降 |  54,400 GB
Windows Server 2008 R2 SP1 |    1,700 GB
Windows Server 2008 SP2 | 1,700 GB
Windows 8 以降  | 54,400 GB
Windows 7   | 1,700 GB


## <a name="supported-file-types-for-backup"></a>バックアップ用にサポートされるファイルの種類

**Type** | **サポートされています** 
--- | --- 
暗号化   | はい 
圧縮 | はい
スパース | はい 
圧縮 + スパース | はい
ハード リンク  | サポートされていません<br/><br/> Skipped
再解析ポイント   | サポートされていません<br/><br/> Skipped
暗号化 + スパース |  サポートされていません<br/><br/> Skipped
圧縮ストリーム   | サポートされていません<br/><br/> Skipped
スパース ストリーム   | サポートされていません<br/><br/> Skipped
One Drive (同期されたファイルはスパース ストリーム)    | サポートされていません 

## <a name="supported-drivesvolumes-for-backup"></a>バックアップ用にサポートされるドライブ/ボリューム

**ドライブ/ボリューム** | **サポートされています** | **詳細**
--- | --- | ---
読み取り専用ボリューム   | サポートされていません | VSS が機能するには、ボリュームが書き込み可能である必要があります。
オフライン ボリューム | サポートされていません |   VSS が機能するには、ボリュームがオンラインである必要があります。
ネットワーク共有   | サポートされていません |   バックアップ用のサーバーでボリュームがローカルである必要があります。
Bitlocker で保護されているボリューム | サポートされていません |   バックアップが機能するには、事前にボリュームがロック解除されている必要があります。
ファイル システムの識別  | サポートされていません |   NTFS のみ。
リムーバブル メディア | サポートされていません |   バックアップ項目のすべてのソースが固定の状態である必要があります。
重複除去されたドライブ | サポートされています。<br/><br/> Azure Backup は、重複除去されたデータを通常のデータに変換します。 データを最適化および暗号化して格納し、コンテナーに送信します。

## <a name="support-for-initial-offline-backup"></a>初期のオフライン バックアップのサポート

Azure Backup は、ディスクを使用して初期バックアップ データを Azure に転送する "オフライン シード処理" をサポートしています。 これは、初期バックアップが数テラバイト (TB) の範囲にある可能性がある場合に役立ちます。 オフライン バックアップのサポート対象は次のとおりです。

- MARS エージェントを実行しているオンプレミスのコンピューター上でのファイルやフォルダーの直接バックアップ。
- DPM サーバーまたは MABS からのワークロードやファイルのバックアップ。
- オフライン バックアップはシステム状態ファイルには使用できません。


## <a name="support-for-restore"></a>復元のサポート

- Azure Backup の新しい[インスタント リストア](/backup-instant-restore-capability.md) リリースを使用すると、データをコンテナーにコピーされる前に復元できます。<br/><br/> この機能を使用するには、バックアップされるコンピューターが .NET Framework 4.5.2 以降を実行している必要があります。
- 古いバージョンのオペレーティング システムが実行されているコンピューターには、バックアップを復元できません。 たとえば、Windows 7 コンピューターから取得されたバックアップは Windows 8 以降で復元できます。 ただし、Windows 8 コンピューターから取得されたバックアップを Windows 7 コンピューターに復元することはできません。


## <a name="next-steps"></a>次の手順
- MARS エージェントを使用したバックアップ アーキテクチャについては、[ここ](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders)を参照してください。
- Microsoft Azure Backup Server (MABS) または System Center DPM で MARS エージェントを実行する場合にサポートされる内容については、[ここ](backup-support-matrix-mabs-dpm.md)を参照してください。


