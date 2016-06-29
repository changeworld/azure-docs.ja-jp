<properties
	pageTitle="削除された Azure SQL Database の復元 (PowerShell) | Microsoft Azure"
	description="削除された Azure SQL Database の復元 (PowerShell)"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/09/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# PowerShell を使用した、削除済み Azure SQL Database の復元

> [AZURE.SELECTOR]
- [概要](sql-database-restore-deleted-database.md)
- [Azure ポータル](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

[AZURE.INCLUDE [PowerShell セッションの開始](../../includes/sql-database-powershell.md)]


## スタンドアロン データベースへの削除済みデータベースの復元

1. [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx) コマンドレットを使用して、復元する削除済みデータベースのバックアップを取得します。

        $DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) コマンドレットを使用して、削除済みデータベースのバックアップからの復元を開始します。
    
        Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

## エラスティック データベース プールへの削除済みデータベースの復元

1. [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx) コマンドレットを使用して、復元する削除済みデータベースのバックアップを取得します。

        $DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) コマンドレットを使用して、削除済みデータベースのバックアップからの復元を開始します。
    
        Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID –ElasticPoolName "elasticpool01" 

## 次のステップ

- [復旧された Azure SQL データベースの最終処理を行う](sql-database-recovered-finalize.md)
- [削除されたデータベースの復元](sql-database-restore-deleted-database.md)
- [Azure ポータルを使用して削除済みデータベースを復元する](sql-database-restore-deleted-database-portal.md)
- [REST API を使用して削除済みデータベースを復元する](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [SQL Database 自動バックアップ](sql-database-automated-backups.md)

## その他のリソース

- [ポイントインタイム リストア](sql-database-point-in-time-restore.md)
- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [geo リストア](sql-database-geo-restore.md)
- [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)
- [クラウド障害復旧用アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->