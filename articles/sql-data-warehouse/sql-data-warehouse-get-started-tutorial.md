---
title: "Azure SQL Data Warehouse - 入門チュートリアル | Microsoft Docs"
description: "Azure SQL Data Warehouse の入門チュートリアルです。"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: barbkess
ms.assetid: 52DFC191-E094-4B04-893F-B64D5828A900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 12/21/2016
ms.author: elbutter
translationtype: Human Translation
ms.sourcegitcommit: 5bb75bf36892c737568b8129b30bb30b02d01bf2
ms.openlocfilehash: 1227903652a944d9d144917922fe36a4f149f820


---
# <a name="get-started-with-sql-data-warehouse"></a>SQL Data Warehouse を使ってみる

Azure SQL Data Warehouse の入門チュートリアルです。 このチュートリアルでは、SQL Data Warehouse のプロビジョニングとデータの読み込みの基本に加え、スケーリング、一時停止、チューニングの基本について説明します。 

**推定所要時間:** 75 分

## <a name="prerequisites"></a>前提条件


### <a name="sign-up-for-microsoft-azure"></a>Microsoft Azure にサインアップ
まだ Microsoft Azure アカウントを持っていない場合、このサービスを使用するには、サインアップする必要があります。 既にアカウントを持っている場合は、この手順をスキップできます。 

