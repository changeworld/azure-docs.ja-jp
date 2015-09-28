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
   ms.date="09/02/2015"
   ms.author="sahajs;barbkess"/>


# PolyBase を使用したデータのロード
PolyBase テクノロジを使用して、複数のソースからデータをクエリして結合できます。これらはすべて Transact-SQL コマンドを使用して実行できます。

次の手順に従って、PolyBase を使用し、Azure BLOB ストレージに格納されたデータに対してクエリを実行して、SQL Data Warehouse のデータベースにロードできます。

- データベース マスター キーと資格情報を作成します。
- PolyBase オブジェクト、つまり、外部データ ソース、外部ファイル形式、および外部テーブルを作成します。 
- Azure BLOB ストレージに格納されたデータをクエリします。
- Azure BLOB ストレージから SQL Data Warehouse にデータをロードします。


## 前提条件
このチュートリアルを進めるには、次が必要です。

- Azure ストレージ アカウント
- 区切りテキスト ファイルとして Azure BLOB ストレージに格納されたデータ

まず、Azure BLOB ストレージ内のデータに接続し、クエリを実行するために PolyBase で必要なオブジェクトを作成します。

## データベース マスター キーの作成
サーバー上のユーザー データベースに接続し、データベース マスター キーを作成します。このキーは、次の手順で資格情報シークレットの暗号化に使用されます。

```
-- Creating master key
CREATE MASTER KEY;
```

リファレンス トピック: [CREATE MASTER KEY (Transact-SQL)][]。

## データベース スコープの資格情報の作成
Azure BLOB ストレージにアクセスするには、Azure ストレージ アカウントの認証情報を格納するデータベース スコープの資格情報を作成する必要があります。Data Warehouse データベースに接続し、アクセスする各 Azure ストレージ アカウントのデータベース スコープ資格情報を作成します。ユーザー名と Azure ストレージ アカウント キーをシークレットとして指定します。ユーザー名は、Azure Storage への認証には影響を及ぼしません。

データベース スコープの資格情報が既に存在するかどうかを確認するには、サーバーの資格情報のみを表示する sys.credentials ではなく sys.database\_credentials を使用します。

```
-- Check for existing database-scoped credentials.
SELECT * FROM sys.database_credentials;

-- Create a database scoped credential
CREATE DATABASE SCOPED CREDENTIAL ASBSecret 
WITH IDENTITY = 'joe'
,    Secret = '<azure_storage_account_key>'
;
```

リファレンス トピック: [CREATE CREDENTIAL (Transact-SQL)][]。

データベース スコープの資格情報を削除するには、単純に次の構文を使用します。

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret
;
```

リファレンス トピック: [DROP CREDENTIAL (Transact-SQL)][]。

## 外部データ ソースの作成
外部データ ソースは、Azure BLOB ストレージのデータとアクセス情報の場所を格納するデータベース オブジェクトです。アクセスする Azure Storage コンテナーごとに外部データ ソースを定義する必要があります。

```
-- Creating external data source (Azure Blob Storage) 
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH
(
    TYPE = HADOOP
,   LOCATION ='wasbs://mycontainer@test.blob.core.windows.net'
,   CREDENTIAL = ASBSecret
)
;
```

リファレンス トピック: [CREATE EXTERNAL DATA SOURCE (Transact-SQL)][]。

外部データ ソースを削除するための構文は次のとおりです。

```
-- Dropping external data source
DROP EXTERNAL DATA SOURCE azure_storage
;
```

リファレンス トピック: [DROP EXTERNAL DATA SOURCE (Transact-SQL)][]。

## 外部ファイル形式の作成
外部ファイル形式は、外部データの形式を指定するデータベース オブジェクトです。この例では、テキスト ファイル内のデータは圧縮せず、フィールドは、パイプ文字 ('|') で区切っています。

```
-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(   
    FORMAT_TYPE = DELIMITEDTEXT 
,	FORMAT_OPTIONS  (
                        FIELD_TERMINATOR ='|'
                    ,   USE_TYPE_DEFAULT = TRUE
                    )
)
;
```

PolyBase は、区切り文字、Hive RCFILE、および HIVE ORC 形式の圧縮データおよび非圧縮データを処理できます。

リファレンス トピック: [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][]。

外部ファイル形式を削除する構文は次のとおりです。

```
-- Dropping external file format
DROP EXTERNAL FILE FORMAT text_file_format
;
```
リファレンス トピック: [DROP EXTERNAL FILE FORMAT (Transact-SQL)][]。

## 外部テーブルの作成

外部テーブルの定義は、リレーショナル テーブルの定義と似ています。主な相違点は、データの場所と形式です。外部テーブルの定義は、SQL Data Warehouse データベースに格納されます。データは、データ ソースで指定された場所に格納されます。

LOCATION オプションで、データ ソースのルートからデータまでのパスが指定されます。この例では、データは 'wasbs://mycontainer@ test.blob.core.windows.net/path/Demo/' にあります。同じテーブルのすべてのファイルは、Azure BLOB の同じ論理フォルダーの下にある必要があります。

必要に応じて、PolyBase が外部データ ソースから受信したダーティ レコードをどのように処理するかを決定する拒否オプション (REJECT\_TYPE、REJECT\_VALUE、REJECT\_SAMPLE\_VALUE) も指定できます。

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] 
(
     [SensorKey]     int    NOT NULL 
,    [CustomerKey]   int    NOT NULL 
,    [GeographyKey]  int        NULL 
,    [Speed]         float  NOT NULL 
,    [YearMeasured]  int    NOT NULL
)
WITH 
(
    LOCATION    = '/Demo/'
,   DATA_SOURCE = azure_storage
,   FILE_FORMAT = text_file_format      
)
;
```

