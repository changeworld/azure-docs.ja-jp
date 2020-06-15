---
title: 自動の geo 冗長バックアップ
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database および Azure SQL Managed Instance は数分ごとにローカル データベースを自動的にバックアップし、Azure 読み取りアクセス geo 冗長ストレージを利用して地理的冗長性を提供します。
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 12/13/2019
ms.openlocfilehash: a99045359d9d6c6a327ef646ba15457c3e4fbbda
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/02/2020
ms.locfileid: "84299810"
---
# <a name="automated-backups---azure-sql-database--sql-managed-instance"></a>自動バックアップ - Azure SQL Database および SQL Managed Instance

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database と Azure SQL Managed Instance のどちらでも、構成された保有期間にわたって保存されるデータベース バックアップが作成されます。 Azure の[読み取りアクセス geo 冗長ストレージ (RA-GRS)](../../storage/common/storage-redundancy.md) を使用して、データセンターが使用できない場合でもバックアップが確実に保存されます。

データの不慮の破損または削除から保護するデータベース バックアップは、ビジネス継続性およびディザスター リカバリー戦略の最も重要な部分です。 セキュリティ規則で、バックアップを長期間 (最長 10 年間) 利用できるようにする必要がある場合は、単一データベースとプールされたデータベースの両方で[長期保有](long-term-retention-overview.md)を構成できます。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>SQL Database バックアップとは何か。

SQL Database と SQL Managed Instance は SQL Server 技術を利用して、[完全バックアップ](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server)を毎週、[差分バックアップ](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server)を 12 時間ごと、そして[トランザクション ログ バックアップ](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server)を 5 から 10 分ごとに作成します。 バックアップは [RA-GRS ストレージ BLOB](../../storage/common/storage-redundancy.md) に格納され、この BLOB はデータセンターの停止に対する保護のために[ペアのデータセンター](../../best-practices-availability-paired-regions.md)にレプリケートされます。 データベースを復元するとき、復元する必要がある完全バックアップ、差分バックアップ、トランザクション ログ バックアップはどれであるかがサービスによって判定されます。

これらのバックアップを使用して、以下を行うことができます。

