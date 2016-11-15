---
title: "SQL Data Warehouse のデータを Power BI で視覚化する | Microsoft Azure"
description: "SQL Data Warehouse のデータを Power BI で視覚化します。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: d7fb89d1-da1d-4788-a111-68d0e3fda799
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f498f4546e8c23f2141d8d30160a360fa0fc2514


---
# <a name="visualize-data-with-power-bi"></a>Power BI でデータを視覚化する
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

このチュートリアルでは、Power BI を使用して、SQL Data Warehouse に接続し、いくつかの基本的な視覚化を作成する方法を示します。

> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/Azure-SQL-Data-Warehouse-Sample-Data-and-PowerBI/player]
> 
> 

## <a name="prerequisites"></a>前提条件
このチュートリアルを進めるには、次が必要です。

* AdventureWorksDW データベースが事前に読み込まれている SQL Data Warehouse。 これをプロビジョニングするには、[SQL Data Warehouse の作成][SQL Data Warehouse の作成]に関するページを参照し、サンプル データの読み込みを選択してください。 データ ウェアハウスは既にあってもサンプル データがない場合は、[サンプル データを手動で読み込む][サンプル データを手動で読み込む]ことができます。

## <a name="1-connect-to-your-database"></a>1.データベースに接続する
Power BI を開き、AdventureWorksDW データベースに接続するには:

1. [Azure Portal][Azure Portal] にサインインします。
2. **[SQL Database]** をクリックし、AdventureWorks SQL Data Warehouse データベースを選択します。
   
    ![データベースの検索][1]
3. [Power BI で開く] ボタンをクリックします。
   
    ![Power BI ボタン][2]
4. SQL Data Warehouse の接続ページにデータベースの Web アドレスが表示されます。 [次へ] をクリックします。
   
    ![Power BI connection][3]
5. Azure SQL Server のユーザー名とパスワードを入力すると、SQL Data Warehouse データベースに完全に接続されます。
   
    ![Power BI sign in][4]
6. Power BI にサインインしたら、左側のブレードの AdventureWorksDW データセットをクリックします。 これでデータベースが開きます。
   
    ![Power BI open AdventureWorksDW][5]

## <a name="2-create-a-report"></a>2.レポートの作成
Power BI を使用して AdventureWorksDW サンプル データを分析する準備が整いました。 分析を実行するため、AdventureWorksDW には AggregateSales という名前のビューがあります。 このビューには、会社の売上高を分析するための重要なメトリックがいくつか含まれています。

1. 郵便番号に基づく売上高の地図を作成するには、右側のフィールド ペインにある [AggregateSales] ビューをクリックして展開します。 [PostalCode] 列と [SalesAmount] 列をクリックして選択します。
   
    ![Power BI select AggregateSales][6]
   
    Power BI はこのデータを地理データとして自動的に認識し、マップに配置します。
   
    ![Power BI マップ][7]
2. この手順では、顧客の所得ごとの売上高を表示する棒グラフを作成します。 これを作成するには、展開した AggregateSales ビューにアクセスします。 [SalesAmount] フィールドをクリックします。 [Customer Income] フィールドを左方向へドラッグし、[軸] にドロップします。
   
    ![Power BI select axis][8]
   
    棒グラフは左側に移動しました。
   
    ![Power BI バー][9]
3. この手順では、発注日ごとの売上高を表示する折れ線グラフを作成します。 これを作成するには、展開した AggregateSales ビューにアクセスします。 [SalesAmount] と [OrderDate] をクリックします。 [視覚化] 列で、折れ線グラフのアイコンをクリックします。"視覚化" の下の 2 行目先頭にあるアイコンです。
   
    ![Power BI select line chart][10]
   
    これで、同じデータを 3 とおりの方法で視覚化したレポートが完成しました。
   
    ![Power BI ライン][11]

**[ファイル]** をクリックして **[保存]** を選択すると、作業をいつでも保存できます。

## <a name="next-steps"></a>次のステップ
いくつかサンプル データを挙げて、[開発][開発]、[読み込み][読み込み]、[移行][移行]の方法を確認しながらウォーミングアップしていきます。 または、[Power BI の Web サイト][Power BI の Web サイト]を参照してください。

<!--Image references-->
[1]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-find-database.png
[2]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-button.png
[3]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-connect-to-azure.png
[4]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-sign-in.png
[5]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-open-adventureworks.png
[6]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-aggregatesales.png
[7]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-map.png
[8]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-chooseaxis.png
[9]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-bar.png
[10]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-prepare-line.png
[11]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-line.png
[12]: media/sql-data-warehouse-get-started-visualize-with-power-bi/pbi-save.png

<!--Article references-->
[移行]: sql-data-warehouse-overview-migrate.md
[開発]: sql-data-warehouse-overview-develop.md
[読み込み]: sql-data-warehouse-overview-load.md
[サンプル データを手動で読み込む]: sql-data-warehouse-load-sample-databases.md
[SQL Data Warehouse への接続]: sql-data-warehouse-integrate-power-bi.md
[SQL Data Warehouse の作成]: sql-data-warehouse-get-started-provision.md

<!--Other-->
[Azure ポータル]: https://portal.azure.com/
[Power BI の Web サイト]: http://www.powerbi.com/



<!--HONumber=Nov16_HO2-->


