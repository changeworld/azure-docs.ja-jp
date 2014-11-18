<properties urlDisplayName="HDInsight Administration" pageTitle="HDInsight での Hadoop ジョブの送信 | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure, hive, mapreduce, HDInsight .NET SDK, powershell, submit mapreduce jobs, submit hive jobs, development, hadoop, apache" description="Hadoop ジョブを Azure HDInsight Hadoop へ送信する方法について説明します。" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="HDInsight での Hadoop ジョブの送信" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# HDInsight での Hadoop ジョブの送信

この記事では、PowerShell と HDInsight .NET SDK を使用して MapReduce ジョブと Hive ジョブを送信する方法について説明します。

**前提条件:**

この記事を読み始める前に、次の項目を用意する必要があります。

-   Azure HDInsight クラスター。手順については、「[Azure HDInsight の概要][Azure HDInsight の概要]」または「[HDInsight クラスターのプロビジョニング][HDInsight クラスターのプロビジョニング]」を参照してください。
-   Azure PowerShell のインストールおよび構成。手順については、[Azure PowerShell のインストールおよび構成に関するページ][Azure PowerShell のインストールおよび構成に関するページ]を参照してください。

## この記事の内容

-   [PowerShell を使用して MapReduce ジョブを送信する][PowerShell を使用して MapReduce ジョブを送信する]
-   [PowerShell を使用して Hive ジョブを送信する][PowerShell を使用して Hive ジョブを送信する]
-   [PowerShell を使用して Sqoop ジョブを送信する][PowerShell を使用して Sqoop ジョブを送信する]
-   [HDInsight .NET SDK を使用して MapReduce ジョブを送信する][HDInsight .NET SDK を使用して MapReduce ジョブを送信する]
-   [HDInsight .NET SDK を使用して Hadoop ストリーミング MapReduce ジョブを送信する][HDInsight .NET SDK を使用して Hadoop ストリーミング MapReduce ジョブを送信する]
-   [HDInsight .NET SDK を使用して Hive ジョブを送信する][HDInsight .NET SDK を使用して Hive ジョブを送信する]
-   [次のステップ][次のステップ]

## <span id="mapreduce-powershell"></span></a> PowerShell を使用して MapReduce ジョブを送信する

Azure PowerShell は、Azure のワークロードの展開と管理を制御し自動化するために使用できる強力なスクリプティング環境です。HDInsight で PowerShell を使用する方法の詳細については、「[PowerShell を使用した HDInsight の管理][PowerShell を使用した HDInsight の管理]」を参照してください。

Hadoop MapReduce は、膨大なデータを処理するアプリケーションを記述するためのソフトウェア フレームワークです。HDInsight クラスターには jar ファイル (*\\example\\jars\\hadoop-examples.jar*) が付属していて、MapReduce サンプルがいくつか格納されています。Version 3.0 の HDInsight クラスターで、このファイルは hadoop-mapreduce-examples.jar という名前に変更されましたサンプルの 1 つは、ソース ファイルに出現する単語の頻度を算出します。ここでは、コンピューターから PowerShell を使用して、ワード カウント サンプルを実行する方法を説明します。MapReduce ジョブの開発と実行の詳細については、「[HDInsight での MapReduce の使用][HDInsight での MapReduce の使用]」を参照してください。

**PowerShell を使用してワード カウント MapReduce プログラムを実行するには**

1.  **Azure PowerShell** を開きます。Azure PowerShell コンソール ウィンドウを開く手順については、「[Azure PowerShell のインストールおよび構成][Azure PowerShell のインストールおよび構成に関するページ]」を参照してください。

2.  次の PowerShell コマンドを実行して、この 2 つの変数を設定します。

        $subscriptionName = "<SubscriptionName>"   
        $clusterName = "<HDInsightClusterName>"    

    サブスクリプションは、HDInsight クラスターの作成時に使用したサブスクリプションです。HDInsight クラスターは、MapReduce ジョブの実行に使用するクラスターです。

