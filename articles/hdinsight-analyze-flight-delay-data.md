<properties linkid="manage-services-hdinsight-analyze-flight-delay-data" urlDisplayName="Analyze flight delay data with Hadoop in HDInsight" pageTitle="Analyze flight delay data using Hadoop in HDInsight | Azure" metaKeywords="" description="Learn how to upload data to HDInsight, how to process the data using Hive, and how to export the results to SQL Database using Sqoop." metaCanonical="" services="hdinsight" documentationCenter="" title="Analyze flight delay data using Hadoop in HDInsight " authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# HDInsight での Hadoop を使用したフライトの遅延データの分析

Hive では、*[HiveQL][HiveQL]* と呼ばれる SQL に似たスクリプト言語を使用して Hadoop MapReduce ジョブを実行します。大規模なデータの集約、照会、分析でも Hive を利用できます。このチュートリアルでは、Hive を使用して空港での平均遅延時間を計算する方法と、Sqoop を使用して結果を SQL データベースにエクスポートする方法を紹介します。

**前提条件:**

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

-   Azure HDInsight クラスター。HDInsight クラスターのプロビジョニングについては、「[Azure HDInsight の概要][Azure HDInsight の概要]」または「[HDInsight クラスターのプロビジョニング][HDInsight クラスターのプロビジョニング]」を参照してください。
-   Azure PowerShell がインストールされ構成されたワークステーション。手順については、[Azure PowerShell のインストールおよび構成に関するページ][Azure PowerShell のインストールおよび構成に関するページ]を参照してください。

**所要時間:** 30 分

## このチュートリアルの内容

-   [チュートリアルを準備する][チュートリアルを準備する]
-   [HiveQL スクリプトを作成してアップロードする][HiveQL スクリプトを作成してアップロードする]
-   [HiveQL スクリプトを実行する][HiveQL スクリプトを実行する]
-   [Azure SQL データベースに出力をエクスポートする][Azure SQL データベースに出力をエクスポートする]
-   [次のステップ][次のステップ]

## <span id="prepare"></span></a>チュートリアルを準備する

このチュートリアルでは、[米国運輸省研究・革新技術庁 (RITA)/運輸統計局][米国運輸省研究・革新技術庁 (RITA)/運輸統計局]が公開している航空便の定時運航データをコンピューターにダウンロードして使用します。次の作業を実行します。

1.  Web ブラウザーを使用して、RITA が公開している定時運航データをワークステーションにダウンロードする
2.  Azure PowerShell を使用して、HDInsight にデータをアップロードする
3.  Azure PowerShell を使用して、SQL Database のデータをエクスポートする準備をする

**HDInsight ストレージについて**

HDInsight はデータ ストレージとして Azure BLOB ストレージを使用します。これは *WASB* または *Azure ストレージ - BLOB* と呼ばれています。WASB は、HDFS を Azure BLOB ストレージ上で Microsoft が実装したものです。詳細については、「[HDInsight での Azure BLOB ストレージの使用][HDInsight での Azure BLOB ストレージの使用]」を参照してください。

HDInsight クラスターをプロビジョニングする際、HDFS と同様に、既定のファイル システムとして BLOB ストレージ コンテナーが指定されます。プロビジョニング プロセスを実行するときに、このコンテナーに加えて、同じ Azure ストレージ アカウントまたは別の Azure ストレージ アカウントに属する付加的なコンテナーを追加することもできます。付加的なストレージ アカウントを追加する方法の詳細については、「[HDInsight クラスターのプロビジョニング][HDInsight クラスターのプロビジョニング]」を参照してください。

このチュートリアルで使用する PowerShell スクリプトを簡単にするために、ファイルはすべて、*/tutorials/flightdelays* にある既定のファイル システム コンテナーに格納されています。既定で、このコンテナーの名前は、HDInsight クラスター名と同じです。

WASB の構文は次のとおりです。

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] HDInsight クラスター Version 3.0 では、*wasb://* 構文のみがサポートされます。旧バージョンの *"asv://"* 構文は、HDInsight 2.1 および 1.6 クラスターではサポートされますが、HDInsight 3.0 クラスターではサポートされず、以降のバージョンでもサポートされません。

> WASB のパスは、仮想パスです。詳細については、「[HDInsight での Azure BLOB ストレージの使用][HDInsight での Azure BLOB ストレージの使用]」を参照してください。

