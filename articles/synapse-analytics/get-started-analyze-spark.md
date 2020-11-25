---
title: 'クイックスタート: Spark を使用して分析を開始する'
description: このチュートリアルでは、Apache Spark を使用してデータを分析する方法について説明します
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 07537e26b169414e3f8ec35cc32945c20f7eb7ce
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843283"
---
# <a name="analyze-with-apache-spark"></a>Apache Spark を使用して分析を行う

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Spark を使用して Blob Storage の NYC タクシー データを分析する

このチュートリアルでは、Apache Spark for Azure Synapse でデータを読み込んで分析する基本的な手順について説明します。

1. **[データ]** ハブで、 **[新しいリソースの追加]** ( **[リンク済み]** の上にある [+] ボタン)、 **[サンプルの参照]** の順にクリックします。 
1. **NYC Taxi & Limousine Commission - yellow taxi trip records (NYC タクシー & リムジン協会 - イエロー タクシー運行記録)** を見つけてクリックします。 
1. ページの下部にある **[続行]** をクリックしたら、 **[データセットの追加]** をクリックします。 
1. **[リンク済み]** の下にある **[データ]** ハブで **[Azure Blob Storage]、[サンプル データセット]、[nyc_tlc_yellow]** を右クリックして順に選択し、 **[新しいノートブック]** を選択します
1. これにより、次のコードを含む新しいノートブックが作成されます。
    ```
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```
1. ノートブックの **[Attach to]\(接続先\)** メニューで、サーバーレス Spark プールを選択します。
1. セルで **[実行]** を選択します。
1. データフレームのスキーマを表示するだけの場合は、次のコードを使用してセルを実行します。
    ```
    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>NYC タクシーのデータを Spark nyctaxi データベースに読み込む

**SQLPOOL1** のテーブルに、使用できるデータがあります。 それを **nyctaxi** という名前の Spark データベースに読み込みます。

1. Synapse Studio で、 **[開発]** ハブに移動します。
1. **[+]**  >  **[ノートブック]** を選択します。
1. ノートブックの上部にある **[アタッチ先]** の値を **[Spark1]** に設定します。
1. **[コードの追加]** を選択してノートブックのコード セルを追加し、次のテキストを貼り付けます。

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. **[データ]** ハブに移動し、 **[データベース]** を右クリックして、 **[更新]** を選択します。 次のデータベースが表示されます。

    - **SQLPOOL1 (SQL)**
    - **nyctaxi (Spark)**

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Spark とノートブックを使用して NYC タクシーのデータを分析する

1. ノートブックに戻ります。
1. 新しいコード セルを作成し、次のテキストを入力します。 その後、このセルを実行して、**nyctaxi** Spark データベースに読み込んだ NYC Taxi データを表示します。

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. 次のコードを実行して、先ほど専用 SQL プール **SQLPOOL1** で行ったのと同じ分析を実行します。 このコードにより、分析の結果が **nyctaxi.passengercountstats** という名前のテーブルに保存され、結果が視覚化されます。

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. セルの結果で **[グラフ]** を選択し、視覚化されたデータを確認します。

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Spark とノートブックを使用してデータの視覚化をカスタマイズする

グラフのレンダリング方法は、ノートブックを使用して制御できます。 次のコードでは、簡単な例を示します。 一般的なライブラリである **matplotlib** と **seaborn** が使用されています。 先ほど実行した SQL クエリと同じ種類の折れ線グラフがレンダリングされます。

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```



## <a name="load-data-from-a-spark-table-into-a-dedicated-sql-pool-table"></a>Spark テーブルから専用 SQL プール テーブルにデータを読み込む

先ほど、専用 SQL プール テーブル **SQLPOOL1.dbo.Trip** のデータを Spark テーブル **nyctaxi.trip** にコピーしました。 次に、Spark を使用して、データを Spark テーブル **nyctaxi.passengercountstats** に集約しました。 今度は、**nyctaxi.passengercountstats** のデータを **SQLPOOL1.dbo.PassengerCountStats** という専用 SQL プール テーブルにコピーします。

ノートブックで次のセルを実行します。 集約された Spark テーブルが専用 SQL プール テーブルにコピーされます。

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [サーバーレス SQL プールを使用してデータを分析する](get-started-analyze-sql-on-demand.md)


