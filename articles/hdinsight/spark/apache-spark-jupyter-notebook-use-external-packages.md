---
title: Azure HDInsight 上の Spark にある Jupyter でのカスタム Maven パッケージの使用
description: HDInsight Spark クラスター内の Jupyter Notebook でカスタム Maven パッケージを使用するための構成手順を説明します。
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: jasonh
ms.openlocfilehash: 51099f64546acc6f18269b2e7ec05106bb3baa2d
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622033"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>HDInsight の Apache Spark クラスターの Jupyter Notebook で外部のパッケージを使用する
> [!div class="op_single_selector"]
> * [cell magic の使用](apache-spark-jupyter-notebook-use-external-packages.md)
> * [スクリプト アクションの使用](apache-spark-python-package-installation.md)
>
>

HDInsight 上の Apache Spark クラスターにある Jupyter Notebook を、そのクラスターの標準では搭載されていない外部のコミュニティから提供されている **Maven** パッケージを使用するように構成する方法について説明します。 

利用できるすべてのパッケージは、 [Maven リポジトリ](http://search.maven.org/) で検索できます。 公開されているパッケージの一覧を他のソースから入手してもかまいません。 たとえば、コミュニティから提供されている全パッケージの一覧を [Spark Packages](http://spark-packages.org/)で入手できます。

この記事では、Jupyter Notebook で [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) パッケージを使用する方法について説明します。

## <a name="prerequisites"></a>前提条件
次のものが必要です。

* HDInsight での Apache Spark クラスター。 手順については、「 [Create Apache Spark clusters in Azure HDInsight (Azure HDInsight での Apache Spark クラスターの作成)](apache-spark-jupyter-spark-sql.md)」を参照してください。

## <a name="use-external-packages-with-jupyter-notebooks"></a>Jupyter Notebook で外部のパッケージを使用する
1. [Azure Portal](https://portal.azure.com/) のスタート画面で Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。 **[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。   

1. Spark クラスター ブレードで、**[クイック リンク]** をクリックし、**[クラスター ダッシュボード]** ブレードで **[Jupyter Notebook]** をクリックします。 入力を求められたら、クラスターの管理者資格情報を入力します。

    > [!NOTE]
    > ブラウザーで次の URL を開き、クラスターの Jupyter Notebook にアクセスすることもできます。 **CLUSTERNAME** をクラスターの名前に置き換えます。
    > 
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
    > 

1. 新しい Notebook を作成します。 **[新規]** をクリックし、**[Spark]** をクリックします。
   
    ![新しい Jupyter Notebook の作成](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "新しい Jupyter Notebook の作成")

1. Untitled.pynb という名前の新しい Notebook が作成されて開かれます。 上部の Notebook 名をクリックし、わかりやすい名前を入力します。
   
    ![Notebook の名前を指定](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Notebook の名前を指定")

1. 外部のパッケージを使用するようにノートブックを構成するには `%%configure` マジックを使用します。 外部のパッケージを使用するノートブックでは必ず、最初のコード セルで `%%configure` マジックを呼び出すようにしてください。 そうすることでセッションが開始される前に、指定のパッケージを使用するようにカーネルが構成されます。

    >[!IMPORTANT] 
    >最初のセルでカーネルを構成しなかった場合、`-f` パラメーターを指定して `%%configure` を使用できますが、その場合セッションが最初からやり直しとなり、すべての進捗が失われます。

    | HDInsight のバージョン | コマンド |
    |-------------------|---------|
    |HDInsight 3.3 および HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|
    | HDInsight 3.5 および HDInsight 3.6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.10:1.4.0" }}`|

1. 上のスニペットには、Maven Central Repository における外部パッケージの maven コーディネートを指定します。 このスニペットの `com.databricks:spark-csv_2.10:1.4.0` は、 **spark-csv** パッケージの maven コーディネートです。 パッケージのコーディネートは、以下の方法で構築します。
   
    a. Maven リポジトリから目的のパッケージを探します。 このチュートリアルでは [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) を使用します。
   
    b. リポジトリで **GroupId**、**ArtifactId**、**Version** の値を確認します。 収集した値が、クラスターに一致することを確認します。 この例では、Scala 2.10 と Spark 1.4.0 パッケージを使用していますが、クラスター内の適切な Scala または Spark のバージョンに対して別のバージョンを選択しなければならないことがあります。 クラスター上の Scala のバージョンを確認するには、Spark Jupyter カーネルまたは Spark 送信に対して `scala.util.Properties.versionString` を実行します。 クラスター上の Spark のバージョンを確認するには、Jupyter Notebook に対して `sc.version` を実行します。
   
    ![Jupyter Notebook で外部パッケージを使用する](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Jupyter Notebook で外部パッケージを使用する")
   
    c. 3 つの値をコロン (**:**) で区切って連結します。
   
        com.databricks:spark-csv_2.10:1.4.0

1. `%%configure` マジックのコード セルを実行します。 これで、指定したパッケージを使用するように基になる Livy セッションが構成されます。 これでノートブック内の後続セルで、指定したパッケージを使用できるようになります (以下の例を参照)。
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    HDInsight 3.6 の場合、次のスニペットをご利用ください。

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. 前の手順で作成したデータフレームからのデータは、以下のスニペットで表示できます。
   
        df.show()
   
        df.select("Time").count()

## <a name="seealso"></a>関連項目
* [概要: Azure HDInsight での Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>シナリオ
* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](apache-spark-use-bi-tools.md)
* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](apache-spark-ipython-notebook-machine-learning.md)
* [Spark with Machine Learning: Use Spark in HDInsight to predict food inspection results (Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する)](apache-spark-machine-learning-mllib-ipython.md)
* [Website log analysis using Spark in HDInsight (HDInsight での Spark を使用した Web サイト ログ分析)](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>アプリケーションの作成と実行
* [Scala を使用してスタンドアロン アプリケーションを作成する](apache-spark-create-standalone-application.md)
* [Livy を使用して Spark クラスターでジョブをリモートで実行する](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>ツールと拡張機能

* [HDInsight Linux の Apache Spark クラスターの Jupyter Notebook で外部の Python パッケージを使用する](apache-spark-python-package-installation.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark Scala アプリケーションを作成し、送信する](apache-spark-intellij-tool-plugin.md)
* [IntelliJ IDEA 用の HDInsight Tools プラグインを使用して Spark アプリケーションをリモートでデバッグする](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](apache-spark-zeppelin-notebook.md)
* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](apache-spark-jupyter-notebook-kernels.md)
* [Jupyter をコンピューターにインストールして HDInsight Spark クラスターに接続する](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>リソースの管理
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](apache-spark-resource-manager.md)
* [HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ](apache-spark-job-debugging.md)
