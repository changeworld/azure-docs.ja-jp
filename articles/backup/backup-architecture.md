---
title: Azure Backup のアーキテクチャ
description: Azure Backup サービスによって使用される、アーキテクチャ、コンポーネント、およびプロセスの概要を示します。
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: raynew
ms.openlocfilehash: 4be483994bd7bc5bd97b1e59df230f66e9b4e24e
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430348"
---
# <a name="azure-backup-architecture"></a>Azure Backup のアーキテクチャ

[Azure Backup サービス](backup-overview.md)を使用して、Microsoft Azure クラウドにデータをバックアップすることができます。 この記事では、Azure Backup のアーキテクチャ、コンポーネント、およびプロセスの概要を示します。 


## <a name="what-does-azure-backup-do"></a>Azure Backup の機能

Azure Backup では、データ、マシンの状態、オンプレミスのマシンや Azure VM で実行されているワークロードをバックアップします。 Azure Backup のシナリオは数多くあります。

## <a name="how-does-azure-backup-work"></a>Azure Backup のしくみ

複数の方法を使用して、マシンとデータをバックアップすることができます。

- **オンプレミスのマシンをバックアップする**:
    - Azure Backup Microsoft Azure Recovery Services (MARS) エージェントを使用して、オンプレミスの Windows マシンを Azure に直接バックアップできます。 Linux マシンはサポートされていません。
    - オンプレミスのマシンをバックアップ サーバー (System Center Data Protection Manager (DPM) または Microsoft Azure Backup Server (MABS)) にバックアップし、さらにバックアップ サーバーを Azure の Azure Backup Recovery Services コンテナーにバックアップすることができます。
- **Azure VM をバックアップする**:
    - Azure VM を直接バックアップすることができます。 これを行うために、Azure Backup によって、VM で実行されている Azure VM エージェントに、バックアップ拡張機能がインストールされます。 これにより、VM 全体がバックアップされます。
    - MARS エージェントを実行することにより、Azure VM 上の特定のファイルおよびフォルダーをバックアップできます。
    - Azure で実行されている MABS に Azure VM をバックアップし、さらに MABS をコンテナーにバックアップできます。

詳細については、[バックアップできる内容](backup-overview.md)と[サポートされるバックアップ シナリオ](backup-support-matrix.md)に関するページを参照してください。


## <a name="where-is-data-backed-up"></a>データはどこにバックアップされますか。

Azure Backup では、Recovery Services コンテナーにバックアップしたデータを格納します。 コンテナーは、バックアップ コピー、復旧ポイント、バックアップ ポリシーなどのデータを保持するために使用される、Azure のオンライン ストレージ エンティティです。

- コンテナーを使用すると、管理オーバーヘッドを最小限に抑えながら、バックアップ データを簡単に整理できます。
- 各 Azure サブスクリプションに、最大 500 個のコンテナーを作成できます。
- Azure VM とオンプレミスのマシンを含む、コンテナーでバックアップされたアイテムを監視することができます。
- Azure の[ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) を使用して、コンテナーのアクセスを管理できます。
- 冗長性のためにコンテナー内のデータをレプリケートする方法を指定します。
    - **LRS**:ローカル冗長ストレージ (LRS) を使用して、データセンターの障害から保護することができます。 LRS では、ストレージ スケール ユニットにデータをレプリケートします。 [詳細情報](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)。
    - **GRS**:geo 冗長ストレージ (GRS) を使用できます。リージョン全体の障害から保護します。 セカンダリ リージョンにデータをレプリケートします。 [詳細情報](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。 
    - 既定では、Backup の Recovery Services コンテナーで GRS が使用されます。 




### <a name="backup-agents"></a>Backup のエージェント

Azure Backup では、バックアップの種類に応じて、さまざまなエージェントが提供されます。

**エージェント** | **詳細** 
--- | --- 
**Microsoft Azure Recovery Services (MARS) エージェント** | 1) オンプレミスの Windows サーバー上で実行され、ファイル、フォルダー、システムの状態をバックアップします<br/><br/> 2) Azure VM 上で実行され、ファイル、フォルダー、システムの状態をバックアップします。<br/><br/>  3) DPM/MABS サーバー上で実行され、DPM/MABS のローカル ストレージ ディスクを Azure にバックアップします。 
**Azure VM 拡張機能** | Azure VM 上で実行され、コンテナーに VM をバックアップします。


