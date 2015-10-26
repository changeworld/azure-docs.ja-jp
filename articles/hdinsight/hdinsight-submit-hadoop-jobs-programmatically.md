<properties
	pageTitle="HDInsight での Hadoop ジョブの送信 | Microsoft Azure"
	description="Hadoop ジョブを Azure HDInsight Hadoop へ送信する方法について説明します。"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/02/2015"
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

* **Azure HDInsight クラスター**。手順については、「[HDInsight の使用][hdinsight-get-started]」または「[HDInsight で Hadoop クラスターを作成する][hdinsight-provision]」を参照してください。
- **Azure PowerShell を実行できるワークステーション**。[Azure PowerShell のインストールおよび使用](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)に関するページを参照してください。



##Azure PowerShell を使用して MapReduce ジョブを送信する
Azure PowerShell は、Azure のワークロードのデプロイメントと管理を制御し自動化するために使用できる強力なスクリプティング環境です。HDInsight で Azure PowerShell を使用する方法の詳細については、「[PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]」をご覧ください。

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

	ストレージ アカウント名は、HDInsight クラスターの作成時に指定した Azure ストレージ アカウントです。ストレージ アカウントは、既定の HDInsight クラスター ファイル システムとして使用する BLOB コンテナーをホストするために使用されます。コンテナー名は、クラスターの作成時に別の名前を指定しない限り、通常、HDInsight クラスターと同じ名前です。

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

**ワードカウント MapReduce ジョブを送信するには**

1. Visual Studio で、C# コンソール アプリケーションを作成します。
2. NuGet パッケージ マネージャー コンソールから、次のコマンドを実行します。

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

2. Program.cs ファイルで次の using ステートメントを使用します。

		using System;
		using System.Collections.Generic;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;

3. Main() 関数に次のコードを追加します。

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
	    HDInsightJobManagementClient _hdiJobManagementClient;

	    List<string> arguments = new List<string> { { "wasb:///example/data/gutenberg/davinci.txt" }, { "wasb:///example/data/WordCountOutput" } };
	
	    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
	    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
	
	    var parameters = new MapReduceJobSubmissionParameters
	    {
	        UserName = ExistingClusterUsername,
	        JarFile = "wasb:///example/jars/hadoop-mapreduce-examples.jar",
	        JarClass = "wordcount",
	        Arguments = ConvertArgsToString(arguments)
	    };
	
	    System.Console.WriteLine("Submitting the MapReduce job to the cluster...");
	    var response = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(parameters);
	    System.Console.WriteLine("Validating that the response is as expected...");
	    System.Console.WriteLine("Response status code is " + response.StatusCode);
	    System.Console.WriteLine("Validating the response object...");
	    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
	    Console.WriteLine("Press ENTER to continue ...");
	    Console.ReadLine();

4. 次のヘルパー関数をクラスに追加します。

        private static string ConvertArgsToString(List<string> args)
        {
            if (args.Count == 0)
            {
                return null;
            }

            return string.Join("&arg=", args.ToArray());
        }

5. **F5** キーを押してアプリケーションを実行します。










##HDInsight .NET SDK を使用して Hadoop ストリーミング ジョブを送信する
HDInsight クラスターには、C# で開発されたワード カウント Hadoop ストリーミング プログラムが付属します。Mapper プログラムは */example/apps/cat.exe* で、Reduce プログラムは */example/apps/wc.exe* です。ここでは、.NET アプリケーションを作成して、ワード カウント サンプルを実行する方法を説明します。

