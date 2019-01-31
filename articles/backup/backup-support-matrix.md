---
title: Azure Backup のサポート マトリックス
description: Azure Backup サービスのサポート設定と制限事項の概要を説明します。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 01/09/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: cb3a60995a4edfe5eb00f1a5e88812146816806a
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2019
ms.locfileid: "54883706"
---
# <a name="azure-backup-support-matrix"></a>Azure Backup のサポート マトリックス

[Azure Backup サービス](backup-overview.md)を使用して、Microsoft Azure クラウドにデータをバックアップすることができます。 この記事では、Azure Backup のシナリオとデプロイのサポート設定と制限事項について説明します。

## <a name="vault-support"></a>コンテナーのサポート

Azure Backup では、Recovery Services コンテナーを使用して、バックアップを調整および管理し、バックアップ データを格納します。

**設定** | **詳細**
--- | ---
コンテナーの数 | 1 つのサブスクリプションで、最大 500 個の Recovery Services コンテナー。
コンテナー内のマシン | 1 つのコンテナーで、最大 1,000 台の Azure VM。<br/><br/> 1 つのコンテナーに、Azure Backup エージェント (Microsoft Azure Recovery Services エージェント (MABS)) を実行している最大で 50 台のオンプレミス マシンを登録できます。
コンテナー ストレージ内のデータ ソース | 最大 54,400 GB。 Azure VM のバックアップに関する制限はありません。
コンテナーへのバックアップ | Azure VM: 1 日 1 回、DPM または MABS によって保護されているマシン: 1 日 2 回、MARS エージェントを使用して直接バックアップされているマシン: 1 日 3 回。  
コンテナーの移動 | Recovery Services コンテナーを移動するには、プライベート プレビューへの登録が必要となります。 お試しになる場合は、AskAzureBackupTeam@microsoft.com までメールでご連絡ください。
コンテナー間のデータの移動 | コンテナー間におけるバックアップ データの移動はサポートされていません。
ストレージ レプリケーションの種類 | バックアップが格納される前に、コンテナーのストレージ レプリケーションの種類 (GRS と LRS) を変更できます。 コンテナーでバックアップが開始された後は、レプリケーションの種類を変更できません。



## <a name="on-premises-backup-support"></a>オンプレミス バックアップのサポート

オンプレミス マシンをバックアップしたい場合に何がサポートされるかを以下に示します。

**マシン** | **場所** | **バックアップ** | **機能**
--- | --- | --- | ---
**Windows 物理または仮想 (バックアップ サーバーなし)** | ファイル、フォルダー、システム状態 | Recovery Services コンテナーにバックアップされます | 1 日に 3 回のバックアップ。<br/><br/> アプリ対応のバックアップなし。<br/><br/> ファイル、フォルダー、ボリュームの復元。
**Linux 物理または仮想 (バックアップ サーバーなし)** | バックアップはサポートされていません。
**物理または仮想 (DPM あり)** | ファイル、フォルダー、ボリューム、システム状態、アプリ データ。 | DPM にバックアップされます (DPM サーバーにローカルにアタッチされているディスク、またはテープに)。<br/><br/> DPM はその後、コンテナーにバックアップされます。 | アプリ対応のスナップショット<br/><br/> バックアップと回復の完全な細分性。<br/><br/> VM (Hyper-V または VMware) で Linux がサポートされています。<br/><br/>。 Oracle はサポートされていません。
**物理または仮想 (MABS あり)** | ファイル、フォルダー、ボリューム、システム状態、アプリ データ。 | MABS にバックアップされます (MABS サーバーにローカルにアタッチされているディスクに)。 テープはサポートされていません<br/><br/> MABS はその後、コンテナーにバックアップされます。 | アプリ対応のスナップショット<br/><br/> バックアップと回復の完全な細分性。<br/><br/> VM (Hyper-V または VMware) で Linux がサポートされています。<br/><br/>。 Oracle はサポートされていません。


## <a name="azure-vms"></a>Azure VM

### <a name="azure-vm-limits"></a>Azure VM の制限

**制限** | **詳細**
--- | ---
Azure VM のデータ ディスク数 | 最大 16 個。
Azure VM のデータ ディスク サイズ | ディスクごとに最大で 4,095 GB。


### <a name="azure-vm-backup-options"></a>Azure VM のバックアップ オプション

Azure VM をバックアップしたい場合に何がサポートされるかを以下に示します。

