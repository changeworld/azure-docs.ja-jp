---
title: Log Replay Service を使用して SQL Managed Instance にデータベースを移行する
description: Log Replay Service を使用して SQL Server から SQL Managed Instance にデータベースを移行する方法について説明する
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: bc0dc72c7547c8f74aec53b7153fc5384c6b634b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690789"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-using-log-replay-service-preview"></a>Log Replay Service (プレビュー) を使用して SQL Server から SQL Managed Instance にデータベースを移行する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

この記事では、現在パブリック プレビュー中の Log Replay Service (LRS) を使用して、SQL Server (2008 から 2019 まで) から SQL Managed Instance へのデータベースの移行を手動で構成する方法について説明します。 これは、SQL Server ログ配布テクノロジに基づいた Managed Instance に対応したクラウド サービスです。 LRS を使用する必要があるのは、複雑なカスタム移行とハイブリッド アーキテクチャが存在する場合、より高度な制御が必要な場合、ダウンタイムの許容範囲がほとんどない場合、または Azure Data Migration Service (DMS) を使用できない場合です。

DMS と LRS はどちらも、基盤に使用される移行テクノロジが同じで、使用される API も同じです。 LRS のリリースにより、オンプレミスの SQL Server と SQL Managed Instance 間の複雑なカスタム移行とハイブリッド アーキテクチャがさらに可能になります。

## <a name="when-to-use-log-replay-service"></a>Log Replay Service を使用する場合

移行に [Azure DMS](/azure/dms/tutorial-sql-server-to-managed-instance.md) を使用できない場合、LRS クラウド サービスを PowerShell、CLI コマンドレット、または API で直接使用して、SQL Managed Instance へのデータベースの移行を手動で構築およびオーケストレーションできます。 

次のような場合に LRS クラウド サービスを使用することを検討してください。
- データベース移行プロジェクトでより高度な制御が必要である
- 移行カットオーバーでダウンタイムの許容範囲が少しある
- 使用環境に DMS 実行可能ファイルをインストールできない
- DMS 実行可能ファイルからデータベースのバックアップ ファイルにアクセスできない
- ホスト OS にアクセスできない、または管理者特権がない
- 使用環境から Azure にネットワーク ポートを開くことができない
- バックアップが、TO URL オプションを使用して Azure Blob Storage に直接保存されている
- 差分バックアップを使用する必要がある

> [!NOTE]
> SQL Server から SQL Managed Instance にデータベースを自動的に移行する場合は、Azure DMS を使用することをお勧めします。 このサービスは、同じ LRS クラウド サービスを、ログ配布を NORECOVERY モードに設定してバックエンドで使用しています。 ご自分のシナリオが Azure DMS によって完全にサポートされない場合は、LRS を手動で使用して移行をオーケストレーションすることを検討してください。

## <a name="how-does-it-work"></a>動作のしくみ

LRS を使用してクラウドにデータベースを移行するカスタム ソリューションを構築するには、後述の図と表で説明するオーケストレーション手順が必要です。

この移行は、SQL Server でデータベースの完全バックアップを CHECKSUM を有効にして作成する手順と、バックアップ ファイルを Azure Blob Storage にコピーする手順で構成されます。 LRS は、バックアップ ファイルを Azure Blob Storage から SQL Managed Instance に復元するのに使用されます。 Azure Blob Storage は、SQL Server と SQL Managed Instance の中間ストレージとして使用します。

完全バックアップが復元された後は、LRS によって Azure Blob Storage で新しい差分またはログ バックアップの追加が監視され、新しいファイルが追加されると、自動的に復元されます。 サービスを使用して SQL Managed Instance 上のバックアップ ファイル復元中の進行状況を監視でき、必要に応じてプロセスを中止することもできます。

Azure Blob Storage に配置されるすべてのファイルが LRS によりスキャンされるため、固有のバックアップ ファイル名前付け規則は必要なく、ファイル ヘッダーのみを読み取るようにバックアップ チェーンが構築されます。 データベースは [NORECOVERY](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) モードで復元され、移行プロセス中は "復元中" 状態になります。移行プロセスが完全に完了するまで読み取りまたは書き込みに使用することはできません。 

複数のデータベースを移行する場合は、各データベースのバックアップを Azure Blob Storage の個別のフォルダーに配置する必要があります。 LRS をデータベースごとに個別に開始する必要があり、Azure Blob Storage のフォルダーごとに異なるパスを指定する必要があります。 

