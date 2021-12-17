---
title: MARS エージェントを使用したサポート マトリックス
description: この記事では、Microsoft Azure Recovery Services (MARS) エージェントを実行しているコンピューターをバックアップする場合の Azure Backup のサポートを要約しています。
ms.date: 06/04/2021
ms.topic: conceptual
ms.openlocfilehash: 068a5391130f569a2d56fa9bd605356036e7737f
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952969"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Microsoft Azure Recovery Services (MARS) エージェントを使用したバックアップのサポート マトリックス

[Azure Backup サービス](backup-overview.md)を使用すると、オンプレミスのコンピューターとアプリをバックアップしたり、Azure 仮想マシン (VM) をバックアップしたりできます。 この記事では、Microsoft Azure Recovery Services (MARS) エージェントを使用してコンピューターをバックアップする場合のサポートの設定および制限を要約しています。

## <a name="the-mars-agent"></a>MARS エージェント

Azure Backup は、MARS エージェントを使用して、オンプレミスのコンピューターや Azure VM から Azure の Backup Recovery Services コンテナーにデータをバックアップします。 MARS エージェントは次の動作が可能です。

- Azure の Backup Recovery Services コンテナーに直接バックアップできるように、オンプレミスの Windows コンピューター上で動作できます。
- コンテナーに直接バックアップできるように、Windows VM 上で動作できます。
- Microsoft Azure Backup Server (MABS) または System Center Data Protection Manager (DPM) サーバー上で動作できます。 このシナリオでは、コンピューターとワークロードは MABS または DPM サーバーにバックアップされます。 その後、MARS エージェントがこのサーバーを Azure のコンテナーにバックアップします。

> [!NOTE]
>Azure Backup は、夏時間 (DST) の時計の自動調整をサポートしていません。 実際の時刻とスケジュールされたバックアップ時刻がずれないようにするため、夏時間を考慮に入れてポリシーを変更してください。

