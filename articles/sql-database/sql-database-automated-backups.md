---
title: 自動の geo 冗長バックアップ
description: SQL Database は数分ごとにローカル データベースをバックアップし、Azure 読み取りアクセス geo 冗長ストレージを利用して地理的冗長性を提供します。
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab, danil
manager: craigg
ms.date: 12/13/2019
ms.openlocfilehash: 16ee8c1e271f0aa3e6565322f9a4a422dd90b8b8
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461776"
---
# <a name="automated-backups"></a>自動バックアップ

SQL Database では、設定されている保持期間にわたり保存されるデータベース バックアップが自動的に作成され、Azure の[読み取りアクセス geo 冗長ストレージ (RA-GRS)](../storage/common/storage-redundancy.md) を使用し、データ センターを使用できない場合でもデータベース バックアップが確実に保存されます。 これらのバックアップは自動的に作成されます。 データの不慮の破損または削除から保護するデータベース バックアップは、ビジネス継続性およびディザスター リカバリー戦略の最も重要な部分です。 セキュリティ規則で、長期間バックアップ (最長 10 年間) を利用できる必要がある場合は、Single Database と Elastic Pool で[長期保有](sql-database-long-term-retention.md)を構成できます。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>SQL Database バックアップとは

SQL Database は SQL Server 技術を利用して、[完全バックアップ](https://docs.microsoft.com/sql/relational-databases/backup-restore/full-database-backups-sql-server)を毎週、[差分バックアップ](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server)を 12 時間ごと、そして[トランザクション ログ バックアップ](https://docs.microsoft.com/sql/relational-databases/backup-restore/transaction-log-backups-sql-server)を 5 分から 10 分ごとに作成します。 バックアップは [RA-GRS ストレージ BLOB](../storage/common/storage-redundancy.md) に格納され、この BLOB はデータ センターの停止に対する保護のために[ペアのデータ センター](../best-practices-availability-paired-regions.md)にレプリケートされます。 データベースを復元するとき、どのバックアップを復元する必要があるかをサービスが判定します (完全、差分、トランザクション ログ)。

これらのバックアップを使用して、以下を行うことができます。

- **保持期間内の過去の特定の時点に既存のデータベースを復元する**。その際、Azure portal、Azure PowerShell、Azure CLI、または REST API を使用します。 Single Database および Elastic Pool では、この操作により、元のデータベースと同じサーバーに、新しいデータベースが作成されます。 Managed Instance では、この操作により、同じサブスクリプションに、データベースのコピーか、同じ Managed Instance または異なる Managed Instance のコピーを作成できます。
- **削除したデータベースを、削除された時点に復元する**。また、保持期間内の任意の時点に復元することもできます。 削除されたデータベースは、元のデータベースが作成された論理サーバーまたは Managed Instance にのみ復元できます。
- **別の地理的リージョンにデータベースを復元する**。 geo リストアにより、サーバーやデータベースにアクセスできないときに、地理的な障害から復旧できます。 世界中のどこでも、あらゆる既存のサーバーで新しいデータベースを作成します。
- **特定の長期バックアップからデータベースを復元する**。データベースが長期アイテム保持ポリシー (LTR) で構成されている場合に、Single Database または Elastic Pool で復元できます。 LTR により、[Azure portal](sql-database-long-term-backup-retention-configure.md#using-azure-portal) または [Azure PowerShell](sql-database-long-term-backup-retention-configure.md#using-powershell) を使用して、コンプライアンスの要求を満たすため、またはアプリケーションの古いバージョンを実行するために、古いバージョンのデータベースを復元できます。 詳細については、「[長期保存](sql-database-long-term-retention.md)」をご覧ください。
- 復元を実行するには、[バックアップからのデータベースの復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。

> [!NOTE]
> Azure Storage の " *レプリケーション* " という用語は、ある場所から別の場所にファイルをコピーすることを表します。 SQL の *データベース レプリケーション* は、複数のセカンダリ データベースとプライマリ データベースとの同期を保つことを意味します。

次の例を使用して、これらの操作のいくつかを試すことができます。

| | Azure ポータル | Azure PowerShell |
|---|---|---|
| バックアップ保有期間を変更する | [Single Database](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) <br/> [Managed Instance](sql-database-automated-backups.md?tabs=managed-instance#change-pitr-backup-retention-period-using-azure-portal) | [Single Database](sql-database-automated-backups.md#change-pitr-backup-retention-period-using-powershell) <br/>[Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasebackupshorttermretentionpolicy) |
| 長期のバックアップ保有期間を変更する | [1 つのデータベース](sql-database-long-term-backup-retention-configure.md#configure-long-term-retention-policies)<br/>Managed Instance - N/A  | [Single Database](sql-database-long-term-backup-retention-configure.md)<br/>Managed Instance - N/A  |
| 特定の時点からデータベースを復元する | [1 つのデータベース](sql-database-recovery-using-backups.md#point-in-time-restore) | [1 つのデータベース](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) <br/> [Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase) |
| 削除済みデータベースの復元 | [1 つのデータベース](sql-database-recovery-using-backups.md) | [1 つのデータベース](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeleteddatabasebackup) <br/> [Managed Instance](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldeletedinstancedatabasebackup)|
| Azure Blob Storage からデータベースを復元する | Single Database - N/A <br/>Managed Instance - N/A  | Single Database - N/A <br/>[Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) |


## <a name="backup-frequency"></a>バックアップ頻度

### <a name="point-in-time-restore"></a>ポイントインタイム リストア

SQL Database では、完全バックアップ、差分バックアップ、トランザクション ログ バックアップを自動的に作成して、ポイントインタイム リストア (PITR) のセルフ サービスをサポートします。 完全データベース バックアップは毎週、差分データベース バックアップは一般的に 12 時間ごとに、トランザクション ログ バックアップは通常、5 - 10 分ごとに作成されます。頻度は、コンピューティング サイズとデータベース アクティビティの量に基づきます。 初回の完全バックアップは、データベースの作成直後にスケジュールされます。 通常この操作は 30 分以内に終了しますが、データベースのサイズが大きい場合はそれ以上かかることがあります。 たとえば、復元されたデータベースまたはデータベースのコピーでは、初期バックアップに時間がかかります。 初回の完全バックアップ以降のバックアップは、すべて自動的にスケジュールされ、バックグラウンドで自動的に管理されます。 データベースのバックアップの正確なタイミングは、全体的なシステムのワークロードのバランスを図りながら SQL Database サービスによって決定されます。 バックアップ ジョブを変更または無効化することはできません。

PITR バックアップは、geo 冗長ストレージで保護されています。 詳細については、「[Azure Storage の冗長性](../storage/common/storage-redundancy.md)」をご覧ください。

詳細については、「[ポイントインタイム リストア](sql-database-recovery-using-backups.md#point-in-time-restore)」をご覧ください。

### <a name="long-term-retention"></a>長期保存

単一データベースとプールされたデータベースには、Azure Blob Storage に対する最大 10 年の完全バックアップの長期保有 (LTR) を構成するオプションが用意されています。 LTR ポリシーが有効になっている場合、週次の完全バックアップは自動的に別の RA-GRS ストレージ コンテナーにコピーされます。 さまざまなコンプライアンス要件を満たすために、毎週、毎月、毎年のバックアップに対して異なったリテンション期間を選択することができます。 ストレージの使用量は、選択したバックアップの頻度とリテンション期間によって異なります。 LTR ストレージのコストは、[LTR 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=sql-database)を使用して見積もることができます。

PITR などの LTR バックアップは、geo 冗長ストレージで保護されています。 詳細については、「[Azure Storage の冗長性](../storage/common/storage-redundancy.md)」をご覧ください。

詳細については、「[Long-term backup retention](sql-database-long-term-retention.md)」(長期バックアップ リテンション) をご覧ください。

## <a name="backup-storage-consumption"></a>バックアップ ストレージ消費量 

単一データベースの場合、バックアップ ストレージの合計使用量は次のように計算されます。   
`Total backup storage size = (size of full backups + size of differential backups + size of log backups) – database size`

エラスティック プールの場合、バックアップ ストレージの合計サイズはプール レベルで集計され、次のように計算されます。   
`Total backup storage size = (total size of all full backups + total size of all differential backups + total size of all log backups) - allocated pool data storage` 

保持期間を過ぎたバックアップはタイムスタンプに基づいて自動的に消去されます。 差分バックアップとログ バックアップにはそれに先行する完全バックアップが必要であるため、それらも毎週まとめて消去されます。 

Azure SQL Database では、保持期間内のバックアップ ストレージの合計が累積値として計算されます。 毎月末に消費量を計算する目的でこの時間あたり使用量を集計する役割を担う Azure 課金パイプラインにこの累積値が 1 時間おきに報告されます。 データベースの削除後は、バックアップの時間経過と共に消費量が減少します。 バックアップが保持期間を過ぎると、課金が停止します。 

   > [!IMPORTANT]
   > データベースのバックアップは、データベースが削除されている場合でも、指定した保持期間にわたって保持されます。 データベースを頻繁に削除して再作成すると、ストレージやコンピューティングのコストが削減される可能性がありますが、削除された各データベースについて、削除されるたびに、指定された保持期間 (少なくとも 7 日) にわたってバックアップが保持されるため、バックアップ ストレージのコストが増加する可能性があります。 



### <a name="monitor-consumption"></a>消費量の監視

各種バックアップ (完全、差分、ログ) はデータベース監視ブレード上で別個のメトリックとして報告されます。 次の図では、単一データベースのバックアップ ストレージ消費量の監視方法が示されています。 この機能は、マネージド インスタンスでは現在使用できません。

![Azure portal のデータベース監視ブレードでデータベース バックアップ消費量を監視する](media/sql-database-automated-backup/backup-metrics.png)

### <a name="fine-tune-the-backup-storage-consumption"></a>バックアップ ストレージ消費量を微調整する

超過のバックアップ ストレージ消費量は、個々のデータベースのワークロードとサイズに依存します。 バックアップ ストレージ消費量をさらに減らす目的で、次の調整手法の中からいくつかを実践できます。

* 必要最小限まで[バックアップの保持期間](#change-pitr-backup-retention-period-using-azure-portal)を短縮します。
* インデックスの再構築など、大規模な書き込み操作を必要以上に行うことを回避します。
* 大規模なデータ読み込み操作の場合、[クラスター化された列ストア インデックス](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)の使用を検討し、クラスター化されていないインデックスの数を減らし、また、約 100 万の行数の一括読み込み操作を検討します。
* General Purpose サービス レベルでは、プロビジョニングされたデータ ストレージは超過のバックアップ ストレージより安価になります。そのため、超過のバックアップ ストレージにかかるコストが連続して高いお客様は、バックアップ ストレージを節約する目的でデータ ストレージを増加することを検討できます。
* 一時的な結果の保存には、パーマネント テーブルではなく TempDB を ETL ロジックで使用します (マネージド インスタンスのみ該当)。
* 機密データが含まれないデータベース (開発またはテスト データベースなど) の TDE 暗号化をオフにすることを検討します。 暗号化されていないデータベースのバックアップを圧縮するときは、通常、圧縮率が高くなります。

> [!IMPORTANT]
> 分析のデータ マート/データ ウェアハウス ワークロードの場合、[クラスター化された列ストア インデックス](https://docs.microsoft.com/sql/database-engine/using-clustered-columnstore-indexes)を使用し、クラスター化されていないインデックスの数を減らし、さらに、100 万くらいの行数による一括読み込み操作を検討し、超過のバックアップ ストレージ消費量を減らすことを強くお勧めします。


## <a name="storage-costs"></a>ストレージ コスト

ストレージの料金は、DTU モデルと仮想コア モデルのどちらを使用しているかによって異なります。 

### <a name="dtu-model"></a>DTU モデル

DTU モデルを使用したデータベースおよびエラスティック プールについては、バックアップ ストレージに追加請求はありません。 

### <a name="vcore-model"></a>仮想コア モデル

単一のデータベースの場合は、データベース サイズの 100% に等しい最小バックアップ ストレージ容量が、追加料金なしで提供されます。 エラスティック プールとマネージド インスタンスの場合は、プールまたはインスタンス サイズにそれぞれ割り当てられたデータ ストレージの 100% に等しい最小バックアップ ストレージ容量が追加料金なしで提供されます。 100% を超えるバックアップ ストレージの使用については、月あたりの GB 数で請求されます。 この追加の消費量は、個々のデータベースのワークロードとサイズによって異なります。

Azure SQL DB では、保持期間内のバックアップ ストレージの合計が累積値として計算されます。 毎月末に消費量を取得する目的でこの時間あたり使用量を集計する役割を担う Azure 課金パイプラインにこの累積値が 1 時間おきに報告されます。 データベースの削除後は、バックアップの時間経過と共に消費量を減少させます。 保持期間を過ぎると、課金が停止します。 ログ バックアップと差分バックアップはすべて、保持期間全体にわたり保持されるため、大幅に変更されたデータベースのバックアップ料金が高くなります。 

データベースのバックアップ ストレージが累積で 744 GB になったとします。この量はまる 1 か月にわたり変化しません。 この累積ストレージ消費量を 1 時間あたりの使用量に変換するには、それを 744.0 (1 か月 31 日 * 1 日 24 時間) で割ります。 したがって、SQL DB からは、データベースは 1 時間あたり 1 GB の PITR バックアップを消費したと報告されます。 Azure の課金機能によりこれが集計され、まる 1 か月間の使用量として 744 GB が表示され、ご利用のリージョンでの $/GB/月レートに基づいてコストが表示されます。 

次の例はもっと複雑になります。 月の真ん中にデータベースの保持期間を 14 日間に増やしたとします。その結果、(仮説上) バックアップ ストレージの合計が 2 倍の 1,488 GB になります。 SQL DB により 1 から 372 時間に対して 1 GB の使用量が、373 から 744 時間に対して 2 GB の使用量が報告されます。 これが集計されると最終的な請求は 1,116 GB/月になります。 

### <a name="monitor-costs"></a>コストを監視する

バックアップ ストレージのコストを把握するには、Azure portal の **コストの管理と請求** にアクセスし、**コスト管理** を選択してから、**コスト分析** を選択します。 **[スコープ]** として目的のサブスクリプションを選択し、目的の期間とサービスが得られるようにフィルター処理します。 

**[サービス名]** のフィルターを追加し、ドロップダウンから **sql database** を選択します。 **[測定サブカテゴリ]** フィルターを使用して、サービスの課金カウンターを選択します。 単一データベースまたはエラスティック プールの場合は、 **[single/elastic pool pitr backup storage]** を選択します。 マネージド インスタンスの場合は、 **[mi pitr backup storage]** を選択します。 **[ストレージ]** と **[コンピューティング]** のサブカテゴリは、バックアップ ストレージのコストに関連付けられていませんが、役に立つ場合があります。 

![バックアップ ストレージのコスト分析](./media/sql-database-automated-backup/check-backup-storage-cost-sql-mi.png)


## <a name="backup-retention"></a>バックアップ保有期間

すべての Azure SQL データベース (単一、プール、マネージド インスタンス データベース) には、**7** 日間という既定のバックアップ保持期間が設定されています。 [バックアップのリテンション期間を最大 35 日間まで変更](#change-pitr-backup-retention-period)できます。

データベースを削除した場合、SQL Database はオンライン データベースの場合と同じようにバックアップを保持します。 たとえば、7 日間のリテンション期間のある基本的なデータベースを削除すると、4 日間保存されているバックアップはさらに 3 日間保存されます。

最大の保有期間より長くバックアップを保持する必要がある場合は、データベースに 1 つまたは複数のより長い保有期間を追加するようにバックアップのプロパティを変更できます。 詳細については、「[長期保存](sql-database-long-term-retention.md)」をご覧ください。

> [!IMPORTANT]
> SQL Database をホストする Azure SQL サーバーを削除すると、サーバーに属するすべてのエラスティック プールとデータベースも削除され、復元できなくなります。 削除されたサーバーを復元することはできません。 長期保有を構成した場合、LTR を使用したデータベースのバックアップは削除されず、これらのデータベースを復元することができます。

## <a name="encrypted-backups"></a>暗号化バックアップ

データベースが TDE で暗号化されている場合、LTR バックアップを含むバックアップは保存中に自動的に暗号化されます。 Azure SQL データベースに対して TDE が有効になっているとき、バックアップも暗号化されます。 新しい Azure SQL データベースはすべて、既定で TDE が有効になった状態で構成されます。 TDE に関する詳細については、「[Azure SQL Database での Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)」をご覧ください。

## <a name="backup-integrity"></a>バックアップの整合性

Azure SQL Database のエンジニアリング チームは、論理サーバーとエラスティック プールに置かれているデータベースの自動データベース バックアップの復元の自動テストを継続的に行っています (これはマネージド インスタンスでは利用できません)。 特定時点への復元時に、データベースは DBCC CHECKDB を使用した整合性チェックも受けます。

マネージド インスタンスでは、以降の完了後、ネイティブ `CHECKSUM` コマンドまたはデータ移行サービスを利用して復元されたデータベースの `RESTORE` を含む自動初回バックアップが受け取られます。

整合性チェック中に問題が見つかると、エンジニアリング チームにアラートが送信されます。 Azure SQL Database におけるデータ整合性の詳細については、「[Data Integrity in Azure SQL Database](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/)」 (Azure SQL Database でのデータ整合性) を参照してください。

## <a name="compliance"></a>コンプライアンス

DTU ベースのサービス レベルから仮想コア ベースのサービス レベルにデータベースを移行した場合、アプリケーションのデータ リカバリ ポリシーに違反しないように、PITR リテンション期間が保持されます。 既定のリテンション期間では、お客様のコンプライアンス要件を満たしていない場合は、PowerShell または REST API を使用して PITR リテンション期間を変更できます。 詳細については、[バックアップのリテンション期間の変更](#change-pitr-backup-retention-period)に関するセクションを参照してください。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="change-pitr-backup-retention-period"></a>PITR バックアップ保持期間の変更

既定の PITR バックアップ保持期間は、Azure portal、PowerShell、または REST API を使用して変更できます。 次の例では、PITR リテンション期間を 28 日間に変更する方法を示します。

> [!WARNING]
> 現在の保持期間を短縮した場合、新しい保持期間より古いすべての既存のバックアップは使用できなくなります。 現在のリテンション期間を延長した場合、SQL Database は、より長いリテンション期間に達するまでに、既存のバックアップを保持します。

> [!NOTE]
> これらの API は PITR リテンション期間にのみ影響を与えます。 データベースの LTR を構成した場合、それには影響ありません。 LTR の保持期間を変更する方法については、[長期保有](sql-database-long-term-retention.md)に関するページを参照してください。

### <a name="change-pitr-backup-retention-period-using-azure-portal"></a>Azure portal を使用して PITR のバックアップ保有期間を変更する

Azure portal を使用して PITR バックアップ保有期間を変更するには、portal 内で保持期間を変更するサーバー オブジェクトに移動し、変更するサーバー オブジェクトに基づいて適切なオプションを選択します。

#### <a name="single-database--elastic-pools"></a>[単一データベースとエラスティック プール](#tab/single-database)

単一の Azure SQL Database に対する PITR バックアップ保有期間の変更は、サーバー レベルで実行されます。 サーバー レベルで行われた変更は、そのサーバー上のデータベースに適用されます。 Azure SQL Database サーバーの PITR を Azure portal から変更するには、サーバーの概要ブレードに移動し、ナビゲーション メニューの [バックアップの管理] をクリックして、ナビゲーション バーの [保有期間の構成] をクリックします。

![Azure portal の PITR の変更](./media/sql-database-automated-backup/configure-backup-retention-sqldb.png)

#### <a name="managed-instance"></a>[Managed Instance](#tab/managed-instance)

SQL Database マネージド インスタンスの PITR バックアップ保有期間の変更は、個々のデータベース レベルで実行されます。 Azure portal からインスタンス データベースの PITR バックアップ保有期間を変更するには、個々のデータベースの概要ブレードに移動し、ナビゲーション バーの [バックアップ保有期間の構成] をクリックします。

![Azure portal の PITR の変更](./media/sql-database-automated-backup/configure-backup-retention-sqlmi.png)

---

### <a name="change-pitr-backup-retention-period-using-powershell"></a>PowerShell を使用して PITR バックアップ リテンション期間を変更する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager モジュールは Azure SQL Database で引き続きサポートされますが、今後の開発はすべて Az.Sql モジュールを対象に行われます。 これらのコマンドレットについては、「[AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)」を参照してください。 Az モジュールと AzureRm モジュールのコマンドの引数は実質的に同じです。

```powershell
Set-AzSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```

### <a name="change-pitr-retention-period-using-rest-api"></a>REST API を使用して PITR リテンション期間を変更する

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

- データの不慮の破損または削除から保護するデータベース バックアップは、ビジネス継続性およびディザスター リカバリー戦略の最も重要な部分です。 その他の Azure SQL Database ビジネス継続性ソリューションの概要については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。
- Azure Portal を使用して特定の時点に復元する方法については、[Azure Portal を使用したデータベースのポイントインタイム リストア](sql-database-recovery-using-backups.md)に関するページをご覧ください。
- PowerShell を使用して特定の時点に復元する方法については、[PowerShell を使用したデータベースのポイントインタイム リストア](scripts/sql-database-restore-database-powershell.md)に関するページをご覧ください。
- Azure Blob Storage に長期保存される自動バックアップを Azure portal を使用して構成、管理、復元する方法については、[Azure portal を使用した長期バックアップ保存の管理](sql-database-long-term-backup-retention-configure.md)に関する記事を参照してください。
- Azure Blob Storage に長期保有される自動バックアップを PowerShell を使用して構成、管理、復元する方法については、[PowerShell を使用した長期バックアップ保有の管理](sql-database-long-term-backup-retention-configure.md)に関する記事を参照してください。
