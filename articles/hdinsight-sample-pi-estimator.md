<properties 
	pageTitle="HDInsight での Pi 推定 Hadoop サンプル | Azure" 
	description="HDInsight で Hadoop MapReduce サンプルを実行する方法について説明します。" 
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
	ms.date="11/10/2014" 
	ms.author="bradsev"/>

# HDInsight での Pi 推定 Hadoop サンプル
 
このトピックでは、Azure PowerShell を使用して数学定数パイ (Pi) の値を推定する Hadoop MapReduce プログラムを HDInsight で実行する方法を紹介します。また、Pi の値を計算するための MapReduce プログラムで使用する Java コードについても説明します。

プログラムでは統計的手法 (準モンテカルロ法) に基づいて Pi の値を計算します。単位正方形の内部にランダムに配置された点は、その正方形に内接する円の内部にも円の面積に等しい確率 (Pi/4) で配置されます。Pi の値は 4R という値で計算されます。ここで R は、正方形の内部にある点の総数と、円の内部にある点の数との比率です。サンプルの点の数が大きくなるほど、推定値の精度が上がります。

mapper 関数と reducer 関数を含む PiEstimator Java コードを次に挙げます。mapper プログラムは、指定された数の点を生成して単位正方形内にランダムに配置して、円の内部にある点の数を計算します。reducer プログラムは、mapper が計算した点の数を累計して、4R という公式から Pi の値を推定します。ここで R は、正方形の内部にある点の総数と、円の内部にある点の数との比率です。

このサンプルで示したスクリプトでは、Hadoop JAR ジョブを送信し、マップ数を 16 として実行し、それぞれがパラメーター値として指定された 1,000 万個のサンプル点を計算します。このパラメーター値を変更すると、Pi の推定値の精度を高めることができます。参考のために、Pi の小数点以下 10 桁までは 3.1415926535 です。

Hadoop on Azure でアプリケーションを展開するときに必要なファイルを含む jar ファイルは zip ファイルでダウンロードできます。さまざまな圧縮ユーティリティを使ってファイルの圧縮を解除して、自由にファイルを調べることができます。

HDInsight を使用して MapReduce ジョブをすばやく実行するのに役立つその他のサンプルは、その実行方法のリンクと共に、「[HDInsight サンプルの実行][hdinsight-samples]」に挙げられています。

**学習内容:**
		
* Azure PowerShell を使用して Pi 推定 MapReduce プログラムを Azure HDInsight で実行する方法。	
* Java で記述された MapReduce プログラムの実例。

**前提条件**:	

