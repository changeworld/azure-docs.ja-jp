<properties 
    pageTitle="以前の時点への Azure SQL Database の復元 (PowerShell) | Microsoft Azure" 
    description="以前の時点に Azure SQL Database を復元する" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="06/09/2016"
    ms.author="sstein"/>

# PowerShell で以前の時点に Azure SQL Database を復元する

この記事では、PowerShell を使用して、[SQL Database 自動バックアップ](sql-database-automated-backups.md)から以前の時点にデータベースを復元する方法について説明します。

[AZURE.INCLUDE [PowerShell セッションの開始](../../includes/sql-database-powershell.md)]

## スタンドアロン データベースとして任意の時点にデータベースを復元

1. [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx) コマンドレットを使用して、復元するデータベースを取得します。

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) コマンドレットを使用して、データベースを任意の時点に復元します。
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"


## エラスティック データベース プールの任意の時点にデータベースを復元
   
1. [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx) コマンドレットを使用して、復元するデータベースを取得します。

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) コマンドレットを使用して、データベースを任意の時点に復元します。
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID –ElasticPoolName "elasticpool01"

## 次のステップ

- [復旧された Azure SQL データベースの最終処理を行う](sql-database-recovered-finalize.md)
- [ポイントインタイム リストア](sql-database-point-in-time-restore.md)
- [Azure ポータルを使用したポイントインタイム リストア](sql-database-point-in-time-restore-portal.md)
- [REST API を使用したポイントインタイム リストア](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [SQL Database 自動バックアップ](sql-database-automated-backups.md)

## その他のリソース

- [削除されたデータベースの復元](sql-database-restore-deleted-database.md)
- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [geo リストア](sql-database-geo-restore.md)
- [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)
- [クラウド障害復旧用アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->