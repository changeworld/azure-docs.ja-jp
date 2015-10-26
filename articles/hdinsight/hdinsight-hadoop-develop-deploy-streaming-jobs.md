
<properties
	pageTitle="HDInsight 用 C# Hadoop ストリーミング プログラムの開発 | Microsoft Azure"
	description="C# で Hadoop ストリーミング MapReduce プログラムを開発する方法、それらのプログラムを Azure HDInsight にデプロイする方法について説明します。"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/08/2015"
	ms.author="jgao"/>



# Develop C# Hadoop streaming programs for HDInsight (HDInsight 用 C# Hadoop ストリーミング プログラムの開発)

Hadoop には MapReduce に対するストリーミング API が用意されていて、Java 以外の言語の map 関数と reduce 関数を記述することができます。このチュートリアルでは、指定した入力データ内の特定の単語の出現回数を数える C# ワード カウント プログラムの作成について説明します。MapReduce フレームワークでワード カウントを実現するしくみを次の図に示します。

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

> [AZURE.NOTE]この記事の手順は、Windows ベースの Azure HDInsight クラスターに対してのみ機能します。Linux ベースの HDInsight のストリーミング例は、「[HDInsight 用 Python ストリーミング プログラムの開発](hdinsight-hadoop-streaming-python.md)」をご覧ください。

このチュートリアルでは、次の操作方法について説明します。

- C# で Hadoop ストリーミング MapReduce プログラムを開発し、HDInsight Emulator for Azure 上でテストする
- Azure HDInsight で同じ MapReduce ジョブを実行する
- MapReduce ジョブの結果を取得する

##<a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、次の作業を完了している必要があります。

- HDInsight Emulator のインストール。手順については、「[HDInsight Emulator の概要][hdinsight-get-started-emulator]」を参照してください。
- エミュレーター コンピューターへの Azure PowerShell のインストール。手順については、[Azure PowerShell のインストールおよび構成に関するページ][powershell-install]を参照してください。
- Azure サブスクリプションの入手。手順については、[購入オプション][azure-purchase-options]、[メンバー プラン][azure-member-offers]、または[無料試用版][azure-free-trial]に関するページを参照してください。


##<a name="develop"></a>C# でワード カウント Hadoop ストリーミング プログラムを開発する

ワード カウント ソリューションには、mapper と reducer という 2 つのコンソール アプリケーション プロジェクトが含まれます。mapper アプリケーションは、コンソールに各単語をストリーミングします。reducer アプリケーションは、ドキュメントからストリーミングされた単語の数をカウントします。mapper と reducer はどちらも標準入力ストリーム (stdin) から 1 行ずつ文字を読み取って、標準出力ストリーム (stdout) に書き込みます。

**C# コンソール アプリケーションを作成するには**

1. Visual Studio 2013 を開きます。
2. **[ファイル]**、**[新規]**、**[プロジェクト]** の順にクリックします。
3. 次の値を入力または選択します。


フィールド|値
---|---
テンプレート|Visual C#/Windows/コンソール アプリケーション
名前|WordCountMapper
場所|C:\\Tutorials
ソリューション名|WordCount


4. **[OK]** をクリックしてプロジェクトを作成します。

**Mapper プログラムを作成するには**

5. ソリューション エクスプローラーで **[Program.cs]** を右クリックし、**[名前の変更]** をクリックします。
6. ファイルの名前を **WordCountMapper.cs** に変更し、**Enter** キーを押します。
7. **[はい]** をクリックして、すべての参照の名前変更を確定します。
8. **WordCountMapper.cs** をダブルクリックして開きます。
9. 次の **using** ステートメントを追加します。

		using System.IO;

10. **Main()** 関数を次のコードに置き換えます。

		static void Main(string[] args)
		{
		    if (args.Length > 0)
		    {
		        Console.SetIn(new StreamReader(args[0]));
		    }

		    string line;
		    string[] words;

		    while ((line = Console.ReadLine()) != null)
		    {
		        words = line.Split(' ');

		        foreach (string word in words)
		            Console.WriteLine(word.ToLower());
		    }
		}

11. **[ビルド]**、**[ソリューションのビルド]** の順にクリックして、mapper プログラムをコンパイルします。


**Reducer プログラムを作成するには**

1. Visual Studio 2013 で **[ファイル]**、**[追加]**、**[新しいプロジェクト]** の順にクリックします。
2. 次の値を入力または選択します。

