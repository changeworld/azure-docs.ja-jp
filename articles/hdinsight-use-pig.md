<properties linkid="manage-services-hdinsight-howto-pig" urlDisplayName="Use Hadoop Pig in HDInsight" pageTitle="Use Hadoop Pig in HDInsight | Azure" metaKeywords="" description="Learn how to use Pig with HDInsight. Write Pig Latin statements to analyze an application log file, and run queries on the data to generate output for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop Pig in HDInsight" authors="jgao" solutions="big data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# HDInsight の Hadoop での Pig の使用

HDInsight で [Apache Pig][] ジョブを実行して Apache log4j ログ ファイルを分析する方法について説明します。

**所要時間:** 30 分

## この記事の内容

-   [Pig の利用例][]
-   [前提条件][]
-   [Pig Latin について][]
-   [PowerShell を使用して Pig ジョブを送信する][]
-   [HDInsight .NET SDK を使用して Pig ジョブを送信する][]
-   [次のステップ][]

## <span id="usage"></span></a>Pig の利用例

データベースは、データ量がそれほど多くなく、クエリの待ち時間が短い場合に適しています。数テラバイトもある大規模なデータセットやビッグ データとなると、従来の SQL データベースでは十分に対応できません。これまでデータベース管理者は、データベースの負荷が増大し、パフォーマンスが低下したとき、より大型のハードウェアを購入することでスケールアップを図ってきました。

通常、アプリケーションで発生したエラー、例外、その他のコーディング問題はすべてログ ファイルに保存されます。管理者はログ ファイルのデータを参照して問題点を確認したり、指標を生成したりします。多くの場合、これらのログ ファイルはサイズが非常に大きくなり、さらにその膨大なログ データを処理、分析しなければなりません。

ログ ファイルはまさにビッグ データといえます。リレーショナル データベースや統計/視覚化パッケージではビッグ データを適切に処理できません。こうした膨大なデータを実用的な速度で処理するには、数十台、数百台、あるいは数千台のサーバー上でソフトウェアを同時に実行する必要があります。Hadoop は、膨大な構造化データと非構造化データを処理するアプリケーションを記述するための MapReduce フレームワークです。クラスター化された多数のコンピューターでこれらのデータを並列処理することで、高い信頼性と耐障害性を実現します。

[Apache *Pig*][Apache Pig] には *MapReduce* ジョブを実行するためのスクリプト言語が備わっており、Java コードの代わりに使用できます。Pig のスクリプト言語を *Pig Latin* といいます。Pig Latin ステートメントは一般的なフローに従います。

-   **Load**: 操作対象のデータをファイル システムから読み込みます。
-   **Transform**: データを操作します。
-   **Dump または store**: データを画面に出力します。または、処理できるように保存します。

Pig を使用すると、mapper プログラムと reducer プログラムの記述に要する時間を短縮できます。つまり、Java は不要であり、従来のようなコードも必要ありません。Java コードと Pig を組み合わせることもできます。多くの複雑なアルゴリズムを、人間による判読が可能な数行の Pig コードで記述できます。

次の 2 つの図は、この記事で実行する処理を視覚化したものです。これらの図はデータセットの一部を例にとり、スクリプト内の Pig コード行を実行したときデータがどように処理されるかを示しています。最初の図は log4j ファイルのサンプルです。

![サンプル ファイル全体][]

2 つ目の図はデータ変換を示しています。

![HDI.PIG.Data.Transformation][]

Pig Latin の詳細については、「[Pig Latin Reference Manual 1][]」および「[Pig Latin Reference Manual 2][]」を参照してください。

## <span id="prerequisites"></span></a>前提条件

この記事を読み始める前に、次の要件に気を付けてください。

-   Azure HDInsight クラスター。手順については、「[Azure HDInsight の概要][]」または「[HDInsight クラスターのプロビジョニング][]」を参照してください。このチュートリアルを読み進めるには、次のデータが必要です。

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <tr class="header">
    <th align="left">クラスター プロパティ</th>
    <th align="left">PowerShell 変数名</th>
    <th align="left">値</th>
    <th align="left">説明</th>
    </tr>
    <tr class="odd">
    <td align="left">HDInsight クラスター名</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">このチュートリアルを実行する HDInsight クラスター。</td>
    </tr>
    </table>

-   Azure PowerShell のインストールおよび構成。手順については、[Azure PowerShell のインストールおよび構成に関するページ][]を参照してください。

**HDInsight ストレージについて**

