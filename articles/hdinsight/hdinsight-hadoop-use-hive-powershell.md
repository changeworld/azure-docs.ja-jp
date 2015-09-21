<properties
   pageTitle="HDInsight での Hadoop Hive と Hadoop の使用 | Microsoft Azure"
   description="PowerShell を使用して、HDInsight での Hadoop で Hive クエリを実行します。"
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
   ms.date="09/03/2015"
   ms.author="larryfr"/>

#PowerShell を使用して Hive クエリを実行

[AZURE.INCLUDE [hive セレクター](../../includes/hdinsight-selector-use-hive.md)]

このドキュメントでは、Azure PowerShell を Azure リソース グループ モードで使用して、HDInsight クラスターの Hadoop で Hive クエリを実行する方法について説明します。Azure サービス モードでの Azure PowerShell の使用については、「[PowerShell ASM モードを使用して Hive クエリを実行](hdinsight-hadoop-use-hive-powershell-v1.md)」を参照してください。

> [AZURE.NOTE]このドキュメントには、例で使用される HiveQL ステートメントで何が実行されるかに関する詳細は含まれていません。この例で使用される HiveQL については、「[HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)」をご覧ください。


**前提条件**

この記事の手順を完了するには、次のものが必要です。

- **Azure HDInsight (HDInsight での Hadoop) クラスター (Windows または Linux ベース)**
- **Azure PowerShell を実行できるワークステーション**。[Azure PowerShell のインストールおよび使用](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)に関するページを参照してください。

##Azure PowerShell を使用して Hive クエリを実行する

Azure PowerShell では、HDInsight で Hive クエリをリモートに実行できる*コマンドレット*が提供されます。これは、HDInsight クラスター上で実行される [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) への REST 呼び出し (旧称: Templeton) を内部的に使用することで機能します。

リモート HDInsight クラスターで Hive クエリを実行するときに次のコマンドレットを使用します。

* **Add-AzureAccount**: Azure サブスクリプションに対して Azure PowerShell を認証します。

* **New-AzureHDInsightHiveJobDefinition**: 指定された HiveQL ステートメントを使用して新しい*ジョブ定義*を作成します。

* **Start-AzureHDInsightJob**: ジョブ定義を HDInsight に送信し、ジョブを開始して、ジョブのステータスの確認に使用できる*ジョブ* オブジェクトを返します。

* **Wait-AzureHDInsightJob**: ジョブ オブジェクトを使用して、ジョブのステータスを確認します。ジョブの完了を待機するか、待機時間が上限に達します。

* **Get-AzureHDInsightJobOutput**: ジョブの出力を取得します。

* **Invoke-AzureHDInsightHiveJob**: HiveQL ステートメントを実行します。これはクエリの完了にブロックし、その結果を返します。

* **Use-AzureHDInsightCluster**: **Invoke-Hive** コマンドに対し現在のクラスターを使用するように設定します。

これらのコマンドレットを使用して、HDInsight クラスターでジョブを実行するための手順を以下に示します。

1. エディターを使用して、次のコードを **hivejob.ps1** として保存します。**CLUSTERNAME** を HDInsight クラスターの名前に置き換えます。

		#Specify the values
		$clusterName = "CLUSTERNAME"
		$resourceGroupName = "RESOURCEGROUPNAME"
		$httpUsername = "HTTPUSERNAME"
		$httpUserPassword  = "HTTPUSERPASSWORD"

		# Switch to the ARM mode
		Switch-AzureMode -Name AzureResourceManager
		
		# Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}

		#HiveQL
		$queryString = "DROP TABLE log4jLogs;" +
				       "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
				       "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

		#Create an HDInsight Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString 

		#Submit the job to the cluster
		Write-Host "Start the Hive job..." -ForegroundColor Green

		$passwd = ConvertTo-SecureString $httpUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($httpUsername, $passwd)
		$hiveJob = Start-AzureHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds


		#Wait for the Hive job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -StandardOutput 

2. **Azure PowerShell** コマンド プロンプトを開きます。ディレクトリを **hivejob.ps1** ファイルの場所に変更し、次のコマンドを使用してスクリプトを実行します。

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

	> [AZURE.NOTE]より長い HiveQL クエリの場合は、Azure PowerShell の **Here-Strings** コマンドレットや HiveQL スクリプト ファイルを使用できます。次のスニペットでは、**Invoke-Hive** コマンドレットを使用して HiveQL スクリプト ファイルを実行する方法を示します。HiveQL スクリプト ファイルは、WASB にアップロードする必要があります。
	>
	> `Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> **Here-Strings** の詳細については、「<a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Windows PowerShell Here-Strings の使用</a>」をご覧ください。

##トラブルシューティング

ジョブの完了時に情報が返されない場合は、処理中にエラーが発生した可能性があります。このジョブに関するエラーを表示するには、以下を **hivejob.ps1** ファイルの末尾に追加して保存し、再実行します。

	# Print the output of the Hive job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardError

これにより、ジョブの実行時にサーバー上の STDERR に書き込まれた情報が返されるため、ジョブ失敗の特定に役立ちます。

##概要

このように、Azure PowerShell を使用すると、HDInsight クラスターで簡単に Hive クエリを実行し、ジョブ ステータスを監視し、出力を取得できます。

##次のステップ

HDInsight での Hive に関する全般的な情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)

<!---HONumber=Sept15_HO2-->