MapReduce ジョブを送信する .NET アプリケーションの作成方法の詳細については、「[HDInsight .NET SDK を使用して MapReduce ジョブを送信する](#mapreduce-sdk)」をご覧ください。

Hadoop ストリーミング ジョブの開発とデプロイ方法の詳細については、「[HDInsight 用 C# Hadoop ストリーミング プログラムの開発][hdinsight-develop-streaming-jobs]」をご覧ください。

次の手順は、Windows の HDInsight クラスターにのみ適用されます。現在のところ、Linux クラスターは C# ストリーミングに対応していません。ただし .NET プログラムを使用し、Linux クラスターが対応している他のプログラミング言語で記述されたストリーム ジョブを送信できます。たとえば、Python です。Python のストリーミング例は、「[HDInsight 用 Python ストリーミング プログラムの開発](hdinsight-hadoop-streaming-python.md)」をご覧ください。

**ワードカウント MapReduce ジョブを送信するには**

1. Visual Studio パッケージ マネージャー、コンソールから、次の Nuget コマンドを実行し、パッケージをインポートします。

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

2. Program.cs ファイルで次の using ステートメントを使用します。

		using System;
		using System.Collections.Generic;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;

3. Main() 関数に次のコードを追加します。

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";

        List<string> arguments = new List<string> { { "/example/apps/cat.exe" }, { "/example/apps/wc.exe" } };

        HDInsightJobManagementClient _hdiJobManagementClient;
        var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
        _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

        var parameters = new MapReduceStreamingJobSubmissionParameters
        {
            UserName = ExistingClusterUsername,
            File = ConvertArgsToString(arguments),
            Mapper = "cat.exe",
            Reducer = "wc.exe",
            Input = "/example/data/gutenberg/davinci.txt",
            Output = "/tutorials/wordcountstreaming/output"
        };

        System.Console.WriteLine("Submitting the MapReduce job to the cluster...");
        var response = _hdiJobManagementClient.JobManagement.SubmitMapReduceStreamingJob(parameters);
        System.Console.WriteLine("Validating that the response is as expected...");
        System.Console.WriteLine("Response status code is " + response.StatusCode);
        System.Console.WriteLine("Validating the response object...");
        System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
        Console.WriteLine("Press ENTER to continue ...");
        Console.ReadLine();

4. クラスにヘルプ関数を追加します。

        private static string ConvertArgsToString(List<string> args)
        {
            if (args.Count == 0)
            {
                return null;
            }

            return string.Join("&arg=", args.ToArray());
        }

5. **F5** キーを押してアプリケーションを実行します。

##HDInsight .NET SDK を使用して Hive ジョブを送信する
HDInsight クラスターには、*hivesampletable* という Hive テーブルのサンプルが付属します。ここでは、.NET アプリケーションを作成して Hive ジョブを実行して、HDInsight クラスター上に作成された Hive テーブルを一覧表示する方法を説明します。Hive の使い方の詳細については、「[HDInsight での Hive の使用][hdinsight-use-hive]」をご覧ください。

SDK を使用して HDInsight クラスターを作成するには、以下の手順が必要です。

- HDInsight .NET SDK のインストール
- コンソール アプリケーションの作成
- アプリケーションの実行


**HDInsight .NET SDK をインストールするには**、公開されている最新の SDK を [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) からインストールできます。次の手順で、具体的な方法を説明します。

**Visual Studio コンソール アプリケーションを作成するには**

1. Visual Studio 2013 または 2015 を開きます。

2. 次の設定を使用して、新しいプロジェクトを作成します。

	|プロパティ|値|
	|--------|-----|
	|テンプレート|テンプレート/Visual C#/Windows/コンソール アプリケーション|
	|名前|SubmitHiveJob|

3. **[ツール]** メニューで **[NuGet パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。
4. コンソールで次のコマンドを実行して、パッケージをインストールします。

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

	これらのコマンドは、.NET ライブラリおよび .NET ライブラリへの参照を現在の Visual Studio プロジェクトに追加します。

5. ソリューション エクスプローラーで、**Program.cs** をダブルクリックして開き、次のコードを貼り付け、変数の値を指定します。

		using System.Collections.Generic;
		using System.Linq;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
		
		namespace SubmitHiveJob
		{
		    class Program
		    {
		        private static HDInsightJobManagementClient _hdiJobManagementClient;
		
		        private const string ExistingClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		
		        private const string ExistingClusterUsername = "<HDINSIGHT HTTP USER NAME>";  //The default name is admin.
		        private const string ExistingClusterPassword = "<HDINSIGHT HTTP USER PASSWORD>";
		
		        private static void Main(string[] args)
		        {
		
		            var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
		            _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
		
		            SubmitHiveJob();
		        }
		
		        private static void SubmitHiveJob()
		        {
		            Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
		            List<string> args = new List<string> { { "argA" }, { "argB" } };
		            var parameters = new HiveJobSubmissionParameters
		            {
		                UserName = ExistingClusterUsername,
		                Query = "SHOW TABLES",
		                Defines = ConvertDefinesToString(defines),
		                Arguments = ConvertArgsToString(args)
		            };
		
		            System.Console.WriteLine("Submitting the Hive job to the cluster...");
		            var response = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
		            System.Console.WriteLine("Validating that the response is as expected...");
		            System.Console.WriteLine("Response status code is " + response.StatusCode);
		            System.Console.WriteLine("Validating the response object...");
		            System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		            System.Console.WriteLine("Press ENTER to continue ...");
		            System.Console.ReadLine();
		        }
		
		        private static string ConvertDefinesToString(Dictionary<string, string> defines)
		        {
		            if (defines.Count == 0)
		            {
		                return null;
		            }
		
		            return string.Join("&define=", defines.Select(x => x.Key + "%3D" + x.Value).ToArray());
		        }
		        private static string ConvertArgsToString(List<string> args)
		        {
		            if (args.Count == 0)
		            {
		                return null;
		            }
		
		            return string.Join("&arg=", args.ToArray());
		        }
		    }
		}

6. **F5** キーを押してアプリケーションを実行します。

##Visual Studio の HDInsight ツールを使用してジョブを送信する

Visual Studio の HDInsight ツールを使用して、Hive クエリと Pig スクリプトを実行できます。「[HDInsight Tools for Visual Studio を使用して Hive クエリを実行する](hdinsight-hadoop-visual-studio-tools-get-started.md)」を参照してください。


##次のステップ
この記事では、HDInsight クラスターを作成する方法をいくつか説明しました。詳細については、次の記事を参照してください。

* [Azure HDInsight の概要][hdinsight-get-started]
* [HDInsight で Hadoop クラスターを作成する][hdinsight-provision]
* [PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]
* [HDInsight コマンドレット リファレンス ドキュメント][hdinsight-powershell-reference]
* [HDInsight での Hive の使用][hdinsight-use-hive]
* [HDInsight での Pig の使用][hdinsight-use-pig]


[azure-certificate]: http://msdn.microsoft.com/library/windowsazure/gg551722.aspx
[azure-management-portal]: https://portal.azure.com/

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

<!---HONumber=Oct15_HO3-->