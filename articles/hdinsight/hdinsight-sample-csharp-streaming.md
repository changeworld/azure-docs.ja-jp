<properties
	pageTitle="C# ストリーミング ワードカウント Hadoop サンプル | Microsoft Azure"
	description="Hadoop ストリーミング インターフェイスを使用する MapReduce プログラムを C# で記述する方法と、PowerShell コマンドレットを使用して HDInsight でプログラムを実行する方法。"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="bradsev"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/30/2014" 
	ms.author="bradsev"/>

# HDInsight の Hadoop での C# ストリーミング ワードカウント MapReduce サンプル

Hadoop には MapReduce に対するストリーミング API が用意されていて、Java 以外の言語 map 関数と reduce 関数を記述できます。このチュートリアルでは、Hadoop ストリーミング インターフェイスを使用する MapReduce プログラムを C# で記述する方法と、PowerShell コマンドレットを使用して HDInsight でプログラムを実行する方法を紹介します。

> [AZURE.NOTE]このチュートリアルの手順は、Windows ベースの HDInsight クラスターに対してのみ機能します。Linux ベースの HDInsight クラスターのストリーミング例は、「[HDInsight 用 Python ストリーミング プログラムの開発](hdinsight-hadoop-streaming-python.md)」をご覧ください。

この例では、mapper と reducer は [stdin][stdin-stdout-stderr] から入力を (1 行ずつ) 読み取り、出力を [stdout][stdin-stdout-stderr] に書き込む実行可能ファイルです。プログラムはテキスト内の単語すべての出現数を計算します。

**mapper** 用の実行可能ファイルが指定されると、各 mapper タスクは mapper 開始時に別のプロセスとしてその実行可能ファイルを起動します。mapper タスクは実行されると、入力を行に変換して、その行をプロセスの [stdin][stdin-stdout-stderr] にフィードします。

一方、mapper はプロセスの stdout から行指向の出力を収集します。各行はキーと値のペアに変換され、mapper の出力として収集されます。既定では、行の最初のタブ文字までがキーであり、行の残り (タブ文字を除く) が値です。行にタブ文字がない場合は、行全体がキーと見なされ、値は null になります。

**reducer** 用の実行可能ファイルが指定されると、各 reducer タスクは reducer 開始時に別のプロセスとしてその実行可能ファイルを起動します。reducer タスクは実行されると、入力のキーと値のペアを行に変換して、その行をプロセスの [stdin][stdin-stdout-stderr] にフィードします。

一方、reducer はプロセスの [stdout][stdin-stdout-stderr] から行指向の出力を収集します。各行はキーと値のペアに変換され、reducer の出力として収集されます。既定では、行の最初のタブ文字までがキーであり、行の残り (タブ文字を除く) が値です。

Hadoop ストリーミング インターフェイスの詳細については、「[Hadoop ストリーミング][hadoop-streaming]」をご覧ください。

**このチュートリアルで学習する内容は次のとおりです。**

* Azure PowerShell を使用して C# ストリーミング プログラムを実行し、ファイルに含まれるデータを HDInsight で分析する方法。
* Hadoop ストリーミング インターフェイスを使用する C# コードの記述方法。


**前提条件**:

開始する前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
- **HDInsight クラスター**。クラスターを作成するさまざまな方法については、「[HDInsight クラスターのプロビジョニング](hdinsight-provision-clusters.md)」を参照してください。
- **Azure PowerShell を実行できるワークステーション**。[Azure PowerShell のインストールおよび使用](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)に関するページを参照してください。



## <a id="run-sample"></a>Azure PowerShell を使用したサンプルの実行

**MapReduce ジョブを実行するには**

1.	**Azure PowerShell** を開きます。Azure PowerShell コンソール ウィンドウを開く手順については、「[Azure PowerShell のインストールおよび構成][powershell-install-configure]」をご覧ください。

3. 次のコマンドを実行して、2 つの変数を設定します。

		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name


2. 次のコマンドを実行して、MapReduce ジョブを定義します。

		# Create a MapReduce job definition for the streaming job.
		$streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe"

	パラメーターは mapper 関数と reducer 関数、入力ファイルと出力ファイルを指定します。

