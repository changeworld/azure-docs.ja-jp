<properties 
	pageTitle="HDInsight での Hadoop MapReduce の使用 | Azure" 
	description="HDInsight を使用して、単純な Hadoop MapReduce ジョブを実行する方法について説明します。" 
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
	ms.date="11/12/2014" 
	ms.author="jgao"/>



# HDInsight での Hadoop MapReduce の使用

Hadoop MapReduce は、膨大なデータを処理するアプリケーションを記述するためのソフトウェア フレームワークです。このチュートリアルでは、ワークステーションから Azure PowerShell を使用して、テキスト内の単語の出現回数をカウントする MapReduce プログラムを HDInsight クラスターに送信します。このワードカウント プログラムは Java で記述され、HDInsight クラスターに付属します。


**前提条件:**

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- HDInsight クラスター。クラスターを作成するさまざまな方法については、[HDInsight クラスターのプロビジョニング][hdinsight-provision]　を参照してください。

- Azure PowerShell がインストールされ構成されたワークステーション。手順については、[Azure PowerShell のインストールおよび構成方法][powershell-install-configure]　を参照してください。

## このチュートリアルの内容
1. [シナリオの理解](#scenario)
2. [Azure PowerShell を使用したサンプルの実行](#run-sample)	
3. [ワードカウント MapReduce プログラムの Java コード](#java-code)
4. [次のステップ](#next-steps)	

## <a id="scenario"></a>シナリオの理解

次の図は、MapReduce が単語数をカウントするしくみを示しています。

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]



MapReduce ジョブの出力は、"キーと値" から成る一連のペアです。キーは単語を指定する文字列。値は、その単語がテキスト内に出現した合計回数です。この処理は 2 段階で実行されます。 

* まず、Mapper が入力テキストから各行を読み込み、単語に分解して、単語が出現するたびに "キー/値" ペア (キーは単語、値は 1) を発行します。出力は reducer に送信する前に並べ替えられます。 

* Reducer は、Mapper が発行した各単語のカウントを合計し、単語と合計出現回数から成る "キー/値" ペアを生成します。

MapReduce ジョブを実行するには次の要素が必要です。

* MapReduce プログラム。このチュートリアルでは、HDInsight クラスターに付属するワード カウント サンプルを使用するため、自分で書く必要はありません。プログラムは */example/jars/hadoop-examples.jar* に格納されています。バージョン 3.0 の HDInsight クラスターでは、このファイルの名前は *hadoop-mapreduce-examples.jar* です。独自の MapReduce ジョブの作成手順については、[HDInsight 用 Java MapReduce プログラムの開発][hdinsight-develop-MapReduce-jobs]を参照してください。
* 入力ファイル。*/example/data/gutenberg/davinci.txt* を入力ファイルとして使用します。ファイルのアップロードについては、[データを HDInsight へアップロードする方法][hdinsight-upload-data]を参照してください。
* 出力ファイル フォルダー。*/example/data/WordCountOutput* を出力ファイル フォルダーとして使用します。フォルダーが存在しない場合は自動的に作成されます。このフォルダーが存在していると、MapReduce ジョブは失敗します。  2 回目に MapReduce ジョブを実行する場合は、出力フォルダーを削除するか、別の出力フォルダーを指定してください。

	
## <a id="run-sample"></a>Azure PowerShell を使用したサンプルの実行

1.	**Azure PowerShell** を開きます。Azure PowerShell コンソール ウィンドウを開く手順については、[Azure PowerShell のインストールと構成に関するページ][powershell-install-configure]を参照してください。

3. 次のコマンドを実行して、2 つの変数を設定します。
		
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name
4. 次のコマンドを実行し、Azure のアカウント情報を指定します。

		Add-AzureAccount
		
5. 次のコマンドを実行して、MapReduce ジョブ定義を作成します。

		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

	> [AZURE.NOTE] *hadoop-examples.jar* は、バージョン 2.1 の HDInsight クラスターに付属しています。バージョン 3.0 の HDInsight クラスターで、このファイルの名前は *hadoop-mapreduce.jar*  に変更されました
	
	hadoop-examples.jar ファイルは HDInsight クラスターのディストリビューションに付属しています。MapReduce ジョブには引数が 2 つあります。最初の引数はソース ファイル名で、2 つ目の引数は出力ファイル パスです。ソース ファイルは HDInsight クラスターのディストリビューションに付属しており、出力ファイル パスは実行時に作成されます。

6. 次のコマンドを実行して、MapReduce ジョブを送信します。

		# Submit the job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	MapReduce ジョブ定義に加えて、MapReduce ジョブを実行する HDInsight クラスター名、および資格情報も指定します。Start-AzureHDInsightJob は非同期呼び出しです。ジョブの完了を確認するには、 *Wait-AzureHDInsightJob*コマンドレットを使用します。

7. 次のコマンドを実行して、MapReduce ジョブの完了を確認します。

		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

8. 次のコマンドを実行して、MapReduce ジョブの実行中に発生したエラーを確認します。	
	
		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 
		
**MapReduce ジョブの結果を取得するには**

1. **Azure PowerShell** を開きます。
2. 次のコマンドを実行して、ディレクトリを c:\ のルートに変更します。

		cd \

	既定の Azure Powershell ディレクトリは *C:\Windows\System32\WindowsPowerShell\v1.0*です。既定では、このフォルダーに対する書き込みアクセス許可がありません。ディレクトリを、C:\ のルート ディレクトリまたは書き込みアクセス許可があるフォルダーに変更する必要があります。

2. 次のコマンドを実行して、3 つの変数を設定します。

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"			   # Blob storage container name

		The Azure Storage account is the one you created earlier in the tutorial. The storage account is used to host the Blob container that is used as the default HDInsight cluster file system.  The Blob storage container name usually share the same name as the HDInsight cluster unless you specify a different name when you provision the cluster.

3. 次のコマンドを実行して、Azure Storage のコンテキスト オブジェクトを作成します。
		
		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	 *Select-AzureSubscription* は、サブスクリプションが複数あり、使用するサブスクリプションが既定のサブスクリプションではない場合に備えて、現在のサブスクリプションを設定するために使用します。 

4. 次のコマンドを実行して、MapReduce ジョブの出力を BLOB コンテナーからコンピューターにダウンロードします。

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	*example/data/WordCountOutput* フォルダーは、MapReduce ジョブの実行時に指定した出力フォルダーです。 *part-r-00000*　は MapReduce ジョブの出力の既定のファイル名です。  ファイルはフォルダー構造を保ったままローカル フォルダーにダウンロードされます。たとえば、次のスクリーンショットでは、現在のフォルダーが C ドライブのルート フォルダーです。  ファイルは *C:\example\data\WordCountOutput\* フォルダーにダウンロードされます。 

5. 次のコマンドを実行して、MapReduce ジョブの出力ファイルの内容を表示します。

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"


	MapReduce ジョブは、単語と出現回数が記録された  *part-r-00000* という名前のファイルを作成します。  スクリプトでは findstr コマンドを使用して、*"there"* を含む単語をすべて表示しています。


MapReduce ジョブの出力ファイルは不変であることに注意してください。そのため、このサンプルを再実行する場合は、出力ファイルの名前を変更する必要があります。

## <a id="java-code"></a>ワードカウント MapReduce プログラムの Java コード

以下に、ワード カウント Java MapReduce プログラムのソース コードを示します。
 
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
 


## <a id="nextsteps"></a>次のステップ
MapReduce は高度な診断機能を備えていますが、使いこなすのは少し難しいツールです。Pig や Hive のような他の言語を使用した方が、HDInsight に保存されているデータを容易に操作できます。詳細については、次の記事を参照してください。

* [Azure の HDInsight の概要][hdinsight-get-started]
* [HDInsight 用 Java MapReduce プログラムの開発][hdinsight-develop-MapReduce-jobs]
* [HDInsight 用 C# Hadoop ストリーミング MapReduce プログラムの開発][hdinsight-develop-streaming]
* [HDInsight での Hive の使用][hdinsight-use-hive]
* [HDInsight での Pig の使用][hdinsight-use-pig] 
* [HDInsight のサンプルを実行する][hdinsight-samples]


[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-develop-mapreduce-jobs]: ../hdinsight-develop-deploy-java-mapreduce/
[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-samples]: ../hdinsight-run-samples/
[hdinsight-provision]: ../hdinsight-provision-clusters/

[powershell-install-configure]: ../install-and-configure-powershell/

[image-hdi-wordcountdiagram]: ./media/hdinsight-get-started/HDI.WordCountDiagram.gif





<!--HONumber=42-->
