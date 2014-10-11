<properties linkid="manage-services-hdinsight-upload-data-for-hadoop-jobs-in-hdinsight" urlDisplayName="Upload Data" pageTitle="Upload data for Hadoop jobs in HDInsight | Azure" metaKeywords="" description="Learn how to upload and access data in HDInsight using Azure Storage Explorer, Azure PowerShell, the Hadoop command line, or Sqoop." metaCanonical="" services="storage,hdinsight" documentationCenter="" title="Upload data for Hadoop jobs in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# HDInsight での Hadoop ジョブ用データのアップロード

Azure の HDInsight では、Azure BLOB ストレージ上でフル機能の Hadoop 分散ファイル システム (HDFS) を利用できます。HDFS 拡張機能として設計されており、Hadoop エコシステムのすべてのコンポーネントが管理対象のデータを直接操作できるので、ユーザーはデータがどこに格納されているのかを意識する必要がありません。Azure BLOB ストレージと HDFS はどちらも、データの保管と処理のために最適化された別個のファイル システムです。Azure BLOB ストレージを使用するメリットについては、「[HDInsight での Azure BLOB ストレージの使用][]」を参照してください。

通常、Azure の HDInsight クラスターは MapReduce ジョブを実行するためにデプロイされ、それらのジョブが完了した時点で削除されます。ジョブ完了後もデータを HDFS クラスターに保持しておくと、データの格納コストがかさんでしまいます。Azure BLOB ストレージは、可用性と拡張性に優れ、大容量、低コストの共有可能なデータ ストレージ手段であり、HDInsight で処理されるデータも格納できます。BLOB ストレージにデータを格納すれば、データを失うことなく、計算処理で使用した HDInsight クラスターを安全に解放できます。

Azure BLOB ストレージにアクセスするには、[AzCopy][]、[Azure PowerShell][]、[Azure の .NET 用ストレージ クライアント ライブラリ][]、またはエクスプローラー ツールを使用する方法があります。次に、使用可能なツールの例を示します。

-   [Azure ストレージ エクスプローラー][]
-   [Cloud Storage Studio 2][]
-   [CloudXplorer][]
-   [Azure 用エクスプローラー][]
-   [Azure 用エクスプローラー (PRO)][]

**前提条件**

この記事を読み始める前に、次の要件に気を付けてください。

-   Azure HDInsight クラスター。手順については、「[Azure HDInsight の概要][]」または「[HDInsight クラスターのプロビジョニング][]」を参照してください。

## この記事の内容

-   [AzCopy を使用して BLOB ストレージにデータをアップロードする][]
-   [Azure PowerShell を使用して BLOB ストレージにデータをアップロードする][]
-   [Azure ストレージ エクスプローラーを使用して BLOB ストレージにデータをアップロードする][]
-   [Hadoop コマンド ラインを使用して BLOB ストレージにデータをアップロードする][]
-   [Sqoop を使用して Azure SQL データベースから BLOB ストレージにデータをインポートする][]

## <span id="azcopy"></span></a>AzCopy を使用して BLOB ストレージにデータをアップロードする

AzCopy は、Azure Storage アカウントでデータの転送タスクが簡単になるコマンド ライン ユーティリティです。スタンドアロン ツールとして使用することも、既存のアプリケーションに組み込むこともできます。[AzCopy をダウンロード][]してください。

AzCopy の構文は次のとおりです。

    AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

詳細については、「[AzCopy - Uploading/Downloading files for Azure Blobs (AzCopy - Azure BLOB のファイルのアップロード/ダウンロードについて)][AzCopy]」を参照してください。

## <span id="powershell"></span></a>Azure PowerShell を使用して BLOB ストレージにデータをアップロードする

Azure PowerShell は、Azure のワークロードの展開と管理を制御し自動化するために使用できる強力なスクリプティング環境です。Azure PowerShell を使用して、BLOB ストレージにデータをアップロードして、データを MapReduce ジョブで処理可能にすることができます。コンピューターを構成して Azure PowerShell を実行する方法については、「[Azure PowerShell のインストールおよび構成][]」を参照してください。

**ローカル ファイルを BLOB ストレージにアップロードするには**

1.  Azure PowerShell コンソール ウィンドウを開きます。手順については「[Azure PowerShell のインストールおよび構成][]」を参照してください。
2.  次のスクリプトで最初の 5 つの変数の値を設定します。

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<ContainerName>"

        $fileName ="<LocalFileName>"
        $blobName = "<BlobName>"

        # Get the storage account key
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}

        # Create the storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to the Blob container        
        Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext

3.  スクリプトを Azure PowerShell コンソール ウィンドウに貼り付けて実行します。

