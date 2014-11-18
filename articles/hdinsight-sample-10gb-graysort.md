<properties urlDisplayName="Hadoop Samples in HDInsight" pageTitle="10 GB GraySort サンプル | Azure" metaKeywords="hdinsight, hadoop, hdinsight administration, hdinsight administration azure" description="このサンプル トピックでは、Azure PowerShell を使用して、HDInsight で Hadoop の汎用的な GraySort を実行する方法について説明します。" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="10 GB GraySort サンプル" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# HDInsight での 10 GB GraySort Hadoop サンプル

このサンプル トピックでは、Azure PowerShell を使用して、Azure HDInsight で汎用 GraySort Hadoop MapReduce プログラムを実行する方法について説明します。GraySort はベンチマーク ソートで、その評価尺度は、非常に大量のデータ、通常は最低でも 100 TB のデータをソートした際のソート速度 (TB/分) です。

このサンプルでは、比較的高速に実行できるように、中程度のサイズの 10 GB のデータを使用します。使用する MapReduce アプリケーションは Owen O'Malley と Arun Murthy が開発したもので、2009 年にはテラバイト ソート ベンチマークの汎用目的 ("daytona") 部門で 0.578 TB/分 (173 分で 100 TB) という年間記録を樹立しました。これも含めたソート ベンチマークの詳細については、[Sortbenchmark][Sortbenchmark] サイトを参照してください。

このサンプルでは 3 組の MapReduce プログラムを使用します。

1.  **TeraGen** は、ソートするデータ行を生成するのに使用できる MapReduce プログラムです。
2.  **TeraSort** は入力データをサンプリングし、MapReduce を使用してデータを合計順にソートします。TeraSort は MapReduce 関数の標準ソートです。ただし、各 reduce のキー範囲を定義する N-1 個のサンプリングされたキーのソート済みリストを使用するカスタム partitioner を使用します。特に、sample[i-1] \<= key \< sample[i] となるキーはすべて reduce i に送られます。このため、reduce i の出力がすべて reduce i+1 の出力より小さくなることが保証されます。
3.  **TeraValidate** は、出力がグローバルにソートされているか検証する MapReduce プログラムです。出力ディレクトリ内のファイルごとにマップを 1 つ作成します。各マップは各キーが前のキー以下であることを保証します。map 関数は、各ファイルの最初のキーと最後のキーの記録も生成し、reduce 関数はファイル i の最初のキーがファイル i-1 の最後のキーよりも大きいことを保証します。問題が発生した場合は、範囲外のキーを持つ reduce の出力としてレポートされます。

3 つのアプリケーションすべてで使用される入力形式と出力形式は、適切な形式のテキスト ファイルを読み書きします。ベンチマーク コンテストでは出力データを複数のノードにレプリケーションする必要がないため、reduce の出力ではレプリケーションが既定の 3 ではなく 1 に設定されます。

**学習内容:**

-   Azure PowerShell を使用して一連の MapReduce プログラムを Azure HDInsight で実行する方法。
-   Java で記述された MapReduce プログラムの実例。

**前提条件**:

-   Azure アカウントが必要です。アカウントにサインアップする方法については、[Azure の無料評価版のページ][Azure の無料評価版のページ]を参照してください。

-   HDInsight クラスターのプロビジョニングを終えている必要があります。クラスターを作成するさまざまな方法については、「[HDInsight クラスターのプロビジョニング][HDInsight クラスターのプロビジョニング]」を参照してください。

-   Azure PowerShell をインストールして、アカウントを使用するように構成している必要があります。その手順については、「[Azure PowerShell のインストールおよび構成][Azure PowerShell のインストールおよび構成]」を参照してください。

## この記事の内容

このトピックでは、サンプルを構成する一連の MapReduce プログラムを実行する方法を説明し、MapReduce プログラムの Java コードを示し、説明した内容をまとめ、次の手順の概略を示します。ここで取り上げる内容は次のとおりです。

1.  [Azure PowerShell を使用したサンプルの実行][Azure PowerShell を使用したサンプルの実行]
2.  [TeraSort MapReduce プログラムの Java コード][TeraSort MapReduce プログラムの Java コード]
3.  [まとめ][まとめ]
4.  [次のステップ][次のステップ]

## <span id="run-sample"></span></a>Azure PowerShell を使用したサンプルの実行

サンプルでは 3 つのタスクを実行する必要があります。各タスクが、先ほど説明した MapReduce プログラムに対応しています。

1.  **TeraGen** MapReduce ジョブを実行して、ソート用のデータを生成します。
2.  **TeraSort** MapReduce ジョブを実行して、データをソートします。
3.  **TeraValidate** MapReduce ジョブを実行して、データが正しくソートされていることを確認します。

**TeraGen プログラムを実行するには**