- 保有期間内の**過去の特定の時点に既存のデータベースを復元する**。その際、Azure portal、Azure PowerShell、Azure CLI、または REST API を使用します。 単一データベースおよびプールされたデータベースでは、この操作により、元のデータベースと同じサーバーの同じサブスクリプションに新しいデータベースが作成されます。 マネージド インスタンスでは、この操作により、同じサブスクリプションに、データベースのコピーか、同じマネージド インスタンスまたは異なるマネージド インスタンスのコピーを作成できます。
- **削除したデータベースを削除の時点に復元する**。また、保有期間内の任意の時点に復元することもできます。 削除されたデータベースは、元のデータベースが作成されたサーバーまたはマネージド インスタンスの同じサブスクリプションにのみ復元できます。
- **別の地理的リージョンにデータベースを復元する**。 geo リストアにより、サーバーやデータベースにアクセスできないときに、地理的な障害から復旧できます。 世界中のどこでも、あらゆる既存のサーバーで新しいデータベースを作成します。
- **特定の長期バックアップからデータベースを復元する**。データベースに長期保有ポリシー (LTR) が構成されている場合に、単一データベースまたはプールされたデータベースで復元できます。 LTR により、[Azure portal](long-term-backup-retention-configure.md#using-azure-portal) または [Azure PowerShell](long-term-backup-retention-configure.md#using-powershell) を使用して、コンプライアンスの要求を満たすため、またはアプリケーションの以前バージョンを実行するために、以前のバージョンのデータベースを復元できます。 詳細については、「[長期保存](long-term-retention-overview.md)」をご覧ください。

復元を実行するには、[バックアップからのデータベースの復元](recovery-using-backups.md)に関する記事を参照してください。

> [!NOTE]
> Azure Storage の "*レプリケーション*" という用語は、ある場所から別の場所にファイルをコピーすることを表します。 Azure SQL Database と SQL Managed Instance では、"*データベース レプリケーション*" は、複数のセカンダリ データベースとプライマリ データベースとの同期を保つことを意味します。

次の例を使用して、これらの操作のいくつかを試すことができます。

| | Azure ポータル | Azure PowerShell |
|---|---|---|
| バックアップ保有期間を変更する | [1 つのデータベース](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) <br/> [マネージド インスタンス](automated-backups-overview.md?tabs=managed-instance#change-the-pitr-backup-retention-period-by-using-the-azure-portal) | [1 つのデータベース](automated-backups-overview.md#change-the-pitr-backup-retention-period-by-using-powershell) <br/>[マネージド インスタンス](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| 長期的なバックアップ保有期間を変更する | [1 つのデータベース](long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>マネージド インスタンス - N/A  | [1 つのデータベース](long-term-backup-retention-configure.md)<br/>マネージド インスタンス - N/A  |
| 特定の時点からデータベースを復元する | [1 つのデータベース](recovery-using-backups.md#point-in-time-restore) | [1 つのデータベース](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [マネージド インスタンス](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| 削除されたデータベースの復元 | [1 つのデータベース](recovery-using-backups.md) | [1 つのデータベース](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [マネージド インスタンス](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Azure BLOB ストレージからデータベースを復元する | Single Database - N/A <br/>マネージド インスタンス - N/A  | Single Database - N/A <br/>[マネージド インスタンス](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |

## <a name="backup-frequency"></a>バックアップ頻度

### <a name="point-in-time-restore"></a>ポイントインタイム リストア

SQL Database と SQL Managed Instance では、完全バックアップ、差分バックアップ、およびトランザクション ログ バックアップが自動的に作成され、ポイントインタイム リストア (PITR) のセルフサービスがサポートされています。 データベースの完全バックアップは毎週作成され、データベースの差分バックアップは一般的に 12 時間ごとに作成されます。 トランザクション ログ バックアップは、通常、5 から 10 分ごとに作成されます。 トランザクション ログ バックアップの頻度は、コンピューティング サイズとデータベース アクティビティの量に基づいて決まります。

初回の完全バックアップは、データベースの作成直後にスケジュールされます。 通常このバックアップは 30 分以内に終了しますが、データベースが大きい場合はそれ以上かかることがあります。 たとえば、復元されたデータベースまたはデータベースのコピーでは、初期バックアップに時間がかかります。 初回の完全バックアップ以降のバックアップは、すべて自動的にスケジュールされ、バックグラウンドで自動的に管理されます。 すべてのデータベースのバックアップの正確なタイミングは、全体的なシステムのワークロードのバランスを図りながら、SQL Database または SQL Managed Instance によって決定されます。 バックアップ ジョブを変更または無効化することはできません。

### <a name="default-backup-retention-period"></a>既定のバックアップの保有期間

PITR バックアップは、geo 冗長ストレージで保護されています。 詳細については、「[Azure Storage の冗長性](../../storage/common/storage-redundancy.md)」をご覧ください。

PITR の詳細については、「[ポイントインタイム リストア](recovery-using-backups.md#point-in-time-restore)」を参照してください。

### <a name="long-term-retention"></a>長期保存

単一データベースとプールされたデータベースには、Azure Blob Storage で最大 10 年間にわたる完全バックアップを行う長期保有 (LTR) を構成できます。 LTR ポリシーを有効にした場合、毎週の完全バックアップは自動的に別の RA-GRS ストレージ コンテナーにコピーされます。 各種のコンプライアンス要件を満たすために、毎週、毎月、毎年のバックアップに対してさまざまな保有期間を選択できます。 ストレージの使用量は、選択したバックアップの頻度と保有期間によって異なります。 LTR ストレージのコストは、[LTR 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=sql-database)を使用して見積もることができます。

PITR バックアップなどの LTR バックアップは、geo 冗長ストレージで保護されています。 詳細については、「[Azure Storage の冗長性](../../storage/common/storage-redundancy.md)」をご覧ください。

LTR の詳細については、[バックアップの長期保有](long-term-retention-overview.md)に関するページを参照してください。

## <a name="backup-storage-consumption"></a>バックアップ ストレージ消費量

単一データベースおよびマネージド インスタンスの場合、バックアップ ストレージ合計使用量の計算にはこの式が使用されます。

`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

プールされたデータベースの場合、バックアップ ストレージの合計サイズはプール レベルで集計され、次のように計算されます。

`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage`

保有期間より前に作成されたバックアップは、タイムスタンプに基づいて自動的に消去されます。 差分バックアップとログ バックアップも毎週まとめて消去されます。これらが有用であるためには、先行する完全バックアップが必要なためです。

SQL Database および SQL Managed Instance では、保持期間内のバックアップ ストレージの合計が累積値として計算されます。 この累積値が 1 時間おきに Azure 課金パイプラインに報告されます。この時間あたり使用量がパイプラインによって集計されて、毎月末に消費量が計算されます。 データベースの削除後は、バックアップの時間経過と共に消費量が減少します。 バックアップが保有期間を過ぎたら、課金が停止します。
   
   > [!IMPORTANT]
   > データベースのバックアップは、データベースが削除されている場合でも、指定した保持期間にわたって保持されます。 データベースを削除して再作成すると、多くの場合にストレージやコンピューティングのコストを削減できますが、削除された各データベースについて、削除されるたびに、指定された保有期間にわたり Microsoft によってバックアップが保持されるため、バックアップ ストレージのコストが増加する可能性があります。 

### <a name="monitor-consumption"></a>消費量の監視

各種バックアップ (完全、差分、ログ) はデータベース監視ブレード上で別個のメトリックとして報告されます。 次の図では、単一データベースのバックアップ ストレージ消費量の監視方法が示されています。 この機能は、マネージド インスタンスでは現在使用できません。

![Azure portal でのデータベース バックアップ消費量の監視](./media/automated-backups-overview/backup-metrics.png)

### <a name="fine-tune-backup-storage-consumption"></a>バックアップ ストレージ消費量を微調整する

超過のバックアップ ストレージ消費量は、個々のデータベースのワークロードとサイズに依存します。 バックアップ ストレージ消費量を減らすには、次の調整手法のいくつかを検討してください。

- 必要最小限まで[バックアップの保持期間](#change-the-pitr-backup-retention-period-by-using-the-azure-portal)を短縮します。
- インデックスの再構築などの大規模な書き込み操作を、必要以上に頻繁に行わないようにします。
- 大規模なデータ読み込み操作の場合、[クラスター化された列ストア インデックス](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)の使用を検討し、クラスター化されていないインデックスの数を減らして、100 万ほどの行数での一括読み込み操作を検討します。
- 汎用サービス レベルでは、プロビジョニングされたデータ ストレージの方が、超過のバックアップ ストレージの価格よりも安価です。 超過のバックアップ ストレージのコストが継続的に増加している場合は、データ ストレージを増やしてバックアップ ストレージを節約することを検討してください。
- 一時的な結果の保存には、パーマネント テーブルではなく TempDB を ETL ロジックで使用します (SQL Managed Instance にのみ該当)。
- 機密データが含まれないデータベース (開発またはテスト データベースなど) の TDE 暗号化をオフにすることを検討します。 暗号化されていないデータベースのバックアップを圧縮するときは、通常、圧縮率が高くなります。

> [!IMPORTANT]
> 分析のデータ マートまたはデータ ウェアハウス ワークロードの場合、[クラスター化された列ストア インデックス](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)を使用し、クラスター化されていないインデックスの数を減らすこと、また、100 万ほどの行数での一括読み込み操作を検討し、超過のバックアップ ストレージ消費量を減らすことを強くお勧めします。

## <a name="storage-costs"></a>ストレージ コスト

ストレージの料金は、DTU モデルと仮想コア モデルのどちらを使用しているかによって異なります。

### <a name="dtu-model"></a>DTU モデル

DTU モデルを使用している場合、データベースおよびエラスティック データベース プールについては、バックアップ ストレージに追加請求はありません。

### <a name="vcore-model"></a>仮想コア モデル

SQL Database の単一データベースの場合は、データベース サイズの 100% に等しい最小バックアップ ストレージ容量が、追加料金なしで提供されます。 SQL Database のエラスティック プールと、SQL Managed Instance の単一インスタンスおよびインスタンス プールの場合は、プールまたはインスタンス サイズにそれぞれ割り当てられたデータ ストレージの 100% に等しい最小バックアップ ストレージ容量が、追加料金なしで提供されます。 100% を超えるバックアップ ストレージの使用については、月あたりの GB 数で請求されます。 この追加の消費量は、個々のデータベースのワークロードとサイズによって異なります。

SQL Database および SQL Managed Instance では、保持期間内のバックアップ ストレージの合計が累積値として計算されます。 この累積値が 1 時間おきに Azure 課金パイプラインに報告されます。この時間あたり使用量がパイプラインによって集計されて、毎月末に消費量が算出されます。 データベースの削除後は、バックアップの時間経過と共に消費量が減少します。 バックアップが保有期間を過ぎたら、課金が停止します。 ログ バックアップと差分バックアップはすべて、保有期間全体にわたり保持されるため、大幅に変更されたデータベースはバックアップ料金が高くなります。

データベースのバックアップ ストレージが累積で 744 GB になったとします。この量はまる 1 か月にわたり変化しません。 この累積ストレージ消費量を 1 時間あたりの使用量に変換するには、744.0 (1 か月 31 日 * 1 日 24 時間) で割ります。 したがって、SQL Database からは、データベースで 1 時間あたり 1 GB の PITR バックアップを消費したと報告されます。 Azure の課金では、この消費量を集計し、1 か月全体で 744 GB という使用量が示されます。 コストは、使っているリージョンでの GB 単位の月額料金に基づいて算出されます。

次の例はもっと複雑になります。 データベースの保有期間が、月の途中で 14 日間に増やされたとします。 この増加 (仮に) により、バックアップ ストレージの合計が 1,488 GB に倍増したとしましょう。 SQL Database によって、1 時間から 372 時間までの使用量が 1 GB と報告されます。 373 時間から 744 時間の使用量は 2 GB と報告されます。 この使用量が集計されて最終的な請求は 1,116 GB/月になります。

### <a name="monitor-costs"></a>コストを監視する

バックアップ ストレージのコストを把握するには、Azure portal の **[コストの管理と請求]** にアクセスし、 **[コスト管理]** を選択してから、 **[コスト分析]** を選択します。 **[スコープ]** として目的のサブスクリプションを選択し、目的の期間とサービスが得られるようにフィルター処理します。

**[サービス名]** のフィルターを追加し、ドロップダウン リストで **[sql database]** を選択します。 **[測定サブカテゴリ]** フィルターを使用して、サービスの課金カウンターを選択します。 単一データベースまたはエラスティック データベース プールの場合は、 **[single/elastic pool pitr backup storage]** を選択します。 マネージド インスタンスの場合は、 **[mi pitr backup storage]** を選択します。 **[ストレージ]** と **[コンピューティング]** のサブカテゴリも必要に応じて使用できますが、これらはバックアップ ストレージのコストに関連付けられていません。

![バックアップ ストレージのコスト分析](./media/automated-backups-overview/check-backup-storage-cost-sql-mi.png)

## <a name="backup-retention"></a>バックアップ保有期間

SQL Database および SQL Managed Instance のすべてのデータベースには、7 日間という既定のバックアップ保有期間が設定されています。 [バックアップ保有期間は、35 日にまで変更](#change-the-pitr-backup-retention-period)できます。

データベースを削除した場合、Azure はオンライン データベースの場合と同じようにバックアップを保持します。 たとえば、7 日間の保有期間が設定された Basic のデータベースを削除すると、4 日間保存されているバックアップはさらに 3 日間保存されます。

最大の保有期間より長くバックアップを保持する必要がある場合は、データベースに 1 つまたは複数のより長い保有期間を追加するようにバックアップのプロパティを変更できます。 詳細については、「[長期保存](long-term-retention-overview.md)」をご覧ください。

> [!IMPORTANT]
> バックアップ保有期間を 1 日 (または 1 から 7 までの任意の値) に設定することは、現時点では PowerShell または REST API 経由でのみサポートされています。 Az.SQL モジュールの最低限必要なバージョンは v2.6.0 です。また、常に最新の Az.SQL バージョンが備わっている CloudShell を通じて実行することもできます。

> [!IMPORTANT]
> サーバーまたはマネージド インスタンスを削除すると、そのサーバーまたはマネージド インスタンスによって管理されているすべてのデータベースも削除されます。 復旧はできません。 削除されたサーバーまたはマネージド インスタンスは復元できません。 一方、SQL Database またはマネージド インスタンスに対して長期保有を構成した場合は、LTR を使用したデータベースのバックアップは削除されず、これらのデータベースを復元することができます。

## <a name="encrypted-backups"></a>暗号化バックアップ

データベースが TDE で暗号化されている場合、LTR バックアップを含むバックアップは保存中に自動的に暗号化されます。 SQL Database または SQL Managed Instance に対して TDE が有効になっている場合は、バックアップも暗号化されます。 SQL Database および SQL Managed Instance のすべての新しいデータベースは、既定では TDE が有効になるように構成されます。 TDE の詳細については、[SQL Database および SQL Managed Instance での Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) に関するページを参照してください。

## <a name="backup-integrity"></a>バックアップの整合性

Azure SQL のエンジニアリング チームは、SQL Database のデータベースの自動データベース バックアップに対する復元の自動テストを継続的に行っています (このテストは SQL Managed Instance では使用できません)。ポイントインタイム リストア時に、データベースは DBCC CHECKDB の整合性チェックも受けます。

SQL Managed Instance では、移行の完了後、ネイティブ `RESTORE` コマンドまたは Azure Data Migration Service を利用して復元されたデータベースの `CHECKSUM` を含む自動初回バックアップが行われます。

整合性チェック中に問題が見つかると、エンジニアリング チームにアラートが送信されます。 詳細については、[SQL Database でのデータ整合性](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)に関するページを参照してください。

## <a name="compliance"></a>コンプライアンス

DTU ベースのサービス レベルから仮想コア ベースのサービス レベルにデータベースを移行した場合、アプリケーションのデータ回復ポリシーに違反しないように、PITR 保有期間が維持されます。 既定の保有期間でコンプライアンス要件が満たされない場合は、PowerShell または REST API を使用して PITR 保有期間を変更できます。 詳細については、「[PITR バックアップ保有期間を変更する](#change-the-pitr-backup-retention-period)」を参照してください。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-pitr-backup-retention-period"></a>PITR バックアップ保有期間を変更する

既定の PITR バックアップ保有期間は、Azure portal、PowerShell、または REST API を使用して変更できます。 次の例では、PITR 保有期間を 28 日間に変更する方法を示します。

> [!WARNING]
> 現在の保有期間を短縮した場合、新しい保有期間より古いすべての既存のバックアップは使用できなくなります。 現在の保有期間を延長した場合、Azure では、長くなった保有期間が終了するまで、既存のバックアップが保持されます。

> [!NOTE]
> これらの API は PITR 保有期間にのみ影響します。 データベースに LTR が構成されている場合、それには影響しません。 LTR の保有期間を変更する方法については、[長期保有](long-term-retention-overview.md)に関するページを参照してください。

### <a name="change-the-pitr-backup-retention-period-by-using-the-azure-portal"></a>Azure portal を使用して PITR バックアップ保有期間を変更する

Azure portal を使用して PITR バックアップ保有期間を変更するには、portal 上で、保有期間を変更するサーバー オブジェクトに移動します。 次に、変更するサーバー オブジェクトに基づいて、適切なオプションを選択します。

#### <a name="sql-database"></a>[SQL Database](#tab/single-database)

SQL Database の PITR バックアップ保有期間に対する変更は、サーバー レベルで実行されます。 サーバー レベルで行われた変更は、そのサーバー上のデータベースに適用されます。 Azure portal でサーバーの PITR 保有期間を変更するには、サーバーの概要ブレードに移動します。 左側のペインで **[バックアップの管理]** を選択し、画面の上部にある **[保有期間の構成]** を選択します。

![PITR 保有期間の変更 (サーバー レベル)](./media/automated-backups-overview/configure-backup-retention-sqldb.png)

#### <a name="sql-managed-instance"></a>[SQL Managed Instance](#tab/managed-instance)

SQL Managed Instance の PITR バックアップ保有期間の変更は、個々のデータベース レベルで実行されます。 Azure portal からインスタンス データベースの PITR バックアップ保有期間を変更するには、個々のデータベースの [概要] ブレードに移動します。 次に、画面の上部にある **[バックアップ保有期間の構成]** を選択します。

![PITR 保有期間の変更 (マネージド インスタンス)](./media/automated-backups-overview/configure-backup-retention-sqlmi.png)

---

### <a name="change-the-pitr-backup-retention-period-by-using-powershell"></a>PowerShell を使用して PITR バックアップ保有期間を変更する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell AzureRM モジュールは SQL Database と SQL Managed Instance によって引き続きサポートされていますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 詳細については、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールのコマンドの引数は、AzureRm モジュールのものと実質的に同じです。

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-the-pitr-backup-retention-period-by-using-the-rest-api"></a>REST API を使用して PITR バックアップ保有期間を変更する方法

#### <a name="sample-request"></a>要求のサンプル

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```

#### <a name="request-body"></a>要求本文

```json
{
  "properties":{
    "retentionDays":28
  }
}
```

#### <a name="sample-response"></a>応答のサンプル

状態コード:200

```json
{
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/providers/Microsoft.Sql/resourceGroups/resourceGroup/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default",
  "name": "default",
  "type": "Microsoft.Sql/resourceGroups/servers/databases/backupShortTermRetentionPolicies",
  "properties": {
    "retentionDays": 28
  }
}
```

詳細については、[バックアップの保有期間の REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- データの不慮の破損または削除から保護するデータベース バックアップは、ビジネス継続性およびディザスター リカバリー戦略の最も重要な部分です。 その他の SQL Database ビジネス継続性ソリューションの概要については、[ビジネス継続性の概要](business-continuity-high-availability-disaster-recover-hadr-overview.md)に関するページを参照してください。
- [Azure portal を使用してデータベースを特定の時点に復元する](recovery-using-backups.md)方法について、詳細を確認してください。
- [PowerShell を使用してデータベースを特定の時点に復元する](scripts/restore-database-powershell.md)方法について、詳細を確認してください。
- Azure Blob Storage に長期保有される自動バックアップを Azure portal を使用して構成、管理、復元する方法の詳細については、[Azure portal を使用した長期的なバックアップ保有期間の管理](long-term-backup-retention-configure.md)に関する記事を参照してください。
- Azure Blob Storage に長期保有される自動バックアップを PowerShell を使用して構成、管理、復元する方法については、[PowerShell を使用した長期的なバックアップ保有期間の管理](long-term-backup-retention-configure.md)に関する記事を参照してください。