フィールド|値
---|---
テンプレート|Visual C#/Windows/コンソール アプリケーション
名前|WordCountReducer
場所|C:\\Tutorials\\WordCount

3. **[ソリューションのディレクトリを作成]** チェック ボックスをオフにし、**[OK]** をクリックしてプロジェクトを作成します。
4. ソリューション エクスプローラーで **[Program.cs]** を右クリックし、**[名前の変更]** をクリックします。
5. ファイルの名前を **WordCountReducer.cs** に変更し、**Enter** キーを押します。
7. **[はい]** をクリックして、すべての参照の名前変更を確定します。
8. **WordCountReducer.cs** をダブルクリックして開きます。
9. 次の **using** ステートメントを追加します。

		using System.IO;

10. **Main()** 関数を次のコードに置き換えます。

		static void Main(string[] args)
		{
		    string word, lastWord = null;
		    int count = 0;

		    if (args.Length > 0)
		    {
		        Console.SetIn(new StreamReader(args[0]));
		    }

		    while ((word = Console.ReadLine()) != null)
		    {
		        if (word != lastWord)
		        {
		            if(lastWord != null)
		                Console.WriteLine("{0}[{1}]", lastWord, count);

		            count = 1;
		            lastWord = word;
		        }
		        else
		        {
		            count += 1;
		        }
		    }
		    Console.WriteLine(count);
		}

11. **[ビルド]**、**[ソリューションのビルド]** の順にクリックして、reducer プログラムをコンパイルします。

mapper と reducer の実行可能ファイルは次の場所にあります。

- C:\\Tutorials\\WordCount\\WordCountMapper\\bin\\Debug\\WordCountMapper.exe
- C:\\Tutorials\\WordCount\\WordCountReducer\\bin\\Debug\\WordCountReducer.exe


##<a name="test"></a>エミュレーターでプログラムをテストする

次の操作を行って、HDInsight Emulator でプログラムをテストします。

1. データをエミュレーターのファイル システムにアップロードする
2. mapper アプリケーションと reducer アプリケーションをエミュレーターのファイル システムにアップロードする
3. ワード カウント MapReduce ジョブを送信する
4. ジョブの状態を確認する
5. ジョブの結果を取得する

既定では、HDInsight Emulator は、ファイル システムとして Hadoop 分散ファイル システム (HDFS) を使用します。必要に応じて、Azure BLOB ストレージを使用するように HDInsight Emulator を構成することもできます。詳細については、「[HDInsight Emulator の概要][hdinsight-emulator-wasb]」を参照してください。このセクションでは、HDFS **copyFromLocal** コマンドを使用して、ファイルをアップロードします。次のセクションでは、Azure PowerShell を使用してファイルをアップロードする方法について説明します。その他の方法については、「[HDInsight へのデータのアップロード][hdinsight-upload-data]」を参照してください。

このチュートリアルでは、次のフォルダー構造を使用します。

フォルダー|注
---|---
\\WordCount|ワード カウント プロジェクトのルート フォルダー。
\\WordCount\\Apps|mapper と reducer の実行可能ファイルのフォルダー。
\\WordCount\\Input|MapReduce のソース ファイル フォルダー。
\\WordCount\\Output|MapReduce の出力ファイル フォルダー。
\\WordCount\\MRStatusOutput|ジョブの出力フォルダー。


このチュートリアルでは、%hadoop\_home% ディレクトリにある .txt ファイルを使用します。

> [AZURE.NOTE]Hadoop HDFS のコマンドは大文字と小文字が区別されます。

**テキスト ファイルをエミュレーターのファイル システムにコピーするには**

1. Hadoop コマンド ライン ウィンドウから次のコマンドを実行して、入力ファイルのディレクトリを作成します。

		hadoop fs -mkdir /WordCount/
		hadoop fs -mkdir /WordCount/Input

	ここで使用しているパスは相対パスです。次のパスと同等です。

		hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

2. 次のコマンドを実行して、テキスト ファイルを HDFS 上の入力フォルダーにコピーします。

		hadoop fs -copyFromLocal %hadoop_home%\share\doc\hadoop\common*.txt \WordCount\Input

3. 次のコマンドを実行して、アップロードしたファイルを一覧表示します。

		hadoop fs -ls \WordCount\Input




**mapper と reducer をエミュレーターのファイル システムにデプロイするには**

1. デスクトップから Hadoop コマンド ラインを開き、HDFS に /Apps フォルダーを作成します。

		hadoop fs -mkdir /WordCount/Apps

