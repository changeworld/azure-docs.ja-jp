<properties linkid="manage-services-hdinsight-develop-Java-MapReduce-programs-for-HDInsight-Hadoop" urlDisplayName="HDInsight Tutorials" pageTitle="Develop Java MapReduce programs for Hadoop in HDInsight | Azure" metaKeywords="hdinsight, hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce, Java" description="Learn how to develop Java MapReduce programs on HDInsight emulator, how to deploy them to HDInsight." services="hdinsight" title="Develop Java MapReduce programs for Hadoop in HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# HDInsight での Hadoop 用 Java MapReduce プログラムの開発

このチュートリアルでは、Java を使ってワード カウント Hadoop MapReduce ジョブを作成して HDInsight Emulator でテストしてから、Azure HDInsight にデプロイして実行するエンド ツー エンド シナリオについて説明します。

**前提条件:**

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

-   Azure HDInsight Emulator のインストール。手順については、「[HDInsight Emulator の概要][]」を参照してください。
-   エミュレーター コンピューターへの Azure PowerShell のインストール。手順については、[Azure PowerShell のインストールおよび構成に関するページ][]を参照してください。
-   Azure サブスクリプションの入手。手順については、[購入オプション][]、[メンバー プラン][]、または[無料評価版][]に関するページを参照してください。

## この記事の内容

-   [Java でワード カウント MapReduce プログラムを開発する][]
-   [エミュレーターでプログラムをテストする][]
-   [Azure BLOB ストレージにデータ ファイルとアプリケーションをアップロードする][]
-   [Azure HDInsight で MapReduce プログラムを実行する][]
-   [MapReduce の結果を取得する][]
-   [次のステップ][]

## <a name="develop"></a>Java でワード カウント MapReduce プログラムを開発する

ワード カウントは、指定された入力内での各単語の出現回数をカウントする単純なアプリケーションです。

**Java でワード カウント MapReduce ジョブを記述するには**

1.  メモ帳を開きます。
2.  次のプログラムをコピーして、メモ帳に貼り付けます。

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

3.  ファイルを **c:\\Tutorials\\WordCountJava\\WordCount.java** として保存します。このフォルダー構造がなければ作成します。

HDInsight Emulator は *javac* コンパイラに付属しています。

**MapReduce プログラムをコンパイルするには**

1.  コマンド プロンプトを開きます。
2.  ディレクトリを **c:\\Tutorials\\WordCountJava** に変更します。これは、ワード カウント MapReduce プログラムのフォルダーです。
3.  次のコマンドを実行して、2 つの jar ファイルがあることを確認します。

        dir %hadoop_home%\hadoop-core-1.1.0-SNAPSHOT.jar
        dir %hadoop_home%\lib\commons-cli-1.2.jar

4.  次のコマンドを実行して、プログラムをコンパイルします。

        C:\Hadoop\java\bin\javac -classpath %hadoop_home%\hadoop-core-1.1.0-SNAPSHOT.jar;%hadoop_home%\lib\commons-cli-1.2.jar WordCount.java

    javac は C:\\Hadoop\\java\\bin フォルダーにあります。最後のパラメーターは、現在のフォルダーにある Java プログラムです。コンパイラによって現在のフォルダーに 3 つのクラス ファイルが作成されます。

5.  次のコマンドを実行して、jar ファイルを作成します。

        C:\Hadoop\java\bin\jar -cvf WordCount.jar *.class

    このコマンドによって、現在のフォルダーに WordCount.jar ファイルが作成されます。

    ![HDI.EMulator.WordCount.Compile][]

## <a name="test"></a>エミュレーターでプログラムをテストする

エミュレーターでの MapReduce ジョブのテストには次の手順が含まれます。

1.  データ ファイルをエミュレーターの HDFS にアップロードする
2.  ワード カウント MapReduce ジョブを送信する
3.  ジョブの状態を確認する
4.  ジョブの結果を取得する

HDInsight Emulator は既定のファイル システムとして HDFS を使用します。必要に応じて、Azure BLOB ストレージを使用するように HDInsight Emulator を構成することもできます。詳細については、「[HDInsight Emulator の概要][1]」を参照してください。

