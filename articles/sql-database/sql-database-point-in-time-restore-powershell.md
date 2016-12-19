---
title: "以前の時点への Azure SQL Database の復元 (PowerShell) | Microsoft Docs"
description: "以前の時点に Azure SQL Database を復元する"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 3d1ecd68-5e78-4e8d-9347-b37b5fa15f36
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 07/17/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 4b70d51cbd4b245605173ee596fc0ea587668b92


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-powershell"></a>PowerShell で以前の時点に Azure SQL Database を復元する
> [!div class="op_single_selector"]
> * [概要](sql-database-recovery-using-backups.md)
> * [ポイントインタイム リストア: Azure ポータル](sql-database-point-in-time-restore-portal.md)
> 
> 

この記事では、 [SQL Database 自動バックアップ](sql-database-automated-backups.md)から以前の時点にデータベースを復元する方法について説明します。 これは PowerShell を使用して実行できます。

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="restore-your-database-to-a-point-in-time-as-a-standalone-database"></a>スタンドアロン データベースとして任意の時点にデータベースを復元
1. [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx) コマンドレットを使用して、復元するデータベースを取得します。
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) コマンドレットを使用して、データベースを任意の時点に復元します。
   
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

## <a name="restore-your-database-to-a-point-in-time-into-an-elastic-database-pool"></a>エラスティック データベース プールの任意の時点にデータベースを復元
1. [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx) コマンドレットを使用して、復元するデータベースを取得します。
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) コマンドレットを使用して、データベースを任意の時点に復元します。
   
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID –ElasticPoolName "elasticpool01"

## <a name="next-steps"></a>次のステップ
* ビジネス継続性の概要およびシナリオについては、 [ビジネス継続性の概要](sql-database-business-continuity.md)
* Azure SQL Database 自動バックアップの詳細については、「 [SQL Database automated backups (SQL Database 自動バックアップ)](sql-database-automated-backups.md)
* 自動バックアップを使用して復旧する方法については、 [サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)
* より迅速な復旧オプションについては、 [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)  
* 自動バックアップを使用したアーカイブについては、 [データベースのコピー](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


