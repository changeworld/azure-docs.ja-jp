---
title: 削除された Azure SQL データベースの復元 (Azure ポータル) | Microsoft Docs
description: 削除された Azure SQL データベースの復元 (Azure ポータル)。
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 07/09/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA

---
# Azure ポータルを使用した、削除済み Azure SQL データベースの復元
> [!div class="op_single_selector"]
> * [概要](sql-database-recovery-using-backups.md)
> * [削除されたデータベースの復元: PowerShell](sql-database-restore-deleted-database-powershell.md)
> 
> 

## 復元するデータベースの選択
Azure ポータルでデータベースを復元するには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com) を開きます。
2. 画面の左側で、**[参照]**、**[SQL サーバー]** の順に選択します。
3. 復元する削除済みデータベースが含まれるサーバーに移動し、サーバーを選択します。
4. 下へスクロールしてサーバー ブレードの **[操作]** セクションに移動し、**[削除済みデータベース]** を選択します: ![Azure SQL データベースの復元](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5. 復元する削除済みデータベースを選択します。
6. データベースの名前を指定し、[OK] をクリックします。
   
   ![Azure SQL データベースの復元](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## 次のステップ
* ビジネス継続性の概要およびシナリオについては、[ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。
* Azure SQL Database 自動バックアップの詳細については、「[SQL Database automated backups (SQL Database 自動バックアップ)](sql-database-automated-backups.md)」を参照してください。
* 自動バックアップを使用して復旧する方法については、[サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。
* より迅速な復旧オプションについては、[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください。
* 自動バックアップを使用したアーカイブについては、[データベースのコピー](sql-database-copy.md)に関する記事を参照してください。

<!---HONumber=AcomDC_0727_2016-->