LRS はオートコンプリートまたは連続モードで開始できます。 オートコンプリート モードで開始した場合は、指定した最後のバックアップ ファイル名が復元されると、移行が自動的に完了します。 連続モードで開始した場合は、新しいバックアップ ファイルが追加されると、サービスによって連続的に復元され、移行は手動カットオーバーのみで完了します。 手動カットオーバーは、ログ末尾の最後のバックアップが取得され、SQL Managed Instance で復元済みとして表示された後に実行することをお勧めします。 最後のカットオーバー手順が終わると、データベースがオンラインになり、SQL Managed Instance で読み取りと書き込みができるようになります。

オートコンプリート時に自動で、またはカットオーバー時に手動で LRS が停止されると、SQL Managed Instance でオンラインにされたデータベースの復元プロセスは再開できなくなります。 オートコンプリートによって、またはカットオーバー時に手動で移行が完了した後に追加のバックアップ ファイルを復元するには、データベースを削除し、LRS を再起動してバックアップ チェーン全体を最初から復元する必要があります。

![Log Replay Service により SQL Managed Instance に対して行われるオーケストレーション手順の説明](./media/log-replay-service-migrate/log-replay-service-conceptual.png)
    
| Operation | 詳細 |
| :----------------------------- | :------------------------- |
| **1.SQL Server のデータベース バックアップを Azure Blob Storage にコピーします**。 | - [Azcopy](/azure/storage/common/storage-use-azcopy-v10) または [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用して、SQL Server の完全バックアップ、差分バックアップ、ログ バックアップを Azure Blob Storage コンテナーにコピーします。 <br />- 任意のファイル名を使用します。LRS では固有のファイル名前付け規則は必要ありません。<br />- 複数のデータベースを移行する場合は、データベースごとに個別のフォルダーが必要です。 |
| **2.クラウドで LRS サービスを開始します**。 | - 次のいずれかのコマンドレットを使用してサービスを開始できます。 <br /> PowerShell [start-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_start コマンドレット](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)。 <br /> - Azure Blob Storage 上の別のバックアップ フォルダーを指す別のデータベースごとに、LRS を個別に開始します。 <br />- サービスが開始されると、Azure Blob Storage コンテナーからバックアップが取得され、復元が SQL Managed Instance で開始されます。<br /> - LRS が連続モードで開始された場合は、最初にアップロードされたすべてのバックアップが復元された後、サービスにより、フォルダーにアップロードされる新しいファイルが監視され、サービスが停止されるまで、LSN チェーンに基づいてログが連続して適用されます。 |
| **2.1.操作の進行状況を監視します**。 | - 復元操作の進行状況は、次のいずれかのコマンドレットを使用して監視できます。 <br /> PowerShell [get-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_show コマンドレット](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)。 |
| **2.2.必要に応じて、操作を停止 (中止) します**。 | - 移行プロセスを中止する必要がある場合は、次のいずれかのコマンドレットを使用して操作を停止できます。 <br /> PowerShell [stop-azsqlinstancedatabaselogreplay]/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop) コマンドレット。 <br /><br />- これにより、SQL Managed Instance 上に復元中のデータベースが削除されます。 <br />- いったん停止されると、データベースに対して LRS を再開することはできなくなります。 移行プロセスを最初から再起動する必要があります。 |
| **3.準備ができたら、クラウドにカットオーバーします**。 | - アプリケーションとワークロードを停止します。 ログ末尾の最後のバックアップを取得し、Azure Blob Storage にアップロードします。<br /> - 次のいずれかのコマンドレットを使用して LRS 完了操作を開始することで、カットオーバーを完了します。 <br />PowerShell [complete-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay) <br /> CLI [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete) コマンドレット。 <br /><br />- これにより、LRS サービスが停止し、データベースがオンラインになって、SQL Managed Instance で読み取りと書き込みを使用できるようになります。<br /> - SQL Server から SQL Managed Instance を指すようにアプリケーションの接続文字列を再設定します。 |

## <a name="requirements-for-getting-started"></a>作業を開始するための要件

### <a name="sql-server-side"></a>SQL Server 側
- SQL Server (2008 から 2019 まで)
- データベースの完全バックアップ (1 つまたは複数のファイル)
- 差分バックアップ (1 つまたは複数のファイル)
- ログ バックアップ (トランザクション ログ ファイル用に分割されていない)
- バックアップの **CHECKSUM を有効にする必要がある** (必須)