バックアップ オプションは、エージェントがインストールされている場所によって異なります。 詳細については、[MARS エージェントを使用した Azure Backup アーキテクチャ](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)に関するページを参照してください。 MABS と DPM のバックアップ アーキテクチャについては、[DPM または MABS へのバックアップ](backup-architecture.md#architecture-back-up-to-dpmmabs)に関するページを参照してください。 バックアップ アーキテクチャに関する[要件](backup-support-matrix-mabs-dpm.md)も参照してください。

**インストール** | **詳細**
--- | ---
最新の MARS エージェントをダウンロードする | 最新バージョンのエージェントはコンテナーからダウンロードするか、または[直接ダウンロード](https://aka.ms/azurebackup_agent)できます。
コンピューターに直接インストールする | MARS エージェントは、オンプレミスの Windows サーバー、またはいずれかの[サポートされるオペレーティング システム](./backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)を実行している Windows VM に直接インストールできます。
バックアップ サーバーにインストールする | Azure にバックアップするように DPM または MABS を設定する場合は、そのサーバーに MARS エージェントをダウンロードしてインストールします。 バックアップ サーバーのサポート マトリックス内の[サポートされるオペレーティング システム](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)にエージェントをインストールできます。

> [!NOTE]
> 既定では、バックアップが有効になっている Azure VM には Azure Backup 拡張機能がインストールされています。 この拡張機能は、VM 全体をバックアップします。 VM 全体ではなく、特定のフォルダーやファイルをバックアップする場合は、この拡張機能と共に MARS エージェントを Azure VM にインストールして実行できます。
> MARS エージェントは、Azure VM 上で実行されると、その VM 上の一時ストレージにあるファイルまたはフォルダーをバックアップします。 それらのファイルまたはフォルダーが一時ストレージから削除されたり、一時ストレージが削除されたりすると、バックアップは失敗します。

## <a name="cache-folder-support"></a>キャッシュ フォルダーのサポート

MARS エージェントを使用してデータをバックアップする場合、エージェントはそのデータのスナップショットを取得し、それをローカルのキャッシュ フォルダーに格納してからデータを Azure に送信します。 キャッシュ (スクラッチ) フォルダーには、次のいくつかの要件があります。

**Cache** | **詳細**
--- | ---
サイズ |  キャッシュ フォルダー内の空き領域は、バックアップ データのサイズ全体の少なくとも 5 ～ 10% である必要があります。
場所 | キャッシュ フォルダーは、バックアップされるコンピューターにローカルに格納されており、かつオンラインである必要があります。 キャッシュ フォルダーがネットワーク共有、リムーバブル メディア、またはオフライン ボリュームに存在していてはいけません。
Folder | キャッシュ フォルダーは、重複除去されたボリューム上や、圧縮されたフォルダー、スパース フォルダー、または再解析ポイントを含むフォルダー内で暗号化されていてはいけません。
場所の変更 | キャッシュの場所は、バックアップ エンジンを停止し (`net stop bengine`)、キャッシュ フォルダーを新しいドライブにコピーすることによって変更できます。 (新しいドライブに十分な領域があることを確認してください。)その後、**HKLM\SOFTWARE\Microsoft\Windows Azure Backup** にある 2 つのレジストリ エントリ (**Config/ScratchLocation** と **Config/CloudBackupProvider/ScratchLocation**) を新しい場所に更新し、エンジンを再起動します。

## <a name="networking-and-access-support"></a>ネットワークとアクセスのサポート

[!INCLUDE [Configuring network connectivity](../../includes/backup-network-connectivity.md)]

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

>[!NOTE]
> MARS エージェントは、Windows Server Core SKU をサポートしていません。

MARS エージェントを使用すると、次のように実行されている一部のオペレーティング システム上の Azure に直接バックアップできます。

1. オンプレミスの Windows Server
2. Windows を実行中の Azure VM

これらのオペレーティング システムは 64 ビットであり、かつ最新の Service Pack および更新プログラムが実行されている必要があります。 次の表は、これらのオペレーティング システムをまとめたものです。

**オペレーティング システム** | **ファイル/フォルダー** | **システム状態** | **ソフトウェア/モジュールの要件**
--- | --- | --- | ---
Windows 10 (Enterprise、Pro、Home) | はい | いいえ |  ソフトウェア/モジュールの要件に対応するサーバーのバージョンをご確認ください
Windows 8.1 (Enterprise、Pro)| はい |いいえ | ソフトウェア/モジュールの要件に対応するサーバーのバージョンをご確認ください
Windows 8 (Enterprise、Pro) | はい | いいえ | ソフトウェア/モジュールの要件に対応するサーバーのバージョンをご確認ください
Windows Server 2016 (Standard、Datacenter、Essentials) | はい | はい | - .NET 4.5 <br> - Windows PowerShell <br> - 互換性のある最新の Microsoft VC++ 再頒布可能パッケージ <br> - Microsoft 管理コンソール (MMC) 3.0
Windows Server 2012 R2 (Standard、Datacenter、Foundation、Essentials) | はい | はい | - .NET 4.5 <br> - Windows PowerShell <br> - 互換性のある最新の Microsoft VC++ 再頒布可能パッケージ <br> - Microsoft 管理コンソール (MMC) 3.0
Windows Server 2012 (Standard、Datacenter、Foundation) | はい | はい |- .NET 4.5 <br> \- Windows PowerShell <br> - 互換性のある最新の Microsoft VC++ 再頒布可能パッケージ <br> - Microsoft 管理コンソール (MMC) 3.0 <br> - 展開イメージのサービスと管理 (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (Standard、Workgroup) | はい | いいえ | - .NET 4.5 <br> - Windows PowerShell <br> - 互換性のある最新の Microsoft VC++ 再頒布可能パッケージ <br> - Microsoft 管理コンソール (MMC) 3.0
Windows Server 2019 (Standard、Datacenter、Essentials) | はい | はい | - .NET 4.5 <br> - Windows PowerShell <br> - 互換性のある最新の Microsoft VC++ 再頒布可能パッケージ <br> - Microsoft 管理コンソール (MMC) 3.0

詳細については、「[サポートされている MABS および DPM オペレーティング システム](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)」を参照してください。

### <a name="operating-systems-at-end-of-support"></a>サポート終了のオペレーティング システム

以下のオペレーティング システムはサポートが終了しました。引き続き保護された状態を維持するため、オペレーティング システムをアップグレードすることを強くお勧めします。

既存のコミットメントによってオペレーティング システムをアップグレードできない場合は、Windows Server を Azure VM に移行し、Azure VM バックアップを利用して、保護された状態を継続することを検討してください。 Windows Server の移行の詳細については、[こちらの移行に関するページ](https://azure.microsoft.com/migration/windows-server/)を参照してください。

オペレーティング システムのアップグレードや Azure への移行ができないオンプレミス環境やホストされた環境の場合は、引き続き保護とサポートの対象となる状態に留まるため、マシンに対する拡張セキュリティ更新プログラムをアクティブにしてください。 拡張セキュリティ更新プログラムの対象となるのは、特定のエディションのみであること注意してください。 詳細については、[FAQ ページ](https://www.microsoft.com/windows-server/extended-security-updates)を参照してください。

| **オペレーティング システム**                                       | **ファイル/フォルダー** | **システム状態** | **ソフトウェア/モジュールの要件**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate、Enterprise、Pro、Home Premium/Basic、Starter) | はい               | いいえ                 | ソフトウェア/モジュールの要件に対応するサーバーのバージョンをご確認ください |
| Windows Server 2008 R2 (Standard、Enterprise、Datacenter、Foundation) | はい               | はい                | - .NET 3.5、.NET 4.5 <br>  - Windows PowerShell <br>  - 互換性のある Microsoft VC++ 再頒布可能パッケージ <br>  - Microsoft 管理コンソール (MMC) 3.0 <br>  - 展開イメージのサービスと管理 (DISM.exe) |
| Windows Server 2008 SP2 (Standard、Datacenter、Foundation)  | はい               | いいえ                 | - .NET 3.5、.NET 4.5 <br>  - Windows PowerShell <br>  - 互換性のある Microsoft VC++ 再頒布可能パッケージ <br>  - Microsoft 管理コンソール (MMC) 3.0 <br>  - 展開イメージのサービスと管理 (DISM.exe) <br>  - Virtual Server 2005 base + KB KB948515 |

## <a name="backup-limits"></a>Backup の制限

### <a name="size-limits"></a>サイズ制限

Azure Backup では、バックアップできるファイルまたはフォルダー データ ソースのサイズが制限されています。 1 つのボリュームからバックアップする項目が、次の表に要約されているサイズを超えることはできません。

**オペレーティング システム** | **サイズの制限**
--- | ---
Windows Server 2012 またはそれ以降 |54,400 GB
Windows Server 2008 R2 SP1 |1,700 GB
Windows Server 2008 SP2| 1,700 GB
Windows 8 以降| 54,400 GB
Windows 7| 1,700 GB

### <a name="minimum-retention-limits"></a>最小保有期間の制限

さまざまな回復ポイントに対して設定できる最小の保有期間は次のとおりです。

|回復ポイント |Duration  |
|---------|---------|
|日次の回復ポイント    |   7 日      |
|週次の回復ポイント     |    4 週間     |
|月次の回復ポイント    |   3 か月      |
|年次の回復ポイント  |      1 年   |

### <a name="other-limitations"></a>その他の制限事項

- MARS では、1 つのコンテナーに対して、同じ名前を持つ複数のマシンの保護をサポートしていません。

## <a name="supported-file-types-for-backup"></a>バックアップ用にサポートされるファイルの種類

**Type** | **サポート**
--- | ---
暗号化<sup>*</sup>| サポートされています。
Compressed | サポートされています。
スパース | サポートされています。
圧縮 + スパース |サポートされています。
ハード リンク| サポートされていません。 スキップされます。
再解析ポイント| サポートされていません。 スキップされます。
暗号化 + スパース |サポートされていません。 スキップされます。
圧縮ストリーム| サポートされていません。 スキップされます。
スパース ストリーム| サポートされていません。 スキップされます。
OneDrive (同期されるファイルはスパース ストリーム)| サポートされていません。
DFS レプリケーションが有効になっているフォルダー | サポートされていません。

\* MARS エージェントが、暗号化されたファイルにアクセスするために必要な証明書にアクセスできることを確認してください。 アクセスできないファイルはスキップされます。

## <a name="supported-drives-or-volumes-for-backup"></a>バックアップ用にサポートされるドライブまたはボリューム

**ドライブ/ボリューム** | **サポート** | **詳細**
--- | --- | ---
読み取り専用ボリューム| サポートされていません | ボリューム コピー シャドウ サービス (VSS) は、ボリュームが書き込み可能な場合にのみ機能します。
オフライン ボリューム| サポートされていません |VSS は、ボリュームがオンラインである場合にのみ機能します。
ネットワーク共有| サポートされていません |サーバー上でボリュームがローカルである必要があります。
BitLocker でロックされているボリューム| サポートされていません |バックアップを開始する前に、ボリュームのロックを解除する必要があります。
ファイル システムの識別| サポートされていません |NTFS のみがサポートされます。
リムーバブル メディア| サポートされていません |バックアップ項目のすべてのソースが *固定* の状態である必要があります。
重複除去されたドライブ | サポートされています | Azure Backup は、重複除去されたデータを通常のデータに変換します。 データを最適化および暗号化して格納し、コンテナーに送信します。

## <a name="support-for-initial-offline-backup"></a>初期のオフライン バックアップのサポート

Azure Backup は、ディスクを使用して初期バックアップ データを Azure に転送する *オフライン シード処理* をサポートしています。 このサポートは、初期バックアップが数テラバイト (TB) のサイズ範囲にある可能性がある場合に役立ちます。 オフライン バックアップのサポート対象は次のとおりです。

- MARS エージェントを実行しているオンプレミスのコンピューター上でのファイルやフォルダーの直接バックアップ。
- DPM サーバーまたは MABS からのワークロードやファイルのバックアップ。

オフライン バックアップはシステム状態ファイルには使用できません。

## <a name="support-for-data-restoration"></a>データ復元のサポート

Azure Backup の[インスタント リストア](backup-instant-restore-capability.md)機能を使用すると、データをコンテナーにコピーされる前に復元できます。 バックアップするコンピューターが .NET Framework 4.5.2 以降を実行している必要があります。

バックアップを、以前のバージョンのオペレーティング システムを実行しているターゲット コンピューターに復元することはできません。 たとえば、Windows 7 を実行しているコンピューターから取得されたバックアップは Windows 8 以降で復元できます。 ただし、Windows 7 を実行しているコンピューター上で、Windows 8 を実行しているコンピューターから取得されたバックアップを復元することはできません。

## <a name="previous-mars-agent-versions"></a>MARS エージェントの以前のバージョン

次の表に、エージェントの以前のバージョンとそのダウンロード リンクを示します。 最新の機能と最適なパフォーマンスを利用できるようにするために、エージェントを最新バージョンにアップグレードすることをお勧めします。

**[バージョン]** | **サポート技術情報の記事**
--- | ---
[2.0.9145.0](https://download.microsoft.com/download/4/5/E/45EB38B4-2DA7-45FA-92E1-5CA1E23D18D1/MARSAgentInstaller.exe) | 使用できません
[2.0.9151.0](https://download.microsoft.com/download/7/1/7/7177B70A-51E8-434D-BDF2-FA3A09E917D6/MARSAgentInstaller.exe) | 使用できません
[2.0.9153.0](https://download.microsoft.com/download/3/D/D/3DD8A2FF-AC48-4A62-8566-B2C05F0BCCD0/MARSAgentInstaller.exe) | 使用できません
[2.0.9162.0](https://download.microsoft.com/download/0/1/0/010E598E-6289-47DB-872A-FFAF5030E6BE/MARSAgentInstaller.exe) | 使用できません
[2.0.9169.0](https://download.microsoft.com/download/f/7/1/f716c719-24bc-4337-af48-113baddc14d8/MARSAgentInstaller.exe) | [4515971](https://support.microsoft.com/help/4538314)
[2.0.9170.0](https://download.microsoft.com/download/1/8/7/187ca9a9-a6e5-45f0-928f-9a843d84aed5/MARSAgentInstaller.exe) | 使用できません
[2.0.9173.0](https://download.microsoft.com/download/7/9/2/79263a35-de87-4ba6-9732-65563a4274b6/MARSAgentInstaller.exe) | [4538314](https://support.microsoft.com/help/4538314)
[2.0.9177.0](https://download.microsoft.com/download/3/0/4/304d3cdf-b123-42ee-ad03-98fb895bc38f/MARSAgentInstaller.exe) | 使用できません
[2.0.9181.0](https://download.microsoft.com/download/6/6/9/6698bc49-e30b-4a3e-a1f4-5c859beafdcc/MARSAgentInstaller.exe) | 使用できません
[2.0.9190.0](https://download.microsoft.com/download/a/c/e/aceffec0-794e-4259-8107-92a3f6c10f55/MARSAgentInstaller.exe) | [4575948](https://support.microsoft.com/help/4575948)
[2.0.9195.0](https://download.microsoft.com/download/6/1/3/613b70a7-f400-4806-9d98-ae26aeb70be9/MARSAgentInstaller.exe) | [4582474](https://support.microsoft.com/help/4582474)
[2.0.9197.0](https://download.microsoft.com/download/2/7/5/27531ace-3100-43bc-b4af-7367680ea66b/MARSAgentInstaller.exe) | [4589598](https://support.microsoft.com/help/4589598)
[2.0.9207.0](https://download.microsoft.com/download/b/5/a/b5a29638-1cef-4906-b704-4d3d914af76e/MARSAgentInstaller.exe) | [5001305](https://support.microsoft.com/help/5001305)

>[!NOTE]
>信頼性とパフォーマンスが若干向上した MARS エージェントのバージョンには、サポート技術情報の記事はありません。

## <a name="next-steps"></a>次のステップ

- [MARS エージェントを使用するバックアップ アーキテクチャ](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)の詳細を学習します。
- [MABS または DPM サーバー上で MARS エージェントを実行する](backup-support-matrix-mabs-dpm.md)場合にサポートされる内容を学習します。
