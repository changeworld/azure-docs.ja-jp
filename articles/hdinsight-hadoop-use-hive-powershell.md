<properties
   pageTitle="HDInsight での Hadoop Hive の使用 | Azure"
   description="PowerShell を通じた HDInsight での Hadoop Hive の使用"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# PowerShell を使用して Hive クエリを実行

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

このドキュメントでは、PowerShell を使用して HDInsight クラスターの Hadoop で Hive クエリを実行する方法について説明します。

> [AZURE.NOTE] このドキュメントには、例で使用される HiveQL ステートメントで何が実行されるかに関する詳細は含まれていません。例で使用される HiveQL の詳細については、「 <a href="../hdinsight-use-hive/" target="_blank">HDInsight での Hive と Hadoop の使用</a>」を参照してください。


## <a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Azure HDInsight (HDInsight での Hadoop) クラスター (Windows または Linux ベースのいずれか)

* <a href="http://azure.microsoft.com/ documentation/articles/install-configure-powershell/" target="_blank">Azure PowerShell</a>


## <a id="powershell"></a>PowerShell を使用して Hive クエリを実行

Azure PowerShell では、HDInsight で Hive クエリをリモートに実行できる *cmdlets*が提供されます。内部的には、これは、HDInsight クラスターで実行する  <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat" target="_blank">WebHCat</a>  (旧称: Templeton) への REST 呼び出しを使用して実行されます。

リモート HDInsight クラスターで Hive クエリを実行するときに次のコマンドレットを使用します。

* **Add-AzureAccount** - Azure サブスクリプションに対して PowerShell を認証します。

* **New-AzureHDInsightHiveJobDefinition** - 指定された HiveQL ステートメントを使用して新しい *job definition*を作成します。

* **Start-AzureHDInsightJob** - ジョブ定義を HDInsight に送信し、ジョブを開始し、ジョブのステータスの確認に使用できる  *job* オブジェクトを返します。

* **Wait-AzureHDInsightJob** - ジョブ オブジェクトを使用して、ジョブのステータスを確認します。ジョブの完了を待機するか、待機時間が上限に達します。

* **Get-AzureHDInsightJobOutput** - ジョブの出力を取得します。

* **Invoke-Hive** - HiveQL ステートメントを実行、その完了をブロックします。その後、結果を返します。

* **Use-AzureHDInsightCluster** - **Invoke-Hive** コマンドに対し現在のクラスターを使用するように設定します。

これらのコマンドレットを使用して、HDInsight クラスターでジョブを実行するための手順を以下に示します。 

1. エディターを使用して、次のコードを **hivejob.ps1** として保存します。**CLUSTERNAME** を HDInsight クラスターの名前に置き換えます。

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}
		
		#Specify the cluster name
		$clusterName = "CLUSTERNAME" 
		
		#HiveQL
		$queryString = "DROP TABLE log4jLogs;" +
				       "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
				       "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"
		
		#Create an HDInsight Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString
		
		#Submit the job to the cluster
		Write-Host "Start the Hive job..." -ForegroundColor Green
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition
		
		#Wait for the Hive job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600
		
		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

2. 新しい **Microsoft Azure PowerShell** プロンプトを開きます。ディレクトリを **hivejob.ps1** ファイルの場所に変更し、以下を使用してスクリプトを実行します。

		.\hivejob.ps1

7. ジョブが完了すると、次のような情報が返されます。

		Display the standard output...
		[ERROR]	3

4. 前述のように、**Invoke-Hive** を使用してクエリを実行し、応答を待機できます。次のコマンドを使用して、**CLUSTERNAME** をクラスターの名前で置き換えます。

		Use-AzureHDInsightCluster CLUSTERNAME
		Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		SELECT * FROM errorLogs;
		"@

	出力は次のようになります。

		2012-02-03	18:35:34	SampleClass0	[ERROR]	incorrect	id	
		2012-02-03	18:55:54	SampleClass1	[ERROR]	incorrect	id	
		2012-02-03	19:25:27	SampleClass4	[ERROR]	incorrect	id

	> [AZURE.NOTE] より長い HiveQL クエリの場合は、PowerShell の Here-Strings または HiveQL スクリプト ファイルを使用できます。次のスニペットでは、 *Invoke-Hive* コマンドレットを使用して HiveQL スクリプト ファイルを実行する方法を示します。HiveQL スクリプト ファイルは、WASB にアップロードする必要があります。
	>
	> `呼び出すには. - ファイル"wasb://< ContainerName > @< StorageAccountName >/< パス >/query.hql"`
	>
	> Here-Strings の詳細については、「 <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Using Windows PowerShell Here-Strings (Windows PowerShell Here-Strings の使用)</a>」を参照してください。

## <a id="troubleshooting"></a>トラブルシューティング

ジョブの完了時に何の情報も返されない場合は、処理中にエラーが発生している可能性があります。このジョブに関するエラーを表示するには、以下を **hivejob.ps1** ファイルの末尾に追加して保存し、再実行します。

	# Print the output of the Hive job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardError

これにより、ジョブの実行時にサーバー上の STDERR に書き込まれた情報が返されるため、ジョブ失敗の特定に役立ちます。

## <a id="summary"></a>まとめ

このように、Azure PowerShell を使用すると、HDInsight クラスターで簡単に Hive クエリを実行し、ジョブ ステータスを監視し、出力を取得できます。

## <a id="nextsteps"></a>次のステップ

HDInsight での Hive に関する全般的な情報

* [HDInsight での Hive と Hadoop の使用](../hdinsight-use-hive/)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Pig と Hadoop の使用](../hdinsight-use-pig/)

* [HDInsight での MapReduce と Hadoop の使用](../hdinsight-use-mapreduce/)

<!--HONumber=45--> 
