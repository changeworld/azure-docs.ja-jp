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
	ms.date="12/22/2015" 
	ms.author="nitinme"/>


# Azure HDInsight での Apache Spark クラスターのリソースの管理 (Windows)

> [AZURE.NOTE] HDInsight は Linux 上に Spark クラスターを提供するようになりました。HDInsight Linux 上の Spark クラスターのリソースを管理する方法については、「[Azure HDInsight での Apache Spark クラスターのリソースの管理 (Linux)](hdinsight-apache-spark-resource-manager.md)」を参照してください。

リソース マネージャーは Spark クラスター ダッシュボードのコンポーネントであり、クラスターで実行される各アプリケーションによって使用されるコアや RAM などのリソースを管理できます。

## <a name="launchrm"></a>リソース マネージャーの起動方法

1. [Azure ポータル](https://ms.portal.azure.com/)のスタート画面で Spark クラスターのタイルをクリックします (スタート画面にピン留めしている場合)。**[すべて参照]** > **[HDInsight クラスター]** でクラスターに移動することもできます。 
 
2. Spark クラスター ブレードで、**[ダッシュ ボード]** をクリックします。入力を求められたら、Spark クラスターの管理者資格情報を入力します。

	![リソース マネージャーを起動する](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.cluster.launch.dashboard.png "リソース マネージャーを開始する")

##<a name="scenariosrm"></a>リソース マネージャーを使用して問題を解決する方法

ここでは、Spark クラスターでよく発生することがある状況と、リソース マネージャーを使用してそれに対処する方法を説明します。

### HDInsight の Spark クラスターが遅い

HDInsight の Apache Spark クラスターはマルチテナント用に設計されているので、リソースは複数のコンポーネントに分割されます (Notebook、ジョブ サーバーなど)。これにより、コンポーネントが実行するためのリソースを取得できないことを心配することなくすべての Spark コンポーネントを同時に使用できますが、リソースがフラグメント化されるため各コンポーネントは遅くなります。これは、ニーズに基づいて調整できます。


### Spark クラスターを含む Jupyter Notebook のみを使用します。どうすればすべてのリソースを割り当てることができますか。

1. **[Spark ダッシュボード]** で **[Spark UI]** タブをクリックして、アプリケーションに割り当てることができる最大コア数と最大 RAM を調べます。

	![リソースの割り当て](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.resource.png "Spark クラスターに割り当てられたリソースを検索します")

	上の画面キャプチャでは、割り当て可能な最大コア数は 7 (合計 8 コアのうち 1 つは使用中) であり、割り当て可能な最大 RAM は 9 GB (合計 12 GB の RAM のうち、2 GB はシステム用、1 GB は他のアプリケーションで使用中) です。

	実行しているすべてのアプリケーションを考慮する必要もあります。実行中のアプリケーションは、**[Spark UI]** タブで確認できます。

	![アプリケーションの実行](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.running.apps.png "クラスターで実行されているアプリケーション")

	
2. HDInsight Spark ダッシュ ボードで、**[リソース マネージャー]** タブをクリックし、**[既定のアプリケーション コア数]** および **[ワーカー ノードごとの既定の実行プログラム メモリ]** の値を指定します。他のプロパティは 0 に設定します。

	![リソースの割り当て](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.allocate.resources.png "アプリケーションにリソースを割り当てます")

### Spark クラスターで BI ツールを使用していません。どのようにしてリソースを取り戻せばいいですか。 

Thrift サーバーのコア数と Thrift サーバーの実行プログラム メモリを 0 と指定します。コアやメモリを割り当てないと、Thrift サーバーは**待機**状態になります。

![リソースの割り当て](./media/hdinsight-apache-spark-resource-manager-v1/hdispark.ui.no.thrift.png "Thrift サーバーへのリソースがありません")

##<a name="seealso"></a>関連項目

* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview-v1.md)
* [HDInsight クラスターでの Spark の作成](hdinsight-apache-spark-provision-clusters.md)
* [HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools-v1.md)
* [Machine Learning アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)
* [リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0330_2016-->