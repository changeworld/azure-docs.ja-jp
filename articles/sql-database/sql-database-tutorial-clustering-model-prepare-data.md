---
title: チュートリアル:R でクラスタリングを実行するためのデータを準備する
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: この 3 部構成のチュートリアル シリーズのパート 1 では、Azure SQL Database Machine Learning Services (プレビュー) を使用して R でクラスタリングを実行するために Azure SQL データベースのデータを準備します。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 07/29/2019
ms.openlocfilehash: 800dbfc05c47a949bf024e9a5c671979b49ad201
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "68639975"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>チュートリアル:Azure SQL Database Machine Learning Services (プレビュー) を使用して R でクラスタリングを実行するためのデータを準備する

この 3 部構成のチュートリアル シリーズのパート 1 では、R を使用して、Azure SQL データベースのデータをインポートして準備します。このシリーズでは、その後、このデータを利用し、R で Azure SQL Database Machine Learning Services (プレビュー) を使用してクラスタリング モデルをトレーニングしてデプロイします。

*クラスター化*は、グループのメンバーにある意味で類似点があるグループにデータを編成すること、として説明できます。
**K-Means** アルゴリズムを使用して、製品の購入と返品のデータセット内で、顧客のクラスタリングを実行します。 顧客をクラスタリングすることで、特定のグループをターゲットして、マーケティングの取り組みをより効果的に進めることができます。
K-Means クラスタリングは、類似性に基づいてデータのパターンを探す*教師なし学習*アルゴリズムです。

このシリーズのパート 1 と 2 では、使用するデータを準備し、機械学習モデルをトレーニングするために、RStudio 内でいくつかの R スクリプトを開発します。 その後、パート 3 では、ストアド プロシージャを使用して SQL データベース内でそれらの R スクリプトを実行します。

この記事では、次の方法について学習します。

> [!div class="checklist"]
> * Azure SQL データベースにサンプル データベースをインポートする
> * R を使用してさまざまな特徴に従って顧客を分類する
> * Azure SQL データベースから R データ フレームにデータを読み込む

[パート 2](sql-database-tutorial-clustering-model-build.md) では、R で K-Means クラスタリング モデルを作成してトレーニングする方法について説明します。

