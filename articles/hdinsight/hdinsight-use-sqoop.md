<properties
	pageTitle="HDInsight での Hadoop Sqoop の使用 | Microsoft Azure"
	description="コンピューターから Azure PowerShell を使用して、Hadoop クラスターと Azure SQL データベース間で Sqoop インポートとエクスポートを実行する方法について説明します。"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="jgao"/>

#HDInsight の Hadoop での Sqoop の使用 (Windows)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

ワークステーションから Azure PowerShell と HDInsight .NET SDK を使用して Sqoop を実行し、HDInsight クラスターと Azure SQL データベースまたは SQL Server データベース間でインポートとエクスポートを行う方法を説明します。

> [AZURE.NOTE]この記事の手順は、Windows ベースまたは Linux ベースの HDInsight クラスターで使用できます。ただし、これらの手順は Windows クライアントでのみ機能します。
>
> Linux、OS X、または Unix クライアントと Linux ベースの HDInsight サーバーを使用している場合は、「[HDInsight の Hadoop での Sqoop の使用 (SSH)](hdinsight-use-sqoop-mac-linux.md)」を参照してください。

##Sqoop とは

Hadoop はログやファイルなどの非構造化データおよび半構造化データを処理する場合に自然な選択ですが、リレーショナル データベースに格納された構造化データを処理する必要が生じることもあります。

[Sqoop][sqoop-user-guide-1.4.4] は、Hadoop クラスターとリレーショナル データベース間でデータを転送するためのツールです。このツールを使用して、SQL Server、MySQL、Oracle などのリレーショナル データベース管理システム (RDBMS) から Hadoop 分散ファイル システム (HDFS) へデータをインポートしたり、MapReduce または Hive を使用して Hadoop のデータを変換してから、そのデータを RDBMS にエクスポートして戻したりできます。このチュートリアルでは、リレーショナル データベースとして SQL Server データベースを使用します。

HDInsight クラスターでサポートされている Sqoop のバージョンについては、「[HDInsight で提供されるクラスター バージョンの新機能][hdinsight-versions]」を参照してください。

##前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure PowerShell を実行できるワークステーション**。[Azure PowerShell のインストールおよび使用](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)に関するページを参照してください。Azure PowerShell スクリプトを実行するには、Azure PowerShell を管理者として実行し、実行ポリシーを *RemoteSigned* に設定する必要があります。「[Run Windows PowerShell scripts (Windows PowerShell スクリプトの実行)][powershell-script]」を参照してください。

- **Azure HDInsight クラスター**: クラスターのプロビジョニングの手順については、「[HDInsight の概要][hdinsight-get-started]」または「[HDInsight クラスターのプロビジョニング][hdinsight-provision]」を参照してください。このチュートリアルを読み進めるには、次のデータが必要です。

	<table border="1">
	<tr><th>クラスター プロパティ</th><th>Azure PowerShell 変数名</th><th>値</th><th>説明</th></tr>
	<tr><td>HDInsight クラスター名</td><td>$clusterName</td><td></td><td>HDInsight クラスター名</td></tr>
	<tr><td>Azure ストレージ アカウント名</td><td>$storageAccountName</td><td></td><td>HDInsight クラスターで使用できる Azure ストレージ アカウント。このチュートリアルでは、クラスターのプロビジョニング プロセス中に指定された既定のストレージ アカウントを使用します。</td></tr>
	<tr><td>Azure BLOB コンテナー名</td><td>$containerName</td><td></td><td>この例では、既定の HDInsight クラスター ファイル システムで使用する BLOB の名前を使用します。既定では、HDInsight クラスターと同じ名前です。</td></tr>
	</table>