既定のファイル システム コンテナーに格納されているファイルの場合、次の URI のどれを使用しても HDInsight からアクセスできます (例として flightdelays.hql を使用しています)。

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
    wasb:///tutorials/flightdelays/flightdelays.hql
    /tutorials/flightdelays/flightdelays.hql

ストレージ アカウントから直接ファイルにアクセスする場合、ファイルの BLOB 名は次のようになります。

    tutorials/flightdelays/flightdelays.hql

このチュートリアルで使用するファイルを次の表に示します。

<table border="1">
<tr><td><strong>ファイル</strong></td><td><strong>説明</strong></td></tr>
<tr><td>\tutotirals\\flightdelays\\data           </td><td>Hive 用にフライト データを入力する </td></tr>
<tr><td>\tutorials\\flightdelays\\output          </td><td>Hive 用に出力する                </td></tr>
<tr><td>\tutorials\\flightdelays\\flightdelays.hql</td><td>HiveQL スクリプト ファイル        </td></tr>
<tr><td>\tutorials\\flightdelays\\jobstatus       </td><td>Hadoop ジョブの状態              </td></tr>
</table>

**Hive の内部テーブルと外部テーブルについて**

Hive の内部テーブルと外部テーブルについて知っておく必要のある事項がいくつかあります。

-   CREATE TABLE コマンドは内部テーブルを作成します。データ ファイルは既定のコンテナーに配置する必要があります。
-   CREATE TABLE コマンドはデータ ファイルを /hive/warehouse/<tablename> フォルダーに移動します。
-   CREATE EXTERNAL TABLE コマンドは外部テーブルを作成します。データ ファイルは既定のコンテナーの外部に配置することもできます。
-   CREATE EXTERNAL TABLE コマンドはデータ ファイルを移動しません。
-   CREATE EXTERNAL TABLE コマンドでは、LOCATION 内のフォルダーは許容されません。チュートリアルで sample.log ファイルのコピーを作成しているのは、これが理由です。

詳細については、「[HDInsight: Hive Internal and External Tables Intro (HDInsight: Hive の内部テーブルと外部テーブルの概要)] [cindygross-hive-tables]」を参照してください。

> [WACOM.NOTE] HiveQL のステートメントの 1 つは、Hive の外部テーブルを作成します。Hive の外部テーブルは、元の場所にデータ ファイルを保持します。Hive の内部テーブルは、hive\\warehouse にデータ ファイルを移動します。Hive の外部テーブルは、データ ファイルを既定のファイル システムの WASB コンテナーに配置する必要があります。既定の BLOB コンテナー以外のコンテナーにフライト データ ファイルを格納する場合は、Hive の内部テーブルを使用する必要があります。

**フライト データをダウンロードするには**

1.  [米国運輸省研究・革新技術庁 (RITA)/運輸統計局][米国運輸省研究・革新技術庁 (RITA)/運輸統計局]のページに移動します。
2.  このページで、次の値を選択します。

    | 名前          | 値                                                                                                                                                                                                                                                                                                                                                                                                     |
    |---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Filter Year   | 2012                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Filter Period | January                                                                                                                                                                                                                                                                                                                                                                                                |
    | Fields:       | \*Year\*、\*FlightDate\*、\*UniqueCarrier\*、\*Carrier\*、\*FlightNum\*、\*OriginAirportID\*、\*Origin\*、\*OriginCityName\*、\*OriginState\*、\*DestAirportID\*、\*Dest\*、\*DestCityName\*、\*DestState\*、\*DepDelayMinutes\*、\*ArrDelay\*、\*ArrDelayMinutes\*、\*CarrierDelay\*、\*WeatherDelay\*、\*NASDelay\*、\*SecurityDelay\*、\*LateAircraftDelay\* (その他のフィールドはすべてオフにする) |

3.  **[Download]** をクリックします。1 つのファイルをダウンロードするのに最長 15 分かかります。
4.  ファイルを **C:\\Tutorials\\FlightDelays\\Data** フォルダーに解凍します。ファイルはそれぞれ CSV ファイルで、サイズは約 60 GB です。
5.  ファイルの名前を、データを含む月の名前に変更します。たとえば、1 月のデータを含むファイルの場合、*January.csv* という名前にします。
6.  手順 2. ～ 5. を繰り返して、2012 年の 12 か月それぞれのファイルをダウンロードします。チュートリアルを実行するには、月別のファイルが少なくとも 1 つ必要です。