HDInsight はデータ ストレージとして Azure BLOB ストレージを使用します。これは *WASB* または *Azure ストレージ - BLOB* と呼ばれています。WASB は、HDFS を Azure BLOB ストレージ上で Microsoft が実装したものです。詳細については、「[HDInsight での Azure BLOB ストレージの使用][]」を参照してください。

HDInsight クラスターをプロビジョニングするときに、HDFS と同じように、Azure Storage アカウントと、そのアカウントに対応する特定の BLOB ストレージ コンテナーを、既定のファイル システムとして指定します。プロビジョニング プロセスを実行するときに、このストレージ アカウントに加えて、同じ Azure サブスクリプションまたは別の Azure サブスクリプションに属する付加的なストレージ アカウントを追加することもできます。付加的なストレージ アカウントを追加する方法の詳細については、「[HDInsight クラスターのプロビジョニング][]」を参照してください。このチュートリアルで使用する PowerShell スクリプトを簡単にするために、ファイルはすべて、*/tutorials/usepig* にある既定のファイル システム コンテナーに格納されています。既定で、このコンテナーの名前は、HDInsight クラスター名と同じです。
WASB の構文は次のとおりです。

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] HDInsight クラスター Version 3.0 では、*wasb://* 構文のみがサポートされます。旧バージョンの *"asv://"* 構文は、HDInsight 2.1 および 1.6 クラスターではサポートされますが、HDInsight 3.0 クラスターではサポートされず、以降のバージョンでもサポートされません。

> [WACOM.NOTE] WASB のパスは仮想パスです。詳細については、「[HDInsight での Azure BLOB ストレージの使用][]」を参照してください。

既定のファイル システム コンテナーに格納されているファイルは、次の URI のどれを使用しても HDInsight からアクセスできます (例として sample.log を使用しています。このファイルは、このチュートリアルで使用されるデータ ファイルです)。

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
    wasb:///example/data/sample.log
    /example/data/sample.log

ストレージ アカウントから直接ファイルにアクセスする場合、ファイルの BLOB 名は次のようになります。

    example/data/sample.log

この記事では、HDInsight クラスターに付属している log4j サンプル ファイル (*\\example\\data\\sample.log*) を使用します。実際のデータ ファイルのアップロードについては、「[データを HDInsight へアップロードする方法][]」を参照してください。

## <span id="understand"></span></a> Pig Latin について

ここでは、いくつかの Pig Latin ステートメントと、その実行結果を確認します。次のセッションでは、PowerShell を使用して Pig ステートメントを実行します。

1.  ファイル システムからデータを読み込んで、結果を表示します。

        LOGS = LOAD 'wasb:///example/data/sample.log';
        DUMP LOGS;

    次のように出力されます。

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

2.  データ ファイルの各行を調べ、6 つのログ レベルと照合します。

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

3.  一致しない行を除去します。たとえば、空の行です。

        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        DUMP FILTEREDLEVELS;

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

4.  ログ レベルのすべてをそれぞれ独自の行にまとめます。

        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        DUMP GROUPEDLEVELS;

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

5.  グループごとにログ レベルの出現回数をカウントします。これらは各ログ レベルの頻度になります。

        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        DUMP FREQUENCIES;

    次のように出力されます。

        (INFO,3355)
        (WARN,361)
        (DEBUG,15608)
        (ERROR,181)
        (FATAL,37)
        (TRACE,29950)

6.  出現頻度が多い順に並べ替えます。

        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;   

    次のように出力されます。

        (TRACE,29950)
        (DEBUG,15608)
        (INFO,3355)
        (WARN,361)
        (ERROR,181)
        (FATAL,37)

## <span id="powershell"></span></a>PowerShell を使用して Pig ジョブを送信する

ここでは PowerShell コマンドレットの使用手順を示します。このセクションを読み進める前に、まずローカル環境をセットアップし、Azure への接続を構成する必要があります。詳細については、「[Azure HDInsight の概要][]」および「[PowerShell を使用した HDInsight の管理][]」を参照してください。

**PowerShell を使用して Pig Latin を実行するには**