このチュートリアルでは、HDFS *copyFromLocal* コマンドを使用して、データ ファイルを HDFS にアップロードします。次のセクションでは、Azure PowerShell を使用してファイルを Azure BLOB ストレージにアップロードする方法について説明します。Azure BLOB ストレージにファイルをアップロードするその他の方法については、「[HDInsight へのデータのアップロード][]」を参照してください。

このチュートリアルでは、次の HDFS フォルダー構造を使用します。

<table border="1">
<tr><th>フォルダー                   </th><th>注                                         </th></tr>
<tr><td>/WordCount               </td><td>ワード カウント プロジェクトのルート フォルダー。  </td></tr>
<tr><td>/WordCount/Apps          </td><td>mapper と reducer の実行可能ファイルのフォルダー。</td></tr>
<tr><td>/WordCount/Input         </td><td>MapReduce のソース ファイル フォルダー。           </td></tr>
<tr><td>/WordCount/Output        </td><td>MapReduce の出力ファイル フォルダー。             </td></tr>
<tr><td>/WordCount/MRStatusOutput</td><td>ジョブの出力フォルダー。                           </td></tr>
</table>

このチュートリアルでは、%hadoop\_home% ディレクトリにある .txt ファイルをデータ ファイルとして使用します。

> [WACOM.NOTE] Hadoop HDFS のコマンドは大文字と小文字が区別されます。

**データ ファイルをエミュレーター上の HDFS にコピーするには**

1.  デスクトップから Hadoop コマンド ラインを開きます。Hadoop コマンド ラインはエミュレーターのインストーラーによってインストールされます。
2.  Hadoop コマンド ライン ウィンドウから次のコマンドを実行して、入力ファイルのディレクトリを作成します。

        hadoop fs -mkdir /WordCount/Input

    ここで使用しているパスは相対パスです。次のパスと同等です。

        hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

3.  次のコマンドを実行して、テキスト ファイルを HDFS 上の入力フォルダーにコピーします。

        hadoop fs -copyFromLocal %hadoop_home%\*.txt /WordCount/Input

    MapReduce ジョブはこれらのファイル内の単語をカウントします。

4.  次のコマンドを実行して、アップロードしたファイルを一覧表示して確認します。

        hadoop fs -ls /WordCount/Input

    8 個の .txt ファイルが表示されます。

**Hadoop コマンド ラインを使用して MapReduce ジョブを実行するには**

1.  デスクトップから Hadoop コマンド ラインを開きます。
2.  次のコマンドを実行して、HDFS から /WordCount/Output フォルダー構造を削除します。/WordCount/Output はワード カウント MapReduce ジョブの出力フォルダーです。このフォルダーが既に存在していると、MapReduce ジョブは失敗します。この手順は、ジョブを実行するのが 2 回目である場合に必要です。

        hadoop fs -rmr /WordCount/Output

3.  次のコマンドを実行します。

        hadoop jar c:\Tutorials\WordCountJava\wordcount\target\wordcount-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

    ジョブが正常に完了した場合は、次のスクリーンショットのような出力が表示されます。

    ![HDI.EMulator.WordCount.Run][]

    スクリーンショットから、map と reduce の両方が 100% 完了したことがわかります。ジョブ ID job\_201312092021\_0002 も一覧表示されます。同じレポートは、デスクトップから **Hadoop MapReduce status** ショートカットを開いてジョブ ID を見つける方法で取得することもできます。

MapReduce ジョブを実行するための別のオプションは、Azure PowerShell を使用することです。手順については、「[HDInsight Emulator の概要][]」を参照してください。

**HDFS からの出力を表示するには**

1.  Hadoop コマンド ラインを開きます。
2.  次のコマンドを実行して、出力を表示します。

        hadoop fs -ls /WordCount/Output/
        hadoop fs -cat /WordCount/Output/part-00000

    "|more" をコマンドの最後に付けることで、ページ ビューを得られます。また、findstr コマンドを使用することで、文字列パターンを検索できます。

        hadoop fs -cat /WordCount/Output/part-00000 | findstr "there"

これで、ワード カウント MapReduce ジョブの開発とエミュレーターでのテストが正常に完了しました。次の手順は、Azure HDInsight でそのジョブを展開して実行することです。

## <span id="upload"></span></a>Azure BLOB ストレージにデータをアップロードする

