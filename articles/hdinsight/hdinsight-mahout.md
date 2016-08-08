<properties
	pageTitle="Mahout と Windows ベースの HDInsight を使用したリコメンデーションの生成 | Microsoft Azure"
	description="Apache Mahout 機械学習ライブラリを使用して Windows ベースの HDInsight (Hadoop) で映画のリコメンデーションを生成する方法について説明します。"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="larryfr"/>

#HDInsight で Apache Mahout と Hadoop を使用して映画のリコメンデーションを生成する

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

[Apache Mahout](http://mahout.apache.org) 機械学習ライブラリを使用して Azure HDInsight で映画のリコメンデーションを生成する方法について説明します。

> [AZURE.NOTE] このドキュメントの手順では、Windows クライアントと Windows ベースの HDInsight クラスターが必要です。Linux、OS X、または Unix クライアントから Linux ベースの HDInsight クラスターと Mahout を使用する方法の詳細については、[HDInsight で Apache Mahout と Linux ベースの Hadoop を使用した映画のリコメンデーションの生成](hdinsight-hadoop-mahout-linux-mac.md)に関するページを参照してください。


##<a name="learn"></a>学習内容

Mahout は、Apache Hadoop の[機械学習][ml]ライブラリの 1 つです。Mahout には、フィルター処理、分類、クラスタリングなどデータを処理するためのアルゴリズムが含まれています。この記事では、リコメンデーション エンジンを使用し、友人たちが鑑賞した映画に基づいて映画のリコメンデーションを生成します。また、デシジョン フォレストで分類を実行する方法についても説明します。学習内容は以下のとおりです。

* Windows PowerShell を使用して Mahout ジョブを実行する方法

* Mahout ジョブを Hadoop コマンド ラインから実行する方法

* Mahout を HDInsight 3.0 と HDInsight 2.0 クラスターにインストールする方法

	> [AZURE.NOTE] Mahout は、HDInsight 3.1 バージョンのクラスターで提供されます。以前のバージョンの HDInsight を使用している場合は、次に進む前に「[Mahout のインストール](#install)」を参照してください。

##前提条件

- **HDInsight での Windows ベースの Hadoop クラスター**。作成の詳細については、[HDInsight での Hadoop の使用][getstarted]に関するページを参照してください。
- **Azure PowerShell を実行できるワークステーション**。

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


##<a name="recommendations"></a>Windows PowerShell を使用してリコメンデーションを生成する

> [AZURE.NOTE] このセクションで使用されるジョブは Windows PowerShell で動作しますが、Mahout で提供されるクラスの多くは現在 Windows PowerShell で動作しないため、Hadoop コマンド ラインを使用して実行する必要があります。Windows PowerShell で動作しないクラスの一覧については、「[トラブルシューティング](#troubleshooting)」セクションを参照してください。
>
> Hadoop コマンド ラインを使用して Mahout ジョブを実行する例については、[Hadoop コマンド ラインを使用してデータを分類する](#classify)を参照してください。

Mahout で提供される機能の 1 つが、リコメンデーション エンジンです。データは、`userID`、`itemId`、`prefValue` (項目に対するユーザーの嗜好) の形式で受け付けられます。Mahout では、共起分析を実行して、_ある項目を嗜好するユーザーが他の項目も嗜好する_ということを判断できます。次に Mahout は、項目の嗜好が似ているユーザーを特定します。これはリコメンデーションの作成に使用できます。

映画を使用した非常にシンプルな例を次に示します。

* __共起__: Joe、Alice、Bob は全員、好きな映画として「_Star Wars (スター ウォーズ)_」、「_The Empire Strikes Back (帝国の逆襲)_」、「_Return of the Jedi (ジェダイの帰還)_」を挙げました。Mahout では、これらの映画のいずれかを好きなユーザーが他の 2 作品も好きであると判断します。

* __共起__: Bob と Alice は「_The Phantom Menace (ファントム メナス)_」、「_Attack of the Clones (クローンの攻撃)_」、「_Revenge of the Sith (シスの復讐)_」も好きな映画として選びました。Mahout では、この例の最初の 3 作品を好きなユーザーがこれらの 3 作品も好きであると判断します。

* __類似性のリコメンデーション__: Joe は、この例の最初の 3 作品を好きな映画として選びました。Mahout では、嗜好が似ている他のユーザーが好きな映画の中で、Joe がまだ観ていない映画を調べます (好み/順位)。この場合、Mahout では、「_The Phantom Menace (ファントム メナス_)」、「_Attack of the Clones (クローンの攻撃)_」、「_Revenge of the Sith (シスの復讐)_」を推薦します。

###データの説明

[GroupLens Research][movielens] では利便性を高めるために、Mahout と互換性のある形式で映画の評価データを提供します。このデータは、クラスターの既定のストレージ (`/HdiSamples/MahoutMovieData`) にあります。

2 つのファイル `moviedb.txt` (映画に関する情報) と `user-ratings.txt` があります。user-ratings.txt ファイルは分析中に使用されます。moviedb.txt は、分析の結果を表示するときに、わかりやすいテキスト情報を提供するために使用されます。

user-ratings.txt に含まれているデータは、`userID`、`movieID`、`userRating`、`timestamp` で構成されており、各ユーザーが映画をどれだけ高く評価したかを示します。次にデータの例を示します。


    196	242	3	881250949
    186	302	3	891717742
    22	377	1	878887116
    244	51	2	880606923
    166	346	1	886397596

###ジョブを実行する

次の Windows PowerShell スクリプトを使用して、映画データで Mahout リコメンデーション エンジンを使用するジョブを実行します。

	# The HDInsight cluster name.
	$clusterName = "the cluster name"
    
    #Get HTTPS/Admin credentials for submitting the job later
    $creds = Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
            
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
            
	# NOTE: The version number in the file path
	# may change in future versions of HDInsight.
	$jarFile =  "file:///C:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar"
    #
	# If you are using an earlier version of HDInsight,
	# set $jarFile to the jar file you
	# uploaded.
	# For example,
	# $jarFile = "wasbs:///example/jars/mahout-core-0.9-job.jar"

	# The arguments for this job
	# * input - the path to the data uploaded to HDInsight
	# * output - the path to store output data
	# * tempDir - the directory for temp files
	$jobArguments = "--similarityClassname", "recommenditembased", `
                    "-s", "SIMILARITY_COOCCURRENCE", `
	                "--input", "wasbs:///HdiSamples/MahoutMovieData/user-ratings.txt",
	                "--output", "wasbs:///example/out",
	                "--tempDir", "wasbs:///example/temp"

	# Create the job definition
	$jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
	  -JarFile $jarFile `
	  -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
	  -Arguments $jobArguments

	# Start the job
	$job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

	# Wait on the job to complete
	Write-Host "Wait for the job to complete ..." -ForegroundColor Green
	Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
    # Download the output
    Get-AzureStorageBlobContent `
            -Blob example/out/part-r-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
            
	# Write out any error information
	Write-Host "STDERR"
	Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

> [AZURE.NOTE] Mahout ジョブは、ジョブの処理中に作成された一時データを削除しません。このサンプル ジョブでは `--tempDir` パラメーターを指定し、一時ファイルを特定のディレクトリに分離します。

Mahout ジョブは出力を STDOUT に返しません。代わりに、指定された出力ディレクトリに __part-r-00000__ として格納します。このファイルは、ワークステーションの現在のディレクトリ内の __output.txt__ にダウンロードされます。

ファイルの内容の例を次に示します。

	1	[234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
	2	[282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
	3	[284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
	4	[690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

最初の列は `userID` です。"[" と "]" に含まれる値は `movieId`:`recommendationScore` です。

###出力を表示する

生成された出力はアプリケーションで使用できるものですが、人間が判読するのは困難です。サーバーの `moviedb.txt` を使用して `movieId` を映画名に解決できますが、次のスクリプトを使用して、このファイルと評価ファイルをサーバーからダウンロードしておく必要があります。

    # The HDInsight cluster name.
	$clusterName = "the cluster name"
    
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
    #Download the files
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/moviedb.txt" `
    -Container $container `
    -Destination moviedb.txt `
    -Context $context
    Get-AzureStorageBlobContent -blob "HdiSamples/MahoutMovieData/user-ratings.txt" `
    -Container $container `
    -Destination user-ratings.txt `
    -Context $context

ファイルをダウンロードしたら、次の PowerShell スクリプトを使用して映画名でリコメンデーションを表示します。

	<#
	.SYNOPSIS
	    Displays recommendations for movies.
	.DESCRIPTION
	    Displays recommendations generated by Mahout
	    with HDInsight example in a human readable format.
	.EXAMPLE
	    .\Show-Recommendation -userId 4
	        -userDataFile "user-ratings.txt"
	        -movieFile "moviedb.txt"
	        -recommendationFile "output.txt"
	#>

	[CmdletBinding(SupportsShouldProcess = $true)]
	param(
	    #The user ID
	    [Parameter(Mandatory = $true)]
	    [String]$userId,

	    [Parameter(Mandatory = $true)]
	    [String]$userDataFile,

	    [Parameter(Mandatory = $true)]
	    [String]$movieFile,

	    [Parameter(Mandatory = $true)]
	    [String]$recommendationFile
	)
	# Read movie ID & description into hash table
	Write-Host "Reading movies descriptions" -ForegroundColor Green
	$movieById = @{}
	foreach($line in Get-Content $movieFile)
	{
	    $tokens = $line.Split("|")
	    $movieById[$tokens[0]] = $tokens[1]
	}
	# Load movies user has already seen (rated)
	# into a hash table
	Write-Host "Reading rated movies" -ForegroundColor Green
	$ratedMovieIds = @{}
	foreach($line in Get-Content $userDataFile)
	{
	    $tokens = $line.Split("`t")
	    if($tokens[0] -eq $userId)
	    {
	        # Resolve the ID to the movie name
	        $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
	    }
	}
	# Read recommendations generated by Mahout
	Write-Host "Reading recommendations" -ForegroundColor Green
	$recommendations = @{}
	foreach($line in get-content $recommendationFile)
	{
	    $tokens = $line.Split("`t")
	    if($tokens[0] -eq $userId)
	    {
	        #Trim leading/treailing [] and split at ,
	        $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
	        foreach($movieIdAndScore in $movieIdAndScores)
	        {
	            #Split at : and store title and score in a hash table
	            $idAndScore = $movieIdAndScore.Split(":")
	            $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
	        }
	        break
	    }
	}

	Write-Host "Rated movies" -ForegroundColor Green
	Write-Host "---------------------------" -ForegroundColor Green
	$ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
	               @{Expression={$_.Value};Label="Rating"}
	$ratedMovieIds | format-table $ratedFormat
	Write-Host "---------------------------" -ForegroundColor Green

	write-host "Recommended movies" -ForegroundColor Green
	Write-Host "---------------------------" -ForegroundColor Green
	$recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
	                        @{Expression={$_.Value};Label="Score"}
	$recommendations | format-table $recommendationFormat

スクリプトを実行する例を次に示します。

	PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt

出力は次のようになります。

	Reading movies descriptions
	Reading rated movies
	Reading recommendations
	Rated movies
	---------------------------
	Movie                                    Rating
	-----                                    ------
	Devil's Own, The (1997)                  1
	Alien: Resurrection (1997)               3
	187 (1997)                               2
	(lines ommitted)

	---------------------------
	Recommended movies
	---------------------------

	Movie                                    Score
	-----                                    -----
	Good Will Hunting (1997)                 4.6504064
	Swingers (1996)                          4.6862745
	Wings of the Dove, The (1997)            4.6666665
	People vs. Larry Flynt, The (1996)       4.834559
	Everyone Says I Love You (1996)          4.707071
	Secrets & Lies (1996)                    4.818182
	That Thing You Do! (1996)                4.75
	Grosse Pointe Blank (1997)               4.8235292
	Donnie Brasco (1997)                     4.6792455
	Lone Star (1996)                         4.7099237  

##<a name="classify"></a>Hadoop コマンド ラインを使用してデータを分類する

Mahout で利用可能は分類方法の 1 つは、[ランダム フォレスト][forest]をビルドすることです。これは複数の手順から成るプロセスです。データの分類に使用されるデシジョン ツリーをトレーニング データで生成する手順も含まれます。Mahout により提供される __org.apache.mahout.classifier.df.tools.Describe__ クラスが使用され、現在は Hadoop コマンド ラインを使用して実行する必要があります。

###データを読み込む

1. ファイルを [The NSL-KDD Data Set](http://nsl.cs.unb.ca/NSL-KDD/) からダウンロードします。

  * [KDDTrain+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff): トレーニング ファイル

  * [KDDTest+.ARFF](http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff): テスト データ

2. 各ファイルを開き、'@' で始まる先頭の数行を削除して、ファイルを保存します。これらが削除されていない場合、このデータを Mahout で使用するとエラー メッセージが返されます。

2. __example/data__ にファイルをアップロードします。この処理には、次のスクリプトを使用できます。__CLUSTERNAME__ を HDInsight クラスターの名前に置き換えます。FILENAME をアップロードするファイルの名前に置き換えます。

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName="CLUSTERNAME"
        $fileToUpload="FILENAME"
        $blobPath="example/data/FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context

###ジョブを実行する

1. このジョブでは、Hadoop コマンドラインが必要です。「[RDP を使用した HDInsight クラスターへの接続](hdinsight-administer-use-management-portal.md#rdp)」の手順に従って、HDInsight クラスターのリモート デスクトップを有効にしてからデスクトップに接続します。

3. 接続した後で、__[Hadoop コマンド ライン]__ アイコンを使用して Hadoop コマンド ラインを開きます。

	![hadoop cli][hadoopcli]

3. 次のコマンドを使用し、Mahout を使用するファイル記述子 (__KDDTrain+.info__) を生成します。

		hadoop jar "c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasbs:///example/data/KDDTrain+.arff" -f "wasbs:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

	`N 3 C 2 N C 4 N C 8 N 2 C 19 N L` は、ファイル内のデータの属性を示します。たとえば、L はラベルを示します。

4. 次のコマンドを使用してデシジョン ツリーのフォレストをビルドします。

		hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasbs:///example/data/KDDTrain+.arff -ds wasbs:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    この操作の出力は、HDInsight クラスターのストレージにある __nsl-forest__ ディレクトリに格納されます (\_\_wasbs://user/&lt;username>/nsl-forest/nsl-forest.seq)。&lt;username> は、リモート デスクトップ セッションに使用されるユーザー名です。これは、人間が判読できないファイルです。

5. __KDDTest+.arff__ データセットを分類してフォレストをテストします。次のコマンドを使用します。

    	hadoop jar c:/apps/dist/mahout-0.9.0.2.2.9.1-8/examples/target/mahout-examples-0.9.0.2.2.9.1-8-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasbs:///example/data/KDDTest+.arff -ds wasbs:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasbs:///example/data/predictions

    このコマンドは、次のような分類プロセスの概要情報を返します。

	    14/07/02 14:29:28 INFO mapreduce.TestForest:

	    =======================================================
	    Summary
	    -------------------------------------------------------
	    Correctly Classified Instances          :      17560       77.8921%
	    Incorrectly Classified Instances        :       4984       22.1079%
	    Total Classified Instances              :      22544

	    =======================================================
	    Confusion Matrix
	    -------------------------------------------------------
	    a       b       <--Classified as
	    9437    274      |  9711        a     = normal
	    4710    8123     |  12833       b     = anomaly

	    =======================================================
	    Statistics
	    -------------------------------------------------------
	    Kappa                                       0.5728
	    Accuracy                                   77.8921%
	    Reliability                                53.4921%
	    Reliability (standard deviation)            0.4933

  このジョブでも、\_\_wasbs:///example/data/predictions/KDDTest+.arff.out__ にファイルが生成されますが、これは、人間が判読できないファイルです。

> [AZURE.NOTE] Mahout ジョブはファイルを上書きしません。これらのジョブをもう一度実行する場合は、前のジョブで作成したファイルを削除する必要があります。

##<a name="troubleshooting"></a>トラブルシューティング

###<a name="install"></a>Mahout のインストール

Mahout は HDInsight 3.1 クラスターにインストールされますが、次の手順を使用して手動で HDInsight 3.0 または HDInsight 2.1 クラスターにインストールできます。

1. 使用する Mahout のバージョンは、使用するクラスターの HDInsight バージョンによって異なります。Azure ポータルでクラスターのプロパティを表示することで、クラスターのバージョンを見つけることができます。

  * __HDInsight 2.1 __ の場合は、[Mahout 0.9](http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar) を含む Java アーカイブ (JAR) ファイルをダウンロードできます。

  * __HDInsight 3.0 の場合は__、[Mahout をソースからビルド][build]して HDInsight により提供される Hadoop バージョンを指定する必要があります。ビルド ページに示された前提条件をインストールし、ソースをダウンロードして、次のコマンドを使用して Mahout jar ファイルを作成します。

			mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

    	ビルドが完了した後、__mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar__ で JAR ファイルを入手できます。

    	> [AZURE.NOTE] Mahout 1.0 がリリースされると、HDInsight 3.0 で既成のパッケージを使用できるようになります。

2. この jar ファイルを、使用しているクラスターの既定のストレージ内の __example/jars__ にアップロードします。次のスクリプトの CLUSTERNAME を HDInsight クラスターの名前に置き換えます。また、FILENAME を __mahout-coure-0.9-job.jar__ ファイルのパスに置き換えます。

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterName = "CLUSTERNAME"
        $fileToUpload = "FILENAME"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob "example/jars/mahout-core-0.9-job.jar" `
            -Container $container `
            -Context $context

###ファイルを上書きできない

Mahout ジョブは、処理中に作成された一時ファイルをクリーンアップしません。さらに、このジョブは既存の出力ファイルを上書きしません。

Mahout ジョブの実行中のエラーを回避するには、実行と実行の間で一時ファイルと出力ファイルを削除するか、一時ディレクトリと出力ディレクトリに一意の名前を使用します。

###JAR ファイルが見つからない

HDInsight 3.1 クラスターには Mahout が含まれていますが、パスとファイル名にはクラスターにインストールされている Mahout のバージョン番号が含まれています。このチュートリアルの Windows PowerShell サンプル スクリプトでは、2015 年 11 月時点で有効なパスを使用していますが、バージョン番号は将来の HDInsight の更新で変わります。使用しているクラスターの Mahout JAR ファイルへの現在のパスを決定するには、次の Windows PowerShell コマンドを使用して、返されるファイル パスを参照するようにスクリプトを変更します。

	Use-AzureRmHDInsightCluster -ClusterName $clusterName
    #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "wasbs:///example/statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target*-job.jar'

###<a name="nopowershell"></a>Windows PowerShell で動作しないクラス

次のクラスを使用する Mahout ジョブを Windows PowerShell から使用すると、さまざまなエラー メッセージが返されます。

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

これらのクラスを使用するジョブを実行するには、HDInsight クラスターに接続し、Hadoop コマンド ラインを使用してジョブを実行します。例については、「[Hadoop コマンド ラインを使用してデータを分類する](#classify)」を参照してください。

##次のステップ

ここまで、Mahout の使用方法を学習し、HDInsight でデータを操作するその他の方法を確認してきました。

* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での MapReduce の使用](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: ../powershell-install-configure.md
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 

<!---HONumber=AcomDC_0727_2016-->