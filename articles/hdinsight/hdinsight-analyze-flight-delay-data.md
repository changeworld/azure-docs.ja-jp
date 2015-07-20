<properties 
	pageTitle="HDInsight の Hadoop を使用したフライトの遅延データの分析 | Microsoft Azure" 
	description="1 つの Windows PowerShell スクリプトを使用して HDInsight クラスターのプロビジョニング、Hive ジョブの実行、Sqool ジョブの実行、クラスターの削除を行う方法について説明します。" 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="jgao"/>

#HDInsight での Hive を使用したフライト遅延データの分析

Hive では、*[HiveQL][hadoop-hiveql]* と呼ばれる SQL に似たスクリプト言語を使用して Hadoop MapReduce ジョブを実行します。大規模なデータの集約、照会、分析に Hive を利用できます。

Azure HDInsight の大きな利点の 1 つに、データ ストレージとコンピューティングの分離があります。HDInsight はデータ ストレージとして Azure BLOB ストレージを使用します。一般に、MapReduce 処理は、次の 3 つのパートに分割できます。

1. **Azure BLOB ストレージにデータを保存する。** これは連続的なプロセスとなる場合があります。たとえば、気象データ、センサー データ、Web ログを Azure BLOB ストレージに保存できます。ここではフライトの遅延データが保存対象となります。
2. **ジョブを実行する。** データを処理する段階になったら、Windows PowerShell スクリプト (またはクライアント アプリケーション) を実行して HDInsight クラスターをプロビジョニングし、ジョブを実行して、クラスターを削除します。このジョブによって、出力データが Azure BLOB ストレージに保存されます。出力データは、クラスターの削除後も維持されます。こうして、実際に消費した分だけが課金されることとなります。 
3. **Azure BLOB ストレージから出力結果を取り出す。**ここでは、Azure SQL データベースにデータをエクスポートする過程がこれに相当します。

このチュートリアルのシナリオと構成を示したのが次の図です。

![HDI.FlightDelays.flow][img-hdi-flightdelays-flow]

**注:** 図中の番号は、セクション タイトルに対応します。

このチュートリアルでは、主に 1 つの Windows PowerShell スクリプトを使用して次のことを実行する方法を示します。

- HDInsight クラスターをプロビジョニングする。
- クラスターで Hive ジョブを実行し、空港での平均遅延時間を計算する。フライトの遅延データは Azure BLOB ストレージ アカウントに保存されます。 
- Sqoop ジョブを実行し、Hive ジョブの出力結果を Azure SQL Database にエクスポートする。
- HDInsight クラスターを削除する。 

フライト遅延データのアップロード手順、Hive クエリ文字列の作成とアップロード手順、および Sqoop ジョブのための Azure SQL データベースの準備手順については、付録を参照してください。


###前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション**。[Azure 無料試用版の取得](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

- **Azure PowerShell を実行できるワークステーション**。[Azure PowerShell のインストールおよび使用](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)に関するページを参照してください。

**HDInsight ストレージについて**

HDInsight の Hadoop クラスターでは、データ ストレージとして Azure BLOB ストレージが使用されます。詳細については、[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]に関するページを参照してください。

HDInsight クラスターをプロビジョニングするときに、Hadoop 分散ファイル システム (HDFS) と同じように、Azure ストレージ アカウントの BLOB ストレージ コンテナーを、既定のファイル システムとして指定します。このストレージ アカウントは "*既定のストレージ アカウント*" と呼ばれ、BLOB コンテナーは、"*既定の BLOB コンテナー*" または "*既定のコンテナー*" と呼ばれています。既定のストレージ アカウントは、HDInsight クラスターと同じデータ センター内に存在する必要があります。HDInsight クラスターを削除しても、既定のコンテナーと既定のストレージ アカウントは削除されません。

プロビジョニング プロセスでは、既定のストレージ アカウントに加え、他の Azure ストレージ アカウントを HDInsight クラスターにバインドすることができます。バインドとは、ストレージ アカウントとストレージ アカウント キーを構成ファイルに追加することです。これにより、実行時にクラスターからこれらのストレージ アカウントにアクセスできるようになります。ストレージ アカウントを追加する方法については、[HDInsight での Hadoop クラスターのプロビジョニング][hdinsight-provision]に関するページを参照してください。

Azure BLOB ストレージの構文を次に示します。

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

>[AZURE.NOTE]BLOB ストレージのパスは仮想パスです。詳細については、[HDInsight での Azure BLOB ストレージの使用][hdinsight-storage]に関するページを参照してください。

既定のコンテナーに格納されているファイルは、次の URI のどれを使用しても HDInsight からアクセスできます (例として flightdelays.hql を使用しています)。

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
	wasb:///tutorials/flightdelays/flightdelays.hql
	/tutorials/flightdelays/flightdelays.hql

ストレージ アカウントから直接ファイルにアクセスする場合、ファイルの BLOB 名は次のようになります。

	tutorials/flightdelays/flightdelays.hql

BLOB 名の前に "/" がないことに注意してください。

**このチュートリアルで使用するファイル**

