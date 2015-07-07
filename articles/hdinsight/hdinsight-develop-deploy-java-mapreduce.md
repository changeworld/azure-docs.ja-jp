<properties 
	pageTitle="Hadoop 用 Java MapReduce プログラムの開発 | Microsoft Azure" 
	description="HDInsight Emulator で Java MapReduce プログラムを開発する方法、それらのプログラムを HDInsight にデプロイする方法について説明します。" 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="nitinme" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="04/01/2015" 
	ms.author="nitinme"/>

# HDInsight での Hadoop 用 Java MapReduce プログラムの開発
このチュートリアルでは、Apache Maven を使って Java でワード カウント Hadoop MapReduce ジョブを開発する場合の全工程にわたるシナリオについて説明します。さらに、アプリケーションを HDInsight Emulator for Azure でテストした後で Azure HDInsight クラスターにデプロイして実行する方法についても説明します。

##<a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、次の作業を完了している必要があります。

- HDInsight Emulator のインストール手順については、「[HDInsight Emulator の概要][hdinsight-emulator]」を参照してください。必要なサービスがすべて実行されていることを確認します。HDInsight Emulator をインストールしているコンピューターで、デスクトップのショートカットから Hadoop コマンド ラインを起動し、**C:\\hdp** に移動して、コマンド **start_local_hdp_services.cmd** を実行します。	
- エミュレーター コンピューターへの Azure PowerShell のインストール。手順については、[Azure PowerShell のインストールおよび構成に関するページ][powershell-install-configure]を参照してください。
- エミュレーター コンピューターへの Java プラットフォーム JDK 7 以降のインストール。これはエミュレーター コンピューターで既に利用可能です。
- [Apache Maven](http://maven.apache.org/) のインストールおよび構成。
- Azure サブスクリプションの入手。手順については、[購入オプション][azure-purchase-options]、[メンバー プラン][azure-member-offers]、または[無料評価版][azure-free-trial]に関するページを参照してください。


##<a name="develop"></a>Apache Maven を使って Java で MapReduce プログラムを作成する

ワード カウント MapReduce アプリケーションを作成します。作成するのは、指定された入力内での各単語の出現回数をカウントする単純なアプリケーションです。このセクションでは、次のタスクを実行します。

1. Apache Maven を使ってプロジェクトを作成する
2. プロジェクト オブジェクト モデル (POM) を更新する
3. ワード カウント MapReduce アプリケーションを作成する
4. アプリケーションをビルドおよびパッケージ化する

**Maven を使ってプロジェクトを作成するには**

1. **C:\Tutorials\WordCountJava\** という名前のディレクトリを作成します。
2. 開発環境のコマンド ラインから、作成した場所にディレクトリを変更します。
3. Maven でインストールされた __mvn__ コマンドを使用し、プロジェクトのスキャフォールディングを生成します。

		mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	これにより、__artifactID__ パラメーターにより指定された名前で、新しいディレクトリが現在のディレクトリに作成されます (この例では **wordcountjava**)。 このディレクトリには、次の項目が含まれます。

	* __pom.xml__ - プロジェクト オブジェクト モデル ([POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) には、プロジェクトのビルドに使用される情報と構成の詳細が含まれています。

	* __src__ - アプリケーションを作成する __main\\java\\org\\apache\\hadoop\\examples__ ディレクトリを格納するディレクトリです。
3. __src\\test\\java\\org\\apache\\hadoop\\examples\\apptest.java__ ファイルはこの例で使用しないため、削除します。

**POM を更新するには**

1. __pom.xml__ ファイルを編集し、`<dependencies>` セクション内に次のコードを追加します。

		<dependency>
		  <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
        </dependency>
    	<dependency>
      	  <groupId>org.apache.hadoop</groupId>
      	  <artifactId>hadoop-mapreduce-client-common</artifactId>
      	  <version>2.5.1</version>
    	</dependency>
    	<dependency>                                                                                     
      	  <groupId>org.apache.hadoop</groupId>                                                                                                       
      	  <artifactId>hadoop-common</artifactId>                                                                                                         
      	  <version>2.5.1</version>                                                                                            
    	</dependency>

	このコードは、プロジェクトで特定のバージョン (<version> 内に記載) のライブラリ ( <artifactId> 内に記載) を必要とすることを Maven に通知しています。これはコンパイル時に、既定の Maven リポジトリからダウンロードされます。[Maven リポジトリ検索](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar)を使用して、その他の情報を表示できます。

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
  		  </plugins>
	    </build>

	これにより、Maven でビルドされる JAR ファイル内のライセンスの重複を防ぐために使用する [Maven Shade Plugin](http://maven.apache.org/plugins/maven-shade-plugin/) が構成されます。ライセンス ファイルの重複は、HDInsight クラスターでの実行時に発生するエラーの原因となるためです。Maven Shade Plugin を `ApacheLicenseResourceTransformer` 実装で使用すると、エラーを回避できます。

	また、Maven Shade Plugin は、アプリケーションで必要とされるすべての依存関係を含む uberjar (fatjar と呼ばれることもある) も生成します。

3. __pom.xml__ ファイルを保存します。

**ワード カウント アプリケーションを作成するには**

1. __wordcountjava\\src\\main\\java\\org\\apache\\hadoop\\examples__ ディレクトリに移動し、__app.java__ ファイルの名前を __WordCount.java__ に変更します。
2. メモ帳を開きます。
2. 次のプログラムをコピーして、メモ帳に貼り付けます。

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

**アプリケーションをビルドおよびパッケージ化するには**

1. コマンド プロンプトを開き、ディレクトリを __wordcountjava__ ディレクトリに変更します。

2. 次のコマンドを使用して、アプリケーションを含む JAR ファイルをビルドします。

		mvn clean package

	これにより、前のビルド アーティファクトを整理し、まだインストールされていない依存関係をダウンロードして、アプリケーションをビルドおよびパッケージ化します。

3. コマンドが終了すると、__wordcountjava\\target__ ディレクトリに __wordcountjava-1.0-SNAPSHOT.jar__ という名前のファイルが格納されます。

	> [AZURE.NOTE]__wordcountjava-1.0-SNAPSHOT.jar__ ファイルは uberjar です。


##<a name="test"></a>エミュレーターでプログラムをテストする

HDInsight Emulator での MapReduce ジョブのテストには次の手順が含まれます。

1. データ ファイルを Hadoop 分散ファイル システム (HDFS) にエミュレーターでアップロードする
2. ローカル ユーザー グループを作成する
3. ワード カウント MapReduce ジョブを実行する
4. ジョブの結果を取得する

既定では、HDInsight Emulator は、ファイル システムとして HDFS を使用します。必要に応じて、Azure BLOB ストレージを使用するように HDInsight Emulator を構成することもできます。詳細については、「[HDInsight Emulator の概要][hdinsight-emulator-wasb]」を参照してください。

このチュートリアルでは、HDFS **copyFromLocal** コマンドを使用して、データ ファイルを HDFS にアップロードします。次のセクションでは、Azure PowerShell を使用してファイルを Azure BLOB ストレージにアップロードする方法について説明します。Azure BLOB ストレージにファイルをアップロードするその他の方法については、「[HDInsight へのデータのアップロード][hdinsight-upload-data]」を参照してください。

このチュートリアルでは、次の HDFS フォルダー構造を使用します。

<table border="1">
<tr><td>フォルダー</td><td>注</td></tr>
<tr><td>/WordCount</td><td>ワード カウント プロジェクトのルート フォルダー。</td></tr>
<tr><td>/WordCount/Apps</td><td>mapper と reducer の実行可能ファイルのフォルダー。</td></tr>
<tr><td>/WordCount/Input</td><td>MapReduce のソース ファイル フォルダー。</td></tr>
<tr><td>/WordCount/Output</td><td>MapReduce の出力ファイル フォルダー。</td></tr>
<tr><td>/WordCount/MRStatusOutput</td><td>ジョブの出力フォルダー。</td></tr>
</table>

このチュートリアルでは、%hadoop_home% ディレクトリにある .txt ファイルをデータ ファイルとして使用します。

> [AZURE.NOTE]Hadoop HDFS のコマンドは大文字と小文字が区別されます。

**データ ファイルをエミュレーター上の HDFS にコピーするには**

1. デスクトップから Hadoop コマンド ラインを開きます。Hadoop コマンド ラインはエミュレーターのインストーラーによってインストールされます。
2. Hadoop コマンド ライン ウィンドウから次のコマンドを実行して、入力ファイルのディレクトリを作成します。

		hadoop fs -mkdir /WordCount
		hadoop fs -mkdir /WordCount/Input

	ここで使用しているパスは相対パスです。次のパスと同等です。

		hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

3. 次のコマンドを実行して、テキスト ファイルを HDFS 上の入力フォルダーにコピーします。

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common*.txt /WordCount/Input

	MapReduce ジョブはこれらのファイル内の単語をカウントします。

4. 次のコマンドを実行して、アップロードしたファイルを一覧表示して確認します。

		hadoop fs -ls /WordCount/Input

**ローカル ユーザー グループを作成するには**

クラスター上で MapReduce ジョブを正常に実行するために、hdfs という名前のユーザー グループを作成する必要があります。次に、このグループに対し、hadoop という名前のユーザーと、エミュレーターにログオンするときに使用するローカル ユーザーを追加します。管理者特権のコマンド プロンプトで、次のコマンドを使用します。

		# Add a user group called hdfs		
		net localgroup hdfs /add

		# Adds a user called hadoop to the group
		net localgroup hdfs hadoop /add

		# Adds the local user to the group
		net localgroup hdfs <username> /add

**Hadoop コマンド ラインを使用して MapReduce ジョブを実行するには**

1. デスクトップから Hadoop コマンド ラインを開きます。
2. 次のコマンドを実行して、HDFS から /WordCount/Output フォルダー構造を削除します。/WordCount/Output はワード カウント MapReduce ジョブの出力フォルダーです。このフォルダーが既に存在していると、MapReduce ジョブは失敗します。この手順は、ジョブを実行するのが 2 回目である場合に必要です。

		hadoop fs -rm - r /WordCount/Output

2. 次のコマンドを実行します。

		hadoop jar C:\Tutorials\WordCountJava\wordcountjava\target\wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

	ジョブが正常に終了した場合は、次のスクリーンショットのような出力が表示されます。

	![HDI.EMulator.WordCount.Run][image-emulator-wordcount-run]

	スクリーンショットから、map と reduce の両方が 100% 完了したことがわかります。ジョブ ID も一覧表示されます。同じレポートは、デスクトップから **Hadoop MapReduce status** ショートカットを開いてこのジョブ ID を見つけることでも取得できます。

MapReduce ジョブを実行するための別のオプションは、Azure PowerShell を使用することです。手順については、「[HDInsight Emulator の概要][hdinsight-emulator]」を参照してください。

**HDFS からの出力を表示するには**

1. Hadoop コマンド ラインを開きます。
2. 次のコマンドを実行して、出力を表示します。

		hadoop fs -ls /WordCount/Output/
		hadoop fs -cat /WordCount/Output/part-r-00000

	"|more" をコマンドの最後に付けることで、ページ ビューを得られます。また、**findstr** コマンドを使用することで、文字列パターンを検索できます。

		hadoop fs -cat /WordCount/Output/part-r-00000 | findstr "there"

これで、ワード カウント MapReduce ジョブの開発とエミュレーターでのテストが正常に完了しました。次の手順は、Azure HDInsight でそのジョブを展開して実行することです。



##<a id="upload"></a>Azure BLOB ストレージにデータとアプリケーションをアップロードする
Azure HDInsight は、データ ストレージとして Azure BLOB ストレージを使用します。HDInsight クラスターをプロビジョニングするときは、Azure BLOB ストレージ コンテナーを使用してシステム ファイルを格納します。この既定のコンテナーか別のコンテナー (Azure の同じストレージ アカウント上、またはクラスターと同じデータ センターに配置された別のストレージ アカウント上) をデータ ファイルの格納に使用できます。

このチュートリアルでは、データ ファイル用と MapReduce アプリケーション用にそれぞれ個別のストレージ アカウント上にコンテナーを作成します。データ ファイルは、エミュレーター ワークステーション上の **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\doc\\hadoop\\common** ディレクトリ内のテキスト ファイルです。

**BLOB ストレージ アカウントとコンテナーを作成するには**

1. Azure PowerShell を開きます。
2. 変数を設定し、次のコマンドを実行します。

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

	**$subscripionName** 変数は、Azure サブスクリプションに関連付けられます。**$storageAccountName_Data** と **$containerName_Data** には名前を付ける必要があります。名前付けの制限については、「[コンテナー、BLOB、およびメタデータの名前付け](http://msdn.microsoft.com/library/windowsazure/dd135715.aspx)」を参照してください

3. 次のコマンドを実行して、ストレージ アカウントと、そのアカウントの BLOB ストレージ コンテナーを作成します。

		# Select an Azure subscription
		Select-AzureSubscription $subscriptionName
		
		# Create a Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Data -location $location
				
		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName_Data -Context $destContext

4. 次のコマンドを実行して、ストレージ アカウントとコンテナーを確認します。

		Get-AzureStorageAccount -StorageAccountName $storageAccountName_Data
		Get-AzureStorageContainer -Context $destContext

**データ ファイルをアップロードするには**

1. Azure PowerShell を開きます。
2. 最初の 3 つの変数を設定し、次のコマンドを実行します。

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common"
		$destFolder = "WordCount/Input"

	**$storageAccountName_Data** 変数と **$containerName_Data** 変数は前の手順で定義したものと同じです。

	ソース ファイル フォルダーは **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT** で、アップロード先フォルダーは **WordCount/Input** です。

3. 次のコマンドを実行して、ソース ファイル フォルダー内の .txt ファイルの一覧を取得します。

		# Get a list of the .txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}
		
4. 次のコマンドを実行して、ストレージ コンテキスト オブジェクトを作成します。

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5. 次のコマンドを実行して、ファイルをコピーします。

		# Copy the files from the local workstation to the Blob container        
		foreach ($file in $filesTxt){
		 
		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"
		
		    write-host "Copying $fileName to $blobName"
		
		    Set-AzureStorageBlobContent -File $fileName -Container $containerName_Data -Blob $blobName -Context $destContext
		}

6. 次のコマンドを実行して、アップロードしたファイルを一覧表示します。

		# List the uploaded files in the Blob storage container
        Write-Host "The Uploaded data files:" -BackgroundColor Green
		Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $destFolder

	アップロードされたテキスト データ ファイルが表示されます。

**ワード カウント アプリケーションをアップロードするには**

1. Azure PowerShell を開きます。
2. 最初の 3 つの変数を設定し、次のコマンドを実行します。

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$jarFile = "C:\Tutorials\WordCountJava\wordcountjava\target\wordcountjava-1.0-SNAPSHOT.jar"
		$blobFolder = "WordCount/jars"

	**$storageAccountName_Data** 変数と **$containerName_Data** 変数は前の手順で定義したものと同じです。つまり、同じストレージ アカウントの同じコンテナーにデータ ファイルとアプリケーションの両方をアップロードします。

	アップロード先フォルダーは **WordCount/jars** です。

4. 次のコマンドを実行して、ストレージ コンテキスト オブジェクトを作成します。

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5. 次のコマンドを実行して、アプリケーションをコピーします。

		Set-AzureStorageBlobContent -File $jarFile -Container $containerName_Data -Blob "$blobFolder/WordCount.jar" -Context $destContext

6. 次のコマンドを実行して、アップロードしたファイルを一覧表示します。

		# List the uploaded files in the Blob storage container
        Write-Host "The Uploaded application file:" -BackgroundColor Green
		Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $blobFolder

	jar ファイルがそこに一覧表示されます。

##<a name="run"></a>Azure HDInsight で MapReduce ジョブを実行する

このセクションでは、次のタスクを実行する Azure PowerShell スクリプトを作成します。

1. HDInsight クラスターをプロビジョニングする
	
	1. HDInsight クラスターの既定のファイル システムとして使用されるストレージ アカウントを作成する
	2. BLOB ストレージ コンテナーを作成する 
	3. HDInsight クラスターの作成

2. MapReduce ジョブを送信する

	1. MapReduce ジョブ定義を作成する
	2. MapReduce ジョブを送信する
	3. ジョブの完了を待機する
	4. 標準エラーを表示する
	5. 標準出力を表示する

3. クラスターを削除する

	1. HDInsight クラスターを削除する
	2. HDInsight クラスターの既定のファイル システムとして使用されるストレージ アカウントを削除する


**Azure PowerShell スクリプトを実行するには**

1. メモ帳を開きます。
2. 次のコードをコピーして貼り付けます。
		
		# The Storage account and the HDInsight cluster variables
		$subscriptionName = "<AzureSubscriptionName>"
		$stringPrefix = "<StringForPrefix>"
		$location = "<MicrosoftDataCenter>"     ### Must match the data Storage account location
		$clusterNodes = <NumberOFNodesInTheCluster>

		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		
		$clusterName = $stringPrefix + "hdicluster"
		
		$storageAccountName_Default = $stringPrefix + "hdistore"
		$containerName_Default =  $stringPrefix + "hdicluster"

		# The MapReduce job variables
		$jarFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/jars/WordCount.jar"
		$className = "org.apache.hadoop.examples.WordCount"
		$mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
		$mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
		$mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"
		
		# Create a PSCredential object. The user name and password are hardcoded here. You can change them if you want.
		$password = ConvertTo-SecureString "Pass@word1" -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ("Admin", $password) 
		
		Select-AzureSubscription $subscriptionName
		
		#=============================
		# Create a Storage account used as the default file system
		Write-Host "Create a storage account" -ForegroundColor Green
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location
		
		#=============================
		# Create a Blob storage container used as the default file system
		Write-Host "Create a Blob storage container" -ForegroundColor Green
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default
		
		New-AzureStorageContainer -Name $containerName_Default -Context $destContext
		
		#=============================
		# Create an HDInsight cluster
		Write-Host "Create an HDInsight cluster" -ForegroundColor Green
		$storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data
		
		New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $creds -Config $config
		
		#=============================
		# Create a MapReduce job definition
		Write-Host "Create a MapReduce job definition" -ForegroundColor Green
		$mrJobDef = New-AzureHDInsightMapReduceJobDefinition -JobName mrWordCountJob  -JarFile $jarFile -ClassName $className -Arguments $mrInput, $mrOutput -StatusFolder /WordCountStatus
		
		#=============================
		# Run the MapReduce job
		Write-Host "Run the MapReduce job" -ForegroundColor Green
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef 
		Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600 
		
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError 
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput
				
		#=============================
		# Delete the HDInsight cluster
		Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
		Remove-AzureHDInsightCluster -Name $clusterName  
		
		# Delete the default file system Storage account
		Write-Host "Delete the storage account" -ForegroundColor Green
		Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3. スクリプトの最初の 6 つの変数を設定します。**$stringPrefix** 変数は、指定した文字列をプレフィックスとして HDInsight クラスター名、ストレージ アカウント名、BLOB ストレージ コンテナー名に付けるために使用されます。これらの名前の長さは 3 ～ 24 文字である必要があるため、指定する文字列とこのスクリプトで使用する名前の合計の長さが名前の文字制限を超えないように注意してください。**$stringPrefix** には、すべて小文字を使用する必要があります。
 
	**$storageAccountName_Data** 変数と **$containerName_Data** 変数は、データ ファイルとアプリケーションの格納に使用するストレージ アカウントとコンテナーです。**$location** 変数は、データ ストレージ アカウントの場所と一致する必要があります。

4. 残りの変数を確認します。
5. スクリプト ファイルを保存します。
6. Azure PowerShell を開きます。
7. 次のコマンドを実行して、実行ポリシーを RemoteSigned に設定します。

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. プロンプトが表示されたら、HDInsight クラスターのユーザー名とパスワードを入力します。スクリプトの最後でクラスターを削除するとユーザー名とパスワードは不要になるため、ユーザー名とパスワードには任意の文字列を使用できます。資格情報の入力を求めないようにする場合は、[Windows PowerShell でのパスワード、セキュリティ保護された文字列、資格情報の使用][powershell-PSCredential]に関するページを参照してください。


##<a name="retrieve"></a>MapReduce ジョブの出力を取得する
このセクションでは、出力をダウンロードして表示する方法を示します。Excel で結果を表示する方法については、「[Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続][hdinsight-ODBC]」と「[Power Query を使用した Excel から HDInsight への接続][hdinsight-power-query]」を参照してください。


**出力を取得するには**

1. Azure PowerShell ウィンドウを開きます。
2. ディレクトリを **C:\\Tutorials\\WordCountJava** に変更します。Azure PowerShell の既定のフォルダーは **C:\\Windows\\System32\\WindowsPowerShell\\v1.0** です。実行するコマンドレットによって出力ファイルが現在のフォルダーにダウンロードされます。出力ファイルをシステム フォルダーにダウンロードするアクセス許可はありません。
2. 次のコマンドを実行して、値を設定します。

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-r-00000"
	
3. 次のコマンドを実行して、Azure Storage のコンテキスト オブジェクトを作成します。
		
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  

4. 次のコマンドを実行して、出力をダウンロードして表示します。

		Get-AzureStorageBlobContent -Container $containerName_Data -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"

ジョブが完了したら、[Sqoop][hdinsight-use-sqoop] を使用してデータを SQL Server または Azure SQL Database にエクスポートするか、Excel にエクスポートするかを選択できます。

##<a id="nextsteps"></a>次のステップ
このチュートリアルでは、Java MapReduce ジョブを作成する方法、HDInsight Emulator でアプリケーションをテストする方法、HDInsight クラスターをプロビジョニングしてクラスターで MapReduce ジョブを実行する Azure PowerShell スクリプトを記述する方法について説明しました。詳細については、次の記事を参照してください。

- [HDInsight 用 C# Hadoop ストリーミング MapReduce プログラムの開発][hdinsight-develop-streaming]
- [Azure HDInsight の概要][hdinsight-get-started]
- [HDInsight Emulator の概要][hdinsight-emulator]
- [HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]
- [Azure PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]
- [HDInsight へのデータのアップロード][hdinsight-upload-data]
- [HDInsight での Hive の使用][hdinsight-use-hive]
- [HDInsight での Pig の使用][hdinsight-use-pig]
- [Power Query を使用した Excel から HDInsight への接続][hdinsight-power-query]
- [Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続][hdinsight-ODBC]

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


<!--HONumber=54--> 