**フライト遅延データを Azure BLOB ストレージにアップロードするには**

1.  Azure PowerShell を開きます。手順については、[Azure PowerShell のインストールおよび構成に関するページ][Azure PowerShell のインストールおよび構成に関するページ]を参照してください。
2.  次のコマンドを実行して、Azure サブスクリプションに接続します。

        Add-AzureAccount

    Azure アカウント資格情報の入力を求められます。

3.  最初の 3 つの変数を設定して、コマンドを実行します。

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $ContainerName = "<BlobStorageContainerName>"

        $localFolder = "C:\Tutorials\FlightDelays\Data"
        $destFolder = "tutorials/flightdelays/data"

        $month = "January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"

    以下は変数とその説明です。

    <table border="1">

    <tr>
    <td>
    <strong>変数名</strong>

    </td>
    <td>
    <strong>説明</strong>

    </td>
    </tr>

    <tr>
    <td>
    $subscriptionName

    </td>
    <td>
    Azure サブスクリプション名。

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    フライト データ ファイルを格納するために使用する Azure Storage アカウント。既定のストレージ アカウントを使用することをお勧めします。

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    フライト データ ファイルを格納するために使用する Azure BLOB ストレージ コンテナー。既定の HDInsight クラスター ファイル システムの BLOB コンテナーを使用することをお勧めします。既定では、HDInsight クラスターと同じ名前です。

    </td>
    </tr>

    <tr>
    <td>
    $localFolder

    </td>
    <td>
    フライト遅延データを格納するワークステーションのフォルダーです。

    </td>
    </tr>

    <tr>
    <td>
    $destFolder

    </td>
    <td>
    フライト遅延データをアップロードする WASB パスです。Hadoop (HDInsight) パスでは、大文字と小文字が区別されます。

    </td>
    </tr>

    <tr>
    <td>
    $month

    </td>
    <td>
    12 個のファイルをすべてダウンロードしなかった場合は、この変数を変更する必要があります。

    </td>
    </tr>

    </table>
    </p>
4.  次のコマンドを実行して、ファイルをアップロードし、その一覧を表示します。

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to WASB        
        foreach ($item in $month){
            $fileName = "$localFolder\$item.csv"
            $blobName = "$destFolder/$item.csv"

            Write-Host "Copying $fileName to $blobName" -BackgroundColor Green

            Set-AzureStorageBlobContent -File $fileName -Container $dataContainerName -Blob $blobName -Context $destContext
        }

        # List the uploaded files on HDinsight
        Get-AzureStorageBlob -Container $dataContainerName  -Context $destContext -Prefix $destFolder

ファイルのアップロード方法として別の方法を選択した場合も、ファイル パスは *tutorials/flightdelays/data* にしてください。ファイルにアクセスする構文は次のとおりです。

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*tutorials/flightdelays/data* は、ファイルのアップロード時に作成した仮想フォルダーです。月ごとに 1 つ、合計 12 個のファイルがあることを確認します。

**SQL データベースを準備するには**

1.  Azure PowerShell を開きます。
2.  次のコマンドを実行して、Azure サブスクリプションに接続します。

        Add-AzureAccount

    Azure アカウント資格情報の入力を求められます。