BLOB ストレージ コンテナーには、"キーと値のペア" としてデータが格納されます。ディレクトリ階層はありません。ただし、キー名でスラッシュ (/) を使用すれば、ファイルがディレクトリ階層に保存されているように見せかけることができます。たとえば、BLOB のキー名を「*input/log1.txt*」とします。この場合、"input" ディレクトリは実際に存在しませんが、キー名でスラッシュ (/) が使用されているのでファイル パスのように見えます。先のスクリプトでは、$blobname 変数を設定することで、ファイルにフォルダー構造を与えることができます。たとえば、*$blobname="myfolder/myfile.txt"* のように設定します。

Azure エクスプローラー ツールを使用すると、サイズが 0 バイトのファイルがあることに気付きます。これらのファイルには 2 つの目的があります。

-   空のフォルダーの場合、フォルダーが存在しているという印になります。BLOB ストレージでは、foo/bar と呼ばれる BLOB が存在している場合、foo と呼ばれるフォルダーも存在すると見なされます。しかし、foo と呼ばれる空のフォルダーが必要な場合、それを示すには、この特殊な 0 バイトのファイルを配置するしか方法はありません。
-   Hadoop ファイル システムが必要とする特殊なメタデータ、特に、フォルダーのアクセス許可と所有者を保持します。

## <span id="storageexplorer"></span></a>Azure ストレージ エクスプローラーを使用して BLOB ストレージにデータをアップロードする

*Azure Storage エクスプローラー*は、Azure Storage 内のデータを調べたり、変更したりするときに役立つ便利なツールです。このツールは、[][]<http://azurestorageexplorer.codeplex.com/></a>から無料でダウンロードできます。

Azure Storage エクスプローラーを使用するには、Azure Storage のアカウント名とアカウント キーを確認しておく必要があります。この情報を取得する方法については、「[ストレージ アカウントの管理][]」の「方法: ストレージ アクセス キーの表示、コピーおよび再生成」を参照してください。

1.  Azure ストレージ エクスプローラーを実行します。

    ![HDI.AzureStorageExplorer][]

2.  **[アカウントの追加]** をクリックします。アカウントを Azure ストレージ エクスプローラーに追加した後は、この手順を再び実行する必要はありません。

    ![HDI.ASEAddAccount][]

3.  **ストレージ アカウント名**と**ストレージ アカウント キー**を入力して、**[ストレージ アカウントの追加]** をクリックします。複数のストレージ アカウントを追加できます。その場合、各アカウントがタブに表示されます。
4.  **[ストレージの種類]** で **[BLOB]** をクリックします。

    ![HDI.ASEBlob][]

5.  **[コンテナー]** で、HDInsight クラスターに関連付けられているコンテナーをクリックします。HDInsight クラスターを作成するときにコンテナーを指定する必要があります。コンテナーを指定しない場合、そのユーザー専用のクラスターが作成されます。
6.  **[BLOB]** で **[アップロード]** をクリックします。
7.  アップロードするファイルを指定して、**[開く]** をクリックします。

## <span id="commandline"></span></a> Hadoop コマンド ラインを使用して BLOB ストレージにデータをアップロードする

Hadoop コマンド ラインを使用するには、最初にリモート デスクトップを使用してクラスターに接続する必要があります。

1.  [管理ポータル][]にサインインします。
2.  **[HDINSIGHT]** をクリックします。デプロイした Hadoop クラスターが一覧表示されます。
3.  データのアップロード先となる HDInsight クラスターをクリックします。
4.  ページの上部にある **[構成]** をクリックします。
5.  まだリモート デスクトップを有効にしていない場合は、**[リモートを有効にする]** をクリックして、指示に従います。そうでない場合は、次の手順に進みます。
6.  ページの下部にある **[接続]** をクリックします。
7.  **[開く]** をクリックします。
8.  ユーザー名とパスワードを入力して **[OK]** をクリックします。
9.  **[はい]** をクリックします。
10. デスクトップで **[Hadoop コマンド ライン]** をクリックします。
11. 次のコマンド例は、HDInsight ヘッド ノード上のローカル ファイル システムから /example/data ディレクトリへ davinci.txt ファイルをコピーします。

        hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt

    既定のファイル システムが Azure BLOB ストレージ上にあるため、/example/data/davinci.txt は実際には Azure BLOB ストレージ上に存在します。このファイルは次のように表すこともできます。

        wasb:///example/data/davinci.txt 

    または

        wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

    *wasbs* を使用するときは完全修飾ドメイン名を指定する必要があります。

12. 次のコマンドを使用して、アップロードしたファイルを一覧表示します。

        hadoop dfs -lsr /example/data

## <span id="sqoop"></span></a> Sqoop を使用して SQL データベース/SQL Server から HDFS にデータをインポートする

Sqoop は、Hadoop とリレーショナル データベース間でデータを転送するためのツールです。このツールを使用して、SQL、MySQL、Oracle などのリレーショナル データベース管理システム (RDBMS) から Hadoop 分散ファイル システム (HDFS) へデータをインポートしたり、MapReduce または Hive を使用して Hadoop のデータを変換し、そのデータを RDBMS へ取り込んだりできます。詳細については、[Sqoop のユーザー ガイド][]を参照してください。

