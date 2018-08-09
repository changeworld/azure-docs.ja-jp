---
title: 'チュートリアル: Azure HDInsight での Apache Spark クラスターへのデータの読み込みとクエリの実行 '
description: Azure HDInsight で Spark クラスターにデータを読み込み、対話型のクエリを実行する方法を説明します。
services: azure-hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.author: jasonh
ms.date: 05/17/2018
ms.openlocfilehash: e83c7a4ed5987108a74153897abe4839dd24418c
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619432"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>チュートリアル: Azure HDInsight での Apache Spark クラスターへのデータの読み込みとクエリの実行

このチュートリアルでは、csv ファイルからデータフレームを作成する方法と、Azure HDInsight で Apache Spark クラスターに対して対話型の Spark SQL クエリを実行する方法を説明します。 Spark で、データフレームは、名前付きの列に編成されたデータの分散型コレクションです。 データフレームは概念的には、リレーショナル データベースのテーブルまたは R/Python のデータ フレームと同等です。
 
このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * csv ファイルからデータフレームを作成する
> * データフレームでクエリを実行する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

* 「[Azure HDInsight での Apache Spark クラスターの作成](apache-spark-jupyter-spark-sql.md)」を完了します。

## <a name="create-a-dataframe-from-a-csv-file"></a>csv ファイルからデータフレームを作成する

アプリケーションは、SQLContext オブジェクトを使用して、既存の Resilient Distributed Dataset (RDD)、Hive テーブル、またはデータ ソースからデータフレームを作成できます。 次のスクリーンショットは、このチュートリアルで使用されている HVAC.csv ファイルのスナップショットを示しています。 csv ファイルには、すべての HDInsight Spark クラスターが付属します。 このデータは、いくつかのビルの温度の変化をキャプチャしています。
    
![対話型 Spark SQL クエリのデータのスナップショット](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "対話型 Spark SQL クエリのデータのスナップショット")


1. 前提条件で作成した Jupyter Notebook を開きます。
2. 次のコードを Notebook の空のセルに貼り付け、**Shift + Enter** キーを押してコードを実行します。 このコードにより、このシナリオに必要な種類がインポートされます。

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Jupyter で対話型のクエリを実行すると、Web ブラウザー ウィンドウまたはタブのキャプションに **[(ビジー)]** 状態と Notebook のタイトルが表示されます。 また、右上隅にある **PySpark** というテキストの横に塗りつぶされた円も表示されます。 ジョブが完了すると、白抜きの円に変化します。

    ![対話型の Spark SQL クエリの状態](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "対話型の Spark SQL クエリの状態")

3. 次のコードを実行してデータフレームと一時テーブル (**hvac**) を作成します。 

    ```PySpark
    # Create an RDD from sample data
    csvFile = spark.read.csv('wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

    > [!NOTE]
    > PySpark カーネルを使用して Notebook を作成すると、最初のコード セルを実行するときに SQL コンテキストが自動的に作成されます。 コンテキストを明示的に作成する必要はありません。


## <a name="run-queries-on-the-dataframe"></a>データフレームでクエリを実行する

テーブルを作成したら、データに対して対話型のクエリを実行できます。

1. Notebook の空のセルで次のコードを実行します。

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Notebook では PySpark カーネルが使用されているため、一時テーブル **hvac** に対して対話型の SQL クエリを直接実行できます。

   次の表形式の出力が表示されます。

     ![対話型の Spark クエリの表形式の出力](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "対話型の Spark クエリの表形式の出力")

3. 他の視覚化でも結果を表示できます。 同じ出力に対する領域グラフを表示するには、**[領域]** を選択し、他の値を次のように設定します。

    ![対話型の Spark クエリ結果の領域グラフ](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "対話型の Spark クエリ結果の領域グラフ")

10. Notebook の **[ファイル]** メニューで、**[Save and Checkpoint] (保存とチェックポイント)** をクリックします。 

11. [次のチュートリアル](apache-spark-use-bi-tools.md)をすぐに開始する場合は、Notebook を開いたままにしておきます。 開始しない場合は、Notebook の **[ファイル]** メニューで **[Close and Halt] (閉じて停止)** を選択し、Notebook をシャットダウンしてクラスター リソースを解放します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

HDInsight を使用すると、データは Azure Storage または Azure Data Lake Store に格納されるため、クラスターは、使用されていない場合に安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。 すぐに次のチュートリアルに取り掛かる場合は、クラスターを保持することができます。

Azure Portal で、クラスターを開き、**[削除]** を選択します。

![HDInsight クラスターの削除](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "HDInsight クラスターの削除")

リソース グループ名を選択し、リソース グループ ページを開いて、**[リソース グループの削除]** を選択することもできます。 リソース グループを削除すると、HDInsight Spark クラスターと既定のストレージ アカウントの両方が削除されます。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

* Spark データフレームを作成します。
* データフレームに対して Spark SQL を実行します。

次の記事に進んで、Spark に登録したデータを Power BI などの BI 分析ツールに取り込む方法を確認してください。 
> [!div class="nextstepaction"]
> [BI ツールを使用したデータの分析](apache-spark-use-bi-tools.md)

