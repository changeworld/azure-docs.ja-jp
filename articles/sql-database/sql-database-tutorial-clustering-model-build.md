---
title: チュートリアル:R でクラスタリング モデルを作成する
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: この 3 部構成のチュートリアル シリーズのパート 2 では、Azure SQL Database Machine Learning Services (プレビュー) を使用して、R でクラスタリングを実行する K-Means モデルを作成します。
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
ms.openlocfilehash: 0a73a2bc3fa76c945cf699133a41b38a9983a234
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345813"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>チュートリアル:Azure SQL Database Machine Learning Services (プレビュー) を使用して R でクラスタリング モデルを作成する

この 3 部構成のチュートリアル シリーズのパート 2 では、クラスタリングを実行する K-Means モデルを R で作成します。 このシリーズの次のパートでは、Azure SQL Database Machine Learning Services (プレビュー) を使用して、このモデルを SQL データベースにデプロイします。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

この記事では、次の方法について学習します。

> [!div class="checklist"]
> * K-Means アルゴリズムのクラスター数を定義する
> * クラスタリングを実行する
> * 結果を分析する

[パート 1](sql-database-tutorial-clustering-model-prepare-data.md) では、クラスタリングを実行するために Azure SQL データベースのデータを準備する方法を学習しました。

[パート 3](sql-database-tutorial-clustering-model-deploy.md) では、R で新しいデータに基づいてクラスタリングを実行できるストアド プロシージャを Azure SQL データベースで作成する方法について学習します。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>前提条件

* このチュートリアルのパート 2 では、[**パート 1**](sql-database-tutorial-clustering-model-prepare-data.md) とその前提条件を完了していることを前提としています。

## <a name="define-the-number-of-clusters"></a>クラスターの数を定義する

顧客データのクラスター化には、データをグループ化する最も単純かつ有名な方法の 1 つ、**K-Means** クラスタリング アルゴリズムを使用します。
K-Means の詳細については、[K-means クラスタリング アルゴリズムの詳細ガイド](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html)に関するページを参照してください。

このアルゴリズムでは、次の 2 つの入力を受け取ります。データ自体、および定義済みの数値 "*k*" は、生成するクラスターの数を表します。
出力は、クラスター間でパーティション分割された入力データを持つ *k* クラスターとなっています。

アルゴリズムで使用するクラスターの数を決定するには、抽出されたクラスターの数によって、グループ内の平方和のプロットを使用します。 使用するクラスターの数は、プロットの曲がっている部分または "カギ" の部分になります。

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![カギ線グラフ](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

グラフに基づいて、*k = 4* の値をまず試します。 *k* 値は、顧客を 4 つのクラスターにグループ化します。

## <a name="perform-clustering"></a>クラスタリングを実行する

次の R スクリプトでは、RevoScaleR パッケージの K-Means 関数である **rxKmeans** 関数を使用します。

```r
# Output table to hold the customer group mappings.
# This is a table where the cluster mappings will be saved in the database.
return_cluster = RxSqlServerData(table = "return_cluster", connectionString = connStr);
# Set the seed for the random number generator for predictability
set.seed(10);
# Generate clusters using rxKmeans and output key / cluster to a table in SQL database
# called return_cluster
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters=4,
                   outFile=return_cluster,
                   outColName="cluster",
                   extraVarsToWrite=c("customer"),
                   overwrite=TRUE);

# Read the customer returns cluster table from the database
customer_cluster <- rxDataStep(return_cluster);
```

## <a name="analyze-the-results"></a>結果を分析する

K-Means を使用してクラスタリングを実行したので、次の手順は、結果を分析してアクションにつながる情報を得られるか確認することです。

**clust** オブジェクトには、K-Means クラスタリングの結果が含まれます。

```r
#Look at the clustering details to analyze results
clust
```

```results
Call:
rxKmeans(formula = ~orderRatio + itemsRatio + monetaryRatio + 
    frequency, data = customer_returns, outFile = return_cluster, 
    outColName = "cluster", extraVarsToWrite = c("customer"), 
    overwrite = TRUE, numClusters = 4)
Data: customer_returns
Number of valid observations: 37336
Number of missing observations: 0 
Clustering algorithm:  

K-means clustering with 4 clusters of sizes 31675, 671, 2851, 2139
Cluster means:
   orderRatio   itemsRatio monetaryRatio frequency
1 0.000000000 0.0000000000    0.00000000  0.000000
2 0.007451565 0.0000000000    0.04449653  4.271237
3 1.008067345 0.2707821817    0.49515232  1.031568
4 0.000000000 0.0004675082    0.10858272  1.186068
Within cluster sum of squares by cluster:
         1          2          3          4
    0.0000  1329.0160 18561.3157   363.2188
```

4 つのクラスターは、[パート 1 ](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers)で定義した変数を使用して指定されます。

* *orderRatio* = 注文返品率 (注文の総数に対する部分的または完全に返品された注文の総数)
* *itemsRatio* = アイテム返品率 (購入されたアイテムの総数に対する返品されたアイテムの総数)
* *monetaryRatio* = 返品金額率 (合計購入金額に対する返されたアイテムの合計金額)
* *frequency* = 返品の頻度

多くの場合、K-Means を使用したデータ マイニングでは結果をさらに詳しく分析し、各クラスターについて理解を深めるためにさらに手順を進める必要がありますが、これにより良いスタートを切ることができるようになります。
これらの結果を解釈するいくつかの方法を次に示します。

* クラスター 1 (もっとも大きいクラスター) はアクティブでない顧客グループであると考えられます (すべての値が 0)。
* クラスター 3 は、戻り値の動作の観点では目立つグループであるように見えます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

***このチュートリアルを続行しない場合は***、Azure SQL Database サーバーから tpcxbb_1gb データベースを削除します。

Azure portal から次の手順を実行します。

1. Azure portal の左側のメニューから、 **[すべてのリソース]** または **[SQL データベース]** を選択します。
1. **[名前でフィルター]** フィールドに、「**tpcxbb_1gb**」と入力し、自分のサブスクリプションを選択します。
1. **tpcxbb_1gb** データベースを選択します。
1. **[概要]** ページで **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアル シリーズの第 2 部では、次の手順を完了しました。

* K-Means アルゴリズムのクラスター数を定義する
* クラスタリングを実行する
* 結果を分析する

作成した機械学習モデルをデプロイするには、このチュートリアル シリーズのパート 3 に従ってください。

> [!div class="nextstepaction"]
> [チュートリアル:Azure SQL Database Machine Learning Services (プレビュー) を使用して R でクラスタリング モデルをデプロイする](sql-database-tutorial-clustering-model-deploy.md)