データをインポートする前に、Azure SQL データベースのサーバー名、データベース アカウント名、アカウント パスワード、データベース名を確認しておく必要があります。

既定では、Azure SQL データベースは Azure HDinsight のような Azure サービスからの接続を許可します。このファイアウォール設定が無効になっている場合は、Azure 管理ポータルから有効にする必要があります。SQL データベースの作成方法とファイアウォール ルールの構成方法については、「[SQL データベースの作成と構成][]」を参照してください。

次の手順では、PowerShell を使用して Sqoop ジョブを送信します。

**Sqoop と PowerShell を使用してデータを HDInsight にインポートするには**

1.  Azure PowerShell コンソール ウィンドウを開きます。手順については「[Azure PowerShell のインストールおよび構成][]」を参照してください。
2.  次のスクリプトで最初の 8 つの変数の値を設定します。

        $subscriptionName = "<AzureSubscriptionName>"
        $clusterName = "<HDInsightClusterName>"

        $sqlDatabaseServerName = "<SQLDatabaseServerName>"
        $sqlDatabaseUserName = "<SQLDatabaseDatabaseName>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"
        $sqlDatabaseDatabaseName = "<SQLDatabaseDatabaseName>"
        $tableName = "<SQLDatabaseTableName>"

        $hdfsOutputDir = "<OutputPath>"  # This is the HDFS path for the output file, for example "/lineItemData".

        Select-AzureSubscription $subscriptionName      
        $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseDatabaseName --table $tableName --target-dir $hdfsOutputDir -m 1" 

        $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
        Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

        Write-Host "Standard Error" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
        Write-Host "Standard Output" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

3.  スクリプトを Azure PowerShell コンソール ウィンドウに貼り付けて実行します。Azure BLOB ストレージからデータ ファイルを取得するための PowerShell サンプルについては、「[HDInsight の概要][Azure HDInsight の概要]」を参照してください。

Sqoop の使用法の詳細については、「[HDInsight と Hive の使用][]」を参照してください。

## 次のステップ

ここでは、HDInsight にデータを取り込む方法を説明しました。次の記事でデータの分析方法を学習してください。

-   [Azure HDInsight の概要][]
-   [プログラムによる Hadoop ジョブの送信][]
-   [HDInsight での Hive の使用][]
-   [HDInsight での Pig の使用][]

  [HDInsight での Azure BLOB ストレージの使用]: ../hdinsight-use-blob-storage/
  [AzCopy]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx
  [Azure PowerShell]: http://msdn.microsoft.com/en-us/library/windowsazure/jj152841.aspx
  [Azure の .NET 用ストレージ クライアント ライブラリ]: /en-us/develop/net/how-to-guides/blob-storage/
  [Azure ストレージ エクスプローラー]: http://azurestorageexplorer.codeplex.com/
  [Cloud Storage Studio 2]: http://www.cerebrata.com/Products/CloudStorageStudio/
  [CloudXplorer]: http://clumsyleaf.com/products/cloudxplorer
  [Azure 用エクスプローラー]: http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx
  [Azure 用エクスプローラー (PRO)]: http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx
  [Azure HDInsight の概要]: ../hdinsight-get-started/
  [HDInsight クラスターのプロビジョニング]: ../hdinsight-provision-clusters/
  [AzCopy を使用して BLOB ストレージにデータをアップロードする]: #azcopy
  [Azure PowerShell を使用して BLOB ストレージにデータをアップロードする]: #powershell
  [Azure ストレージ エクスプローラーを使用して BLOB ストレージにデータをアップロードする]: #storageexplorer
  [Hadoop コマンド ラインを使用して BLOB ストレージにデータをアップロードする]: #commandline
  [Sqoop を使用して Azure SQL データベースから BLOB ストレージにデータをインポートする]: #sqoop
  [AzCopy をダウンロード]: http://aka.ms/WaCopy
  [Azure PowerShell のインストールおよび構成]: ../install-configure-powershell/
  []: http://azurestorageexplorer.codeplex.com/ "Azure Storage エクスプローラー"
  [ストレージ アカウントの管理]: ../storage-manage-storage-account/
  [HDI.AzureStorageExplorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
  [HDI.ASEAddAccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
  [HDI.ASEBlob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png
  [管理ポータル]: https://manage.windowsazure.com
  [Sqoop のユーザー ガイド]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
  [SQL データベースの作成と構成]: ../sql-database-create-configure/
  [HDInsight と Hive の使用]: ../hdinsight-use-sqoop/
  [プログラムによる Hadoop ジョブの送信]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [HDInsight での Hive の使用]: ../hdinsight-use-hive/
  [HDInsight での Pig の使用]: ../hdinsight-use-pig
