<properties
	pageTitle="SQL Database への Excel の接続 | Microsoft Azure"
	description="Microsoft Excel をクラウド内の Azure SQL データベースに接続する方法について説明します。レポートの作成およびデータの探索を目的として、データを Excel にインポートします。"
	services="sql-database"
	keywords="SQL への Excel の接続、Excel へのデータのインポート"
	documentationCenter=""
	authors="joseidz"
	manager="jeffreyg"
	editor="jeffreyg"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/04/2016"
	ms.author="joseidz"/>


# Excel を Azure SQL データベースに接続し、レポートを作成する 

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Excel にデータをインポートするために Excel を SQL データベースに接続する方法について説明します。次に、データに関するレポートの作成方法を説明します。

まず、SQL データベースが必要です。SQL データベースがない場合は、「[最初の SQL Database を作成する](sql-database-get-started.md)」を参照することで、サンプル データを含むデータベースを数分で作成して実行できます。この記事では、サンプル データを Excel にインポートしますが、独自のデータでも同様の手順を実行できます。

また、Excel も必要です。この記事では、[Microsoft Excel 2016](https://products.office.com/ja-JP/) を使用します。

## Excel を SQL データベースに接続し、レポートを作成する

1.	Excel を SQL データベースに接続するには、Excel を開き、新しいブックを作成します。または、SQL データベースに接続する既存の Excel ブックを開きます。

2.	ページの上部にあるメニュー バーで、**[データ]**、**[その他のデータ ソース]**、**[SQL Server]** の順にクリックします。

	![データ ソースの選択: SQL データベースに Excel を接続します。](./media/sql-database-connect-excel/excel_data_source.png)

	データ接続ウィザードが開きます。

3.	**[データベース サーバーへの接続]** ダイアログ ボックスで、接続する論理サーバーをホストする**サーバー名**を **<*servername*>.database.windows.net** の形式で入力します。たとえば、「**adventureserver.database.windows.net**」のように入力します。

4.	**[ログオン資格情報]** セクションで、**[次のユーザー名とパスワードを使用する]** をクリックし、SQL Database サーバーを作成した際に設定した**ユーザー名**と**パスワード**を入力して、**[次へ]** をクリックします。

	> [AZURE.TIP] Excel の [PowerPivot](https://www.microsoft.com/download/details.aspx?id=102) アドインと [Power Query](https://www.microsoft.com/download/details.aspx?id=39379) アドインは、よく似た機能を備えています。

5. **[データベースとテーブルの選択]** ダイアログ ボックスで、プルダウン メニューから **[AdventureWorks]** データベースを選択し、テーブルとビューの一覧から **[vGetAllCategories]** を選択して、**[次へ]** をクリックします。

	![データベースとテーブルを選択します。][5]

6. **[データ接続ファイルを保存して終了]** ダイアログ ボックスで、**[完了]** をクリックします。

7. **[データのインポート]** ダイアログ ボックスで **[ピボットグラフ]** を選択し、**[OK]** をクリックします。

	![Excel へのデータのインポート: [データのインポート] ダイアログ ボックスで [PivotChart] を選択します。][2]

8. **[ピボットグラフ フィールド]** ダイアログ ボックスで、カテゴリごとに製品数のレポートを作成するために、次の構成を選択します。

	![データベース レポートの構成][3]

	成功すると、次のような画面が表示されます。

	![成功: Excel が SQL データベースに接続されました。][4]

## 次のステップ

サービスとしてのソフトウェア (SaaS) 開発者である場合は、[エラスティック データベース プール](sql-database-elastic-pool.md)について学習してください。[Elastic Database ジョブ](sql-database-elastic-jobs-overview.md)を使用すれば、大規模なデータベースのコレクションを簡単に管理できます。

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=AcomDC_0204_2016-->