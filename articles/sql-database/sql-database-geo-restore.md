---
title: "geo 冗長バックアップからの Azure SQL Database 復元 | Microsoft Docs"
description: "Azure ポータルまたは PowerShell を使用して、geo 冗長バックアップから新しいサーバーに Azure SQL Database を復元します"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 4b42bffa-f98c-406a-9a96-51721cc423d4
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/19/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6104936089ac9a1a6bbc08a345105e5fa4ae8be7
ms.openlocfilehash: 0ab7090ba7b37b3447da95a3d577cfe82ea8003e


---
# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup"></a>geo 冗長バックアップからの Azure SQL Database の復元

この記事では、geo リストアを使用して新しいサーバーにデータベースを復元する方法について説明します。 これ行うには、Azure ポータルまたは PowerShell を使用します。

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-the-azure-portal"></a>Azure ポータルを使用した geo 冗長バックアップからの Azure SQL Database のリストア

Azure ポータルでデータベースを geo リストアするには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. 画面の左側で、**[+ 新規]** > **[Databases]** > **[SQL Database]** を選択します。
   
   ![Azure SQL データベースの復元](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. ソースとして **[バックアップ]** を選択し、復元するバックアップを選択します。 データベース名と、データベースの復元先サーバーを指定し、**[作成]** をクリックします。
   
   ![Azure SQL データベースの復元](./media/sql-database-geo-restore-portal/geo-restore.png)

4. ページの右上の通知アイコンをクリックして、復元操作の状態を確認します。

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-powershell"></a>PowerShell を使用した geo 冗長バックアップからの Azure SQL Database の復元

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell-h3.md)]

### <a name="geo-restore-your-database-into-a-standalone-database"></a>スタンドアロン データベースへのデータベースの geo リストア
1. [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx) コマンドレットを使用して、復元するデータベースの geo 冗長バックアップを取得します。
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) コマンドレットを使用して、geo 冗長バックアップからの復元を開始します。
   
        Restore-AzureRmSqlDatabase -FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" -ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"

### <a name="geo-restore-your-database-into-an-elastic-pool"></a>エラスティック プールへのデータベースの geo リストア
1. [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx) コマンドレットを使用して、復元するデータベースの geo 冗長バックアップを取得します。
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) コマンドレットを使用して、geo 冗長バックアップからの復元を開始します。 データベースの復元先プールの名前を指定します。
   
        Restore-AzureRmSqlDatabase -FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" -ResourceId $GeoBackup.ResourceID -ElasticPoolName "elasticpool01"  

## <a name="next-steps"></a>次のステップ
* ビジネス継続性の概要およびシナリオについては、 [ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。
* Azure SQL Database 自動バックアップの詳細については、「 [SQL Database 自動バックアップ](sql-database-automated-backups.md)」を参照してください。
* 自動バックアップを使用して復旧する方法については、 [サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。
* より迅速な復旧オプションについては、[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください。  
* 自動バックアップを使用したアーカイブについては、[データベースのコピー](sql-database-copy.md)に関する記事を参照してください。




<!--HONumber=Dec16_HO3-->


