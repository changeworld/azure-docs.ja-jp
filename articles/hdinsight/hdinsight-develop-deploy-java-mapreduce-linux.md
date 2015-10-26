<properties
	pageTitle="Linux ベースの HDInsight 用 Java MapReduce プログラムの開発 | Microsoft Azure"
	description="Java MapReduce プログラムを開発する方法と、それらのプログラムを Linux ベースの HDInsight にデプロイする方法について説明します。"
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="Blackmist"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="10/09/2015"
	ms.author="larryfr"/>

# HDInsight での Hadoop 用 Java MapReduce プログラムの開発

[AZURE.INCLUDE [pig セレクター](../../includes/hdinsight-maven-mapreduce-selector.md)]

このドキュメントでは、Apache Maven を使用して MapReduce アプリケーションを作成し、HDInsight クラスターの Linux ベースの Hadoop にこれをデプロイして実行する手順について説明します。HDInsight クラスターでの Windows ベースの Hadoop の使用の詳細については、「[HDInsight での Hadoop 用 Java MapReduce プログラムの開発 (Windows)](hdinsight-develop-deploy-java-mapreduce.md)」を参照してください。

##<a name="prerequisites"></a>前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 以降 (または同等の OpenJDK など)

- [Apache Maven](http://maven.apache.org/)

- **Azure サブスクリプション**: [Azure 無料試用版の取得](get-azure-free-trial-for-testing-hadoop-in-hdinsight.md)に関するページをご覧ください。

- **Azure CLI**: 詳細については、[Azure CLI のインストールおよび構成](../xplat-cli-install.md)に関するページをご覧ください。

##環境変数を構成する

Java と JDK をインストールするときに、次のような環境変数が設定される場合があります。ただし、これらが存在するかどうかや、システムに対して適切な値が含まれているかを確認する必要があります。

* **JAVA\_HOME** - Java ランタイム環境 (JRE) がインストールされているディレクトリを指している必要があります。たとえば、OS X、Unix、Linux システムの場合は、`/usr/lib/jvm/java-7-oracle` のような値になります。Windows の場合は、`c:\Program Files (x86)\Java\jre1.7` のような値になります。

* **PATH** - 次のパスを含む必要があります。

	* **JAVA\_HOME** または同等のパス

	* **JAVA\_HOME\\bin** または同等のパス

	* Maven がインストールされているディレクトリ

##新しい Maven プロジェクトを作成する

1. ターミナル セッションまたは開発環境のコマンド ラインから、このプロジェクトを格納する場所にディレクトリを変更します。

3. Maven でインストールされた __mvn__ コマンドを使用し、プロジェクトのスキャフォールディングを生成します。

		mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	これにより、__artifactID__ パラメーターにより指定された名前で、新しいディレクトリが現在のディレクトリに作成されます (この例では **wordcountjava**)。 このディレクトリには、次の項目が含まれます。

	* __pom.xml__ - プロジェクト オブジェクト モデル ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) には、プロジェクトのビルドに使用される情報と構成の詳細が含まれています。

	* __src__ - アプリケーションを作成する __main/java/org/apache/hadoop/examples__ ディレクトリを格納するディレクトリです。

3. __src/test/java/org/apache/hadoop/examples/apptest.java__ ファイルはこの例で使用しないため、削除します。

##依存関係を追加する

1. __pom.xml__ ファイルを編集し、`<dependencies>` セクション内に次のコードを追加します。

		<dependency>
		  <groupId>org.apache.hadoop</groupId>
	      <artifactId>hadoop-mapreduce-examples</artifactId>
	      <version>2.5.1</version>
		  <scope>provided</scope>
	    </dependency>
		<dependency>
	  	  <groupId>org.apache.hadoop</groupId>
	  	  <artifactId>hadoop-mapreduce-client-common</artifactId>
	  	  <version>2.5.1</version>
		  <scope>provided</scope>
		</dependency>
		<dependency>
	  	  <groupId>org.apache.hadoop</groupId>
	  	  <artifactId>hadoop-common</artifactId>
	  	  <version>2.5.1</version>
		  <scope>provided</scope>
		</dependency>

	このコードは、プロジェクトで特定のバージョン (&lt;version> 内に記載) のライブラリ ( &lt;artifactId> 内に記載) を必要とすることを Maven に通知しています。これはコンパイル時に、既定の Maven リポジトリからダウンロードされます。[Maven リポジトリ検索](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar)を使用して、その他の情報を表示できます。

	`<scope>provided</scope>` は、これらの依存関係はアプリケーションに付属せず、実行時に HDInsight クラスターによって提供されることを Maven に通知しています。

