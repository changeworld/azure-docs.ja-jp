<properties urlDisplayName="Analyze flight delay data with Hadoop in HDInsight" pageTitle="HDInsight の Hadoop を使用したフライトの遅延データの分析 | Azure" metaKeywords="" description="HDInsight にデータをアップロードする方法、Hive を使用してデータを処理する方法、Sqoop を使用して SQL Database に結果をエクスポートする方法について説明します。" metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight での Hadoop を使用したフライトの遅延データの分析 " authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# HDInsight での Hadoop を使用したフライトの遅延データの分析

Hive では、*[HiveQL][HiveQL]* と呼ばれる SQL に似たスクリプト言語を使用して Hadoop MapReduce ジョブを実行します。大規模なデータの集約、照会、分析に Hive を利用できます。このチュートリアルでは、Hive を使用して空港での平均遅延時間を計算する方法と、Sqoop を使用して結果を Azure SQL Database にエクスポートする方法を紹介します。

HDInsight の大きな利点の 1 つに、データ ストレージとコンピューティングの分離があります。HDInsight では、データ ストレージとして Azure BLOB ストレージが使用されます。一般に、HDInsight 処理は、次の 3 つのパートに分割できます。

1.  Azure BLOB ストレージにデータを保存する。これは連続的なプロセスとなる場合があります。たとえば、気象データ、センサー データ、Web ログを BLOB ストレージに保存できます。ここではフライトの遅延データが保存対象となります。
2.  ジョブを実行する。データを処理する段階になったら、PowerShell スクリプト (またはクライアント アプリケーション) を実行して HDInsight クラスターをプロビジョニングし、ジョブを実行して、クラスターを削除します。このジョブによって、出力データが Azure BLOB ストレージに保存されます。出力データは、クラスターの削除後も維持されます。こうして、実際に消費した分だけが課金されることとなります。
3.  BLOB ストレージから出力結果を取り出す。ここでは、Azure SQL Database にデータをエクスポートする過程がこれに相当します。

この記事のシナリオと構成を示したのが次の図です。

![HDI.FlightDelays.flow][HDI.FlightDelays.flow]

注: 図中の番号は、セクション タイトルに対応します。

**所要時間:** 30 分

## このチュートリアルの内容

-   [前提条件][前提条件]
-   [新規または既存の HDInsight クラスター上で Hive ジョブを実行する (M1)][新規または既存の HDInsight クラスター上で Hive ジョブを実行する (M1)]
-   [Sqoop を使用して出力結果を Azure SQL Database にエクスポートする (M2)][Sqoop を使用して出力結果を Azure SQL Database にエクスポートする (M2)]
-   [次のステップ][次のステップ]
-   [付録 A: Azure BLOB ストレージへのフライト遅延データのアップロード (A1)][付録 A: Azure BLOB ストレージへのフライト遅延データのアップロード (A1)]
-   [付録 B: HiveQL スクリプトの作成とアップロード (A2)][付録 B: HiveQL スクリプトの作成とアップロード (A2)]

## <span id="prerequisite"></span></a>前提条件

このチュートリアルを読み始める前に、次の項目を用意する必要があります。

-   Azure PowerShell がインストールされ構成されたワークステーション。手順については、[Azure PowerShell のインストールおよび構成に関するページ][Azure PowerShell のインストールおよび構成に関するページ]を参照してください。
-   Azure サブスクリプション。 \*\*\*

**HDInsight ストレージについて**

HDInsight の Hadoop クラスターでは、データ ストレージとして Azure BLOB ストレージが使用されます。これは *WASB* または *Azure ストレージ - BLOB* と呼ばれています。WASB は、*HDFS* を Azure BLOB ストレージ上で Microsoft が実装したものです。詳細については、「[HDInsight での Azure BLOB ストレージの使用][HDInsight での Azure BLOB ストレージの使用]」を参照してください。

HDInsight クラスターをプロビジョニングする際は、HDFS と同じ要領で、Azure ストレージ アカウントの BLOB ストレージ コンテナーを既定のファイル システムとして指定します。このストレージ アカウントを "*既定のストレージ アカウント*" といい、BLOB コンテナーは、"*既定の BLOB コンテナー*" または "*既定のコンテナー*" と呼ばれています。既定のストレージ アカウントは、HDInsight クラスターと同じデータ センター内に存在する必要があります。HDInsight クラスターを削除しても、既定のコンテナーと既定のストレージ アカウントは削除されません。

