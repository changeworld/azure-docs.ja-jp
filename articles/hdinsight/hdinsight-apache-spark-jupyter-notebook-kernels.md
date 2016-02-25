<properties 
	pageTitle="Linux 上の HDInsight Spark クラスターの Jupyter Notebook で使用可能なカーネル | Microsoft Azure" 
	description="HDInsight Linux 上の Spark クラスターで使用できる追加の Jupyter Notebook カーネルについて説明します。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2016" 
	ms.author="nitinme"/>


# HDInsight (Linux) の Spark クラスターと Jupyter Notebook で使用可能なカーネル

HDInsight (Linux) の Apache Spark クラスターには、アプリケーションのテストに使用できる Jupyter Notebook が含まれています。既定では、Jupyter Notebook には **Python2** カーネルが付属しています。HDInsight の Spark クラスターには、Jupyter Notebook で使用できる 2 つの追加のカーネルが用意されています。次のとおりです。

1. **Spark** (Scala で記述されたアプリケーション用)
2. **PySpark** (Python で記述されたアプリケーション用)

この記事では、これらのカーネルの使い方と、これらを使用する利点について説明します。

**前提条件:**

次のものが必要です。

- Azure サブスクリプション。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
- HDInsight Linux での Apache Spark クラスター。手順については、[Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関するページを参照してください。

## カーネルの使い方 

1. [Azure プレビュー ポータル](https://portal.azure.com/)のスタート画面で Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。**[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。   

2. Spark クラスター ブレードで、**[クイック リンク]** をクリックし、**[クラスター ダッシュボード]** ブレードで **[Jupyter Notebook]** をクリックします。入力を求められたら、クラスターの管理者資格情報を入力します。

	> [AZURE.NOTE] ブラウザーで次の URL を開き、クラスターの Jupyter Notebook にアクセスすることもできます。__CLUSTERNAME__ をクラスターの名前に置き換えます。
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 新しいカーネルで、新しい Notebook を作成します。**[新規]** をクリックし、**[Pyspark]** または **[Spark]** をクリックします。Scala アプリケーションには Spark カーネルを使用し、Python アプリケーションには PySpark カーネルを使用する必要があります。

	![新しい Jupyter Notebook を作成します](./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "新しい Jupyter Notebook を作成します")

3. 選択したカーネルで、新しい Notebook が開かれます。

## 新しいカーネルを使用する理由

新しいカーネルを使用すると、いくつかの利点があります。

1. 既定の **Python2** カーネルでは、開発しているアプリケーションの操作を開始する前に、Spark、SQL、または Hive コンテキストを設定する必要があります。新しいカーネル (**Spark** または **PySpark**) を使用する場合は、これらのコンテキストが既定で利用可能です。各コンテキストは次のとおりです。

	* **sc**: Spark コンテキスト用
	* **sqlContext**: SQL コンテキスト用
	* **hiveContext** - Hive コンテキスト用


	そのため、コンテキストを設定するための次のようなステートメントを実行する必要はありません。

		###################################################
		# YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
		###################################################
		sc = SparkContext('yarn-client')
		sqlContext = SQLContext(sc)
		hiveContext = HiveContext(sc)

	代わりに、事前に設定されたコンテキストをアプリケーションで直接使用できます。
	
2. SQL クエリまたは Hive クエリを使用するには、それぞれ **%sql** または **%hive** を直接使用することができます。そのため、次のようなコードを、先行するコード ステートメントなしで、そのまま機能させることができます。

		%hive
		SELECT * FROM hivesampletable LIMIT 10

## 新しいカーネルを使用する場合の考慮事項

使用するカーネルが Python2、PySpark、Spark のいずれであっても、Notebook を実行したままにしておくと、クラスターのリソースが消費されます。Python2 Notebook では、コンテキストを明示的に作成するため、アプリケーションの終了時にコンテキストも強制終了させることができます。

しかし、PySpark と Spark カーネルでは、コンテキストが事前設定されているため、コンテキストを明示的に終了させることができません。そのため、Notebook を単に終了しただけでは、コンテキストが実行され続け、クラスター リソースも使用されたままになります。PySpark および Spark カーネルでは、Notebook の **[ファイル]** メニューで **[閉じて停止]** オプションを使用することをお勧めします。これにより、コンテキストが強制終了され、Notebook が終了されます。


## いくつかの例

Jupyter Notebook を開くと、ルート レベルで利用可能な 2 つのフォルダーが表示されます。

* **Python** フォルダーには、既定の **Python2** カーネルを使用するサンプル Notebook があります。
* **Scala** フォルダーには、新しい **Spark** カーネルを使用するサンプル Notebook があります。

各フォルダーの同じ Notebook (たとえば **READ ME FIRST - Learn the Basics of Spark on HDInsight**) を開くと、Python2 Notebook では必要なコンテキストの設定が常に最初に行われ、Spark Notebook では事前に設定されたコンテキストが使用されることを確認できます。

## フィードバック

新しいカーネルは、まだ初期ステージであり、今後も進化していきます。カーネルが改良されるにつれて、API も変更される可能性があります。これらの新しいカーネルに関するフィードバックを、ぜひお寄せください。これらのカーネルの最終リリースの設計に、大いに役立ちます。ご意見やフィードバックは、この記事の下部にある**コメント**のセクションからお寄せください。


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

* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-use-zeppelin-notebook.md)

### リソースの管理

* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0211_2016-->
