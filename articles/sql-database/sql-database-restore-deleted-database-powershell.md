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
	ms.workload="sqldb-bcdr"
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

- Azure ポータルを使用して、削除されたデータベースを復元する方法の詳細な手順については、[Azure ポータルを使用した削除済みデータベースの復元](sql-database-restore-deleted-database-portal.md)に関するページをご覧ください。
- 削除されたデータベースを復元する方法については、[REST API を使用した削除済みデータベースの復元](https://msdn.microsoft.com/library/azure/mt163685.aspx)に関するページをご覧ください。
- 削除されたデータベースの復元に関する詳細については、[削除されたデータベースの復元](sql-database-restore-deleted-database.md)に関するページをご覧ください。
- Azure SQL Database 自動バックアップの詳細については、「 [SQL Database automated backups (SQL Database 自動バックアップ)](sql-database-automated-backups.md)」をご覧ください。

## その他のリソース

- [ポイントインタイム リストア](sql-database-point-in-time-restore.md)
- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [geo リストア](sql-database-geo-restore.md)
- [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)
- [クラウド障害復旧用アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->