3.  次のスクリプトで最初の 6 個の変数を設定して、コマンドを実行します。

        # Azure subscription name
        $subscriptionName = "<AzureSubscriptionName>"

        # SQL database server variables
        $sqlDatabaseServer = ""  # specify the Azure SQL database server name if you have one created. Otherwise use "".
        $sqlDatabaseUsername = "<SQLDatabaseUserName>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"
        $sqlDatabaseLocation = "<MicrosoftDataCenter>"   #For example, West US.

        # SQL database variables
        $sqlDatabaseName = ""  # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
        $sqlDatabaseMaxSizeGB = 10

        #SQL query string for creating AvgDelays table
        $sqlDatabaseTableName = "AvgDelays"
        $sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                [origin_city_name] [nvarchar](50) NOT NULL,
                [weather_delay] float,
            CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
            (
                [origin_city_name] ASC
            )
            )"

    以下は変数とその説明です。

    <table border="1">

    <tr>
    <td>
    <strong>変数名</strong>

    </td>
    <td>
    <strong>説明</strong>

    </td>
    </tr>

    <tr>
    <td>
    $subscriptionName

    </td>
    <td>
    Azure サブスクリプション名。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseServer

    </td>
    <td>
    データのエクスポート先として Sqoop が使用する SQL Database サーバー名。そのままにしておくと、スクリプトによって自動的に作成されます。それ以外の場合は、既存の SQL Database または SQL Server を指定します。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseUsername

    </td>
    <td>
    SQL Database または SQL Server のユーザー名。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabasePassword

    </td>
    <td>
    SQL Database または SQL Server のユーザー パスワード。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseLocation

    </td>
    <td>
    これはスクリプトで SQL Database サーバーを作成する場合にのみ使用されます。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseName

    </td>
    <td>
    データのエクスポート先として Sqoop が使用する SQL Database 名。そのままにしておくと、スクリプトによって自動的に作成されます。それ以外の場合は、既存の SQL Database または SQL Server を指定します。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseMaxSizeGB

    </td>
    <td>
    これはスクリプトで SQL Database を作成する場合にのみ使用されます。

    </td>
    </tr>

    </table>
    </p>
4.  次のコマンドを実行して、SQL Database サーバー、データベース、テーブルを作成します。

        # Select the current Azure subscription in case there are multiple subscriptions
        Select-AzureSubscription $subscriptionName

        # create a new Azure SQL Database if requested
        if ([string]::IsNullOrEmpty($sqlDatabaseServer))
        {
            $sqlDatabaseServer = New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation 
            Write-Host "The new SQL Database server is $sqlDatabaseServer."  -BackgroundColor Green

        }
        else
        {
            Write-Host "Use an existing SQL Database server: $sqlDatabaseServer" -BackgroundColor Green
        }

        # Create a new SQL database if requested
        if ([string]::IsNullOrEmpty($sqlDatabaseName))
        {
            $sqlDatabaseName = "HDISqoop"
            $sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 
            $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 

            $sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB

            Write-Host "The new SQL Database is $sqlDatabaseName."  -BackgroundColor Green

        }
        else
        {
            Write-Host "Use an existing SQL Database : $sqlDatabaseName" -BackgroundColor Green
        }

        #Create AvgDelays table
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()

        $conn.close()

## <span id="createscript"></span></a>HiveQL スクリプトを作成してアップロードする

Azure PowerShell を使用して、複数の HiveQL ステートメントを一度に実行することも、HiveQL ステートメントをスクリプト ファイルにまとめることもできます。このチュートリアルでは、HiveQL スクリプトを作成します。スクリプト ファイルは、WASB にアップロードする必要があります。次のセクションでは、Azure PowerShell を使用してスクリプト ファイルを実行します。

HiveQL スクリプトは、次の作業を実行します。

1.  **delays\_raw テーブルを削除します** (テーブルが既に存在する場合)。
2.  **delays\_raw 外部 Hive テーブルを作成します**。このテーブルはフライト遅延ファイルのある WASB の場所を指しています。このクエリでは、フィールドがコンマ (,) 区切りで、行末が "\\n" であることを指定しています。この場合、フィールド値にコンマが "含まれている" と問題が発生します。Hive は、フィールド区切りのコンマとフィールド値の一部であるコンマを識別できません (ORIGIN\_CITY\_NAME フィールドや DEST\_CITY\_NAME フィールドの値など)。これに対処するため、クエリでは、間違って複数の列に分割されるデータを格納する TEMP 列を作成します。
3.  **delays テーブルを削除します** (テーブルが既に存在する場合)。
4.  **delays テーブルを作成します**。次へ進む前にデータをクリーンアップしておくと面倒がありません。次のクエリは、*delays\_raw* テーブルを基にして、新しい *delays* テーブルを作成します。(前述のように) TEMP 列はコピーしません。*substring* 関数を使用してデータから引用符を削除します。
5.  **悪天候による平均遅延を計算し、その結果を都市名ごとにグループ化します。**さらに、結果を WASB に出力します。このクエリはデータからアポストロフィを削除し、*weather\_dealy* の値が *null* である行を除外します。このチュートリアルの後の方で使用する Sqoop では、これらの値が適切に処理されないためです。

