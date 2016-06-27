<properties
	pageTitle="以前の時点への Azure SQL Database の復元 (Azure ポータル) | Microsoft Azure"
	description="以前の時点に Azure SQL Database を復元します。"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/09/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Azure ポータルで以前の時点に Azure SQL Database を復元する


> [AZURE.SELECTOR]
- [概要](sql-database-point-in-time-restore.md)
- [Azure ポータル](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

この記事では、Azure ポータルを使用して、[SQL Database 自動バックアップ](sql-database-automated-backups.md)から以前の時点にデータベースを復元する方法について説明します。

## 以前の時点に復元するデータベースの選択

Azure ポータルでデータベースを復元するには、次の手順を実行します。

1.	[Azure ポータル](https://portal.azure.com) を開きます。
2.  画面の左側で、**[参照]**、**[SQL データベース]** の順に選択します。
3.  復元するデータベースに移動して、選択します。
4.  データベースのブレードの上部で **[復元]** を選択します。

    ![Azure SQL データベースの復元](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  データベース名と復元ポイントを指定して [OK] をクリックします。

    ![Azure SQL データベースの復元](./media/sql-database-point-in-time-restore-portal/restore-details.png)


## 次のステップ

- [復旧された Azure SQL データベースの最終処理を行う](sql-database-recovered-finalize.md)
- [ポイントインタイム リストア](sql-database-point-in-time-restore.md)
- [REST API を使用したポイントインタイム リストア](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [SQL Database 自動バックアップ](sql-database-automated-backups.md)

## その他のリソース

- [削除されたデータベースの復元](sql-database-restore-deleted-database.md)
- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [geo リストア](sql-database-geo-restore.md)
- [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)
- [クラウド障害復旧用アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->