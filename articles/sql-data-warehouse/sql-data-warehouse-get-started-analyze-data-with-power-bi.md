<properties
    pageTitle="Power BI での SQL Data Warehouse データの分析 | Microsoft Azure"
    description="Power BI での SQL Data Warehouse データの分析"
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
    ms.date="10/07/2015"
    ms.author="lodipalm"/>

# Power BI を使用したデータ分析
このチュートリアルでは、Power BI を使用して、SQL Data Warehouse に接続し、いくつかの基本的な視覚化を作成する方法を示します。

> [AZURE.NOTE]このチュートリアルを完了するには、AdventureWorksDW サンプル データベースを事前に読み込まれている SQL Data Warehouse データベースが必要です。「[Create a SQL Data Warehouse (SQL Data Warehouse の作成)](sql-data-warehouse-get-started-provision.md)」では、作成方法が示されています。
> 
> SQL Data Warehouse データベースは既にあっても、サンプル データがない場合は、[サンプル データを手動で読み込む][]ことができます。


## SQL Data Warehouse データベースに接続する

Power BI を開き、AdventureWorksDW データベースに接続するには:

1. [Azure プレビュー ポータル][]にサインインします。
2. **[SQL Database]** をクリックし、AdventureWorks SQL Data Warehouse データベースを選択します。 

    ![データベースの検索][1]

3. [Power BI で開く] ボタンをクリックします。

    ![Power BI ボタン][2]

4. SQL Data Warehouse の接続ページにデータベースの情報を表示されます。パスワードを入力すると、SQL Data Warehouse データベースに完全に接続されます。


## Power BI を使用したデータ分析

Power BI を使用して AdventureWorksDW サンプル データを分析する準備が整いました。分析を実行するため、AdventureWorksDW には AggregateSales という名前のビューがあります。このビューには、会社の売上高を分析するための重要なメトリックがいくつか含まれています。

1. 郵便番号に従って売上高のマップを作成するには、AggregateSales ビューをクリックして展開し、PostalCode と SalesAmount の列をクリックします。Power BI はこのデータを地理データとして自動的に認識し、マップに配置します。

    ![Power BI マップ][3]

2. 売上高の棒グラフを作成するには、SalesAmount 列だけをクリックします。詳細を追加するには、CustomerIncome チャートを AggregateSales の左にある [軸] フィールドにドラッグすると、顧客の収入別の売り上げが表示されます。

    ![Power BI バー][4]

3. 売り上げのタイムラインを作成する場合は、SalesAmount、OrderDate、および [折れ線グラフ] ([視覚エフェクト] の 2 行目の最初のアイコン) をクリックします。

    ![Power BI ライン][5]

左上にある **[保存]** をクリックし、視覚エフェクトをレポートとして保存することで、進行状況をいつでも保存できます。

## 次のステップ
いくつかサンプル データを挙げて、どのようにして[開発][]、[ロード][]、[移行][]するかを確認しながらウォーミングアップしていきます。

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-find-database.png
[2]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-button.png
[3]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-map.png
[4]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-bar.png
[5]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-line.png

<!--Article references-->
[移行]: sql-data-warehouse-overview-migrate.md
[開発]: sql-data-warehouse-overview-develop.md
[ロード]: sql-data-warehouse-overview-load.md
[サンプル データを手動で読み込む]: sql-data-warehouse-get-started-manually-load-samples.md
[Azure プレビュー ポータル]: https://portal.azure.com/
[Power BI]: http://www.powerbi.com/
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!---HONumber=Oct15_HO2-->