<properties urlDisplayName="Hadoop Samples in HDInsight" pageTitle="HDInsight での C# ストリーミング ワードカウント Hadoop サンプル | Azure" metaKeywords="hadoop, hdinsight, hdinsight administration, hdinsight administration azure" description="サンプル TBD を実行する方法について説明します。" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="HDInsight での C# ストリーミング ワードカウント Hadoop サンプル" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# HDInsight での C# ストリーミング ワードカウント Hadoop サンプル

Hadoop には MapReduce に対するストリーミング API が用意されていて、Java 以外の言語 map 関数と reduce 関数を記述することができます。このチュートリアルでは、Hadoop ストリーミング インターフェイスを使用する MapReduce プログラムを C# で記述する方法、および Azure PowerShell コマンドレットを使用して Azure HDInsight でプログラムを実行する方法を紹介します。

この例では、mapper と reducer はどちらも [stdin][stdin] から入力を (1 行ずつ) 読み取り、出力を [stdout][stdin] に書き込む実行可能ファイルです。プログラムはテキスト内の単語すべての出現数を計算します。

**mapper** 用の実行可能ファイルが指定されると、各 mapper タスクは mapper 開始時に別のプロセスとしてその実行可能ファイルを起動します。mapper タスクは実行されると、入力を行に変換して、その行をプロセスの [stdin][stdin] にフィードします。一方、mapper はプロセスの stdout から行指向の出力を収集して、各行をキーと値のペアに変換します。これが mapper の出力として収集されます。既定では、行の最初のタブ文字までがキーであり、行の残り (タブ文字を除く) がデータです。行にタブ文字がない場合は、行全体がキーと見なされ、値は null になります。

**reducer** 用の実行可能ファイルが指定されると、各 reducer タスクは reducer 開始時に別のプロセスとしてその実行可能ファイルを起動します。reducer タスクは実行されると、入力のキーと値のペアを行に変換して、その行をプロセスの [stdin][stdin] にフィードします。一方、reducer はプロセスの [stdout][stdin] から行指向の出力を収集して、各行をキーと値のペアに変換します。これが reducer の出力として収集されます。既定では、行の最初のタブ文字までがキーであり、行の残り (タブ文字を除く) がデータです。

Hadoop ストリーミング インターフェイスの詳細については、[Hadoop ストリーミングに関するサイト][Hadoop ストリーミングに関するサイト]を参照してください。

**学習内容:**

-   Azure PowerShell を使用して C# ストリーミング プログラムを実行し、ファイルに含まれるデータを HDInsight で分析する方法。
-   Hadoop ストリーミング インターフェイスを使用する C# コードの記述方法。

**前提条件**:

-   Azure アカウントが必要です。アカウントにサインアップする方法については、[Azure の無料評価版のページ][Azure の無料評価版のページ]を参照してください。

-   HDInsight クラスターのプロビジョニングを終えている必要があります。クラスターを作成するさまざまな方法については、「[HDInsight クラスターのプロビジョニング][HDInsight クラスターのプロビジョニング]」を参照してください。

-   Azure PowerShell をインストールして、アカウントを使用するように構成している必要があります。その手順については、「[Azure PowerShell のインストールおよび構成][Azure PowerShell のインストールおよび構成]」を参照してください。

## この記事の内容

このトピックでは、サンプルを実行する方法について説明し、MapReduce プログラムの Java コードを示し、説明した内容をまとめ、次の手順の概略を示します。ここで取り上げる内容は次のとおりです。

