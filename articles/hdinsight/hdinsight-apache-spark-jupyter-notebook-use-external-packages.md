---
title: HDInsight 上の Apache Spark クラスターの Jupyter Notebook で外部のパッケージを使用する | Microsoft Docs
description: HDInsight Spark クラスター内の Jupyter Notebook で外部の Spark パッケージを使用するための構成手順を説明します。
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: nitinme

---
# HDInsight Linux の Apache Spark クラスターの Jupyter Notebook で外部のパッケージを使用する
HDInsight (Linux) 上の Apache Spark クラスターに標準では搭載されていない外部のコミュニティから提供されているパッケージを使用するようにクラスター内の Jupyter Notebook を構成する方法について説明します。

利用できるすべてのパッケージは、[Maven リポジトリ](http://search.maven.org/)で検索できます。公開されているパッケージの一覧を他のソースから入手してもかまいません。たとえば、コミュニティから提供されている全パッケージの一覧を [Spark Packages](http://spark-packages.org/) で入手できます。

この記事では、Jupyter Notebook で [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) パッケージを使用する方法について説明します。

## 前提条件
次のものが必要です。

* Azure サブスクリプション。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* HDInsight Linux での Apache Spark クラスター。手順については、[Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関するページを参照してください。

## Jupyter Notebook で外部のパッケージを使用する
1. [Azure ポータル](https://portal.azure.com/)のスタート画面で Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。**[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。   
2. Spark クラスター ブレードで、**[クイック リンク]** をクリックし、**[クラスター ダッシュボード]** ブレードで **[Jupyter Notebook]** をクリックします。入力を求められたら、クラスターの管理者資格情報を入力します。
   
   > [!NOTE]
   > ブラウザーで次の URL を開き、クラスターの Jupyter Notebook にアクセスすることもできます。**CLUSTERNAME** をクラスターの名前に置き換えます。
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 
3. 新しい Notebook を作成します。**[新規]** をクリックし、**[Spark]** をクリックします。
   
    ![新しい Jupyter Notebook を作成します](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.createnotebook.png "新しい Jupyter Notebook を作成します")
4. Untitled.pynb という名前の新しい Notebook が作成されて開かれます。上部の Notebook 名をクリックし、わかりやすい名前を入力します。
   
    ![Notebook の名前を指定します](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.notebook.name.png "Notebook の名前を指定します")
5. 外部のパッケージを使用するようにノートブックを構成するには `%%configure` マジックを使用します。外部のパッケージを使用するノートブックでは必ず、最初のコード セルで `%%configure` マジックを呼び出すようにしてください。そうすることでセッションが開始される前に、指定のパッケージを使用するようにカーネルが構成されます。
   
        %%configure
        { "packages":["com.databricks:spark-csv_2.10:1.4.0"] }

    >[AZURE.IMPORTANT] 最初のセルでカーネルを構成しなかった場合、`-f` パラメーターを指定して `%%configure` を使用できますが、その場合セッションが最初からやり直しとなり、すべての進捗が失われます。

1. 上のスニペットの `packages` には、Maven Central Repository における maven コーディネートのリストを指定します。このスニペットの `com.databricks:spark-csv_2.10:1.4.0` は、**spark-csv** パッケージの maven コーディネートです。パッケージのコーディネートは、以下の方法で構築します。
   
    a.Maven リポジトリから目的のパッケージを探します。このチュートリアルでは [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) を使用します。
   
    b.リポジトリで **GroupId**、**ArtifactId**、**Version** の値を確認します。
   
    ![Use external packages with Jupyter notebook](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Use external packages with Jupyter notebook")
   
    c.3 つの値をコロン (**:**) で区切って連結します。
   
        com.databricks:spark-csv_2.10:1.4.0
2. `%%configure` マジックのコード セルを実行します。これで、指定したパッケージを使用するように基になる Livy セッションが構成されます。これでノートブック内の後続セルで、指定したパッケージを使用できるようになります (以下の例を参照)。
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
3. 前の手順で作成したデータフレームからのデータは、以下のスニペットで表示できます。
   
        df.show()
   
        df.select("Time").count()

## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### ツールと拡張機能
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark Scala アプリケーションを作成し、送信する](hdinsight-apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](hdinsight-apache-spark-job-debugging.md)

<!---HONumber=AcomDC_0914_2016-->