Azure HDInsight は、データ ストレージとして Azure BLOB ストレージを使用します。HDInsight クラスターをプロビジョニングするときは、Azure BLOB ストレージ コンテナーを使用してシステム ファイルを格納します。この既定のコンテナーか別のコンテナー (Azure の同じストレージ アカウント上、またはクラスターと同じデータ センターに配置された別のストレージ アカウント上) をデータ ファイルの格納に使用できます。

このチュートリアルでは、データ ファイル用と MapReduce アプリケーション用にそれぞれ個別のストレージ アカウント上にコンテナーを作成します。データ ファイルはワークステーション上の %hadoop\_home% ディレクトリ内のテキスト ファイルです。

**BLOB ストレージとコンテナーを作成するには**

1.  Azure PowerShell を開きます。
2.  変数を設定し、次のコマンドを実行します。

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName_Data = "<AzureStorageAccountName>"  
        $containerName_Data = "<ContainerName>"
        $location = "<MicrosoftDataCenter>"  # For example, "East US"

    **$subscripionName** は、Azure サブスクリプションに関連付けられます。**$storageAccountName\_Data** と **$storageAccountName\_Data** には名前を付ける必要があります。名前付けの制限については、「[コンテナー、BLOB、およびメタデータの名前付け][]」を参照してください

3.  次のコマンドを実行して、ストレージ アカウントと、そのアカウントの BLOB ストレージ コンテナーを作成します。

        # Select Azure subscription
        Select-AzureSubscription $subscriptionName

        # Create a storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName_Data -location $location

        # Create a Blob storage container
        $storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
        $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  
        New-AzureStorageContainer -Name $containerName_Data -Context $destContext

4.  次のコマンドを実行して、ストレージ アカウントとコンテナーを確認します。

        Get-AzureStorageAccount -StorageAccountName $storageAccountName_Data
        Get-AzureStorageContainer -Context $destContext

**データ ファイルをアップロードするには**

1.  Azure PowerShell を開きます。
2.  最初の 3 つの変数を設定し、次のコマンドを実行します。

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName_Data = "<AzureStorageAccountName>"  
        $containerName_Data = "<ContainerName>"

        $localFolder = "c:\Hadoop\hadoop-1.1.0-SNAPSHOT"
        $destFolder = "WordCount/Input"

    **$storageAccountName\_Data** と **$containerName\_Data** は前の手順で定義したものと同じです。

    ソース ファイル フォルダーは **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT** で、アップロード先フォルダーは **WordCount/Input** です。

3.  次のコマンドを実行して、ソース ファイル フォルダー内の txt ファイルの一覧を取得します。

        # Get a list of the txt files
        $filesAll = Get-ChildItem $localFolder
        $filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

4.  次のコマンドを実行して、ストレージ コンテキスト オブジェクトを作成します。

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5.  次のコマンドを実行して、ファイルをコピーします。

        # Copy the file from local workstation to the Blob container        
        foreach ($file in $filesTxt){

            $fileName = "$localFolder\$file"
            $blobName = "$destFolder/$file"

            write-host "Copying $fileName to $blobName"

            Set-AzureStorageBlobContent -File $fileName -Container $containerName_Data -Blob $blobName -Context $destContext
        }

6.  次のコマンドを実行して、アップロードしたファイルを一覧表示します。

        # List the uploaded files in the Blob storage container
        Write-Host "The Uploaded data files:" -BackgroundColor Green
        Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $destFolder

    8 個のテキスト データ ファイルが表示されます。

**ワード カウント アプリケーションをアップロードするには**

1.  Azure PowerShell を開きます。
2.  最初の 3 つの変数を設定し、次のコマンドを実行します。

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName_Data = "<AzureStorageAccountName>"  
        $containerName_Data = "<ContainerName>"

        $jarFile = "C:\Tutorials\WordCountJava\WordCount.jar"
        $blobFolder = "WordCount/jars"

    **$storageAccountName\_Data** と **$containerName\_Data** は前の手順で定義したものと同じです。つまり、同じストレージ アカウントの同じコンテナーにデータ ファイルとアプリケーションの両方をアップロードします。

    アップロード先フォルダーは **WordCount/jars** です。

3.  次のコマンドを実行して、ストレージ コンテキスト オブジェクトを作成します。

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

