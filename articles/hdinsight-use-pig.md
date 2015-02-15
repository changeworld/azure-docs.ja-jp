<properties 
	pageTitle="HDInsight での Hadoop Pig の使用 | Azure" 
	description="HDInsight で Pig を使用する方法について説明します。Pig Latin ステートメントを記述してアプリケーション ログ ファイルを分析し、データに対してクエリを実行して分析用の出力を生成します。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="jgao"/>



# HDInsight の Hadoop での Pig の使用

このチュートリアルでは、HDInsight で [Apache Pig][apachepig-home] ジョブを実行して、大規模なデータ ファイルを分析する方法について説明します。Pig には、 *MapReduce*  ジョブを実行するためのスクリプト言語が備わっており、Java コードの代わりに使用できます。Pig のスクリプト言語を *Pig Latin* といいます。

**前提条件**

* Azure HDInsight クラスターのプロビジョニングを終えている必要があります。手順については、[Azure HDInsight の概要][hdinsight-get-started]または[HDInsight クラスターのプロビジョニング][hdinsight-provision]を参照してください。チュートリアルを完了するために、クラスター名が必要となります。

* Azure PowerShell をコンピューターにインストールし構成しておく必要があります。手順については、[Azure PowerShell のインストールおよび構成方法][powershell-install-configure]を参照してください。 

**推定所要時間:** 30 分

## この記事の内容

