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
    ms.date="05/10/2016"
    ms.author="sstein"/>

# PowerShell で以前の時点に Azure SQL Database を復元する

> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

この記事では、PowerShell を使用して以前の時点にデータベースを復元する方法について説明します。

セルフサービス機能である[**ポイントインタイム リストア**](sql-database-point-in-time-restore.md)を使用すると、すべてのデータベースに対して自動作成されたバックアップから、データベースのリテンション期間内の任意のポイントにデータベースを復元できます。データベースのリテンション期間の詳細については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関するページをご覧ください。

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
- [SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)


## その他のリソース

- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->