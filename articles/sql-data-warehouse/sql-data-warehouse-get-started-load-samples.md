<properties
   pageTitle="SQL Data Warehouse へのサンプル データのロード | Microsoft Azure"
   description="SQL Data Warehouse にサンプル データをロードする方法を説明します"
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
   ms.date="03/03/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# SQL Data Warehouse へのサンプル データのロード

SQL Data Warehouse インスタンスを作成するときに、何らかのサンプル データを簡単に読み込むことができます。プロビジョニング時にこの手順を実行しなかった場合は、[手動でサンプル データを読み込む][]こともできます。

ここでは、AdventureWorksDW をデータベースに読み込む方法の概要を説明します。このデータセットは、AdventureWorks という架空の企業のサンプル データ ウェアハウス構造を元に作られています。データは主にこの会社の売上と顧客に関するものです。

## 作成時にサンプル データを追加する
次の手順で、デプロイメント中にサンプル データを SQL Data Warehouse に読み込むことができます。

1. [Azure クラシック ポータル][]で SQL Data Warehouse を検索して作成プロセスを開始するには、[+ 新規]、[データ + ストレージ] の順にクリックするか、Marketplace で "SQL Data Warehouse" を検索します。

2. プロセスが開始されたら、[ソースの選択] をクリックして、[サンプル] に設定します。新しいサーバーを作成しない場合は、作成に使用しているサーバーのログイン情報の入力も求められます。


> [AZURE.NOTE] インスタンスにサンプル データを読み込むには、Azure サービスでサーバーへのアクセスを有効にする必要があります (新しいサーバーを作成する場合の既定では有効です)。有効にしないと、読み込みは失敗しますが、[手動でサンプルを読み込む][]ことはできます。


## PowerBI を使用して Adventureworks を分析する

初めて PowerBI を使用するときは、サンプル データ セットを使用することをお勧めします。サンプル データを読み込んだ後に、SQL Data Warehouse への接続を開くには、Azure クラシック ポータルで [PowerBI で開く] をクリックするか、[PowerBI][] を開き、[SQL Data Warehouse に接続][]します。接続後は、データ ウェアハウスと同じ名前で新しいデータセットが作成されます。分析を簡単にするために、ここでは、会社の売上分析に重要ないくつかのメトリックを含む 'AggregateSales' というビューを作成しました。このビューの名前をクリックすると、ビューが展開されて含まれる列が表示されます。次の手順で、いくつかの簡単な視覚エフェクトを作成できます。

1. まず、[PostalCode] 列と [SalesAmount] 列をクリックして、全売上のマップを簡単に作成できます。Power BI では、このデータを地理データとして自動的に認識し、マップに配置することができます。

2. 売上の棒グラフを作成するには、[SalesAmount] 列をクリックすると、自動的に作成されます。[CustomerIncome] グラフを [AggregateSales] の左にある [Axis] フィールドにドラッグすると、次元が追加され、顧客の収入ブラケット別の売上が表示されます。

3. 最後に、売上のタイムラインを作成する場合、必要な操作は、[SalesAmount]、[OrderDate]、および \[折れ線グラフ] ([視覚エフェクト] の 2 行目の最初のアイコン) のクリックのみです。

左上にある [保存] をクリックし、視覚エフェクトをレポートとして保存することで、進行状況をいつでも保存できます。

## サンプルへの接続とクエリ

また、従来の方法を使用してサンプル データを分析することもできます。「[接続とクエリ][]」で説明されているように、Visual Studio で SQL Server Data Tools を使用してこのデータベースに接続することができます。SQL Data Warehouse にサンプル データをロードしたら、いくつかのクエリの実行をすぐに開始できます。

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
[移行]: ./sql-data-warehouse-overview-migrate.md
[開発]: ./sql-data-warehouse-overview-develop.md
[ロード]: ./sql-data-warehouse-overview-load.md
[接続とクエリ]: ./sql-data-warehouse-get-started-connect.md
[コードの移行]: ./sql-data-warehouse-migrate-code.md
[手動でサンプル データを読み込む]: ./sql-data-warehouse-get-started-manually-load-samples.md
[手動でサンプルを読み込む]: ./sql-data-warehouse-get-started-manually-load-samples.md
[Azure クラシック ポータル]: https://portal.azure.com/
[PowerBI]: http://www.powerbi.com/
[SQL Data Warehouse に接続]: ./sql-data-warehouse-integrate-power-bi.md

<!--MSDN references-->
[Microsoft Command Line Utilities for SQL Server]: http://www.microsoft.com/download/details.aspx?id=36433/

<!--Other Web references-->
[Sample Data Scripts]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksPDW2012.zip/

<!---HONumber=AcomDC_0309_2016-->