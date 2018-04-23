---
title: Azure HDInsight Spark クラスターに対して対話型クエリを実行する | Microsoft Docs
description: HDInsight で Apache Spark クラスターを作成する方法に関する HDInsight Spark のクイック スタート。
keywords: spark クイック スタート,対話型 spark,対話型クエリ,hdinsight spark,azure spark
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: jgao
ms.openlocfilehash: 177fb47c72e9abbafcda69416643fbd3848373bd
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2018
---
# <a name="run-interactive-queries-on-spark-clusters-in-hdinsight"></a>HDInsight で Spark クラスターに対して対話型クエリを実行する

Jupyter Notebook を使用して、Spark クラスターに対して対話型の Spark SQL クエリを実行する方法を説明します。 

[Jupyter Notebook](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html) は、コンソール ベースの対話型機能を Web に拡張するブラウザー ベースのアプリケーションです。 HDInsight の Spark には [Zeppelin Notebook](apache-spark-zeppelin-notebook.md) も含まれています。 このチュートリアルでは Jupyter Notebook を使用します。

Jupyter Notebook では、HDInsight クラスター上で **PySpark**、**PySpark3**、**Spark** の 3 つのカーネルをサポートしています。 このチュートリアルでは、**PySpark** カーネルを使用します。 これらのカーネルと **PySpark** を使用するメリットについては、[HDInsight での Apache Spark クラスターと Jupyter Notebook カーネルの使用](apache-spark-jupyter-notebook-kernels.md)に関する記事を参照してください。 Zeppelin Notebook を使用するには、「[Azure HDInsight の Apache Spark クラスターで Zeppelin Notebook を使用する](./apache-spark-zeppelin-notebook.md)」をご覧ください。

このチュートリアルでは、csv ファイルでデータのクエリを実行します。 最初に Spark にデータフレームとしてそのデータを読み込む必要があります。 Jupyter Notebook を使用してデータフレームでクエリを実行することができます。 

## <a name="prerequisites"></a>前提条件

* **Azure HDInsight Spark クラスター**。 手順については、[Azure HDInsight での Apache Spark クラスターの作成](apache-spark-jupyter-spark-sql.md)に関するページをご覧ください。
* **PySpark を使用した Jupyter Notebook**。 手順については、[Jupyter Notebook の作成](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook)に関するページをご覧ください。

## <a name="create-a-dataframe-from-a-csv-file"></a>csv ファイルからデータフレームを作成する

SQLContext で、アプリケーションは既存の RDD、Hive テーブル、またはデータ ソースからデータフレームを作成できます。 

**csv ファイルからデータフレームを作成するには**

1. Jupyter Notebook がまだない場合は、PySpark を使用して作成します。 手順については、[Jupyter Notebook の作成](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook)に関するページをご覧ください。

2. 次のコードを Notebook の空のセルに貼り付け、**Shift + Enter** キーを押してコードを実行します。 このコードにより、このシナリオに必要な種類がインポートされます。

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```
    PySpark カーネルを使用して Notebook を作成すると、最初のコード セルを実行するときに Spark コンテキストと Hive コンテキストが自動的に作成されます。 コンテキストを明示的に作成する必要はありません。

    Jupyter で対話型のクエリを実行すると、Web ブラウザー ウィンドウまたはタブのキャプションに **[(ビジー)]** 状態と Notebook のタイトルが表示されます。 また、右上隅にある **PySpark** というテキストの横に塗りつぶされた円も表示されます。 ジョブが完了すると、白抜きの円に変化します。

    ![対話型の Spark SQL クエリの状態](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "対話型の Spark SQL クエリの状態")

3. 次のコードを実行して、データフレームと一時テーブル (**hvac**) を作成します。コードでは、CSV ファイル内の使用可能なすべての列が抽出されるわけではありません。 

    ```PySpark
    # Create an RDD from sample data
    hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
    # Create a schema for our data
    Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')
    
    # Parse the data and create a schema
    hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
    hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
    
    # Infer the schema and create a table       
    hvacTable = sqlContext.createDataFrame(hvac)
    hvacTable.registerTempTable('hvactemptable')
    dfw = DataFrameWriter(hvacTable)
    dfw.saveAsTable('hvac')
    ```
    次のスクリーンショットは、HVAC.csv ファイルのスナップショットを示しています。 csv ファイルには、すべての HDInsigt Spark クラスターが付属します。 このデータは、ビルの温度の変化をキャプチャしています。

    ![対話型 Spark SQL クエリのデータのスナップショット](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "対話型 Spark SQL クエリのデータのスナップショット")

## <a name="run-queries-on-the-dataframe"></a>データフレームでクエリを実行する

テーブルを作成したら、データに対して対話型のクエリを実行できます。

**クエリを実行するには**

1. Notebook の空のセルで次のコードを実行します。

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Notebook では PySpark カーネルが使用されているため、`%%sql` マジックを使用して、作成した一時テーブル **hvac** に対して対話型の SQL クエリを直接実行できます。 `%%sql` マジックの詳細と、PySpark カーネルで使用できるその他のマジックの詳細については、[Spark HDInsight クラスターと Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)に関する記事を参照してください。

   次の表形式の出力が既定で表示されます。

     ![対話型の Spark クエリの表形式の出力](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "対話型の Spark クエリの表形式の出力")

3. 他の視覚化でも結果を表示できます。 同じ出力に対する領域グラフを表示するには、**[領域]** を選択し、他の値を次のように設定します。

    ![対話型の Spark クエリ結果の領域グラフ](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "対話型の Spark クエリ結果の領域グラフ")

10. Notebook の **[ファイル]** メニューで、**[Save and Checkpoint]\(保存とチェックポイント\)** をクリックします。 

11. [次のチュートリアル](apache-spark-use-bi-tools.md)をすぐに開始する場合は、Notebook を開いたままにしておきます。 開始しない場合は、Notebook の **[ファイル]** メニューで **[Close and Halt]\(閉じて停止\)** をクリックし、Notebook をシャットダウンしてクラスター リソースを解放します。

## <a name="next-step"></a>次のステップ

この記事では、Jupyter Notebook を使用して Spark で対話型のクエリを実行する方法について学習しました。 次の記事に進んで、Spark に登録したデータを Power BI や Tableau などの BI 分析ツールに取り込む方法を確認してください。 

> [!div class="nextstepaction"]
>[Azure HDInsight でデータ視覚化ツールを使用する Spark BI](apache-spark-use-bi-tools.md)




