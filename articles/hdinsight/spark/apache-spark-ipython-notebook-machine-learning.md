---
title: Azure HDInsight で Apache Spark Machine Learning アプリケーションを作成する | Microsoft Docs
description: Apache Spark Machine Learning アプリケーションを HDInsight Spark クラスター上で Jupyter Notebook を使用して作成するための詳細な手順
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f584ca5e-abee-4b7c-ae58-2e45dfc56bf4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: jgao
ms.openlocfilehash: 95daab2bd7bc57d01bc9e3c05404958edd71eecc
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2018
---
# <a name="build-apache-spark-machine-learning-applications-on-azure-hdinsight"></a>Azure HDInsight で Apache Spark Machine Learning アプリケーションを作成する

HDInsight で Spark クラスターを使用して Apache Spark Machine Learning アプリケーションを作成する方法を説明します。 この記事では、クラスターで使用できる Jupyter Notebook を使用して、このアプリケーションを作成およびテストする方法を説明します。 このアプリケーションでは、すべてのクラスターにおいて既定で利用可能なサンプル HVAC.csv データを使用します。

[MLlib](https://spark.apache.org/docs/1.1.0/mllib-guide.html) は、分類、回帰、クラスタリング、協調フィルタリング、次元縮小、基になっている最適化プリミティブなど、一般的な学習アルゴリズムとユーティリティで構成された Spark のスケーラブル Machine Learning ライブラリです。

## <a name="prerequisites"></a>前提条件:

次の項目が必要です。

* HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。 

## <a name="data"></a>データ セットを理解する

次のデータは、HVAC (Heating, Ventilating, Air Conditioning: 冷暖房空調設備) システムがインストールされているいくつかのビルの目標温度と実際の温度を示します。 **[System]** 列はシステム ID を表し、**[SystemAge]** 列は HVAC システムがビルに設置されてからの年数を表します。 このチュートリアルでは、このデータを使用し、システム ID とシステム使用年数から得られる目標温度を基にしてビルが暑すぎるか寒すぎるかを予測します。

![Spark Machine Learning サンプルで使用されるデータのスナップショット](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Spark Machine Learning サンプルで使用されるデータのスナップショット")

データ ファイル (**HVAC.csv**) は、すべての HDInsight クラスター上の **\HdiSamples\HdiSamples\SensorSampleData\hvac** にあります。

## <a name="app"></a>Spark MLlib を使用した Spark Machine Learning アプリケーションの作成
このアプリケーションでは、Spark [ML パイプライン](https://spark.apache.org/docs/2.2.0/ml-pipeline.html)を使用して、ドキュメントの分類を実行します。 ML パイプラインでは、実用的な機械学習パイプラインの作成および調整に役立つデータ フレームを基盤とする、統一された高レベルの API 一式が提供されます。 パイプラインでは、ドキュメントを単語に分割し、単語を数値特徴ベクトルに変換して、最後に特徴ベクトルとラベルを使用して予測モデルを作成します。 アプリケーションを作成するには、次の手順を実行します。

1. PySpark カーネルを使用して Jupyter Notebook を作成します。 手順については、[Jupyter Notebook の作成](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook)に関するページをご覧ください。
2. このシナリオに必要な型をインポートします。 次のスニペットを空のセルに貼り付けて、 **Shift + Enter**キーを押します。 

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row

    import os
    import sys
    from pyspark.sql.types import *

    from pyspark.mllib.classification import LogisticRegressionWithSGD
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array
    ```
3. データ (hvac.csv) を読み込み、解析し、それを使用してモデルをトレーニングします。 

    ```PySpark
    # Define a type called LabelDocument
    LabeledDocument = Row("BuildingID", "SystemInfo", "label")

    # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
    def parseDocument(line):
        values = [str(x) for x in line.split(',')]
        if (values[3] > values[2]):
            hot = 1.0
        else:
            hot = 0.0        

        textValue = str(values[4]) + " " + str(values[5])

        return LabeledDocument((values[6]), textValue, hot)

    # Load the raw HVAC.csv file, parse it using the function
    data = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
    training = documents.toDF()
    ```

    このコード スニペットでは、実際の温度と目標温度とを比較する関数を定義します。 実際の温度の方が高い場合、ビルは暑く、値 **1.0**で示されます。 それ以外の場合、ビルは寒く、値 **0.0** で示されます。 

4. トークナイザー、hashingTF、lr という 3 つのステージで構成される Spark 機械学習パイプラインを構成します。 

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    パイプラインの概要と機能について詳しくは、<a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Spark 機械学習に関するページ</a>をご覧ください。

5. パイプラインをトレーニング ドキュメントに適合させます。
   
    ```PySpark
    model = pipeline.fit(training)
    ```

6. トレーニング ドキュメントを検証してアプリケーションでの進行状況をチェックポイントします。
   
    ```PySpark
    training.show()
    ```
   
    出力は次のようになります。

    ```
    +----------+----------+-----+
    |BuildingID|SystemInfo|label|
    +----------+----------+-----+
    |         4|     13 20|  0.0|
    |        17|      3 20|  0.0|
    |        18|     17 20|  1.0|
    |        15|      2 23|  0.0|
    |         3|      16 9|  1.0|
    |         4|     13 28|  0.0|
    |         2|     12 24|  0.0|
    |        16|     20 26|  1.0|
    |         9|      16 9|  1.0|
    |        12|       6 5|  0.0|
    |        15|     10 17|  1.0|
    |         7|      2 11|  0.0|
    |        15|      14 2|  1.0|
    |         6|       3 2|  0.0|
    |        20|     19 22|  0.0|
    |         8|     19 11|  0.0|
    |         6|      15 7|  0.0|
    |        13|      12 5|  0.0|
    |         4|      8 22|  0.0|
    |         7|      17 5|  0.0|
    +----------+----------+-----+
    ```

    生の CSV ファイルと照らして出力を比較します。 たとえば、CSV ファイルの最初の行のデータは次のとおりです。

    ![Spark Machine Learning サンプルの出力データ スナップショット](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Spark Machine Learning サンプルの出力データ スナップショット")

    実際の温度は目標温度より低く、ビルが寒いことを示します。 そのため、トレーニングの出力では、最初の行の **label** の値は **0.0** であり、ビルが暑くないことを意味します。

7. トレーニング済みのモデルを実行するようにデータ セットを準備します。 そのためには、システム ID とシステム経過年数 (トレーニング出力では **SystemInfo** として示されます) を渡し、モデルはそのシステム ID とシステム経過年数のビルが暑すぎるか (1.0 で示されます) または寒すぎるか (0.0 で示されます) を予測します。
   
    ```PySpark   
    # SystemInfo here is a combination of system ID followed by system age
    Document = Row("id", "SystemInfo")
    test = sc.parallelize([(1L, "20 25"),
                    (2L, "4 15"),
                    (3L, "16 9"),
                    (4L, "9 22"),
                    (5L, "17 10"),
                    (6L, "7 22")]) \
        .map(lambda x: Document(*x)).toDF() 
    ```
8. 最後に、テスト データで予測を行います。 
   
    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    次のような出力が表示されます。

    ```   
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```
   
   予測の 1 行目からは、ID が 20 でシステム経過年数が 25 年の HVAC システムではビルが暑い (**prediction=1.0**) ことがわかります。 DenseVector の 1 番目の値 (0.49999) は予測 0.0 に対応し、2 番目の値 (0.5001) は予測 1.0 に対応します。 出力では、2 番目の値はわずかに高いだけですが、モデルは **prediction=1.0**を示します。
10. Notebook をシャットダウンしてリソースを解放します。 そのためには、Notebook の **[ファイル]** メニューの **[Close and Halt]** (閉じて停止) をクリックします。 これにより、Notebook がシャットダウンされ、閉じられます。

## <a name="anaconda"></a>Spark Machine Learning での Anaconda scikit-learn ライブラリの使用
HDInsight の Apache Spark クラスターには、Anaconda ライブラリが含まれます。 これには、機械学習用の **scikit-learn** ライブラリも含まれます。 ライブラリには、Jupyter Notebook からサンプル アプリケーションを直接作成するために使用できるさまざまなデータ セットも含まれます。 scikit-learn ライブラリの使用例については、「[ http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html)」を参照してください。

## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](apache-spark-use-bi-tools.md)
* [Spark with Machine Learning: Use Spark in HDInsight to predict food inspection results (Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する)](apache-spark-machine-learning-mllib-ipython.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark Scala アプリケーションを作成し、送信する](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter Notebook で外部のパッケージを使用する](apache-spark-jupyter-notebook-use-external-packages.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-weblogs-sample]:../hadoop/apache-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]:../hadoop/apache-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
