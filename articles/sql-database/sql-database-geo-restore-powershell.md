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
    ms.workload="sqldb-bcdr" 
    ms.date="06/17/2016"
    ms.author="sstein"/>

# PowerShell を使用した geo 冗長バックアップからの Azure SQL Database の復元


> [AZURE.SELECTOR]
- [概要](sql-database-geo-restore.md)
- [Azure ポータル](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

この記事では、PowerShell で geo リストアを使用して新しいサーバーにデータベースを復元する方法について説明します。

[AZURE.INCLUDE [PowerShell セッションの開始](../../includes/sql-database-powershell.md)]

## スタンドアロン データベースへのデータベースの geo リストア

1. [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx) コマンドレットを使用して、復元するデータベースの geo 冗長バックアップを取得します。

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) コマンドレットを使用して、geo 冗長バックアップからの復元を開始します。
    
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"


## エラスティック データベース プールへのデータベースの geo リストア

1. [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx) コマンドレットを使用して、復元するデータベースの geo 冗長バックアップを取得します。

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) コマンドレットを使用して、geo 冗長バックアップからの復元を開始します。データベースの復元先プールの名前を指定します。
    
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID –ElasticPoolName "elasticpool01"  

## 次のステップ

- Azure ポータルを使用して geo 冗長バックアップから Azure SQL Database を復元する方法の詳細な手順については、「[Azure ポータルを使用した geo リストア](sql-database-geo-restore-portal.md)」をご覧ください。
- geo 冗長バックアップから Azure SQL Database を復元する方法の詳細な手順については、「[PowerShell を使用した geo リストア](sql-database-geo-restore.md)」をご覧ください。
- 障害から回復する方法の詳細については、[障害からの回復](sql-database-disaster-recovery.md)に関するページをご覧ください。


## その他のリソース

- [ビジネス継続性のシナリオ](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->