2. __pom.xml__ ファイルに次のコードを追加します。このコードは、ファイルの `<project>...</project>` タグ内に配置する必要があります (たとえば `</dependencies>` と `</project>` の間)。

		<build>
  		  <plugins>
    		<plugin>
      		  <groupId>org.apache.maven.plugins</groupId>
      		  <artifactId>maven-shade-plugin</artifactId>
      		  <version>2.3</version>
      		  <configuration>
        		<transformers>
          		  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
		          </transformer>
        		</transformers>
      		  </configuration>
      		  <executions>
        		<execution>
          		  <phase>package</phase>
          			<goals>
            		  <goal>shade</goal>
          			</goals>
        	    </execution>
      		  </executions>
      	    </plugin>
			<plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <configuration>
               <source>1.7</source>
               <target>1.7</target>
              </configuration>
            </plugin>
  		  </plugins>
	    </build>

	最初のプラグインは [Maven Shade Plugin](http://maven.apache.org/plugins/maven-shade-plugin/) を構成します。これは、アプリケーションで必要な依存関係を含む uberjar (fatjar と呼ばれることもある) を作成するために使用されます。また、一部のシステムで問題が発生する可能性がある jar パッケージ内のライセンスの重複を防止します。

	2 番目のプラグインは Maven コンパイラを構成します。これは、HDInsight クラスターで使用されるバージョンに、このアプリケーションで必要な Java バージョンを設定するために使用されます。

3. __pom.xml__ ファイルを保存します。

##MapReduce アプリケーションを作成する

1. __wordcountjava/src/main/java/org/apache/hadoop/examples__ ディレクトリに移動し、__App.java__ ファイルの名前を __WordCount.java__ に変更します。

2. テキスト エディターで __WordCount.java__ ファイルを開き、内容を次のように置き換えます。

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

	パッケージ名は **org.apache.hadoop.examples** で、クラス名は **WordCount** です。これらの名前は MapReduce ジョブを送信するときに使用します。

3. ファイルを保存します。

##アプリケーションのビルド

1. __wordcountjava__ ディレクトリに移動します (現在、このディレクトリではない場合)。

2. 次のコマンドを使用して、アプリケーションを含む JAR ファイルをビルドします。

		mvn clean package

	これにより、前のビルド アーティファクトを整理し、まだインストールされていない依存関係をダウンロードして、アプリケーションをビルドおよびパッケージ化します。

3. コマンドが終了すると、__wordcountjava/target__ ディレクトリに __wordcountjava-1.0-SNAPSHOT.jar__ という名前のファイルが格納されます。

	> [AZURE.NOTE]__wordcountjava-1.0-SNAPSHOT.jar__ ファイルは uberjar です。これには、WordCount ジョブだけでなく、ジョブの実行時に必要な依存関係も含まれます。


##<a id="upload"></a>jar をアップロードする

次のコマンドを使用して、HDInsight ヘッドノードに jar ファイルをアップロードします。

	scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

	Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

これにより、ファイルがローカル システムからヘッド ノードにコピーされます。

> [AZURE.NOTE]SSH アカウントのセキュリティ保護にパスワードを使用している場合は、パスワードの入力が求められます。SSH キーを使用している場合は、`-i` パラメーターと、秘密キーのパスを使用する必要があることがあります。たとえば、「`scp -i /path/to/private/key wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`」のように入力します。

##<a name="run"></a>MapReduce ジョブを実行する

1. 次の記事の説明に従って、SSH を使用して HDInsight に接続します。

    - [Linux、Unix、OS X から HDInsight 上の Linux ベースの Hadoop で SSH キーを使用する](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [HDInsight の Linux ベースの Hadoop で Windows から SSH を使用する](hdinsight-hadoop-linux-use-ssh-windows.md)

2. SSH セッションから、次のコマンドを使用して MapReduce アプリケーションを実行します。

		hadoop jar wordcountjava.jar org.apache.hadoop.examples.WordCount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/wordcountout

	これは、WordCount MapReduce アプリケーションを使用して davinci.txt ファイル内の単語数をカウントし、結果を \_\___wasb:///example/data/wordcountout__ に保存します。入力ファイルと出力は、両方ともクラスターの既定のストレージに格納されます。

3. ジョブが完了したら、次のコマンドを使用して結果を表示します。

		hadoop fs -cat wasb:///example/data/wordcountout/*

	次のような文字と回数の値の一覧が表示されます。

		zeal    1
		zelus   1
		zenith  2

##<a id="nextsteps"></a>次のステップ

このドキュメントでは、Java MapReduce ジョブを作成する方法を説明しました。HDInsight を使用する他の方法については、次のドキュメントを参照してください。

- [HDInsight での Hive の使用][hdinsight-use-hive]
- [HDInsight での Pig の使用][hdinsight-use-pig]
- [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

詳細については、[Java デベロッパー センター](/develop/java/)も参照してください。

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md

[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-emulator]: ../hdinsight-get-started-emulator.md
[hdinsight-emulator-wasb]: ../hdinsight-get-started-emulator.md#blobstorage
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[powershell-install-configure]: ../install-configure-powershell.md



[image-emulator-wordcount-compile]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Compile-Java-MapReduce.png
[image-emulator-wordcount-run]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png

<!---HONumber=Oct15_HO3-->