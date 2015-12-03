<properties
   pageTitle="SQL Data Warehouse の PolyBase チュートリアル | Microsoft Azure"
   description="データ ウェアハウジングのシナリオに沿って、PolyBase の概要と、その使用方法を学習します。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor="jrowlandjones"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="11/04/2015"
   ms.author="sahajs;barbkess"/>


# PolyBase を使用したデータのロード
PolyBase テクノロジを使用して、複数のソースからデータをクエリして結合できます。これらはすべて Transact-SQL コマンドを使用して実行できます。

次の手順に従って、PolyBase を使用し、Azure BLOB ストレージに格納されたデータに対してクエリを実行して、SQL Data Warehouse のデータベースにロードできます。

- データベース マスター キーと資格情報を作成します。
- PolyBase オブジェクト、つまり、外部データ ソース、外部ファイル形式、および外部テーブルを作成します。 
- Azure BLOB ストレージに格納されたデータをクエリします。
- Azure BLOB ストレージから SQL Data Warehouse にデータをロードします。
- SQL Data Warehouse から Azure BLOB ストレージにデータをエクスポートします。


## 前提条件
このチュートリアルを進めるには、次が必要です。

- Azure ストレージ アカウント
- 区切りテキスト ファイルとして Azure BLOB ストレージに格納されたデータ

まず、Azure BLOB ストレージ内のデータに接続し、クエリを実行するために PolyBase で必要なオブジェクトを作成します。

> [AZURE.IMPORTANT]PolyBase でサポートされている Azure ストレージ アカウントの種類は次のとおりです。
> 
> + Standard ローカル冗長ストレージ (Standard-LRS)
> + Standard 地理冗長ストレージ (Standard-GRS)
> + Standard 読み取りアクセス 地理冗長ストレージ (Standard-RAGRS)
>
> PolyBase では、Standard ゾーン冗長ストレージ (Standard-ZRS) および Premium ローカル冗長ストレージ (Premium-LRS) のアカウント種類はサポートされていません。新しい Azure ストレージ アカウントを作成する場合は、価格レベルから PolyBase でサポートされているアカウントの種類を選択していることを確認してください。

## 手順 1: データベースに資格情報を格納する
Azure BLOB ストレージにアクセスするには、Azure ストレージ アカウントの認証情報を格納するデータベース スコープの資格情報を作成する必要があります。次の手順を使用して、データベースで資格情報を格納します。　

1. SQL Data Warehouse データベースに接続します。
2. [CREATE MASTER KEY (Transact-SQL)][] を使用して、データベースのマスター キーを作成します。データベースに既にマスター キーがある場合は、マスター キーを余分に作成する必要はありません。このキーは、次の手順で資格情報 "シークレット" の暗号化に使用されます。

    ```
    -- Create a E master key
    CREATE MASTER KEY;
    ```

1. データベースの資格情報が既に存在するかどうかを確認します。これを行うには、サーバーの資格情報を表示するだけの sys.credentials ではなく、sys.database\_credentials システム ビューを使用します。

    ```
    -- Check for existing database-scoped credentials.
    SELECT * FROM sys.database_credentials;
    ```

3. [CREATE CREDENTIAL (Transact-SQL)][] を使用して、アクセスする Azure ストレージ アカウントごとにデータベース スコープの資格情報を作成します。次の例では、IDENTITY は資格情報を表すわかりやすい名前になっています。この名前は Azure ストレージへの認証には影響を及ぼしません。SECRET は Azure ストレージ アカウント キーです。

    -- データベース スコープの資格情報の作成 CREATE DATABASE SCOPED CREDENTIAL ASBSecret WITH IDENTITY = 'joe' , Secret = '<azure_storage_account_key>' ; ```

1. データベース スコープの資格情報を削除する必要がある場合は、[DROP CREDENTIAL (Transact-SQL)][] を使用します。

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret
;
```