1.  Windows PowerShell ISE を開きます (Windows 8 のスタート画面で、「**PowerShell\_ISE**」と入力し、**[Windows PowerShell ISE]** をクリックします。「[Start Windows PowerShell on Windows 8 and Windows (Windows 8 と Windows での Windows PowerShell の起動)][])」を参照してください。
2.  下のウィンドウで、次のコマンドを実行して、Azure サブスクリプションに接続します。

        Add-AzureAccount

    Azure アカウント資格情報の入力を求められます。サブスクリプション接続を追加するこの方法はタイム アウトし、12 時間後には、このコマンドレットを再度実行する必要があります。

    > [WACOM.NOTE] Azure サブスクリプションが複数あり、使用するサブスクリプションが既定のサブスクリプションではない場合は、**Select-AzureSubscription** コマンドレットを使用して現在のサブスクリプションを選択します。

3.  スクリプト ウィンドウで、次のコードをコピーして貼り付けます。

        $clusterName = "<HDInsightClusterName>" 
        $statusFolder = "/tutorials/usepig/status"

4.  $clusterName 変数を設定します。

5.  スクリプト ウィンドウで次のコードを追加します。このコードによって、Pig Latin クエリ文字列を定義し、Pig ジョブの定義を作成します。

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

    -File スイッチを使用して、HDFS 上の Pig スクリプト ファイルを指定することもできます。-StatusFolder スイッチを使用すると、標準エラー ログと標準出力ファイルがそのフォルダーに作成されます。

6.  Pig ジョブを送信するための、次のコードを追加します。

        # Submit the Pig job
        Write-Host "Submit the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition  

7.  Pig ジョブの完了を待つための、次のコードを追加します。

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

8.  Pig ジョブの出力を表示するための、次のコードを追加します。

        # Print the standard error and the standard output of the Pig job.
        #Write-Host "Display the standard error log ..." -ForegroundColor Green
        #Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardError

        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

    > [WACOM.NOTE] 次に示すスクリーンショットでは、出力を短縮するために、Get-AzureHDInsightJobOut コマンドレットの 1 つがコメント アウトされています。

9.  **F5** キーを押して、スクリプトを実行します。
    ![HDI.Pig.PowerShell][]

    Pig ジョブがログの種類の出現回数を計算します。

## <span id="sdk"></span></a>HDInsight .NET SDK を使用して Pig ジョブを送信する

次に、HDInsight .NET SDK を使用した Pig ジョブの送信の例を示します。Hadoop ジョブの送信用の C# アプリケーションを作成する方法については、「[HDInsight .NET SDK を使用して MapReduce ジョブを送信する][]」を参照してください。

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

## <span id="nextsteps"></span></a>次のステップ

Pig ではデータを分析できますが、HDInsight には便利なその他の言語も用意されています。Hive は SQL に似たクエリ言語であり、HDInsight に保存されているデータに対して簡単にクエリを実行できます。また、Java で記述された MapReduce ジョブでは複雑なデータ分析を実行できます。詳細については、次のトピックを参照してください。

-   [Azure HDInsight の概要][]
-   [HDInsight へのデータのアップロード][データを HDInsight へアップロードする方法]
-   [プログラムによる Hadoop ジョブの送信][HDInsight .NET SDK を使用して MapReduce ジョブを送信する]
-   [HDInsight での Hive の使用][]

  [Apache Pig]: http://pig.apache.org/
  [Pig の利用例]: #usage
  [前提条件]: #prerequisites
  [Pig Latin について]: #understand
  [PowerShell を使用して Pig ジョブを送信する]: #powershell
  [HDInsight .NET SDK を使用して Pig ジョブを送信する]: #sdk
  [次のステップ]: #nextsteps
  [サンプル ファイル全体]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
  [HDI.PIG.Data.Transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
  [Pig Latin Reference Manual 1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
  [Pig Latin Reference Manual 2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
  [Azure HDInsight の概要]: ../hdinsight-get-started/
  [HDInsight クラスターのプロビジョニング]: ../hdinsight-provision-clusters/
  [Azure PowerShell のインストールおよび構成に関するページ]: ../install-configure-powershell/
  [HDInsight での Azure BLOB ストレージの使用]: ../hdinsight-use-blob-storage/
  [データを HDInsight へアップロードする方法]: ../hdinsight-upload-data/
  [PowerShell を使用した HDInsight の管理]: ../hdinsight-administer-use-powershell/
  [Start Windows PowerShell on Windows 8 and Windows (Windows 8 と Windows での Windows PowerShell の起動)]: http://technet.microsoft.com/en-us/library/hh847889.aspx
  [HDI.Pig.PowerShell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
  [HDInsight .NET SDK を使用して MapReduce ジョブを送信する]: ../hdinsight-submit-hadoop-jobs-programmatically/#mapreduce-sdk
  [HDInsight での Hive の使用]: ../hdinsight-use-hive/
