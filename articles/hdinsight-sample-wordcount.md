<properties urlDisplayName="Hadoop Samples in HDInsight" pageTitle="HDInsight でのワードカウント Hadoop サンプル | Azure" metaKeywords="hdinsight, hdinsight sample, hadoop, mapreduce" description="HDInsight で単純な MapReduce サンプルを実行する方法について説明します。" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="HDInsight でのワードカウント Hadoop サンプル" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# HDInsight でのワードカウント Hadoop サンプル

このサンプル トピックでは、Azure PowerShell を使用してテキスト ファイル内の単語の出現回数を計算する Hadoop MapReduce プログラムを HDInsight で実行する方法を紹介します。ワードカウント MapReduce プログラムは Java で記述され、HDInsight によって管理される Hadoop クラスター上で実行されます。このサンプルで分析するテキスト ファイルは、『The Notebooks of Leonardo Da Vinci (レオナルド・ダ・ヴィンチの手記)』の Project Gutenberg 電子書籍版です。

Hadoop MapReduce プログラムはテキスト ファイルを読み取って、各単語の出現回数を計算します。出力は新しいテキスト ファイルで、各行に単語とその単語のファイル内での出現回数 (キーと値をタブで区切ったペア) が記録されています。この処理は 2 段階で実行されます。まず、Mapper が入力テキストから各行を読み込み、単語に分解して、単語が出現するたびに "キー/値" ペア (値は 1) を発行します。Reducer は、Mapper が発行した各単語のカウントを合計し、単語と合計出現回数から成る "キー/値" ペアを生成します。

**学習内容:**

-   Azure PowerShell を使用して MapReduce プログラムを HDInsight クラスター上で実行する方法。
-   MapReduce プログラムを Java で記述する方法。

**前提条件**:

-   Azure アカウントが必要です。アカウントにサインアップする方法については、[Azure の無料評価版のページ][Azure の無料評価版のページ]を参照してください。

-   HDInsight クラスターのプロビジョニングを終えている必要があります。クラスターを作成するさまざまな方法については、「[Azure HDInsight の概要][Azure HDInsight の概要]」または「[HDInsight クラスターのプロビジョニング][HDInsight クラスターのプロビジョニング]」を参照してください。

-   Azure PowerShell をインストールして、アカウントを使用するように構成している必要があります。その手順については、「[Azure PowerShell のインストールおよび構成][Azure PowerShell のインストールおよび構成]」を参照してください。

## この記事の内容

このトピックでは、サンプルを実行する方法について説明し、MapReduce プログラムの Java コードを示し、説明した内容をまとめ、次の手順の概略を示します。ここで取り上げる内容は次のとおりです。

1.  [Azure PowerShell を使用したサンプルの実行][Azure PowerShell を使用したサンプルの実行]
2.  [ワードカウント MapReduce プログラムの Java コード][ワードカウント MapReduce プログラムの Java コード]
3.  [まとめ][まとめ]
4.  [次のステップ][次のステップ]

## <span id="run-sample"></span></a>Azure PowerShell を使用したサンプルの実行

**MapReduce ジョブを送信するには**

1.  **Azure PowerShell** を開きます。Azure PowerShell コンソール ウィンドウを開く手順については、[Azure PowerShell のインストールと構成に関するページ][Azure PowerShell のインストールおよび構成]を参照してください。

2.  次のコマンドを実行して、2 つの変数を設定します。

        $subscriptionName = "<SubscriptionName>"   # Azure subscription name
        $clusterName = "<ClusterName>"             # HDInsight cluster name

3.  次のコマンドを実行して、MapReduce ジョブ定義を作成します。

        # Define the MapReduce job
        $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

    > [WACOM.NOTE] *hadoop-examples.jar* は、Version 2.1 の HDInsight クラスターに付属しています。バージョン 3.0 の HDInsight クラスターで、このファイルの名前は *hadoop-mapreduce.jar* に変更されました

    hadoop-examples.jar ファイルは HDInsight クラスターのディストリビューションに付属しています。MapReduce ジョブには引数が 2 つあります。最初の引数はソース ファイル名で、2 つ目の引数は出力ファイル パスです。ソース ファイルは HDInsight クラスターのディストリビューションに付属しており、出力ファイル パスは実行時に作成されます。

4.  次のコマンドを実行して、MapReduce ジョブを送信します。

        # Submit the job
        Select-AzureSubscription $subscriptionName
        $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

    MapReduce ジョブ定義に加えて、MapReduce ジョブを実行する HDInsight クラスター名も指定します。

5.  次のコマンドを実行して、MapReduce ジョブの実行中に発生したエラーを確認します。

        # Get the job output
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 

**MapReduce ジョブの結果を取得するには**

1.  **Azure PowerShell** を開きます。
2.  次のコマンドを実行して、3 つの変数を設定します。

        $subscriptionName = "<SubscriptionName>"       # Azure subscription name

        $storageAccountName = "<StorageAccountName>"   # Azure storage account name
        $containerName = "<ContainerName>"             # Blob storage container name

    Azure のストレージ アカウントは、このチュートリアルで先に作成したアカウントです。ストレージ アカウントは、既定の HDInsight クラスター ファイル システムとして使用する BLOB コンテナーをホストするために使用されます。BLOB ストレージ コンテナー名は、クラスターのプロビジョニング時に別の名前を指定しない限り、通常、HDInsight クラスターと同じ名前です。