HiveQL コマンドの完全な一覧については、「[Hive Data Definition Language (Hive データ定義言語)][HiveQL]」を参照してください。各 HiveQL コマンドは、セミコロンで終了します。

**HiveQL スクリプト ファイルを作成するには**

1.  Azure PowerShell を開きます。
2.  次のコマンドを実行して、Azure サブスクリプションに接続します。

        Add-AzureAccount

3.  最初の 2 つの変数を設定し、次のコマンドを実行します。

        $storageAccountName = "<AzureStorageAccountName>"
        $containerName ="<BlobStorageContainerName>"

        $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql"  
        $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 

        $srcDataFolder = "tutorials/flightdelays/data" 
        $dstDataFolder = "tutorials/flightdelays/output"

    以下は変数とその説明です。

    <table border="1">

    <tr>
    <td>
    <strong>変数名</strong>

    </td>
    <td>
    <strong>説明</strong>

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    HiveQL スクリプト ファイルを格納するために使用する Azure Storage アカウント。このチュートリアルで用意された PowerShell スクリプトでは、フライト データ ファイルとスクリプト ファイルがどちらも同じ Azure Storage アカウントおよび BLOB ストレージ コンテナーに存在する必要があります。

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    HiveQL スクリプト ファイルを格納するために使用する Azure BLOB ストレージ コンテナー。このチュートリアルで用意された PowerShell スクリプトでは、フライト データ ファイルとスクリプト ファイルがどちらも同じ Azure Storage アカウントおよび BLOB ストレージ コンテナーに存在する必要があります。

    </td>
    </tr>

    <tr>
    <td>
    $hqlLocalFileName

    </td>
    <td>
    HiveQL スクリプトが WASB にアップロードされる前のローカル ファイル名。PowerShell スクリプトを簡単にするために、ファイルをローカルに作成し、Set-AzureStorageBlobContent コマンドレットを使用して HDInsight にスクリプト ファイルをアップロードします。

    </td>
    </tr>

    <tr>
    <td>
    $hqlBlobName

    </td>
    <td>
    WASB のパスが付いたスクリプト ファイル名です。

    </td>
    </tr>

    <tr>
    <td>
    $srcDataFolder

    </td>
    <td>
    HiveQL スクリプトがデータを取り出す WASB のフォルダーです。

    </td>
    </tr>

    <tr>
    <td>
    $dstDataFolder

    </td>
    <td>
    HiveQL スクリプトが出力を送信する WASB のフォルダーです。このチュートリアルの後の方で、Sqoop を使用して Azure SQL データベースにこのフォルダーのデータをエクスポートします。

    </td>
    </tr>

    </table>
    </p>
4.  次のコマンドを実行して、HiveQL ステートメントを定義します。

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
            "LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$srcDataFolder';" 

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

        $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY 'wasb://$containerName@$storageAccountName.blob.core.windows.net/$dstDataFolder' " +
            "SELECT regexp_replace(origin_city_name, '''', ''), " +
                "avg(weather_delay) " +
            "FROM delays " +
            "WHERE weather_delay IS NOT NULL " +
            "GROUP BY origin_city_name;"

        $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal

5.  次のコマンドを実行して、ワークステーションに Hive スクリプト ファイルを作成し、WASB にアップロードします。

        # Write the Hive script into a local file
        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to WASB
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $containerName -Blob $hqlBlobName -Context $destContext

        # List the script file from WASB
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $hqlBlobName

    次のように出力されます。

        Container Uri: https://xxxxxxxx.blob.core.windows.net/hdi0212v3

        Name                              BlobType   Length                            ContentType                       LastModified                      SnapshotTime
        ----                              --------   ------                            -----------                       ------------                      ------------
        tutorials/flightdelays/flightd... BlockBlob  1938                              application/octet-stream          2/12/2014 9:57:28 PM +00:00

## <span id="executehqlscript"></span></a>HiveQL スクリプトを実行する

Hive の実行に使用できる Azure PowerShell コマンドレットはいくつかあります。このチュートリアルでは Invoke-Hive を使用します。その他の方法については、「[HDInsight での Hive の使用][HDInsight での Hive の使用]」を参照してください。Invoke-Hive を使用して、HiveQL ステートメントまたは HiveQL スクリプトを実行できます。ここでは、作成後に Azure BLOB ストレージにアップロードした HiveQL スクリプトを使用します。