## <a name="backup-types"></a>バックアップの種類

**バックアップの種類** | **詳細** | **使用方法**
--- | --- | ---
**完全** | バックアップにはデータ ソース全体が含まれます。 完全バックアップでは、より多くのネットワーク帯域幅が使用されます。 | 初回バックアップで使用されます。
**差分** |  初回の完全バックアップ以降に変更されたブロックを格納します。 より少ない量のネットワークとストレージが使用され、変更されていないデータの冗長コピーは保持されません。<br/><br/> 以降の各バックアップの間には変更されていないデータ ブロックが転送され、格納されるため、非効率です。 | Azure Backup では使用されません。
**増分** | ストレージとネットワークの効率が高くなります。 前回のバックアップ以降に変更されたデータのブロックのみが格納されます。 <br/><br/> 増分バックアップを使用する場合、完全バックアップで補完する必要はありません。 | ディスク バックアップで DPM/MABS によって使用され、Azure へのすべてのバックアップで使用されます。

## <a name="sql-server-backup-types"></a>SQL Server のバックアップの種類

**バックアップの種類** | **詳細** | **使用方法**
--- | --- | ---
**完全バックアップ** | 完全なデータベース バックアップでは、データベース全体をバックアップします。 特定のデータベースあるいは一連のファイル グループまたはファイル内のすべてのデータと、そのデータを復旧するための十分なログが含まれます。 | 最多で、1 日に 1 回の完全バックアップをトリガーできます。<br/><br/> 日次または週次で完全バックアップを取得することを選択できます。
**差分バックアップ** | 差分バックアップは、直近の完全データ バックアップに基づきます。<br/><br/> 完全バックアップ以降に変更されたデータのみがキャプチャされます。 |  最多で、1 日に 1 回の差分バックアップをトリガーできます。<br/><br/> 完全バックアップと差分バックアップを同じ日に構成することはできません。
**トランザクション ログ バックアップ** | ログ バックアップでは、ある特定の時点への復元を、指定した秒に至るまで可能にします。 | 最多で、15 分ごとにトランザクション ログ バックアップを構成できます。


### <a name="comparison"></a>比較

バックアップの種類によって、ストレージの使用量、目標復旧時間 (RTO)、およびネットワークの使用量が異なります。 次の図では、バックアップの種類を比較しています。
- データ ソース A は A1 から A10 の 10 個のストレージ ブロックで構成され、毎月バックアップされます。
- ブロック A2、A3、A4、および A9 は最初の月に変更され、ブロック A5 はその翌月に変更されます。
- 差分バックアップでは、2 か月目に、変更されたブロック A2、A3、A4、および A9 がバックアップされます。 3 か月目は、既にバックアップ済みのブロックが再度バックアップされたうえで、変更された A5 がバックアップされます。 変更されたブロックは、次回完全バックアップが行われるまでバックアップされ続けます。
- 増分バックアップでは、最初の月に完全バックアップが行われた後、ブロック A2、A3、A4、および A9 が変更済みとしてマークされ、2 か月目に転送されます。 3 か月目は、変更された A5 のみがマークされ、転送されます。 

![バックアップ方法の比較を示す図](./media/backup-architecture/backup-method-comparison.png)

## <a name="backup-features"></a>バックアップの機能

次の表は、さまざまな種類のバックアップの機能をまとめたものです。

