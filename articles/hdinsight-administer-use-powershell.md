<properties 
	pageTitle="Azure PowerShell を使用した HDInsight Hadoop クラスターの管理 | Azure" 
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
	ms.date="11/21/2014" 
	ms.author="jgao"/>

# Azure PowerShell を使用した HDInsight での Hadoop クラスターの管理

Azure PowerShell は、Azure のワークロードの展開と管理を制御し自動化するために使用できる強力なスクリプティング環境です。この記事では、ローカルの Azure PowerShell コンソールを使って HDInsight で Hadoop クラスターを管理する方法を紹介します。例として、Windows PowerShell を使用します。HDInsight PowerShell コマンドレットの一覧については、「[HDInsight コマンドレット リファレンス][hdinsight-powershell-reference]」を参照してください。

**前提条件:**

この記事を読み始める前に、次の項目を用意する必要があります。

- Azure サブスクリプション。Azure はサブスクリプション方式のプラットフォームです。HDInsight PowerShell コマンドレットはサブスクリプションを使ってタスクを実行します。サブスクリプションの入手方法の詳細については、[購入オプション][azure-purchase-options]、[メンバー プラン][azure-member-offers]、または[無料評価版][azure-free-trial]に関するページを参照してください。

- Azure PowerShell を実行できるワークステーション。手順については、[Azure PowerShell のインストールおよび構成][Powershell-install-configure]に関するページを参照してください。

			
	

## この記事の内容