3.  次のコマンドを実行して、Azure Storage のコンテキスト オブジェクトを作成します。

        # Select the current subscription
        Select-AzureSubscription $subscriptionName

        # Create the storage account context object
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    *Select-AzureSubscription* は、サブスクリプションが複数あり、使用するサブスクリプションが既定のサブスクリプションではない場合に備えて、現在のサブスクリプションを設定するために使用します。

4.  次のコマンドを実行して、MapReduce ジョブの出力を BLOB コンテナーからコンピューターにダウンロードします。

        # Download the job output to the workstation
        Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    *example/data/WordCountOutput* フォルダーは、MapReduce ジョブの実行時に指定した出力フォルダーです。*part-r-00000* は MapReduce ジョブの出力の既定のファイル名です。ファイルはフォルダー構造を保ったままローカル フォルダーにダウンロードされます。たとえば、次のスクリーンショットでは、現在のフォルダーが C ドライブのルート フォルダーです。ファイルは *C:\\example\\data\\WordCountOutput* フォルダーにダウンロードされます。

5.  次のコマンドを実行して、MapReduce ジョブの出力ファイルの内容を表示します。

        cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    MapReduce ジョブは、単語と出現回数が記録された *part-r-00000* という名前のファイルを作成します。スクリプトでは findstr コマンドを使用して、*"there"* を含む単語をすべて表示しています。

ワードカウント スクリプトの出力がコマンド ウィンドウに表示されます。

![HDI.Sample.WordCount.Output][HDI.Sample.WordCount.Output]

MapReduce ジョブの出力ファイルは不変であることに注意してください。そのため、このサンプルを再実行する場合は、出力ファイルの名前を変更する必要があります。

## <span id="java-code"></span></a>ワードカウント MapReduce プログラムの Java コード

    package org.apache.hadoop.examples;
    import java.io.IOException;
    import java.util.StringTokenizer;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.IntWritable;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapreduce.Job;
    import org.apache.hadoop.mapreduce.Mapper;
    import org.apache.hadoop.mapreduce.Reducer;
    import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
    import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
    import org.apache.hadoop.util.GenericOptionsParser;

    public class WordCount {

    public static class TokenizerMapper 
       extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();
      
    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
      StringTokenizer itr = new StringTokenizer(value.toString());
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
      }
    }

    public static class IntSumReducer 
       extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values, 
                       Context context
                       ) throws IOException, InterruptedException {
      int sum = 0;
      for (IntWritable val : values) {
        sum += val.get();
      }
      result.set(sum);
      context.write(key, result);
      }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
      System.err.println("Usage: wordcount <in> <out>");
      System.exit(2);
        }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
    }

## <span id="summary"></span></a>まとめ

このチュートリアルでは、Azure PowerShell を使用して HDInsight でテキスト ファイル内の単語の出現回数を計算する MapReduce プログラムを実行する方法を紹介しました。

## <span id="next-steps"></span></a>次のステップ

Azure PowerShell を使用して Azure HDInsight 上で他のサンプルを実行するチュートリアルや、Pig、Hive、MapReduce の使用方法に関するチュートリアルについては、次のトピックを参照してください。

-   [Azure HDInsight の概要][Azure HDInsight の概要]
-   [サンプル: 10 GB GraySort][サンプル: 10 GB GraySort]
-   [サンプル: Pi 推定][サンプル: Pi 推定]
-   [サンプル: C\# ストリーミング][サンプル: C\# ストリーミング]
-   [HDInsight での Pig の使用][HDInsight での Pig の使用]
-   [HDInsight での Hive の使用][HDInsight での Hive の使用]
-   [Azure HDInsight SDK のドキュメント][Azure HDInsight SDK のドキュメント]

  [Azure の無料評価版のページ]: http://azure.microsoft.com/ja-jp/pricing/free-trial/
  [Azure HDInsight の概要]: ../hdinsight-get-started/
  [HDInsight クラスターのプロビジョニング]: ../hdinsight-provision-clusters/
  [Azure PowerShell のインストールおよび構成]: ../install-configure-powershell/
  [Azure PowerShell を使用したサンプルの実行]: #run-sample
  [ワードカウント MapReduce プログラムの Java コード]: #java-code
  [まとめ]: #summary
  [次のステップ]: #next-steps
  [HDI.Sample.WordCount.Output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png
  [サンプル: 10 GB GraySort]: ../hdinsight-sample-10gb-graysort/
  [サンプル: Pi 推定]: ../hdinsight-sample-pi-estimator/
  [サンプル: C\# ストリーミング]: ../hdinsight-sample-csharp-streaming/
  [HDInsight での Pig の使用]: ../hdinsight-use-pig/
  [HDInsight での Hive の使用]: ../hdinsight-use-hive/
  [Azure HDInsight SDK のドキュメント]: http://msdnstage.redmond.corp.microsoft.com/ja-jp/library/dn479185.aspx
