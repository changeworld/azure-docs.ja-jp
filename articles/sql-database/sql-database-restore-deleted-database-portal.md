<properties
	pageTitle="削除された Azure SQL データベースの復元 (Azure ポータル) | Microsoft Azure"
	description="削除された Azure SQL データベースの復元 (Azure ポータル)。"
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


# Azure ポータルを使用した、削除済み Azure SQL データベースの復元

> [AZURE.SELECTOR]
- [概要](sql-database-restore-deleted-database.md)
- [Azure ポータル](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

## 復元するデータベースの選択 

Azure ポータルでデータベースを復元するには、次の手順を実行します。

1.	[Azure ポータル](https://portal.azure.com) を開きます。
2.  画面の左側で、**[参照]**、**[SQL サーバー]** の順に選択します。
3.  復元する削除済みデータベースが含まれるサーバーに移動し、サーバーを選択します。
4.  下へスクロールしてサーバー ブレードの **[操作]** セクションに移動し、**[削除済みデータベース]** を選択します: ![Azure SQL データベースの復元](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  復元する削除済みデータベースを選択します。
6.  データベースの名前を指定し、[OK] をクリックします。

    ![Azure SQL データベースの復元](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## 次のステップ

- [復旧された Azure SQL データベースの最終処理を行う](sql-database-recovered-finalize.md)
- [削除されたデータベースの復元](sql-database-restore-deleted-database.md)
- [PowerShell を使用して削除済みデータベースを復元する](sql-database-restore-deleted-database-powershell.md)
- [REST API を使用して削除済みデータベースを復元する](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [SQL Database 自動バックアップ](sql-database-automated-backups.md)

## その他のリソース

- [ポイントインタイム リストア](sql-database-point-in-time-restore.md)
- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [geo リストア](sql-database-geo-restore.md)
- [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)
- [クラウド障害復旧用アプリケーションの設計](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->