* [クラスターのプロビジョニング](#provision)
* [クラスターの一覧と表示](#listshow)
* [クラスターの削除](#delete)
* [HTTP サービスのアクセス許可の付与/取り消し](#httpservices)
* [MapReduce ジョブの送信](#mapreduce)
* [Hive ジョブの送信](#hive)
* [BLOB ストレージへのデータのアップロード](#upload)
* [BLOB ストレージからの MapReduce 出力データのダウンロード](#download)


##<a id="provision"></a> HDInsight クラスターをプロビジョニングする
HDInsight は、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。HDInsight クラスターを作成するには Azure ストレージ アカウントとストレージ コンテナーが必要です。 

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Azure Storage アカウントを作成するには**

発行設定ファイルをインポートした後、次のコマンドを使ってストレージ アカウントを作成できます。

	# Create an Azure storage account
	$storageAccountName = "<StorageAcccountName>"
	$location = "<Microsoft data center>"           # For example, "West US"

	New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

> [AZURE.NOTE] ストレージ アカウントは、HDInsight クラスターと同じデータ センターに置く必要があります。現在、HDInsight クラスターのプロビジョニングができるのは次のデータ センターだけです。

><ul>
<li>東南アジア</li>
<li>北ヨーロッパ</li>
<li>西ヨーロッパ</li>
<li>米国東部</li>
<li>米国西部</li>
</ul>



管理ポータルを使った Azure Storage アカウントの作成については、[Azure Storage アカウントの作成、管理、削除](に関するページを参照してください(storage-create-storage-account.md)。

既にストレージ アカウントを持っていて、アカウント名とアカウント キーがわからない場合は、次のコマンドを使ってその情報を取得できます。

	# List storage accounts for the current subscription
	Get-AzureStorageAccount
	# List the keys for a storage account
	Get-AzureStorageKey <StorageAccountName>

管理ポータルを使用して情報を取得する方法の詳細については、「*方法: ストレージ アクセス キーの表示、コピーおよび再生成」*セクション ([ストレージ アカウントの作成、管理、削除方法](storage-create-storage-account.md)に関するページ）を参照してください。

**Azure Storage コンテナーを作成するには**

PowerShell は、HDInsight のプロビジョニング処理中に、BLOB コンテナーを作成することはできません。コンテナーは次のスクリプトを使って作成します。

	$storageAccountName = "<StorageAccountName>"
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$containerName="<ContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
	                                       -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**クラスターをプロビジョニングするには**

ストレージ アカウントを用意して、BLOB コンテナーを準備したら、クラスターを作成する準備は整いました。    
		
	$storageAccountName = "<StorageAccountName>"
	$containerName = "<ContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<MicrosoftDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the storage account key
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

	# Create a new HDInsight cluster
	New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes


次の図はスクリプト実行時のスクリーンショットです。

![HDI.PS.Provision][image-hdi-ps-provision]




##<a id="listshow"></a> クラスターの一覧と詳細の表示
クラスターの一覧と詳細を表示するには、次のコマンドを使用します。

**現在のサブスクリプションにあるクラスターをすべて一覧表示するには**

	Get-AzureHDInsightCluster 

**現在のサブスクリプションにある特定のクラスターの詳細を表示するには**

	Get-AzureHDInsightCluster -Name <ClusterName> 

##<a id="delete"></a> クラスターの削除
クラスターを削除するには、次のコマンドを使用します。

	Remove-AzureHDInsightCluster -Name <ClusterName> 

##<a id="httpservice"></a> HTTP サービスのアクセス許可の付与/取り消し

HDInsight クラスターには、以下の HTTP Web サービスがあります (これらすべてのサービスには、REST ベースのエンドポイントがあります)。

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


既定では、これらのサービスへのアクセス許可が付与されます。アクセス許可を取り消す/付与することができます。例を示します。

	Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

この例で、 <i>hdiv2</i> が HDInsight クラスター名です。

>[AZURE.NOTE] アクセス許可を付与する/取り消すことで、クラスター ユーザーのユーザー名とパスワードがリセットされます。

この処理は、Windows Azure 管理ポータルを使って行うこともできます。[管理ポータルを使用した HDInsight の管理][hdinsight-admin-portal]に関するページを参照してください。

##<a id="mapreduce"></a> MapReduce ジョブの送信
HDInsight クラスターのディストリビューションには MapReduce のサンプルが付属します。サンプルの 1 つは、ソース ファイルに出現する単語の頻度を算出します。

**MapReduce ジョブを送信するには**

以下に、ワード カウント サンプル ジョブを送信する PowerShell スクリプトを示します。 
	
	$clusterName = "<HDInsightClusterName>"            
	
	# Define the MapReduce job
	$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"
	
	# Run the job and show the standard error 
	$wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}
	
> [AZURE.NOTE] *hadoop-examples.jar* は、バージョン 2.1 の HDInsight クラスターに付属しています。バージョン 3.0 の HDInsight クラスターで、このファイルの名前は  *hadoop-mapreduce.jar* に変更されました。

WASB プレフィックスについては、[HDInsight での Azure BLOB ストレージの使用][hdinsight-
storage]に関するページを参照してください。

**MapReduce ジョブの出力をダウンロードするには**

次の PowerShell スクリプトは、先の手順で実行した MapReduce ジョブの出力を取得します。

	$storageAccountName = "<StorageAccountName>"   
	$containerName = "<ContainerName>"             
		
	# Create the storage account context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Download the output to local computer
	Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
	
	# Display the output
	cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

MapReduce ジョブの開発と実行の詳細については、[HDInsight での Hadoop MapReduce の使用][hdinsight-use-mapreduce]に関するページを参照してください。






































##<a id="hive"></a> Hive ジョブの送信
HDInsight クラスターのディストリビューションには、 *hivesampletable* という Hive テーブルのサンプルが付属します。HiveQL の "show tables;" を使うと、クラスターの Hive テーブルの一覧を表示できます。

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

この Hive ジョブはまず、クラスターに作成された Hive テーブルを表示し、続いて hivesampletable から返されたデータを表示します。

Hive の使用法の詳細については、[HDInsight での Hive の使用][hdinsight-use-hive]に関するページを参照してください。


##<a id="upload"></a>BLOB ストレージへのデータのアップロード
[HDInsight へのデータのアップロード][hdinsight-upload-data]に関するページを参照してください。

##<a id="download"></a>BLOB ストレージからの MapReduce 出力のダウンロード
この記事の「[MapReduce ジョブの送信]」 セクション(#mapreduce) を参照してください。

## 関連項目
* [HDInsight コマンドレット リファレンス ドキュメント][hdinsight-powershell-reference]
* [管理ポータルを使用した HDInsight クラスターの管理][hdinsight-admin-portal]に関するページ
* [コマンド ライン インターフェイスを使用した HDInsight の管理][hdinsight-admin-cli]に関するページ
* [HDInsight クラスターのプロビジョニング][hdinsight-provision]に関するページ
* [HDInsight へのデータのアップロード][hdinsight-upload-data]に関するページ
* [プログラムによる Hadoop ジョブの送信][hdinsight-submit-jobs]に関するページ
* [Azure HDInsight の概要][hdinsight-get-started]に関するページ


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-provision]: ../hdinsight-provision-clusters/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/

[hdinsight-admin-cli]: ../hdinsight-administer-use-command-line/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-upload-data]: ../hdinsight-upload-data/

[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx

[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png



<!--HONumber=42-->
