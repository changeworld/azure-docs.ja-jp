---
title: "以前の時点への Azure SQL Database の復元 (Azure Portal) | Microsoft Docs"
description: "以前の時点に Azure SQL Database を復元します。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: d1822905-a11f-4c42-8940-98c6b81aed20
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.date: 10/18/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6feaea525ae1fceff5acdc95fefa115939d5b1da


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>Azure Portal で以前の時点に Azure SQL Database を復元する
> [!div class="op_single_selector"]
> * [概要](sql-database-recovery-using-backups.md)
> * [ポイントインタイム リストア: PowerShell](sql-database-point-in-time-restore-powershell.md)
> 
> 

この記事では、Azure Portal を使用して、[SQL Database 自動バックアップ](sql-database-automated-backups.md)から以前の時点にデータベースを復元する方法について説明します。

## <a name="restore-a-sql-database-to-a-previous-point-in-time"></a>過去のある時点に SQL Database を復元する
Azure Portal で、復元するデータベースを選択します。

1. [Azure ポータル](https://portal.azure.com)を開きます。
2. 画面の左側で、**[その他のサービス]** > **[SQL データベース]** を選択します。
3. 復元するデータベースをクリックします。
4. データベースのページの上部で **[復元]** を選択します。
   
   ![Azure SQL データベースの復元](./media/sql-database-point-in-time-restore-portal/restore.png)
5. **[復元]** ページで、データベースの復元ポイント (UTC での日付と時刻) を選択し、**[OK]** をクリックします。
   
   ![Azure SQL データベースの復元](./media/sql-database-point-in-time-restore-portal/restore-details.png)

## <a name="monitor-the-restore-operation"></a>復元操作を確認する
1. 前の手順で **[OK]** をクリックした後、ページの右上にある通知アイコンをクリックし、**[SQL データベースを復元しています]** という通知をクリックします。
   
    ![Azure SQL データベースの復元](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
2. [SQL データベースを復元しています] ページが開き、復元操作の状態に関する情報が表示されます。 項目をクリックすると、その詳細を確認できます。
   
    ![Azure SQL データベースの復元](./media/sql-database-point-in-time-restore-portal/inprogress.png)

## <a name="next-steps"></a>次のステップ
* ビジネス継続性の概要およびシナリオについては、 [ビジネス継続性の概要](sql-database-business-continuity.md)
* Azure SQL Database 自動バックアップの詳細については、「 [SQL Database automated backups (SQL Database 自動バックアップ)](sql-database-automated-backups.md)
* 自動バックアップを使用して復旧する方法については、 [サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)
* より迅速な復旧オプションについては、 [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)  
* 自動バックアップを使用したアーカイブについては、 [データベースのコピー](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


