---
title: Azure Backup のサポート マトリックス
description: Azure Backup サービスのサポート設定と制限事項の概要を説明します。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: ff4ee1d88bd13e647d0f6218d7e9c9b2c57a5a01
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429572"
---
# <a name="azure-backup-support-matrix"></a>Azure Backup のサポート マトリックス

[Azure Backup サービス](backup-overview.md)を使用して、Microsoft Azure クラウドにデータをバックアップすることができます。 この記事では、Azure Backup のシナリオとデプロイの一般的なサポート設定と制限事項について説明します。

他に以下のサポート マトリックスを使用できます。

Azure VM バックアップの[サポート マトリックス](backup-support-matrix-iaas.md)、System Center DPM/Microsoft Azure Backup サーバー (MABS) を使用するバックアップの[サポート マトリックス](backup-support-matrix-mabs-dpm.md)、Microsoft Azure Recovery Services (MARS) エージェントを使用するバックアップの[サポート マトリックス](backup-support-matrix-mars-agent.md)

## <a name="vault-support"></a>コンテナーのサポート

Azure Backup では、Recovery Services コンテナーを使用して、バックアップを調整および管理し、バックアップ データを格納します。

**設定** | **詳細**
--- | ---
**サブスクリプション内のコンテナー数** | 1 つのサブスクリプションで、最大 500 個の Recovery Services コンテナー。
**コンテナー内のマシン数** | 1 つのコンテナーで、最大 1,000 台の Azure VM。<br/><br/> 1 つのコンテナーに最大で 50 MABS を登録することができます。
**コンテナー ストレージ内のデータ ソース量** | 最大 54,400 GB。 Azure VM のバックアップに関する制限はありません。
**コンテナーへのバックアップ回数** | Azure VM: 1 日に 1 回<br/><br/>DPM/MABS で保護されたマシン: 1 日に 2 回<br/><br/> MARS エージェントを使用して直接バックアップされているマシン: 1 日に 3 回。 
**コンテナー間のバックアップ** | バックアップは 1 つのリージョン内です。<br/><br/> バックアップする VM が含まれるすべての Azure リージョンにコンテナーが必要です。 異なるリージョンにバックアップすることはできません。 
**コンテナーの移動** | 異なるサブスクリプション間で、または同じサブスクリプション内のリソース グループ間で、[コンテナーを移動する](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault)ことができます。
**コンテナー間のデータの移動** | コンテナー間におけるバックアップ データの移動はサポートされていません。
**コンテナー ストレージの種類の変更** | バックアップが格納される前に、コンテナーのストレージ レプリケーションの種類 (GRS と LRS) を変更できます。 コンテナーでバックアップが開始された後は、レプリケーションの種類を変更できません。



## <a name="on-premises-backup-support"></a>オンプレミス バックアップのサポート

オンプレミス マシンをバックアップしたい場合に何がサポートされるかを以下に示します。

**マシン** | **バックアップ対象** | **場所** | **機能**
--- | --- | --- | ---
**MARS エージェントを使用した Windows マシンの直接バックアップ** | ファイル、フォルダー、システム状態 | Recovery Services コンテナーへのバックアップ | 1 日に 3 回のバックアップ。<br/><br/> アプリ対応のバックアップなし。<br/><br/> ファイル、フォルダー、ボリュームの復元。
**MARS エージェントを使用した Linux マシンの直接バックアップ** | バックアップはサポートされていません。
**DPM へのバックアップ** | ファイル、フォルダー、ボリューム、システム状態、アプリ データ。 | ローカル DPM ストレージにバックアップします。 DPM はその後、コンテナーにバックアップされます。 | アプリ対応のスナップショット<br/><br/> バックアップと回復の完全な細分性。<br/><br/> VM (Hyper-V または VMware) で Linux がサポートされています。<br/><br/>。 Oracle はサポートされていません。
**MABS へのバックアップ** | ファイル、フォルダー、ボリューム、システム状態、アプリ データ。 | MABS ローカル ストレージにバックアップします。 MABS はその後、コンテナーにバックアップされます。 | アプリ対応のスナップショット<br/><br/> バックアップと回復の完全な細分性。<br/><br/> VM (Hyper-V または VMware) で Linux がサポートされています。<br/><br/>。 Oracle はサポートされていません。


## <a name="azure-vm-backup-support"></a>Azure VM バックアップのサポート

### <a name="azure-vm-limits"></a>Azure VM の制限

**制限** | **詳細**
--- | ---
Azure VM のデータ ディスク数 | 最大 16 個。
Azure VM のデータ ディスク サイズ | ディスクごとに最大で 4,095 GB。


### <a name="azure-vm-backup-options"></a>Azure VM のバックアップ オプション

Azure VM をバックアップしたい場合に何がサポートされるかを以下に示します。

