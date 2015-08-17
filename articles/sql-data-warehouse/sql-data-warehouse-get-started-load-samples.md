<properties
   pageTitle="SQL Data Warehouse へのサンプル データのロード | Microsoft Azure"
   description="SQL Data Warehouse へのサンプル データのロード"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/05/2015"
   ms.author="lodipalm;barbkess"/>

#SQL Data Warehouse へのサンプル データのロード

SQL Data Warehouse インスタンスをセットアップしたら、あとは容易にサンプル データをインスタンスにロードできます。以下はデータベースに AdventureWorksPDW2012 と呼ばれるデータセットを作成する手順です。このデータセットは、AdventureWorks という架空の会社のためのデータ ウェアハウスの構造をサンプルとして作っています。以下の手順を進めるには、BCP がインストールされている必要があります。BCP を現時点でインストールしていない場合は、[Microsoft Command Line Utilities for SQL Server][] (SQL Server の Microsoft コマンドライン ユーティリティ) からインストールしてください。

1. はじめに [サンプル データ スクリプト][] をクリックしてダウンロードしてください。

2. ファイルをダウンロードしたら、AdventureWorksPDW2012.zip ファイルを展開し、新しくできた AdventureWorksPDW2012 フォルダーを開きます。

3. aw\_create.bat ファイルを編集し、ファイルの先頭の以下の値を設定します: a **Server**: SQL Data Warehouse が存在するサーバーの完全修飾名。b **User**: 上記のサーバーのユーザー。c **Password**: サーバー ログイン用のパスワード。d **Database**: データのロード先の SQL Data Warehouse インスタンスの名前。

4. aw\_create.bat を存在しているディレクトリ上で実行します。この実行により、BCP を使用して、すべてのテーブルにスキーマを作成してデータをロードします。


## サンプルへの接続とクエリ

「[接続とクエリ][]」のドキュメントで述べたように、このデータベースには Visual Studio や SSDT を使用して接続できます。SQL Data Warehouse にサンプル データをロードしたら、いくつかのクエリの実行をすぐに開始できます。

簡単な SELECT ステートメントを実行して、従業員のすべての情報を取得できます。

	SELECT * FROM DimEmployee;

また、GROUP BY などのコンストラクトを使用する、より複雑なクエリを実行して、日ごとの総売上金額の合計を参照することもできます。

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

WHERE 句を使用して、ある日付以前の注文をフィルター処理できます。

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	WHERE OrderDateKey > '20020801'
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

実際、SQL Data Warehouse では、SQL Server がサポートしているほとんどすべての T-SQL コンストラクトをサポートしており、「[コードの移行][]」ドキュメントで相違点を確認できます。

## 次のステップ
いくつかサンプル データを挙げて、どのようにして[開発][]、[ロード][]、[移行][]するかを確認しながらウォーミングアップしていきます。

<!--Image references-->

<!--Article references-->
[移行]: https://azure.microsoft.com/ja-jp/documentation/articles/sql-data-warehouse-overview-migrate/
[開発]: https://azure.microsoft.com/ja-jp/documentation/articles/sql-data-warehouse-overview-develop/
[ロード]: https://azure.microsoft.com/ja-jp/documentation/articles/sql-data-warehouse-overview-load/
[接続とクエリ]: https://azure.microsoft.com/ja-jp/documentation/articles/sql-data-warehouse-get-started-connect-query/
[コードの移行]: https://azure.microsoft.com/ja-jp/documentation/articles/sql-data-warehouse-migrate-code/

<!--MSDN references-->
[Microsoft Command Line Utilities for SQL Server]: http://www.microsoft.com/ja-jp/download/details.aspx?id=36433

<!--Other Web references-->
[サンプル データ スクリプト]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksPDW2012.zip

<!---HONumber=August15_HO6-->