- **Azure SQL データベース**: ワークステーションから Azure SQL データベース サーバーに対するアクセスを許可するようにファイアウォール ルールを構成する必要があります。Azure SQL データベースを作成して、ファイアウォールを構成する手順については、「[Azure SQL データベースの概要][sqldatabase-get-started]」を参照してください。この記事には、このチュートリアルに必要な Azure SQL データベース テーブルを作成するための Windows PowerShell スクリプトが示されています。

	<table border="1">
	<tr><th>Azure SQL データベースのプロパティ</th><th>Azure PowerShell 変数名</th><th>値</th><th>説明</th></tr>
	<tr><td>Azure SQL データベース サーバー名</td><td>$sqlDatabaseServer</td><td></td><td>Sqoop によるデータのエクスポート先、またはインポート元になる Azure SQL データベース サーバー。</td></tr>
	<tr><td>Azyre SQL データベースのログイン名</td><td>$sqlDatabaseLogin</td><td></td><td>Azure SQL データベースのログイン名。</td></tr>
	<tr><td>Azure SQL データベースのログイン パスワード</td><td>$sqlDatabasePassword</td><td></td><td>Azure SQL データベースのログイン パスワード。</td></tr>
	<tr><td>Azure SQL データベース名</td><td>$sqlDatabaseName</td><td></td><td>Sqoop によるデータのエクスポート先、またはインポート元になる Azure SQL データベース。</td></tr>
	</table>

	> [AZURE.NOTE]既定では、Azure SQL データベースは Azure HDinsight などの Azure サービスからの接続を許可します。このファイアウォール設定が無効になっている場合は、Azure プレビュー ポータルから有効にする必要があります。Azure SQL データベースの作成方法とファイアウォール ルールの構成方法については、「[SQL データベースの作成と構成][sqldatabase-create-configue]」を参照してください。

* **SQL Server**: HDInsight クラスターが SQL Server と同じ Azure の仮想ネットワーク上にある場合は、この記事の手順を使用して、SQL Server データベースとの間でデータをインポートおよびエクスポートできます。

	> [AZURE.NOTE]HDInsight は場所ベースの仮想ネットワークのみをサポートし、アフィニティ グループ ベースの仮想ネットワークは現在扱っていません。

	* 仮想ネットワークを作成および構成する場合は、「[仮想ネットワークの構成タスク](http://msdn.microsoft.com/library/azure/jj156206.aspx)」を参照してください。

		* SQL Server をデータセンター内で使用している場合は、仮想ネットワークを*サイト間*または*ポイント対サイト*として構成する必要があります。

			> [AZURE.NOTE]**ポイント対サイト**仮想ネットワークの場合、SQL Server が VPN クライアント構成アプリケーションを実行している必要があります。このアプリケーションは、Azure 仮想ネットワーク構成の**ダッシュボード**から入手できます。

		* SQL Server を Azure 仮想マシンで使用する際に、SQL Server をホストする仮想マシンが HDInsight と同じ仮想ネットワークに属している場合は、任意の仮想ネットワーク構成を使用できます。

	* 仮想ネットワークに HDInsight クラスターをプロビジョニングする場合は、「[カスタム オプションを使用した HDInsight での Hadoop クラスターのプロビジョニング](hdinsight-provision-clusters.md)」を参照してください。

	> [AZURE.NOTE]SQL Server では認証を許可する必要もあります。この記事の手順を実行するには、SQL Server ログインを使用する必要があります。

	<table border="1">
<tr><th>SQL Server データベースのプロパティ</th><th>Azure PowerShell 変数名</th><th>値</th><th>説明</th></tr>
<tr><td>SQL Server name</td><td>$sqlDatabaseServer</td><td></td><td>Sqoop によるデータのエクスポート先、またはインポート元になる SQL Server。</td></tr>
<tr><td>SQL Server login name</td><td>$sqlDatabaseLogin</td><td></td><td>SQL Server のログイン名。</td></tr>
<tr><td>SQL Server ログイン パスワード</td><td>$sqlDatabasePassword</td><td></td><td>SQL Server のログイン パスワード。</td></tr>
<tr><td>SQL Server database name</td><td>$sqlDatabaseName</td><td></td><td>Sqoop によるデータのエクスポート先、またはインポート元になる SQL Server データベース。</td></tr>
</table>


> [AZURE.NOTE]上のテーブルに値を入力します。そうしておくと、このチュートリアルを読み進める際に役に立ちます。

##シナリオの理解
HDInsight クラスターにはサンプル データがいくつか付属しています。そのうちの以下の 2 つを使用します。

- log4j ログ ファイル (*/example/data/sample.log*)。次のようなログがファイルから抽出されます。

		2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
		2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
		2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
		...

- *hivesampletable* という名前の Hive テーブル。*/hive/warehouse/hivesampletable* にあるデータ ファイルを参照しています。テーブルはモバイル デバイスのデータを含んでいます。Hive テーブルのスキーマは次のとおりです。

	<table border="1">
<tr><th>フィールド</th><th>データ型</th></tr>
<tr><td>clientid</td><td>string</td></tr>
<tr><td>querytime</td><td>string</td></tr>
<tr><td>market</td><td>string</td></tr>
<tr><td>deviceplatform</td><td>string</td></tr>
<tr><td>devicemake</td><td>string</td></tr>
<tr><td>devicemodel</td><td>string</td></tr>
<tr><td>state</td><td>string</td></tr>
<tr><td>country</td><td>string</td></tr>
<tr><td>querydwelltime</td><td>double</td></tr>
<tr><td>sessionid</td><td>bigint</td></tr>
<tr><td>sessionpagevieworder</td><td>bigint</td></tr>
</table>

まず、*sample.log* と *hivesampletable* を Azure SQL データベースまたは SQL Server にエクスポートしてから、次のパスを使用して、モバイル デバイスのデータを含むテーブルを HDInsight にインポートして戻します。

	/tutorials/usesqoop/importeddata

###HDInsight ストレージについて

HDInsight はデータ ストレージとして Azure BLOB ストレージを使用します。詳細については、「[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]」を参照してください。

HDInsight クラスターをプロビジョニングするときに、HDFS と同じように、Azure ストレージ アカウントと、そのアカウントの特定の BLOB ストレージ コンテナーを、既定のファイル システムとして指定します。プロビジョニング プロセス中に、このストレージ アカウントに加えて、同じ Azure サブスクリプションか、別の Azure サブスクリプションに属するストレージ アカウントをさらに追加することもできます。

ストレージ アカウントをさらに追加する手順については、「[HDInsight クラスターのプロビジョニング][hdinsight-provision]」を参照してください。このチュートリアルで使用する Windows PowerShell スクリプトを簡単にするために、ファイルはすべて、*/tutorials/usesqoop* にある既定のファイル システム コンテナーに格納されています。既定では、このコンテナーの名前は HDInsight クラスター名と同じです。の構文は次のとおりです。

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE]HDInsight クラスター バージョン 3.0 では、**wasb://* 構文のみがサポートされます。旧バージョンの **asv://* 構文は、HDInsight 2.1 および 1.6 クラスターではサポートされますが、HDInsight 3.0 クラスターではサポートされません。

