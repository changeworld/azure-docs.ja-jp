<properties 
	pageTitle="PowerShell を使用した HDInsight での Hadoop クラスターの管理 | Microsoft Azure" 
	description="HDInsight の Hadoop クラスターを Azure PowerShell を使用して管理する方法について説明します。" 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="jgao"/>

# Azure PowerShell を使用した HDInsight での Hadoop クラスターの管理


Azure PowerShell は、Azure のワークロードの展開と管理を制御し自動化するために使用できる強力なスクリプティング環境です。この記事では、ローカルの Azure PowerShell コンソールを使って Azure HDInsight で Hadoop クラスターを管理する方法を紹介します。例として、Windows PowerShell を使用します。HDInsight PowerShell コマンドレットの一覧については、「[HDInsight コマンドレット リファレンス][hdinsight-powershell-reference]」を参照してください。

##前提条件

この記事を読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

- **Azure PowerShell を実行できるワークステーション**。[Azure PowerShell のインストールおよび使用](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)に関するページを参照してください。


##HDInsight クラスターのプロビジョニング
HDInsight は、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには Azure ストレージ アカウントとストレージ コンテナーが必要です。

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Azure ストレージ アカウントを作成するには**

発行設定ファイルをインポートした後、次のコマンドを使ってストレージ アカウントを作成できます。

	# Create an Azure Storage account
	$storageAccountName = "<StorageAcccountName>"
	$location = "<Microsoft data center>"           # For example, "West US"

	New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location


[AZURE.INCLUDE [データ センターの一覧](../../includes/hdinsight-pricing-data-centers-clusters.md)]


Azure ポータルを使った Azure ストレージ アカウントの作成については、[ストレージ アカウントの作成、管理、削除](../storage-create-storage-account/)に関するページを参照してください。

既にストレージ アカウントを持っていて、アカウント名とアカウント キーがわからない場合は、次のコマンドを使ってその情報を取得できます。

	# List Storage accounts for the current subscription
	Get-AzureStorageAccount
	# List the keys for a Storage account
	Get-AzureStorageKey <StorageAccountName>

Azure ポータルを使用して情報を取得する方法の詳細については、[ストレージ アカウントの作成、管理、削除](../storage-create-storage-account/)に関するページの「ストレージ アクセス キーを表示、コピー、再生成する」セクションを参照してください。

**Azure ストレージ コンテナーを作成するには**

Azure PowerShell は、HDInsight のプロビジョニング処理中に、BLOB コンテナーを作成することはできません。コンテナーは次のスクリプトを使って作成します。

	$storageAccountName = "<StorageAccountName>"
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$containerName="<ContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**クラスターをプロビジョニングするには**

ストレージ アカウントを用意して、BLOB コンテナーを準備したら、クラスターを作成する準備は整いました。
		
	$storageAccountName = "<StorageAccountName>"
	$containerName = "<ContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<MicrosoftDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the Storage account key
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

	# Create a new HDInsight cluster
	New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes


次の図はスクリプト実行時のスクリーンショットです。

![HDI.PS.Provision][image-hdi-ps-provision]




##クラスターの詳細の一覧
現在のサブスクリプションにあるクラスターすべてを一覧表示するには次のコマンドを使用します。

	Get-AzureHDInsightCluster 

現在のサブスクリプションにある特定のクラスターの詳細を表示するには次のコマンドを使用します。

	Get-AzureHDInsightCluster -Name <ClusterName> 

##クラスターの削除
クラスターを削除するには、次のコマンドを使用します。

	Remove-AzureHDInsightCluster -Name <ClusterName> 



##HTTP サービスのアクセス許可の付与/取り消し

HDInsight クラスターには、以下の HTTP Web サービスがあります (これらすべてのサービスには、REST ベースのエンドポイントがあります)。

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


既定では、これらのサービスへのアクセス許可が付与されます。アクセス許可を取り消す/付与することができます。例を示します。

	Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

この例では、<i>hdiv2</i> が HDInsight クラスター名です。

>[AZURE.NOTE]アクセス許可を付与するか、取り消すことで、クラスターのユーザー名とパスワードがリセットされます。

これは、Azure ポータルを使用して行うこともできます。[Azure ポータルを使用した HDInsight の管理][hdinsight-admin-portal]に関するページを参照してください。

##クラスターのスケール
[HDInsight での Hadoop クラスターのスケール](hdinsight-hadoop-cluster-scaling.md)に関するページを参照してください。

##MapReduce ジョブの送信
HDInsight クラスターのディストリビューションには MapReduce のサンプルが付属します。サンプルの 1 つは、ソース ファイルに出現する単語の頻度を算出します。

**MapReduce ジョブを送信するには**

以下に、ワード カウント サンプル ジョブを送信する Azure PowerShell スクリプトを示します。
	
	$clusterName = "<HDInsightClusterName>"            
	
	# Define the MapReduce job
	$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"
	
	# Run the job and show the standard error 
	$wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}
	
**wasb** プレフィックスについては、[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]に関するページを参照してください。

**MapReduce ジョブの出力をダウンロードするには**

次の Azure PowerShell スクリプトは、先の手順で実行した MapReduce ジョブの出力を取得します。

	$storageAccountName = "<StorageAccountName>"   
	$containerName = "<ContainerName>"             
		
	# Create the Storage account context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Download the output to local computer
	Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
	
	# Display the output
	cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

MapReduce ジョブの開発と実行の詳細については、「[HDInsight での MapReduce の使用][hdinsight-use-mapreduce]」を参照してください。






































##Hive ジョブの送信
HDInsight クラスターのディストリビューションには、*hivesampletable* という Hive テーブルのサンプルが付属します。HiveQL の **SHOW TABLES** コマンドを使うと、クラスターの Hive テーブルの一覧を表示できます。

**Hive ジョブを送信するには**

以下に、Hive テーブルの一覧を表示する Hive ジョブを送信するスクリプトを示します。
	
	$clusterName = "<HDInsightClusterName>"               
	
	# HiveQL query
	$querystring = @"
		SHOW TABLES;
		SELECT * FROM hivesampletable 
			WHERE Country='United Kingdom'
			LIMIT 10;
	"@

	Use-AzureHDInsightCluster -Name $clusterName
	Invoke-Hive $querystring

この Hive ジョブはまず、クラスターに作成された Hive テーブルを表示し、続いて hivesampletable テーブルから返されたデータを表示します。

Hive の使用法の詳細については、「[HDInsight での Hive の使用][hdinsight-use-hive]」を参照してください。


##Azure BLOB ストレージにデータをアップロードする
「[データを HDInsight にアップロードする方法][hdinsight-upload-data]」を参照してください。

##Azure BLOB ストレージからのジョブの出力のダウンロード
この記事の「[MapReduce ジョブの送信](#mapreduce)」セクションを参照してください。

## 関連項目
* [HDInsight コマンドレット リファレンス ドキュメント][hdinsight-powershell-reference]
* [Azure ポータルを使用した HDInsight の管理][hdinsight-admin-portal]
* [コマンド ライン インターフェイスを使用した HDInsight の管理][hdinsight-admin-cli]
* [HDInsight クラスターのプロビジョニング][hdinsight-provision]
* [HDInsight へのデータのアップロード][hdinsight-upload-data]
* [プログラムによる Hadoop ジョブの送信][hdinsight-submit-jobs]
* [Azure HDInsight の概要][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png


 

<!---HONumber=62-->