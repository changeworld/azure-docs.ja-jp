<properties 
	pageTitle="HDInsight での Hadoop ジョブの送信 | Microsoft Azure" 
	description="Hadoop ジョブを Azure HDInsight Hadoop へ送信する方法について説明します。" 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/15/2015" 
	ms.author="jgao"/>

# HDInsight での Hadoop ジョブの送信

Azure PowerShell を使用して MapReduce、と Hive ジョブを送信する方法と、HDInsight .NET SDK を使用して、MapReduce、Hadoop ストリーミング、Hive ジョブを送信する方法について説明します。

> [AZURE.NOTE]この記事の手順は、Windows クライアントから実行する必要があります。Linux、OS X、または Unix クライアントを使用して、HDInsight で MapReduce、Hive、または Pig を操作する方法の詳細については、次の記事を参照し、**SSH** または **Curl** のリンクを選択してください。
>
> - [HDInsight での Hive の使用](hdinsight-use-hive.md)
> - [HDInsight での Pig の使用](hdinsight-use-pig.md)
> - [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

##前提条件

この記事を読み始める前に、次の項目を用意する必要があります。

* **Azure HDInsight クラスター**。手順については、「[Azure HDInsight の概要][hdinsight-get-started]」または「[HDInsight クラスターのプロビジョニング][hdinsight-provision]」を参照してください。
- **Azure PowerShell を実行できるワークステーション**。[Azure PowerShell のインストールおよび使用](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)に関するページを参照してください。



##Azure PowerShell を使用して MapReduce ジョブを送信する
Azure PowerShell は、Azure のワークロードのデプロイと管理を制御し自動化するために使用できる強力なスクリプティング環境です。HDInsight で Azure PowerShell を使用する方法の詳細については、「[PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]」をご覧ください。

Hadoop MapReduce は、膨大なデータを処理するアプリケーションを記述するためのソフトウェア フレームワークです。HDInsight クラスターには JAR ファイル (*\\example\\jars\\hadoop-mapreduce-examples.jar*) が付属していて、MapReduce サンプルがいくつか格納されています。

サンプルの 1 つは、ソース ファイルに出現する単語の頻度を算出します。ここでは、ワークステーションから Azure PowerShell を使用して、ワード カウント サンプルを実行する方法を説明します。MapReduce ジョブの開発と実行の詳細については、「[HDInsight での MapReduce の使用][hdinsight-use-mapreduce]」をご覧ください。

**Azure PowerShell を使用してワード カウント MapReduce プログラムを実行するには**

1.	**Azure PowerShell** を開きます。Azure PowerShell コンソール ウィンドウを開く手順については、「[Azure PowerShell のインストールおよび構成][powershell-install-configure]」をご覧ください。

3. これらの Azure PowerShell コマンドを実行して、次の変数を設定します。
		
		$subscriptionName = "<SubscriptionName>"   
		$clusterName = "<HDInsightClusterName>"    

	サブスクリプション名は、HDInsight クラスターの作成時に使用したサブスクリプションです。HDInsight クラスターは、MapReduce ジョブの実行に使用するクラスターです。
	
5. 次のコマンドを実行して、MapReduce ジョブ定義を作成します。

		# Define the word count MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	引数が 2 つあります。最初の引数はソース ファイル名で、2 つ目の引数は出力ファイル パスです。wasb:// プレフィックスの詳細については、「[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]」をご覧ください。

6. 次のコマンドを実行して、MapReduce ジョブを実行します。

		# Submit the MapReduce job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 

	MapReduce ジョブ定義に加えて、MapReduce ジョブを実行する HDInsight クラスター名も指定します。

7. 次のコマンドを実行して、MapReduce ジョブの完了を確認します。

		# Wait for the job to complete
		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 
		

8. 次のコマンドを実行して、MapReduce ジョブの実行中に発生したエラーを確認します。

		# Get the job standard error output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 
					
	次のスクリーンショットは、正常実行時の出力を示しています。正常でない場合は、エラー メッセージが表示されます。

	![HDI.GettingStarted.RunMRJob][image-hdi-gettingstarted-runmrjob]

		
**MapReduce ジョブの結果を取得するには**

1. **Azure PowerShell** を開きます。
2. これらの Azure PowerShell コマンドを実行して、次の変数を設定します。

		$subscriptionName = "<SubscriptionName>"       
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"			

	ストレージ アカウント名は、HDInsight クラスターのプロビジョニング時に指定した Azure ストレージ アカウントです。ストレージ アカウントは、既定の HDInsight クラスター ファイル システムとして使用する BLOB コンテナーをホストするために使用されます。コンテナー名は、クラスターのプロビジョニング時に別の名前を指定しない限り、通常、HDInsight クラスターと同じ名前です。

3. 次のコマンドを実行して、Azure BLOB ストレージのコンテキスト オブジェクトを作成します。

		# Create the storage account context object
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	**Select-AzureSubscription** は、サブスクリプションが複数あり、使用するサブスクリプションが既定のサブスクリプションではない場合に備えて、現在のサブスクリプションを設定するために使用します。

4. 次のコマンドを実行して、MapReduce ジョブの出力を BLOB コンテナーからコンピューターにダウンロードします。

		# Get the blob content
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	*example/data/WordCountOutput* フォルダーは、MapReduce ジョブの実行時に指定した出力フォルダーです。*part-r-00000* は MapReduce ジョブの出力の既定のファイル名です。ファイルはフォルダー構造を保ったままローカル フォルダーにダウンロードされます。たとえば、次のスクリーンショットでは、現在のフォルダーが C ドライブのルート フォルダーです。ファイルは以下にダウンロードされます。

**C:\\example\\data\\WordCountOutput*

5. 次のコマンドを実行して、MapReduce ジョブの出力ファイルの内容を表示します。

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	![HDI.GettingStarted.MRJobOutput][image-hdi-gettingstarted-mrjoboutput]

	MapReduce ジョブは、単語と出現回数が記録された *part-r-00000* という名前のファイルを作成します。スクリプトでは **findstr** コマンドを使用して、"there" を含む単語をすべて表示しています。


> [AZURE.NOTE]MapReduce ジョブで出力された複数行を含む ./example/data/WordCountOutput/part-r-00000 ファイルをメモ帳で開いた場合、改行が正しく表示されません。これは予期されることです。









































































































































##Azure PowerShell を使用して Hive ジョブを送信する
[Apache Hive][apache-hive] では、*HiveQL* と呼ばれる SQL に似たスクリプト言語を使用して MapReduce ジョブを実行します。大規模なデータの集約、照会、分析でも Hive を利用できます。

HDInsight クラスターには、*hivesampletable* という Hive テーブルのサンプルが付属します。ここでは、Azure PowerShell を使用して Hive ジョブを実行して、Hive テーブルのデータを表示する方法を説明します。

**Azure PowerShell を使用して Hive ジョブを実行するには**

1.	**Azure PowerShell** を開きます。Azure PowerShell コンソール ウィンドウを開く手順については、「[Azure PowerShell のインストールおよび構成][powershell-install-configure]」をご覧ください。

2. 次のコマンドで最初の 2 つの変数を設定して、コマンドを実行します。
		
		$subscriptionName = "<SubscriptionName>"   
		$clusterName = "<HDInsightClusterName>"             
		$querystring = "SELECT * FROM hivesampletable WHERE Country='United Kingdom';"

	$querystring は HiveQL クエリです。

3. 次のコマンドを実行して、Hive ジョブを実行する Azure サブスクリプションとクラスターを選択します。

		Select-AzureSubscription -SubscriptionName $subscriptionName

4. 次のコマンドを実行して、Hive ジョブを送信します。

		Use-AzureHDInsightCluster $clusterName
		Invoke-Hive -Query $queryString

	**-File** スイッチを使用して Hadoop 分散ファイル システム (HDFS) で HiveQL スクリプト ファイルを指定します。

Hive の詳細については、「[HDInsight での Hive の使用][hdinsight-use-hive]」を参照してください。


## Visual Studio を使用して Hive ジョブを送信する

「[HDInsight Hadoop Tools for Visual Studio の使用開始][hdinsight-visual-studio-tools]」をご覧ください。

##Azure PowerShell を使用して Sqoop ジョブを送信する

「[Hadoop .NET SDK と HDInsight の使用][hdinsight-use-sqoop]」を参照してください。

##HDInsight .NET SDK を使用して MapReduce ジョブを送信する
HDInsight .NET SDK は、.NET から HDInsight クラスターを簡単に操作できる .NET クライアント ライブラリを提供します。HDInsight クラスターには JAR ファイル (*\\example\\jars\\hadoop-mapreduce-examples.jar*) が付属していて、MapReduce サンプルがいくつか格納されています。サンプルの 1 つは、ソース ファイルに出現する単語の頻度を算出します。ここでは、.NET アプリケーションを作成して、ワード カウント サンプルを実行する方法を説明します。MapReduce ジョブの開発と実行の詳細については、「[HDInsight での MapReduce の使用][hdinsight-use-mapreduce]」をご覧ください。


SDK を使用して HDInsight クラスターをプロビジョニングするには、以下の手順が必要です。

- HDInsight .NET SDK のインストール
- コンソール アプリケーションの作成
- アプリケーションの実行


**HDInsight .NET SDK をインストールするには**、公開されている最新の SDK を [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) からインストールできます。次の手順で、具体的な方法を説明します。

**Visual Studio コンソール アプリケーションを作成するには**

1. Visual Studio を開きます。

2. **[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。

3. **[新しいプロジェクト]** で、次の値を入力するか、選択します。

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">プロパティ</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">値</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">カテゴリ</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">テンプレート/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">テンプレート</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">コンソール アプリケーション</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名前</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitMapReduceJob</td></tr>
</table>

4. **[OK]** をクリックしてプロジェクトを作成します。


5. **[ツール]** メニューで **[ライブラリ パッケージ マネージャー]** をクリックし、次に **[パッケージ マネージャー コンソール]** をクリックします。

6. コンソールで次のコマンドを実行して、パッケージをインストールします。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	このコマンドは、.NET ライブラリおよび .NET ライブラリへの参照を現在の Visual Studio プロジェクトに追加します。バージョンは 0.11.0.1 またはそれ以降である必要があります。

7. **ソリューション エクスプローラー**で **[Program.cs]** をダブルクリックして開きます。

8. 次の using ステートメントをファイルの先頭に追加します。

		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;
		
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;

9. 次の関数定義をクラスに追加します。この関数は Hadoop ジョブの完了を待機するために使用されます。

        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
        {
            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }
	
10. **Main()** 関数で、次のコードをファイルに貼り付けます。
		
		// Set the variables
		string subscriptionID = "<Azure subscription ID>";
		string certFriendlyName = "<certificate friendly name>";

		string clusterName = "<HDInsight cluster name>";
		
		string storageAccountName = "<Azure storage account name>";
		string storageAccountKey = "<Azure storage account key>";
		string containerName = "<Blob container name>";
		
	
	これがプログラムで設定する必要のある変数のすべてです。Azure のサブスクリプション名は [Azure ポータル][azure-management-portal]で確認できます。

	証明書については、「[Azure の管理証明書の作成とアップロード][azure-certificate]」をご覧ください。証明書を構成する簡単な方法は、Azure PowerShell コマンドレットの **Get-AzurePublishSettingsFile** と **Import-AzurePublishSettingsFile** を実行することです。管理証明書が自動的に作成されアップロードされます。これらのコマンドレットを実行した後、ワークステーションから **certmgr.msc** を開いて **[個人]** > **[証明書]** の順に展開すると、証明書が表示されます。Azure PowerShell コマンドレットによって作成された証明書は、**[発行先]** と **[発行元]** が [Azure Tools] になります。

	Azure のストレージ アカウント名は、HDInsight クラスターのプロビジョニング時に指定するアカウントです。既定のコンテナー名は、HDInsight クラスター名と同じです。
	
11. **Main()** 関数の最後に次のコードをコピーして、MapReduce ジョブを定義します。


        // Define the MapReduce job
        MapReduceJobCreateParameters mrJobDefinition = new MapReduceJobCreateParameters()
        {
            JarFile = "wasb:///example/jars/hadoop-mapreduce-examples.jar",
            ClassName = "wordcount"
        };

        mrJobDefinition.Arguments.Add("wasb:///example/data/gutenberg/davinci.txt");
        mrJobDefinition.Arguments.Add("wasb:///example/data/WordCountOutput");

	引数が 2 つあります。最初の引数はソース ファイル名で、2 つ目の引数は出力ファイル パスです。wasb:// プレフィックスの詳細については、「[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]」をご覧ください。
		
12. **Main()** 関数の最後に次のコードをコピーして、JobSubmissionCertificateCredential オブジェクトを作成します。

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
13. **Main()** 関数の最後に次のコードをコピーして、ジョブを実行し、ジョブの完了を待ちます。

        // Create a hadoop client to connect to HDInsight
        var jobClient = JobSubmissionClientFactory.Connect(creds);

        // Run the MapReduce job
        JobCreationResults mrJobResults = jobClient.CreateMapReduceJob(mrJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(mrJobResults, jobClient);

14. **Main()** 関数の最後に次のコードをコピーして、MapReduce ジョブの出力を表示します。

		// Print the MapReduce job output
		Stream stream = new MemoryStream();
		
		CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey);
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
		CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);
		CloudBlockBlob blockBlob = blobContainer.GetBlockBlobReference("example/data/WordCountOutput/part-r-00000");
		
		blockBlob.DownloadToStream(stream);
		stream.Position = 0;
		
		StreamReader reader = new StreamReader(stream);
		Console.WriteLine(reader.ReadToEnd());
		
        Console.WriteLine("Press ENTER to continue.");
		Console.ReadLine();

	出力フォルダーは MapReduce ジョブの定義時に指定します。既定のファイル名は **part-r-00000** です。

**アプリケーションを実行するには**

アプリケーションを Visual Studio で開いている間に、**F5** キーを押してアプリケーションを実行します。コンソール ウィンドウが開き、アプリケーションの状態とアプリケーションの出力が表示されます。

##HDInsight .NET SDK を使用して Hadoop ストリーミング ジョブを送信する
HDInsight クラスターには、C# で開発されたワード カウント Hadoop ストリーミング プログラムが付属します。Mapper プログラムは */example/apps/cat.exe* で、Reduce プログラムは */example/apps/wc.exe* です。ここでは、.NET アプリケーションを作成して、ワード カウント サンプルを実行する方法を説明します。

MapReduce ジョブを送信する .NET アプリケーションの作成方法の詳細については、「[HDInsight .NET SDK を使用して MapReduce ジョブを送信する](#mapreduce-sdk)」をご覧ください。

Hadoop ストリーミング ジョブの開発とデプロイ方法の詳細については、「[HDInsight 用 C# Hadoop ストリーミング プログラムの開発][hdinsight-develop-streaming-jobs]」をご覧ください。

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	
	using System.IO;
	using System.Threading;
	using System.Security.Cryptography.X509Certificates;
	
	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.Hadoop.Client;
	
	namespace SubmitStreamingJob
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {

				// Set the variables
				string subscriptionID = "<Azure subscription ID>";
				string certFriendlyName = "<certificate friendly name>";
		
				string clusterName = "<HDInsight cluster name>";
				string statusFolderName = @"/tutorials/wordcountstreaming/status";

	            // Define the Hadoop streaming MapReduce job
	            StreamingMapReduceJobCreateParameters myJobDefinition = new StreamingMapReduceJobCreateParameters()
	            {
	                JobName = "my word counting job",
	                StatusFolder = statusFolderName,
	                Input = "/example/data/gutenberg/davinci.txt",
	                Output = "/tutorials/wordcountstreaming/output",
	                Reducer = "wc.exe",
	                Mapper = "cat.exe"
	            };
	
	            myJobDefinition.Files.Add("/example/apps/wc.exe");
	            myJobDefinition.Files.Add("/example/apps/cat.exe");
	
	            // Get the certificate object from certificate store using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
	
	            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
	
	            // Create a hadoop client to connect to HDInsight
	            var jobClient = JobSubmissionClientFactory.Connect(creds);
	
	            // Run the MapReduce job
	            Console.WriteLine("----- Submit the Hadoop streaming job ...");
	            JobCreationResults mrJobResults = jobClient.CreateStreamingJob(myJobDefinition);
	
	            // Wait for the job to complete
	            Console.WriteLine("----- Wait for the Hadoop streaming job to complete ...");
	            WaitForJobCompletion(mrJobResults, jobClient);
	
	            // Display the error log
	            Console.WriteLine("----- The hadoop streaming job error log.");
	            using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
	            {
	                var reader = new StreamReader(stream);
	                Console.WriteLine(reader.ReadToEnd());
	            }
	
	            // Display the output log
	            Console.WriteLine("----- The hadoop streaming job output log.");
	            using (Stream stream = jobClient.GetJobOutput(mrJobResults.JobId))
	            {
	                var reader = new StreamReader(stream);
	                Console.WriteLine(reader.ReadToEnd());
	            }
	
	            Console.WriteLine("----- Press ENTER to continue.");
	            Console.ReadLine();
	        }
	
	        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
	        {
	            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
	            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
	            {
	                jobInProgress = client.GetJob(jobInProgress.JobId);
	                Thread.Sleep(TimeSpan.FromSeconds(10));
	            }
	        }
	    }
	}






##HDInsight .NET SDK を使用して Hive ジョブを送信する 
HDInsight クラスターには、*hivesampletable* という Hive テーブルのサンプルが付属します。ここでは、.NET アプリケーションを作成して Hive ジョブを実行して、HDInsight クラスター上に作成された Hive テーブルを一覧表示する方法を説明します。Hive の使い方の詳細については、「[HDInsight での Hive の使用][hdinsight-use-hive]」をご覧ください。

SDK を使用して HDInsight クラスターをプロビジョニングするには、以下の手順が必要です。

- HDInsight .NET SDK のインストール
- コンソール アプリケーションの作成
- アプリケーションの実行


**HDInsight .NET SDK をインストールするには**、公開されている最新の SDK を [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) からインストールできます。次の手順で、具体的な方法を説明します。

**Visual Studio コンソール アプリケーションを作成するには**

1. Visual Studio を開きます。

2. **[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。

3. **[新しいプロジェクト]** で、次の値を入力するか、選択します。

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">プロパティ</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">値</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">カテゴリ</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">テンプレート/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">テンプレート</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">コンソール アプリケーション</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">名前</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitHiveJob</td></tr>
</table>

4. **[OK]** をクリックしてプロジェクトを作成します。


5. **[ツール]** メニューで **[ライブラリ パッケージ マネージャー]** をクリックし、次に **[パッケージ マネージャー コンソール]** をクリックします。

6. コンソールで次のコマンドを実行して、パッケージをインストールします。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight


	このコマンドは、.NET ライブラリおよび .NET ライブラリへの参照を現在の Visual Studio プロジェクトに追加します。

7. **ソリューション エクスプローラー**で **[Program.cs]** をダブルクリックして開きます。

8. 次の **using** ステートメントをファイルの先頭に追加します。

		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;

		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;

9. 次の関数定義をクラスに追加します。この関数は Hadoop ジョブの完了を待機するために使用されます。

        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
        {
            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }
	
10. **Main()** 関数で、次のコードをファイルに貼り付けます。
		
		// Set the variables
		string subscriptionID = "<Azure subscription ID>";
		string clusterName = "<HDInsight cluster name>";
		string certFriendlyName = "<certificate friendly name>";		
		
	
	これがプログラムで設定する必要のある変数のすべてです。Azure のサブスクリプション ID はシステム管理者から入手できます。

	証明書については、「[Azure の管理証明書の作成とアップロード][azure-certificate]」をご覧ください。証明書を構成する簡単な方法は、Azure PowerShell コマンドレットの **Get-AzurePublishSettingsFile** と **Import-AzurePublishSettingsFile** を実行することです。管理証明書が自動的に作成されアップロードされます。これらのコマンドレットを実行した後、ワークステーションから **certmgr.msc** を開いて **[個人]** > **[証明書]** の順に展開すると、証明書が表示されます。Azure PowerShell コマンドレットによって作成された証明書には、**[発行先]** と **[発行元]** フィールドの Azure Tools があります。
	
11. **Main()** 関数の最後に次のコードをコピーして、Hive ジョブを定義します。

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            Query = "show tables;"
        };

	**File** パラメーターを使用して、HDFS 上の HiveQL スクリプト ファイルを指定することもできます。

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            File = "/user/admin/showtables.hql"
        };

		