> [AZURE.NOTE]**wasb://* パスは仮想パスです。詳細については、「[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]」を参照してください。

既定のファイル システム BLOB に格納されているファイルは、次の URI のどれを使用しても HDInsight からアクセスできます (以下の例では sample.log を使用)。

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
	wasb:///example/data/sample.log
	/example/data/sample.log

ストレージ アカウントから直接ファイルにアクセスする場合、ファイルの BLOB 名は次のようになります。

	example/data/sample.log


##チュートリアルを準備する

Azure SQL データベースまたは SQL Server で 2 つのテーブルを作成します。これらは、チュートリアルの後半で Sqoop エクスポートで使用されます。また、Sqoop による処理の前に、sample.log ファイルを処理する必要もあります。

###SQL テーブルを作成する

**Azure SQL データベースの場合**

1. Windows PowerShell ISE を開きます (Windows 8 のスタート画面で、「**PowerShell\_ISE**」と入力し、**[Windows PowerShell ISE]** をクリックします。「[Start Windows PowerShell on Windows 8 and Windows (Windows 8 と Windows での Windows PowerShell の起動)][powershell-start])」を参照してください。

2. 次のスクリプトをスクリプト ウィンドウにコピーし、最初の 4 個の変数を設定します。

		#SQL database variables
		$sqlDatabaseServer = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseName = "<SQLDatabaseName>"

		$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"

	変数の詳細については、このチュートリアルの「[前提条件](#prerequisites)」セクションを参照してください。

3. スクリプト ウィンドウの末尾に次のスクリプトを追加します。これは 2 つのテーブルとクラスター化インデックスを定義する SQL ステートメントです。Azure SQL データベースにはクラスター化インデックスが必要です。

		# SQL query strings for creating tables and clustered indexes
		$cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
		    [t1] [nvarchar](50),
		    [t2] [nvarchar](50),
		    [t3] [nvarchar](50),
		    [t4] [nvarchar](50),
		    [t5] [nvarchar](50),
		    [t6] [nvarchar](50),
		    [t7] [nvarchar](50))"

		$cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"

		$cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
		[clientid] [nvarchar](50),
		[querytime] [nvarchar](50),
		[market] [nvarchar](50),
		[deviceplatform] [nvarchar](50),
		[devicemake] [nvarchar](50),
		[devicemodel] [nvarchar](50),
		[state] [nvarchar](50),
		[country] [nvarchar](50),
		[querydwelltime] [float],
		[sessionid] [bigint],
		[sessionpagevieworder][bigint])"

		$cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"