3.  次のコマンドを実行して、MapReduce ジョブ定義を作成します。

        # Define the word count MapReduce job
        $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    引数が 2 つあります。最初の引数はソース ファイル名で、2 つ目の引数は出力ファイル パスです。wasb プレフィックスの詳細については、「[HDInsight での Azure BLOB ストレージの使用][HDInsight での Azure BLOB ストレージの使用]」を参照してください。

4.  次のコマンドを実行して、MapReduce ジョブを実行します。

        # Submit the MapReduce job
        Select-AzureSubscription $subscriptionName
        $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 

    MapReduce ジョブ定義に加えて、MapReduce ジョブを実行する HDInsight クラスター名も指定します。

5.  次のコマンドを実行して、MapReduce ジョブの完了を確認します。

        # Wait for the job to complete
        Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

6.  次のコマンドを実行して、MapReduce ジョブの実行中に発生したエラーを確認します。

        # Get the job standard error output
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 

    次のスクリーンショットは、正常実行時の出力を示しています。正常でない場合は、エラー メッセージが表示されます。

    ![HDI.GettingStarted.RunMRJob][HDI.GettingStarted.RunMRJob]

**MapReduce ジョブの結果を取得するには**

1.  **Azure PowerShell** を開きます。
2.  次の PowerShell コマンドを実行して、この 3 つの変数を設定します。

        $subscriptionName = "<SubscriptionName>"       
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<ContainerName>"          

    Azure のストレージ アカウントは、HDInsight クラスターのプロビジョニング時に指定したアカウントです。ストレージ アカウントは、既定の HDInsight クラスター ファイル システムとして使用する BLOB コンテナーをホストするために使用されます。BLOB ストレージ コンテナー名は、クラスターのプロビジョニング時に別の名前を指定しない限り、通常、HDInsight クラスターと同じ名前です。

3.  次のコマンドを実行して、Azure Storage のコンテキスト オブジェクトを作成します。

        # Create the storage account context object
        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Select-AzureSubscription は、サブスクリプションが複数あり、使用するサブスクリプションが既定のサブスクリプションではない場合に備えて、現在のサブスクリプションを設定するために使用されます。

4.  次のコマンドを実行して、MapReduce ジョブの出力を BLOB コンテナーからコンピューターにダウンロードします。

        # Get the blob content
        Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    *example/data/WordCountOutput* フォルダーは、MapReduce ジョブの実行時に指定した出力フォルダーです。*part-r-00000* は MapReduce ジョブの出力の既定のファイル名です。ファイルはフォルダー構造を保ったままローカル フォルダーにダウンロードされます。たとえば、次のスクリーンショットでは、現在のフォルダーが C ドライブのルート フォルダーです。ファイルは C:\\example\\data\\WordCountOutput フォルダーにダウンロードされます。

5.  次のコマンドを実行して、MapReduce ジョブの出力ファイルの内容を表示します。

        cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    ![HDI.GettingStarted.MRJobOutput][HDI.GettingStarted.MRJobOutput]

    MapReduce ジョブは、単語と出現回数が記録された *part-r-00000* という名前のファイルを作成します。スクリプトでは findstr コマンドを使用して、"there" を含む単語をすべて表示しています。

> [WACOM.NOTE] MapReduce ジョブで出力された複数行を含む ./example/data/WordCountOutput/part-r-00000 ファイルをメモ帳で開いた場合、改行が正しく表示されません。これは予期されることです。

## <span id="hive-powershell"></span></a> PowerShell を使用して Hive ジョブを送信する

Apache [hdinsight-use-hive][hdinsight-use-hive] では、*HiveQL* と呼ばれる SQL に似たスクリプト言語を使用して MapReduce ジョブを実行します。大規模なデータの集約、照会、分析でも Hive を利用できます。

HDInsight クラスターには、*hivesampletable* という Hive テーブルのサンプルが付属します。ここでは、PowerShell を使用して Hive ジョブを実行して、Hive テーブルのデータを表示する方法を説明します。

**PowerShell を使用して Hive ジョブを実行するには**

