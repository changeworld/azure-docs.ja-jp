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
ms.date: 03/24/2021
ms.openlocfilehash: de48f906f4dc86bf6297cfb3b76f406df49feec3
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363854"
---
# <a name="analyze-with-apache-spark"></a>Apache Spark を使用して分析を行う

このチュートリアルでは、Apache Spark for Azure Synapse でデータを読み込んで分析する基本的な手順について説明します。

## <a name="create-a-serverless-apache-spark-pool"></a>サーバーレス Apache Spark プールを作成する

1. Synapse Studio の左側のペインで、 **[管理]**  >  **[Apache Spark プール]** を選択します。
1. **[新規]** を選択します。 
1. **[Apache Spark プール名]** に「**Spark1**」と入力します。
1. **[ノード サイズ]** に「**Small**」と入力します。
1. **[ノード数]** で、最小数を 3 に、最大数を 3 に設定します。
1. **[確認と作成]**  >  **[作成]** の順に選択します。 Apache Spark プールの準備は数秒で完了します。

## <a name="understanding-serverless-apache-spark-pools"></a>サーバーレス Apache Spark プールについて

サーバーレス Spark プールは、ユーザーが Spark の操作方法を示すための手段です。 プールの使用を開始すると、必要に応じて Spark セッションが作成されます。 プールでは、そのセッションで使用される Spark リソースの数と、セッションが自動的に一時停止するまでの継続時間を制御します。 課金は、プール自体ではなく、そのセッション中に使用された Spark リソースに対して発生します。 このように Spark プールを使用すると、クラスターの管理を気にすることなく Spark を操作できます。 これは、サーバーレス SQL プールの動作に似ています。

## <a name="analyze-nyc-taxi-data-with-a-spark-pool"></a>Spark プールで NYC タクシーのデータを分析する

1. Synapse Studio で、 **[開発]** ハブに移動します。
2. 新しい Notebook を作成する
3. 新しいコード セルを作成し、次のコードをそのセルに貼り付けます。
    ```py
    %%pyspark
    df = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTripSmall.parquet', format='parquet')
    display(df.limit(10))
    ```
1. ノートブックの **[アタッチ先]** メニューで、前に作成した **Spark1** サーバーレス Spark プールを選択します。
1. セルで **[実行]** を選択します。 このセルを実行するために必要であれば、Synapse によって新しい Spark セッションが開始されます。 新しい Spark セッションが必要な場合、最初は作成に約 2 秒かかります。 
1. データフレームのスキーマを表示するだけの場合は、次のコードを使用してセルを実行します。

    ```py
    %%pyspark
    df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>NYC タクシーのデータを Spark nyctaxi データベースに読み込む

データは、**df** という名前のデータフレームを使って取得できます。 それを **nyctaxi** という名前の Spark データベースに読み込みます。

1. ノートブックに新しいコード セルを追加し、次のコードを入力します。

    ```py
    %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Spark とノートブックを使用して NYC タクシーのデータを分析する

1. 新しいコード セルを作成し、次のコードを入力します。 

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. このセルを実行して、**nyctaxi** Spark データベースに読み込んだ NYC Taxi データを表示します。
1. 新しいコード セルを作成し、次のコードを入力します。 このデータを分析し、**nyctaxi.passengercountstats** というテーブルに結果を保存します。

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


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [専用の SQL プールを使用してデータを分析する](get-started-analyze-sql-pool.md)