Hive パスには既知の問題があります。この問題の解決方法は [TechNet Wiki][TechNet Wiki] をご覧ください。

**PowerShell を使用して Hive クエリを実行するには**

1.  Azure PowerShell を開きます。
2.  次のコマンドを実行して、現在のディレクトリを変更します。

    cd \\Tutorials\\FlightDelays\\

    この手順は、Hive の出力のコピーをコンピューターにダウンロードするために必要になります。既定では、PowerShell フォルダーに対する書き込みアクセス許可がありません。

3.  次のコマンドを実行して、Azure サブスクリプションに接続します。

        Add-AzureAccount

4.  最初の 3 つの変数を設定して、実行します。

        $clusterName = "<HDInsightClusterName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureBlobStorageContainerName>"
        $hqlScriptFile = "wasb://$containerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql"
        $outputBlobName = "tutorials/flightdelays/output/000000_0"

    以下は変数とその説明です。

    <table border="1">

    <tr>
    <td>
    <strong>変数名</strong>

    </td>
    <td>
    <strong>説明</strong>

    </td>
    </tr>

    <tr>
    <td>
    $clusterName

    </td>
    <td>
    Hive スクリプトと Sqoop のエクスポートを実行する HDInsight クラスター。

    </td>
    </tr>

    <tr>
    <td>
    $storageAccountName

    </td>
    <td>
    HiveQL スクリプトを格納するために使用する Azure Storage アカウント。「<a href = "#createScript">[HiveQL スクリプトを作成してアップロードする][1]</a>」を参照してください。

    </td>
    </tr>

    <tr>
    <td>
    $containerName

    </td>
    <td>
    HiveQL スクリプトを格納するために使用する Azure BLOB ストレージ コンテナー。「<a href = "#createScript">[HiveQL スクリプトを作成してアップロードする][1]</a>」を参照してください。

    </td>
    </tr>

    <tr>
    <td>
    $hqlScriptFile

    </td>
    <td>
    HiveQL スクリプト ファイルの URI です。

    </td>
    </tr>

    <tr>
    <td>
    $outputBlobName

    </td>
    <td>
    HiveQL スクリプトの出力ファイルです。既定の名前は *000000\_0* です。

    </td>
    </tr>

    </table>
    </p>
5.  次のコマンドを実行して、Hive を呼び出します。

        Use-AzureHDInsightCluster $clusterName

        Invoke-Hive –File $hqlScriptFile

6.  次のコマンドを実行して、結果を確認します。

        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

        Get-AzureStorageBlobContent -Container $ContainerName -Blob $outputBlobName -Context $storageContext 

        cat ".\$outputBlobName" | findstr /B "Ch"

    次のように出力されます。

        Champaign/Urbana 19.023255813953487
        Charleston 24.93975903614458
        Charleston/Dunbar 16.954545454545453
        Charlotte 16.88616981831665
        Charlotte Amalie 12.88888888888889
        Charlottesville 25.444444444444443
        Chattanooga 19.883561643835616
        Cheyenne 9.875
        Chicago 16.77321649680922
        Chico 12.340909090909092
        Christiansted 12.318181818181818

    各都市と遅延時間の間には、PowerShell の出力ウィンドウに表示されない区切り記号があります。それは "\\001" です。この区切り記号は、Sqoop エクスポートを実行するときに使用します。

## <span id="exportdata"></span></a>Azure SQL データベースに Hive ジョブ出力をエクスポートする

最後の手順として、Sqoop エクスポートを実行してデータを SQL データベースにエクスポートします。SQL データベースと AvgDelays テーブルはこのチュートリアルで既に作成しています。

**SQL データベースにデータをエクスポートするには**

1.  Azure PowerShell を開きます。
2.  次のコマンドを実行して、Azure サブスクリプションに接続します。

        Add-AzureAccount

