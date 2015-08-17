<properties
   pageTitle="HDInsight で Hadoop Pig と .NET の使用 | Microsoft Azure"
   description=".NET SDK for Hadoop を使用して HDInsight で Hadoop に Pig ジョブを送信する方法を説明します。"
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>

#HDInsight で .NET SDK for Hadoop を使用した Pig ジョブの実行

[AZURE.INCLUDE [pig セレクター](../../includes/hdinsight-selector-use-pig.md)]

このドキュメントでは、.NET SDK for Hadoop を使用して HDInsight クラスターで Hadoop に Pig ジョブを送信する方法の例を紹介します。

HDInsight .NET SDK は、.NET から HDInsight クラスターを簡単に操作できる .NET クライアント ライブラリを提供します。Pig では、一連のデータ変換をモデル化することで MapReduce 操作を作成できます。基本的な C# アプリケーションを使用して、Pig ジョブを HDInsight クラスターに送信する方法を説明します。

##<a id="prereq"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Azure HDInsight (HDInsight での Hadoop) クラスター (Windows または Linux ベースのいずれか)

* Visual Studio 2012 または 2013

##<a id="certificate"></a>管理証明書の作成

Azure HDInsight にアプリケーションを認証するには、自己署名証明書を作成して開発ワークステーションにインストールすると同時に、それを Azure サブスクリプションにもアップロードする必要があります。

この方法に関する手順については、「[自己署名証明書の作成](http://go.microsoft.com/fwlink/?LinkId=511138)」をご覧ください。

> [AZURE.NOTE]後で使用するため、証明書にはわかりやすい名前を付けてください。

##<a id="subscriptionid"></a>サブスクリプション ID の検索

各 Azure サブスクリプションは、サブスクリプション ID と呼ばれる GUID で識別されます。次の手順に従って、この値を検索します。

1. [Azure 管理コンソール](https://manage.windowsazure.com/)にアクセスします。

2. ポータルの左側のバーから **[設定]** を選択します。

3. ページの右にある情報から使用するサブスクリプションと、**[サブスクリプション ID]** 列の値を見つけます。

後で使用するため、サブスクリプション ID を書き留めます。

##<a id="create"></a>アプリケーションを作成する

1. Visual Studio 2012 または 2013 を開く

2. **[ファイル]** メニューから **[新規]**、**[プロジェクト]** の順に選択します。

3. 新しいプロジェクトの場合は、次の値を入力または選択します。

	<table>
<tr>
<th>プロパティ</th>
<th>値</th>
</tr>
<tr>
<th>カテゴリ</th>
<th>テンプレート/Visual C#/Windows</th>
</tr>
<tr>
<th>テンプレート</th>
<th>コンソール アプリケーション</th>
</tr>
<tr>
<th>名前</th>
<th>SubmitPigJob</th>
</tr>
</table>

4. **[OK]** をクリックしてプロジェクトを作成します。

5. **[ツール]** メニューで **[ライブラリ パッケージ マネージャー]** または **[NuGet パッケージ マネージャー]** を選択し、**[パッケージ マネージャー コンソール]** を選択します。

6. コンソールで次のコマンドを実行して、.NET SDK パッケージをインストールします。

		Install-Package Microsoft.Windowsazure.Management.HDInsight

7. ソリューション エクスプローラーで **Program.cs** をダブルクリックして、このファイルを開きます。既存のコードを次のコードに置き換えます。

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

		namespace SubmitPigJob
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
		            // Get the subscription ID
		            string subscriptionID = PromptForInput("Enter your Azure Subscription ID:");

		            // Get the certificate name
		            string certFriendlyName = PromptForInput("Enter the management certificate name:");

		            // Get the cluster name
		            string clusterName = PromptForInput("Enter the HDInsight cluster name:");

		            // Set the folder that job status is written to
		            string statusFolderName = @"/tutorials/usepig/status";

		            // The Pig Latin statements to run
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
		            JobCreationResults mrJobResults = jobClient.CreatePigJob		(myJobDefinition);

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

		        private static string PromptForInput(string message)
		        {
		            Console.WriteLine(message);
		            return Console.ReadLine();
		        }
		    }
		}


7. ファイルを保存します。

##<a id="run"></a>アプリケーションを実行します。

**F5** キーを押してアプリケーションを起動します。プロンプトに従って、**サブスクリプション ID**、**証明書のフレンドリ名**、**HDInsight クラスター名**を入力します。アプリケーションで数行の情報が生成され、次のように表示されます。

```
----- The Pig job output log.
(TRACE,816)
(DEBUG,434)
(INFO,96)
(WARN,11)
(ERROR,6)
(FATAL,2)

----- Press ENTER to continue.
```

**ENTER** キーを押してアプリケーションを閉じます。

##<a id="summary"></a>概要

このように、.NET SDK for Hadoop では Pig ジョブを HDInsight クラスターに送信する .NET アプリケーションを作成して、ジョブのステータスを監視し、出力を取得できます。

##<a id="nextsteps"></a>次のステップ

HDInsight での Pig に関する全般的な情報

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)

<!---HONumber=August15_HO6-->