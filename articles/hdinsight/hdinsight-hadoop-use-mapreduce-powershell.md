<properties
   pageTitle="Hadoop で MapReduce と PowerShell を使用する | Microsoft Azure"
   description="PowerShell を使用して HDInsight の Hadoop で MapReduce ジョブをリモートで実行する方法を説明します。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>

#PowerShell を使用して HDInsight の Hadoop で Hive クエリを実行

[AZURE.INCLUDE [mapreduce セレクター](../../includes/hdinsight-selector-use-mapreduce.md)]

このドキュメントでは、Azure PowerShell を使用して HDInsight クラスターの Hadoop で MapReduce ジョブを実行する方法を説明します。

##<a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

- **Azure HDInsight (HDInsight での Hadoop) クラスター (Windows または Linux ベース)**

- **Azure PowerShell を実行できるワークステーション**。「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」を参照

##<a id="powershell"></a>Azure PowerShell を使用した MapReduce ジョブの実行

Azure PowerShell では、HDInsight で MapReduce ジョブをリモートで実行できる*コマンドレット*が提供されます。これは、HDInsight クラスター上で実行される [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) への REST 呼び出し (旧称: Templeton) を内部的に使用することで機能します。

リモート HDInsight クラスターで MapReduce ジョブを実行するときに次のコマンドレットを使用します。

* **Add-AzureAccount**: Azure サブスクリプションに対して Azure PowerShell を認証します。

* **New-AzureHDInsightMapReduceJobDefinition**: 指定された MapReduce 情報を使用して新しい*ジョブ定義*を作成します。

* **Start-AzureHDInsightJob**: ジョブ定義を HDInsight に送信し、ジョブを開始して、ジョブのステータスの確認に使用できる*ジョブ* オブジェクトを返します。

* **Wait-AzureHDInsightJob**: ジョブ オブジェクトを使用して、ジョブのステータスを確認します。ジョブの完了を待機するか、待機時間が上限に達します。

* **Get-AzureHDInsightJobOutput**: ジョブの出力を取得します。

これらのコマンドレットを使用して、HDInsight クラスターでジョブを実行するための手順を以下に示します。

1. エディターを使用して、次のコードを **mapreducejob.ps1** として保存します。**CLUSTERNAME** を HDInsight クラスターの名前に置き換えます。

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"

		#Define the MapReduce job
		#NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
		# -JarFile = the JAR containing the MapReduce application
		# -ClassName = the class of the application
		# -Arguments = The input file, and the output directory
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
		                          -ClassName "wordcount" `
		                          -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

		#Submit the job to the cluster
		Write-Host "Start the MapReduce job..." -ForegroundColor Green
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition

		#Wait for the job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600

		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardOutput

2. **Azure PowerShell** コマンド プロンプトを開きます。ディレクトリを **mapreducejob.ps1** ファイルの場所に変更し、次のコマンドを使用してスクリプトを実行します。

		.\mapreducejob.ps1

3. コマンドが完了すると、次のような出力が返されます。

		Cluster         : CLUSTERNAME
		ExitCode        : 0
		Name            : wordcount
		PercentComplete : map 100% reduce 100%
		Query           :
		State           : Completed
		StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
		SubmissionTime  : 12/5/2014 8:34:09 PM
		JobId           : job_1415949758166_0071

	この出力は、ジョブが正常に完了したことを示しています。

	> [AZURE.NOTE]**ExitCode** が 0 以外の値の場合は、[トラブルシューティング](#troubleshooting)をご覧ください

##<a id="results"></a>ジョブの出力の表示

MapReduce ジョブは Azure BLOB ストレージの操作の結果を、ジョブの引数として指定された **wasb:///example/data/WordCountOutput** パスに格納しました。Azure BLOB ストレージは Azure PowerShell からアクセスできますが、ストレージ アカウント名、キー、ファイルに直接アクセスするために HDInsight クラスターが使用するコンテナーの情報が必要です。

これらの情報は次の Azure PowerShell コマンドレットを使用して取得できます。

* **Get-AzureHDInsightCluster**: HDInsight クラスターやそれに関連付けられたストレージ アカウントに関する情報を返します。クラスターには常に既定のストレージ アカウントが関連付けられます。
* **New-AzureStorageContext**: **Get-AzureHDInsightCluster** を使用して取得されるストレージ アカウント名とキーを指定することで、ストレージ アカウントへのアクセスに使用できるコンテキスト オブジェクトを返します。
* **Get-AzureStorageBlob**: コンテキスト オブジェクトとコンテナー名を指定することで、コンテナー内の BLOB の一覧を返します。
* **Get-AzureStorageBlobContent**: **Get-AzureHDinsightCluster** から返されるコンテキスト オブジェクト、ファイル パス、ファイル名、コンテナー名を指定することで、Azure BLOB ストレージからファイルをダウンロードします。

次の例では、**wasb:///example/data/WordCountOutput** からストレージ情報を取得し、出力をダウンロードします。**CLUSTERNAME** を、使用する HDInsight クラスターの名前に置き換えます。

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"

		#Retrieve the cluster information
		$clusterInfo = Get-AzureHDInsightCluster -ClusterName $clusterName

		#Get the storage account information
		$storageAccountName = $clusterInfo.DefaultStorageAccount.StorageAccountName
		$storageAccountKey = $clusterInfo.DefaultStorageAccount.StorageAccountKey
		$storageContainer = $clusterInfo.DefaultStorageAccount.StorageContainerName

		#Create the context object
		$context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		#Download the files from wasb:///example/data/WordCountOutput
		#Use the -blob switch to filter only blobs contained in example/data/WordCountOutput
		Get-AzureStorageBlob -Container $storageContainer -Blob example/data/WordCountOutput/* -Context $context | Get-AzureStorageBlobContent -Context $context

> [AZURE.NOTE]この例では、スクリプトが実行されるディレクトリにある **example/data/WordCountOutput** フォルダーにダウンロードしたファイルを格納します。

MapReduce ジョブの出力は *part-r-#####* という名前のファイルに格納されます。テキスト エディターで **example/data/WordCountOutput/part-r-00000** ファイルを開き、ジョブによって生成された文字と回数を確認します。

> [AZURE.NOTE]MapReduce ジョブの出力ファイルは変更できません。そのため、このサンプルを再実行する場合は、出力ファイルの名前を変更する必要があります。

##<a id="troubleshooting"></a>トラブルシューティング

ジョブの完了時に情報が返されない場合は、処理中にエラーが発生した可能性があります。このジョブに関するエラーを表示するには、次のコマンドを **mapreducejob.ps1** ファイルの末尾に追加して保存し、再実行します。

	# Print the output of the WordCount job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

これにより、ジョブの実行時にサーバー上の STDERR に書き込まれた情報が返されるため、ジョブ失敗の特定に役立ちます。

##<a id="summary"></a>概要

このように、Azure PowerShell を使用すると、HDInsight クラスターで簡単に MapReduce ジョブを実行し、ジョブ ステータスを監視し、出力を取得できます。

##<a id="nextsteps"></a>次のステップ

HDInsight での MapReduce ジョブに関する全般的な情報:

* [HDInsight Hadoop での MapReduce の使用](hdinsight-use-mapreduce.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

<!---HONumber=July15_HO2-->