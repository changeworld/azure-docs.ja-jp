<properties 
	pageTitle="HDInsight での Apache Spark ジョブ サーバー | Microsoft Azure" 
	description="Spark ジョブ サーバーを使用して Spark クラスター上のジョブをリモートで送信および管理する方法を説明します。" 
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
	ms.date="12/08/2015" 
	ms.author="nitinme"/>


# Azure HDInsight クラスター上の Spark ジョブ サーバー (Windows)

> [AZURE.NOTE] HDInsight は Linux 上に Spark クラスターを提供するようになりました。ここでは、Livy を使用して Spark クラスターにジョブをリモートで送信します。Linux 上の HDInsight Spark で Livy を使用する方法の詳細については、「[HDInsight の Spark クラスターで Livy を使用して Spark ジョブをリモートで送信する (Linux)](hdinsight-apache-spark-livy-rest-interface.md)」を参照してください。

Azure HDInight 上の Apache Spark クラスターでは、クラスター デプロイメントの一部として Spark ジョブ サーバーがパッケージ化されます。Spark ジョブ サーバーが提供する REST API を使用すると、Spark コンテキストの作成、コンテキストへの Spark アプリケーションの送信、ジョブのステータスのチェック、コンテキストの削除などを行うことができます。この記事では、ジョブ サーバーを使用する Spark クラスター上で Curl を使用して一般的なタスクを実行する方法の例を示します。

>[AZURE.NOTE] Spark ジョブ サーバーの詳細なドキュメントについては、[https://github.com/spark-jobserver/spark-jobserver](https://github.com/spark-jobserver/spark-jobserver) をご覧ください。

## <a name="uploadjar"></a>jar を Spark クラスターにアップロードする

	curl.exe -k -u "<hdinsight user>:<user password>" --data-binary @<location of jar on the computer> https://<cluster name>.azurehdinsight.net/sparkjobserver/jars/<application name>

例:
	
	curl.exe -k -u "myuser:myPass@word1" --data-binary @C:\mylocation\eventhubs-examples\target\spark-streaming-eventhubs-example-0.1.0-jar-with-dependencies.jar https://mysparkcluster.azurehdinsight.net/sparkjobserver/jars/streamingjar


##<a name="createcontext"></a>ジョブ サーバーで新しい永続的コンテキストを作成する

	curl.exe -k -u "<hdinsight user>:<user password>" -d "" "https://<cluster name>.azurehdinsight.net/sparkjobserver/contexts/<context name>?num-cpu-cores=<value>&memory-per-node=<value>"

例:

	curl.exe -k -u "myuser:myPass@word1" -d "" "https://mysparkcluster.azurehdinsight.net/sparkjobserver/contexts/mystreaming?num-cpu-cores=4&memory-per-node=1024m"


##<a name="submitapp"></a>アプリケーションをクラスターに送信する

	curl.exe -k -u "<hdinsight user>:<user password>" -d @<input file name> "https://<cluster name>.azurehdinsight.net/sparkjobserver/jobs?appName=<app name>&classPath=<class path>&context=<context>"

例:

	curl.exe -k -u "myuser:myPass@word1" -d @mypostdata.txt "https://mysparkcluster.azurehdinsight.net/sparkjobserver/jobs?appName=streamingjar&classPath=org.apache.spark.streaming.eventhubs.example.EventCountJobServer&context=mystreaming"

mypostdata.txt ではアプリケーションが定義されています。


##<a name="submitapp"></a>ジョブを削除する

	curl.exe -X DELETE -k -u "<hdinsight user>:<user password>" "https://<cluster name>.azurehdinsight.net/sparkjobserver/contexts/<context>"

例:

	curl.exe -X DELETE -k -u "myuser:myPass@word1" "https://mysparkcluster.azurehdinsight.net/sparkjobserver/contexts/mystreaming"


##<a name="seealso"></a>関連項目

* [概要: Azure HDInsight での Apache Spark](hdinsight-apache-spark-overview-v1.md)
* [HDInsight クラスターでの Spark の作成](hdinsight-apache-spark-provision-clusters.md)
* [HDInsight と BI ツールで Spark を使用した対話型データ分析の実行](hdinsight-apache-spark-use-bi-tools-v1.md)
* [Machine Learning アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)
* [リアルタイム ストリーミング アプリケーションを作成するための HDInsight での Spark の使用](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)
* [Azure HDInsight での Apache Spark クラスターのリソースの管理](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0218_2016-->