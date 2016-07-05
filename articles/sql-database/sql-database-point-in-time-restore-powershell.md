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
    ms.workload="sqldb-bcdr" 
    ms.date="06/17/2016"
    ms.author="sstein"/>

# PowerShell で以前の時点に Azure SQL Database を復元する

> [AZURE.SELECTOR]
- [概要](sql-database-point-in-time-restore.md)
- [Azure ポータル](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

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

- Azure ポータルを使用して特定の時点に復旧する詳細な手順については、[Azure ポータルを使用したポイントインタイム リストア](sql-database-point-in-time-restore-portal.md)に関するページをご覧ください。
- REST API を使用して特定の時点に復旧する詳細な手順については、[REST API を使用したポイントインタイム リストア](https://msdn.microsoft.com/library/azure/mt163685.aspx)に関するページをご覧ください。
- ポイントイン タイム リストアの概要については、[ポイントインタイム リストア](sql-database-point-in-time-restore.md)に関するページをご覧ください。
- ユーザー エラーまたはアプリケーション エラーから復旧する方法の詳細については、[ユーザー エラーの復旧](sql-database-user-error-recovery.md)に関するページをご覧ください。

## その他のリソース

- [ビジネス継続性のシナリオ](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->