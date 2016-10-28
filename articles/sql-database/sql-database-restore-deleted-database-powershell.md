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
	ms.date="07/09/2016"
	ms.author="sstein"
	ms.workload="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# PowerShell を使用した、削除済み Azure SQL Database の復元

> [AZURE.SELECTOR]
- [概要](sql-database-recovery-using-backups.md)
- [削除されたデータベースの復元: Azure ポータル](sql-database-restore-deleted-database-portal.md)

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

- ビジネス継続性の概要およびシナリオについては、[ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。
- Azure SQL Database 自動バックアップの詳細については、「[SQL Database automated backups (SQL Database 自動バックアップ)](sql-database-automated-backups.md)」を参照してください。
- 自動バックアップを使用して復旧する方法については、[サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。
- より迅速な復旧オプションについては、[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください。
- 自動バックアップを使用したアーカイブについては、[データベースのコピー](sql-database-copy.md)に関する記事を参照してください。

<!---HONumber=AcomDC_0803_2016-->