4. SQL コマンドを実行するために、スクリプト ウィンドウの末尾に次のスクリプトを追加します。

		Write-Host "Connect to the SQL Database ..." -ForegroundColor Green
		$conn = New-Object System.Data.SqlClient.SqlConnection
		$conn.ConnectionString = $sqlDatabaseConnectionString
		$conn.Open()

		Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
		$cmd = New-Object System.Data.SqlClient.SqlCommand
		$cmd.Connection = $conn
		$cmd.CommandText = $cmdCreateLog4jTable
		$ret = $cmd.ExecuteNonQuery()
		$cmd.CommandText = $cmdCreateLog4jClusteredIndex
		$cmd.ExecuteNonQuery()

		Write-Host "Create log4j table and clustered index ..." -ForegroundColor Green
		$cmd.CommandText = $cmdCreateMobileTable
		$cmd.ExecuteNonQuery()
		$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
		$cmd.ExecuteNonQuery()

		Write-Host "Close connection ..." -ForegroundColor Green
		$conn.close()

		Write-Host "Done" -ForegroundColor Green

5. **[スクリプトの実行]** をクリックするか、**F5** キーを押して、スクリプトを実行します。
6. [プレビュー ポータル][azure-management-portal]を使用して、テーブルとクラスター化インデックスを確認します。

**SQL Server の場合**

1. **SQL Server Management Studio** を開き、SQL Server に接続します。

2. **sqoopdb** という名前の新しいデータベースを作成します。

3. **sqoopdb** データベースを選択し、次に、SQL Server Management Studio の上部のリボンから **[新しいクエリ]** を選択します。

4. クエリ ウィンドウに次の情報を入力します。

		CREATE TABLE [dbo].[log4jlogs](
		 [t1] [nvarchar](50),
		 [t2] [nvarchar](50),
		 [t3] [nvarchar](50),
		 [t4] [nvarchar](50),
		 [t5] [nvarchar](50),
		 [t6] [nvarchar](50),
		 [t7] [nvarchar](50))

		CREATE TABLE [dbo].[mobiledata](
		 [clientid] [nvarchar](50),
		 [querytime] [nvarchar](50),
		 [market] [nvarchar](50),
		 [deviceplatform] [nvarchar](50),
		 [devicemake] [nvarchar](50),
		 [devicemodel] [nvarchar](50),
		 [state] [nvarchar](50),
		 [country] [nvarchar](50),
		 [querydwelltime] [float],
		 [sessionid] [bigint],
		 [sessionpagevieworder][bigint])

5. **F5** をクリックするか、リボンの [**! 実行**] を選択してクエリを実行します。クエリの下に次のメッセージが表示されます。

		Command(s) completed successfully.

6. SQL Server Management Studio を閉じます。

###データを生成する

このチュートリアルでは、log4j ログ ファイル (区切られたファイル) と Hive テーブルを Azure SQL データベースにエクスポートします。区切られたファイルは */example/data/sample.log* と呼ばれます。チュートリアルの前半で、log4j ログのサンプルをいくつか示しました。ログ ファイルには空白行がいくつかあり、その他の行は次のような内容です。

	java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
		at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)

