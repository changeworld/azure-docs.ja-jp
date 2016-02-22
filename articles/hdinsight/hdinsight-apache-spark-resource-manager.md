<properties 
	pageTitle="リソース マネージャーを使用した HDInsight の Apache Spark クラスターへのリソースの割り当て | Microsoft Azure" 
	description="リソース マネージャーを使用して HDInsight の Spark クラスターのパフォーマンスを向上させる方法について説明します。" 
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


# Azure HDInsight (Linux) での Apache Spark クラスターのリソースの管理

Azure HDInsight (Linux) の Spark には、クラスター リソースの管理とクラスターの正常性の監視を行うための Ambari Web UI が用意されています。クラスターで実行したアプリケーションを追跡するために、Spark History Server を使用することもできます。クラスターで現在実行されているアプリケーションを監視するには、YARN UI を使用することができます。この記事では、これらの UI にアクセスする手順と、これらのインターフェイスを使用して基本的なリソース管理タスクを実行する手順について説明します。

**前提条件:**

次のものが必要です。

- Azure サブスクリプション。[Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
- HDInsight Linux での Apache Spark クラスター。手順については、[Azure HDInsight での Apache Spark クラスターの作成](hdinsight-apache-spark-jupyter-spark-sql.md)に関するページを参照してください。

## Ambari Web UI の起動方法

1. [Azure プレビュー ポータル](https://ms.portal.azure.com/)のスタート画面で Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。**[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。 
 
2. Spark クラスター ブレードで、**[ダッシュ ボード]** をクリックします。入力を求められたら、Spark クラスターの管理者資格情報を入力します。

	![Launch Ambari](./media/hdinsight-apache-spark-resource-manager/hdispark.cluster.launch.dashboard.png "リソース マネージャーを開始する")

3. これで、次の図のように、Ambari Web UI が起動されます。

	![Ambari Web UI](./media/hdinsight-apache-spark-resource-manager/ambari-web-ui.png "Ambari Web UI")

## Spark History Server の起動方法

1. [Azure プレビュー ポータル](https://ms.portal.azure.com/)のスタート画面で Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。

2. クラスター ブレードの **[クイック リンク]** で、**[クラスター ダッシュボード]** をクリックします。**[クラスター ダッシュボード]** ブレードで **[Spark History Server]** をクリックします。

	![Spark History Server](./media/hdinsight-apache-spark-resource-manager/launch-history-server.png "Spark History Server")

	入力を求められたら、Spark クラスターの管理者資格情報を入力します。


## Yarn UI の起動方法

Spark クラスターで現在実行されているアプリケーションを監視するには、YARN UI を使用することができます。YARN UI にアクセスするには、クラスターへの SSH トンネリングを有効にする必要があります。手順については、[SSH トンネリングを使用して Ambari Web UI にアクセスする](hdinsight-linux-ambari-ssh-tunnel.md)方法に関するページを参照してください。

1. 上のセクションの手順に従って、Ambari Web UI を起動します。

2. Ambari Web UI でページの左側にある一覧から [YARN] を選択します。

3. YARN サービスの情報が表示されたら、**[クイック リンク]** を選択します。クラスターのヘッド ノードの一覧が表示されます。ヘッド ノードのいずれかを選択し、**[ResourceManager UI]** を選択します。

	![Launch YARN UI](./media/hdinsight-apache-spark-resource-manager/launch-yarn-ui.png "Launch YARN UI")

4. これで YARN UI が起動され、次のようなページが表示されます。

	![YARN UI](./media/hdinsight-apache-spark-resource-manager/yarn-ui.png "YARN UI")

##<a name="scenariosrm"></a>Ambari Web UI を使用してリソースを管理する方法

ここでは、Spark クラスターでよく発生することがある状況と、Ambari Web UI を使用してそれに対処する方法を説明します。

### Spark クラスターで BI は使用しません。リソースを取り戻すにはどうすればよいですか?

1. 上の手順に従って、Ambari Web UI を起動します。左側のナビゲーション ウィンドウで、**[Spark]**、**[Configs]** の順にクリックします。

2. 利用可能な構成の一覧で **[Custom spark-thrift-sparkconf]** を探し、**[spark.executor.memory]** および **[spark.drivers.core]** の値を **0** に変更します。

	![Resources for BI](./media/hdinsight-apache-spark-resource-manager/spark-bi-resources.png "Resources for BI")

3. **[保存]** をクリックします。行った変更の説明を入力し、もう一度 **[Save]** をクリックします。

4. ページの上部に、Spark サービスの再起動を求めるメッセージが表示されます。変更内容を有効にするには、**[Restart]** をクリックします。


### Jupyter Notebook が期待どおりに実行されていません。サービスを再起動するには、どうすればよいですか?

1. 上の手順に従って、Ambari Web UI を起動します。左側のナビゲーション ウィンドウで、**[Jupyter]**、**[Service Actions]**、**[Restart All]** の順にクリックします。これで、すべてのヘッドノードで Jupyter サービスが開始されます。

	![Restart Jupyter](./media/hdinsight-apache-spark-resource-manager/restart-jupyter.png "Restart Jupyter")

	


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

* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applicatons (Linux)](hdinsight-apache-spark-intellij-tool-plugin.md)

* [HDInsight の Spark クラスターで Zeppelin Notebook を使用する](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [HDInsight 用の Spark クラスターの Jupyter Notebook で使用可能なカーネル](hdinsight-apache-spark-jupyter-notebook-kernels.md)



[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_0211_2016-->