4.  次のコマンドを実行して、アプリケーションをコピーします。

        Set-AzureStorageBlobContent -File $jarFile -Container $containerName_Data -Blob "$blobFolder/WordCount.jar" -Context $destContext

5.  次のコマンドを実行して、アップロードしたファイルを一覧表示します。

        # List the uploaded files in the Blob storage container
        Write-Host "The Uploaded application file:" -BackgroundColor Green
        Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $blobFolder

    jar ファイルがそこに一覧表示されます。

## <a name="run"></a>Azure HDInsight で MapReduce ジョブを実行する

ここで示している PowerShell スクリプトは次のタスクを実行します。

1.  HDInsight クラスターをプロビジョニングする

    1.  HDInsight クラスターの既定のファイル システムとして使用されるストレージ アカウントを作成する
    2.  BLOB ストレージ コンテナーを作成する
    3.  HDInsight クラスターを作成する

2.  MapReduce ジョブを送信する

    1.  MapReduce ジョブ定義を作成する
    2.  MapReduce ジョブを送信する
    3.  ジョブの完了を待機する
    4.  標準エラーを表示する
    5.  標準出力を表示する

3.  クラスターを削除する

    1.  HDInsight クラスターを削除する
    2.  HDInsight クラスターの既定のファイル システムとして使用されるストレージ アカウントを削除する

**PowerShell スクリプトを実行するには**

1.  メモ帳を開きます。
2.  次のコードをコピーして貼り付けます。

        # The storage account and the HDInsight cluster variables
        $subscriptionName = "<AzureSubscriptionName>"
        $serviceNameToken = "<ServiceNameTokenString>"
        $location = "<MicrosoftDataCenter>"     ### must match the data storage account location
        $clusterNodes = <NumberOFNodesInTheCluster>

        $storageAccountName_Data = "<TheDataStorageAccountName>"
        $containerName_Data = "<TheDataBlobStorageContainerName>"

        $clusterName = $serviceNameToken + "hdicluster"

        $storageAccountName_Default = $serviceNameToken + "hdistore"
        $containerName_Default =  $serviceNameToken + "hdicluster"

        # The MapReduce job variables
        $jarFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/jars/WordCount.jar"
        $className = "org.apache.hadoop.examples.WordCount"
        $mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
        $mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
        $mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"

        # Create a PSCredential object. The username and password are hardcoded here.  You can change them if you want.
        $password = ConvertTo-SecureString "Pass@word1" -AsPlainText -Force
        $creds = New-Object System.Management.Automation.PSCredential ("Admin", $password) 

        Select-AzureSubscription $subscriptionName

        #=============================
        # Create a storage account used as the default file system
        Write-Host "Create a storage account" -ForegroundColor Green
        New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location

        #=============================
        # Create a Blob storage container used as teh default file system
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

        # Delete the default file system storage account
        Write-Host "Delete the storage account" -ForegroundColor Green
        Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3.  スクリプトの最初の 6 つの変数を設定します。**$serviceNameToken** は、HDInsight クラスター名、既定のストレージ アカウント名、および既定の BLOB ストレージ コンテナー名に使用されます。サービス名は 3 〜 24 文字であり、スクリプトは最大 10 文字の文字列を名前に付加するため、サービス名の文字列は 14 文字以下に制限する必要があります。$serviceNameToken には小文字を使用してください。**$storageAccountName\_Data** と **$containerName\_Data** は、データ ファイルとアプリケーションの格納に使用するストレージ アカウントとコンテナーです。**$location** は、データ ストレージ アカウントの場所と一致する必要があります。
4.  残りの変数を確認します。
5.  スクリプト ファイルを保存します。
6.  Azure PowerShell を開きます。
7.  次のコマンドを実行して、実行ポリシーを RemoteSigned に設定します。

        PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8.  プロンプトが表示されたら、HDInsight クラスターのユーザー名とパスワードを入力します。スクリプトの最後でクラスターを削除し、ユーザー名とパスワードが必要なくなるため、ユーザー名とパスワードとして任意の文字列を使用できます。資格情報の入力を求めないようにする場合は、[Windows PowerShell でのパスワード、セキュリティ保護された文字列、資格情報の使用][]に関するページを参照してください。