1. アカウントのページ ([https://azure.microsoft.com/account/](https://azure.microsoft.com/account/)) に移動します。
2. 無料の Azure アカウントを作成するか、アカウントを購入します。
3. 手順に従います。

### <a name="install-appropriate-sql-client-driver-and-tools"></a>適切な SQL クライアント ドライバーとツールのインストール

ほとんどの SQL クライアント ツールは、JDBC、ODBC、または ADO.net を使用して Azure SQL Data Warehouse に接続できます。 製品が複雑で、SQL Data Warehouse でサポートされている T-SQL 機能が多いため、すべてのクライアント アプリケーションに SQL Data Warehouse との完全な互換性があるわけではありません。

Windows オペレーティング システムを実行している場合は、[Visual Studio] または [SQL Server Management Studio] を使用することをお勧めします。


[!INCLUDE [Create a new logical server](../../includes/sql-data-warehouse-create-logical-server.md)] 

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="create-an-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse の作成

> [!NOTE]
> SQL Data Warehouse を作成すると、新しい課金対象サービスを使用することになる場合があります。  詳細については、「[SQL Data Warehouse の価格](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)」を参照してください。
>


### <a name="create-a-sql-data-warehouse"></a>SQL Data Warehouse の作成
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **[新規]** > **[Databases]** > **[SQL Data Warehouse]** の順にクリックします。

    ![NewBlade](../../includes/media/sql-data-warehouse-create-dw/blade-click-new.png)
    ![SelectDW](../../includes/media/sql-data-warehouse-create-dw/blade-select-dw.png)

3. デプロイの詳細を入力します。

    **[データベース名]**: 任意の名前を付けます。 複数の SQL DW インスタンスがある場合は、リージョン、環境などの詳細を含む名前をお勧めします (例: *mydw-westus-1-test*)。

    **[サブスクリプション]**: Azure のサブスクリプション。

    **[リソース グループ]**: 新規作成します (Azure SQL Data Warehouse を他のサービスと共に使用する予定の場合は、既存のものを使用します)。
    > [!NOTE]
    > リソース グループ内のサービスには、同じライフサイクルが設定されている必要があります。 リソース グループは、アクセス制御のスコープ設定やテンプレート化されたデプロイなどのリソース管理に役立ちます。 Azure リソース グループの詳細とベスト プラクティスについては、[こちら](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups)を参照してください。
    >

    **[ソース]**: 空のデータベース。

    **[サーバー]**: 「[前提条件]」で作成したサーバーを選択します。

    **[照合順序]**: 既定の照合順序である SQL_Latin1_General_CP1_CI_AS のままにしておきます。

    **[Select performance (パフォーマンスの選択)]**: 標準の 400DWU のまま使用することをお勧めします。

4. **[ダッシュボードにピン留めする]** チェック ボックスをオンにします。
    ![ダッシュボードにピン留めする](./media/sql-data-warehouse-get-started-tutorial/pin-to-dashboard.png)

5. Azure SQL Data Warehouse がデプロイされるまでしばらく待ちます。 通常、この処理には数分かかります。 インスタンスのデプロイが完了すると、ポータルによって通知されます。 

## <a name="connect-to-azure-sql-data-warehouse-through-sql-server-logical-server"></a>SQL サーバー (論理サーバー) を経由した Azure SQL Data Warehouse への接続

このチュートリアルでは、SQL Server Management Studio を使用して SQL Data Warehouse に接続します。 他のツールを使用して、サポートされているコネクタ (ADO.NET、JDBC、ODBC、および PHP) を経由した SQL Data Warehouse への接続も可能です。 Microsoft がサポートしていないツールでは、機能が限定される場合があることに注意してください。


### <a name="get-connection-information"></a>接続情報の取得

SQL Data Warehouse に接続するには、「[前提条件]」で作成した SQL サーバー (論理サーバー) を通じて接続する必要があります。

1. ダッシュボードで SQL Data Warehouse を選択するか、リソース内で検索します。

    ![SQL Data Warehouse ダッシュボード](./media/sql-data-warehouse-get-started-tutorial/sql-dw-dashboard.png)

2. 論理サーバーの完全な名前を検索します。

    ![サーバー名を選択する](./media/sql-data-warehouse-get-started-tutorial/select-server.png)

3. SSMS を開き、オブジェクト エクスプローラーで、「[前提条件]」で作成した資格情報を使用して、このサーバーに接続します。

    ![SSMS で接続する](./media/sql-data-warehouse-get-started-tutorial/ssms-connect.png)

すべてが正しく設定されていれば、この時点で SQL サーバー (論理サーバー) インスタンスに接続されるはずです。 サーバー資格情報を使用すると、データベース所有者としてサーバー上の任意のデータベースに対して認証することができます。 ただし、ベスト プラクティスとしては、データベースごとに異なるログインとユーザーを作成する必要があります。 ユーザーの作成については、「[SQL Data Warehouse のユーザーの作成](./sql-data-warehouse-get-started-tutorial.md#create-a-user-for-sql-data-warehouse)」で説明します。 

## <a name="create-a-user-for-sql-data-warehouse"></a>SQL Data Warehouse のユーザーの作成

### <a name="why-create-a-separate-user"></a>個別のユーザーを作成する理由

SQL Data Warehouse の新しいユーザーを作成するには、SQL サーバー (論理サーバー) への接続と、前の手順のサーバー資格情報を使用します。 SQL DW に個別のユーザーとログインを作成する理由は、主に 2 つあります。

1.  組織のユーザーは別のアカウントを使用して認証する必要があります。 そうすることで、アプリケーションに付与されるアクセス許可を制限し、悪意のあるアクティビティのリスクを軽減することができます。

2. 既定では、現在接続しているサーバー管理者ログインは、小さいリソース クラスを使用します。 リソース クラスにより、クエリに対するメモリ割り当てと CPU サイクルの制御が行われます。 **smallrc** のユーザーにはより少ないメモリが割り当てられ、その結果、より多くの同時実行が可能になります。 これに対し、**xlargerc** に割り当てられたユーザーには多くのメモリが与えられるため、同時実行が許可されるクエリの数は少なくなります。 圧縮が最適化される方法でデータを読み込むには、データを読み込むユーザーが大きなリソース クラスに属しているようにします。 リソース クラスの詳細については、[こちら](./sql-data-warehouse-develop-concurrency.md#resource-classes)を参照してください。

### <a name="creating-a-user-of-a-larger-resource-class"></a>大きなリソース クラスのユーザーの作成

1. サーバーの **master** データベースに対する新しいクエリを作成します。

    ![Master に対する新しいクエリ](./media/sql-data-warehouse-get-started-tutorial/query-on-server.png)

    ![Master1 に対する新しいクエリ](./media/sql-data-warehouse-get-started-tutorial/query-on-master.png)

2. サーバー ログインとユーザーを作成します。

    ```sql
    CREATE LOGIN XLRCLOGIN WITH PASSWORD = 'a123reallySTRONGpassword!';
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

3. サーバー ログインに基づいて新しいデータベース ユーザーを作成します。
    ```sql
    CREATE USER LoadingUser FOR LOGIN XLRCLOGIN;
    ```

4. ユーザーに DB 制御を許可します。
    ```sql
    GRANT CONTROL ON DATABASE::[NYT] to LoadingUser;
    ```
    > [!NOTE]
    > データベース名にハイフンが含まれる場合は、その名前を角かっこで囲んでください。 
    >

5. データベース ユーザーを **xlargerc** リソース クラス ロールに追加します。
    ```sql
    EXEC sp_addrolememeber 'xlargerc', 'LoadingUser';
    ```

6. 新しい資格情報を使用してデータベースにログインします。

    ![新しいログインでログインする](./media/sql-data-warehouse-get-started-tutorial/new-login.png)


## <a name="loading-data"></a>データの読み込み

### <a name="defining-external-data"></a>外部データの定義
1. 外部データ ソースを定義します。

    ```sql
    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
    TYPE = Hadoop
    , LOCATION = 'wasbs://2013@nytpublic.blob.core.windows.net/'
    );
    ```


2. 外部ファイル形式を定義します。

    ```CREATE EXTERNAL FILE FORMAT``` コマンドを使用して、読み込み元の外部データの形式を指定します。 ニューヨークの公共タクシーのデータについては、Azure Blob Storage にデータを格納する際に 2 つの形式を使用しました。

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = ','
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    );

    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH
    ( FORMAT_TYPE = DELIMITEDTEXT
    , FORMAT_OPTIONS ( FIELD_TERMINATOR = '|'
    , STRING_DELIMITER = ''
    , DATE_FORMAT = ''
    , USE_TYPE_DEFAULT = False
    )
    , DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

3.  外部ファイル形式のスキーマを作成します。

    ```sql
    CREATE SCHEMA ext;
    GO
    ```

4. 外部テーブルを作成する これらのテーブルは、HDFS または Azure Blob Storage に格納されたデータを参照します。 

    ```sql
    CREATE EXTERNAL TABLE [ext].[Date] 
    (
    [DateID] int NOT NULL,
    [Date] datetime NULL,
    [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FirstDayOfMonth] date NULL,
    [LastDayOfMonth] date NULL,
    [FirstDayOfQuarter] date NULL,
    [LastDayOfQuarter] date NULL,
    [FirstDayOfYear] date NULL,
    [LastDayOfYear] date NULL,
    [IsHolidayUSA] bit NULL,
    [IsWeekday] bit NULL,
    [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Date'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    CREATE EXTERNAL TABLE [ext].[Geography]
    (
    [GeographyID] int NOT NULL,
    [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Geography'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0 
    )
    ;
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
    [HackneyLicenseID] int NOT NULL,
    [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'HackneyLicense'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Medallion]
    (
    [MedallionID] int NOT NULL,
    [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
    LOCATION = 'Medallion'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Time]
    (
    [TimeID] int NOT NULL,
    [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [HourNumber] tinyint NOT NULL,
    [MinuteNumber] tinyint NOT NULL,
    [SecondNumber] tinyint NOT NULL,
    [TimeInSecond] int NOT NULL,
    [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
    [DayTimeBucketGroupKey] int NOT NULL,
    [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
    LOCATION = 'Time'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Trip]
    (
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
    )
    WITH
    (
    LOCATION = 'Trip2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = compressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
    [DateID] int NOT NULL,
    [GeographyID] int NOT NULL,
    [PrecipitationInches] float NOT NULL,
    [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
    LOCATION = 'Weather2013'
    , DATA_SOURCE = NYTPublic
    , FILE_FORMAT = uncompressedcsv
    , REJECT_TYPE = value
    , REJECT_VALUE = 0
    )
    ;
    ```

### <a name="create-table-as-select-ctas"></a>Create Table as Select (CTAS)

5. 外部テーブルから SQL Data Warehouse インスタンスにデータを読み込みます。 
    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    CREATE TABLE [dbo].[Geography]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    CREATE TABLE [dbo].[Medallion]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    CREATE TABLE [dbo].[Time]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    CREATE TABLE [dbo].[Weather]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    CREATE TABLE [dbo].[Trip]
    WITH
    ( DISTRIBUTION = ROUND_ROBIN
    , CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT *
    FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

    > [!NOTE]
    > 数 GB のデータを読み込み、高パフォーマンスのクラスター化列ストア インデックスに圧縮しています。 以下の DMV クエリを実行した後、Azure SQL Data Warehouse によって負荷のかかる処理が行われている間に休憩してください。
    >

6. 新しいクエリを作成し、データがこの動的管理ビュー (DMV) で表示されることを確認します。

    ```sql
    SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024 as gb_processed
    FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
    on r.request_id = s.request_id
    WHERE
    r.[label] = 'CTAS : Load [dbo].[Date]' OR
    r.[label] = 'CTAS : Load [dbo].[Geography]' OR
    r.[label] = 'CTAS : Load [dbo].[HackneyLicense]' OR
    r.[label] = 'CTAS : Load [dbo].[Medallion]' OR
    r.[label] = 'CTAS : Load [dbo].[Time]' OR
    r.[label] = 'CTAS : Load [dbo].[Weather]' OR
    r.[label] = 'CTAS : Load [dbo].[Trip]'
    GROUP BY
    r.command,
    s.request_id,
    r.status
    ORDER BY
    nbr_files desc, gb_processed desc;
    ```

7. すべてのシステム クエリを表示します。

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

8. Azure SQL Data Warehouse にデータが適切に読み込まれたことを確認します。

    ![データが読み込まれたことを確認する](./media/sql-data-warehouse-get-started-tutorial/see-data-loaded.png)



## <a name="querying-data"></a>データのクエリ実行 

### <a name="scan-query-with-scaling"></a>スケーリングによるクエリのスキャン

スケーリングがクエリの速度にどのように影響するかを調べてみましょう。

その前に、1 つのコンピューティング ノード単独のパフォーマンスがわかるように、操作を 100 DWU にスケールダウンします。

1. ポータルに移動し、SQL Data Warehouse インスタンスを選択します。

2. SQL Data Warehouse ブレードで、スケールを選択します。 

    ![ポータルから DW をスケーリングする](./media/sql-data-warehouse-get-started-tutorial/scale-dw.png)

3. パフォーマンス バーを 100 DWU にスケールダウンし、保存します。

    ![スケーリングして保存する](./media/sql-data-warehouse-get-started-tutorial/scale-and-save.png)

4. スケーリング操作が完了するまで待ちます。

    > [!NOTE]
    > スケーリング操作を行うと、現在実行中のクエリが**中止**され、新しいクエリを実行できなくなることに注意してください。
    >
    
5. すべての列の上位 100 万エントリを選択して、乗車データに対するスキャン操作を実行します。 早く先へ進みたい場合は、選択する行を少なくしてもかまいません。

    ```sql
    SELECT TOP(1000000) * FROM dbo.[Trip]
    ```

この操作の実行にかかった時間を書き留めておきます。

6. インスタンスを 400 DWU にスケールアップします。 100 DWU ごとに、1 つのコンピューティング ノードが Azure SQL Data Warehouse に追加されることに注意してください。

7. もう一度クエリを実行します。 大きな違いに気付くはずです。 

> [!NOTE]
> Azure SQL Data Warehouse は、大量並列処理 (MPP) プラットフォームです。 さまざまなノード間で作業を並列処理が可能なクエリや操作では、Azure SQL Data Warehouse の真価を感じることができます。
>

### <a name="join-query-with-statistics"></a>統計を使用した結合クエリ

1. Date テーブルと Trip テーブルを結合するクエリを実行します。

    ```sql
    SELECT TOP (1000000) dt.[DayOfWeek]
    ,tr.[MedallionID]
    ,tr.[HackneyLicenseID]
    ,tr.[PickupTimeID]
    ,tr.[DropoffTimeID]
    ,tr.[PickupGeographyID]
    ,tr.[DropoffGeographyID]
    ,tr.[PickupLatitude]
    ,tr.[PickupLongitude]
    ,tr.[PickupLatLong]
    ,tr.[DropoffLatitude]
    ,tr.[DropoffLongitude]
    ,tr.[DropoffLatLong]
    ,tr.[PassengerCount]
    ,tr.[TripDurationSeconds]
    ,tr.[TripDistanceMiles]
    ,tr.[PaymentType]
    ,tr.[FareAmount]
    ,tr.[SurchargeAmount]
    ,tr.[TaxAmount]
    ,tr.[TipAmount]
    ,tr.[TollsAmount]
    ,tr.[TotalAmount]
    FROM [dbo].[Trip] as tr
    join
    dbo.[Date] as dt
    on tr.DateID = dt.DateID
    ```

    予想どおり、ノード間でデータをシャッフルするとき (特にこのような結合シナリオの場合) に、クエリの実行時間が長くなります。

2. 次のステートメントを実行して、結合している列に関する統計を作成する場合にどのように変わるかを見てみましょう。

    ```sql
    CREATE STATISTICS [dbo.Date DateID stats] ON dbo.Date (DateID);
    CREATE STATISTICS [dbo.Trip DateID stats] ON dbo.Trip (DateID);
    ```

    > [!NOTE]
    > SQL DW では、統計は自動的には管理されません。 統計はクエリのパフォーマンスにとって重要なため、統計を作成および更新することを強くお勧めします。
    > 
    > **結合に含まれる列、WHERE 句で使用されている列、および GROUP BY に含まれている列に関する統計を作成すると、最も大きなメリットが得られます。**
    >

3. 前提条件のクエリを再実行し、パフォーマンスの違いを観察します。 クエリのパフォーマンスの違いは、スケールアップの場合ほど劇的ではありませんが、高速化したことがはっきりわかります。 

## <a name="next-steps"></a>次のステップ

これで、クエリを実行して探索する準備ができました。 ベスト プラクティスやヒントを確認してください。

一日の作業を終了した場合は、インスタンスを一時停止するようにしてください。 運用環境では、一時停止とビジネス ニーズに合わせたスケーリングによって、さまざまな節約が可能になります。

![一時停止](./media/sql-data-warehouse-get-started-tutorial/pause.png)

## <a name="useful-readings"></a>有益な参考情報

[同時実行とワークロード管理]

[Azure SQL Data Warehouse のベスト プラクティス]

[クエリ監視]

[大規模なリレーショナル データ ウェアハウスを構築するためのベストプラクティスのトップ 10]

[Azure SQL Data Warehouse へのデータの移行]


[同時実行とワークロード管理]: sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example
[Azure SQL Data Warehouse のベスト プラクティス]: sql-data-warehouse-best-practices.md#hash-distribute-large-tables
[クエリ監視]: sql-data-warehouse-manage-monitor.md
[大規模なリレーショナル データ ウェアハウスを構築するためのベストプラクティスのトップ 10]: https://blogs.msdn.microsoft.com/sqlcat/2013/09/16/top-10-best-practices-for-building-a-large-scale-relational-data-warehouse/
[Azure SQL Data Warehouse へのデータの移行]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/



[!INCLUDE [Additional Resources](../../includes/sql-data-warehouse-article-footer.md)]

<!-- Internal Links -->
[前提条件]: sql-data-warehouse-get-started-tutorial.md#prerequisites

<!--Other Web references-->
[Visual Studio]: https://www.visualstudio.com/
[SQL Server Management Studio]: https://msdn.microsoft.com/en-us/library/mt238290.aspx



<!--HONumber=Jan17_HO2-->


