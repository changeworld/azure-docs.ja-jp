<properties 
    pageTitle="geo 冗長バックアップからの Azure SQL Database 復元 (PowerShell) | Microsoft Azure" 
    description="geo 冗長バックアップから新しいサーバーに Azure SQL Database を復元します" 
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

# PowerShell を使用した geo 冗長バックアップからの Azure SQL Database の復元

> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

この記事では、PowerShell で geo リストアを使用して新しいサーバーにデータベースを復元する方法について説明します。

[geo リストア](sql-database-geo-restore.md)を利用すると、geo 冗長バックアップからデータベースを復元して新しいデータベースを作成できます。データベースは任意の Azure リージョンの任意のサーバーに作成できます。ソースとして geo 冗長バックアップが使用されるため、障害によってデータベースにアクセスできない場合でも、それを使用してデータベースを復旧できます。geo リストアは、追加料金なしですべてのサービス層に対して自動的に有効になります。

[AZURE.INCLUDE [PowerShell セッションの開始](../../includes/sql-database-powershell.md)]

## スタンドアロン データベースへのデータベースの geo リストア

1. [Get AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx) コマンドレットを使用して、復元するデータベースの geo 冗長バックアップを取得します。

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) コマンドレットを使用して、geo 冗長バックアップからの復元を開始します。
    
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"


## エラスティック データベース プールへのデータベースの geo リストア

1. [Get AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx) コマンドレットを使用して、復元するデータベースの geo 冗長バックアップを取得します。

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) コマンドレットを使用して、geo 冗長バックアップからの復元を開始します。データベースの復元先プールの名前を指定します。
    
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID –ElasticPoolName "elasticpool01"  

## 次のステップ

- [復旧された Azure SQL データベースの最終処理を行う](sql-database-recovered-finalize.md)
- [SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)


## その他のリソース

- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->