これは、このデータを使用する他の例に適していますが、Azure SQL データベースまたは SQL Server にインポートする前にこれらの例外を削除する必要があります。空の文字列がある場合、または Azure SQL データベース テーブルで定義されているフィールドの数よりも要素の数が少ない行がある場合、Sqoop エクスポートは失敗します。log4jlogs テーブルには 7 個の文字列型のフィールドがあります。

**sample.log ファイルを処理するには**

1. Windows PowerShell ISE を開きます。
2. 下のウィンドウで、次のコマンドを実行して、Azure サブスクリプションに接続します。

		Add-AzureAccount

	Azure アカウント資格情報の入力を求められます。サブスクリプション接続を追加するこの方法はタイム アウトし、12 時間後には、再度ログインする必要があります。

	> [AZURE.NOTE]Azure サブスクリプションが複数あり、使用するサブスクリプションが既定のサブスクリプションではない場合は、<strong>Select-AzureSubscription</strong> コマンドレットを使用して現在のサブスクリプションを選択します。

3. 次のスクリプトをスクリプト ウィンドウにコピーし、最初の 2 個の変数を設定します。

		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<BlobContainerName>"

		$sourceBlobName = "example/data/sample.log"
		$destBlobName = "tutorials/usesqoop/data/sample.log"

	変数の詳細については、このチュートリアルの「[前提条件](#prerequisites)」セクションを参照してください。

4. スクリプト ウィンドウの末尾に、次のスクリプトを追加します。

		# Define the connection string
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey"

		# Create block blob objects referencing the source and destination blob.
		$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
		$storageClient = $storageAccount.CreateCloudBlobClient();
		$storageContainer = $storageClient.GetContainerReference($containerName)
		$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
		$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

		# Define a MemoryStream and a StreamReader for reading from the source file
		$stream = New-Object System.IO.MemoryStream
		$stream = $sourceBlob.OpenRead()
		$sReader = New-Object System.IO.StreamReader($stream)

		# Define a MemoryStream and a StreamWriter for writing into the destination file
		$memStream = New-Object System.IO.MemoryStream
		$writeStream = New-Object System.IO.StreamWriter $memStream

		# process the source blob
		$exString = "java.lang.Exception:"
		while(-Not $sReader.EndOfStream){
		    $line = $sReader.ReadLine()
		    $split = $line.Split(" ")

		    # remove the "java.lang.Exception" from the first element of the array
		    # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
		    if ($split[0] -eq $exString){
		        #create a new ArrayList to remove $split[0]
		        $newArray = [System.Collections.ArrayList] $split
		        $newArray.Remove($exString)

		        # update $split and $line
		        $split = $newArray
		        $line = $newArray -join(" ")
		    }

		    # remove the lines that has less than 7 elements
		    if ($split.count -ge 7){
		        write-host $line
		        $writeStream.WriteLine($line)
		    }
		}

		# Write to the destination blob
		$writeStream.Flush()
		$memStream.Seek(0, "Begin")
		$destBlob.UploadFromStream($memStream)

5. **[スクリプトの実行]** をクリックするか、**F5** キーを押して、スクリプトを実行します。
6. 変更されたデータ ファイルを確認する場合は、プレビュー ポータル、Azure Storage エクスプローラー ツール、または Azure PowerShell を使用できます。「[Azure HDInsight の概要][hdinsight-get-started]」に、Azure PowerShell を使用してファイルをダウンロードし、ファイルの内容を表示するコード例が示されています。


##PowerShell を使用して Sqoop エクスポートを実行する

このセクションでは、Azure PowerShell を使用して Sqoop エクスポート コマンドを実行し、Hive テーブルとデータ ファイルを Azure SQL データベースまたは SQL Server にエクスポートします。次のセクションでは、HDInsight の .NET サンプルを示します。

> [AZURE.NOTE]接続文字列情報を除き、このセクションの手順は、Azure SQL データベースまたは SQL Server で動作します。これらの手順は次の構成を使用してテスト済みです。
>
> * **Azure Virtual Network ポイント対サイト構成**: HDInsight クラスターをプライベート データセンター内の SQL Server に接続する仮想ネットワーク。詳細については、「[管理ポータルでのポイント対サイト VPN の構成](../vpn-gateway/vpn-gateway-point-to-site-create.md)」を参照してください。
> * **Azure HDInsight 3.1**: 仮想ネットワークでのクラスターの作成については、「[カスタム オプションを使用した HDInsight での Hadoop クラスターのプロビジョニング](hdinsight-provision-clusters.md)」を参照してください。
> * **SQL Server 2014**: 仮想ネットワークに安全に接続するために、認証と、VPN クライアント構成パッケージの実行を許可する構成。

**log4j ログ ファイルをエクスポートするには**

1. Windows PowerShell ISE を開きます。
2. 下のウィンドウで、次のコマンドを実行して、Azure サブスクリプションに接続します。

		Add-AzureAccount

	Azure アカウント資格情報の入力を求められます。

3. 次のスクリプトをスクリプト ウィンドウにコピーし、最初の 7 個の変数を設定します。

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"

		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$databaseName = "<SQLDatabaseName>"

		$tableName_log4j = "log4jlogs"

		# Connection string for Azure SQL Database.
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server.
		# Uncomment if using SQL Server.
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"

		$exportDir_log4j = "/tutorials/usesqoop/data"

	変数の詳細については、このチュートリアルの「[前提条件](#prerequisites)」セクションを参照してください。

	$exportDir\_log4j に sample.log ファイルの名前が指定されていないことに注意してください。Sqoop は、そのフォルダーの下にあるすべてのファイルからデータをエクスポートします。

4. スクリプト ウィンドウの末尾に、次のスクリプトを追加します。

		# Submit a Sqoop job
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

	フィールドの区切り記号は **\\0x20** (スペース) です。区切り記号は、sample.log ファイルの Azure PowerShell スクリプトで定義されています。**-m 1** については、[Sqoop ユーザー ガイド][sqoop-user-guide-1.4.4]を参照してください。

5. **[スクリプトの実行]** をクリックするか、**F5** キーを押して、スクリプトを実行します。
6. [プレビュー ポータル][azure-management-portal]を使用して、エクスポートされたデータを確認します。

**hivesampletable Hive テーブルをエクスポートするには**

1. Windows PowerShell ISE を開きます。
2. 下のウィンドウで、次のコマンドを実行して、Azure サブスクリプションに接続します。

		Add-AzureAccount

	Azure アカウント資格情報の入力を求められます。

3. 次のスクリプトをスクリプト ウィンドウにコピーし、最初の 7 個の変数を設定します。

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"

		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_mobile = "mobiledata"

		# Connection string for Azure SQL Database.
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server.
		# Uncomment if using SQL Server
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"

		$exportDir_mobile = "/hive/warehouse/hivesampletable"

	変数の詳細については、このチュートリアルの「[前提条件](#prerequisites)」セクションを参照してください。

4. スクリプト ウィンドウの末尾に、次のスクリプトを追加します。

		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"


		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. **[スクリプトの実行]** をクリックするか、**F5** キーを押して、スクリプトを実行します。
6. [プレビュー ポータル][azure-management-portal]を使用して、エクスポートされたデータを確認します。



##HDInsight .NET SDK を使用して Sqoop エクスポートを実行する

以下は、HDInsight .NET SDK を使用して Sqoop エクスポートを実行する C# サンプルです。HDInsight .NET SDK の使用に関する一般的な情報については、「[プログラムによる Hadoop ジョブの送信][hdinsight-submit-jobs]」を参照してください。


	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.IO;
	using System.Threading;
	using System.Security.Cryptography.X509Certificates;
	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.Hadoop.Client;

	namespace sqoopSDKSample
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            // Set the variables
	            string subscriptionID = "<AzureSubscriptionID>";
	            string clusterName = "<HDInsightClusterName>";
	            string certFriendlyName = "<AzureCertificateFriendlyName>";
	            string sqlDatabaseServerName = "<SQLDatabaseServerName>";
	            string sqlDatabaseLogin = "<SQLDatabaseLogin>";
	            string sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
	            string sqlDatabaseDatabaseName = "hdisqoop";
	            string sqlDatabaseTableName = "log4jlogs";

	            cmdExport = @"export";
				// Connection string for using Azure SQL Database.
				// Comment if using SQL Server
	            cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
				// Connection string for using SQL Server.
				// Uncomment if using SQL Server
				//cmdExport = cmdExport + @" --connect jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
	            cmdExport = cmdExport + @" --table " + sqlDatabaseTableName;
	            cmdExport = cmdExport + @" --export-dir /tutorials/usesqoop/data";
	            cmdExport = cmdExport + @" --input-fields-terminated-by \0x20 -m 1";

	            SqoopJobCreateParameters sqoopJobDefinition = new SqoopJobCreateParameters()
	            {
	                Command = cmdExport,
	                StatusFolder = "/tutorials/usesqoop/jobStatus"
	            };

	            // Get the certificate object from certificate store using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
	            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

	            // Submit the Hive job
	            var jobClient = JobSubmissionClientFactory.Connect(creds);
	            JobCreationResults jobResults = jobClient.CreateSqoopJob(sqoopJobDefinition);

	            // Wait for the job to complete
	            WaitForJobCompletion(jobResults, jobClient);

	            // Print the Hive job output
	            System.IO.Stream stream = jobClient.GetJobErrorLogs(jobResults.JobId);

	            StreamReader reader = new StreamReader(stream);
	            Console.WriteLine(reader.ReadToEnd());

	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadLine();
	        }

	        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
	        {
	            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
	            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
	            {
	                jobInProgress = client.GetJob(jobInProgress.JobId);
	                Thread.Sleep(TimeSpan.FromSeconds(10));
	            }
	        }
	    }
	}