5. 次のコマンドを実行して、MapReduce ジョブを実行し、ジョブの完了を待ち、標準エラー メッセージを出力します。

		# Run the C# Streaming MapReduce job.
		# Wait for the job to complete.
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError

6. 次のコマンドを実行して、ワード カウントの結果を表示します。

		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Blob storage container and account name
      $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $_.Primary } $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Retrieve the output
		Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force

		# The number of words in the text is:
		cat ./example/data/StreamingOutput/wc.txt/part-00000

	MapReduce ジョブの出力ファイルは不変であることに注意してください。そのため、このサンプルを再実行する場合は、出力ファイルの名前を変更する必要があります。


## <a id="java-code"></a>Hadoop ストリーミングの C# コード


MapReduce プログラムでは、cat.exe アプリケーションを map インターフェイスとして使ってコンソールにテキストをストリーミングし、wc.exe アプリケーションを reduce インターフェイスとして使って、文書からストリーミングされた単語の数を計算します。mapper と reducer はどちらも標準入力ストリーム (stdin) から 1 行ずつ文字を読み取って、標準出力ストリーム (stdout) に書き込みます。



	// The source code for the cat.exe (Mapper).

	using System;
	using System.IO;

	namespace cat
	{
	    class cat
	    {
	        static void Main(string[] args)
	        {
	            if (args.Length > 0)
	            {
	                Console.SetIn(new StreamReader(args[0]));
	            }

	            string line;
	            while ((line = Console.ReadLine()) != null)
	            {
	                Console.WriteLine(line);
	            }
	        }
	    }
	}



cat.cs ファイルの mapper コードは、[StreamReader][streamreader] オブジェクトを使用して入力ストリームの文字をコンソールに読み取り、次にコンソールが静的な [Console.Writeline][console-writeline] メソッドを使用してストリームを標準出力ストリームに書き込みます。


	// The source code for wc.exe (Reducer) is:

	using System;
	using System.IO;
	using System.Linq;

	namespace wc
	{
	    class wc
	    {
	        static void Main(string[] args)
	        {
	            string line;
	            var count = 0;

	            if (args.Length > 0){
	                Console.SetIn(new StreamReader(args[0]));
	            }

	            while ((line = Console.ReadLine()) != null) {
	                count += line.Count(cr => (cr == ' ' || cr == '\n'));
	            }
	            Console.WriteLine(count);
	        }
	    }
	}


wc.cs ファイルの reducer コードは、[StreamReader][streamreader] オブジェクトを使用して、mapper の cat.exe によって出力された標準入力ストリームから文字を読み取ります。[Console.Writeline][console-writeline] メソッドを使用して文字を読み取ると、各単語の末尾にある空白および改行文字をカウントして、[Console.Writeline][console-writeline] メソッドを使用して合計を標準出力ストリームに書き込みます。


## <a id="summary"></a>概要

このチュートリアルでは、Hadoop ストリーミングを使用して HDInsight で MapReduce ジョブをデプロイする方法を説明しました。

## <a id="next-steps"></a>次のステップ


Azure PowerShell を使用して Azure HDInsight 上で他のサンプルを実行するチュートリアルや、Pig、Hive、MapReduce ジョブの使用方法に関するチュートリアルについては、次のトピックをご覧ください。

* [Azure HDInsight の概要][hdinsight-get-started]
* [サンプル: Pi 推定][hdinsight-sample-pi-estimator]
* [サンプル: ワード カウント][hdinsight-sample-wordcount]
* [サンプル: 10 GB GraySort][hdinsight-sample-10gb-graysort]
* [HDInsight での Pig の使用][hdinsight-use-pig]
* [HDInsight での Hive の使用][hdinsight-use-hive]
* [Azure HDInsight SDK のドキュメント][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[hadoop-streaming]: http://wiki.apache.org/hadoop/HadoopStreaming
[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: http://msdn.microsoft.com/library/3x292kth(v=vs.110).aspx

[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
 

<!---HONumber=62-->