**マシン** | **バックアップ対象** | **場所** | **機能**
--- | --- | --- | ---
**VM 拡張機能を使用した Azure VM のバックアップ** | VM 全体 | コンテナーへのバックアップ | VM のバックアップを有効にするときにインストールされる拡張機能。<br/><br/> 1 日 1 回のバックアップ。<br/><br/> Windows VM の場合はアプリ対応バックアップ、Linux VM の場合はファイル整合性バックアップ。 カスタム スクリプトを使用して、Linux マシン用にアプリ整合性を構成できます。<br/><br/> VM またはディスクの復元。<br/><br/> Azure VM では、オンプレミスの場所へのバックアップはできません。
**MARS エージェントを使用する Azure VM のバックアップ** | ファイル、フォルダー | コンテナーへのバックアップ | 1 日に 3 回のバックアップ。<br/><br/> VM 全体ではなく特定のファイル/フォルダーをバックアップしたい場合は、MARS エージェントを VM 拡張機能と共に実行できます。
**Azure VM (DPM あり)** | ファイル、フォルダー、ボリューム、システム状態、アプリ データ。 | DPM が実行されている Azure VM のローカル ストレージにバックアップします。 DPM はその後、コンテナーにバックアップされます。 | アプリ対応のスナップショット<br/><br/> バックアップと回復の完全な細分性。<br/><br/> VM (Hyper-V または VMware) で Linux がサポートされています。<br/><br/>。 Oracle はサポートされていません。
**Azure VM (MABS あり)** | ファイル、フォルダー、ボリューム、システム状態、アプリ データ。 | MABS が実行されている Azure VM のローカル ストレージにバックアップされます。 MABS はその後、コンテナーにバックアップされます。 | アプリ対応のスナップショット<br/><br/> バックアップと回復の完全な細分性。<br/><br/> VM (Hyper-V または VMware) で Linux がサポートされています。<br/><br/> Oracle はサポートされていません。





## <a name="linux-backup-support"></a>Linux のバックアップ サポート

Linux マシンをバックアップをしたい場合に何がサポートされるかを以下に示します。

**Backup** | **Linux (Azure での動作保証済み)**
--- | ---
**Linux が実行されているオンプレミスのマシンの直接バックアップ** | いいえ。 MARS エージェントをインストールできるのは Windows マシンだけです。
**Linux が実行されている Azure VM のバックアップ (エージェント拡張機能を使用)** | [カスタム スクリプト](backup-azure-linux-app-consistent.md)を使用したアプリ整合性バックアップ。<br/><br/> ファイルレベルの回復。<br/><br/> 復旧ポイントまたはディスクから VM を作成することによる復元。
**DPM を使用した、Linux が実行されているオンプレミスまたは Azure VM のバックアップ** | Hyper-V および VMWare 上の Linux Guest VM のファイル整合性バックアップ<br/><br/> Hyper-V および VMWare Linux Guest VM の VM 復元</br></br> ファイル整合性バックアップは Azure VM では利用できません
**MABS を使用した、Linux が実行されているオンプレミス マシン/Azure VM のバックアップ** | Hyper-V および VMWare 上の Linux Guest VM のファイル整合性バックアップ<br/><br/> Hyper-V および VMWare Linux ゲスト VM の VM 復元</br></br> ファイル整合性バックアップは Azure VM では利用できません。

## <a name="daylight-saving-support"></a>夏時間のサポート

Azure Backup では、Azure VM バックアップの夏時間変更に対する時計の自動調整はサポートされていません。 必要に応じて手動でバックアップ ポリシーを変更します。


## <a name="disk-deduplication-support"></a>ディスクの重複除去のサポート

ディスクの重複除去のサポートは、次のとおりです。
- Windows を実行している Hyper-V VM をバックアップするために DPM または MABS を使用する場合、ディスクの重複除去はオンプレミスでサポートされています。 Windows Server では、重複除去は、バックアップ ストレージとして仮想マシンに接続されている仮想ハード ディスク (VHD) 上で (ホスト レベルで) 実行されます。
- Azure では、どの Backup コンポーネントでも重複除去はサポートされていません。 System Center DPM および Backup Server が Azure にデプロイされる場合、VM にアタッチされているストレージ ディスクは重複除去できません。


## <a name="securityencryption-support"></a>セキュリティおよび暗号化のサポート

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



## <a name="compression-support"></a>圧縮のサポート

Backup では、次の表にまとめたように、バックアップ トラフィックの圧縮をサポートしています。 

- Azure VM では、ストレージ ネットワーク経由で Azure ストレージ アカウントから直接データが VM 拡張機能によって読み取られます。そのため、このトラフィックを圧縮する必要はありません。
- DPM または MABS を使用する場合は、帯域幅を節約するために、データを DPM または MABS にバックアップする前に圧縮できます。

**マシン** | **MABS または DPM に圧縮 (TCP)** | **コンテナーに圧縮 (HTTPS)**
--- | --- | ---
**オンプレミスの Windows マシンの直接バックアップ** | NA | はい
**VM 拡張機能を使用した Azure VM のバックアップ** | NA | NA
** MABS/DPM を使用したオンプレミス/Azure マシンでのバックアップ | ![はい][green] | ![はい][green]



## <a name="retention-limits"></a>保有の制限

**設定** | **制限**
--- | ---
保護されているインスタンス (マシンまたはワークロード) ごとの最大復旧ポイント数 | 9999
復旧ポイントの最大有効期限 | 制限なし
DPM または MABS へのバックアップの最大頻度 | SQL Server の場合は 15 分ごと<br/><br/> 他のワークロードの場合は 1 時間に 1 回。
コンテナーへのバックアップの最大頻度 | MARS を実行しているオンプレミスの Windows マシンまたは Azure VM: 1 日に 3 回<br/><br/> DPM または MABS: 1 日に 2 回<br/><br/> Azure VM バックアップ: 1 回 (1 日あたり)
復旧ポイントの保持期間 | 日、週、月、年。
最大保有期間 | バックアップ頻度による。
DPM または MABS ディスクの復旧ポイント数 | ファイル サーバーの場合 64 個、アプリ サーバーの場合 448 個。<br/><br/> テープの復旧ポイント数は、オンプレミス DPM に対しては無制限。

## <a name="next-steps"></a>次の手順

- Azure VM バックアップの[サポート マトリックスを確認します](backup-support-matrix-iaas.md)。


[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