スクリプト ファイルを実行するには、

	Command = cmdExport,

 を以下に置き換えます。

	File = "/tutorials/usesqoop/sqoopexport.txt",

スクリプト ファイルは、Azure Blob ストレージにある必要があります。




##Azure PowerShell を使用して Sqoop インポートを実行する

このセクションでは、log4j ログ (Azure SQL データベースにエクスポートしたもの) を HDInsight にインポートして戻します。

1. Windows PowerShell ISE を開きます。
2. 下のウィンドウで、次のコマンドを実行して、Azure サブスクリプションに接続します。

		Add-AzureAccount

	Azure アカウント資格情報の入力を求められます。

3. 次のスクリプトをスクリプト ウィンドウにコピーし、最初の 7 個の変数を設定します。

		# Define the cluster variables
		$clusterName = "<HDInsightClusterName>"
		$storageAccountName = "<AzureStorageAccount>"
		$containerName = "<BlobStorageContainerName>"

		# Define the SQL database variables
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseLogin = "<SQLDatabaseUsername>"
		$sqlDatabasePassword = "SQLDatabasePassword>"
		$databaseName = "SQLDatabaseName"

		$tableName_log4j = "log4jlogs"

		# Connection string for Azure SQL Database
		# Comment if using SQL Server
		$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$databaseName"
		# Connection string for SQL Server
		# Uncomment if using SQL Server
		#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$databaseName"

		$tableName_mobile = "mobiledata"
		$targetDir_mobile = "/tutorials/usesqoop/importeddata/"

	変数の詳細については、このチュートリアルの「[前提条件](#prerequisites)」セクションを参照してください。

4. スクリプト ウィンドウの末尾に、次のスクリプトを追加します。

		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"

		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5. **[スクリプトの実行]** をクリックするか、**F5** キーを押して、スクリプトを実行します。
6. 変更されたデータ ファイルを確認する場合は、プレビュー ポータル、Azure Storage エクスプローラー ツール、または Azure PowerShell を使用できます。「[HDInsight の概要][hdinsight-get-started]」に、Azure PowerShell を使用してファイルをダウンロードし、ファイルの内容を表示するコード例が示されています。

##次のステップ

ここでは Sqoop の使用方法を説明しました。詳細については、次を参照してください。

- [HDInsight での Oozie の使用][hdinsight-use-oozie]\: Oozie ワークフローで Sqoop アクションを使用します。
- [HDInsight を使用したフライト遅延データの分析][hdinsight-analyze-flight-data]\: Hive を使用してフライト遅延データを分析し、Sqoop を使用して Azure SQL データベースにデータをエクスポートします。
- [HDInsight へのデータのアップロード][hdinsight-upload-data]\: HDInsight/Azure BLOB ストレージにデータをアップロードするその他の方法を説明します。




[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!----HONumber=August15_HO8-->