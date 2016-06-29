<properties
	pageTitle="geo 冗長バックアップからの Azure SQL Database の復元 (Azure ポータル) | Microsoft Azure"
	description="geo 冗長バックアップから Azure SQL Database を geo リストアします (Azure ポータル)。"
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


# Azure ポータルを使用した geo 冗長バックアップからの Azure SQL Database の geo リストア


> [AZURE.SELECTOR]
- [概要](sql-database-geo-restore.md)
- [Azure ポータル](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

この記事では、Azure ポータルで geo リストアを使用して新しいサーバーにデータベースを復元する方法について説明します。

## 復元するデータベースの選択

Azure ポータルでデータベースを復元するには、次の手順を実行します。

1.	[Azure ポータル](https://portal.azure.com) を開きます。
2.  画面の左側で、**[新規]**、**[データ + ストレージ]**、**[SQL Database]** の順に選択します。
3.  ソースとして **[バックアップ]** を選択し、復元する geo 冗長バックアップを選択します。

    ![Azure SQL データベースの復元](./media/sql-database-geo-restore-portal/geo-restore.png)

4.  データベース名と、データベースの復元先サーバーを指定し、[作成] をクリックします。

## 次のステップ

- [SQL Server Management Studio を使用して SQL Database に接続し、T-SQL サンプル クエリを実行する](sql-database-connect-query-ssms.md)
- [災害復旧訓練](sql-database-disaster-recovery-drills.md)


## その他のリソース

- [geo リストア](sql-database-geo-restore.md)
- [ビジネス継続性の概要](sql-database-business-continuity.md)
- [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0615_2016-->