## 手順 2: 外部データ ソースを作成する
外部データ ソースは、Azure BLOB ストレージのデータとアクセス情報の場所を格納するデータベース オブジェクトです。[CREATE EXTERNAL DATA SOURCE (Transact-SQL)][] を使用して、アクセスする Azure ストレージ BLOB ごとに外部データ ソースを定義します。

    ```
    -- Create an external data source for an Azure storage blob
    CREATE EXTERNAL DATA SOURCE azure_storage 
    WITH
    (
        TYPE = HADOOP,
        LOCATION ='wasbs://mycontainer@test.blob.core.windows.net',
        CREDENTIAL = ASBSecret
    )
    ;
    ```

外部テーブルを削除する必要がある場合は、[DROP EXTERNAL DATA SOURCE][] を使用します。

    ```
    -- Drop an external data source
    DROP EXTERNAL DATA SOURCE azure_storage
    ;
    ```

## 手順 3: 外部ファイル形式を作成する
外部ファイル形式は、外部データの形式を指定するデータベース オブジェクトです。PolyBase は、区切り文字、Hive RCFILE、および HIVE ORC 形式の圧縮データおよび非圧縮データを処理できます。

[CREATE EXTERNAL FILE FORMAT (Transact-SQL)][] を使用して、外部ファイル形式を作成します。次の例では、ファイル内のデータは圧縮されていないテキストとし、フィールドはパイプ文字 ('|') で区切ることが規定されています。

```
-- Create an external file format for a text-delimited file.
-- Data is uncompressed and fields are separated with the
-- pipe character.
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(   
    FORMAT_TYPE = DELIMITEDTEXT, 
    FORMAT_OPTIONS  
    (
        FIELD_TERMINATOR ='|',
        USE_TYPE_DEFAULT = TRUE
    )
)
;
```

外部ファイル形式を削除する必要がある場合は、[DROP EXTERNAL FILE FORMAT] を使用します。

```
-- Dropping external file format
DROP EXTERNAL FILE FORMAT text_file_format
;
```

## 外部テーブルの作成

外部テーブルの定義は、リレーショナル テーブルの定義と似ています。主な相違点は、データの場所と形式です。

- 外部テーブルの定義は、メタデータとして SQL Data Warehouse データベースに格納されます。 
- データは、データ ソースで指定された外部の場所に格納されます。

[CREATE EXTERNAL TABLE (Transact-SQL)][] を使用して、外部テーブルを定義します。

LOCATION オプションで、データ ソースのルートからデータまでのパスが指定されます。この例では、データは 'wasbs://mycontainer@test.blob.core.windows.net/path/Demo/' にあります。同じテーブルのすべてのファイルは、Azure BLOB ストレージの同じ論理フォルダーの下にある必要があります。

必要に応じて、PolyBase が外部データ ソースから受信したダーティ レコードをどのように処理するかを決定する拒否オプション (REJECT\_TYPE、REJECT\_VALUE、REJECT\_SAMPLE\_VALUE) も指定できます。

```
-- Creating an external table for data in Azure blob storage.
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] 
(
     [SensorKey]     int    NOT NULL,
     [CustomerKey]   int    NOT NULL,
     [GeographyKey]  int        NULL,
     [Speed]         float  NOT NULL,
     [YearMeasured]  int    NOT NULL,
)
WITH 
(
    LOCATION    = '/Demo/',
    DATA_SOURCE = azure_storage,
    FILE_FORMAT = text_file_format      
)
;
```

作成したオブジェクトは、SQL Data Warehouse のデータベースに格納されます。SQL Server Data Tools (SSDT) のオブジェクト エクスプローラーでそれらを表示できます。

外部テーブルを削除するには、次の構文を使用します。

