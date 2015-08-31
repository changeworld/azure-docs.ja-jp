<properties
   pageTitle="HDInsight での Hadoop Pig と PowerShell の使用 | Microsoft Azure"
   description="Azure PowerShell を使用して HDInsight で Pig ジョブを Hadoop クラスターに送信する方法を説明します。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>

#PowerShell を使用した Pig ジョブの実行

[AZURE.INCLUDE [pig セレクター](../../includes/hdinsight-selector-use-pig.md)]

このドキュメントでは、Azure PowerShell を使用して HDInsight クラスターで Pig ジョブを Hadoop に送信する方法を説明します。Pig では map 関数や reduce 関数ではなく、データ変換をモデル化する言語 (Pig Latin) を使用して MapReduce ジョブを記述できます。

> [AZURE.NOTE]このドキュメントには、例で使用される Pig Latin ステートメントで何が実行されるかに関する詳細は含まれていません。この例で使用される Pig Latin については「[HDInsight での Pig Latin と Hadoop の使用](hdinsight-use-pig.md)」をご覧ください。

##<a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

- **Azure PowerShell を実行できるワークステーション**。[Azure PowerShell のインストールおよび使用](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)に関するページを参照してください。


##<a id="powershell"></a>PowerShell を使用した Pig ジョブの実行

Azure PowerShell では、HDInsight で Pig ジョブをリモートで実行できる*コマンドレット*が提供されます。これは、HDInsight クラスター上で実行される [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) への REST 呼び出し (旧称: Templeton) を内部的に使用することで機能します。

リモート HDInsight クラスターで Pig ジョブを実行するときに次のコマンドレットを使用します。

* **Add-AzureAccount**: Azure サブスクリプションに対して Azure PowerShell を認証します。

* **New-AzureHDInsightPigJobDefinition**: 指定された Pig Latin ステートメントを使用して新しい*ジョブ定義*を作成します。

* **Start-AzureHDInsightJob**: ジョブ定義を HDInsight に送信し、ジョブを開始して、ジョブのステータスの確認に使用できる*ジョブ* オブジェクトを返します。

* **Wait-AzureHDInsightJob**: ジョブ オブジェクトを使用して、ジョブのステータスを確認します。ジョブの完了を待機するか、待機時間が上限に達します。

* **Get-AzureHDInsightJobOutput**: ジョブの出力を取得します。

これらのコマンドレットを使用して、HDInsight クラスターでジョブを実行するための手順を以下に示します。

1. エディターを使用して、次のコードを **pigjob.ps1** として保存します。**CLUSTERNAME** を HDInsight クラスターの名前に置き換えます。

		#Login to your Azure subscription
		Add-AzureAccount

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"
		#Where the output will be saved
		$statusFolder = "/tutorial/pig/status"

		#Store the Pig Latin into $QueryString
		$QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		"LEVELS = foreach LOGS generate REGEX_EXTRACT(`$0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		"FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		"GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		"FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		"RESULT = order FREQUENCIES by COUNT desc;" +
		"DUMP RESULT;"

		#Create a new HDInsight Pig Job definition
		$pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString -StatusFolder $statusFolder

		# Start the Pig job on the HDInsight cluster
		Write-Host "Start the Pig job ..." -ForegroundColor Green
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition

		# Wait for the Pig job to complete
		Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

		# Print the output of the Pig job.
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

2. Azure PowerShell コマンド プロンプトを開きます。ディレクトリを **pigjob.ps1** ファイルの場所に変更し、次のコマンドを使用してスクリプトを実行します。

		.\pigjob.ps1

7. ジョブが完了すると、次のような情報が返されます。

		Start the Pig job ...
		Wait for the Pig job to complete ...

		Cluster         : CLUSTERNAME
		ExitCode        : 0
		Name            :
		PercentComplete : 100% complete
		Query           : LOGS = LOAD 'wasb:///example/data/sample.log';LEVELS = foreach LOGS generate REGEX_EXTRACT($0,
			'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is
			not null;GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;FREQUENCIES = foreach GROUPEDLEVELS
			generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;RESULT = order FREQUENCIES by
			COUNT desc;DUMP RESULT;
			State           : Completed
			StatusDirectory : /tutorial/pig/status
			SubmissionTime  : 11/20/2014 4:04:58 PM
			JobId           : job_1415949758166_0023

			Display the standard output ...
			(TRACE,816)
			(DEBUG,434)
			(INFO,96)
			(WARN,11)
			(ERROR,6)
			(FATAL,2)

##<a id="troubleshooting"></a>トラブルシューティング

ジョブの完了時に情報が返されない場合は、処理中にエラーが発生した可能性があります。このジョブに関するエラー情報を表示するには、次のコマンドを **pigjob.ps1** ファイルの末尾に追加して保存し、再実行します。

	# Print the output of the Pig job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardError

これにより、ジョブの実行時にサーバー上の STDERR に書き込まれた情報が返されるため、ジョブ失敗の特定に役立ちます。

##<a id="summary"></a>概要

このように、Azure PowerShell を使用すると、HDInsight クラスターで簡単に Pig ジョブを実行し、ジョブ ステータスを監視し、出力を取得できます。

##<a id="nextsteps"></a>次のステップ

HDInsight での Pig に関する全般的な情報:

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)

<!---HONumber=August15_HO8-->