プロビジョニング プロセスでは、既定のストレージ アカウントに加え、他の Azure ストレージ アカウントを HDInsight クラスターにバインドすることができます。ここでのバインドとは、ストレージ アカウントとストレージ アカウント キーを構成ファイルに追加することです。そうすることでクラスターは実行時に、それらのストレージ アカウントにアクセスすることができます。ストレージ アカウントを追加する方法については、「[HDInsight での Hadoop クラスターのプロビジョニング][HDInsight での Hadoop クラスターのプロビジョニング]」を参照してください。

WASB の構文は次のとおりです。

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] WASB のパスは仮想パスです。詳細については、「[HDInsight での Azure BLOB ストレージの使用][HDInsight での Azure BLOB ストレージの使用]」を参照してください。

既定のコンテナーに格納されているファイルは、次の URI のどれを使用しても HDInsight からアクセスできます (例として flightdelays.hql を使用しています)。

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
    wasb:///tutorials/flightdelays/flightdelays.hql
    /tutorials/flightdelays/flightdelays.hql

ストレージ アカウントから直接ファイルにアクセスする場合、ファイルの BLOB 名は次のようになります。

    tutorials/flightdelays/flightdelays.hql

BLOB 名の前に "/" がないことに注意してください。

**このチュートリアルで使用するファイル**

このチュートリアルで使用するファイルを次の表に示します。

| ファイル                                                                  | 説明                                                                        |
|---------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql | 実行する Hive ジョブで必要となる HiveQL スクリプト ファイル。               |
| wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data         | Hive ジョブの入力データ。                                                   |
| \\tutorials\\flightdelays\\output                                         | Hive ジョブの出力パス。出力データの保存には、既定のコンテナーを使用します。 |
| \\tutorials\\flightdelays\\jobstatus                                      | Hive ジョブ ステータス フォルダー。                                         |

このチュートリアルでは、[米国運輸省研究・革新技術庁 (RITA)/運輸統計局][米国運輸省研究・革新技術庁 (RITA)/運輸統計局]が公開している航空便の定時運航データをダウンロードして使用します。データは、パブリック BLOB アクセス権限の設定された Azure BLOB ストレージ コンテナーにアップロード済みです。パブリック BLOB コンテナーであるため、このストレージ アカウントを HDInsight クラスターにバインドする必要はありません。HiveQL スクリプトのアップロード先も同じ BLOB コンテナーです。独自のストレージ アカウントにデータを取得/アップロードする方法や、HiveQL スクリプト ファイルを作成/アップロードする方法については、[付録][付録]を参照してください。

**Hive の内部テーブルと外部テーブルについて**

Hive の内部テーブルと外部テーブルについて知っておく必要のある事項がいくつかあります。

-   CREATE TABLE コマンドは内部テーブルを作成します。データ ファイルは既定のコンテナーに配置する必要があります。
-   CREATE TABLE コマンドはデータ ファイルを /hive/warehouse/<tablename> フォルダーに移動します。
-   CREATE EXTERNAL TABLE コマンドは外部テーブルを作成します。データ ファイルは既定のコンテナーの外部に配置することもできます。
-   CREATE EXTERNAL TABLE コマンドはデータ ファイルを移動しません。
-   CREATE EXTERNAL TABLE コマンドでは、LOCATION 内のフォルダーは許容されません。チュートリアルで sample.log ファイルのコピーを作成しているのは、これが理由です。

詳細については、「[HDInsight: Hive Internal and External Tables Intro (HDInsight: Hive の内部テーブルと外部テーブルの概要)][HDInsight: Hive Internal and External Tables Intro (HDInsight: Hive の内部テーブルと外部テーブルの概要)]」を参照してください。

> [WACOM.NOTE] HiveQL のステートメントの 1 つは、Hive の外部テーブルを作成します。Hive の外部テーブルは、元の場所にデータ ファイルを保持します。Hive の内部テーブルは、hive\\warehouse にデータ ファイルを移動します。Hive の内部テーブルは、データ ファイルを既定のコンテナーに配置する必要があります。既定の BLOB コンテナー外にデータを保存する場合は、Hive 外部テーブルを使用する必要があります。

