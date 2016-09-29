<properties
   pageTitle="Hadoop で MapReduce と PowerShell を使用する | Microsoft Azure"
   description="PowerShell を使用して HDInsight の Hadoop で MapReduce ジョブをリモートで実行する方法を説明します。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/29/2016"
   ms.author="larryfr"/>

# PowerShell を使用して HDInsight の Hadoop で MapReduce ジョブを実行

[AZURE.INCLUDE [mapreduce セレクター](../../includes/hdinsight-selector-use-mapreduce.md)]

このドキュメントでは、Azure PowerShell を使用して HDInsight クラスターの Hadoop で MapReduce ジョブを実行する方法を説明します。

##<a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

- **Azure HDInsight (HDInsight での Hadoop) クラスター (Windows または Linux ベース)**

- **Azure PowerShell を実行できるワークステーション**。

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##<a id="powershell"></a>Azure PowerShell を使用した MapReduce ジョブの実行

Azure PowerShell では、HDInsight で MapReduce ジョブをリモートで実行できる*コマンドレット*が提供されます。これは、HDInsight クラスター上で実行される [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) への REST 呼び出し (旧称: Templeton) を内部的に使用することで機能します。

リモート HDInsight クラスターで MapReduce ジョブを実行するときに次のコマンドレットを使用します。

* **Login-AzureRmAccount**: Azure サブスクリプションに対して Azure PowerShell を認証します。

* **New-AzureRmHDInsightMapReduceJobDefinition**: 指定された MapReduce 情報を使用して、新しい*ジョブ定義*を作成します。

* **Start-AzureRmHDInsightJob**: ジョブ定義を HDInsight に送信し、ジョブを開始して、ジョブのステータスの確認に使用できる*ジョブ* オブジェクトを返します。

* **Wait-AzureRmHDInsightJob**: ジョブ オブジェクトを使用して、ジョブのステータスを確認します。ジョブの完了を待機するか、待機時間が上限に達します。

* **Get-AzureRmHDInsightJobOutput**: ジョブの出力を取得する場合に使用します。

これらのコマンドレットを使用して、HDInsight クラスターでジョブを実行するための手順を以下に示します。

1. エディターを使用して、次のコードを **mapreducejob.ps1** として保存します。**CLUSTERNAME** を HDInsight クラスターの名前に置き換えます。

        #Specify the values
        $clusterName = "CLUSTERNAME"
                
        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Login-AzureRmAccount
        }

        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        #Define the MapReduce job
        #NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
        # -JarFile = the JAR containing the MapReduce application
        # -ClassName = the class of the application
        # -Arguments = The input file, and the output directory
        $wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
            -ClassName "wordcount" `
            -Arguments `
                "wasbs:///example/data/gutenberg/davinci.txt", `
                "wasbs:///example/data/WordCountOutput"

        #Submit the job to the cluster
        Write-Host "Start the MapReduce job..." -ForegroundColor Green
        $wordCountJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $wordCountJobDefinition `
            -HttpCredential $creds

        #Wait for the job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $wordCountJob.JobId `
            -HttpCredential $creds
        # Download the output
        Get-AzureStorageBlobContent `
            -Blob 'example/data/WordCountOutput/part-r-00000' `
            -Container $container `
            -Destination output.txt `
            -Context $context
        # Print the output
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. **Azure PowerShell** コマンド プロンプトを開きます。ディレクトリを **mapreducejob.ps1** ファイルの場所に変更し、次のコマンドを使用してスクリプトを実行します。

		.\mapreducejob.ps1
    
    スクリプトの実行時に、Azure サブスクリプションに対して認証を行うように求められる場合があります。HDInsight クラスターの HTTPS/管理者アカウント名とパスワードの入力も求められます。

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

	> [AZURE.NOTE] **ExitCode** が 0 以外の値の場合は、[トラブルシューティング](#troubleshooting)をご覧ください

    この例では、スクリプトが実行されるディレクトリにある **output.txt** ファイルにダウンロードしたファイルを格納します。

###出力の表示

テキスト エディターで **output.txt** ファイルを開き、ジョブによって生成された文字と文字数を確認します。

> [AZURE.NOTE] MapReduce ジョブの出力ファイルは変更できません。そのため、このサンプルを再実行する場合は、出力ファイルの名前を変更する必要があります。

##<a id="troubleshooting"></a>トラブルシューティング

ジョブの完了時に情報が返されない場合は、処理中にエラーが発生した可能性があります。このジョブに関するエラーを表示するには、次のコマンドを **mapreducejob.ps1** ファイルの末尾に追加して保存し、再実行します。

	# Print the output of the WordCount job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

これにより、ジョブの実行時にサーバー上の STDERR に書き込まれた情報が返されるため、ジョブ失敗の特定に役立ちます。

##<a id="summary"></a>概要

このように、Azure PowerShell を使用すると、HDInsight クラスターで簡単に MapReduce ジョブを実行し、ジョブ ステータスを監視し、出力を取得できます。

##<a id="nextsteps"></a>次のステップ

HDInsight での MapReduce ジョブに関する全般的な情報:

* [HDInsight Hadoop での MapReduce の使用](hdinsight-use-mapreduce.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

<!---HONumber=AcomDC_0914_2016-->