1.  Azure PowerShell を開きます。Azure PowerShell コンソール ウィンドウを開く手順については、[Azure PowerShell のインストールと構成に関するページ][Azure PowerShell のインストールおよび構成]を参照してください。
2.  次のコマンドを実行して、2 つの変数を設定します。

        # Provide the Azure subscription name and the HDInsight cluster name.
        $subscriptionName = "myAzureSubscriptionName"   
        $clusterName = "myClusterName"

3.  次のコマンドを実行して、MapReduce ジョブ定義を作成します。

        # Create a MapReduce job definition for the TeraGen MapReduce program
        $teragen = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-examples.jar" -ClassName "teragen" -Arguments "-Dmapred.map.tasks=50", "100000000", "/example/data/10GB-sort-input" 

    > [WACOM.NOTE] *hadoop-examples.jar* は、Version 2.1 の HDInsight クラスターに付属しています。バージョン 3.0 の HDInsight クラスターで、このファイルの名前は *hadoop-mapreduce.jar* に変更されました

    "*-Dmapred.map.tasks=50*" 引数は、ジョブを実行するために 50 個のマップを作成することを指定しています。*100000000* 引数は、生成するデータの量を指定しています。最後の引数 */example/data/10GB-sort-input* は、結果を保存する出力ディレクトリを指定しています (これが次のソート段階用の入力になります)。

4.  次のコマンドを実行して、ジョブを送信し、ジョブの完了を待ち、標準エラーを出力します。

        # Run the TeraGen MapReduce job.
        # Wait for the job to complete.
        # Print output and standard error file of the MapReduce job
        Select-AzureSubscription $subscriptionName         
        $teragen | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

**TeraSort プログラムを実行するには**

1.  Azure PowerShell を開きます。
2.  次のコマンドを実行して、2 つの変数を設定します。

        # Provide the Azure subscription name and the HDInsight cluster name.
        $subscriptionName = "myAzureSubscriptionName"   
        $clusterName = "myClusterName"

3.  次のコマンドを実行して、MapReduce ジョブを定義します。

        # Create a MapReduce job definition for the TeraSort MapReduce program
        $terasort = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-examples.jar" -ClassName "terasort" -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-input", "/example/data/10GB-sort-output" 

    "*-Dmapred.map.tasks=50*" 引数は、ジョブを実行するために 50 個のマップを作成することを指定しています。*100000000* 引数は、生成するデータの量を指定しています。最後の 2 つの引数は、入力ディレクトリと出力ディレクトリを指定しています。

4.  次のコマンドを実行して、ジョブを送信し、ジョブの完了を待ち、標準エラーを出力します。

        # Run the TeraSort MapReduce job.
        # Wait for the job to complete.
        # Print output and standard error file of the MapReduce job 
        Select-AzureSubscription $subscriptionName        
        $terasort | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

**TeraValidate プログラムを実行するには**

1.  Azure PowerShell を開きます。
2.  次のコマンドを実行して、2 つの変数を設定します。

        # Provide the Azure subscription name and the HDInsight cluster name.
        $subscriptionName = "myAzureSubscriptionName"   
        $clusterName = "myClusterName"

3.  次のコマンドを実行して、MapReduce ジョブを定義します。

        #   Create a MapReduce job definition for the TeraValidate MapReduce program
        $teravalidate = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-examples.jar" -ClassName "teravalidate" -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-output", "/example/data/10GB-sort-validate" 

    "*-Dmapred.map.tasks=50*" 引数は、ジョブを実行するために 50 個のマップを作成することを指定しています。"*-Dmapred.reduce.tasks=25*" 引数は、ジョブを実行するために 25 個の reduce タスクを作成することを指定しています。最後の 2 つの引数は、入力ディレクトリと出力ディレクトリを指定しています。

4.  次のコマンドを実行して、MapReduce ジョブを送信し、ジョブの完了を待ち、標準エラーを出力します。

        # Run the TeraSort MapReduce job.
        # Wait for the job to complete.
        # Print output and standard error file of the MapReduce job 
        Select-AzureSubscription $subscriptionName 
        $teravalidate | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError 

## <span id="java-code"></span></a>TeraSort MapReduce プログラムの Java コード