1.  [Azure PowerShell を使用したサンプルの実行][Azure PowerShell を使用したサンプルの実行]
2.  [Hadoop ストリーミングの C# コード][Hadoop ストリーミングの C# コード]
3.  [まとめ][まとめ]
4.  [次のステップ][次のステップ]

## <span id="run-sample"></span></a>Azure PowerShell を使用したサンプルの実行

**MapReduce ジョブを実行するには**

1.  **Azure PowerShell** を開きます。Azure PowerShell コンソール ウィンドウを開く手順については、[Azure PowerShell のインストールと構成に関するページ][Azure PowerShell のインストールおよび構成]を参照してください。

2.  次のコマンドを実行して、2 つの変数を設定します。

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name

3.  次のコマンドを実行して、MapReduce ジョブを定義します。

        # Create a MapReduce job definition for the streaming job.
        $streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe" 

    パラメーターは mapper 関数と reducer 関数、入力ファイルと出力ファイルを指定します。

4.  次のコマンドを実行して、MapReduce ジョブを実行し、ジョブの完了を待ち、標準エラーを出力します。

        # Run the C# Streaming MapReduce job.
        # Wait for the job to complete.
        # Print output and standard error file of the MapReduce job
        Select-AzureSubscription $subscriptionName
        $streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

5.  次のコマンドを実行して、ワード カウントの結果を表示します。

        $subscriptionName = "<SubscriptionName>"   
        $storageAccountName = "<StorageAccountName>" 
        $containerName = "<ContainerName>"

        # Select the current subscription
        Select-AzureSubscription $subscriptionName

        # Blob storage container and account name

    $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $\_.Primary }
     $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

        # Retrieve the output
        Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force 

        # The number of words in the text is:
        cat ./example/data/StreamingOutput/wc.txt/part-00000

    MapReduce ジョブの出力ファイルは不変であることに注意してください。そのため、このサンプルを再実行する場合は、出力ファイルの名前を変更する必要があります。

## <span id="java-code"></span></a>Hadoop ストリーミングの C# コード

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

cat.cs ファイルの mapper コードは、StreamReader オブジェクトを使用して入力ストリームの文字をコンソールに読み取り、次にコンソールが静的な Console.Writeline メソッドを使用してストリームを標準出力ストリームに書き込みます。

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

wc.cs ファイルの reducer コードは、[StreamReader][StreamReader] オブジェクトを使用して、mapper の cat.exe によって出力された標準入力ストリームから文字を読み取ります。[Console.Writeline][Console.Writeline] メソッドを使用して文字を読み取ると、各単語の末尾にある空白および改行文字をカウントして、[Console.Writeline][Console.Writeline] メソッドを使用して合計を標準出力ストリームに書き込みます。

## <span id="summary"></span></a>まとめ

このチュートリアルでは、Hadoop ストリーミングを使用して HDInsight で MapReduce ジョブをデプロイする方法を説明しました。

## <span id="next-steps"></span></a>次のステップ

Azure PowerShell を使用して Azure HDInsight 上で他のサンプルを実行するチュートリアルや、Pig、Hive、MapReduce の使用方法に関するチュートリアルについては、次のトピックを参照してください。

-   [Azure HDInsight の概要][Azure HDInsight の概要]
-   [サンプル: Pi 推定][サンプル: Pi 推定]
-   [サンプル: ワードカウント][サンプル: ワードカウント]
-   [サンプル: 10 GB GraySort][サンプル: 10 GB GraySort]
-   [HDInsight での Pig の使用][HDInsight での Pig の使用]
-   [HDInsight での Hive の使用][HDInsight での Hive の使用]
-   [Azure HDInsight SDK のドキュメント][Azure HDInsight SDK のドキュメント]

  [stdin]: http://msdn.microsoft.com/ja-jp/library/3x292kth(v=vs.110).aspx
  [Hadoop ストリーミングに関するサイト]: http://wiki.apache.org/hadoop/HadoopStreaming
  [Azure の無料評価版のページ]: http://azure.microsoft.com/ja-jp/pricing/free-trial/
  [HDInsight クラスターのプロビジョニング]: ../hdinsight-provision-clusters/
  [Azure PowerShell のインストールおよび構成]: ../install-configure-powershell/
  [Azure PowerShell を使用したサンプルの実行]: #run-sample
  [Hadoop ストリーミングの C# コード]: #java-code
  [まとめ]: #summary
  [次のステップ]: #next-steps
  [StreamReader]: http://msdn.microsoft.com/ja-jp/library/system.io.streamreader.aspx
  [Console.Writeline]: http://msdn.microsoft.com/ja-jp/library/system.console.writeline
  [Azure HDInsight の概要]: ../hdinsight-get-started/
  [サンプル: Pi 推定]: ../hdinsight-sample-pi-estimator/
  [サンプル: ワードカウント]: ../hdinsight-sample-wordcount/
  [サンプル: 10 GB GraySort]: ../hdinsight-sample-10gb-graysort/
  [HDInsight での Pig の使用]: ../hdinsight-use-pig/
  [HDInsight での Hive の使用]: ../hdinsight-use-hive/
  [Azure HDInsight SDK のドキュメント]: http://msdnstage.redmond.corp.microsoft.com/ja-jp/library/dn479185.aspx