このチュートリアルでは、[米国運輸省研究・革新技術庁/運輸統計局][rita-website]が公開している航空便の定時運航データをダウンロードして使用します。データは、パブリック BLOB アクセス権限の設定された Azure BLOB ストレージ コンテナーにアップロード済みです。パブリック BLOB コンテナーであるため、Hive スクリプトを実行する HDInsight クラスターにこのストレージ アカウントをバインドする必要はありません。HiveQL スクリプトのアップロード先も同じ BLOB コンテナーです。独自のストレージ アカウントにデータを取得/アップロードする方法や、HiveQL スクリプト ファイルを作成/アップロードする方法については、[付録 A](#appendix-a) および[付録 B](#appendix-b) を参照してください。

このチュートリアルで使用するファイルを次の表に示します。

<table border="1">
<tr><th>ファイル</th><th>説明</th></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>実行する Hive ジョブで使用する HiveQL スクリプト ファイル。このスクリプトは、パブリック アクセス権限の設定された Azure BLOB ストレージ アカウントにアップロード済みです。このファイルの準備と Azure BLOB ストレージ アカウントへのアップロードの手順については、<a href="#appendix-b">付録 B</a> を参照してください。</td></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Hive ジョブの入力データ。このデータは、パブリック アクセス権限の設定された Azure BLOB ストレージ アカウントにアップロード済みです。このデータの取得と Azure BLOB ストレージ アカウントへのアップロードの手順については、<a href="#appendix-a">付録 A</a> を参照してください。</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>Hive ジョブの出力パス。出力データの保存には、既定のコンテナーを使用します。</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>既定のコンテナーにある Hive ジョブのステータス フォルダー。</td></tr>
</table>



**Hive の内部テーブルと外部テーブルについて**

Hive の内部テーブルと外部テーブルについて知っておく必要のある事項がいくつかあります。

- **CREATE TABLE** コマンドは内部テーブルを作成します。データ ファイルは既定のコンテナーに配置する必要があります。
- **CREATE TABLE** コマンドはデータ ファイルを /hive/warehouse/<TableName> フォルダーに移動します。
- **CREATE EXTERNAL TABLE** コマンドは外部テーブルを作成します。データ ファイルは既定のコンテナーの外部に配置することもできます。
- **CREATE EXTERNAL TABLE** コマンドはデータ ファイルを移動しません。
- **CREATE EXTERNAL TABLE** コマンドでは、LOCATION 内のフォルダーは許容されません。チュートリアルで sample.log ファイルのコピーを作成しているのは、これが理由です。

詳細については、「[HDInsight: Hive Internal and External Tables Intro (HDInsight: Hive の内部テーブルと外部テーブルの概要)][cindygross-hive-tables]」を参照してください。

> [AZURE.NOTE]HiveQL のステートメントの 1 つが、Hive の外部テーブルを作成します。Hive の外部テーブルは、元の場所にデータ ファイルを保持します。Hive の内部テーブルは、hive\\warehouse にデータ ファイルを移動します。Hive の内部テーブルは、データ ファイルを既定のコンテナーに配置する必要があります。既定の BLOB コンテナー外にデータを保存する場合は、Hive 外部テーブルを使用する必要があります。









##HDInsight クラスターのプロビジョニングおよび Hive/Sqoop ジョブの実行 

Hadoop MapReduce はバッチ処理です。Hive ジョブの実行方法としては、ジョブに使用するクラスターをプロビジョニングし、完了後、ジョブを削除するのが最も経済的です。以下のスクリプトは、その全過程をカバーしています。HDInsight クラスターのプロビジョニングと Hive ジョブの実行の詳細については、「[HDInsight での Hadoop クラスターのプロビジョニング][hdinsight-provision]」と「[HDInsight での Hive の使用][hdinsight-use-hive]」を参照してください。

**Windows PowerShell を使用して Hive クエリを実行するには**

1. [付録 C](#appendix-c) の手順に従い、Azure SQL データベースと Sqoop ジョブ出力用のテーブルを作成します。
2. 次のパラメーターを準備します。

	<table border="1">
<tr><th>変数名</th><th>メモ</th></tr>
<tr><td>$hdinsightClusterName</td><td>HDInsight クラスター名。クラスターが存在しない場合、入力した名前のクラスターがスクリプトにより作成されます。</td></tr>
<tr><td>$storageAccountName</td><td>既定のストレージ アカウントとして使用する Azure ストレージ アカウント。この値が必要なのは、スクリプトで HDInsight クラスターを作成する必要がある場合のみです。$hdinsightClusterName に対して既存の HDInsight クラスター名を指定している場合は、空白のままにします。入力した値のストレージ アカウントが存在しない場合、入力した名前のストレージ アカウントがスクリプトにより作成されます。</td></tr>
<tr><td>$blobContainerName</td><td>既定のファイル システムとして使用される BLOB コンテナー。空白のままにすると、$hdinsightClusterName の値が使用されます。</td></tr>
<tr><td>$sqlDatabaseServerName</td><td>Azure SQL Database のサーバー名。既存のサーバー名にする必要があります。作成方法については、<a href="#appendix-c">付録 C</a> を参照してください。</td></tr>
<tr><td>$sqlDatabaseUsername</td><td>Azure SQL Database サーバーのログイン名。</td></tr>
<tr><td>$sqlDatabasePassword</td><td>Azure SQL Database サーバーのログイン パスワード。</td></tr>
<tr><td>$sqlDatabaseName</td><td>Sqoop によるデータのエクスポート先となる SQL データベース。既定のファイル名は HDISqoop です。Sqoop ジョブ出力用のテーブル名は AvgDelays です。</td></tr>
</table>
3. Windows PowerShell Integrated Scripting Environment (ISE) を開きます。
4. 次のスクリプトをスクリプト ウィンドウにコピーして貼り付けます。

		[CmdletBinding()]
		Param(
		
		    # HDInsight cluster variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the HDInsight cluster name. If the cluster doesn't exist, the script will create one.")]
		    [String]$hdinsightClusterName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [AllowEmptyString()]
		    [String]$storageAccountName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [AllowEmptyString()]
		    [String]$blobContainerName,
		
		    #SQL database server variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database Server Name where to export data.")]
		    [String]$sqlDatabaseServerName,  # specify the Azure SQL database server name where you want to export data to.
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database login username.")]
		    [String]$sqlDatabaseUsername,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database login user password.")]
		    [String]$sqlDatabasePassword,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the database name where data will be exported to.")]
		    [String]$sqlDatabaseName  # the default value is HDISqoop
		)
		
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		#region - HDInsight cluster variables
		[int]$clusterSize = 1                # One data node is sufficient for this tutorial
		[String]$location = "Central US"     # For better performance, choose a datacenter near you
		[String]$hadoopUserLogin = "admin"   # Use "admin" as the Hadoop login name
		[String]$hadoopUserpw = "Pass@word1" # Use "Pass@word1" as the Hadoop login password
		
		[Bool]$isNewCluster = $false      # Indicates whether a new HDInsight cluster is created by the script  
		                                  # If this variable is true, then the script can optionally delete the cluster after running the Hive and Sqoop jobs
		
		[Bool]$isNewStorageAccount = $false
		
		$storageAccountName = $storageAccountName.ToLower() # Storage account names must be between 3 and 24 characters in length and use numbers and lower-case letters only.
		#endregion
		
		#region - Hive job variables
		[String]$hqlScriptFile = "wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql" # The HiveQL script is located in a public Blob container. Update this URI if you want to use your own script file.
		
		[String]$jobStatusFolder = "/tutorials/flightdelays/jobstatus" # The script saves both the output data and the job status file to the default container.
		                                                               # The output data path is set in the HiveQL file.
		
		#[String]$jobOutputBlobName = "tutorials/flightdelays/output/000000_0" # This is the output file of the Hive job. The path is set in the HiveQL script.
		#endregion
		
		#region - Sqoop job variables
		[String]$sqlDatabaseTableName = "AvgDelays" 
		[String]$sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
		#endregion Constants and variables
		
		#region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		if (-not (Get-AzureAccount)){ Add-AzureAccount}
		#endregion
		
		#region - Validate user input, and provision HDInsight cluster if needed
		Write-Host "`nValidating user input ..." -ForegroundColor Green
		
		# Both the Azure SQL database server and database must exist
		if (-not (Get-AzureSqlDatabaseServer|Where-Object{$_.ServerName -eq $sqlDatabaseServerName})){
		    Write-host "The Azure SQL database server, $sqlDatabaseServerName doesn't exist." -ForegroundColor Red
		    Exit
		}
		else
		{
		    if (-not ((Get-AzureSqlDatabase -ServerName $sqlDatabaseServerName)|Where-Object{$_.Name -eq $sqlDatabaseName})){
		        Write-host "The Azure SQL database, $sqlDatabaseName doesn't exist." -ForegroundColor Red
		        Exit
		    }
		}
		
		if (Test-AzureName -Service -Name $hdinsightClusterName)     # If it is an existing HDInsight cluster ...
		{
		    Write-Host "`tThe HDInsight cluster, $hdinsightClusterName, exists. This cluster will be used to run the Hive job." -ForegroundColor Cyan
		
		    #region - Retrieve the default Storage account/container names if the cluster exists
		    # The Hive job output will be stored in the default container. The 
		    # information is used to download a copy of the output file from 
		    # Blob storage to workstation for the validation purpose.
		    Write-Host "`nRetrieving the HDInsight cluster default storage account information ..." `
		                -ForegroundColor Green
		
		    $hdi = Get-AzureHDInsightCluster -Name $HDInsightClusterName
		
		    # Use the default Storage account and the default container even if the names are different from the user input
		    $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName `
		                            -replace ".blob.core.windows.net"
		    $blobContainerName = $hdi.DefaultStorageAccount.StorageContainerName
		
		    Write-Host "`tThe default storage account for the cluster is $storageAccountName." `
		                -ForegroundColor Cyan
		    Write-Host "`tThe default Blob container for the cluster is $blobContainerName." `
		                -ForegroundColor Cyan
		    #endregion
		}
		else     #If the cluster doesn't exist, a new one will be provisioned
		{
		    if ([string]::IsNullOrEmpty($storageAccountName))
		    {
		        Write-Host "You must provide a storage account name" -ForegroundColor Red
		        EXit           
		    }
		    else
		    {
		        # If the container name is not specified, use the cluster name as the container name
		        if ([string]::IsNullOrEmpty($blobContainerName))
		        {
		            $blobContainerName = $hdinsightClusterName
		        }
		        $blobContainerName = $blobContainerName.ToLower()
		
		        #region - Provision HDInsight cluster
		        # Create an Azure Storage account if it doesn't exist
		        if (-not (Get-AzureStorageAccount|Where-Object{$_.Label -eq $storageAccountName}))
		        {
		            Write-Host "`nCreating the Azure storage account, $storageAccountName ..." -ForegroundColor Green
		            if (-not (New-AzureStorageAccount -StorageAccountName $storageAccountName.ToLower() -Location $location)){
		                Write-Host "Error creating the storage account, $storageAccountName" -ForegroundColor Red
		                Exit
		            }
		            $isNewStorageAccount = $True
		        }
		
		        # Create a Blob container used as the default container
		        $storageAccountKey = get-azurestoragekey -StorageAccountName $storageAccountName | %{$_.Primary}
		        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
		        {
		            Write-Host "`nCreating the Azure Blob container, $blobContainerName ..." -ForegroundColor Green
		            if (-not (New-AzureStorageContainer -name $blobContainerName -Context $storageContext)){
		                Write-Host "Error creating the Blob container, $blobContainerName" -ForegroundColor Red
		                Exit
		            }
		        }
		
		        # Create a new HDInsight cluster
		        Write-Host "`nProvisioning the HDInsight cluster, $hdinsightClusterName ..." -ForegroundColor Green
		        Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		        $hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
		        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
		        if (-not $credential)
		        {
		            Write-Host "Error creating the PSCredential object" -ForegroundColor Red
		            Exit
		        }
		
		        if (-not (New-AzureHDInsightCluster -Name $hdinsightClusterName -Location $location -Credential $credential -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $blobContainerName -ClusterSizeInNodes $clusterSize)){
		            Write-Host "Error provisioning the cluster, $hdinsightClusterName." -ForegroundColor Red
		            Exit
		        }
		        Else
		        {
		            $isNewCluster = $True
		        }
		        #endregion
		    }
		}
		#endregion
		
		#region - Submit Hive job
		Write-Host "`nSubmitting the Hive job ..." -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		
		Use-AzureHDInsightCluster $HDInsightClusterName
		$response = Invoke-Hive –File $hqlScriptFile -StatusFolder $jobStatusFolder
		
		Write-Host "`nThe Hive job status" -ForegroundColor Cyan
		Write-Host "---------------------------------------------------------" -ForegroundColor Cyan
		write-Host $response
		Write-Host "---------------------------------------------------------" -ForegroundColor Cyan
		#endregion 
		
		#region - Run Sqoop job
		Write-Host "`nSubmitting the Sqoop job ..." -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		
		[String]$exportDir = "wasb://$blobContainerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/output"
		
		
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
		$sqoopJob = Start-AzureHDInsightJob -Cluster $hdinsightClusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $hdinsightClusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $hdinsightClusterName -JobId $sqoopJob.JobId -StandardOutput
		#endregion
		
		#region - Delete the HDInsight cluster
		if ($isNewCluster -eq $True)
		{
		    $isDelete = Read-Host 'Do you want to delete the HDInsight Hadoop cluster ' $hdinsightClusterName '? (Y/N)'
		
		    if ($isDelete.ToLower() -eq "y")
		    {
		        Write-Host "`nDeleting the HDInsight cluster ..." -ForegroundColor Green
		        Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		        Remove-AzureHDInsightCluster -Name $hdinsightClusterName
		    }
		}
		#endregion
		
		#region - Delete the Storage account
		if ($isNewStorageAccount -eq $True)
		{
		    $isDelete = Read-Host 'Do you want to delete the Azure storage account ' $storageAccountName '? (Y/N)'
		
		    if ($isDelete.ToLower() -eq "y")
		    {
		        Write-Host "`nDeleting the Azure storage account ..." -ForegroundColor Green
		        Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		        Remove-AzureStorageAccount -StorageAccountName $storageAccountName
		    }
		}
		#endregion
		
		Write-Host "End of the PowerShell script" -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow

5. **F5** キーを押して、スクリプトを実行します。次のように出力されます。

	![HDI.FlightDelays.RunHiveJob.output][img-hdi-flightdelays-run-hive-job-output]
		
6. SQL データベースに接続し、AvgDelays テーブルに保存されている都市別平均遅延を表示します。

	![HDI.FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]



---
##<a id="appendix-a"></a>付録 A: フライト遅延データを Azure BLOB ストレージにアップロードする
データ ファイルと HiveQL スクリプト ファイルをアップロード ([付録 B](#appendix-b) 参照) する前に、いくつか計画を立てる必要があります。データ ファイルと HiveQL ファイルを保存してから HDInsight クラスターをプロビジョニングし、Hive ジョブを実行するという考え方です。2 つのオプションがあります。

- **HDInsight クラスターと既定のファイル システムで、使用する Azure ストレージ アカウントを同じものにする。** HDInsight クラスターにはストレージ アカウントのアクセス キーがあるため、追加の変更は不要です。
- **HDInsight クラスターと既定のファイル システムで、使用する Azure ストレージ アカウントを別のものにする。** この場合は、ストレージ アカウントを追加のストレージ アカウントとして含める必要があるため、「[HDInsight クラスターのプロビジョニングおよび Hive/Sqoop ジョブの実行](#runjob)」に記載されている Windows PowerShell スクリプトのプロビジョニングに関する部分を変更する必要があります。手順については、「[HDInsight での Hadoop クラスターのプロビジョニング][hdinsight-provision]」を参照してください。ストレージ アカウントのアクセス キーが HDInsight クラスターで認識されるようになります。

>[AZURE.NOTE]データ ファイルの BLOB ストレージ パスは、HiveQL スクリプト ファイルでハードコーディングされます。このパスは適宜更新する必要があります。

**フライト データをダウンロードするには**

1. [米国運輸省研究・革新技術庁/運輸統計局][rita-website]のページに移動します。
2. このページで、次の値を選択します。

	<table border="1">
<tr><th>名前</th><th>値</th></tr>
<tr><td>Filter Year</td><td>2013 </td></tr>
<tr><td>Filter Period</td><td>January</td></tr>
<tr><td>フィールド</td><td>*Year*、*FlightDate*、*UniqueCarrier*、*Carrier*、*FlightNum*、*OriginAirportID*、*Origin*、*OriginCityName*、*OriginState*、*DestAirportID*、*Dest*、*DestCityName*、*DestState*、*DepDelayMinutes*、*ArrDelay*、*ArrDelayMinutes*、*CarrierDelay*、*WeatherDelay*、*NASDelay*、*SecurityDelay*、*LateAircraftDelay* (その他のフィールドはすべてオフにする)</td></tr>
</table>

3. **[Download]** をクリックします。
4. ファイルを **C:\\Tutorials\\FlightDelays\\Data** フォルダーに解凍します。ファイルはそれぞれ CSV ファイルで、サイズは約 60 GB です。
5.	ファイルの名前を、データを含む月の名前に変更します。たとえば、1 月のデータを含むファイルの場合、*January.csv* という名前にします。
6. 手順 2. ～ 5. を繰り返して、2013 年の 12 か月分のファイルをダウンロードします。チュートリアルを実行するには、月別のファイルが少なくとも 1 つ必要です。  

**フライト遅延データを Azure BLOB ストレージにアップロードするには**

1. 次のパラメーターを準備します。

	<table border="1">
<tr><th>変数名</th><th>メモ</th></tr>
<tr><td>$storageAccountName</td><td>データのアップロード先となる Azure ストレージ アカウント。</td></tr>
<tr><td>$blobContainerName</td><td>データのアップロード先となる BLOB コンテナー。</td></tr>
</table>
2. Azure PowerShell ISE を開きます。
3. 次のスクリプトをスクリプト ウィンドウに貼り付けます。

		[CmdletBinding()]
		Param(
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [String]$storageAccountName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [String]$blobContainerName
		)
		
		#Region - Variables
		$localFolder = "C:\Tutorials\FlightDelays\Data"  # The source folder
		$destFolder = "tutorials/flightdelays/data"     #The blob name prefix for the files to be uploaded
		#EndRegion
		
		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		if (-not (Get-AzureAccount)){ Add-AzureAccount}
		#EndRegion
		
		#Region - Validate user input
		# Validate the Storage account
		if (-not (Get-AzureStorageAccount|Where-Object{$_.Label -eq $storageAccountName}))
		{
		    Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
		    exit
		}
		
		# Validate the container
		$storageAccountKey = get-azurestoragekey -StorageAccountName $storageAccountName | %{$_.Primary}
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
		{
		    Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
		    Exit
		}
		#EngRegion
		
		#Region - Copy the file from local workstation to Azure Blob storage  
		if (test-path -Path $localFolder)
		{
		    foreach ($item in Get-ChildItem -Path $localFolder){
		        $fileName = "$localFolder\$item"
		        $blobName = "$destFolder/$item"
		
		        Write-Host "Copying $fileName to $blobName" -ForegroundColor Green
		
		        Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
		    }
		}
		else
		{
		    Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
		}
		
		# List the uploaded files on HDInsight
		Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
		#EndRegion

4. **F5** キーを押して、スクリプトを実行します。

ファイルのアップロード方法として別の方法を選択した場合も、ファイル パスは tutorials/flightdelays/data にしてください。ファイルにアクセスする構文は次のとおりです。

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

パス tutorials/flightdelays/data は、ファイルのアップロード時に作成した仮想フォルダーです。月ごとに 1 つ、合計 12 個のファイルがあることを確認します。

>[AZURE.NOTE]新しい場所からファイルを読み取るには、Hive クエリを更新する必要があります。

> コンテナーのアクセス権限をパブリックに設定するか、またはストレージ アカウントを HDInsight クラスターにバインドする必要があります。そうしないと、Hive クエリからデータ ファイルにアクセスできません。

---
##<a id="appendix-b"></a>付録 B: HiveQL スクリプトを作成してアップロードする

Azure PowerShell を使用して、複数の HiveQL ステートメントを一度に実行することも、HiveQL ステートメントをスクリプト ファイルにまとめることもできます。このセクションでは、HiveQL スクリプトを作成し、Azure PowerShell を使用して Azure BLOB ストレージにアップロードする方法を説明します。Hive を利用するには、HiveQL スクリプトが Azure BLOB ストレージに格納されている必要があります。

HiveQL スクリプトは、次の作業を実行します。

1. **delays_raw テーブルを削除します** (テーブルが既に存在する場合)。
2. **delays_raw 外部 Hive テーブルを作成します**。このテーブルはフライト遅延ファイルのある BLOB ストレージの場所を指しています。このクエリでは、フィールドがコンマ (,) 区切りで、行末が "\\n" であることを指定しています。この場合、フィールド値にコンマが "含まれている" と問題が発生します。Hive は、フィールド区切りのコンマとフィールド値の一部であるコンマを識別できません (ORIGIN_CITY_NAME フィールドや DEST_CITY_NAME フィールドの値など)。これに対処するため、クエリでは、間違って複数の列に分割されるデータを格納する TEMP 列を作成します。  
3. **delays テーブルを削除します** (テーブルが既に存在する場合)。
4. **delays テーブルを作成します**。次へ進む前にデータをクリーンアップしておくと面倒がありません。次のクエリは、delays_raw テーブルを基にして、新しい *delays* テーブルを作成します。(前述のように) TEMP 列はコピーしません。**substring** 関数を使用してデータから引用符を削除します。 
5. **悪天候による平均遅延を計算し、その結果を都市名ごとにグループ化します。** さらに、結果を BLOB ストレージに出力します。このクエリは、データからアポストロフィを削除し、**weather_delay** の値が null の行を除外します。このチュートリアルで後ほど使用する Sqoop ではこれらの値が既定では適切に処理されないため、この処理が必要です。

HiveQL コマンドの完全な一覧については、「[Hive Data Definition Language (Hive データ定義言語)][hadoop-hiveql]」を参照してください。各 HiveQL コマンドは、セミコロンで終了します。

**HiveQL スクリプト ファイルを作成するには**

1. 次のパラメーターを準備します。

	<table border="1">
<tr><th>変数名</th><th>メモ</th></tr>
<tr><td>$storageAccountName</td><td>HiveQL スクリプトのアップロード先となる Azure ストレージ アカウント。</td></tr>
<tr><td>$blobContainerName</td><td>HiveQL スクリプトのアップロード先となる BLOB コンテナー。</td></tr>
</table>
2. Azure PowerShell ISE を開きます。

3. 次のスクリプトをスクリプト ウィンドウにコピーして貼り付けます。

		[CmdletBinding()]
		Param(
		
		    # Azure Blob storage variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [String]$storageAccountName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [String]$blobContainerName
		
		)
		
		#region - Define variables
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		# The HiveQL script file is exported as this file before it's uploaded to Blob storage
		$hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql" 
		
		# The HiveQL script file will be uploaded to Blob storage as this blob name
		$hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 
		
		# These two constants are used by the HiveQL script file
		#$srcDataFolder = "tutorials/flightdelays/data" 
		$dstDataFolder = "/tutorials/flightdelays/output"
		#endregion
		
		#region - Validate the file and file path
		
		# Check if a file with the same file name already exists on the workstation
		Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
		if (test-path $hqlLocalFileName){
		
		    $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'
		
		    if ($isDelete.ToLower() -ne "y")
		    {
		        Exit
		    }
		}
		
		# Create the folder if it doesn't exist
		$folder = split-path $hqlLocalFileName
		if (-not (test-path $folder))
		{
		    Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green
		
		    new-item $folder -ItemType directory  
		}
		#end region
		
		#region - Add the Azure account 
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		$azureAccounts= Get-AzureAccount
		if (! $azureAccounts)
		{
		    Add-AzureAccount
		}
		#endregion
		
		#region - Write the Hive script into a local file
		Write-Host "`nWriting the Hive script into a file on your workstation ..." `
		            -ForegroundColor Green
		
		$hqlDropDelaysRaw = "DROP TABLE delays_raw;"
		
		$hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
		        "YEAR string, " +
		        "FL_DATE string, " +
		        "UNIQUE_CARRIER string, " +
		        "CARRIER string, " +
		        "FL_NUM string, " +
		        "ORIGIN_AIRPORT_ID string, " +
		        "ORIGIN string, " +
		        "ORIGIN_CITY_NAME string, " +
		        "ORIGIN_CITY_NAME_TEMP string, " +
		        "ORIGIN_STATE_ABR string, " +
		        "DEST_AIRPORT_ID string, " +
		        "DEST string, " +
		        "DEST_CITY_NAME string, " +
		        "DEST_CITY_NAME_TEMP string, " +
		        "DEST_STATE_ABR string, " +
		        "DEP_DELAY_NEW float, " +
		        "ARR_DELAY_NEW float, " +
		        "CARRIER_DELAY float, " +
		        "WEATHER_DELAY float, " +
		        "NAS_DELAY float, " +
		        "SECURITY_DELAY float, " +
		        "LATE_AIRCRAFT_DELAY float) " +
		    "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
		    "LINES TERMINATED BY '\n' " +
		    "STORED AS TEXTFILE " +
		    "LOCATION 'wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';" 
		
		$hqlDropDelays = "DROP TABLE delays;"
		
		$hqlCreateDelays = "CREATE TABLE delays AS " +
		    "SELECT YEAR AS year, " +
		        "FL_DATE AS flight_date, " +
		        "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
		        "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
		        "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
		        "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
		        "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
		        "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
		        "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
		        "DEST_AIRPORT_ID AS dest_airport_id, " +
		        "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
		        "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
		        "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
		        "DEP_DELAY_NEW AS dep_delay_new, " +
		        "ARR_DELAY_NEW AS arr_delay_new, " +
		        "CARRIER_DELAY AS carrier_delay, " +
		        "WEATHER_DELAY AS weather_delay, " +
		        "NAS_DELAY AS nas_delay, " +
		        "SECURITY_DELAY AS security_delay, " +
		        "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
		    "FROM delays_raw;" 
		
		$hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
		    "SELECT regexp_replace(origin_city_name, '''', ''), " +
		        "avg(weather_delay) " +
		    "FROM delays " +
		    "WHERE weather_delay IS NOT NULL " +
		    "GROUP BY origin_city_name;"
		
		$hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal
		
		$hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force 
		#endregion
		
		#region - Upload the Hive script to the default Blob container
		Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green
		
		# Create a storage context object
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		# Upload the file from local workstation to Blob storage
		Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext 
		#endregion
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	スクリプトには次の変数が使用されています。

	- **$hqlLocalFileName** - HiveQL スクリプト ファイルは、BLOB ストレージにアップロードされる前に、いったんローカルに保存されます。その際に、このファイル名が使用されます。既定値は <u>C:\\tutorials\\flightdelays\\flightdelays.hql</u> です。
	- **$hqlBlobName** - Azure BLOB ストレージで使用される HiveQL スクリプト ファイルの BLOB 名。既定値は tutorials/flightdelays/flightdelays.hql です。ファイルは直接 Azure BLOB ストレージに書き込まれるため、BLOB 名の先頭に "/" はありません。BLOB ストレージからファイルにアクセスする場合は、ファイル名の先頭に "/" を追加する必要があります。
	- **$srcDataFolder** と **$dstDataFolder** はそれぞれ "tutorials/flightdelays/data" と "tutorials/flightdelays/output" です。


---
##<a id="appendix-c"></a>付録 C - Sqoop ジョブを出力するための Azure SQL Database の準備
**SQL データベースを準備するには (Sqoop スクリプトとマージ)**

1. 次のパラメーターを準備します。

	<table border="1">
<tr><th>変数名</th><th>メモ</th></tr>
<tr><td>$sqlDatabaseServerName</td><td>Azure SQL Database サーバーの名前。新しいサーバーを作成する場合は、何も入力しないでください。</td></tr>
<tr><td>$sqlDatabaseUsername</td><td>Azure SQL Database サーバーのログイン名。$sqlDatabaseServerName が既存のサーバーである場合、ログイン名とログイン パスワードを使用してサーバーを認証します。$sqlDatabaseServerName が既存のサーバーでない場合、ログイン名とログイン パスワードを使用して新しいサーバーを作成します。</td></tr>
<tr><td>$sqlDatabasePassword</td><td>Azure SQL Database サーバーのログイン パスワード。</td></tr>
<tr><td>$sqlDatabaseLocation</td><td>この値は、新しい Azure データベース サーバーを作成するときにのみ使用されます。</td></tr>
<tr><td>$sqlDatabaseName</td><td>Sqoop ジョブ用の AvgDelays テーブルの作成に使用する SQL データベース。空白のままにすると、"HDISqoop" というデータベースが作成されます。Sqoop ジョブ出力用のテーブル名は AvgDelays です。</td></tr>
</table>
2. Azure PowerShell ISE を開きます。 
3. 次のスクリプトをスクリプト ウィンドウにコピーして貼り付けます。
	
		[CmdletBinding()]
		Param(
		
		    # SQL database server variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database Server Name to use an existing one. Enter nothing to create a new one.")]
		    [AllowEmptyString()]
		    [String]$sqlDatabaseServer,  # Specify the Azure SQL database server name if you have one created. Otherwise use "".
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database admin user.")]
		    [String]$sqlDatabaseUsername,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database admin user password.")]
		    [String]$sqlDatabasePassword,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the region to create the Database in.")]
		    [AllowEmptyString()]
		    [String]$sqlDatabaseLocation,   #For example, West US.
		
		    # SQL database variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the database name if you have created one. Enter nothing to create one.")]
		    [AllowEmptyString()]
		    [String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
		)
		
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		#region - Constants and variables
		
		# IP address REST service used for retrieving external IP address and creating firewall rules
		[String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
		[String]$fireWallRuleName = "FlightDelay"
		
		# SQL database variables
		[String]$sqlDatabaseMaxSizeGB = 10
		
		#SQL query string for creating AvgDelays table
		[String]$sqlDatabaseTableName = "AvgDelays"
		[String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
		            [origin_city_name] [nvarchar](50) NOT NULL,
		            [weather_delay] float,
		        CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
		        (
		            [origin_city_name] ASC
		        )
		        )"
		#endregion

		#region - Add the Azure account 
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		$azureAccounts= Get-AzureAccount
		if (! $azureAccounts)
		{
		Add-AzureAccount
		}
		#endregion
		
		#region - Create and validate Azure SQL database server
		if ([string]::IsNullOrEmpty($sqlDatabaseServer))
		{
			Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
			$sqlDatabaseServer = (New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation).ServerName
		    Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan
		
		    Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
		    $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
		    New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress	
		    New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-Azureservices" -AllowAllAzureServices 
		}
		else
		{
		    $dbServer = Get-AzureSqlDatabaseServer -ServerName $sqlDatabaseServer
		    if (! $dbServer)
		    {
		        throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
		    }
		    else
		    {
			    Write-Host "`nUse an existing SQL Database server, $sqlDatabaseServer" -ForegroundColor Green
		    }
		}
		#endregion
		
		#region - Create and validate Azure SQL database
		if ([string]::IsNullOrEmpty($sqlDatabaseName))
		{
			Write-Host "`nCreating SQL Database, HDISqoop ..."  -ForegroundColor Green
		
			$sqlDatabaseName = "HDISqoop"
			$sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 
		
		    $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 
			
			$sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB
		}
		else
		{
		    $db = Get-AzureSqlDatabase -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
		    if (! $db)
		    {
		        throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
		    }
		    else
		    {
			    Write-Host "`nUse an existing SQL Database, $sqlDatabaseName" -ForegroundColor Green
		    }
		}
		#endregion
			
		#region -  Execute an SQL command to create the AvgDelays table
			
		Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
		$conn = New-Object System.Data.SqlClient.SqlConnection
		$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
		$conn.open()
		$cmd = New-Object System.Data.SqlClient.SqlCommand
		$cmd.connection = $conn
		$cmd.commandtext = $sqlCreateAvgDelaysTable
		$cmd.executenonquery()
			
		$conn.close()
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	>[AZURE.NOTE]このスクリプトは Representational State Transfer (REST) サービスの 1 つである http://bot.whatismyipaddress.com を使用して外部 IP アドレスを取得します。SQL データベース サーバーのファイアウォール ルールを作成する際に、その IP アドレスを使用します。

	スクリプトには次のいくつかの変数が使用されています。

	- **$ipAddressRestService** - 既定値は http://bot.whatismyipaddress.com です。外部 IP アドレスを取得するためのパブリック IP アドレス (REST サービス) です。必要に応じて他のサービスを使用することもできます。このサービスを使用して取得した外部 IP アドレスは、Azure SQL Database サーバーのファイアウォール ルールを作成する際に使用され、ご利用のワークステーションから (Windows PowerShell スクリプトを使用して) データベースへのアクセスが許可されます。
	- **$fireWallRuleName** - Azure SQL Database サーバーのファイアウォール ルールの名前です。既定の名前は <u>FlightDelay</u> です。この名前は必要に応じて変更できます。
	- **$sqlDatabaseMaxSizeGB** - この値は、新しい Azure SQL Database サーバーを作成するときにのみ使用されます。既定値は 10 GB です。このチュートリアルにはこれで十分です。
	- **$sqlDatabaseName** - この値は、新しい Azure SQL データベースを作成するときにのみ使用されます。既定値は HDISqoop です。この名前を変更した場合は、Sqoop Windows PowerShell スクリプトにも反映する必要があります。 

4. **F5** キーを押して、スクリプトを実行します。
5. スクリプトの出力結果を検証します。スクリプトが正常に実行されたことを確認してください。	

##<a id="nextsteps"></a> 次のステップ
ここでは、ファイルを Azure BLOB ストレージにアップロードする方法、Azure BLOB ストレージのデータを Hive テーブルに取り込む方法、Hive クエリの実行方法、Sqoop を使用して HDFS から Azure SQL データベースにデータをエクスポートする方法を学習しました。詳細については、次の記事を参照してください。

* [Azure HDInsight の概要][hdinsight-get-started]
* [HDInsight での Hive の使用][hdinsight-use-hive]
* [HDInsight での Oozie の使用][hdinsight-use-oozie]
* [HDInsight での Sqoop の使用][hdinsight-use-sqoop]
* [HDInsight での Pig の使用][hdinsight-use-pig]
* [Develop Java MapReduce programs for HDInsight (HDInsight 用 Java MapReduce プログラムの開発)][hdinsight-develop-mapreduce]
* [Develop C# Hadoop streaming programs for HDInsight (HDInsight 用 C# Hadoop ストリーミング プログラムの開発)][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png

 

<!---HONumber=July15_HO2-->