**機能** | **オンプレミスの Windows マシン (直接)** | **Azure VM** | **DPM/MABS を使用するマシン/アプリ**
--- | --- | --- | ---
コンテナーにバックアップする | ![はい][green] | ![はい][green] | ![はい][green] 
DPM/MABS ディスクにバックアップしてから、Azure にバックアップする | | | ![はい][green] 
バックアップのために送信されるデータを圧縮する | ![はい][green] | データの転送時に圧縮は使用されません。 ストレージが少し大きくなりますが、復元はより高速になります。  | ![はい][green] 
増分バックアップを実行する |![はい][green] |![はい][green] |![はい][green] 
重複除去されたディスクをバックアップする | | | ![部分的][yellow]<br/><br/> オンプレミスにデプロイされた DPM/MABS サーバーの場合のみ。 

![table key](./media/backup-architecture/table-key.png)





## <a name="architecture-direct-backup-of-azure-vms"></a>アーキテクチャ:Azure VM の直接バックアップ

1. Azure VM のバックアップを有効にすると、指定したスケジュールに従ってバックアップが実行されます。
2. 最初のバックアップ時に、バックアップ拡張機能が VM (実行されている場合) にインストールされます。
    - Windows VM の場合、VMSnapshot 拡張機能がインストールされます。
    - Linux VM の場合、VMSnapshot Linux 拡張機能がインストールされます。
3. この拡張機能によって、ストレージ レベルのスナップショットが取得されます。 
    - 実行されている Windows VM の場合、Backup は VSS と連携して、VM のアプリ整合性スナップショットを取得します。 既定では、Backup で完全 VSS バックアップを取得します。 Backup でアプリ整合性スナップショットを取得できない場合、ファイル整合性スナップショットが取得されます。
    - Linux VM の場合、Backup ではファイル整合性スナップショットが取得されます。 アプリ整合性スナップショットの場合は、事前/事後スクリプトを手動でカスタマイズする必要があります。
    - Backup は、各 VM ディスクを並行してバックアップすることで最適化されます。 バックアップ対象の各ディスクについては、Azure Backup でディスク上のブロックが読み取られ、変更されたデータのみが格納されます。 
4. スナップショットが取得された後、データはコンテナーに転送されます。 
    - 最後のバックアップ以降に変更されたデータのブロックのみがコピーされます。
    - データは暗号化されません。 Azure Backup では、Azure Disk Encryption (ADE) を使用して暗号化された Azure VM をバックアップできます。
    - スナップショット データはコンテナーにすぐにコピーされない場合があります。 ピーク時には、数時間かかる場合があります。 毎日のバックアップ ポリシーでは、VM のバックアップの合計時間は 24 時間未満になります。
5. データがコンテナーに送信された後、スナップショットが削除され、回復ポイントが作成されます。

Azure VM では制御コマンド用にインターネット アクセスが必要であることに注意してください。 VM 内のワークロードでバッキングしている場合は (SQL Server のバックアップなど)、バック データもインターネット アクセスが必要です。 

![Azure VM のバックアップ](./media/backup-architecture/architecture-azure-vm.png)

## <a name="architecture-direct-backup-of-on-premises-windows-machinesazure-vm-filesfolders"></a>アーキテクチャ:オンプレミスの Windows マシン/Azure VM ファイル/フォルダーの直接バックアップ

1. シナリオを設定するには、Microsoft Azure Recovery Services (MARS) エージェントをマシンにダウンロードしてインストールし、バックアップの対象、バックアップを実行するタイミング、および Azure での保持期間を選択します。
2. 初期バックアップは、バックアップ設定に従って実行されます。
3. MARS エージェントでは Windows ボリューム シャドウ コピー (VSS) サービスを使用して、バックアップ用に選択されたボリュームの特定の時点のスナップショットを取得します。
    - MARS エージェントでは Windows システムによる書き込みのみを使用して、スナップショットをキャプチャします。
    - エージェントではアプリケーションの VSS ライターは使用しないため、アプリ整合性スナップショットはキャプチャしません。
