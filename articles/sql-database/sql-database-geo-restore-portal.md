---
title: "自動バックアップからの Azure SQL Database の復元 (Azure Portal) | Microsoft Docs"
description: "自動バックアップから Azure SQL Database を復元します (Azure Portal)。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 62d94085-d7e7-4f08-bb83-404cf866a6c1
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.date: 10/18/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 85f8f2473e372a72c66153cd4131ba46e1c8c741


---
# <a name="restore-an-azure-sql-database-from-an-automatic-backup-using-the-azure-portal"></a>Azure Portal を使用した自動バックアップからの Azure SQL Database の復元
> [!div class="op_single_selector"]
> * [概要](sql-database-recovery-using-backups.md#geo-restore)
> * [geo リストア: PowerShell](sql-database-geo-restore-powershell.md)
> 
> 

この記事では、Azure Portal で [geo リストア](sql-database-recovery-using-backups.md#geo-restore)を使用して、[自動バックアップ](sql-database-automated-backups.md)から新しいサーバーにデータベースを復元する方法について説明します。

## <a name="select-a-database-to-restore"></a>復元するデータベースの選択
Azure Portal でデータベースを復元するには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. 画面の左側で、**[+ 新規]** > **[Databases]** > **[SQL Database]** を選択します。
   
   ![Azure SQL データベースの復元](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. ソースとして **[バックアップ]** を選択し、復元するバックアップを選択します。 データベース名と、データベースの復元先サーバーを指定し、**[作成]** をクリックします。
   
   ![Azure SQL データベースの復元](./media/sql-database-geo-restore-portal/geo-restore.png)

ページの右上の通知アイコンをクリックして、復元操作の状態を確認します。

## <a name="next-steps"></a>次のステップ
* ビジネス継続性の概要およびシナリオについては、 [ビジネス継続性の概要](sql-database-business-continuity.md)
* Azure SQL Database 自動バックアップの詳細については、「 [SQL Database automated backups (SQL Database 自動バックアップ)](sql-database-automated-backups.md)
* 自動バックアップを使用して復旧する方法については、 [サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)
* より迅速な復旧オプションについては、 [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)  
* 自動バックアップを使用したアーカイブについては、 [データベースのコピー](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


