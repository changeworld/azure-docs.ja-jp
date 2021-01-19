---
title: 'クイックスタート: Spark を使用して分析を開始する'
description: このチュートリアルでは、Apache Spark を使用してデータを分析する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 3b5f5d64498922e9fc35942ff4570d801aa6c516
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118882"
---
# <a name="analyze-with-apache-spark"></a>Apache Spark を使用して分析を行う

このチュートリアルでは、Apache Spark for Azure Synapse でデータを読み込んで分析する基本的な手順について説明します。

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>Spark を使用して Blob Storage の NYC タクシー データを分析する

1. **[データ]** ハブで、 **[新しいリソースの追加]** ( **[リンク済み]** の上にある [+] ボタン)、 **[ギャラリーの参照]** の順に選択します。
1. **[NYC Taxi & Limousine Commission - yellow taxi trip records]\(NYC タクシー & リムジン協会 - イエロー タクシー運行記録\)** を選択します。
1. ページの下部で、 **[続行]**  >  **[データセットの追加]** を選択します。
1. **[Linked]\(リンク済み\)** の **[データ]** ハブで **[Azure Blob Storage]** を右クリックし、 **[Sample Datasets]\(サンプル データセット\)**  >  **[nyc_tlc_yellow]** を選択します。
1. **[New notebook]\(新しいノートブック\)** を選択し、次のコードを使用して新しいノートブックを作成します。

    ```py
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```

1. ノートブックの **[Attach to]\(接続先\)** メニューで、サーバーレス Spark プールを選択します。
1. セルで **[実行]** を選択します。
1. データフレームのスキーマを表示するだけの場合は、次のコードを使用してセルを実行します。

    ```py
    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>NYC タクシーのデータを Spark nyctaxi データベースに読み込む

**SQLPOOL1** のテーブルにデータが用意されています。 それを **nyctaxi** という名前の Spark データベースに読み込みます。

1. Synapse Studio で、 **[開発]** ハブに移動します。
1. **[+]**  >  **[ノートブック]** を選択します。
1. ノートブックの上部にある **[アタッチ先]** の値を **[Spark1]** に設定します。
1. **[コードの追加]** を選択してノートブックのコード セルを追加し、次のテキストを入力します。

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. セルで **[実行]** を選択します。
1. **[データ]** ハブで **[データベース]** を右クリックして、 **[更新]** を選択します。 次のデータベースが表示されます。

    - **SQLPOOL1 (SQL)**
    - **nyctaxi (Spark)**

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Spark とノートブックを使用して NYC タクシーのデータを分析する

1. ノートブックに戻ります。
1. 新しいコード セルを作成し、次のテキストを入力します。

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. このセルを実行して、**nyctaxi** Spark データベースに読み込んだ NYC Taxi データを表示します。
1. 次のコードを実行して、先ほど専用 SQL プール **SQLPOOL1** で行ったのと同じ分析を実行します。 このコードにより、**nyctaxi.passengercountstats** という名前のテーブルに分析の結果が保存されて表示されます。

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

## <a name="load-data-from-a-spark-table-into-a-dedicated-sql-pool-table"></a>Spark テーブルから専用 SQL プール テーブルにデータを読み込む

先ほど、専用 SQL プール テーブル **SQLPOOL1.dbo.Trip** のデータを Spark テーブル **nyctaxi.trip** にコピーしました。 次に、データを Spark テーブル **nyctaxi.passengercountstats** に集約しました。 今度は、**nyctaxi.passengercountstats** のデータを **SQLPOOL1.dbo.PassengerCountStats** という専用 SQL プール テーブルにコピーします。

ノートブックで次のセルを実行します。 集約された Spark テーブルが専用 SQL プール テーブルにコピーされます。

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [サーバーレス SQL プールを使用してデータを分析する](get-started-analyze-sql-on-demand.md)