**マシン** | **場所** | **バックアップ** | **機能**
--- | --- | --- | ---
**Azure VM (バックアップ サーバーなし)** | ファイル、フォルダー、システム状態 | コンテナーにバックアップされます。 | 1 日 1 回のバックアップ。<br/><br/> Windows VM の場合はアプリ対応バックアップ、Linux VM の場合はファイル整合性バックアップ。 カスタム スクリプトを使用して、Linux マシン用にアプリ整合性を構成できます。<br/><br/> VM またはディスクの復元。<br/><br/> Azure VM では、オンプレミスの場所へのバックアップはできません。
**Azure VM (DPM あり)** | ファイル、フォルダー、ボリューム、システム状態、アプリ データ。 | Azure で実行されている DPM にバックアップされます (DPM サーバーにローカルにアタッチされているディスクに)。 テープはサポートされていません。<br/><br/> DPM はその後、コンテナーにバックアップされます。 | アプリ対応のスナップショット<br/><br/> バックアップと回復の完全な細分性。<br/><br/> VM (Hyper-V または VMware) で Linux がサポートされています。<br/><br/>。 Oracle はサポートされていません。
**Azure VM (MABS あり)** | ファイル、フォルダー、ボリューム、システム状態、アプリ データ。 | Azure で実行されている MABS にバックアップされます (MABS サーバーにローカルにアタッチされているディスクに)。 テープはサポートされていません<br/><br/> MABS はその後、コンテナーにバックアップされます。 | アプリ対応のスナップショット<br/><br/> バックアップと回復の完全な細分性。<br/><br/> VM (Hyper-V または VMware) で Linux がサポートされています。<br/><br/>。 Oracle はサポートされていません。





## <a name="linux-backup-support"></a>Linux のバックアップ サポート

Linux マシンをバックアップをしたい場合に何がサポートされるかを以下に示します。

**Backup** | **Linux (Azure での動作保証済み)**
--- | ---
**オンプレミスの Linux マシン (DPM または MABS なし)**。 | いいえ。 MARS エージェントをインストールできるのは Windows マシンだけです。
**Azure VM (DPM または MABS なし)** | [カスタム スクリプト](backup-azure-linux-app-consistent.md)を使用したアプリ整合性バックアップ。<br/><br/> ファイルレベルの回復。<br/><br/> 復旧ポイントまたはディスクから VM を作成することによる復元。
**オンプレミス マシンまたは Azure VM (DPM あり)** | Hyper-V および VMWare 上の Linux Guest VM のファイル整合性バックアップ<br/><br/> Hyper-V および VMWare Linux Guest VM の VM 復元</br></br> ファイル整合性バックアップは Azure VM では利用できません
**オンプレミス マシンまたは Azure VM (MABS あり)** | Hyper-V および VMWare 上の Linux Guest VM のファイル整合性バックアップ<br/><br/> Hyper-V および VMWare Linux ゲスト VM の VM 復元</br></br> ファイル整合性バックアップは Azure VM では利用できません。

## <a name="disk-support"></a>ディスクのサポート

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
オンプレミスの Windows マシン (DPM または MABS なし) | ![[はい]][green] | ![[はい]][green]
Azure VM | ![[はい]][green] | ![[はい]][green]
オンプレミス VM または Azure VM (DPM あり) | ![[はい]][green] | ![[はい]][green]
オンプレミス VM または Azure VM (MABS あり) | ![[はい]][green] | ![[はい]][green]



## <a name="compression-support"></a>圧縮のサポート

Backup では、次の表にまとめたように、バックアップ トラフィックの圧縮をサポートしています。 以下の点に注意してください。

- Azure VM では、ストレージ ネットワーク経由で Azure ストレージ アカウントから直接データが VM 拡張機能によって読み取られます。そのため、このトラフィックを圧縮する必要はありません。
- DPM または MABS を使用する場合は、帯域幅を節約するために、データを DPM または MABS にバックアップする前に圧縮できます。

**マシン** | **MABS または DPM に圧縮 (TCP)** | **コンテナーに圧縮 (HTTPS)**
--- | --- | ---
オンプレミスの Windows マシン (DPM または MABS なし) | NA | [はい]
Azure VM | NA | NA
オンプレミス VM または Azure VM (DPM あり) | ![[はい]][green] | ![[はい]][green]
オンプレミス VM または Azure VM (MABS あり) | ![[はい]][green] | ![[はい]][green]



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

- [Azure VM のバックアップ](backup-azure-arm-vms-prepare.md)
- バックアップ サーバーなしで、[Windows マシンを直接バックアップ](tutorial-backup-windows-server-to-azure.md)します。
- Azure へのバックアップのために [MABS を設定](backup-azure-microsoft-azure-backup.md)してから、MABS にワークロードをバックアップします。
- Azure へのバックアップのために [DPM を設定](backup-azure-dpm-introduction.md)してから、DPM にワークロードをバックアップします。

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