## <a name="retrieve"></a>MapReduce ジョブの出力を取得する

このセクションでは、出力をダウンロードして表示する方法を示します。Excel で結果を表示する方法については、「[Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続][]」と「[Power Query を使用した Excel から HDInsight への接続][]」を参照してください。

**出力を取得するには**

1.  Azure PowerShell ウィンドウを開きます。
2.  ディレクトリを **C:\\Tutorials\\WordCountJava** に変更します。Azure PowerShell の既定のフォルダーは **C:\\Windows\\System32\\WindowsPowerShell\\v1.0** です。実行するコマンドレットによって出力ファイルが現在のフォルダーにダウンロードされます。出力ファイルをシステム フォルダーにダウンロードするアクセス許可はありません。
3.  次のコマンドを実行して、値を設定します。

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName_Data = "<TheDataStorageAccountName>"
        $containerName_Data = "<TheDataBlobStorageContainerName>"
        $blobName = "WordCount/Output/part-r-00000"

4.  次のコマンドを実行して、Azure のストレージ コンテキスト オブジェクトを作成します。

        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
        $storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  

5.  次のコマンドを実行して、出力をダウンロードして表示します。

        Get-AzureStorageBlobContent -Container $containerName_Data -Blob $blobName -Context $storageContext -Force
        cat "./$blobName" | findstr "there"

ジョブが完了したら、[Sqoop][] を使用してデータを SQL Server または Azure SQL データベースにエクスポートするか、Excel にエクスポートするかを選択できます。

## <span id="nextsteps"></span></a>次のステップ

このチュートリアルでは、Java MapReduce ジョブを作成する方法、HDInsight Emulator でアプリケーションをテストする方法、HDInsight クラスターをプロビジョニングしてクラスターで MapReduce を実行する PowerShell スクリプトを記述する方法について説明しました。詳細については、次の記事を参照してください。

-   [HDInsight 用 C# Hadoop ストリーミング MapReduce プログラムの開発][]
-   [Azure HDInsight の概要][]
-   [HDInsight Emulator の概要][]
-   [HDInsight での Azure BLOB ストレージの使用][]
-   [PowerShell を使用した HDInsight の管理][]
-   [HDInsight へのデータのアップロード][]
-   [HDInsight での Hive の使用][]
-   [HDInsight での Pig の使用][]
-   [Power Query を使用した Excel から HDInsight への接続][]
-   [Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続][]

  [HDInsight Emulator の概要]: ../hdinsight-get-started-emulator/
  [Azure PowerShell のインストールおよび構成に関するページ]: ../install-configure-powershell/
  [購入オプション]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [メンバー プラン]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [無料評価版]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Java でワード カウント MapReduce プログラムを開発する]: #develop
  [エミュレーターでプログラムをテストする]: #test
  [Azure BLOB ストレージにデータ ファイルとアプリケーションをアップロードする]: #upload
  [Azure HDInsight で MapReduce プログラムを実行する]: #run
  [MapReduce の結果を取得する]: #retrieve
  [次のステップ]: #nextsteps
  [HDI.EMulator.WordCount.Compile]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Compile-Java-MapReduce.png
  [1]: ../hdinsight-get-started-emulator/#blobstorage
  [HDInsight へのデータのアップロード]: ../hdinsight-upload-data/
  [HDI.EMulator.WordCount.Run]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png
  [コンテナー、BLOB、およびメタデータの名前付け]: http://msdn.microsoft.com/en-us/library/windowsazure/dd135715.aspx
  [Windows PowerShell でのパスワード、セキュリティ保護された文字列、資格情報の使用]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
  [Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Power Query を使用した Excel から HDInsight への接続]: ../hdinsight-connect-excel-power-query/
  [Sqoop]: ../hdinsight-use-sqoop/
  [HDInsight 用 C# Hadoop ストリーミング MapReduce プログラムの開発]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Azure HDInsight の概要]: ../hdinsight-get-started/
  [HDInsight での Azure BLOB ストレージの使用]: ../hdinsight-use-blob-storage/
  [PowerShell を使用した HDInsight の管理]: ../hdinsight-administer-use-powershell/
  [HDInsight での Hive の使用]: ../hdinsight-use-hive/
  [HDInsight での Pig の使用]: ../hdinsight-use-pig/