## <span id="runjob"></span></a>新規または既存の HDInsight クラスター上で Hive ジョブを実行する

Hadoop はバッチ処理です。Hive ジョブの実行方法としては、ジョブに使用するクラスターをプロビジョニングし、完了後、ジョブを削除するのが最も経済的です。以下のスクリプトは、その全過程をカバーしています。HDInsight クラスターのプロビジョニングと Hive ジョブの実行の詳細については、「[HDInsight での Hadoop クラスターのプロビジョニング][HDInsight での Hadoop クラスターのプロビジョニング]」と「[HDInsight での Hive の使用][HDInsight での Hive の使用]」を参照してください。

**PowerShell を使用して Hive クエリを実行するには**

1.  PowerShell ISE を開きます。
2.  次のスクリプトをスクリプト ウィンドウにコピーして貼り付けます。

        *** add script here

3.  **F5** キーを押して、スクリプトを実行します。次のように出力されます。

    ![HDI.FlightDelays.RunHiveJob.output][HDI.FlightDelays.RunHiveJob.output]

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
    **変数名**

    </td>
    <td>
    **注**

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
-   [Develop C\# Hadoop streaming programs for HDInsight (HDInsight 用 C\# Hadoop ストリーミング プログラムの開発)][Develop C\# Hadoop streaming programs for HDInsight (HDInsight 用 C\# Hadoop ストリーミング プログラムの開発)]

## <span id="appendix-a"></span></a>付録 A: フライト遅延データを Azure BLOB ストレージにアップロードする

**フライト データをダウンロードするには**

1.  [米国運輸省研究・革新技術庁 (RITA)/運輸統計局][米国運輸省研究・革新技術庁 (RITA)/運輸統計局]のページに移動します。
2.  このページで、次の値を選択します。

    | 名前          | 値                                                                                                                                                                                                                                                                                                                                                                                                     |
    |---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Filter Year   | 2013                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Filter Period | January                                                                                                                                                                                                                                                                                                                                                                                                |
    | Fields:       | \*Year\*、\*FlightDate\*、\*UniqueCarrier\*、\*Carrier\*、\*FlightNum\*、\*OriginAirportID\*、\*Origin\*、\*OriginCityName\*、\*OriginState\*、\*DestAirportID\*、\*Dest\*、\*DestCityName\*、\*DestState\*、\*DepDelayMinutes\*、\*ArrDelay\*、\*ArrDelayMinutes\*、\*CarrierDelay\*、\*WeatherDelay\*、\*NASDelay\*、\*SecurityDelay\*、\*LateAircraftDelay\* (その他のフィールドはすべてオフにする) |

3.  **[Download]** をクリックします。
4.  ファイルを **C:\\Tutorials\\FlightDelays\\Data** フォルダーに解凍します。ファイルはそれぞれ CSV ファイルで、サイズは約 60 GB です。
5.  ファイルの名前を、データを含む月の名前に変更します。たとえば、1 月のデータを含むファイルの場合、*January.csv* という名前にします。
6.  手順 2. ～ 5. を繰り返して、2013 年の 12 か月それぞれのファイルをダウンロードします。チュートリアルを実行するには、月別のファイルが少なくとも 1 つ必要です。

**フライト遅延データを Azure BLOB ストレージにアップロードするには**

1.  PowerShell ISE を開きます。
2.  次のスクリプトをスクリプト ウィンドウに貼り付けます。

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
        $localFolder = "C:\Tutorials\FlightDelays\2014Data"  # the source folder
        $destFolder = "tutorials/flightdelays/data"     #the blob name prefix for the files to be uploaded
        #EndRegion

        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        if (-not (Get-AzureAccount)){ Add-AzureAccount}
        #EndRegion

        #Region - Validate user inpute
        # Validate the storage account
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

        # List the uploaded files on HDinsight
        Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
        #EndRegion

3.  **F5** キーを押して、スクリプトを実行します。

## <span id="appendix-b"></span></a>付録 B: HiveQL スクリプトを作成してアップロードする

Azure PowerShell を使用して、複数の HiveQL ステートメントを一度に実行することも、HiveQL ステートメントをスクリプト ファイルにまとめることもできます。このセクションでは、HiveQL スクリプトを作成し、PowerShell を使用して Azure BLOB ストレージにアップロードする方法を説明します。Hive を利用するには、HiveQL スクリプトが WASB に格納されている必要があります。

HiveQL スクリプトは、次の作業を実行します。

1.  **delays\_raw テーブルを削除します** (テーブルが既に存在する場合)。
2.  **delays\_raw 外部 Hive テーブルを作成します**。このテーブルはフライト遅延ファイルのある WASB の場所を指しています。このクエリでは、フィールドがコンマ (,) 区切りで、行末が "\\n" であることを指定しています。この場合、フィールド値にコンマが "含まれている" と問題が発生します。Hive は、フィールド区切りのコンマとフィールド値の一部であるコンマを識別できません (ORIGIN\_CITY\_NAME フィールドや DEST\_CITY\_NAME フィールドの値など)。これに対処するため、クエリでは、間違って複数の列に分割されるデータを格納する TEMP 列を作成します。
3.  **delays テーブルを削除します** (テーブルが既に存在する場合)。
4.  **delays テーブルを作成します**。次へ進む前にデータをクリーンアップしておくと面倒がありません。次のクエリは、*delays\_raw* テーブルを基にして、新しい *delays* テーブルを作成します。(前述のように) TEMP 列はコピーしません。*substring* 関数を使用してデータから引用符を削除します。
5.  **悪天候による平均遅延を計算し、その結果を都市名ごとにグループ化します。**さらに、結果を WASB に出力します。このクエリはデータからアポストロフィを削除し、*weather\_dealy* の値が *null* である行を除外します。このチュートリアルの後の方で使用する Sqoop では、これらの値が適切に処理されないためです。

HiveQL コマンドの完全な一覧については、「[Hive Data Definition Language (Hive データ定義言語)][HiveQL]」を参照してください。各 HiveQL コマンドは、セミコロンで終了します。

**HiveQL スクリプト ファイルを作成するには**

1.  Azure PowerShell ISE を開きます。
2.  次のスクリプトをスクリプト ウィンドウにコピーして貼り付けます。

        [CmdletBinding()]
        Param(

            # Azure blob storage variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the HDInsight cluster name.")]
            [String]$HDInsightClusterName
        )

        #--------------------------------------------------------------------
        # Constants used in the script
        #--------------------------------------------------------------------
        # the HQL script file is exported as this file before uploaded to WASB
        $hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql" 
        # the HQL script file will be upload to WASB as this blob name
        $hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 

        # this two constants are used by the HQL scrpit file
        $srcDataFolder = "tutorials/flightdelays/data" 
        $dstDataFolder = "tutorials/flightdelays/output"

        #--------------------------------------------------------------------
        # Validate the file and file path
        #--------------------------------------------------------------------

        # check if a file with the same file name already exist on the workstation
        Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
        if (test-path $hqlLocalFileName){

            $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", `
                "Overwrite the existing file."

            $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", `
                "Not overwrite the existing file."

            $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)

            $result = $Host.UI.PromptForChoice("Write file", 
                                                "Overwrite the existing script file, 
                                                $hqlLocalFileName?", 
                                                $options, 
                                                0)

            switch ($result)
            {
            0 {
                # create the folder if it doesn't exist
                $folder = split-path $hqlLocalFileName
                if (-not (test-path $folder))
                {
                    Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green

                    new-item $folder -ItemType directory  
                }
            }
            1 { throw "User cancelled the operation."}
            }
        }

        #--------------------------------------------------------------------
        # Add the Azure account 
        #--------------------------------------------------------------------
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Add-AzureAccount


        #--------------------------------------------------------------------
        # Retrieve the default storage account and the default container name
        #--------------------------------------------------------------------
        Write-Host "`nRetrieving the HDInsight cluster default storage account information ..." `
                    -ForegroundColor Green

        $hdi = Get-AzureHDInsightCluster -Name $HDInsightClusterName

        if (-not $hdi){
            throw "The HDInsight cluster, $HDInsightClusterName, doesn't exist!" 
        }
        else{
            $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName `
                                    -replace ".blob.core.windows.net"
            $containerName = $hdi.DefaultStorageAccount.StorageContainerName

            Write-Host "`tThe default storage account for the cluster is $storageAccountName." `
                        -ForegroundColor Cyan
            Write-host "`tThe default Blob container for the cluster is $containerName." `
                        -ForegroundColor Cyan
        }

        #--------------------------------------------------------------------
        # Write the Hive script into a local file
        #--------------------------------------------------------------------
        Write-Host "Writing the Hive script into a file on your workstation ..." `
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

        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force 

        #--------------------------------------------------------------------
        # Upload the Hive script to the default Blob container
        #--------------------------------------------------------------------
        Write-Host "Uploading the Hive script to the default Blob container ..." -ForegroundColor Green

        # Create a storage context object
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Upload the file from local workstation to WASB
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $containerName -Blob $hqlBlobName -Context $destContext 

        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    スクリプトには次の定数が使用されています。

    -   **$hqlLocalFileName**: HiveQL スクリプト ファイルは、WASB にアップロードされる前に、いったんローカルに保存されます。その際に、このファイル名が使用されます。既定値は <u>C:\\tutorials\\flightdelays\\flightdelays.hql</u> です。
    -   **$hqlBlobName**: Azure BLOB ストレージで使用される HiveQL スクリプト ファイルの BLOB 名。既定値は <u>tutorials/flightdelays/flightdelays.hql</u> です。ファイルは直接 Azure BLOB ストレージに書き込まれるため、BLOB 名の先頭に "/" はありません。WASB からファイルにアクセスする場合は、ファイル名の先頭に "/" を追加する必要があります。
    -   **$srcDataFolder** と **$dstDataFolder**: = "tutorials/flightdelays/data"
         = "tutorials/flightdelays/output"

3.  次のコマンドを実行して、Azure サブスクリプションに接続します。

        Add-AzureAccount

4.  最初の 2 つの変数を設定し、次のコマンドを実行します。

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
    **変数名**

    </td>
    <td>
    **説明**

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
5.  次のコマンドを実行して、HiveQL ステートメントを定義します。

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

6.  次のコマンドを実行して、ワークステーションに Hive スクリプト ファイルを作成し、WASB にアップロードします。

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

## SQL データベースの準備

**SQL データベースを準備するには (Sqoop スクリプトとマージ)**

1.  PowerShell ISE を開きます。
2.  次のスクリプトをスクリプト ウィンドウにコピーして貼り付けます。

        [CmdletBinding()]
        Param(

            # SQL database server variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database Server Name to use an existing one. Enter nothing to create a new one.")]
            [AllowEmptyString()]
            [String]$sqlDatabaseServer,  # specify the Azure SQL database server name if you have one created. Otherwise use "".

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database admin user.")]
            [String]$sqlDatabaseUsername,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure SQL Database admin user password.")]
            [String]$sqlDatabasePassword,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the region to create the Database in.")]
            [String]$sqlDatabaseLocation,   #For example, West US.

            # SQL database variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the database name if you have created one. Enter nothing to create one.")]
            [AllowEmptyString()]
            [String]$sqlDatabaseName # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
        )

        #--------------------------------------------------------------------
        # Constants used in the script
        #--------------------------------------------------------------------
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

        #--------------------------------------------------------------------
        # Add the Azure account 
        #--------------------------------------------------------------------
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        Add-AzureAccount

        #--------------------------------------------------------------------
        # Create and validate Azure SQL Database server
        #--------------------------------------------------------------------
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

        #--------------------------------------------------------------------
        # Create and validate Azure SQL database
        #--------------------------------------------------------------------   
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

        #--------------------------------------------------------------------
        # Excute a SQL command to create the AvgDelays table
        #--------------------------------------------------------------------   
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

    > [WACOM.NOTE] このスクリプトは REST サービスの 1 つである http://bot.whatismyipaddress.com を使用して外部 IP アドレスを取得します。SQL Database サーバーのファイアウォール ルールを作成する際に、その IP アドレスを使用します。

    スクリプトには次の定数が使用されています。

    -   **$ipAddressRestService**: 既定値は <u>http://bot.whatismyipaddress.com</u>。外部 IP アドレスを取得するためのパブリック IP アドレス (Rest サービス) です。必要に応じて他のサービスを使用することもできます。このサービスを使用して取得した外部 IP アドレスは、Azure SQL データベース サーバーのファイアウォール ルールを作成する際に使用され、ご利用のワークステーションから (PowerShell スクリプトを使用して) データベースへのアクセスが許可されます。
    -   **$fireWallRuleName**: Azure SQL データベース サーバーのファイアウォール ルールの名前。既定の名前は <u>FlightDelay</u> です。この名前は必要に応じて変更できます。
    -   **$sqlDatabaseMaxSizeGB**: この値は、新しい Azure SQL データベース サーバーを作成するときにのみ使用されます。既定値は <u>10 GB</u> です。このチュートリアルにはこれで十分です。
    -   **$sqlDatabaseName**: この値は、新しい Azure SQL データベースを作成するときにのみ使用されます。既定値は <u>HDISqoop</u> です。この名前を変更した場合は、Sqoop PowerShell スクリプトにも反映する必要があります。

3.  **F5** キーを押して、スクリプトを実行します。Azure サブスクリプションの資格情報のほか、次の値を入力する必要があります。

    -   **sqlDatabaseServer**: Hive 出力のエクスポート先となる Azure SQL データベース サーバーの名前を入力します。新たに作成する場合は、何も入力しないでください。
    -   **sqlDatabaseUsername**: データベースのログイン アカウントを入力します。この指定は、データベース サーバーを新規作成する場合も、既存のデータベース サーバーを使用する場合も必要となります。
    -   **sqlDatabasePassword**: データベースのログイン パスワードを入力します。この指定は、データベース サーバーを新規作成する場合も、既存のデータベース サーバーを使用する場合も必要となります。
    -   **sqlDatabaseLocation**: リージョンを入力します。これは、新しいデータベース サーバーを作成するときにのみ使用されます。
    -   **sqlDatabaseName**: Azure SQL データベースの名前を入力します。新たに作成する場合は、何も入力しないでください。既定のデータベース名は **HDISqoop** です。

4.  スクリプトの出力結果を検証します。スクリプトが正常に実行されたことを確認してください。

ファイルのアップロード方法として別の方法を選択した場合も、ファイル パスは *tutorials/flightdelays/data* にしてください。ファイルにアクセスする構文は次のとおりです。

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*tutorials/flightdelays/data* は、ファイルのアップロード時に作成した仮想フォルダーです。月ごとに 1 つ、合計 12 個のファイルがあることを確認します。

  [HiveQL]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
  [HDI.FlightDelays.flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
  [前提条件]: #prerequisite
  [新規または既存の HDInsight クラスター上で Hive ジョブを実行する (M1)]: #runjob
  [Sqoop を使用して出力結果を Azure SQL Database にエクスポートする (M2)]: #exportdata
  [次のステップ]: #nextsteps
  [付録 A: Azure BLOB ストレージへのフライト遅延データのアップロード (A1)]: #appdendix-a
  [付録 B: HiveQL スクリプトの作成とアップロード (A2)]: #appendix-b
  [Azure PowerShell のインストールおよび構成に関するページ]: ../install-configure-powershell/
  [HDInsight での Azure BLOB ストレージの使用]: ../hdinsight-use-blob-storage/
  [HDInsight での Hadoop クラスターのプロビジョニング]: ../hdinsight-provision-clusters/
  [米国運輸省研究・革新技術庁 (RITA)/運輸統計局]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
  [付録]: #appendix
  [HDInsight: Hive Internal and External Tables Intro (HDInsight: Hive の内部テーブルと外部テーブルの概要)]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
  [HDInsight での Hive の使用]: ../hdinsight-use-hive/
  [HDI.FlightDelays.RunHiveJob.output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
  [HDI.FlightDelays.AvgDelays.Dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
  [Azure HDInsight の概要]: ../hdinsight-get-started/
  [Use Oozie with HDInsight (HDInsight での Oozie の使用)]: ../hdinsight-use-oozie/
  [HDInsight での Sqoop の使用]: ../hdinsight-use-sqoop/
  [HDInsight での Pig の使用]: ../hdinsight-use-pig/
  [Develop Java MapReduce programs for HDInsight (HDInsight 用 Java MapReduce プログラムの開発)]: ../hdinsight-develop-deploy-java-mapreduce/
  [Develop C\# Hadoop streaming programs for HDInsight (HDInsight 用 C\# Hadoop ストリーミング プログラムの開発)]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