1.  **Azure PowerShell** を開きます。Azure PowerShell コンソール ウィンドウを開く手順については、[Azure PowerShell のインストールと構成に関するページ][Azure PowerShell のインストールおよび構成に関するページ]を参照してください。

2.  次のコマンドで最初の 2 つの変数を設定して、コマンドを実行します。

        $subscriptionName = "<SubscriptionName>"   
        $clusterName = "<HDInsightClusterName>"             
        $querystring = "SELECT * FROM hivesampletable WHERE Country='United Kingdom';"

    $querystring は HiveQL クエリです。

3.  次のコマンドを実行して、Hive ジョブを実行する Azure サブスクリプションとクラスターを選択します。

        Select-AzureSubscription -SubscriptionName $subscriptionName

4.  Hive ジョブを送信します。

        Use-AzureHDInsightCluster $clusterName
        Invoke-Hive -Query $queryString

    -File スイッチを使用して、HDFS 上の HiveQL スクリプト ファイルを指定できます。

Hive の詳細については、「[HDInsight での Hive の使用][HDInsight での Hive の使用]」を参照してください。

## <span id="sqoop-powershell"></span></a>PowerShell を使用して Sqoop ジョブを送信する

「[Hadoop .NET SDK と HDInsight の使用][Hadoop .NET SDK と HDInsight の使用]」を参照してください。

## <span id="mapreduce-sdk"></span></a> HDInsight .NET SDK を使用して MapReduce ジョブを送信する

HDInsight .NET SDK は、.NET から HDInsight クラスターを簡単に操作できる .NET クライアント ライブラリを提供します。HDInsight クラスターには jar ファイル (*\\example\\jars\\hadoop-examples.jar*) が付属していて、MapReduce サンプルがいくつか格納されています。サンプルの 1 つは、ソース ファイルに出現する単語の頻度を算出します。ここでは、.NET アプリケーションを作成して、ワード カウント サンプルを実行する方法を説明します。MapReduce ジョブの開発と実行の詳細については、「[HDInsight での MapReduce の使用][HDInsight での MapReduce の使用]」を参照してください。

SDK を使用して HDInsight クラスターをプロビジョニングするには、以下の手順が必要です。

-   HDInsight .NET SDK のインストール
-   コンソール アプリケーションの作成
-   アプリケーションの実行

**HDInsight .NET SDK をインストールするには**
、公開されている最新の SDK を [NuGet][NuGet] からインストールできます。次の手順で、具体的な方法を説明します。

**Visual Studio コンソール アプリケーションを作成するには**

1.  Visual Studio 2012 を開きます。

2.  [ファイル] メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。

3.  [新しいプロジェクト] で、次の値を入力または選択します。

    | プロパティ   | 値                              |
    |--------------|---------------------------------|
    | カテゴリ     | テンプレート/Visual C\#/Windows |
    | テンプレート | コンソール アプリケーション     |
    | 名前         | SubmitMapReduceJob              |

4.  **[OK]** をクリックしてプロジェクトを作成します。