このセクションでは、内容を確認するために TeraSort MapReduce プログラムのコードを示します。

    /**
     * Licensed to the Apache Software Foundation (ASF) under one   
     * or more contributor license agreements.  See the NOTICE file 
     * distributed with this work for additional information    
     * regarding copyright ownership.  The ASF licenses this file   
     * to you under the Apache License, Version 2.0 (the    
     * "License"); you may not use this file except in compliance   
     * with the License.  You may obtain a copy of the License at   
     *  
     *     http://www.apache.org/licenses/LICENSE-2.0   
     *  
     * Unless required by applicable law or agreed to in writing, software  
     * distributed under the License is distributed on an "AS IS" BASIS,    
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. 
     * See the License for the specific language governing permissions and  
     * limitations under the License.   
     */ 

    package org.apache.hadoop.examples.terasort;

    import java.io.IOException;
    import java.io.PrintStream;
    import java.net.URI;
    import java.util.ArrayList;
    import java.util.List;

    import org.apache.commons.logging.Log;
    import org.apache.commons.logging.LogFactory;
    import org.apache.hadoop.conf.Configured;
    import org.apache.hadoop.filecache.DistributedCache;
    import org.apache.hadoop.fs.FileSystem;
    import org.apache.hadoop.fs.Path;
    import org.apache.hadoop.io.NullWritable;
    import org.apache.hadoop.io.SequenceFile;
    import org.apache.hadoop.io.Text;
    import org.apache.hadoop.mapred.FileOutputFormat;
    import org.apache.hadoop.mapred.JobClient;
    import org.apache.hadoop.mapred.JobConf;
    import org.apache.hadoop.mapred.Partitioner;
    import org.apache.hadoop.util.Tool;
    import org.apache.hadoop.util.ToolRunner;

    /** 
     * Generates the sampled split points, launches the job,    
     * and waits for it to finish.  
     * <p>
     * To run the program:  
     * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>  
     */ 

    public class TeraSort extends Configured implements Tool {
      private static final Log LOG = LogFactory.getLog(TeraSort.class);

      /**   
       * A partitioner that splits text keys into roughly equal     
       * partitions in a global sorted order.   
       */   

      static class TotalOrderPartitioner implements Partitioner<Text,Text>{
        private TrieNode trie;
        private Text[] splitPoints;

        /**
         * A generic trie node
         */
        static abstract class TrieNode {
          private int level;
          TrieNode(int level) {
            this.level = level;
          }
          abstract int findPartition(Text key);
          abstract void print(PrintStream strm) throws IOException;
          int getLevel() {
            return level;
          }
        }

        /**
         * An inner trie node that contains 256 children based on the next
         * character.
         */
        static class InnerTrieNode extends TrieNode {
          private TrieNode[] child = new TrieNode[256];
          
          InnerTrieNode(int level) {
            super(level);
          }
          int findPartition(Text key) {
            int level = getLevel();
            if (key.getLength() <= level) {
              return child[0].findPartition(key);
            }
            return child[key.getBytes()[level]].findPartition(key);
          }
          void setChild(int idx, TrieNode child) {
            this.child[idx] = child;
          }
          void print(PrintStream strm) throws IOException {
            for(int ch=0; ch < 255; ++ch) {
              for(int i = 0; i < 2*getLevel(); ++i) {
                strm.print(' ');
              }
              strm.print(ch);
              strm.println(" ->");
              if (child[ch] != null) {
                child[ch].print(strm);
              }
            }
          }
        }

        /**
         * A leaf trie node that does string compares to figure out where the given
         * key belongs between lower..upper.
         */
        static class LeafTrieNode extends TrieNode {
          int lower;
          int upper;
          Text[] splitPoints;
          LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
            super(level);
            this.splitPoints = splitPoints;
            this.lower = lower;
            this.upper = upper;
          }
          int findPartition(Text key) {
            for(int i=lower; i<upper; ++i) {
              if (splitPoints[i].compareTo(key) >= 0) {
                return i;
              }
            }
            return upper;
          }
          void print(PrintStream strm) throws IOException {
            for(int i = 0; i < 2*getLevel(); ++i) {
              strm.print(' ');
            }
            strm.print(lower);
            strm.print(", ");
            strm.println(upper);
          }
        }


        /**
         * Read the cut points from the given sequence file.
         * @param fs the file system
         * @param p the path to read
         * @param job the job config
         * @return the strings to split the partitions on
         * @throws IOException
         */
        private static Text[] readPartitions(FileSystem fs, Path p, 
                                             JobConf job) throws IOException {
          SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
          List<Text> parts = new ArrayList<Text>();
          Text key = new Text();
          NullWritable value = NullWritable.get();
          while (reader.next(key, value)) {
            parts.add(key);
            key = new Text();
          }
          reader.close();
          return parts.toArray(new Text[parts.size()]);  
        }

        /**
         * Given a sorted set of cut points, build a trie that will find the correct
         * partition quickly.
         * @param splits the list of cut points
         * @param lower the lower bound of partitions 0..numPartitions-1
         * @param upper the upper bound of partitions 0..numPartitions-1
         * @param prefix the prefix that we have already checked against
         * @param maxDepth the maximum depth we will build a trie for
         * @return the trie node that will divide the splits correctly
         */
        private static TrieNode buildTrie(Text[] splits, int lower, int upper, 
                                          Text prefix, int maxDepth) {
          int depth = prefix.getLength();
          if (depth >= maxDepth || lower == upper) {
            return new LeafTrieNode(depth, splits, lower, upper);
          }
          InnerTrieNode result = new InnerTrieNode(depth);
          Text trial = new Text(prefix);
          // append an extra byte on to the prefix
          trial.append(new byte[1], 0, 1);
          int currentBound = lower;
          for(int ch = 0; ch < 255; ++ch) {
            trial.getBytes()[depth] = (byte) (ch + 1);
            lower = currentBound;
            while (currentBound < upper) {
              if (splits[currentBound].compareTo(trial) >= 0) {
                break;
              }
              currentBound += 1;
            }
            trial.getBytes()[depth] = (byte) ch;
            result.child[ch] = buildTrie(splits, lower, currentBound, trial, 
                                         maxDepth);
          }
          // pick up the rest
          trial.getBytes()[depth] = 127;
          result.child[255] = buildTrie(splits, currentBound, upper, trial,
                                        maxDepth);
          return result;
        }

        public void configure(JobConf job) {
          try {
            FileSystem fs = FileSystem.getLocal(job);
            Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
            splitPoints = readPartitions(fs, partFile, job);
            trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
          } catch (IOException ie) {
            throw new IllegalArgumentException("can't read paritions file", ie);
          }
        }

        public TotalOrderPartitioner() {
        }

        public int getPartition(Text key, Text value, int numPartitions) {
          return trie.findPartition(key);
        }
        
      }
      
      public int run(String[] args) throws Exception {
        LOG.info("starting");
        JobConf job = (JobConf) getConf();
        Path inputDir = new Path(args[0]);
        inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
        Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
        URI partitionUri = new URI(partitionFile.toString() +
                                   "#" + TeraInputFormat.PARTITION_FILENAME);
        TeraInputFormat.setInputPaths(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        job.setJobName("TeraSort");
        job.setJarByClass(TeraSort.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(Text.class);
        job.setInputFormat(TeraInputFormat.class);
        job.setOutputFormat(TeraOutputFormat.class);
        job.setPartitionerClass(TotalOrderPartitioner.class);
        TeraInputFormat.writePartitionFile(job, partitionFile);
        DistributedCache.addCacheFile(partitionUri, job);
        DistributedCache.createSymlink(job);
        job.setInt("dfs.replication", 1);
        TeraOutputFormat.setFinalSync(job, true);
        JobClient.runJob(job);
        LOG.info("done");
        return 0;
      }

      /**
       * @param args
       */

      public static void main(String[] args) throws Exception {
        int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
        System.exit(res);
      }

    }

## <span id="summary"></span></a>まとめ

このサンプルでは、Azure HDInsight で一連の MapReduce ジョブを実行する方法を示しました。一連のジョブでは、1 つのジョブのデータ出力が次のジョブの入力になります。

## <span id="next-steps"></span></a>次のステップ

Azure PowerShell を使用して Azure HDInsight 上で他のサンプルを実行するチュートリアルや、Pig、Hive、MapReduce の使用方法に関するチュートリアルについては、次のトピックを参照してください。

-   [Azure HDInsight の概要][Azure HDInsight の概要]
-   [サンプル: Pi 推定][サンプル: Pi 推定]
-   [サンプル: ワードカウント][サンプル: ワードカウント]
-   [サンプル: C\# ストリーミング][サンプル: C\# ストリーミング]
-   [HDInsight での Pig の使用][HDInsight での Pig の使用]
-   [HDInsight での Hive の使用][HDInsight での Hive の使用]
-   [Azure HDInsight SDK のドキュメント][Azure HDInsight SDK のドキュメント]

  [Sortbenchmark]: http://sortbenchmark.org/
  [Azure の無料評価版のページ]: http://azure.microsoft.com/ja-jp/pricing/free-trial/
  [HDInsight クラスターのプロビジョニング]: ../hdinsight-provision-clusters/
  [Azure PowerShell のインストールおよび構成]: ../install-configure-powershell/
  [Azure PowerShell を使用したサンプルの実行]: #run-sample
  [TeraSort MapReduce プログラムの Java コード]: #java-code
  [まとめ]: #summary
  [次のステップ]: #next-steps
  [Azure HDInsight の概要]: ../hdinsight-get-started/
  [サンプル: Pi 推定]: ../hdinsight-sample-pi-estimator/
  [サンプル: ワードカウント]: ../hdinsight-sample-wordcount/
  [サンプル: C\# ストリーミング]: ../hdinsight-sample-csharp-streaming/
  [HDInsight での Pig の使用]: ../hdinsight-use-pig/
  [HDInsight での Hive の使用]: ../hdinsight-use-hive/
  [Azure HDInsight SDK のドキュメント]: http://msdnstage.redmond.corp.microsoft.com/ja-jp/library/dn479185.aspx