- Azure アカウントが必要です。アカウントにサインアップする方法については、[Azure の無料評価版のページ](http://azure.microsoft.com/ja-jp/pricing/free-trial/)を参照してください。

- HDInsight クラスターのプロビジョニングを終えている必要があります。クラスターを作成するさまざまな方法については、「[HDInsight クラスターのプロビジョニング](../hdinsight-provision-clusters/)」を参照してください。

- Azure PowerShell をインストールして、アカウントを使用するように構成している必要があります。その手順については、「[Azure PowerShell のインストールおよび構成方法][powershell-install-configure]」を参照してください。

## この記事の内容	
このトピックでは、サンプルを実行する方法について説明し、Pi 推定 MapReduce プログラムの Java コードを示し、説明した内容をまとめ、次の手順の概略を示します。ここで取り上げる内容は次のとおりです。
	
1. [Azure PowerShell を使用したサンプルの実行](#run-sample)	
2. [Pi 推定 MapReduce プログラムの Java コード](#java-code)
3. [まとめ](#summary)	
4. [次のステップ](#next-steps)	

<h2><a id="run-sample"></a>Azure PowerShell を使用したサンプルの実行</h2>

**MapReduce ジョブを送信するには**

1. Azure PowerShell を開きます。Azure PowerShell コンソール ウィンドウを開く手順については、「[Azure PowerShell のインストールおよび構成方法][powershell-install-configure]」を参照してください。
2. 次のコマンドを実行して、2 つの変数を設定します。
	
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name

3. 次のコマンドを実行して、MapReduce ジョブ定義を作成します。	

		$piEstimatorJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "pi" -Arguments "16", "10000000" 

	
	最初の引数は作成するマップの数を示します (既定値は 16)。2 つ目の引数はマップごとに生成するサンプル数を示します (既定値は 1000 万)。つまりこのプログラムでは、Pi を推定するために 16 × 1,000 万 = 1 億 6,000 万のランダム ポイントが使用されます。3 つ目の引数は HDInsight 3.0 および 3.1 クラスターでサンプルを実行するときに使用される .jar ファイルの場所と名前を示します(このファイルの内容として以下の内容を使用します)。

4. 次のコマンドを実行して、MapReduce ジョブを送信し、ジョブの完了を待ちます。

		# Run the Pi Estimator MapReduce job.
		Select-AzureSubscription $subscriptionName
		$piJob = $piEstimatorJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName
	
		# Wait for the job to complete.  
		$piJob | Wait-AzureHDInsightJob -Subscription $subscriptionName -WaitTimeoutInSeconds 3600  

5. 次のコマンドを実行して、MapReduce ジョブの標準出力を取得します。

		# Print output and standard error file of the MapReduce job
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $piJob.JobId -StandardOutput

	比較のために、Pi の小数点以下 10 桁までは 3.1415926535 です。


<h2><a id="java-code"></a>Pi 推定 MapReduce プログラムの Java コード</h2>



 	/**	
 	* Licensed to the Apache Software Foundation (ASF) under one	
 	* or more contributor license agreements. See the NOTICE file	
 	* distributed with this work for additional information	
 	* regarding copyright ownership. The ASF licenses this file	
 	* to you under the Apache License, Version 2.0 (the	
 	* "License"); you may not use this file except in compliance	
 	* with the License. You may obtain a copy of the License at	
 	*	
	* http://www.apache.org/licenses/LICENSE-2.0	
 	*	
 	* Unless required by applicable law or agreed to in writing, software	
 	* distributed under the License is distributed on an "AS IS" BASIS,	
 	* WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or 	implied.
 	* See the License for the specific language governing permissions and	
 	* limitations under the License.		
 	*/	

 	package org.apache.hadoop.examples;

 	import java.io.IOException;
 	import java.math.BigDecimal;
 	import java.util.Iterator;

 	import org.apache.hadoop.conf.Configured;
 	import org.apache.hadoop.fs.FileSystem;
 	import org.apache.hadoop.fs.Path;
 	import org.apache.hadoop.io.BooleanWritable;
 	import org.apache.hadoop.io.LongWritable;
 	import org.apache.hadoop.io.SequenceFile;
 	import org.apache.hadoop.io.Writable;
 	import org.apache.hadoop.io.WritableComparable;
 	import org.apache.hadoop.io.SequenceFile.CompressionType;
 	import org.apache.hadoop.mapred.FileInputFormat;
 	import org.apache.hadoop.mapred.FileOutputFormat;
 	import org.apache.hadoop.mapred.JobClient;
 	import org.apache.hadoop.mapred.JobConf;
 	import org.apache.hadoop.mapred.MapReduceBase;
 	import org.apache.hadoop.mapred.Mapper;
 	import org.apache.hadoop.mapred.OutputCollector;
 	import org.apache.hadoop.mapred.Reducer;
 	import org.apache.hadoop.mapred.Reporter;
 	import org.apache.hadoop.mapred.SequenceFileInputFormat;
 	import org.apache.hadoop.mapred.SequenceFileOutputFormat;
 	import org.apache.hadoop.util.Tool;
 	import org.apache.hadoop.util.ToolRunner;


	//A Map-reduce program to estimate the value of Pi	
	//using quasi-Monte Carlo method.	
	//	
	//Mapper:	
	//Generate points in a unit square	
	//and then count points inside/outside of the inscribed circle of the square.	
	//	
	//Reducer:	
	//Accumulate points inside/outside results from the mappers.	
	//Let numTotal = numInside + numOutside.	
	//The fraction numInside/numTotal is a rational approximation of	
	//the value (Area of the circle)/(Area of the square),	
	//where the area of the inscribed circle is Pi/4	
	//and the area of unit square is 1.	
	//Then, Pi is estimated value to be 4(numInside/numTotal). 	
	//	

 	public class PiEstimator extends Configured implements Tool {	
	//tmp directory for input/output	
 	static private final Path TMP_DIR = new Path(	
 	PiEstimator.class.getSimpleName() + "_TMP_3_141592654");	

	//2-dimensional Halton sequence {H(i)},		
	//where H(i) is a 2-dimensional point and i >= 1 is the index.		
	//Halton sequence is used to generate sample points for Pi estimation.	 
 	private static class HaltonSequence {	
	// Bases	
 	static final int[] P = {2, 3}; 	
	//Maximum number of digits allowed	
 	static final int[] K = {63, 40}; 	

 	private long index;	
 	private double[] x;	
 	private double[][] q;	
 	private int[][] d;	

	//Initialize to H(startindex),	
	//so the sequence begins with H(startindex+1).	
 	HaltonSequence(long startindex) {	
 	index = startindex;	
 	x = new double[K.length];	
 	q = new double[K.length][];	
 	d = new int[K.length][];	
 	for(int i = 0; i < K.length; i++) {	
 	q[i] = new double[K[i]];	
 	d[i] = new int[K[i]];	
 	}

 	for(int i = 0; i < K.length; i++) {
 	long k = index;
 	x[i] = 0;
	
 	for(int j = 0; j < K[i]; j++) {	
 	q[i][j] = (j == 0? 1.0: q[i][j-1])/P[i];	
 	d[i][j] = (int)(k % P[i]);	
 	k = (k - d[i][j])/P[i];	
 	x[i] += d[i][j] * q[i][j];	
 	}	
 	}
 	}

	//Compute next point.	
	//Assume the current point is H(index).	
	//Compute H(index+1).	
	//@return a 2-dimensional point with coordinates in [0,1)^2		
 	double[] nextPoint() {		
 	index++;	
 	for(int i = 0; i < K.length; i++) {	
 	for(int j = 0; j < K[i]; j++) {	
 	d[i][j]++;	
 	x[i] += q[i][j];	
 	if (d[i][j] < P[i]) {	
 	break;		
 	}	
 	d[i][j] = 0;	
 	x[i] -= (j == 0? 1.0: q[i][j-1]);	
 	}	
 	}	
 	return x;	
 	}	
 	}	

	//Mapper class for Pi estimation.	
	//Generate points in a unit square and then		
	//count points inside/outside of the inscribed circle of the square.	
 	public static class PiMapper extends MapReduceBase
 	implements Mapper<LongWritable, LongWritable, BooleanWritable, LongWritable> {

	//Map method.	
	//@param offset samples starting from the (offset+1)th sample.	
	//@param size the number of samples for this map	
	//@param out output {ture->numInside, false->numOutside}	
	//@param reporter	
 	public void map(LongWritable offset,
 	LongWritable size,
 	OutputCollector<BooleanWritable, LongWritable> out,
 	Reporter reporter) throws IOException {

 	final HaltonSequence haltonsequence = new HaltonSequence(offset.get());		
 	long numInside = 0L;	
 	long numOutside = 0L;	

 	for(long i = 0; i < size.get(); ) {
 	//generate points in a unit square
 	final double[] point = haltonsequence.nextPoint();

 	//count points inside/outside of the inscribed circle of the square
 	final double x = point[0] - 0.5;
 	final double y = point[1] - 0.5;
 	if (x*x + y*y > 0.25) {
 	numOutside++;
 	} else {
 	numInside++;
 	}

 	//report status
 	i++;
 	if (i % 1000 == 0) {
 	reporter.setStatus("Generated " + i + " samples.");
 	}
 	}

 	//output map results
 	out.collect(new BooleanWritable(true), new LongWritable(numInside));
 	out.collect(new BooleanWritable(false), new LongWritable(numOutside));
 	}
 	}


	//Reducer class for Pi estimation.		
	//Accumulate points inside/outside results from the mappers.		
 	public static class PiReducer extends MapReduceBase
 	implements Reducer<BooleanWritable, LongWritable, WritableComparable<?>, Writable> {

 	private long numInside = 0;
 	private long numOutside = 0;
 	private JobConf conf; //configuration for accessing the file system

	//Store job configuration.	
 	@Override	
 	public void configure(JobConf job) {
 	conf = job;
 	}


	// Accumulate number of points inside/outside results from the mappers.		
	// @param isInside Is the points inside? 	
	// @param values An iterator to a list of point counts	
	// @param output dummy, not used here.	
	// @param reporter	

 	public void reduce(BooleanWritable isInside,
 	Iterator<LongWritable> values,
 	OutputCollector<WritableComparable<?>, Writable> output,
 	Reporter reporter) throws IOException {
 	if (isInside.get()) {
 	for(; values.hasNext(); numInside += values.next().get());
 	} else {
 	for(; values.hasNext(); numOutside += values.next().get());
 	}
 	}
	
 	//Reduce task done, write output to a file.	
 	@Override	
 	public void close() throws IOException {
 	//write output to a file
 	Path outDir = new Path(TMP_DIR, "out");
 	Path outFile = new Path(outDir, "reduce-out");
 	FileSystem fileSys = FileSystem.get(conf);
 	SequenceFile.Writer writer = SequenceFile.createWriter(fileSys, conf,
 	outFile, LongWritable.class, LongWritable.class, 
 	CompressionType.NONE);
 	writer.append(new LongWritable(numInside), new LongWritable(numOutside));
 	writer.close();
 	}
 	}
	
	//Run a map/reduce job for estimating Pi.	
	//@return the estimated value of Pi.		
 	public static BigDecimal estimate(int numMaps, long numPoints, JobConf jobConf
 	) 	
 	throws IOException {
 	//setup job conf
 	jobConf.setJobName(PiEstimator.class.getSimpleName());
	
 	jobConf.setInputFormat(SequenceFileInputFormat.class);
	
 	jobConf.setOutputKeyClass(BooleanWritable.class);
 	jobConf.setOutputValueClass(LongWritable.class);
 	jobConf.setOutputFormat(SequenceFileOutputFormat.class);
	
 	jobConf.setMapperClass(PiMapper.class);
 	jobConf.setNumMapTasks(numMaps);
	
 	jobConf.setReducerClass(PiReducer.class);
 	jobConf.setNumReduceTasks(1);
	
 	// turn off speculative execution, because DFS doesn't handle		
 	// multiple writers to the same file.		
 	jobConf.setSpeculativeExecution(false);
	
 	//setup input/output directories	
 	final Path inDir = new Path(TMP_DIR, "in");	
 	final Path outDir = new Path(TMP_DIR, "out");	
 	FileInputFormat.setInputPaths(jobConf, inDir);	
 	FileOutputFormat.setOutputPath(jobConf, outDir);	
	
 	final FileSystem fs = FileSystem.get(jobConf);		
 	if (fs.exists(TMP_DIR)) {	
	 throw new IOException("Tmp directory " + fs.makeQualified(TMP_DIR)
	 + " already exists. Please remove it first.");	
	 }	
	 if (!fs.mkdirs(inDir)) {	
	 throw new IOException("Cannot create input directory " + inDir);	
	 }	

	 //generate an input file for each map task		
	 try {
	 for(int i=0; i < numMaps; ++i) {
	 final Path file = new Path(inDir, "part"+i);	
	 final LongWritable offset = new LongWritable(i * numPoints);	
	 final LongWritable size = new LongWritable(numPoints);	
	 final SequenceFile.Writer writer = SequenceFile.createWriter(
	 fs, jobConf, file,
	 LongWritable.class, LongWritable.class, CompressionType.NONE);
	 try {
	 writer.append(offset, size);
	 } finally {
	 writer.close();	
	 }
	 System.out.println("Wrote input for Map #"+i);
	 }
	
	 //start a map/reduce job		
	 System.out.println("Starting Job");
	 final long startTime = System.currentTimeMillis();
	 JobClient.runJob(jobConf);
	 final double duration = (System.currentTimeMillis() - startTime)/1000.0;
	 System.out.println("Job Finished in " + duration + " seconds");
	
	 //read outputs		
	 Path inFile = new Path(outDir, "reduce-out");
	 LongWritable numInside = new LongWritable();
	 LongWritable numOutside = new LongWritable();
	 SequenceFile.Reader reader = new SequenceFile.Reader(fs, inFile, jobConf);
	 try {
	 reader.next(numInside, numOutside);
	 } finally {
	 reader.close();
	 }
	
	 //compute estimated value
	 return BigDecimal.valueOf(4).setScale(20)
	 .multiply(BigDecimal.valueOf(numInside.get()))
	 .divide(BigDecimal.valueOf(numMaps))
	 .divide(BigDecimal.valueOf(numPoints));
	 } finally {
	 fs.delete(TMP_DIR, true);
	 }
	 }
	
	//Parse arguments and then runs a map/reduce job.	
	//Print output in standard out.		
	//@return a non-zero if there is an error. Otherwise, return 0. 	
	 public int run(String[] args) throws Exception {
	 if (args.length != 2) {
	 System.err.println("Usage: "+getClass().getName()+" <nMaps> <nSamples>");
	 ToolRunner.printGenericCommandUsage(System.err);
	 return -1;
	 }

	 final int nMaps = Integer.parseInt(args[0]);
	 final long nSamples = Long.parseLong(args[1]);
	
	 System.out.println("Number of Maps = " + nMaps);
	 System.out.println("Samples per Map = " + nSamples);
	
	 final JobConf jobConf = new JobConf(getConf(), getClass());
	 System.out.println("Estimated value of Pi is "
	 + estimate(nMaps, nSamples, jobConf));
	 return 0;
	 }
	
	 //main method for running it as a stand alone command. 		
	 public static void main(String[] argv) throws Exception {
	 System.exit(ToolRunner.run(null, new PiEstimator(), argv));
	 }
	 }
  

<h2><a id="summary"></a>まとめ</h2>

このチュートリアルでは、HDInsight 上で MapReduce ジョブを実行し、このサービスで管理できる大量のデータ セットを生成するモンテカルロ法を使用する方法を説明しました。

次に示すのは、既定の HDInsight 3.1 クラスターまたは 3.0 クラスターでこのサンプルを実行するときに使用する完全なスクリプトです。 

	### Provide the Windows Azure subscription name and the HDInsight cluster name. 
	$subscriptionName = "<SubscriptionName>" 
	$clusterName = "<ClusterName>"  

	###Select the Azure subscription to use.
	Select-AzureSubscription $subscriptionName 

	### Create a MapReduce job definition. 
	$piEstimatorJobDefinition = New-AzureHDInsightMapReduceJobDefinition -ClassName "pi" -Arguments "32", "1000000000" -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar"

	### Run the MapReduce job. 
	$piJob = $piEstimatorJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName
 
	### Wait for the job to complete.  
	$piJob | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600

	### Print the standard error file of the MapReduce job.
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $piJob.JobId -StandardOutput



<h2><a id="next-steps"></a>次のステップ</h2>

Azure PowerShell を使用して Azure HDInsight 上で他のサンプルを実行するチュートリアルや、Pig、Hive、MapReduce の使用方法に関するチュートリアルについては、次のトピックを参照してください。

* [Azure の HDInsight の概要][hdinsight-get-started]
* [サンプル:10 GB GraySort][hdinsight-sample-10gb-graysort]
* [サンプル:ワードカウント][hdinsight-sample-wordcount]
* [サンプル:C# ストリーミング][hdinsight-sample-csharp-streaming]
* [HDInsight での Pig の使用][hdinsight-use-pig]
* [HDInsight での Hive の使用][hdinsight-use-hive]
* [Azure の HDInsight SDK ドキュメント][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/ja-jp/library/dn479185.aspx

[Powershell-install-configure]: ../install-configure-powershell/

[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-samples]: ../hdinsight-run-samples/
[hdinsight-sample-10gb-graysort]: ../hdinsight-sample-10gb-graysort/
[hdinsight-sample-csharp-streaming]: ../hdinsight-sample-csharp-streaming/
[hdinsight-sample-pi-estimator]: ../hdinsight-sample-pi-estimator/
[hdinsight-sample-wordcount]: ../hdinsight-sample-wordcount/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
 


<!--HONumber=42-->