5.  **[ツール]** メニューで **[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。

6.  コンソールで次のコマンドを実行して、パッケージをインストールします。

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

    このコマンドは、.NET ライブラリおよび .NET ライブラリへの参照を現在の Visual Studio プロジェクトに追加します。バージョンは 0.11.0.1 またはそれ以降になります。

7.  ソリューション エクスプローラーで **Program.cs** をダブルクリックして、このファイルを開きます。

8.  次の using ステートメントをファイルの先頭に追加します。

        using System.IO;
        using System.Threading;
        using System.Security.Cryptography.X509Certificates;

        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

        using Microsoft.WindowsAzure.Management.HDInsight;
        using Microsoft.Hadoop.Client;

9.  次の関数定義をクラスに追加します。この関数は Hadoop ジョブの完了を待機するために使用されます。

        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
        {
            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }

10. Main() 関数で、次のコードをコピーしてファイルに貼り付けます。

        // Set the variables
        string subscriptionID = "<Azure subscription ID>";
        string certFriendlyName = "<certificate friendly name>";

        string clusterName = "<HDInsight cluster name>";

        string storageAccountName = "<Azure storage account name>";
        string storageAccountKey = "<Azure storage account key>";
        string containerName = "<Blob container name>";

    これがプログラムで設定する必要のある変数のすべてです。Azure のサブスクリプション名は [Azure の管理ポータル][Azure の管理ポータル]で確認できます。

    証明書については、「[Create and Upload a Management Certificate for Azure (Azure の管理証明書の作成とアップロード)][Create and Upload a Management Certificate for Azure (Azure の管理証明書の作成とアップロード)]」を参照してください。証明書を構成する簡単な方法は、PowerShell コマンドレットの *Get-AzurePublishSettingsFile* および *Import-AzurePublishSettingsFile* を実行することです。管理証明書が自動的に作成されアップロードされます。この PowerShell コマンドレットを実行した後、ワークステーションから *certmgr.msc* を開いて *[個人]、[証明書]* の順に展開すると、証明書が表示されます。PowerShell コマンドレットによって作成された証明書は、*[発行先]* と *[発行元]* の両方が *[Azure Tools]* になります。

    Azure のストレージ アカウント名は、HDInsight クラスターのプロビジョニング時に指定するアカウントです。既定のコンテナー名は、HDInsight クラスター名と同じです。

11. Main() 関数の最後に次のコードをコピーして、MapReduce ジョブを定義します。

        // Define the MapReduce job
        MapReduceJobCreateParameters mrJobDefinition = new MapReduceJobCreateParameters()
        {
            JarFile = "wasb:///example/jars/hadoop-examples.jar",
            ClassName = "wordcount"
        };

        mrJobDefinition.Arguments.Add("wasb:///example/data/gutenberg/davinci.txt");
        mrJobDefinition.Arguments.Add("wasb:///example/data/WordCountOutput");

    引数が 2 つあります。最初の引数はソース ファイル名で、2 つ目の引数は出力ファイル パスです。wasb プレフィックスの詳細については、「[HDInsight での Azure BLOB ストレージの使用][HDInsight での Azure BLOB ストレージの使用]」を参照してください。

12. Main() 関数の最後に次のコードをコピーして、JobSubmissionCertificateCredential オブジェクトを作成します。

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

13. Main() 関数の最後に次のコードをコピーして、ジョブを実行し、ジョブの完了を待ちます。

        // Create a hadoop client to connect to HDInsight
        var jobClient = JobSubmissionClientFactory.Connect(creds);

        // Run the MapReduce job
        JobCreationResults mrJobResults = jobClient.CreateMapReduceJob(mrJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(mrJobResults, jobClient);

14. Main() 関数の最後に次のコードをコピーして、MapReduce ジョブの出力を表示します。

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

    出力フォルダーは MapReduce ジョブの定義時に指定します。既定のファイル名は *part-r-00000* です。

**アプリケーションを実行するには**

アプリケーションを Visual Studio で開いている間に、**F5** キーを押してアプリケーションを実行します。コンソール ウィンドウが開き、アプリケーションの状態とアプリケーションの出力が表示されます。

## <span id="streaming-sdk"></span></a>HDInsight .NET SDK を使用して Hadoop ストリーミング ジョブを送信する

HDInsight クラスターには、C\# で開発されたワード カウント Hadoop ストリーミング プログラムが付属します。Mapper プログラムは */example/apps/cat.exe* で、Reduce プログラムは */example/apps/wc.exe* です。ここでは、.NET アプリケーションを作成して、ワード カウント サンプルを実行する方法を説明します。

MapReduce ジョブを送信する .NET アプリケーションの作成方法の詳細については、「[HDInsight .NET SDK を使用して MapReduce ジョブを送信する][HDInsight .NET SDK を使用して MapReduce ジョブを送信する]」を参照してください。

Hadoop ストリーミング ジョブの開発およびデプロイ方法の詳細については、「[Develop C\# Hadoop streaming programs for HDInsight (HDInsight 用 C\# Hadoop ストリーミング プログラムの開発)][Develop C\# Hadoop streaming programs for HDInsight (HDInsight 用 C\# Hadoop ストリーミング プログラムの開発)]」を参照してください。

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

## <span id="hive-sdk"></span></a> HDInsight .NET SDK を使用して Hive ジョブを送信する

HDInsight クラスターには、*hivesampletable* という Hive テーブルのサンプルが付属します。ここでは、.NET アプリケーションを作成して Hive ジョブを実行して、HDInsight クラスター上に作成された Hive テーブルを一覧表示する方法を説明します。Hive の使用法の詳細については、「[HDInsight での Hive の使用][HDInsight での Hive の使用]」を参照してください。

SDK を使用して HDInsight クラスターをプロビジョニングするには、以下の手順が必要です。

-   HDInsight .NET SDK のインストール
-   コンソール アプリケーションの作成
-   アプリケーションの実行

**HDInsight .NET SDK をインストールするには**
、公開されている最新の SDK を [NuGet][NuGet] からインストールできます。次の手順で、具体的な方法を説明します。

**Visual Studio コンソール アプリケーションを作成するには**

1.  Visual Studio 2012 を開きます。

2.  [ファイル] メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。

3.  [新しいプロジェクト] で、次の値を入力または選択します。

    | プロパティ   | 値                              |
    |--------------|---------------------------------|
    | カテゴリ     | テンプレート/Visual C\#/Windows |
    | テンプレート | コンソール アプリケーション     |
    | 名前         | SubmitHiveJob                   |

4.  **[OK]** をクリックしてプロジェクトを作成します。

5.  **[ツール]** メニューで **[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。

6.  コンソールで次のコマンドを実行して、パッケージをインストールします。

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

    このコマンドは、.NET ライブラリおよび .NET ライブラリへの参照を現在の Visual Studio プロジェクトに追加します。

7.  ソリューション エクスプローラーで **Program.cs** をダブルクリックして、このファイルを開きます。

8.  次の using ステートメントをファイルの先頭に追加します。

        using System.IO;
        using System.Threading;
        using System.Security.Cryptography.X509Certificates;

        using Microsoft.WindowsAzure.Management.HDInsight;
        using Microsoft.Hadoop.Client;

9.  次の関数定義をクラスに追加します。この関数は Hadoop ジョブの完了を待機するために使用されます。

        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
        {
            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
            {
                jobInProgress = client.GetJob(jobInProgress.JobId);
                Thread.Sleep(TimeSpan.FromSeconds(10));
            }
        }

10. Main() 関数で、次のコードをコピーしてファイルに貼り付けます。

        // Set the variables
        string subscriptionID = "<Azure subscription ID>";
        string clusterName = "<HDInsight cluster name>";
        string certFriendlyName = "<certificate friendly name>";        

    これがプログラムで設定する必要のある変数のすべてです。Azure のサブスクリプション ID はシステム管理者から入手できます。

    証明書については、「[Create and Upload a Management Certificate for Azure (Azure の管理証明書の作成とアップロード)][Create and Upload a Management Certificate for Azure (Azure の管理証明書の作成とアップロード)]」を参照してください。証明書を構成する簡単な方法は、PowerShell コマンドレットの *Get-AzurePublishSettingsFile* および *Import-AzurePublishSettingsFile* を実行することです。管理証明書が自動的に作成されアップロードされます。この PowerShell コマンドレットを実行した後、ワークステーションから *certmgr.msc* を開いて *[個人]、[証明書]* の順に展開すると、証明書が表示されます。PowerShell コマンドレットによって作成された証明書は、*[発行先]* と *[発行元]* の両方が *[Azure Tools]* になります。

11. Main() 関数の最後に次のコードをコピーして、Hive ジョブを定義します。

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            Query = "show tables;"
        };

    File パラメーターを使用して、HDFS 上の HiveQL スクリプト ファイルを指定することもできます。たとえば、次のように入力します。

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            File = "/user/admin/showtables.hql"
        };

12. Main() 関数の最後に次のコードをコピーして、JobSubmissionCertificateCredential オブジェクトを作成します。

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

13. Main() 関数の最後に次のコードをコピーして、ジョブを実行し、ジョブの完了を待ちます。

        // Submit the Hive job
        var jobClient = JobSubmissionClientFactory.Connect(creds);
        JobCreationResults jobResults = jobClient.CreateHiveJob(hiveJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(jobResults, jobClient);

14. Main() 関数の最後に次のコードをコピーして、Hive ジョブの出力を表示します。

        // Print the Hive job output
        System.IO.Stream stream = jobClient.GetJobOutput(jobResults.JobId);

        StreamReader reader = new StreamReader(stream);
        Console.WriteLine(reader.ReadToEnd());

        Console.WriteLine("Press ENTER to continue.");
        Console.ReadLine();

**アプリケーションを実行するには**

アプリケーションを Visual Studio で開いている間に、**F5** キーを押してアプリケーションを実行します。コンソール ウィンドウが開き、アプリケーションの状態が表示されます。出力は次のようになります。

    hivesampletable

## <span id="nextsteps"></span></a>次のステップ

この記事では、HDInsight クラスターをプロビジョニングする方法をいくつか説明しました。詳細については、次の記事を参照してください。

-   [Azure HDInsight の概要][Azure HDInsight の概要]
-   [HDInsight クラスターのプロビジョニング][HDInsight クラスターのプロビジョニング]
-   [PowerShell を使用した HDInsight の管理][PowerShell を使用した HDInsight の管理]
-   [HDInsight Cmdlet Reference Documentation (HDInsight コマンドレット リファレンス ドキュメント)][HDInsight Cmdlet Reference Documentation (HDInsight コマンドレット リファレンス ドキュメント)]
-   [HDInsight での Hive の使用][HDInsight での Hive の使用]
-   [HDInsight での Pig の使用][HDInsight での Pig の使用]

  [Azure HDInsight の概要]: ../hdinsight-get-started/
  [HDInsight クラスターのプロビジョニング]: ../hdinsight-provision-clusters/
  [Azure PowerShell のインストールおよび構成に関するページ]: ../install-configure-powershell/
  [PowerShell を使用して MapReduce ジョブを送信する]: #mapreduce-powershell
  [PowerShell を使用して Hive ジョブを送信する]: #hive-powershell
  [PowerShell を使用して Sqoop ジョブを送信する]: #sqoop-powershell
  [HDInsight .NET SDK を使用して MapReduce ジョブを送信する]: #mapreduce-sdk
  [HDInsight .NET SDK を使用して Hadoop ストリーミング MapReduce ジョブを送信する]: #streaming-sdk
  [HDInsight .NET SDK を使用して Hive ジョブを送信する]: #hive-sdk
  [次のステップ]: #nextsteps
  [PowerShell を使用した HDInsight の管理]: ../hdinsight-administer-use-powershell/
  [HDInsight での MapReduce の使用]: ../hdinsight-use-mapreduce/
  [HDInsight での Azure BLOB ストレージの使用]: ../hdinsight-use-blob-storage/
  [HDI.GettingStarted.RunMRJob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png
  [HDI.GettingStarted.MRJobOutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png
  [hdinsight-use-hive]: http://hive.apache.org/
  [HDInsight での Hive の使用]: ../hdinsight-use-hive/
  [Hadoop .NET SDK と HDInsight の使用]: ../hdinsight-use-sqoop/
  [NuGet]: http://nuget.codeplex.com/wikipage?title=Getting%20Started
  [Azure の管理ポータル]: http://manage.windowsazure.com/
  [Create and Upload a Management Certificate for Azure (Azure の管理証明書の作成とアップロード)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg551722.aspx
  [Develop C\# Hadoop streaming programs for HDInsight (HDInsight 用 C\# Hadoop ストリーミング プログラムの開発)]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [HDInsight Cmdlet Reference Documentation (HDInsight コマンドレット リファレンス ドキュメント)]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn479228.aspx
  [HDInsight での Pig の使用]: ../hdinsight-use-pig/
