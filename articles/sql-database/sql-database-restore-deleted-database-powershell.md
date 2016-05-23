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
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# PowerShell を使用した、削除済み Azure SQL Database の復元


> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

この記事では、削除済みの Azure SQL Database を復元する方法について説明します。

データベースが削除された場合、Azure SQL Database では、削除されたデータベースを削除された時点の状態に復元できます。Azure SQL Database では、データベースの保持期間にわたり、削除されたデータベースのバックアップを格納します。

削除されたデータベースの保持期間は、データベースのサービス階層に基づく日数とデータベースが存在していた日数のどちらか短い方になります。データベースのリテンション期間の詳細については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関するページをご覧ください。


[AZURE.INCLUDE [PowerShell セッションの開始](../../includes/sql-database-powershell.md)]


## スタンドアロン データベースへの削除済みデータベースの復元

1. [Get AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx) コマンドレットを使用して、復元する削除済みデータベースのバックアップを取得します。

        $DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) コマンドレットを使用して、削除済みデータベースのバックアップからの復元を開始します。
    
        Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

## エラスティック データベース プールへの削除済みデータベースの復元

1. [Get AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx) コマンドレットを使用して、復元する削除済みデータベースのバックアップを取得します。

        $DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) コマンドレットを使用して、削除済みデータベースのバックアップからの復元を開始します。
    
        Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID –ElasticPoolName "elasticpool01" 

## 次のステップ

- [復旧された Azure SQL データベースの最終処理を行う](sql-database-recovered-finalize.md)
- [SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)



## その他のリソース

- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->