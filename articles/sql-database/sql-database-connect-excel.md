<properties
	pageTitle="Excel を使用した Azure SQL Database への接続"
	description="Excel スプレッドシートを Azure SQL Database に接続して、レポート作成やデータ探索を行います。"
	services="sql-database"
	documentationCenter=""
	authors="joseidz"
	manager="joseidz"
	editor="joseidz"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="joseidz"/>


# Excel を使用した Azure SQL Database への接続
Excel を Azure SQL Database に接続し、データベース内のデータに関するレポートを作成します。

## 前提条件
- Azure SQL Database のプロビジョニングと実行。新しい SQL Database を作成するには、「[Microsoft Azure SQL Database の概要](sql-database-get-started.md)」をご覧ください。
- [Microsoft Excel 2013](https://products.office.com/ja-jp/) (または Microsoft Excel 2010)

## SQL Database に接続し、レポートを作成する
1.	Excel を開きます。
2.	ページの上部にあるメニュー バーで **[データ] **をクリックします。
3.	**[その他のデータ ソース]** をクリックし、**[SQL Server]** をクリックします。**[データ接続ウィザード]** が表示されます。

	![データ接続ウィザード][1]
4.	**[サーバー名]** ボックスに、Azure SQL Database のサーバー名を入力します。例:

	 	adventureserver.database.windows.net
5.	**[ログオン時の証明]** セクションで、**[以下のユーザー名とパスワードを使用する]** を選択し、SQL Database サーバーの適切な資格情報を入力します。その後、**[次へ]** をクリックします。

	注: Excel の [PowerPivot](https://www.microsoft.com/download/details.aspx?id=102) アドインと [Power Query](https://www.microsoft.com/download/details.aspx?id=39379) のアドインは、非常によく似た機能を備えています。

6. **[データベースとテーブルの選択]** ダイアログ ボックスで、プルダウン メニューから **[AdventureWorks]** データベースを選択し、テーブルとビューの一覧から **[vGetAllCategories]** を選択して、**[次へ]** をクリックします。

	![データベースとテーブルの選択][5]
7. **[データ接続ファイルを保存して終了]** ダイアログ ボックスで、**[完了]** をクリックします。
8. **[データのインポート]** ダイアログ ボックスで **[ピボットグラフ]** を選択し、**[OK]** をクリックします。

	![インポート データの選択][2]
9. **[ピボットグラフ フィールド]** ダイアログ ボックスで、カテゴリごとに製品数のレポートを作成するために、次の構成を選択します。

	![構成][3]
10.	成功すると、次のような画面が表示されます。

	![成功][4]

## 次のステップ

サービスとしてのソフトウェア (SaaS) 開発者である場合は、[エラスティック データベース プール](sql-database-elastic-pool.md)について学習してください。[エラスティック データベース ジョブ](sql-database-elastic-jobs-overview.md)を使用すれば、大規模なデータベースのコレクションを簡単に管理できます。

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=July15_HO4-->