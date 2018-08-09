---
title: Azure SQL Database のバックアップ - 自動、geo 冗長 | Microsoft Docs
description: SQL Database は数分ごとにローカル データベースをバックアップし、Azure 読み取りアクセス geo 冗長ストレージを利用して地理的冗長性を提供します。
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.workload: Active
ms.date: 07/25/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 8798d0f17918ecce473afe8dc21b3f60bf0fa4b1
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39620132"
---
# <a name="learn-about-automatic-sql-database-backups"></a>SQL Database 自動バックアップについての詳細情報

SQL Database はデータベースをバックアップし、Azure 読み取りアクセス geo 冗長ストレージ (RA-GRS) を利用して地理的冗長性を提供します。 バックアップは自動的に作成され、追加料金は発生しません。 ユーザー側の操作は必要ありません。 データの不慮の破損または削除から保護するデータベース バックアップは、ビジネス継続性およびディザスター リカバリー戦略の最も重要な部分です。 セキュリティ規則で、長期間バックアップが利用できる必要がある場合は、長期的なバックアップ アイテム保持ポリシーを構成できます。 詳細については、「[長期保存](sql-database-long-term-retention.md)」をご覧ください。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-sql-database-backup"></a>SQL Database バックアップとは何か。

SQL Database は、ポイントインタイム リストア (PITR) の目的で、SQL Server 技術を利用して、[完全](https://msdn.microsoft.com/library/ms186289.aspx)バックアップ、[差分](https://docs.microsoft.com/sql/relational-databases/backup-restore/differential-backups-sql-server)バックアップ、[トランザクション ログ](https://msdn.microsoft.com/library/ms191429.aspx) バックアップを作成します。 トランザクション ログ バックアップは一般的には 5 - 10 分ごとに発生し、差分バックアップは 12 時間ごとに行われます。頻度はパフォーマンス レベルとデータベース アクティビティの量に基づきます。 完全バックアップと差分バックアップによるトランザクション ログ バックアップにより、データベースをホストする同じサーバーに、データベースを特定の時点に復元できます。 データベースを復元するとき、どのバックアップを復元する必要があるかをサービスが判定します (完全、差分、トランザクション ログ)。


これらのバックアップを使用して、以下を行うことができます。

* リテンション期間内の特定の時点にデータベースを復元します。 この操作により、元のデータベースと同じサーバーに新しいデータベースが作成されます。
* 削除したデータベースを、削除された時点または保有期間内の任意の時点に復元します。 削除されたデータベースは、元のデータベースが作成されたサーバーと同じサーバーにのみ復元できます。
* 別の地理的リージョンにデータベースを復元します。 これにより、サーバーやデータベースにアクセスできないときに、地理的な障害から復旧できます。 世界中のどこでも、あらゆる既存のサーバーで新しいデータベースを作成します。 
* データベースが長期アイテム保持ポリシー (LTR) で構成されている場合、そのデータベースは、特定の長期バックアップから復元します。 これにより、データベースの古いバージョンに復元でき、コンプライアンスの要求を満たし、またはアプリケーションの古いバージョンを実行できます。 [長期保存](sql-database-long-term-retention.md)に関する記事を参照してください。
* 復元を実行するには、[バックアップからのデータベースの復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。

> [!NOTE]
> Azure Storage の " *レプリケーション* " という用語は、ある場所から別の場所にファイルをコピーすることを表します。 SQL の *データベース レプリケーション* は、複数のセカンダリ データベースとプライマリ データベースとの同期を保つことを意味します。 
> 

## <a name="how-long-are-backups-kept"></a>バックアップはどれだけの時間保持されますか。
各 SQL Database バックアップにはデータベースのサービス レベルに基づく既定のリテンション期間があります。さらに、各 SQL Database バックアップは、[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)と[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)とで異なります。 データベースのバックアップのリテンション期間を更新することができます。 詳細については、[バックアップのリテンション期間の変更](#how-to-change-backup-retention-period)に関するセクションを参照してください。

データベースを削除した場合、SQL Database はオンライン データベースの場合と同じようにバックアップを保持します。 たとえば、7 日間のリテンション期間のある基本的なデータベースを削除すると、4 日間保存されているバックアップはさらに 3 日間保存されます。

最大 PITR リテンション期間より長くバックアップを保持する必要がある場合は、データベースに 1 つまたは複数のより長いリテンション期間を追加するようにバックアップのプロパティを変更できます。 詳細については、[バックアップの長期保有期間](sql-database-long-term-retention.md)に関するページを参照してください。

> [!IMPORTANT]
> SQL Database をホストする Azure SQL サーバーを削除すると、サーバーに属するすべてのエラスティック プールとデータベースも削除され、復元できなくなります。 削除されたサーバーを復元することはできません。 長期保有を構成した場合、LTR を使用したデータベースのバックアップは削除されず、これらのデータベースを復元することができます。

### <a name="pitr-retention-for-dtu-based-service-tiers"></a>DTU に基づくサービス層の PITR リテンション期間
DTU ベースの購入モデルを使用して作成されたデータベースのリテンション期間は、サービス レベルに依存します。

* Basic サービスレベルの場合、1 週間です。
* Standard サービス レベルの場合、5 週間です。
* Premium サービス レベルの場合、5 週間です。

現在の PITR リテンション期間を短縮した場合、新しいリテンション期間より古いすべての既存のバックアップは使用できなくなります。 

現在の PITR リテンション期間を延長した場合、SQL Database は、より長いリテンション期間に達するまでに、既存のバックアップを保持します。

## <a name="how-often-do-backups-happen"></a>バックアップはどのくらいの頻度で行われますか。
### <a name="backups-for-point-in-time-restore"></a>ポイントインタイム リストアのバックアップ
SQL Database では、完全バックアップ、差分バックアップ、トランザクション ログ バックアップを自動的に作成して、ポイントインタイム リストア (PITR) のセルフ サービスをサポートします。 完全データベース バックアップは毎週、差分データベース バックアップは一般的に 12 時間ごとに、トランザクション ログ バックアップは通常、5 - 10 分ごとに作成されます。頻度は、パフォーマンス レベルとデータベース アクティビティの量に基づきます。 初回の完全バックアップは、データベースの作成直後にスケジュールされます。 通常この操作は 30 分以内に終了しますが、データベースのサイズが大きい場合はそれ以上かかることがあります。 たとえば、復元されたデータベースまたはデータベースのコピーでは、初期バックアップに時間がかかります。 初回の完全バックアップ以降のバックアップは、すべて自動的にスケジュールされ、バックグラウンドで自動的に管理されます。 データベースのバックアップの正確なタイミングは、全体的なシステムのワークロードのバランスを図りながら SQL Database サービスによって決定されます。

PITR バックアップは、geo 冗長であり、[Azure Storage のリージョン間レプリケーション](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)によって保護されます。

詳細については、「[ポイントインタイム リストア](sql-database-recovery-using-backups.md#point-in-time-restore)」をご覧ください。

### <a name="backups-for-long-term-retention"></a>長期保有のためのバックアップ
SQL Database には、Azure Blob Storage に対する最大 10 年の完全バックアップの長期保有 (LTR) を構成するオプションが用意されています。 LTR ポリシーが有効になっている場合、週次の完全バックアップは自動的に別の RA-GRS ストレージ コンテナーにコピーされます。 さまざまなコンプライアンス要件を満たすために、毎週、毎月、毎年のバックアップに対して異なったリテンション期間を選択することができます。 ストレージの使用量は、選択したバックアップの頻度とリテンション期間によって異なります。 LTR ストレージのコストは、[LTR 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=sql-database)を使用して見積もることができます。 

PITR と同じように、LTR バックアップは、geo 冗長であり、[Azure Storage のリージョン間レプリケーション](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)によって保護されます。

詳細については、「[Long-term backup retention](sql-database-long-term-retention.md)」(長期バックアップ リテンション) をご覧ください。

## <a name="are-backups-encrypted"></a>バックアップは暗号化されますか?

データベースが TDE で暗号化されている場合、LTR バックアップを含むバックアップは保存中に自動的に暗号化されます。 Azure SQL データベースに対して TDE が有効になっているとき、バックアップも暗号化されます。 新しい Azure SQL データベースはすべて、既定で TDE が有効になった状態で構成されます。 TDE に関する詳細については、「[Azure SQL Database での Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)」をご覧ください。

## <a name="how-do-automated-backups-impact-my-compliance"></a>自動バックアップはどのようにコンプライアンスに影響しますか。

PITR リテンション期間が 35 日間である DTU ベースのサービス レベルから仮想コア ベースのサービス レベルにデータベースを移行した場合、アプリケーションのデータ リカバリ ポリシーが損なわれないように、PITR リテンション期間が保持されます。 既定のリテンション期間では、お客様のコンプライアンス要件を満たしていない場合は、PowerShell または REST API を使用して PITR リテンション期間を変更できます。 詳細については、[バックアップのリテンション期間の変更](#how-to-change-backup-retention-period)に関するセクションを参照してください。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="how-to-change-backup-retention-period"></a>バックアップのリテンション期間の変更方法
REST API または PowerShell を使用して既定のリテンション期間を変更することができます。 サポートされている値は 7、14、21、28、35 日間です。 次の例では、PITR リテンション期間を 28 日間に変更する方法を示します。 

> [!NOTE]
> これらの API は PITR リテンション期間にのみ影響を与えます。 データベースの LTR を構成した場合、それには影響ありません。 LTR リテンション期間の変更方法については、[長期的なバックアップ保有期間](sql-database-long-term-retention.md)に関するページを参照してください。

### <a name="change-pitr-backup-retention-period-using-powershell"></a>PowerShell を使用して PITR バックアップ リテンション期間を変更する
```powershell
Set-AzureRmSqlDatabaseBackupShortTermRetentionPolicy -ResourceGroupName resourceGroup -ServerName testserver -DatabaseName testDatabase -RetentionDays 28
```
> [!IMPORTANT]
> この API は、バージョン [4.7.0-preview](https://www.powershellgallery.com/packages/AzureRM.Sql/4.7.0-preview) 以降の AzureRM.Sql PowerShell モジュールに含まれています。 

### <a name="change-pitr-retention-period-using-rest-api"></a>REST API を使用して PITR リテンション期間を変更する
**要求のサンプル**
```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/resourceGroup/providers/Microsoft.Sql/servers/testserver/databases/testDatabase/backupShortTermRetentionPolicies/default?api-version=2017-10-01-preview
```
**要求本文**
```json
{
  "properties":{  
      "retentionDays":28
   }
}
```
**応答のサンプル**

状態コード: 200
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
詳細については、[バックアップ リテンション期間 REST API](https://docs.microsoft.com/rest/api/sql/backupshorttermretentionpolicies)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- データの不慮の破損または削除から保護するデータベース バックアップは、ビジネス継続性およびディザスター リカバリー戦略の最も重要な部分です。 その他の Azure SQL Database ビジネス継続性ソリューションの概要については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。
- Azure Portal を使用して特定の時点に復元する方法については、[Azure Portal を使用したデータベースのポイントインタイム リストア](sql-database-recovery-using-backups.md)に関するページをご覧ください。
- PowerShell を使用して特定の時点に復元する方法については、[PowerShell を使用したデータベースのポイントインタイム リストア](scripts/sql-database-restore-database-powershell.md)に関するページをご覧ください。
- Azure Blob Storage に長期保存される自動バックアップを Azure portal を使用して構成、管理、復元する方法については、[Azure portal を使用した長期バックアップ保存の管理](sql-database-long-term-backup-retention-configure.md)に関する記事を参照してください。
- Azure Blob Storage に長期保存される自動バックアップを PowerShell を使用して構成、管理、復元する方法については、[PowerShell を使用した長期バックアップ保存の管理](sql-database-long-term-backup-retention-configure.md)に関する記事を参照してください。
