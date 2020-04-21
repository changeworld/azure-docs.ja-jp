---
title: SQL オンデマンド (プレビュー) で OPENROWSET を使用する方法
description: この記事では、SQL オンデマンド (プレビュー) での OPENROWSET の構文と、引数の使用方法について説明します。
services: synapse-analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 25eb93a01c59225b6d9e64db5d08b954adb4f8ab
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420106"
---
# <a name="how-to-use-openrowset-with-sql-on-demand-preview"></a>SQL オンデマンド (プレビュー) で OPENROWSET を使用する方法

OPENROWSET(BULK...) 関数を使用すると、Azure Storage 内のファイルにアクセスできます。 SQL オンデマンド (プレビュー) リソース内では、OPENROWSET 関数を呼び出し、BULK オプションを指定することによって、OPENROWSET BULK 行セット プロバイダーにアクセスします。  

OPENROWSET 関数は、テーブル名 OPENROWSET と同じように、クエリの FROM 句で参照できます。 組み込みの BULK プロバイダーによる一括操作がサポートされ、ファイルのデータを行セットとして読み取り、返すことができます。

OPENROWSET は、現在 SQL プールではサポートされていません。

## <a name="syntax"></a>構文

```
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT='PARQUET' }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT = 'CSV'
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char' ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = 'first_row'  ]     
[ , FIELDQUOTE = 'quote_characters']
```

## <a name="arguments"></a>引数

クエリの対象データを含む入力ファイルには、2 つの選択肢があります。 有効な値は次のとおりです。

- 'CSV' - 行と列の区切り文字を含む区切りテキスト ファイルが含まれます。 任意の文字をフィールド区切り文字として使用できます。例: TSV:FIELDTERMINATOR = tab。

- 'PARQUET' - Parquet 形式のバイナリ ファイル 

'unstructured_data_path'

データへのパスを確立する unstructured_data_path は、次のように構成されます: '<prefix>://<storage_account_path>/<storage_path>' 
 
 
 以下に、特定の外部データ ソースにリンクする、関連するストレージ アカウント パスを示します。 

| 外部データ ソース       | Prefix | ストレージ アカウント パス                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Store Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | https  | <storage_account>.dfs.core.windows.net              |

 '<storage_path>' 

 読み取り対象のフォルダーまたはファイルを指す、ストレージ内のパスを指定します。 パスがコンテナーまたはフォルダーを指している場合は、その特定のコンテナーまたはフォルダーからすべてのファイルが読み取られます。 サブフォルダー内のファイルは含まれません。 
 
 ワイルドカードを使用して、複数のファイルまたはフォルダーを対象にすることができます。 連続しない複数のワイルドカードを使用できます。
次に示すのは、 */csv/population* で始まるすべてのフォルダーから、*population* で始まるすべての *csv* ファイルを読み取る例です:  'https://sqlondemandstorage.blob.core.windows.net/csv/population */population*.csv'

unstructured_data_path でフォルダーを指定すると、SQL オンデマンド クエリはそのフォルダーからファイルを取得します。 

> [!NOTE]
> Hadoop や PolyBase とは異なり、SQL オンデマンドではサブフォルダーは返されません。 また、Hadoop や PloyBase とは異なり、SQL オンデマンドでは、ファイル名が下線 (_) やピリオド (.) で始まるファイルが返されます。

次の例で、unstructured_data_path='https://mystorageaccount.dfs.core.windows.net/webdata/ ' の場合、SQL オンデマンド クエリでは、mydata.txt と _hidden.txt からの行が返されます。 mydata2.txt と mydata3.txt はサブフォルダー内にあるため、これらは返されません。

![外部テーブルの再帰型データ](./media/develop-openrowset/folder-traversal.png)

[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]

WITH 句を使用すると、ファイルから読み取る列を指定できます。

- CSV データ ファイルの場合、すべての列を読み取るには、列名とそのデータ型を指定します。 列のサブセットが必要な場合は、序数を使用して、元のデータ ファイルから序数で列を選択します。 列は、序数の指定によってバインドされます。 

> [!IMPORTANT]
> CSV ファイルの場合、WITH 句は必須です。
- Parquet データ ファイルの場合は、元のデータ ファイル内の列名と一致する列名を指定します。 列は名前によってバインドされます。 WITH 句を省略すると、Parquet ファイルのすべての列が返されます。

column_name = 出力列の名前。 指定した場合、この名前はソース ファイル内の列名をオーバーライドします。

column_type = 出力列のデータ型。 ここでは、暗黙的なデータ型の変換が行われます。

column_ordinal = ソース ファイル内の列の序数。 Parquet ファイルの場合、バインドは名前で行われるため、この引数は無視されます。 次の例では、CSV ファイルからのみ 2 列目が返されます。

```sql
WITH (
    --[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
    --[year] smallint,
    --[population] bigint
)
```
<bulk_options>

FIELDTERMINATOR ='field_terminator'

使用するフィールド ターミネータを指定します。 既定のフィールド ターミネータはコンマ (" **,** ") です。

ROWTERMINATOR ='row_terminator'`

使用する行ターミネータを指定します。 既定の行ターミネータは、\r\n などの改行文字です。

ESCAPE_CHAR = 'char'

ファイル内でそれ自体とすべての区切り記号の値をエスケープするために使用するファイル内の文字を指定します。 エスケープ文字の後にそれ自体以外の値、またはいずれかの区切り記号の値が続く場合は、その値を読み取るときにエスケープ文字が削除されます。 

ESCAPE_CHAR パラメーターは、FIELDQUOTE が有効かどうかに関係なく適用されます。 引用文字をエスケープするために使用されることはありません。 引用文字は、Excel CSV の動作に合わせて二重引用符でエスケープされます。

FIRSTROW = 'first_row' 

読み込み開始行の行番号を指定します。 既定値は 1 です。 指定したデータ ファイルの最初の行を示します。 行番号は行ターミネータの数をカウントして決定されます。 FIRSTROW は 1 から始まります。

FIELDQUOTE = 'field_quote' 

CSV ファイルで引用符文字として使用される文字を指定します。 指定しない場合、引用文字 (") が使用されます。 

## <a name="examples"></a>例

次の例では、population*.csv ファイルから序数 1 と 4 の 2 列だけが返されます。 ファイルにはヘッダー行がないため、最初の行から読み取りを開始します。

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```



次の例では、列名とデータ型を指定せずに、census データ セットから最初の行のすべての列が Parquet 形式で返されます。 

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

SELECT 
    TOP 1 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
        FORMAT='PARQUET'
    ) AS [r]
```



## <a name="next-steps"></a>次のステップ

その他のサンプルについては、[クイックスタート](query-data-storage.md)にアクセスするか、[CETAS](develop-tables-cetas.md) を使用してクエリの結果を Azure Storage に保存してください。
