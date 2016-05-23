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
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Azure ポータルを使用した、削除済み Azure SQL データベースの復元


> [AZURE.SELECTOR]
- [Azure ポータル](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

この記事では、削除済みの Azure SQL データベースを復元する方法について説明します。

データベースが削除された場合、Azure SQL Database では、削除されたデータベースを削除された時点の状態に復元できます。Azure SQL Database では、データベースの保持期間にわたり、削除されたデータベースのバックアップを格納します。

削除されたデータベースの保持期間は、データベースのサービス階層に基づく日数とデータベースが存在していた日数のどちらか短い方になります。データベースのリテンション期間の詳細については、[ビジネス継続性の概要](sql-database-business-continuity.md)に関するページをお読みください。

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
- [SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)



## その他のリソース

- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->