<properties
	pageTitle="HDInsight での Hadoop MapReduce ワード カウント サンプルの実行 | Azure"
	description="HDInsight の Hadoop クラスターで MapReduce ワード カウント サンプルを実行します。Java で記述されたこのプログラムは、テキスト ファイル内の単語の出現回数をカウントします。"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="bradsev"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2015" 
	ms.author="bradsev"/>

#HDInsight の Hadoop クラスターで MapReduce ワード カウント サンプルを実行します。

このチュートリアルでは、HDInsight の Hadoop クラスターで、MapReduce ワード カウント サンプルを実行する方法を説明します。Java で記述するこのプログラムは、テキスト ファイル内の単語出現回数をカウントし、各単語とその単語の出現回数をペアにして記録した新しいテキスト ファイルを出力します。このサンプルで分析するテキスト ファイルは、『The Notebooks of Leonardo Da Vinci (レオナルド・ダ・ヴィンチの手記)』の Project Gutenberg 電子書籍版です。


**学習内容:**

* Azure PowerShell を使用して MapReduce プログラムを HDInsight クラスター上で実行する方法。
* MapReduce プログラムを Java で記述する方法。


**前提条件**:

- Azure アカウントが必要です。アカウントにサインアップする方法については、[Azure の無料評価版のページ](http://azure.microsoft.com/pricing/free-trial/)をご覧ください。

- HDInsight クラスターのプロビジョニングを終えている必要があります。クラスターを作成するさまざまな方法については、「[Azure HDInsight の概要][hdinsight-get-started]」または「[HDInsight クラスターのプロビジョニング](hdinsight-provision-clusters.md)」をご覧ください。

- Azure PowerShell をインストールして、アカウントを使用するように構成している必要があります。その手順については、「[Azure PowerShell のインストールおよび構成][powershell-install-configure]」をご覧ください。

<h2><a id="run-sample"></a>Azure PowerShell を使用したサンプルの実行</h2>

**MapReduce ジョブを送信するには**

1.	**Azure PowerShell **コンソールを開きます。手順については、[Azure PowerShell のインストールおよび構成に関するページ][powershell-install-configure]を参照してください。

3. 次のコマンドを実行して、2 つの変数を設定します。

		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name

5. 次のコマンドを実行して、MapReduce ジョブ定義を作成します。

		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	> [AZURE.NOTE]*hadoop-examples.jar* は、HDInsight バージョン 2.1 クラスターに付属しています。ファイル名は HDInsight バージョン 3.0 クラスターで *hadoop-mapreduce.jar* に変更されます。

	hadoop-mapreduce-examples.jar ファイルは HDInsight クラスターに付属しています。MapReduce ジョブには引数が 2 つあります。最初の引数はソース ファイル名で、2 つ目の引数は出力ファイル パスです。ソース ファイルは HDInsight クラスターに付属しており、出力ファイル パスは実行時に作成されます。

6. 次のコマンドを実行して、MapReduce ジョブを送信します。

		# Submit the job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	MapReduce ジョブ定義に加えて、MapReduce ジョブを実行する HDInsight クラスター名も指定します。

8. 次のコマンドを実行して、MapReduce ジョブの実行中に発生したエラーを確認します。

		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

**MapReduce ジョブの結果を取得するには**

1. **Azure PowerShell **コンソールを開きます。
2. 次のコマンドを実行して、3 つの変数を設定します。

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"			   # Blob storage container name

	Azure のストレージ アカウントは、このチュートリアルで先に作成したアカウントです。ストレージ アカウントは、既定の HDInsight クラスター ファイル システムとして使用する BLOB をホストするために使用されます。Azure BLOB ストレージ コンテナー名は、クラスターのプロビジョニング時に別の名前を指定しない限り、通常、HDInsight クラスターと同じ名前です。

3. 次のコマンドを実行して、Azure Storage のコンテキスト オブジェクトを作成します。

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	**Select-AzureSubscription** は、サブスクリプションが複数あり、使用するサブスクリプションが既定のサブスクリプションではない場合に備えて、現在のサブスクリプションを設定するために使用します。

4. 次のコマンドを実行して、MapReduce ジョブの出力を BLOB からワークステーションにダウンロードします。

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	*example/data/WordCountOutput* フォルダーは、MapReduce ジョブの実行時に指定した出力フォルダーです。*part-r-00000* は MapReduce ジョブの出力の既定のファイル名です。ファイルはフォルダー構造を保ったままローカル フォルダーにダウンロードされます。たとえば、次のスクリーンショットでは、現在のフォルダーが C ドライブのルート フォルダーです。ファイルは *C:\\example\\data\\WordCountOutput* フォルダーにダウンロードされます。

5. 次のコマンドを実行して、MapReduce ジョブの出力ファイルの内容を表示します。

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"


	MapReduce ジョブは、単語と出現回数が記録された *part-r-00000* という名前のファイルを作成します。スクリプトでは **findstr** コマンドを使用して、*"there"* を含む単語をすべて表示しています。

ワードカウント スクリプトの出力がコマンド ウィンドウに表示されます。

![HDInsight での Hadoop MapReduce ワード カウント サンプルからの PowerShell での単語頻度結果。][image-hdi-sample-wordcount-output]

MapReduce ジョブの出力ファイルは不変であることに注意してください。そのため、このサンプルを再実行する場合は、出力ファイルの名前を変更する必要があります。

<h2><a id="java-code"></a>ワードカウント MapReduce プログラムの Java コード</h2>



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



このチュートリアルでは、Azure PowerShell を使用して HDInsight でテキスト ファイル内の単語の出現回数を計算する MapReduce プログラムを実行する方法を紹介しました。

<h2><a id="next-steps"></a>次のステップ</h2>

Azure PowerShell を使用して Azure HDInsight 上で他のサンプルを実行するチュートリアルや、Pig、Hive、MapReduce ジョブの使用方法に関するチュートリアルについては、次のトピックをご覧ください。

* [Azure HDInsight の概要][hdinsight-get-started]
* [サンプル: 10 GB GraySort][hdinsight-sample-10gb-graysort]
* [サンプル: Pi 推定][hdinsight-sample-pi-estimator]
* [サンプル: C ストリーミング][hdinsight-sample-cs-streaming]
* [HDInsight の Hadoop での Pig の使用][hdinsight-use-pig]
* [HDInsight での Hive の使用][hdinsight-use-hive]
* [Azure HDInsight SDK のドキュメント][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-cs-streaming]: hdinsight-sample-csharp-streaming.md


[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[hdinsight-get-started]: hdinsight-get-started.md

[Powershell-install-configure]: install-configure-powershell.md

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png

<!--HONumber=54-->