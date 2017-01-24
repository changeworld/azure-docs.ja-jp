---
title: "以前の時点への Azure SQL Database の復元 | Microsoft Docs"
description: "以前の時点に Azure SQL Database を復元する"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/08/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 75bf523679c8d8ad6fbe4a8aa8a561d03008e59b
ms.openlocfilehash: 4a3c7ae453ead16aec68d362676aa0a000b647ba


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time"></a>以前の時点に Azure SQL Database を復元する 

この記事では、[SQL Database 自動バックアップ](sql-database-automated-backups.md)から以前の時点にデータベースを復元する方法について説明します。 

## <a name="restore-to-a-previous-point-in-time-using-the-azure-portal"></a>Azure ポータルを使用した以前の時点への復元

> [!TIP]
> チュートリアルについては、「[データの保護と回復のためのバックアップと復元の概要](sql-database-get-started-backup-recovery.md)」を参照してください。
>

Azure Portal で、復元するデータベースを選択します。

1. [Azure ポータル](https://portal.azure.com)を開きます。
2. 画面の左側で、**[その他のサービス]** > **[SQL データベース]** を選択します。
3. 復元するデータベースをクリックします。
4. データベースのページの上部で **[復元]** を選択します。
   
   ![Azure SQL データベースの復元](./media/sql-database-point-in-time-restore-portal/restore.png)
5. **[復元]** ページで、データベースの復元ポイント (UTC での日付と時刻) を選択し、**[OK]** をクリックします。
   
   ![Azure SQL データベースの復元](./media/sql-database-point-in-time-restore-portal/restore-details.png)

6. 前の手順で **[OK]** をクリックした後、ページの右上にある通知アイコンをクリックし、**[SQL データベースを復元しています]** という通知をクリックします。
   
    ![Azure SQL データベースの復元](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
7. [SQL データベースを復元しています] ページが開き、復元操作の状態に関する情報が表示されます。 項目をクリックすると、その詳細を確認できます。
   
    ![Azure SQL データベースの復元](./media/sql-database-point-in-time-restore-portal/inprogress.png)

## <a name="restore-to-a-previous-point-in-time-using-powershell"></a>PowerShell を使用した以前の時点への復元

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell-h3.md)]

### <a name="restore-your-database-to-a-point-in-time-as-a-single-database"></a>単一データベースとして任意の時点にデータベースを復元
1. [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx) コマンドレットを使用して、復元するデータベースを取得します。
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) コマンドレットを使用して、データベースを任意の時点に復元します。
   
        Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

### <a name="restore-your-database-to-a-point-in-time-into-an-elastic-pool"></a>エラスティック プールのデータベースを任意の時点に復元
1. [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx) コマンドレットを使用して、復元するデータベースを取得します。
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx) コマンドレットを使用して、データベースを任意の時点に復元します。
   
        Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $Database.ResourceID -ElasticPoolName "elasticpool01"

## <a name="next-steps"></a>次のステップ
* Azure SQL Database 自動バックアップの詳細については、「 [SQL Database 自動バックアップ](sql-database-automated-backups.md)
* 自動バックアップを使用して復旧する方法については、 [サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)
* サービスによって生成されたデータベース バックアップから最も古い復元ポイントを確認するには、[最も古い復元ポイントの表示](sql-database-view-oldest-restore-point.md)に関する記事を参照してください。
* ビジネス継続性の概要およびシナリオについては、 [ビジネス継続性の概要](sql-database-business-continuity.md)




<!--HONumber=Dec16_HO3-->