> [AZURE.NOTE]この時点では、外部テーブルで統計を作成することはできません。

リファレンス トピック: [CREATE EXTERNAL TABLE (Transact-SQL)][]。

作成したオブジェクトは、SQL Data Warehouse のデータベースに格納されます。SQL Server Data Tools (SSDT) のオブジェクト エクスプローラーでそれらを表示できます。

外部テーブルを削除するには、次の構文を使用します。

```
--Dropping external table
DROP EXTERNAL TABLE [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE]外部テーブルを削除するときは `DROP EXTERNAL TABLE` を使用する必要があります。`DROP TABLE` は**使用できません**。

リファレンス トピック: [DROP EXTERNAL TABLE (Transact-SQL)][].

外部テーブルは両方の `sys.tables`で表示できること、より具体的には `sys.external_tables` カタログ ビューに表示できることにも注意してください。

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

> [AZURE.NOTE]外部テーブルに対するクエリが、*"クエリは中止されました。外部ソースの読み取り中に最大拒否しきい値に達しました"* というエラーで失敗する場合があります。これは、外部データに*ダーティ*なレコードが含まれていることを示します。データ レコードは、列の実際のデータの種類/数値が外部テーブルの列定義と一致しない場合、またはデータが指定された外部ファイルの形式に従っていない場合に「ダーティ」であるとみなされます。これを修正するには、外部テーブルと外部ファイルの形式の定義が正しいこと、および外部データがこれらの定義に従っていることを確認します。外部データ レコードのサブセットがダーティである場合は、CREATE EXTERNAL TABLE DDL の中で拒否オプションを使用することで、クエリでこれらのレコードを拒否することを選択できます。


## Azure BLOB ストレージからのデータのロード
この例では、Azure BLOB ストレージから SQL Data Warehouse データベースにデータをロードします。

データを直接格納すると、クエリのデータ転送に要する時間が削減されます。columnstore インデックスを使用してデータを格納すると、分析クエリに対するクエリのパフォーマンスが最大で 10 倍改善されます。

この例では、CREATE TABLE AS SELECT ステートメントを使用してデータをロードします。新しいテーブルは、クエリで指定された列を継承します。これは、外部テーブル定義からそれらの列のデータ型を継承します。

CREATE TABLE AS SELECT は、INSERT...SELECT に取って代わる高パフォーマンス Transact-SQL ステートメントです。元来、これは Analytics Platform System で超並列処理 (MPP) エンジン用に開発されたものですが、現在は SQL Data Warehouse で使用されています。

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

<!---HONumber=Sept15_HO3-->