---
title: 以前の時点への Azure SQL Database の復元 (Azure ポータル) | Microsoft Docs
description: 以前の時点に Azure SQL Database を復元します。
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 07/17/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA

---
# Azure ポータルで以前の時点に Azure SQL Database を復元する
> [!div class="op_single_selector"]
> * [概要](sql-database-recovery-using-backups.md)
> * [ポイントインタイム リストア: PowerShell](sql-database-point-in-time-restore-powershell.md)
> 
> 

この記事では、Azure ポータルを使用して、[SQL Database 自動バックアップ](sql-database-automated-backups.md)から以前の時点にデータベースを復元する方法について説明します。

## 以前の時点に復元するデータベースの選択
Azure ポータルでデータベースを復元するには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com) を開きます。
2. 画面の左側で、**[参照]**、**[SQL データベース]** の順に選択します。
3. 復元するデータベースに移動して、選択します。
4. データベースのブレードの上部で **[復元]** を選択します。
   
   ![Azure SQL データベースの復元](./media/sql-database-point-in-time-restore-portal/restore.png)
5. データベース名と復元ポイントを指定して [OK] をクリックします。
   
   ![Azure SQL データベースの復元](./media/sql-database-point-in-time-restore-portal/restore-details.png)

## 次のステップ
* ビジネス継続性の概要およびシナリオについては、[ビジネス継続性の概要](sql-database-business-continuity.md)に関する記事を参照してください。
* Azure SQL Database 自動バックアップの詳細については、「[SQL Database automated backups (SQL Database 自動バックアップ)](sql-database-automated-backups.md)」を参照してください。
* 自動バックアップを使用して復旧する方法については、[サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)に関する記事を参照してください。
* より迅速な復旧オプションについては、[アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事を参照してください。
* 自動バックアップを使用したアーカイブについては、[データベースのコピー](sql-database-copy.md)に関する記事を参照してください。

<!---HONumber=AcomDC_0727_2016-->