* [Pig を使用する理由](#usage)
* [このチュートリアルで実行する内容](#what)
* [分析するデータの特定](#data)
* [Pig Latin について](#understand)
* [PowerShell を使用して Pig ジョブを送信する](#powershell)
* [HDInsight .NET SDK を使用して Pig ジョブを送信する](#sdk)
* [次のステップ](#nextsteps)
 
## <a id="usage"></a>Pig を使用する理由
リレーショナル データベースや統計/視覚化パッケージではビッグ データを適切に処理できません。こうした膨大なデータを実用的な速度で処理するには、数十台、数百台、あるいは数千台のサーバー上でソフトウェアを同時に実行する必要があります。Hadoop は、膨大な構造化データと非構造化データを処理するアプリケーションを記述するための *MapReduce*  フレームワークです。クラスター化された多数のコンピューターでこれらのデータを並列処理することで、高い信頼性と耐障害性を実現します。

![HDI.Pig.Architecture][image-hdi-pig-architecture]

[Apache *Pig*][apachepig-home] は、Java ベースの MapReduce フレームワーク上に抽象化レイヤーを提供しており、ユーザーはそのレイヤーを使用してデータを分析できます。Java や MapReduce の知識は必要ありません。Pig は、 *Pig Latin* と呼ばれる使いやすいデータフロー言語を使用して大規模なデータ セットを分析するためのプラットフォームです。Pig によって、mapper プログラムと reducer プログラムの記述に要する時間を短縮できます。Java の知識は必要ありません。Java コードと Pig を組み合わせることもできます。多くの複雑なアルゴリズムを、人間による判読が可能な数行の Pig コードで記述できます。 

Pig Latin ステートメントは一般的なフローに従います。   

- **Load**:操作対象のデータをファイル システムから読み込みます。
- **Transform**:データを操作します。 
- **Dump または store**:データを画面に出力します。または、処理できるように保存します。

Pig Latin の詳細については、[Pig Latin Reference Manual 1][piglatin-manual-1] および[Pig Latin Reference Manual 2][piglatin-manual-2]を参照してください。

## <a id="what"></a>このチュートリアルで実行する内容
このチュートリアルでは、Apache ログ ファイル (*sample.log*) を分析して、INFO、DEBUG、TRACE などの異なるログ レベルの件数を判別します。この記事で完了する内容を次の 2 つの図で視覚的に示します。最初の図は、sample.log ファイルのスニペットです。

![Whole File Sample][image-hdi-log4j-sample]

2 番目の図は、スクリプト内の Pig コードの行に従って実行されるデータのフローと変換を示してます。

![HDI.PIG.Data.Transformation][image-hdi-pig-data-transformation]

このチュートリアルで作成する Pig ジョブは同じフローを辿ります。

## <a id="data"></a>分析するデータの特定

HDInsight は、Hadoop クラスター対応の既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。クラスター プロビジョニングの一部としていくつかのサンプル データ ファイルが BLOB ストレージに追加されています。これらのサンプル データ ファイルを使用して、クラスター上で Hive クエリを実行できます。必要な場合は、クラスターに関連付けられた BLOB ストレージ アカウントにユーザー専用のデータ ファイルをアップロードすることもできます。手順については、[HDInsight でのデータのアップロード][hdinsight-upload-data] を参照してください。HDInsight と共に Azure BLOB ストレージを使用する方法の詳細については、[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage] を参照してください。

BLOB ストレージ内のファイルにアクセスするための構文は次のとおりです。

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE] HDInsight クラスター バージョン 3.0 では、 *wasb://*  構文のみがサポートされます。旧バージョンの  *asv://*  構文は、HDInsight 2.1 および 1.6 クラスターではサポートされますが、HDInsight 3.0 クラスターではサポートされず、以降のバージョンでもサポートされません。

既定のファイル システム コンテナーに格納されているファイルは、次の URI のどれを使用しても HDInsight からアクセスできます (例として sample.log を使用しています。  このファイルは、このチュートリアルで使用されるデータ ファイルです)。

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
	wasb:///example/data/sample.log
	/example/data/sample.log

ストレージ アカウントから直接ファイルにアクセスする場合、ファイルの BLOB 名は次のようになります。

	example/data/sample.log

この記事では  *log4j* サンプル ファイルを使用します。このファイルは、HDInsight クラスターに付属しており、*\example\data\sample.log* に格納されています。実際のデータ ファイルのアップロードについては、[HDInsight でのデータのアップロード][hdinsight-upload-data]を参照してください。




## <a id="understand"></a> Pig Latin について

ここでは、いくつかの Pig Latin ステートメントと、その実行結果を確認します。次のセクションでは、PowerShell を使用して Pig ステートメントを一緒に実行し、サンプル ログ ファイルを分析します。HDInsight クラスター上で個々の Pig Latin ステートメントを直接実行する必要があります。

1. [RDP を使用した HDInsight クラスターへの接続](http://azure.microsoft.com/ja-jp/documentation/articles/hdinsight-administer-use-management-portal/#rdp)の手順に従って、HDInsight クラスターのリモート デスクトップを有効にします。クラスター ノードにログインし、デスクトップから、**[Hadoop コマンド ライン]** をクリックします。

2. コマンド ラインから、**Pig** がインストールされているディレクトリに移動します。次のコマンドを入力します。

		C:\apps\dist\hadoop-<version>> cd %pig_home%\bin

3. コマンド プロンプトで、 *pig* と入力し、Enter キーを押して *grunt* シェルに切り替えます。

		C:\apps\dist\pig-<version>\bin>pig
		...
		grunt>  

4. ファイル システムでサンプル ファイルからデータを読み込むため、以下を入力し、結果を表示します。 

		grunt> LOGS = LOAD 'wasb:///example/data/sample.log';
		grunt> DUMP LOGS;
	
	The output is similar to the following:
	
		(2012-02-05 19:23:50 SampleClass5 [TRACE] verbose detail for id 313393809)
		(2012-02-05 19:23:50 SampleClass6 [DEBUG] detail for id 536603383)
		(2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail for id 564842645)
		(2012-02-05 19:23:50 SampleClass8 [TRACE] verbose detail for id 1929822199)
		(2012-02-05 19:23:50 SampleClass5 [DEBUG] detail for id 1599724386)
		(2012-02-05 19:23:50 SampleClass0 [INFO] everything normal for id 2047808796)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1774407365)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 2099982986)
		(2012-02-05 19:23:50 SampleClass4 [DEBUG] detail for id 180683124)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1072988373)
		(2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail)
		...

5. データ ファイルの各行を調べ、6 つのログ レベルと照合します。

		grunt> LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
 
6. 一致しない行を除去し、結果を表示します。これで、空の行が除去されます。

		grunt> FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		grunt> DUMP FILTEREDLEVELS;

	次のように出力されます。

		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		...

7. ログ レベルのすべてをそれぞれ独自の行にまとめ、結果を表示します。

		grunt> GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		grunt> DUMP GROUPEDLEVELS;

	次のように出力されます。

		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE), ...


8. グループごとにログ レベルの出現回数をカウントし、結果を表示します。

		grunt> FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		grunt> DUMP FREQUENCIES;
 
	次のように出力されます。

		(INFO,96)
		(WARN,11)
		(DEBUG,434)
		(ERROR,6)
		(FATAL,2)
		(TRACE,816)

9. 頻度を降順で並べ替え、結果を表示します。

		grunt> RESULT = order FREQUENCIES by COUNT desc;
		grunt> DUMP RESULT;   

	次のように出力されます。 

		(TRACE,816)
		(DEBUG,434)
		(INFO,96)
		(WARN,11)
	 	(ERROR,6)
		(FATAL,2)

## <a id="powershell"></a>PowerShell を使用して Pig ジョブを送信する

ここでは PowerShell コマンドレットの使用手順を示します。このセクションを読み進める前に、まずローカル環境をセットアップし、Azure への接続を構成する必要があります。詳細については、[Azure HDInsight の概要][hdinsight-get-started]および[PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell] を参照してください。


**PowerShell を使用して Pig Latin を実行するには**

1. Windows PowerShell ISE を開きます。Windows 8 のスタート画面で、**PowerShell_ISE**と入力し、**Windows PowerShell ISE** をクリックします。[Windows 8 と Windows での Windows PowerShell の起動][powershell-start]を参照してください。

2. 下のウィンドウで、次のコマンドを実行して、Azure サブスクリプションに接続します。

		Add-AzureAccount

	Azure アカウント資格情報の入力を求められます。サブスクリプション接続を追加するこの方法はタイム アウトし、12 時間後には、このコマンドレットを再度実行する必要があります。 

	> [AZURE.NOTE] Azure サブスクリプションが複数あり、使用するサブスクリプションが既定のサブスクリプションではない場合は、 <strong>Select-AzureSubscription</strong> コマンドレットを使用して現在のサブスクリプションを選択します。
2. スクリプト ウィンドウで、次のコードをコピーして貼り付けます。

		$clusterName = "<HDInsightClusterName>" 	#Specify the cluster name
		$statusFolder = "/tutorials/usepig/status"	#Specify the folder to dump results

	指定したステータス フォルダーが存在しない場合は、スクリプトによって作成されます。 

3. スクリプト ウィンドウで次のコードを追加します。このコードによって、Pig Latin クエリ文字列を定義し、Pig ジョブの定義を作成します。

		# Create the Pig job definition
		$0 = '$0';
		$QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;" 
		
		$pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString -StatusFolder $statusFolder 

	**-File** スイッチを使用して、HDFS 上の Pig スクリプト ファイルを指定することもできます。**-StatusFolder** スイッチを使用すると、標準エラー ログと標準出力ファイルがそのフォルダーに作成されます。

4. Pig ジョブを送信するための、次のコードを追加します。
		
		# Submit the Pig job
		Write-Host "Submit the Pig job ..." -ForegroundColor Green
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition  

5. Pig ジョブの完了を待つための、次のコードを追加します。		

		# Wait for the Pig job to complete
		Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

6. Pig ジョブの出力を表示するための、次のコードを追加します。
		
		# Print the standard error and the standard output of the Pig job.
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

	> [AZURE.NOTE] 次に示すスクリーンショットでは、出力を短縮するために、Get-AzureHDInsightJobOut コマンドレットの 1 つがコメント アウトされています。   

7. **F5** キーを押して、スクリプトを実行します。

	![HDI.Pig.PowerShell][image-hdi-pig-powershell]

	Pig ジョブがログの種類の出現回数を計算します。


## <a id="sdk"></a>HDInsight .NET SDK を使用して Pig ジョブを送信する

C# アプリケーションを使用して Pig ジョブを送信するには、以下の手順に従います。   Hadoop ジョブの送信用の C# アプリケーションを作成する方法については、[プログラムによる Hadoop ジョブの送信][hdinsight-submit-jobs]を参照してください。

1. 自己署名証明書を作成し、それをコンピューターにインストールして、さらに、Azure サブスクリプションにアップロードします。手順については、「[自己署名証明書の作成](http://go.microsoft.com/fwlink/?LinkId=511138)」を参照してください。

2. Visual Studio コンソール アプリケーションを作成し、HDInsight パッケージをインストールします。[ツール] メニューで、**[Nuget パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。プロンプトで、以下を入力します。

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

3. Program.cs をダブルクリックして、次のコードを貼り付け、Pig ジョブを送信します。変数に値を入力します。

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
		
		namespace SubmitPigJobs
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
					// Set the variables
					string subscriptionID = "<Azure subscription ID>";
					string certFriendlyName = "<certificate friendly name>";
			
					string clusterName = "<HDInsight cluster name>";
		            string statusFolderName = @"/tutorials/usepig/status";
		
		            string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;";
		
		            // Define the Pig job
		            PigJobCreateParameters myJobDefinition = new PigJobCreateParameters()
		            {
		                Query = queryString,
		                StatusFolder = statusFolderName
		            };
		
		            // Get the certificate object from certificate store using the friendly name to identify it
		            X509Store store = new X509Store();
		            store.Open(OpenFlags.ReadOnly);
		            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
		
		            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
		            // Create a hadoop client to connect to HDInsight
		            var jobClient = JobSubmissionClientFactory.Connect(creds);
		
		            // Run the MapReduce job
		            Console.WriteLine("----- Submit the Pig job ...");
		            JobCreationResults mrJobResults = jobClient.CreatePigJob(myJobDefinition);
		
		            // Wait for the job to complete
		            Console.WriteLine("----- Wait for the Pig job to complete ...");
		            WaitForJobCompletion(mrJobResults, jobClient);
		
		            // Display the error log
		            Console.WriteLine("----- The Pig job error log.");
		            using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
		            {
		                var reader = new StreamReader(stream);
		                Console.WriteLine(reader.ReadToEnd());
		            }
		
		            // Display the output log
		            Console.WriteLine("----- The Pig job output log.");
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

## <a id="nextsteps"></a>次のステップ

Pig ではデータを分析できますが、HDInsight には便利なその他の言語も用意されています。Hive は SQL に似たクエリ言語であり、HDInsight に保存されているデータに対して簡単にクエリを実行できます。また、Java で記述された MapReduce ジョブでは複雑なデータ分析を実行できます。詳細については、次のトピックを参照してください。


* [Azure の HDInsight の概要][hdinsight-get-started]
* [データを HDInsight へアップロード][hdinsight-upload-data]
* [プログラムによる Hadoop ジョブの送信][hdinsight-submit-jobs]
* [HDInsight での Hive の使用][hdinsight-use-hive]



[piglatin-manual-1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
[piglatin-manual-2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
[apachepig-home]: http://pig.apache.org/


[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-use-hive]: ../hdinsight-use-hive/

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/#mapreduce-sdk

[Powershell-install-configure]: ../install-configure-powershell/

[powershell-start]: http://technet.microsoft.com/ja-jp/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png


<!--HONumber=42-->