2. 次のコマンドを実行します。

		hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe /WordCount/Apps/WordCountMapper.exe
		hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe /WordCount/Apps/WordCountReducer.exe

3. 次のコマンドを実行して、アップロードしたファイルを一覧表示します。

		hadoop fs -ls /WordCount/Apps

	2 つの .exe ファイルが表示されます。


**Azure PowerShell を使用して MapReduce ジョブを実行する**

1. Azure PowerShell を開きます。手順については、[Azure PowerShell のインストールおよび構成に関するページ][powershell-install]を参照してください。
3. 次のコマンドを実行して、変数を設定します。

		$clusterName = "http://localhost:50111"

		$mrMapper = "WordCountMapper.exe"
		$mrReducer = "WordCountReducer.exe"
		$mrMapperFile = "/WordCount/Apps/WordCountMapper.exe"
		$mrReducerFile = "/WordCount/Apps/WordCountReducer.exe"
		$mrInput = "/WordCount/Input/"
		$mrOutput = "/WordCount/Output"
		$mrStatusOutput = "/WordCount/MRStatusOutput"

	HDInsight Emulator のクラスター名は "http://localhost:50111" です。

4. 次のコマンドを実行して、ストリーミング ジョブを定義します。

		$mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
		$mrJobDef.Files.Add($mrMapperFile)
		$mrJobDef.Files.Add($mrReducerFile)

5. 次のコマンドを実行して、資格情報オブジェクトを作成します。

		$creds = Get-Credential -Message "Enter password" -UserName "hadoop"

	パスワードを入力するように求められます。パスワードは任意の文字列でかまいません。ユーザー名として "hadoop" を使用する必要があります。

6. 次のコマンドを実行して、MapReduce ジョブを送信し、ジョブの完了を待ちます。

		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -Credential $creds -JobDefinition $mrJobDef
		Wait-AzureHDInsightJob -Credential $creds -job $mrJob -WaitTimeoutInSeconds 3600

	ジョブが完了すると、次のような出力が表示されます。

		StatusDirectory : /WordCount/MRStatusOutput
		ExitCode        :
		Name            : mrWordCountStreamingJob
		Query           :
		State           : Completed
		SubmissionTime  : 11/15/2013 7:18:16 PM
		Cluster         : http://localhost:50111
		PercentComplete : map 100%  reduce 100%
		JobId           : job_201311132317_0034

	出力にジョブ ID (たとえば、*job-201311132317-0034*) が含まれています。

**ジョブの状態を確認するには**

1. デスクトップで **[Hadoop YARN のステータス]** をクリックするか、****http://localhost:50030/jobtracker.jsp** を参照します。
2. **[実行中]** カテゴリまたは **[完了]** カテゴリで、このジョブ ID を使用しているジョブを見つけます。
3. ジョブが失敗している場合は **[失敗]** カテゴリに表示されます。ジョブの詳細を開き、デバッグに役立つ情報を検索することもできます。


**HDFS からの出力を表示するには**

1. Hadoop コマンド ラインを開きます。
2. 次のコマンドを実行して、出力を表示します。

		hadoop fs -ls /WordCount/Output/
		hadoop fs -cat /WordCount/Output/part-00000

	"|more" をコマンドの最後に付けることで、ページ ビューを得られます。

##<a id="upload"></a>Azure BLOB ストレージにデータをアップロードする
Azure HDInsight は、既定のファイル システムとして Azure BLOB ストレージを使用します。データ ファイル用に追加の Azure BLOB ストレージを使用するように HDInsight クラスターを構成できます。このセクションでは、Azure ストレージ アカウントを作成し、データ ファイルを BLOB ストレージにアップロードします。データ ファイルは %hadoop\_home%\\share\\doc\\hadoop\\common ディレクトリ内の .txt ファイルです。


**ストレージ アカウントとコンテナーを作成するには**

1. Azure PowerShell を開きます。
2. 変数を設定し、次のコマンドを実行します。

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"  
		$containerName = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

3. 次のコマンドを実行して、ストレージ アカウントと、そのアカウントの BLOB ストレージ コンテナーを作成します。

		# Select an Azure subscription
		Select-AzureSubscription $subscriptionName

		# Create a Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -location $location

		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName -Context $destContext

4. 次のコマンドを実行して、ストレージ アカウントとコンテナーを確認します。

		Get-AzureStorageAccount -StorageAccountName $storageAccountName
		Get-AzureStorageContainer -Context $destContext

