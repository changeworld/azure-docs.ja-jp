<properties linkid="manage-services-hdinsight-develop-hadoop-streaming-programs-for-hdinsight" urlDisplayName="" pageTitle="Develop C# Hadoop streaming programs for HDInsight | Azure" metaKeywords="hdinsight hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce" description="Learn how to develop Hadoop streaming MapReduce programs in C#, and how to deploy them to Azure HDInsight." metaCanonical="" services="hdinsight" documentationCenter="" title="Develop C# Hadoop streaming programs for HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Develop C# Hadoop streaming programs for HDInsight (HDInsight 用 C# Hadoop ストリーミング プログラムの開発)

Hadoop には MapReduce に対するストリーミング API が用意されていて、Java 以外の言語 map 関数と reduce 関数を記述することができます。このチュートリアルでは、C# での Hadoop ストリーミング MapReduce プログラムの開発と HDInsight Emulator でのテストから、Azure HDInsight での MapReduce ジョブの実行、その結果の取得まで、全工程にわたるシナリオについて説明します。

**前提条件:**

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

-   Azure HDInsight Emulator のインストール。手順については、「[HDInsight Emulator の概要][]」を参照してください。
-   エミュレーター コンピューターへの Azure PowerShell のインストール。手順については、「[Azure PowerShell のインストールおよび構成][]」を参照してください。
-   Azure サブスクリプションの入手。手順については、[購入オプション][]、[メンバー プラン][]、または[無料評価版][]に関するページを参照してください。

## この記事の内容

