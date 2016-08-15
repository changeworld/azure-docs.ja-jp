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
    ms.workload="NA"
    ms.date="07/17/2016"
    ms.author="sstein"/>

# PowerShell を使用した geo 冗長バックアップからの Azure SQL Database の復元


> [AZURE.SELECTOR]
- [概要](sql-database-recovery-using-backups.md)
- [geo リストア: Azure ポータル](sql-database-geo-restore-portal.md)

この記事では、geo リストアを使用して新しいサーバーにデータベースを復元する方法について説明します。これは、PowerShell を使って実行できます。

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

- ビジネス継続性の概要およびシナリオについては、[ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。
- Azure SQL Database 自動バックアップの詳細については、「[SQL Database 自動バックアップ](sql-database-automated-backups.md)」を参照してください
- 自動バックアップを使用して復旧する方法については、[サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)に関するページをご覧ください
- より迅速な回復オプションについては、[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関するページをご覧ください
- 自動バックアップを使用したアーカイブについては、[データベースのコピー](sql-database-copy.md)に関するページをご覧ください

<!---HONumber=AcomDC_0803_2016-->