**データ ファイルをアップロードするには**

1. Azure PowerShell ウィンドウで、ローカル フォルダーとアップロード先フォルダーの値を設定します。

		$localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common"
		$destFolder = "WordCount/Input"

	ローカル ソース ファイル フォルダーは **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\doc\\hadoop\\common** で、アップロード先フォルダーは **WordCount/Input** です。ソースの場所は、HDInsight Emulator 上の .txt ファイルの場所です。アップロード先は、Azure Blob コンテナーに反映するフォルダー構造です。

3. 次のコマンドを実行して、ソース ファイル フォルダー内の .txt ファイルの一覧を取得します。

		# Get a list of the .txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

5. 次のスニペットを実行して、ファイルをコピーします。

		# Copy the files from the local workstation to the Blob container
		foreach ($file in $filesTxt){

		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"

		    write-host "Copying $fileName to $blobName"

		    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -Context $destContext
		}

6. 次のコマンドを実行して、アップロードしたファイルを一覧表示します。

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $destFolder


**ワード カウント アプリケーションをアップロードするには**

1. Azure PowerShell ウィンドウで、次の変数を設定します。

		$mapperFile = "C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe"
		$reducerFile = "C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe"
		$blobFolder = "WordCount/Apps"

	アップロード先フォルダーは、Azure Blob コンテナーに反映される構造である **WordCount/Apps** です。

2. 次のコマンドを実行して、アプリケーションをコピーします。

		Set-AzureStorageBlobContent -File $mapperFile -Container $containerName -Blob "$blobFolder/WordCountMapper.exe" -Context $destContext
		Set-AzureStorageBlobContent -File $reducerFile -Container $containerName -Blob "$blobFolder/WordCountReducer.exe" -Context $destContext

3. 次のコマンドを実行して、アップロードしたファイルを一覧表示します。

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $blobFolder

	両方のアプリケーション ファイルがそこに一覧表示されます。


##<a name="run"></a>Azure HDInsight で MapReduce ジョブを実行する

このセクションでは、MapReduce ジョブの実行に関連するすべてのタスクを実行する Azure PowerShell スクリプトについて説明します。これらのタスクには次のタスクが含まれます。

1. HDInsight クラスターをプロビジョニングする

	1. HDInsight クラスターの既定のファイル システムとして使用されるストレージ アカウントを作成する
	2. BLOB ストレージ コンテナーを作成する
	3. HDInsight クラスターの作成

2. MapReduce ジョブを送信する

	1. ストリーミング MapReduce ジョブ定義を作成する
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

		# ====== STORAGE ACCOUNT AND HDINSIGHT CLUSTER VARIABLES ======
		$subscriptionName = "<AzureSubscriptionName>"
		$stringPrefix = "<StringForPrefix>"     ### Prefix to cluster, Storage account, and container names
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$location = "<MicrosoftDataCenter>"     ### Must match the data storage account location
		$clusterNodes = 1

		$clusterName = $stringPrefix + "hdicluster"

		$storageAccountName_Default = $stringPrefix + "hdistore"
		$containerName_Default =  $stringPrefix + "hdicluster"

		# ====== THE STREAMING MAPREDUCE JOB VARIABLES ======
		$mrMapper = "WordCountMapper.exe"
		$mrReducer = "WordCountReducer.exe"
		$mrMapperFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountMapper.exe"
		$mrReducerFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountReducer.exe"
		$mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
		$mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
		$mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"

		Select-AzureSubscription $subscriptionName

		#====== CREATE A STORAGE ACCOUNT ======
		Write-Host "Create a storage account" -ForegroundColor Green
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location

		#====== CREATE A BLOB STORAGE CONTAINER ======
		Write-Host "Create a Blob storage container" -ForegroundColor Green
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default

		New-AzureStorageContainer -Name $containerName_Default -Context $destContext

		#====== CREATE AN HDINSIGHT CLUSTER ======
		Write-Host "Create an HDInsight cluster" -ForegroundColor Green
		$storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }

		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data

		Select-AzureSubscription $subscriptionName
		New-AzureHDInsightCluster -Name $clusterName -Location $location -Config $config

		#====== CREATE A STREAMING MAPREDUCE JOB DEFINITION ======
		Write-Host "Create a streaming MapReduce job definition" -ForegroundColor Green

		$mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
		$mrJobDef.Files.Add($mrMapperFile)
		$mrJobDef.Files.Add($mrReducerFile)

		#====== RUN A STREAMING MAPREDUCE JOB ======
		Write-Host "Run a streaming MapReduce job" -ForegroundColor Green
		Select-AzureSubscription $subscriptionName
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef
		Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600

		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput

		#====== DELETE THE HDINSIGHT CLUSTER ======
		Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
		Select-AzureSubscription $subscriptionName
		Remove-AzureHDInsightCluster -Name $clusterName

		#====== DELETE THE STORAGE ACCOUNT ======
		Write-Host "Delete the storage account" -ForegroundColor Green
		Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3. スクリプトの最初の 4 つの変数を設定します。**$stringPrefix** 変数は、指定した文字列をプレフィックスとして HDInsight クラスター名、ストレージ アカウント名、BLOB ストレージ コンテナー名に付けるために使用されます。これらの名前の長さは 3 ～ 24 文字である必要があるため、指定する文字列とこのスクリプトで使用する名前の合計の長さが名前の文字制限を超えないように注意してください。**$stringPrefix** には、すべて小文字を使用する必要があります。

	**$storageAccountName\_Data** 変数と **$containerName\_Data** 変数はストレージ アカウントとコンテナーで、既に前の手順で作成したものです。そこで、これらに名前を付ける必要があります。これらは、データ ファイルとアプリケーションを格納するために使用します。**$location** 変数は、データ ストレージ アカウントの場所と一致する必要があります。

