<properties 
	pageTitle="HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ | Microsoft Azure" 
	description="Azure HDInsight の Spark クラスターで実行されるジョブの追跡とデバッグには、YARN UI、Spark UI、Spark History Server を使用します。" 
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
	ms.date="05/31/2016" 
	ms.author="nitinme"/>

# Azure HDInsight の Apache Spark クラスターで実行されるジョブの追跡とデバッグ (プレビュー)

この記事では、YARN UI と Spark UI、Spark History Server を使用して Spark ジョブの追跡とデバッグを行う方法について説明します。この記事では、「**Machine Learning: MLlib を使用した食品検査データの予測分析**」で使用した Spark クラスターのノートブックを使用して Spark ジョブを実行します。他の方法 (**spark-submit** など) で送信したアプリケーションについても、以下の手順に従って追跡することができます。

##前提条件

次のものが必要です。

- Azure サブスクリプション。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
- HDInsight Linux での Apache Spark クラスター。手順については、「[HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)」を参照してください。
- 「**[Machine Learning: MLlib を使用した食品検査データの予測分析](hdinsight-apache-spark-machine-learning-mllib-ipython.md)**」のノートブックを実行済みであること。このノートブックの実行方法については、リンク先のページを参照してください。  

## YARN UI でのアプリケーションの追跡

1. YARN UI を起動します。クラスター ブレードから **[クラスター ダッシュボード]** をクリックし、**[YARN]** をクリックします。

	![Launch YARN UI](./media/hdinsight-apache-spark-job-debugging/launch-yarn-ui.png)

	>[AZURE.TIP] Ambari UI から YARN UI を起動してもかまいません。Ambari UI を起動するには、クラスター ブレードから **[クラスター ダッシュボード]** をクリックし、**[HDInsight クラスター ダッシュボード]** をクリックします。Ambari UI から **[YARN]**、**[クイック リンク]** の順にクリックし、アクティブなリソース マネージャーをクリックして、**[ResourceManager UI]** をクリックします。

3. Jupyter Notebook を使用して Spark ジョブを開始したため、アプリケーションの名前は **remotesparkmagics** (ノートブックから開始されたすべてのアプリケーションに使用される名前) になっています。ジョブに関する詳しい情報を確認するには、アプリケーション名に対応するアプリケーション ID をクリックします。これによりアプリケーション ビューが起動されます。

	![Find Spark application ID](./media/hdinsight-apache-spark-job-debugging/find-application-id.png)

	Jupyter Notebook から起動されたアプリケーションの場合、ノートブックを終了するまでステータスは常に **[実行中]** になります。

4. アプリケーション ビューからドリルダウンして、アプリケーションやログ (stdout/stderr) に関連付けられているコンテナーを探すことができます。次のように、**[追跡 URL]** に対応するリンクをクリックして Spark UI を起動することもできます。

	![Download container logs](./media/hdinsight-apache-spark-job-debugging/download-container-logs.png)

## Spark UI でのアプリケーションの追跡

以前アプリケーションを実行したときに生成された Spark ジョブは、Spark UI からドリルダウンすることができます。

1. Spark UI を起動するには、アプリケーション ビューから **[追跡 URL]** に対応するリンクをクリックします (上のスクリーン ショットを参照)。Jupyter Notebook で実行中のアプリケーションによって開始されたすべての Spark ジョブを表示できます。

	![View Spark jobs](./media/hdinsight-apache-spark-job-debugging/view-spark-jobs.png)

2. **[Executors]** タブをクリックすると、実行プログラムごとの処理や記憶域の情報が表示されます。**[Thread Dump (スレッド ダンプ)]** リンクをクリックして呼び出し履歴を取得することもできます。

	![View Spark executors](./media/hdinsight-apache-spark-job-debugging/view-spark-executors.png)
 
3. アプリケーションに関連付けられているステージを確認するには、**[Stages (ステージ)]** タブをクリックします。

	![View Spark stages](./media/hdinsight-apache-spark-job-debugging/view-spark-stages.png)

	1 つのステージに複数のタスクが存在する場合もあり、対応する実行の統計を表示することができます (下図参照)。

	![View Spark stages](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-details.png)

4. ステージの詳細ページから DAG Visualization を起動できます。以下に示したように、ページの上部にある **[DAG Visualization]** リンクを展開してください。

	![View Spark stages DAG visualization](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

	DAG (Direct Aclyic Graph) は、アプリケーション内の各種ステージを表します。グラフ内の青色のボックスはそれぞれ、アプリケーションから呼び出された Spark 操作を表します。

5. ステージの詳細ページから、アプリケーションのタイムライン ビューを起動することもできます。以下に示したように、ページの上部にある **[Event Timeline (イベントのタイムライン)]** リンクを展開してください。

	![View Spark stages event timeline](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-event-timeline.png)

	Spark イベントがタイムラインの形式で表示されます。タイムライン ビューには 3 種類の表示があります (ジョブ間、ジョブ内、ステージ内)。上の画像は、特定のステージのタイムライン ビューをキャプチャしたものです。

	>[AZURE.TIP] **[Enable zooming (ズームを有効にする)]** チェック ボックスをオンにすると、タイムライン ビューを左右にスクロールすることができます。

6. Spark UI には、Spark インスタンスに関する有益な情報を得ることのできるタブが他にもあります。

	* [Storage (ストレージ)] タブ: アプリケーションで RDD を作成した場合、その情報を [Storage (ストレージ)] タブで確認できます。
	* [Environment (環境)] タブ: Spark インスタンスに関する有益な情報が多数表示されます。その例を次に示します。 
		* Scala バージョン
		* クラスターに関連付けられているイベント ログ ディレクトリ
		* アプリケーションの Executor のコア数
		* その他

## 完了したジョブに関する情報を Spark History Server で探す

完了したジョブに関する情報は、Spark History Server に保存されています。

1. Spark History Server を起動するには、クラスター ブレードから **[クラスター ダッシュボード]** をクリックし、**[Spark History Server]** をクリックします。

	![Launch Spark History Server](./media/hdinsight-apache-spark-job-debugging/launch-spark-history-server.png)

	>[AZURE.TIP] Ambari UI から Spark History Server の UI を起動してもかまいません。Ambari UI を起動するには、クラスター ブレードから **[クラスター ダッシュボード]** をクリックし、**[HDInsight クラスター ダッシュボード]** をクリックします。Ambari UI から **[Spark]**、**[クイック リンク]** の順にクリックし、**Spark History Server UI** をクリックします。

2. 完了済みのすべてのアプリケーションが一覧表示されます。アプリケーションをドリルダウンして、より詳しい情報を入手するには、アプリケーション ID をクリックします。

	![Launch Spark History Server](./media/hdinsight-apache-spark-job-debugging/view-completed-applications.png)
	

## <a name="seealso"></a>関連項目


* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview.md)

### シナリオ

* [Spark と BI: HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools.md)

* [Spark と Machine Learning: HDInsight で Spark を使用して HVAC データを基に建物の温度を分析する](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark と Machine Learning: HDInsight で Spark を使用して食品の検査結果を予測する](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark ストリーミング: リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-eventhub-streaming.md)

### アプリケーションの作成と実行

* [Scala を使用してスタンドアロン アプリケーションを作成する](hdinsight-apache-spark-create-standalone-application.md)

* [Livy を使用して Spark クラスターでジョブをリモートで実行する](hdinsight-apache-spark-livy-rest-interface.md)

### ツールと拡張機能

* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (Linux)](hdinsight-apache-spark-intellij-tool-plugin.md)

* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### リソースの管理

* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0601_2016-->