```
--Dropping external table
DROP EXTERNAL TABLE [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE]外部テーブルを削除するときは、`DROP EXTERNAL TABLE` を使用する必要があります。`DROP TABLE` は使用**できません**。

リファレンス トピック: [DROP EXTERNAL TABLE (Transact-SQL)][]。

外部テーブルは両方の `sys.tables` で表示できること、より具体的には `sys.external_tables` カタログ ビューに表示できることにも注意してください。

## ストレージ キーの交換

セキュリティ上の理由で、BLOB ストレージへのアクセス キーの交換を検討する必要がある場合があります。

このタスクを実行する最もスマートな方法として、"キーの交換" と呼ばれるプロセスがあります。BLOB ストレージ アカウントには 2 つのストレージ キーがあります。これらを切り替えることができます。

Azure ストレージ アカウント キーの交換は、次のシンプルな 3 つの手順から成るプロセスです

1. セカンダリ ストレージ アクセス キーに基づいたセカンダリ データベース スコープの資格情報を作成します
2. この新しい資格情報に基づいて 2 つ目の外部データ ソースを作成します
3. 外部データ ソースを参照する外部テーブルを削除して、新しい外部テーブルを作成します

すべての外部テーブルを新しい外部データ ソースに移行したら、クリーンアップ作業を実行できます。
 
1. 1 つ目の外部データ ソースを削除する
2. プライマリ ストレージ アクセス キーに基づいた 1 つ目のデータベース スコープ資格情報を削除します
3. Azure にログインし、次回に備えてプライマリ アクセス キーを再生成します

## Azure BLOB ストレージ データのクエリ
外部テーブルに対するクエリでは、リレーショナル テーブルであるかのように、単にテーブル名が使用されます。


```

-- Query Azure storage resident data via external table. 
SELECT * FROM [ext].[CarSensor_Data]
;

```

> [AZURE.NOTE]外部テーブルに対するクエリが、*"クエリは中止されました。外部ソースの読み取り中に最大拒否しきい値に達しました"* というエラーで失敗する場合があります。これは、外部データに*ダーティ*なレコードが含まれていることを示します。データ レコードは、列の実際のデータの種類/数値が外部テーブルの列定義と一致しない場合、またはデータが指定された外部ファイルの形式に従っていない場合に「ダーティ」であると見なされます。これを修正するには、外部テーブルと外部ファイルの形式の定義が正しいこと、および外部データがこれらの定義に従っていることを確認します。外部データ レコードのサブセットがダーティである場合は、CREATE EXTERNAL TABLE DDL の中で拒否オプションを使用することで、クエリでこれらのレコードを拒否することを選択できます。


## Azure BLOB ストレージからのデータのロード
この例では、Azure BLOB ストレージから SQL Data Warehouse データベースにデータをロードします。

データを直接格納すると、クエリのデータ転送に要する時間が削減されます。columnstore インデックスを使用してデータを格納すると、分析クエリに対するクエリのパフォーマンスが最大で 10 倍改善されます。

この例では、CREATE TABLE AS SELECT ステートメントを使用してデータをロードします。新しいテーブルは、クエリで指定された列を継承します。これは、外部テーブル定義からそれらの列のデータ型を継承します。

CREATE TABLE AS SELECT は、SQL Data Warehouse のすべてのコンピューティング ノードにデータを同時に読み込む高パフォーマンス Transact-SQL ステートメントです。元来、これは Analytics Platform System で超並列処理 (MPP) エンジン用に開発されたものですが、現在は SQL Data Warehouse で使用されています。

```
-- Load data from Azure blob storage to SQL Data Warehouse 