4. 残りの変数を確認します。
5. スクリプト ファイルを保存します。
6. Azure PowerShell を開きます。
7. 次のコマンドを実行して、実行ポリシーを RemoteSigned に設定します。

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. プロンプトが表示されたら、HDInsight クラスターのユーザー名とパスワードを入力します。パスワードが 10 文字以上であり、大文字、小文字、数字、特殊文字がそれぞれ 1 文字以上含まれていることを確認してください。資格情報の入力を求めないようにする場合は、「[Windows PowerShell でのパスワード、セキュリティ保護された文字列、資格情報の使用][powershell-PSCredential]」をご覧ください。

Hadoop ストリーミング ジョブを送信する HDInsight .NET SDK サンプルについては、「[プログラムによる Hadoop ジョブの送信][hdinsight-submit-jobs]」をご覧ください。


##<a name="retrieve"></a>MapReduce ジョブの出力を取得する
このセクションでは、出力をダウンロードして表示する方法を示します。Excel で結果を表示する方法については、「[Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続][hdinsight-ODBC]」と「[Power Query を使用した Excel から HDInsight への接続][hdinsight-power-query]」をご覧ください。


**出力を取得するには**

1. Azure PowerShell ウィンドウを開きます。
2. ここで示している値を設定し、次のコマンドを実行します。

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<TheDataStorageAccountName>"
		$containerName = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-00000"

3. 次のコマンドを実行して、Azure Storage のコンテキスト オブジェクトを作成します。

		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  

4. 次のコマンドを実行して、出力をダウンロードして表示します。

		Get-AzureStorageBlobContent -Container $ContainerName -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"



##<a id="nextsteps"></a>次のステップ
このチュートリアルでは、Hadoop ストリーミング MapReduce ジョブを作成する方法、HDInsight Emulator でアプリケーションをテストする方法、HDInsight クラスターをプロビジョニングしてクラスターで MapReduce ジョブを実行する Azure PowerShell スクリプトを記述する方法について説明しました。詳細については、次の記事を参照してください。

- [Azure HDInsight の概要](../hdinsight-get-started.md)
- [HDInsight Emulator の概要][hdinsight-get-started-emulator]
- [HDInsight 用 Java MapReduce プログラムの開発][hdinsight-develop-mapreduce]
- [HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]
- [Azure PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]
- [HDInsight へのデータのアップロード][hdinsight-upload-data]
- [HDInsight での Hive の使用][hdinsight-use-hive]
- [HDInsight での Pig の使用][hdinsight-use-pig]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator.md
[hdinsight-emulator-wasb]: ../hdinsight-get-started-emulator.md#blobstorage
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[powershell-install]: ../powershell-install-configure.md

[image-hdi-wordcountdiagram]: ./media/hdinsight-hadoop-develop-deploy-streaming-jobs/HDI.WordCountDiagram.gif "MapReduce ワード カウント アプリケーションのフロー"

<!---HONumber=Oct15_HO3-->