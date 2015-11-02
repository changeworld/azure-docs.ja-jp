<properties
	pageTitle="HDInsight での Hadoop MapReduce ワード カウント サンプルの実行 | Microsoft Azure"
	description="HDInsight の Hadoop クラスターで MapReduce ワード カウント サンプルを実行します。Java で記述されたこのプログラムは、テキスト ファイル内の単語の出現回数をカウントします。"
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
	ms.date="10/15/2015"
	ms.author="jgao"/>

#HDInsight の Hadoop クラスターでワード カウント MapReduce プログラムを実行する

Azure PowerShell を使用して HDInsight の Hadoop クラスターで MapReduce プログラムを実行する方法について説明します。プログラムは Java で作成されています。テキスト ファイル内の単語出現回数をカウントし、各単語とその単語の出現回数をペアにして記録した新しいテキスト ファイルを出力します。

プログラムは、クラスターにインストールされます。このチュートリアルで分析するテキスト ファイルは、『The Notebooks of Leonardo Da Vinci (レオナルド・ダ・ヴィンチの手記)』の Project Gutenberg 電子書籍版です。

> [AZURE.NOTE]このドキュメントの手順では、Windows クライアントが必要です。Linux ベースの HDInsight クラスターで、Linux、OS X、または Unix クライアントのワード カウント サンプルを使用する手順については、「[SSH による HDInsight での MapReduce と Hadoop の使用](hdinsight-hadoop-use-mapreduce-ssh.md)」、または「[Curl による HDInsight での MapReduce と Hadoop の使用](hdinsight-hadoop-use-mapreduce-curl.md)」を参照してください。

**その他の関連記事:**

* [Azure HDInsight の概要][hdinsight-get-started]
* [HDInsight での Hadoop 用 Java MapReduce プログラムの開発](hdinsight-develop-deploy-java-mapreduce.md)
* [HDInsight での Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md)
* [サンプル: 10 GB GraySort][hdinsight-sample-10gb-graysort]
* [サンプル: Pi 推定][hdinsight-sample-pi-estimator]
* [サンプル: C ストリーミング][hdinsight-sample-cs-streaming]

**前提条件**:

- **HDInsight クラスター**。クラスターを作成するさまざまな方法については、「[Azure HDInsight の概要][hdinsight-get-started]」または「[HDInsight クラスターのプロビジョニング](hdinsight-provision-clusters.md)」をご覧ください。
- **Azure PowerShell を実行できるワークステーション**。[Azure PowerShell のインストールおよび使用](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)に関するページを参照してください。

## Azure PowerShell を使用したサンプルの実行

**MapReduce ジョブを送信するには**

1. **Windows PowerShell ISE** を開きます。手順については、[Azure PowerShell のインストールおよび構成に関するページ][powershell-install-configure]を参照してください。
2. 次の PowerShell スクリプトを貼り付けます。

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name
		
		Select-AzureRmSubscription $subscriptionName
		
		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
									-JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
									-ClassName "wordcount" `
									-Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput1"
		
		# Submit the job and wait for job completion
		$cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:" 
		$wordCountJob = Start-AzureRmHDInsightJob `
							-ResourceGroupName $resourceGroupName `
							-ClusterName $clusterName `
							-HttpCredential $cred `
							-JobDefinition $wordCountJobDefinition 
		
		Wait-AzureRmHDInsightJob `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-JobId $wordCountJob.JobId 
		
		# Get the job output
		$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
		$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
		$defaultStorageContainer = $cluster.DefaultStorageContainer
		
		Get-AzureRmHDInsightJobOutput `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-DefaultStorageAccountName $defaultStorageAccount `
			-DefaultStorageAccountKey $defaultStorageAccountKey `
			-DefaultContainer $defaultStorageContainer  `
			-JobId $wordCountJob.JobId `
			-DisplayOutputType StandardError

3. 最初の 3 つの変数を設定し、スクリプトを実行します。
		
**MapReduce ジョブの結果を取得するには**

1. **Windows PowerShell ISE** を開きます。手順については、[Azure PowerShell のインストールおよび構成に関するページ][powershell-install-configure]を参照してください。
2. 次の PowerShell スクリプトを貼り付けます。

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
		
		# Get the cluster properties
		$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
		$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
		$defaultStorageContainer = $cluster.DefaultStorageContainer
		
		# Download the job output to the workstation
		$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
		Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
		
		# Display the output file
		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	MapReduce ジョブは、単語と出現回数が記録された *part-r-00000* という名前のファイルを作成します。スクリプトでは **findstr** コマンドを使用して、*"there"* を含む単語をすべて表示しています。

ワードカウント スクリプトの出力がコマンド ウィンドウに表示されます。

![HDInsight での Hadoop MapReduce ワード カウント サンプルからの PowerShell での単語頻度結果。][image-hdi-sample-wordcount-output]

MapReduce ジョブの出力ファイルは不変であることに注意してください。そのため、このサンプルを再実行する場合は、出力ファイルの名前を変更する必要があります。

##Java ソース コード

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

## 次のステップ

* [Azure HDInsight の概要][hdinsight-get-started]
* [HDInsight での Hadoop 用 Java MapReduce プログラムの開発](hdinsight-develop-deploy-java-mapreduce.md)
* [HDInsight での Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md)
* [サンプル: 10 GB GraySort][hdinsight-sample-10gb-graysort]
* [サンプル: Pi 推定][hdinsight-sample-pi-estimator]
* [サンプル: C ストリーミング][hdinsight-sample-cs-streaming]

[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-cs-streaming]: hdinsight-sample-csharp-streaming.md


[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png

<!---HONumber=Oct15_HO4-->