-   [C# でワード カウント Hadoop ストリーミング プログラムを開発する][]
-   [エミュレーターでプログラムをテストする][]
-   [Azure BLOB ストレージにデータとアプリケーションをアップロードする][]
-   [Azure HDInsight で MapReduce プログラムを実行する][]
-   [MapReduce の結果を取得する][]
-   [次のステップ][]

## <a name="develop"></a>C# でワード カウント Hadoop ストリーミング プログラムを開発する

ワード カウント ソリューションには、mapper と reducer という 2 つのコンソール アプリケーション プロジェクトが含まれます。mapper アプリケーションは、コンソールに各単語をストリーミングします。reducer アプリケーションは、ドキュメントからストリーミングされた単語の数をカウントします。mapper と reducer はどちらも標準入力ストリーム (stdin) から 1 行ずつ文字を読み取って、標準出力ストリーム (stdout) に書き込みます。

**C# コンソール アプリケーションを作成するには**

1.  Visual Studio 2013 を開きます。
2.  **[ファイル]**、**[新規]**、**[プロジェクト]** の順にクリックします。
3.  次の値を入力または選択します。

	<table border="1">
    <tr><th>フィールド</th><th>値</th></tr>
    <tr><td>テンプレート</td><td>Visual C#/Windows/コンソール アプリケーション</td></tr>
    <tr><td>名前</td><td>WordCountMapper</td></tr>
    <tr><td>場所</td><td>C:\\Tutorials</td></tr>
    <tr><td>ソリューション名</td><td>WordCount</td></tr>
	</table>

4.  **[OK]** をクリックしてプロジェクトを作成します。

**Mapper プログラムを作成するには**

1.  ソリューション エクスプローラーで **[Program.cs]** を右クリックし、**[名前の変更]** をクリックします。
2.  ファイルの名前を **WordCountMapper.cs** に変更し、**Enter** キーを押します。
3.  **[はい]** をクリックして、すべての参照の名前変更を確定します。
4.  **WordCountMapper.cs** をダブルクリックして開きます。
5.  次の using ステートメントを追加します。

        using System.IO;

6.  Main() 関数を次のコードに置き換えます。

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

7.  **[ビルド]**、**[ソリューションのビルド]** の順にクリックして、Mapper プログラムをコンパイルします。

**Reducer プログラムを作成するには**

1.  Visual Studio 2013 で **[ファイル]**、**[追加]**、**[新しいプロジェクト]** の順にクリックします。
2.  次の値を入力または選択します。

    <table border="1">
    <tr><th>フィールド</th><th>値</th></tr>
    <tr><td>テンプレート</td><td>Visual C#/Windows/コンソール アプリケーション</td></tr>
    <tr><td>名前</td><td>WordCountReducer</td></tr>
    <tr><td>場所</td><td>C:\\Tutorials\\WordCount</td></tr>
	</table>

3.  **[OK]** をクリックしてプロジェクトを作成します。
4.  ソリューション エクスプローラーで **[Program.cs]** を右クリックし、**[名前の変更]** をクリックします。
5.  ファイルの名前を **WordCountReducer.cs** に変更し、**Enter** キーを押します。
6.  **[はい]** をクリックして、すべての参照の名前変更を確定します。
7.  **WordCountReducer.cs** をダブルクリックして開きます。
8.  次の using ステートメントを追加します。

        using System.IO;

9.  Main() 関数を次のコードに置き換えます。

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

10. **[ビルド]**、**[ソリューションのビルド]** の順にクリックして、ソリューションをコンパイルします。

Mapper と Reducer の実行可能ファイルは次の場所にあります。

-   C:\\Tutorials\\WordCount\\WordCountMapper\\bin\\Debug\\WordCountMapper.exe
-   C:\\Tutorials\\WordCount\\WordCountReducer\\bin\\Debug\\WordCountReducer.exe

## <a name="test"></a>エミュレーターでプログラムをテストする

このセクションは、次の工程で構成されています。

1.  データをエミュレーター上の HDFS にアップロードする
2.  Mapper と Reducer をエミュレーター上の HDFS にアップロードする
3.  ワード カウント MapReduce ジョブを送信する
4.  ジョブの状態を確認する
5.  ジョブの結果を取得する

HDInsight Emulator は既定のファイル システムとして HDFS を使用します。必要に応じて、Azure BLOB ストレージを使用するように HDInsight Emulator を構成することもできます。詳細については、「[HDInsight Emulator の概要][1]」を参照してください。このセクションでは、HDFS copyFromLocal コマンドを使用して、ファイルをアップロードします。次のセクションでは、Azure PowerShell を使用してファイルをアップロードする方法について説明します。その他の方法については、「[HDInsight へのデータのアップロード][]」を参照してください。

このチュートリアルでは、次のフォルダー構造を使用します。

<table border="1">
<tr><th>フォルダー</th><th>注</th></tr>
<tr><td>\\WordCount</td><td>ワード カウント プロジェクトのルート フォルダー。</td></tr>
<tr><td>\\WordCount\\Apps</td><td>mapper と reducer の実行可能ファイルのフォルダー。</td></tr>
<tr><td>\\WordCount\\Input</td><td>MapReduce のソース ファイル フォルダー。</td></tr>
<tr><td>\\WordCount\\Output</td><td>MapReduce の出力ファイル フォルダー。</td></tr>
<tr><td>\\WordCount\\MRStatusOutput</td><td>ジョブの出力フォルダー。</td></tr>
</table>
</br>

このチュートリアルでは、%hadoop\_home% ディレクトリにある .txt ファイルを使用します。

> [WACOM.NOTE] Hadoop HDFS のコマンドは大文字と小文字が区別されます。

**テキスト ファイルをエミュレーター上の HDFS にコピーするには**

1.  Hadoop コマンド ライン ウィンドウから次のコマンドを実行して、入力ファイルのディレクトリを作成します。

        hadoop fs -mkdir /WordCount/Input

    ここで使用しているパスは相対パスです。次のパスと同等です。

        hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

2.  次のコマンドを実行して、テキスト ファイルを HDFS 上の入力フォルダーにコピーします。

        hadoop fs -copyFromLocal %hadoop_home%\*.txt \WordCount\Input

3.  次のコマンドを実行して、アップロードしたファイルを一覧表示します。

        hadoop fs -ls \WordCount\Input

    8 個の .txt ファイルが表示されます。

**Mapper と Reducer をエミュレーター上の HDFS に 展開するには**

1.  デスクトップから Hadoop コマンド ラインを開きます。
2.  次のコマンドを実行します。

        hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe /WordCount/Apps/WordCountMapper.exe
        hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe /WordCount/Apps/WordCountReducer.exe

3.  次のコマンドを実行して、アップロードしたファイルを一覧表示します。

        hadoop fs -lsr /WordCount/Apps

    2 つの .exe ファイルが表示されます。

**HDInsight PowerShell を使用して MapReduce ジョブを実行するには**

1.  Azure PowerShell を開きます。手順については、[Azure PowerShell のインストールおよび構成に関するページ][Azure PowerShell のインストールおよび構成]を参照してください。
2.  次のコマンドを実行して、変数を設定します。

        $clusterName = "http://localhost:50111"

        $mrMapper = "WordCountMapper.exe"
        $mrReducer = "WordCountReducer.exe"
        $mrMapperFile = "/WordCount/Apps/WordCountMapper.exe"
        $mrReducerFile = "/WordCount/Apps/WordCountReducer.exe"
        $mrInput = "/WordCount/Input/"
        $mrOutput = "/WordCount/Output"
        $mrStatusOutput = "/WordCount/MRStatusOutput"

    HDInsight Emulator のクラスター名は "<http://localhost:50111>" です。

3.  次のコマンドを実行して、ストリーミング ジョブを定義します。

        $mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
        $mrJobDef.Files.Add($mrMapperFile)
        $mrJobDef.Files.Add($mrReducerFile)

4.  次のコマンドを実行して、資格情報オブジェクトを作成します。

        $creds = Get-Credential -Message "Enter password" -UserName "hadoop"

    パスワードを入力するように求められます。パスワードは任意の文字列でかまいません。ユーザー名として "hadoop" を使用する必要があります。

5.  次のコマンドを実行して、MapReduce ジョブを送信し、ジョブの完了を待ちます。

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

    たとえば、ここではジョブ ID が job-201311132317-0034 であることがわかります。

**ジョブの状態を確認するには**

1.  デスクトップで **[Hadoop MapReduce Status]** をクリックするか、**<http://localhost:50030/jobtracker.jsp>** を参照します。
2.  **[Completed Jobs]**、**[Running Jobs]**、**[Retired Jobs]** 3 つのうちのいずれかのセクションでジョブ ID を使用してジョブを見つけます。
3.  ジョブが失敗した場合は、ジョブの詳細を開き、デバッグに役立つ情報を検索できます。

**HDFS からの出力を表示するには**

1.  Hadoop コマンド ラインを開きます。
2.  次のコマンドを実行して、出力を表示します。

        hadoop fs -ls /WordCount/Output/
        hadoop fs -cat /WordCount/Output/part-00000

    "|more" をコマンドの最後に付けることで、ページ ビューを得られます。

## <span id="upload"></span></a>Azure BLOB ストレージにデータをアップロードする

Azure HDInsight は、既定のファイル システムとして Azure BLOB ストレージを使用します。データ ファイル用に追加の Azure BLOB ストレージを使用するように HDInsight クラスターを構成できます。このセクションでは、ストレージ アカウントを作成し、データ ファイルを BLOB ストレージにアップロードします。データ ファイルは %hadoop\_home% ディレクトリ内の txt ファイルです。

**BLOB ストレージとコンテナーを作成するには**

1.  Azure PowerShell を開きます。
2.  変数を設定し、次のコマンドを実行します。

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"  
        $containerName = "<ContainerName>"
        $location = "<MicrosoftDataCenter>"  # For example, "East US"

3.  次のコマンドを実行して、ストレージ アカウントと、そのアカウントの BLOB ストレージ コンテナーを作成します。

        # Select Azure subscription
        Select-AzureSubscription $subscriptionName

        # Create a storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName -location $location

        # Create a Blob storage container
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  
        New-AzureStorageContainer -Name $containerName -Context $destContext

4.  次のコマンドを実行して、ストレージ アカウントとコンテナーを確認します。

        Get-AzureStorageAccount -StorageAccountName $storageAccountName
        Get-AzureStorageContainer -Context $destContext

**データ ファイルをアップロードするには**

1.  Azure PowerShell を開きます。
2.  最初の 3 つの変数を設定し、次のコマンドを実行します。

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"  
        $containerName = "<ContainerName>"

        $localFolder = "c:\Hadoop\hadoop-1.1.0-SNAPSHOT"
        $destFolder = "WordCount/Input"

    ソース ファイル フォルダーは **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT** で、アップロード先フォルダーは **WordCount/Input** です。

3.  次のコマンドを実行して、ソース ファイル フォルダー内の txt ファイルの一覧を取得します。

        # Get a list of the txt files
        $filesAll = Get-ChildItem $localFolder
        $filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

4.  次のコマンドを実行して、ストレージ コンテキスト オブジェクトを作成します。

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

5.  次のコマンドを実行して、ファイルをコピーします。

        # Copy the file from local workstation to the Blob container        
        foreach ($file in $filesTxt){

            $fileName = "$localFolder\$file"
            $blobName = "$destFolder/$file"

            write-host "Copying $fileName to $blobName"

            Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -Context $destContext
        }

6.  次のコマンドを実行して、アップロードしたファイルを一覧表示します。

        # List the uploaded files in the Blob storage container
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $destFolder

**ワード カウント アプリケーションをアップロードするには**

1.  Azure PowerShell を開きます。
2.  最初の 3 つの変数を設定し、次のコマンドを実行します。

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"  
        $containerName = "<ContainerName>"

        $mapperFile = "C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe"
        $reducerFile = "C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe"
        $blobFolder = "WordCount/Apps"

    アップロード先フォルダーは **WordCount/Apps** です。

3.  次のコマンドを実行して、ストレージ コンテキスト オブジェクトを作成します。

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

4.  次のコマンドを実行して、アプリケーションをコピーします。

        Set-AzureStorageBlobContent -File $mapperFile -Container $containerName -Blob "$blobFolder/WordCountMapper.exe" -Context $destContext
        Set-AzureStorageBlobContent -File $reducerFile -Container $containerName -Blob "$blobFolder/WordCountReducer.exe" -Context $destContext

5.  次のコマンドを実行して、アップロードしたファイルを一覧表示します。

        # List the uploaded files in the Blob storage container
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $blobFolder

    両方のファイルがそこに一覧表示されます。

## <a name="run"></a>Azure HDInsight で MapReduce ジョブを実行する

ここで示している PowerShell スクリプトは次のタスクを実行します。

1.  HDInsight クラスターをプロビジョニングする

    1.  HDInsight クラスターの既定のファイル システムとして使用されるストレージ アカウントを作成する
    2.  BLOB ストレージ コンテナーを作成する
    3.  HDInsight クラスターを作成する

2.  MapReduce ジョブを送信する

    1.  ストリーミング MapReduce ジョブ定義を作成する
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
        $storageAccountName_Data = "<TheDataStorageAccountName>"
        $containerName_Data = "<TheDataBlobStorageContainerName>"
        $location = "<MicrosoftDataCenter>"     ### must match the data storage account location
        $clusterNodes = 1

        $clusterName = $serviceNameToken + "hdicluster"

        $storageAccountName_Default = $serviceNameToken + "hdistore"
        $containerName_Default =  $serviceNameToken + "hdicluster"

        # The streaming MapReduce job variables
        $mrMapper = "WordCountMapper.exe"
        $mrReducer = "WordCountReducer.exe"
        $mrMapperFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountMapper.exe"
        $mrReducerFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountReducer.exe"
        $mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
        $mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
        $mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"

        Select-AzureSubscription $subscriptionName

        #=============================
        # Create a storage account
        Write-Host "Create a storage account" -ForegroundColor Green
        New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location

        #=============================
        # Create a Blob storage container
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

        New-AzureHDInsightCluster -Subscription $subscriptionName  -Name $clusterName -Location $location -Config $config

        #=============================
        # Create a streaming MapReduce job definition
        Write-Host "Create a streaming MapReduce job definition" -ForegroundColor Green

        $mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
        $mrJobDef.Files.Add($mrMapperFile)
        $mrJobDef.Files.Add($mrReducerFile)

        #=============================
        # Run a streaming MapReduce job
        Write-Host "Run a streaming MapReduce job" -ForegroundColor Green
        $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -Subscription $subscriptionName -JobDefinition $mrJobDef 
        Wait-AzureHDInsightJob -Subscription $subscriptionName -Job $mrJob -WaitTimeoutInSeconds 3600 

        Get-AzureHDInsightJobOutput -Cluster $clusterName -Subscription $subscriptionName -JobId $mrJob.JobId -StandardError 
        Get-AzureHDInsightJobOutput -Cluster $clusterName -Subscription $subscriptionName -JobId $mrJob.JobId -StandardOutput

        #=============================
        # Delete the HDInsight cluster
        Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
        Remove-AzureHDInsightCluster -Name $clusterName -Subscription $subscriptionName 

        # Delete the storage account
        Write-Host "Delete the storage account" -ForegroundColor Green
        Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3.  スクリプトの最初の 4 つの変数を設定します。$serviceNameToken は HDInsight クラスター名、ストレージ アカウント名、BLOB ストレージ コンテナー名に使用されます。サービス名は 3 〜 24 文字であり、スクリプトは最大 10 文字の文字列を名前に付加するため、サービス名の文字列は 14 文字以下に制限する必要があります。また、$ serviceNameToken には小文字を使用する必要があります。$storageAccountName\_Data と $containerName\_Data はデータ ファイルとアプリケーションの格納に使用されるストレージ アカウントとコンテナーです。$location は、データ ストレージ アカウントの場所と一致する必要があります。
4.  残りの変数を確認します。
5.  スクリプト ファイルを保存します。
6.  Azure PowerShell を開きます。
7.  次のコマンドを実行して、実行ポリシーを RemoteSigned に設定します。

        PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8.  プロンプトが表示されたら、HDInsight クラスターのユーザー名とパスワードを入力します。スクリプトの最後でクラスターを削除し、ユーザー名とパスワードが必要なくなるため、ユーザー名とパスワードとして任意の文字列を使用できます。資格情報の入力を求めないようにする場合は、[Windows PowerShell でのパスワード、セキュリティ保護された文字列、資格情報の使用][]に関するページを参照してください。

Hadoop ストリーミング ジョブを送信する HDInsight .NET SDK サンプルについては、「[プログラムによる Hadoop ジョブの送信][]」を参照してください。

## <a name="retrieve"></a>MapReduce ジョブの出力を取得する

このセクションでは、出力をダウンロードして表示する方法を示します。Excel で結果を表示する方法については、「[Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続][]」と「[Power Query を使用した Excel から HDInsight への接続][]」を参照してください。

**出力を取得するには**

1.  Azure PowerShell ウィンドウを開きます。
2.  ここで示している値を設定し、次のコマンドを実行します。

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<TheDataStorageAccountName>"
        $containerName = "<TheDataBlobStorageContainerName>"
        $blobName = "WordCount/Output/part-00000"

3.  次のコマンドを実行して、Azure Storage のコンテキスト オブジェクトを作成します。

        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  

4.  次のコマンドを実行して、出力をダウンロードして表示します。

        Get-AzureStorageBlobContent -Container $ContainerName -Blob $blobName -Context $storageContext -Force
        cat "./$blobName" | findstr "there"

## <span id="nextsteps"></span></a>次のステップ

このチュートリアルでは、Hadoop ストリーミング MapReduce ジョブを作成する方法、HDInsight Emulator でアプリケーションをテストする方法、HDInsight クラスターをプロビジョニングしてクラスターで MapReduce を実行する PowerShell スクリプトを記述する方法について説明しました。詳細については、次の記事を参照してください。

-   [Azure HDInsight の概要][]
-   [HDInsight Emulator の概要][]
-   [Develop Java MapReduce programs for HDInsight (HDInsight 用 Java MapReduce プログラムの開発)][]
-   [HDInsight での Azure BLOB ストレージの使用][]
-   [PowerShell を使用した HDInsight の管理][]
-   [HDInsight へのデータのアップロード][]
-   [HDInsight での Hive の使用][]
-   [HDInsight での Pig の使用][]

  [HDInsight Emulator の概要]: ../hdinsight-get-started-emulator/
  [Azure PowerShell のインストールおよび構成]: ../install-configure-powershell/
  [購入オプション]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [メンバー プラン]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [無料評価版]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [C# でワード カウント Hadoop ストリーミング プログラムを開発する]: #develop
  [エミュレーターでプログラムをテストする]: #test
  [Azure BLOB ストレージにデータとアプリケーションをアップロードする]: #upload
  [Azure HDInsight で MapReduce プログラムを実行する]: #run
  [MapReduce の結果を取得する]: #retrieve
  [次のステップ]: #nextsteps
  [1]: ../hdinsight-get-started-emulator/#blobstorage
  [HDInsight へのデータのアップロード]: ../hdinsight-upload-data/
  [Windows PowerShell でのパスワード、セキュリティ保護された文字列、資格情報の使用]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
  [プログラムによる Hadoop ジョブの送信]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Microsoft Hive ODBC ドライバーを使用した Excel から HDInsight への接続]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Power Query を使用した Excel から HDInsight への接続]: ../hdinsight-connect-excel-power-query/
  [Azure HDInsight の概要]: ../hdinsight-get-started/
  [Develop Java MapReduce programs for HDInsight (HDInsight 用 Java MapReduce プログラムの開発)]: ../hdinsight-develop-deploy-java-mapreduce/
  [HDInsight での Azure BLOB ストレージの使用]: ../hdinsight-use-blob-storage/
  [PowerShell を使用した HDInsight の管理]: ../hdinsight-administer-use-powershell/
  [HDInsight での Hive の使用]: ../hdinsight-use-hive/
  [HDInsight での Pig の使用]: ../hdinsight-use-pig/