[パート 3](sql-database-tutorial-clustering-model-deploy.md) では、R で新しいデータに基づいてクラスタリングを実行できるストアド プロシージャを Azure SQL データベースで作成する方法について学習します。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - Azure サブスクリプションをお持ちでない場合は、開始する前に[アカウントを作成](https://azure.microsoft.com/free/)してください。

* Machine Learning Services が有効な Azure SQL Database サーバー - パブリック プレビュー期間中は、Microsoft でお客様のオンボードを行い、既存のデータベースまたは新しいデータベースに対して機械学習を有効にします。 「[Sign up for the preview (プレビューにサインアップする)](sql-database-machine-learning-services-overview.md#signup)」の手順に従ってください。

* RevoScaleR パッケージ - このパッケージをローカルでインストールするためのオプションについては、[RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) に関するページを参照してください。

* R IDE - このチュートリアルでは [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) を使用します。

* SQL クエリ ツール - このチュートリアルでは、[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) または [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) を使用していることが前提となります。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="import-the-sample-database"></a>サンプル データベースをインポートする

このチュートリアルで使用されるサンプル データセットは、 **.bacpac** データベース バックアップ ファイルに保存されています。このファイルをダウンロードして使用します。 このデータセットは、[トランザクション処理性能評議会 (TPC)](http://www.tpc.org/default.asp) によって提供される [tpcx-bb](http://www.tpc.org/tpcx-bb/default.asp) データセットから派生しています。

1. ファイル [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac) をダウンロードします。

1. これらの詳細を使用して、[Azure SQL データベースを作成するための BACPAC ファイルのインポート](https://docs.microsoft.com/azure/sql-database/sql-database-import)に関するページの指示に従います。

   * ダウンロードした **tpcxbb_1gb.bacpac** ファイルからインポートする
   * パブリック プレビュー期間中は、新しいデータベースに対して **Gen5/vCore** 構成を選択する
   * 新しいデータベースに "tpcxbb_1gb" という名前を付ける

## <a name="separate-customers"></a>顧客を分離する

RStudio で新しい RScript ファイルを作成し、以下のスクリプトを実行します。
SQL クエリで、次の特徴に従って顧客を分類しています。

* **orderRatio** = 注文返品率 (注文の総数に対する部分的または完全に返品された注文の総数)
* **itemsRatio** = アイテム返品率 (購入されたアイテムの総数に対する返品されたアイテムの総数)
* **monetaryRatio** = 返品金額率 (合計購入金額に対する返されたアイテムの合計金額)
* **frequency** = 返品の頻度

**paste** 関数では、**Server**、**UID**、および **PWD** を実際の接続情報に置き換えます。

```r
# Define the connection string to connect to the tpcxbb_1gb database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=tpcxbb_1gb",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");


#Define the query to select data from SQL Server
input_query <- "
SELECT ss_customer_sk AS customer
    ,round(CASE 
            WHEN (
                       (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio
    ,round(CASE 
            WHEN (
                     (orders_items = 0)
                  OR (returns_items IS NULL)
                  OR (orders_items IS NULL)
                  OR ((returns_items / orders_items) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio
    ,round(CASE 
            WHEN (
                     (orders_money = 0)
                  OR (returns_money IS NULL)
                  OR (orders_money IS NULL)
                  OR ((returns_money / orders_money) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio
    ,round(CASE 
            WHEN (returns_count IS NULL)
            THEN 0.0
            ELSE returns_count
            END, 0) AS frequency
FROM (
    SELECT ss_customer_sk,
        -- return order ratio
        COUNT(DISTINCT (ss_ticket_number)) AS orders_count,
        -- return ss_item_sk ratio
        COUNT(ss_item_sk) AS orders_items,
        -- return monetary amount ratio
        SUM(ss_net_paid) AS orders_money
    FROM store_sales s
    GROUP BY ss_customer_sk
    ) orders
LEFT OUTER JOIN (
    SELECT sr_customer_sk,
        -- return order ratio
        count(DISTINCT (sr_ticket_number)) AS returns_count,
        -- return ss_item_sk ratio
        COUNT(sr_item_sk) AS returns_items,
        -- return monetary amount ratio
        SUM(sr_return_amt) AS returns_money
    FROM store_returns
    GROUP BY sr_customer_sk
    ) returned ON ss_customer_sk = sr_customer_sk
"
```

## <a name="load-the-data-into-a-data-frame"></a>データをデータ フレームに読み込む

ここで、次のスクリプトを使用すると、**rxSqlServerData** 関数を使用してクエリの結果が R データ フレームに返されます。
このプロセスの一環として、選択した列の型を (colClasses を使用して) 定義し、その型が正しく R に転送されることを確認します。

```r
# Query SQL Server using input_query and get the results back
# to data frame customer_returns
# Define the types for selected columns (using colClasses),
# to make sure that the types are correctly transferred to R
customer_returns <- rxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);

# Transform the data from an input dataset to an output dataset
customer_data <- rxDataStep(customer_returns);

# Take a look at the data just loaded from SQL Server
head(customer_data, n = 5);
```

次のような結果が表示されます。

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

***このチュートリアルを続行しない場合は***、Azure SQL Database サーバーから tpcxbb_1gb データベースを削除します。

Azure portal から次の手順を実行します。

1. Azure portal の左側のメニューから、 **[すべてのリソース]** または **[SQL データベース]** を選択します。
1. **[名前でフィルター]** フィールドに、「**tpcxbb_1gb**」と入力し、自分のサブスクリプションを選択します。
1. **tpcxbb_1gb** データベースを選択します。
1. **[概要]** ページで **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアル シリーズの第 1 部では、これらの手順を完了しました。

* Azure SQL データベースにサンプル データベースをインポートする
* R を使用してさまざまな特徴に従って顧客を分類する
* Azure SQL データベースから R データ フレームにデータを読み込む

この顧客データを使用する機械学習モデルを作成するには、このチュートリアル シリーズのパート 2 に従ってください。

> [!div class="nextstepaction"]
> [チュートリアル:R で Azure SQL Database Machine Learning Services (プレビュー) を使用して予測モデルを作成する](sql-database-tutorial-clustering-model-build.md)