3. VSS でスナップショットを取得した後、MARS エージェントでは、バックアップの構成時に指定されたキャッシュ フォルダーに VHD を作成し、各データ ブロックのチェックサムを格納します。 
4. 増分バックアップは、アドホック バックアップが実行されない限り、指定されたスケジュールに従って実行されます。
5. 増分バックアップでは、変更されたファイルが識別され、新しい VHD が作成されます。 これは圧縮および暗号化されて、コンテナーに送信されます。
6. 増分バックアップが完了した後、新しい VHD は初期レプリケーション後に作成された VHD にマージされ、進行中のバックアップの比較に使用される最新の状態が提供されます。 

![MARS エージェントを使用するオンプレミス Windows サーバーのバックアップ](./media/backup-architecture/architecture-on-premises-mars.png)

## <a name="architecture-back-up-to-dpmmabs"></a>アーキテクチャ:DPM/MABS へのバックアップ

1. 保護対象のマシン上に DPM または MABS 保護エージェントをインストールし、DPM 保護グループにマシンを追加します。
    - オンプレミス マシンを保護するには、DPM または MABS サーバーをオンプレミスに配置する必要があります。
    - Azure VM を保護するには、MABS サーバーを Azure に配置し、Azure VM として実行する必要があります。
    - DPM/MABS を使用することで、バックアップ ボリューム、共有、ファイル、およびフォルダーを保護できます。 マシン システムの状態/ベア メタルを保護し、アプリ対応のバックアップ設定で特定のアプリを保護することができます。
2. DPM/MABS でマシンまたはアプリの保護を設定する場合は、短期的なストレージ用の MABS/DPM ローカル ディスクにバックアップしてから、Azure にバックアップ (オンライン保護) することを選択します。 また、ローカル DPM/MABS ストレージへのバックアップを実行するタイミングと、Azure へのオンライン バックアップを実行するタイミングを指定する必要があります。
3. 指定したスケジュールに従って、保護されたワークロードのディスクがローカル MABS/DPM ディスクにバックアップされます。
4. DPM/MABS ディスクは、DPM/MABS サーバーで実行されている MARS エージェントによって、コンテナーにバックアップされます。

![DPM または MABS で保護されたマシン/ワークロードのバックアップ](./media/backup-architecture/architecture-dpm-mabs.png)







## <a name="azure-vm-storage"></a>Azure VM ストレージ

- Azure VM ではディスクを使用して、そのオペレーティング システム、アプリ、およびデータを格納します。
- Azure VM には少なくとも 2 つのディスクがあります。 1 つはオペレーティング システム用の一時ディスクです。 アプリ データ用のデータ ディスクもあります。 ディスクは VHD として格納されます。
- Azure では、Standard または Premium Storage アカウントにページ BLOB として VHD が格納されます。
    - Standard Storage:待機時間の影響を受けないワークロードを実行する VM 向けの信頼性の高い低コストのディスク サポート。 Standard Storage では Standard SSD ディスクまたは Standard HDD ディスクを使用できます。
    - Premium Storage:高パフォーマンスのディスク サポート。 Premium SSD ディスクを使用します。
- ディスクにはさまざまなパフォーマンス レベルがあります。
    - Standard HDD ディスク:HDD によってサポートされ、コスト効率の高いストレージに使用されます。
    - Standard SSD ディスク:Premium SSD ディスクと Standard HDD ディスクの要素を結合します。HDD より一貫性のあるパフォーマンスと信頼性が提供されますが、コスト効率は高いままです。
    - Premium SSD ディスク:SSD によってサポートされ、I/O 集中型ワークロードを実行する VM 用の高パフォーマンスと低待機時間を提供します。
