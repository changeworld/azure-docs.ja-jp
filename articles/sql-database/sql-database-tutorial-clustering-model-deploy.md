---
title: チュートリアル:R でクラスタリング モデルをデプロイする
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: この 3 部構成のチュートリアル シリーズのパート 3 では、Azure SQL Database Machine Learning Services (プレビュー) を使用して R でクラスタリング モデルをデプロイします。
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
ms.openlocfilehash: d67f007ac91d4830557a2cae646698b130b02314
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345800"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>チュートリアル:Azure SQL Database Machine Learning Services (プレビュー) を使用して R でクラスタリング モデルをデプロイする

この 3 部構成のチュートリアル シリーズのパート 3 では、Azure SQL Database Machine Learning Services (プレビュー) を使用して、R で開発したクラスタリング モデルを SQL データベースにデプロイします。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

クラスタリングを実行する埋め込みの R スクリプトを含むストアド プロシージャを作成します。 モデルは Azure SQL データベースで実行されるため、データベースに格納されているデータに対して簡単にトレーニングすることができます。

この記事では、次の方法について学習します。

> [!div class="checklist"]
> * モデルを生成するストアド プロシージャの作成
> * SQL Database 上でのクラスタリング実行
> * クラスタリング情報の使用

[パート 1](sql-database-tutorial-clustering-model-prepare-data.md) では、クラスタリングを実行するために Azure SQL データベースのデータを準備する方法を学習しました。

[パート 2](sql-database-tutorial-clustering-model-build.md) では、R で K-Means クラスタリング モデルを作成してトレーニングする方法について学習しました。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>前提条件

* このチュートリアル シリーズのパート 3 では、[**パート 1**](sql-database-tutorial-clustering-model-prepare-data.md) と[**パート 2**](sql-database-tutorial-clustering-model-build.md) が完了していることを前提とします。

## <a name="create-a-stored-procedure-that-generates-the-model"></a>モデルを生成するストアド プロシージャの作成

以下の T-SQL スクリプトを実行して、ストアド プロシージャを作成します。 このプロシージャでは、本チュートリアル シリーズの第 1 部・2 部で作成した手順を再作成します。

* 購入・返却履歴に基づく顧客の分類
* K-Means アルゴリズムを使用した、4 つの顧客クラスターの生成

このプロシージャにより、結果として作成された顧客のクラスター マッピングがデータベース テーブル **customer_return_clusters** に格納されます。

```sql
USE [tpcxbb_1gb]
DROP PROC IF EXISTS generate_customer_return_clusters;
GO
CREATE procedure [dbo].[generate_customer_return_clusters]
AS
/*
  This procedure uses R to classify customers into different groups
  based on their purchase & return history.
*/
BEGIN
    DECLARE @duration FLOAT
    , @instance_name NVARCHAR(100) = @@SERVERNAME
    , @database_name NVARCHAR(128) = db_name()
-- Input query to generate the purchase history & return metrics
    , @input_query NVARCHAR(MAX) = N'
SELECT ss_customer_sk AS customer,
    round(CASE 
            WHEN (
                    (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio,
    round(CASE 
            WHEN (
                    (orders_items = 0)
                    OR (returns_items IS NULL)
                    OR (orders_items IS NULL)
                    OR ((returns_items / orders_items) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio,
    round(CASE 
            WHEN (
                    (orders_money = 0)
                    OR (returns_money IS NULL)
                    OR (orders_money IS NULL)
                    OR ((returns_money / orders_money) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio,
    round(CASE 
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
 '
EXECUTE sp_execute_external_script
      @language = N'R'
    , @script = N'
# Define the connection string
connStr <- paste("Driver=SQL Server; Server=", instance_name,
               "; Database=", database_name,
               "; Trusted_Connection=true; ",
                  sep="" );

# Input customer data that needs to be classified.
# This is the result we get from the query.
customer_returns <- RxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);
# Output table to hold the customer cluster mappings
return_cluster = RxSqlServerData(table = "customer_return_clusters",
                                 connectionString = connStr);

# Set seed for random number generator for predictability
set.seed(10);

# Generate clusters using rxKmeans and output clusters to a table
# called "customer_return_clusters"
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters = 4,
                   outFile = return_cluster,
                   outColName = "cluster",
                   writeModelVars = TRUE ,
                   extraVarsToWrite = c("customer"),
                   overwrite = TRUE);
'
    , @input_data_1 = N''
    , @params = N'@instance_name nvarchar(100), @database_name nvarchar(128), @input_query nvarchar(max), @duration float OUTPUT'
    , @instance_name = @instance_name
    , @database_name = @database_name
    , @input_query = @input_query
    , @duration = @duration OUTPUT;
END;

GO
```

## <a name="perform-clustering-in-sql-database"></a>SQL Database 上でのクラスタリング実行

ストアド プロシージャを作成したところで、次のスクリプトを実行してクラスタリングを実行します。

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

それが動作することと、実際に顧客とそのクラスター マッピングの一覧が表示されていることを確認します。

```sql
--Select data from table customer_return_clusters
--to verify that the clustering data was loaded
SELECT TOP (5) *
FROM customer_return_clusters;
```

```result
cluster  customer  orderRatio  itemsRatio  monetaryRatio  frequency
1        29727     0           0           0              0
4        26429     0           0           0.041979       1
2        60053     0           0           0.065762       3
2        97643     0           0           0.037034       3
2        32549     0           0           0.031281       4
```

## <a name="use-the-clustering-information"></a>クラスタリング情報の使用

クラスタリングのプロシージャはデータベースに格納されているため、同じデータベースに格納されている顧客データに対し、効率的にクラスタリングを実行できます。 顧客データが更新されるたびにプロシージャを実行し、更新されたクラスタリング情報を利用できます。

クラスター 3 (より積極的に返品を行うグループ) の顧客にプロモーション用のメールを送信したいとします (4 つのクラスターの説明は[パート 2](sql-database-tutorial-clustering-model-build.md#analyze-the-results) を参照してください)。 次のコードでは、クラスター 3 に含まれる顧客のメール アドレスを選択します。

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

**r.cluster**の値を変更すると、他のクラスターに含まれる顧客のメール アドレスを返すことができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルを完了した場合は、Azure SQL Database サーバーから tpcxbb_1gb データベースを削除できます。

Azure portal から次の手順を実行します。

1. Azure portal の左側のメニューから、 **[すべてのリソース]** または **[SQL データベース]** を選択します。
1. **[名前でフィルター]** フィールドに、「**tpcxbb_1gb**」と入力し、自分のサブスクリプションを選択します。
1. **tpcxbb_1gb** データベースを選択します。
1. **[概要]** ページで **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアル シリーズのパート 3 では、次の手順を完了しました。

* モデルを生成するストアド プロシージャの作成
* SQL Database 上でのクラスタリング実行
* クラスタリング情報の使用

Azure SQL Database Machine Learning Services (プレビュー) での R の使用について、さらに学習する場合は、以下を参照してください。

* [チュートリアル:R で Azure SQL Database Machine Learning Services (プレビュー) を使用して予測モデルをトレーニングするためのデータを準備する](sql-database-tutorial-predictive-model-prepare-data.md)
* [Machine Learning Services (プレビュー) を使用して Azure SQL Database に高度な R 関数を記述する](sql-database-machine-learning-services-functions.md)
* [Azure SQL Database Machine Learning Services (プレビュー) での R および SQL データの処理](sql-database-machine-learning-services-data-issues.md)
* [Azure SQL Database Machine Learning Services (プレビュー) に R パッケージを追加する](sql-database-machine-learning-services-add-r-packages.md)
