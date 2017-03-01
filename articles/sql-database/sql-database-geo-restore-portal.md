---
title: "Azure Portal: geo 冗長バックアップから SQL Database を復元する | Microsoft Docs"
description: "Azure Portal を使って、geo 冗長バックアップから新しいサーバーに Azure SQL Database を復元します"
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
ms.date: 12/19/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 7d9314444c39cda3f9d893e4f97b4afce4c75777
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-with-the-azure-portal"></a>Azure Portal で geo 冗長バックアップから Azure SQL Database を復元する

この記事では、Azure Portal で geo リストアを使って新しいサーバーにデータベースを復元する方法について説明します。 このタスクは、[PowerShell を使って](sql-database-geo-restore-powershell.md)行うこともできます。

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-the-azure-portal"></a>Azure ポータルを使用した geo 冗長バックアップからの Azure SQL Database のリストア

Azure ポータルでデータベースを geo リストアするには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. 画面の左側で、**[+ 新規]** > **[Databases]** > **[SQL Database]** を選択します。
   
   ![Azure SQL データベースの復元](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. ソースとして **[バックアップ]** を選択し、復元するバックアップを選択します。 データベース名と、データベースの復元先サーバーを指定し、**[作成]** をクリックします。
   
   ![Azure SQL データベースの復元](./media/sql-database-geo-restore-portal/geo-restore.png)

4. ページの右上の通知アイコンをクリックして、復元操作の状態を確認します。


## <a name="next-steps"></a>次のステップ
* ビジネス継続性の概要およびシナリオについては、 [ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。
* Azure SQL Database 自動バックアップの詳細については、「 [SQL Database 自動バックアップ](sql-database-automated-backups.md)」を参照してください。
* 自動バックアップを使用して復旧する方法については、 [サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。
* より迅速な復旧オプションについては、[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください。  
* 自動バックアップを使用したアーカイブについては、[データベースのコピー](sql-database-copy.md)に関する記事を参照してください。