- ディスクには、次のようなマネージドとアンマネージドがあります。
    - アンマネージド ディスク:VM で使用されるディスクの従来の種類。 これらのディスクでは、独自のストレージ アカウントを作成し、ディスクの作成時にそれを指定します。 VM のストレージ リソースを最大化する方法を確認する必要があります。
    - マネージド ディスク:Azure でストレージ アカウントの作成と管理が処理されます。 ディスク サイズとパフォーマンス レベルの指定はユーザーが行い、マネージド ディスクの作成は Azure で行います。 ユーザーがディスクを追加し、VM をスケーリングしたときに、Azure でストレージが処理されます。

詳細:

- [Windows](../virtual-machines/windows/managed-disks-overview.md) および [Linux](../virtual-machines/linux/managed-disks-overview.md) VM のディスク ストレージに関する詳細を参照してください。
- Standard や Premium など、利用可能な[ディスクの種類](../virtual-machines/windows/disks-types.md)について学習してください。


### <a name="backing-up-and-restoring-azure-vms-with-premium-storage"></a>Premium Storage を使用する Azure VM のバックアップと復元 

Azure Backup で Premium Storage を使用して、Azure VM をバックアップすることができます。

- Premium Storage で VM をバックアップすると、Backup サービスによって、Storage アカウントに "AzureBackup-" という名前の一時的なステージング場所が作成されます。 このステージング場所のサイズは、復旧ポイントのスナップショットのサイズと同じです。
- Premium Storage アカウントに、一時的なステージング場所として使用できる十分な空き領域があることを確認してください。 [詳細情報](../storage/common/storage-scalability-targets.md#premium-storage-account-scale-limits)。 ステージングの場所は変更しないでください。
- バックアップ ジョブが完了した後、ステージングの場所は削除されます。
- ステージングの場所に使用されるストレージの価格は、[Premium Storage の価格](../virtual-machines/windows/disks-types.md#billing)に準じます。

Premium Storage を使用して Azure VM を復元する場合、Premium または Standard Storage に復元できます。 通常は、Premium に復元しますが、VM からファイルのサブセットのみが必要な場合は、Standard に復元するほうがコスト効率が良いことがあります。

### <a name="backing-up-and-restoring-managed-disks"></a>マネージド ディスクのバックアップと復元

マネージド ディスクを使用して Azure VM をバックアップすることができます。
- 他の Azure VM の場合と同じように、マネージド ディスクで VM をバックアップします。 仮想マシン設定から直接 VM をバックアップすることも、Recovery Services コンテナーで VM のバックアップを有効にすることもできます。
- マネージド ディスクに VM をバックアップするには、マネージド ディスク上に構築された RestorePoint コレクションを使用します。
- Azure Backup では、Azure Disk Encryption (ADE) を使用して暗号化されたマネージド ディスク VM のバックアップもサポートされます。

マネージド ディスクで VM を復元する場合、マネージド ディスクで完全な VM に復元するか、ストレージ アカウントに復元することができます。
- Azure によって、復元プロセス中にマネージド ディスクが処理されます。ユーザーは、ストレージ アカウント オプションを使用して、復元中に作成されたストレージ アカウントを管理します。
- 暗号化されたマネージド VM を復元する場合、復元プロセスを開始する前に、VM のキーとシークレットがキー コンテナーに存在する必要があります。




## <a name="next-steps"></a>次の手順

- サポート マトリックスを[確認](backup-support-matrix.md)し、バックアップ シナリオでサポートされる機能と制限事項について学習します。
- シナリオのいずれかのバックアップを設定します。
    - [Azure VM のバックアップ](backup-azure-arm-vms-prepare.md)
    - バックアップ サーバーなしで、[Windows マシンを直接バックアップ](tutorial-backup-windows-server-to-azure.md)します。
    - Azure へのバックアップのために [MABS を設定](backup-azure-microsoft-azure-backup.md)してから、MABS にワークロードをバックアップします。
    - Azure へのバックアップのために [DPM を設定](backup-azure-dpm-introduction.md)してから、DPM にワークロードをバックアップします。


[green]: ./media/backup-architecture/green.png
[yellow]: ./media/backup-architecture/yellow.png
[red]: ./media/backup-architecture/red.png