### <a name="azure-side"></a>Azure 側
- PowerShell Az.SQL module version 2.16.0 以降 ([インストール](https://www.powershellgallery.com/packages/Az.Sql/)、または Azure [Cloud Shell](/azure/cloud-shell/) を使用)
- CLI バージョン 2.19.0 以降 ([インストール](/cli/azure/install-azure-cli))
- プロビジョニングされた Azure Blob Storage コンテナー
- BLOB ストレージ コンテナーに対して **読み取り** と **リスト** のアクセス許可のみが付与された SAS セキュリティ トークンが生成されている

### <a name="migrating-multiple-databases"></a>複数のデータベースの移行
- 異なるデータベースのバックアップ ファイルは、Azure Blob Storage 上の個別のフォルダーに配置する必要があります。
- LRS は、Azure Blob Storage 上の適切なフォルダーを指すデータベースごとに個別に開始される必要があります。
- LRS は、1 つの SQL Managed Instance ごとに最大 100 個の同時復元プロセスをサポートできます。

### <a name="azure-rbac-permissions-required"></a>必要な Azure RBAC アクセス許可
指定したクライアントを介して LRS を実行するには、次のいずれかの Azure ロールが必要です。
- サブスクリプションの所有者ロール、または
- [Managed Instance 共同作成者](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)ロール、または
- 次のアクセス許可を持つカスタム ロール:
  - `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>ベスト プラクティス

ベスト プラクティスとして、以下を強くお勧めします。
- [Data Migration Assistant](/sql/dma/dma-overview) を実行して、データベースを SQL Managed Instance に移行する準備ができていることを確認します。 
- 完全バックアップと差分バックアップを、1 つのファイルではなく複数のファイルに分割します。
- バックアップの圧縮を有効にします。
- リリースされる最新のコマンドレットに常に更新されるため、Cloud Shell を使用してスクリプトを実行します。
- LRS サービスが開始されてから 47 時間以内に移行を完了するように計画します。 これは、LRS が開始された後に、システム管理されているソフトウェア パッチが適用されない猶予期間です。

> [!IMPORTANT]
> - LRS を使用して復元中のデータベースは、移行プロセスが完了するまで使用できません。
> - 移行中のデータベースに対する読み取り専用アクセスは、LRS ではサポートされていません。
> - 移行が完了すると、LRS では復元の再開はサポートされていないため、移行プロセスが完結します。

## <a name="steps-to-execute"></a>実行する手順

### <a name="make-backups-on-the-sql-server"></a>SQL Server のバックアップを作成する

SQL Server のバックアップは、次の 2 つのオプションのいずれかを使用して作成できます。

- お使いの環境で Azure Blob Storage への直接バックアップが制限されている場合は、ローカル ディスク ストレージにバックアップしてから、ファイルを Azure Blob Storage にアップロードします。
- お使いの環境とセキュリティ手順により許可されている場合は、T-SQL の "TO URL" オプションを使用して Azure Blob Storage に直接バックアップします。 

完全復旧モードに移行するデータベースを、ログ バックアップを許可するように設定します。

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

ローカル ストレージでデータベースの完全バックアップ、差分バックアップ、ログ バックアップを手動で作成するには、T-SQL スクリプトの提供されている次のサンプルを使用します。 CHECKSUM オプションを確実に有効にします。これは LRS の必須要件です。

```SQL
-- Example on how to make full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to the locak disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-azure-blob-storage"></a>Azure BLOB ストレージを作成する

Azure Blob Storage は、SQL Server と SQL Managed Instance 間のバックアップ ファイルの中間ストレージとして使用されます。 ストレージ アカウント内に新しいストレージ アカウントと BLOB コンテナーを作成するには、次の手順を行います。

1. [ストレージ アカウントの作成](../../storage/common/storage-account-create.md?tabs=azure-portal)
2. ストレージ アカウント内に [BLOB コンテナーを作成します](../../storage/blobs/storage-quickstart-blobs-portal.md)

### <a name="copy-backups-from-sql-server-to-azure-blob-storage"></a>SQL Server のバックアップを Azure Blob Storage にコピーする

次のような方法を利用することで、LRS を使用してデータベースをマネージド インスタンスに移行するときにバックアップを BLOB ストレージにアップロードできます。
- [Azcopy](/azure/storage/common/storage-use-azcopy-v10) または [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer) を使用して、バックアップを BLOB コンテナーにアップロードする。
- Azure portal で Storage Explorer を使用する。

### <a name="make-backups-from-sql-server-directly-to-azure-blob-storage"></a>SQL Server のバックアップを Azure Blob Storage に直接作成する

別の方法として、SQL Server のネイティブの [BACKUP TO URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) が企業およびネットワーク ポリシーによって許可されている場合は、このオプションを使用して、SQL Server のバックアップを Azure Blob Storage に直接作成できます。 このオプションを実行できる場合は、ローカル ストレージにバックアップを作成して Azure Blob Storage にアップロードする必要がありません。

この操作の 1 番目の手順として、Azure Blob Storage に対する SAS 認証トークンを生成する必要があり、このトークンを SQL Server にインポートする必要があります。 2 番目の手順で、T-SQL の "TO URL" オプションを使用してバックアップを作成します。 すべてのバックアップが確実に CHEKSUM オプションを有効にして作成されるようにします。

参考のために、Azure Blob Storage のバックアップを作成するサンプル コードを以下に示します。 この例には、SAS トークンをインポートする手順は含まれていません。 SAS トークンを生成して SQL Server にインポートする方法など、詳細な手順については、[Azure Blob Storage サービスと SQL Server の使用](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage)に関する記事をご覧ください。 

```SQL
-- Example on how to make full database backup to URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example on how to make differential database backup to URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example on how to make the transactional log backup to URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-azure-blob-storage-sas-authentication-for-lrs"></a>Azure Blob Storage の LRS 用の SAS 認証を生成する

Azure Blob Storage は、SQL Server と SQL Managed Instance 間のバックアップ ファイルの中間ストレージとして使用されます。 リストと読み取りのアクセス許可のみが付与された SAS 認証トークンが、LRS サービスで使用するために生成される必要があります。 これにより、LRS サービスから、Azure Blob Storage にアクセスし、バックアップ ファイルを使用して SQL Managed Instance に復元することができます。 次の手順に従って、LRS に使用する SAS 認証を生成します。

1. Azure portal から Storage Explorer にアクセスします
2. BLOB コンテナーを展開します
3. BLOB コンテナーを右クリックし、[Get Shared Access Signature]\(Shared Access Signature の取得\) を選択します ![Log Replay Service により、SAS 認証トークンが生成される](./media/log-replay-service-migrate/lrs-sas-token-01.png)
4. トークンの有効期間を選択します。 移行期間中、トークンが確実に有効であるようにします。
5. トークンのタイム ゾーン (UTC または現地時間) を選択します
    - トークンとご利用の SQL Managed Instance のタイム ゾーンが一致していない場合があります。 タイム ゾーンを考慮した適切な有効期間が SAS トークンに確実に設定されるようにします。 可能であれば、タイム ゾーンを、移行予定時間枠のより早い時間とより遅い時間に設定します。
6. 読み取りとリストのアクセス許可のみを選択します
    - LRS を開始できなくなるので、他のアクセス許可は選択しないでください。 このセキュリティ要件は仕様です。
7. [作成] ボタンをクリックします ![Log Replay Service により、SAS 認証トークンが生成される](./media/log-replay-service-migrate/lrs-sas-token-02.png)

先ほど指定した有効期間の SAS 認証が生成されます。 次のスクリーンショットに示すように、生成されたトークンの URI バージョンは後で必要になります。

![Log Replay Service により生成された SAS 認証 URI の例](./media/log-replay-service-migrate/lrs-generated-uri-token.png)

### <a name="copy-parameters-from-sas-token-generated"></a>生成された SAS トークンからパラメーターをコピーする

LRS を開始するために SAS トークンを適切に使用できるようにするには、その構造を理解する必要があります。 生成された SAS トークンの URI は、次の 2 つの部分で構成されます。
- StorageContainerUri と 
- StorageContainerSasToken (次の図に示すように疑問符 (?) で区切られている)。

    ![Log Replay Service により生成された SAS 認証 URI の例](./media/log-replay-service-migrate/lrs-token-structure.png)

- "https://" から疑問符 (?) までが前半部分で、LRS への入力として指定する StorageContainerURI パラメーターに使用します。 これにより、データベース バックアップ ファイルが格納されているフォルダーの情報が LRS に提供されます。
- 疑問符 (?) の後から文字列の終わりまで (例では "sp=") が後半部分で、StorageContainerSasToken パラメーターです。 これが実際の署名付き認証トークンで、指定した期間有効です。 この部分は、必ずしも例のように "sp =" で始まる必要はなく、場合によって異なります。

パラメーターを次のようにコピーします。

1. 次のスクリーンショットに示すように、LRS を開始するため、トークンの前半部分 (https:// から疑問符 (?) まで) をコピーして、PowerShell または CLI の StorageContainerUri パラメーターとして使用します。

    ![Log Replay Service で、StorageContainerUri パラメーターをコピーする](./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png)

2. 次のスクリーンショットに示すように、LRS を開始するため、トークンの後半部分 (疑問符 (?) から文字列の終わりまで) をコピーして、PowerShell または CLI の StorageContainerSasToken パラメーターとして使用します。

    ![Log Replay Service で、StorageContainerSasToken パラメーターをコピーする](./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png)

> [!IMPORTANT]
> - Azure Blob Storage に対する SAS トークンのアクセス許可は、読み取りとリストのみにする必要があります。 SAS 認証トークンに他のアクセス許可が付与されている場合、LRS サービスの開始は失敗します。 これらのセキュリティ要件は仕様です。
> - トークンには、適切な有効期間が設定されている必要があります。 トークンとマネージド インスタンスの間のタイム ゾーンが確実に考慮されるようにします。
> - PowerShell または CLI の StorageContainerUri パラメーターには、生成されたトークンの URI から、 https:// から疑問符 (?) までの部分が確実にコピーされるようにします。 疑問符は含めないでください。
> PowerShell または CLI の StorageContainerSasToken パラメーターには、生成されたトークンから、疑問符 (?) から文字列の終わりまでの部分が確実にコピーされるようにします。 疑問符は含めないでください。

### <a name="log-in-to-azure-and-select-subscription"></a>Azure にログインしてサブスクリプションを選択する

次の PowerShell コマンドレットを使用して Azure にログインします。

```powershell
Login-AzAccount
```

次の PowerShell コマンドレットを使用して、SQL Managed Instance が存在する適切なサブスクリプションを選択します。

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>移行を開始する

LRS サービスを開始すると、移行が開始されます。 このサービスはオートコンプリートまたは連続モードで開始できます。 オートコンプリート モードで開始した場合は、指定した最後のバックアップ ファイルが復元されると、移行が自動的に完了します。 このオプションでは、start コマンドに最後のバックアップ ファイルのファイル名を指定する必要があります。 LRS を連続モードで開始した場合は、新しいバックアップ ファイルが追加されると、サービスによって連続的に復元され、移行は手動カットオーバーでのみで完了します。 

### <a name="start-lrs-in-autocomplete-mode"></a>LRS をオートコンプリート モードで開始する

LRS サービスをオートコンプリート モードで開始するには、次の PowerShell または CLI コマンドを使用します。 -LastBackupName パラメーターを使用して、最後のバックアップ ファイル名を指定します。 指定された最後のバックアップ ファイル名を復元すると、サービスにより、カットオーバーが自動的に開始されます。

LRS をオートコンプリート モードで開始する - PowerShell の例:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

LRS をオートコンプリート モードで開始する - CLI の例:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>LRS を連続モードで開始する

LRS を連続モードで開始する - PowerShell の例:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

LRS を連続モードで開始する - CLI の例:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="scripting-lrs-start-in-continuous-mode"></a>スクリプト LRS を連続モードで開始する

PowerShell および CLI クライアントで LRS を連続モードで開始すると、同期されます。 これは、ジョブ開始の成否が API 応答により報告されるまでクライアントが待機することを意味します。 この待機中、制御は、コマンドによってコマンド プロンプトに戻されません。 移行操作のスクリプトを実行していて、LRS start コマンドで、スクリプトの残りを続行するためにすぐに制御を戻す必要がある場合は、-AsJob スイッチを使用して PowerShell をバックグラウンド ジョブとして実行できます。 次に例を示します。

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

バックグラウンド ジョブを開始すると、ジョブが終了するまでに時間がかかる場合でも、ジョブ オブジェクトがすぐに返されます。 ジョブの実行中は、中断されることなく引き続きセッションで作業できます。 PowerShell をバックグラウンド ジョブとして実行する方法の詳細については、[PowerShell Start-Job](/powershell/module/microsoft.powershell.core/start-job#description) に関するドキュメントを参照してください。

同様に、Linux 上で CLI コマンドをバックグラウンド プロセスとして開始するには、LRS start コマンドの最後にアンパサンド (&) 記号を使用します。

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> LRS が開始されると、システム管理されているソフトウェア パッチが 47 時間停止されます。 この時間枠を過ぎると、自動化された次のソフトウェア パッチによって、進行中の LRS が自動的に停止されます。 このような場合、移行を再開することはできず、最初から再起動する必要があります。 

## <a name="monitor-the-migration-progress"></a>移行の進行状況を監視する

移行操作の進行状況を監視するには、次の PowerShell コマンドを使用します。

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

移行操作の進行状況を監視するには、次の CLI コマンドを使用します。

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>移行を停止する

移行を停止する必要がある場合は、次のコマンドレットを使用します。 移行を停止すると、SQL Managed Instance で復元中のデータベースが削除されるため、移行を再開することはできなくなります。

移行プロセスを停止 (中止) するには、次の PowerShell コマンドを使用します。

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

移行プロセスを停止 (中止) するには、次の CLI コマンドを使用します。

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>移行を完了する (連続モード)

LRS が連続モードで開始された場合、すべてのバックアップが確実に復元されたら、カットオーバーを開始すると移行が完了します。 カットオーバーが完了すると、データベースが移行され、読み取りと書き込みのアクセスの準備が整います。

LRS 連続モードで移行プロセスを完了するには、次の PowerShell コマンドを使用します。

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

LRS 連続モードで移行プロセスを完了するには、次の CLI コマンドを使用します。

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>機能制限

Log Replay Service (LRS) の機能制限は次のとおりです。
- 復元中のデータベースを、移行プロセス中に読み取り専用アクセスに使用することはできません。
- LRS を開始すると、システム管理されているソフトウェア パッチが 47 時間ブロックされます。 この時間枠が終了すると、次のソフトウェア更新プログラムにより、LRS が停止されます。 このような場合は、LRS を最初から再起動する必要があります。
- LRS では、SQL Server のデータベースを CHECKSUM オプションを有効にしてバックアップする必要があります。
- LRS で使用される SAS トークンは Azure Blob Storage コンテナー全体に対して生成される必要があり、読み取りとリストのアクセス許可のみが必要です。
- 異なるデータベースのバックアップ ファイルは、Azure Blob Storage 上の個別のフォルダーに配置する必要があります。
- LRS を Azure Blob Storage 上のバックアップ ファイルがある個別のフォルダーを指すデータベースごとに個別に開始する必要があります。
- LRS は、1 つの SQL Managed Instance ごとに最大 100 個の同時復元プロセスをサポートできます。

## <a name="troubleshooting"></a>トラブルシューティング

LRS を開始したら、監視コマンドレット (get-azsqlinstancedatabaselogreplay または az_sql_midb_log_replay_show) を使用して、操作の状態を確認します。 しばらくしても LRS がエラーで開始できない場合は、最も一般的な問題のいくつかをご確認ください。
- SQL Server から移行しようとしている SQL MI に同じ名前のデータベースが既に存在していますか? この競合を解決するには、一方のデータベースの名前を変更します。
- SQL Server のデータベース バックアップは、**CHECKSUM** オプションを使用して作成しましたか?
- LRS サービスに使用する SAS トークンには、**読み取り** と **リスト** のアクセス許可のみが付与されていますか?
- LRS に使用した SAS トークンに、疑問符 "?" の後の "sv=2020-02-10..." のように始まるコンテンツをコピーしましたか? 
- 移行の開始と完了の時間枠に、SAS **トークンの有効期間** が適用されていますか? SQL Managed Instance と SAS トークンに使用される **タイム ゾーン** が異なることにより、不一致が発生するおそれがあります。 時間枠のトークンの有効期間を現在の日付より前および後に拡張して、SAS トークンを再生成してみてください。
- データベース名、リソース グループ名、マネージド インスタンス名のスペルは正しいですか?
- LRS がオートコンプリート モードで開始された場合、最後のバックアップ ファイルに有効なファイル名を指定しましたか?

## <a name="next-steps"></a>次のステップ
- [SQL Server から SQL Managed Instance に移行する](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)について学ぶ。
- [SQL Server と Azure SQL Managed Instance の相違点](transact-sql-tsql-differences-sql-server.md)について学ぶ。
- [Azure に移行するワークロードのコストとサイズ設定のベスト プラクティス](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)について学ぶ。