12. **Main()** 関数の最後に次のコードをコピーして、**JobSubmissionCertificateCredential**オブジェクトを作成します。
	
        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
13. **Main()** 関数の最後に次のコードをコピーして、ジョブを実行し、ジョブの完了を待ちます。

        // Submit the Hive job
        var jobClient = JobSubmissionClientFactory.Connect(creds);
        JobCreationResults jobResults = jobClient.CreateHiveJob(hiveJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(jobResults, jobClient);
		
14. **Main()** 関数の最後に次のコードをコピーして、Hive ジョブの出力を表示します。

        // Print the Hive job output
        System.IO.Stream stream = jobClient.GetJobOutput(jobResults.JobId);

        StreamReader reader = new StreamReader(stream);
        Console.WriteLine(reader.ReadToEnd());

        Console.WriteLine("Press ENTER to continue.");
        Console.ReadLine();

**アプリケーションを実行するには**

アプリケーションを Visual Studio で開いている間に、**F5** キーを押してアプリケーションを実行します。コンソール ウィンドウが開き、アプリケーションの状態が表示されます。出力は次のようになります。

	hivesampletable




##次のステップ
この記事では、HDInsight クラスターをプロビジョニングする方法をいくつか説明しました。詳細については、次の記事を参照してください。

* [Azure HDInsight の概要][hdinsight-get-started]
* [HDInsight クラスターのプロビジョニング][hdinsight-provision]
* [PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]
* [HDInsight コマンドレット リファレンス ドキュメント][hdinsight-powershell-reference]
* [HDInsight での Hive の使用][hdinsight-use-hive]
* [HDInsight での Pig の使用][hdinsight-use-pig]


[azure-certificate]: http://msdn.microsoft.com/library/windowsazure/gg551722.aspx
[azure-management-portal]: http://manage.windowsazure.com/

[hdinsight-visual-studio-tools]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/
 

<!---HONumber=August15_HO6-->