3.  最初の 5 つの変数を設定し、コマンドを実行します。

        $clusterName = "<HDInsightClusterName>"

        $sqlDatabaseServerName = "<SQLDatabaseServerName>"
        $sqlDatabaseUserName = "<SQLDatabaseUsername>"
        $sqlDatabasePassword = "<SQLDatabasePassword>"

        $sqlDatabaseName = "<SQLDatabaseName>" # The default name is "HDISqoop"
        $sqlDatabaseTableName = "AvgDelays" 

        $exportDir = "wasb://$containerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/output"

        $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"

    以下は変数とその説明です。

    <table border="1">

    <tr>
    <td>
    <strong>変数名</strong>

    </td>
    <td>
    <strong>注</strong>

    </td>
    </tr>

    <tr>
    <td>
    $clusterName

    </td>
    <td>
    HDInsight クラスター名。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseServer

    </td>
    <td>
    Sqoop によるデータのエクスポート先となる SQL Database サーバー。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseUsername

    </td>
    <td>
    SQL Database のユーザー名。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabasePassword

    </td>
    <td>
    SQL Database のユーザー パスワード。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseName

    </td>
    <td>
    Sqoop によるデータのエクスポート先となる SQL Database。既定のファイル名は \*HDISqoop" です。

    </td>
    </tr>

    <tr>
    <td>
    $sqlDatabaseTableName

    </td>
    <td>
    Sqoop によるデータのエクスポート先となる SQL Database。既定の名前は AvgDelays です。これは、このチュートリアルで先に作成したテーブルです。

    </td>
    </tr>

    <tr>
    <td>
    $exportDir

    </td>
    <td>
    Hive の出力ファイルの場所です。Sqoop は、この場所にあるファイルを SQL Database にエクスポートします。

    </td>
    </tr>

    </table>
    </p>
4.  次のコマンドを実行して、データをエクスポートします。

        $sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "

        $sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
        Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob

        Write-Host "Standard Error" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
        Write-Host "Standard Output" -BackgroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

5.  SQL データベースに接続し、*AvgDelays* テーブルに保存されている都市別平均遅延を表示します。

    ![HDI.FlightDelays.AvgDelays.Dataset][HDI.FlightDelays.AvgDelays.Dataset]

## <span id="nextsteps"></span></a>次のステップ

ここでは、ファイルを BLOB ストレージへアップロードする方法、BLOB ストレージのデータを Hive テーブルへ取り込む方法、Hive クエリの実行方法、Sqoop を使用して HDFS から Azure SQL データベースへデータをエクスポートする方法を学習しました。詳細については、次の記事を参照してください。

-   [Azure HDInsight の概要][Azure HDInsight の概要]
-   [HDInsight での Hive の使用][HDInsight での Hive の使用]
-   [Use Oozie with HDInsight (HDInsight での Oozie の使用)][Use Oozie with HDInsight (HDInsight での Oozie の使用)]
-   [HDInsight での Sqoop の使用][HDInsight での Sqoop の使用]
-   [HDInsight での Pig の使用][HDInsight での Pig の使用]
-   [Develop Java MapReduce programs for HDInsight (HDInsight 用 Java MapReduce プログラムの開発)][Develop Java MapReduce programs for HDInsight (HDInsight 用 Java MapReduce プログラムの開発)]
-   [Develop C# Hadoop streaming programs for HDInsight (HDInsight 用 C# Hadoop ストリーミング プログラムの開発)][Develop C# Hadoop streaming programs for HDInsight (HDInsight 用 C# Hadoop ストリーミング プログラムの開発)]

  [HiveQL]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
  [Azure HDInsight の概要]: ../hdinsight-get-started/
  [HDInsight クラスターのプロビジョニング]: ../hdinsight-provision-clusters/
  [Azure PowerShell のインストールおよび構成に関するページ]: ../install-configure-powershell/
  [チュートリアルを準備する]: #prepare
  [HiveQL スクリプトを作成してアップロードする]: #createscript
  [HiveQL スクリプトを実行する]: #executehqlscript
  [Azure SQL データベースに出力をエクスポートする]: #exportdata
  [次のステップ]: #nextsteps
  [HDInsight での Azure BLOB ストレージの使用]: ../hdinsight-use-blob-storage/
  [HDInsight での Hive の使用]: ../hdinsight-use-hive/
  [TechNet Wiki]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
  [1]: #createScript
  [HDI.FlightDelays.AvgDelays.Dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
  [HDInsight での Sqoop の使用]: ../hdinsight-use-sqoop/
  [HDInsight での Pig の使用]: ../hdinsight-use-pig/