CREATE TABLE [dbo].[Customer_Speed]
WITH 
(   
    CLUSTERED COLUMNSTORE INDEX
,	DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS 
SELECT * 
FROM   [ext].[CarSensor_Data]
;
```

「[CREATE TABLE AS SELECT (Transact-SQL)][]」を参照してください。

## 新しくロードしたデータの統計を作成する

Azure SQL Data Warehouse は、統計の自動作成または自動更新をまだサポートしていません。クエリから最高のパフォーマンスを取得するには、最初の読み込み後またはそれ以降のデータの変更後に、すべてのテーブルのすべての列で統計を作成することが重要です。統計の詳細については、開発トピック グループの「[統計][]」トピックを参照してください。この例でロードしたテーブルの統計を作成する方法の簡単な例を次に示します。

```
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## Azure BLOB ストレージにデータをエクスポートする
このセクションでは、SQL Data Warehouse から Azure BLOB ストレージにデータをエクスポートする方法を示します。この例では、すべてのコンピューティング ノードからデータを同時にエクスポートする高パフォーマンス Transact-SQL ステートメントである CREATE EXTERNAL TABLE AS SELECT を使用します。

次の例では、dbo.Weblogs テーブルの列の定義とデータを使用して、外部テーブル Weblogs2014 を作成します。外部テーブルの定義は、SQL Data Warehouse に格納されます。また、SELECT ステートメントの結果は、データ ソースで指定された BLOB コンテナーにある "/archive/log2014/" ディレクトリにエクスポートされます。データは、指定されたテキスト ファイル形式でエクスポートされます。

```
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```


## PolyBase UTF-8 要件に対処する
現在、PolyBase では、UTF-8 でエンコードされたデータ ファイルの読み込みをサポートしています。UTF-8 では ASCII と同じ文字エンコードを使用するため、PolyBase でも ASCII でエンコードされたデータの読み込みがサポートされています。ただし、PolyBase では、UTF-16 / Unicode または拡張 ASCII 文字などの文字エンコードはサポートされていません。拡張 ASCII には、ドイツ語によく見られるウムラウトなどのアクセント付きの文字が含まれています。

この要件に対処するための最善の方法は、UTF-8 エンコードを書き換えることです。

これを行うには、いくつかの方法があります。Powershell を使用した 2 つの方法を以下に紹介します。

### 小さいファイル向けの単純な例

以下は、ファイルを作成する単純な 1 行の Powershell スクリプトです。
 
```
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

これは、データを再エンコードする簡単な方法ですが、最も効率的であるとは言えません。以下の IO ストリーミングの例では、これよりも格段に速く同じ結果を得られます。

### 大きいファイル向けの IO ストリーミングの例

以下のコード サンプルはやや複雑ですが、ソースからターゲットにデータ行をストリームするため、はるかに効率的です。この方法は、大規模なファイル向けです。

```
#Static variables
$ascii = [System.Text.Encoding]::ASCII
$utf16le = [System.Text.Encoding]::Unicode
$utf8 = [System.Text.Encoding]::UTF8
$ansi = [System.Text.Encoding]::Default
$append = $False

#Set source file path and file name
$src = [System.IO.Path]::Combine("C:\input_file_path","input_file_name.txt")

#Set source file encoding (using list above)
$src_enc = $ansi

#Set target file path and file name
$tgt = [System.IO.Path]::Combine("C:\output_file_path","output_file_name.txt")

#Set target file encoding (using list above)
$tgt_enc = $utf8

$read = New-Object System.IO.StreamReader($src,$src_enc)
$write = New-Object System.IO.StreamWriter($tgt,$append,$tgt_enc)

while ($read.Peek() -ne -1)
{
    $line = $read.ReadLine();
    $write.WriteLine($line);
}
$read.Close()
$read.Dispose()
$write.Close()
$write.Dispose()
```

## 次のステップ
開発のその他のヒントについては、[開発の概要][]に関するページをご覧ください。

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Load with PolyBase]: sql-data-warehouse-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[開発の概要]: sql-data-warehouse-overview-develop.md
[統計]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!-- External Links -->
[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/ja-JP/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/ja-JP/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/ja-JP/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/ja-JP/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/ja-JP/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/ja-JP/library/ms189450.aspx

<!----HONumber=Nov15_HO3-->