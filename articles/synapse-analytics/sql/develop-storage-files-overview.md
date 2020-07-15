---
title: Synapse SQL 内で SQL オンデマンド (プレビュー) を使用してストレージ上のファイルにアクセスする
description: Synapse SQL 内で SQL オンデマンド (プレビュー) リソースを使用してストレージ ファイルに対してクエリを実行する方法について説明します。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/19/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: c251b70d1988be82821f1e133151dae1ac6d1bc9
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921288"
---
# <a name="accessing-external-storage-in-synapse-sql-on-demand"></a>Synapse SQL (オンデマンド) 内の外部ストレージへのアクセス

このドキュメントでは、ユーザーが Synapse SQL (オンデマンド) で、Azure Storage に格納されているファイルからデータを読み取る方法について説明します。 ユーザーは、ストレージにアクセスするための次のオプションを使用できます。

- [OPENROWSET](develop-openrowset.md) 関数。Azure Storage 内のファイルに対するアドホック クエリを有効にします。
- [外部テーブル](develop-tables-external-tables.md)。外部ファイル セット上に構築された定義済みのデータ構造です。

ユーザーは、Azure AD パススルー認証 (Azure AD プリンシパルの既定値) や SAS 認証 (SQL プリンシパルの既定値) などの[さまざまな認証方法](develop-storage-files-storage-access-control.md)を使用できます。

## <a name="openrowset"></a>OPENROWSET

[OPENROWSET](develop-openrowset.md) 関数を使用すると、ユーザーは Azure Storage からファイルを読み取ることができます。

### <a name="query-files-using-openrowset"></a>OPENROWSET を使用してファイルに対してクエリを実行する

OPENROWSET を使用すると、ユーザーはストレージにアクセスできる場合に、Azure Storage 上の外部ファイルに対してクエリを実行できます。 Synapse SQL オンデマンド エンドポイントに接続されているユーザーは、次のクエリを使用して、Azure Storage 上のファイルの内容を読み取る必要があります。

```sql
SELECT * FROM
 OPENROWSET(BULK 'http://storage...com/container/file/path/*.csv', format= 'parquet') as rows
```

ユーザーは、次のアクセス ルールを使用してストレージにアクセスできます。

- Azure AD ユーザー - OPENROWSET では、呼び出し元の Azure AD ID を使用して、Azure Storage にアクセスしたり、匿名アクセスでストレージにアクセスしたりします。
- SQL ユーザー – OPENROWSET では、匿名アクセスでストレージにアクセスします。

SQL プリンシパルでは、OPENROWSET を使用して、ワークスペースのマネージド ID または SAS トークンで保護されたファイルに対して、直接クエリを実行することもできます。 SQL ユーザーがこの関数を実行する場合、ALTER ANY CREDENTIAL 権限を持つパワー ユーザーは、(ストレージ名とコンテナーを使用して) 関数内の URL に一致するサーバースコープの資格情報を作成し、この資格情報に対する REFERENCES 権限を OPENROWSET 関数の呼び出し元に付与する必要があります。

```sql
EXECUTE AS somepoweruser

CREATE CREDENTIAL [http://storage.dfs.com/container]
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 'sas token';

GRANT REFERENCES CREDENTIAL::[http://storage.dfs.com/container] TO sqluser
```

URL に一致するサーバーレベルの資格情報がない場合、または SQL ユーザーがこの資格情報に対する参照権限を持っていない場合は、エラーが返されます。 SQL プリンシパルでは、何らかの Azure AD ID を使用して偽装することはできません。

> [!NOTE]
> このバージョンの OPENROWSET は、既定の認証を使用して迅速かつ簡単にデータを探索できるように設計されています。 偽装またはマネージド ID を活用するには、次のセクションで説明されているように OPENROWSET を DATASOURCE と共に使用します。

### <a name="querying-data-sources-using-openrowset"></a>OPENROWSET を使用してデータ ソースに対してクエリを実行する

OPENROWSET を使用すると、ユーザーは外部データ ソースに配置されたファイルに対してクエリを実行できます。

```sql
SELECT * FROM
 OPENROWSET(BULK 'file/path/*.parquet',
 DATASOURCE = MyAzureInvoices,
 FORMAT= 'parquet') as rows
```

CONTROL DATABASE 権限を持つパワー ユーザーは、ストレージにアクセスするために使用される DATABASE SCOPED CREDENTIAL、および使用する必要がある資格情報とデータ ソースの URL を指定する EXTERNAL DATA SOURCE を作成する必要があります。

```sql
CREATE DATABASE SCOPED CREDENTIAL AccessAzureInvoices
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&amp;sp=rwac&amp;se=2017-02-01T00:55:34Z&amp;st=201********' ;

CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://newinvoices.blob.core.windows.net/week3' ,
 CREDENTIAL = AccessAzureInvoices) ;
```

DATABASE SCOPED CREDENTIAL では、参照されるデータ ソース上のファイルへのアクセス方法 (現在は SAS およびマネージド ID) を指定します。

呼び出し元には、OPENROWSET 関数を実行するための次のいずれかの権限が必要です。

- OPENROWSET を実行するための次のいずれかの権限:
  - ADMINISTER BULK OPERATION を使用すると、ログインで OPENROWSET 関数を実行できます。
  - ADMINISTER DATABASE BULK OPERATION を使用すると、データベース スコープ ユーザーは OPENROWSET 関数を実行できます。
- EXTERNAL DATA SOURCE で参照される資格情報に対する REFERENCES DATABASE SCOPED CREDENTIAL

#### <a name="accessing-anonymous-data-sources"></a>匿名データ ソースへのアクセス

ユーザーは、パブリック アクセス ストレージを参照する CREDENTIAL なしの EXTERNAL DATA SOURCE を作成するか、Azure AD パススルー認証を使用することができます。

```sql
CREATE EXTERNAL DATA SOURCE MyAzureInvoices
 WITH ( LOCATION = 'https://newinvoices.blob.core.windows.net/week3') ;
```

## <a name="external-table"></a>EXTERNAL TABLE

テーブルを読み取る権限を持つユーザーは、Azure Storage のフォルダーとファイルのセット上に作成された EXTERNAL TABLE を使用して外部ファイルにアクセスできます。

[外部テーブルを作成するための権限](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=sql-server-ver15#permissions) (CREATE TABLE、ALTER ANY CREDENTIAL、REFERENCES DATABASE SCOPED CREDENTIAL など) を持つユーザーは、次のスクリプトを使用して、Azure Storage データソース上にテーブルを作成できます。

```sql
CREATE EXTERNAL TABLE [dbo].[DimProductexternal]
( ProductKey int, ProductLabel nvarchar, ProductName nvarchar )
WITH
(
LOCATION='/DimProduct/year=*/month=*' ,
DATA_SOURCE = AzureDataLakeStore ,
FILE_FORMAT = TextFileFormat
) ;
```

CONTROL DATABASE 権限を持つユーザーは、ストレージにアクセスするために使用される DATABASE SCOPED CREDENTIAL、および使用する必要がある資格情報とデータ ソースの URL を指定する EXTERNAL DATA SOURCE を作成する必要があります。

```sql
CREATE DATABASE SCOPED CREDENTIAL cred
 WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
 SECRET = '******srt=sco&sp=rwac&se=2017-02-01T00:55:34Z&st=201********' ;

CREATE EXTERNAL DATA SOURCE AzureDataLakeStore
 WITH ( LOCATION = 'https://samples.blob.core.windows.net/products' ,
 CREDENTIAL = cred
 ) ;
```

DATABASE SCOPED CREDENTIAL では、参照されるデータ ソース上のファイルへのアクセス方法を指定します。

### <a name="reading-external-files-with-external-table"></a>EXTERNAL TABLE を使用した外部ファイルの読み取り

EXTERNAL TABLE では、標準的な SQL SELECT ステートメントを使用して、データ ソースを介して参照されるファイルからデータを読み取ることができます。

```sql
SELECT *
FROM dbo.DimProductsExternal
```

呼び出し元には、データを読み取るための次の権限が必要です。
- 外部テーブルに対する `SELECT` 権限
- `DATA SOURCE` に `CREDENTIAL` がある場合は、`REFERENCES DATABASE SCOPED CREDENTIAL` 権限

## <a name="permissions"></a>アクセス許可

次の表には、上に一覧表示されている操作に必要な権限が一覧表示されています。

| クエリ | 必要なアクセス許可|
| --- | --- |
| OPENROWSET(BULK) (データソースを指定しない) | `ADMINISTER BULK ADMIN`、`ADMINISTER DATABASE BULK ADMIN`、または SQL ログインには、SAS で保護されたストレージに対する REFERENCES CREDENTIAL::\<URL> が必要です |
| OPENROWSET (BULK) (資格情報なしのデータソースを指定) | `ADMINISTER BULK ADMIN` または `ADMINISTER DATABASE BULK ADMIN` |
| OPENROWSET (BULK) (資格情報ありのデータソースを指定) | `ADMINISTER BULK ADMIN`、`ADMINISTER DATABASE BULK ADMIN`、または `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CREATE EXTERNAL DATA SOURCE | `ALTER ANY EXTERNAL DATA SOURCE` および `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CREATE EXTERNAL TABLE | `CREATE TABLE`、`ALTER ANY SCHEMA`、`ALTER ANY EXTERNAL FILE FORMAT`、および `ALTER ANY EXTERNAL DATA SOURCE` |
| SELECT FROM EXTERNAL TABLE | `SELECT TABLE` および `REFERENCES DATABASE SCOPED CREDENTIAL` |
| CETAS | テーブルを作成する場合 - `CREATE TABLE`、`ALTER ANY SCHEMA`、`ALTER ANY DATA SOURCE`、および `ALTER ANY EXTERNAL FILE FORMAT`。 データを読み取る場合: クエリのテーブル、ビューまたは関数ごとの `ADMIN BULK OPERATIONS`、`REFERENCES CREDENTIAL` または `SELECT TABLE` + ストレージに対する読み取りと書き込み権限 |

## <a name="next-steps"></a>次のステップ

これで、操作方法に関する次の記事に進む準備ができました。

- [ストレージ上のデータに対してクエリを実行する](query-data-storage.md)

- [CSV ファイルに対してクエリを実行する](query-single-csv-file.md)

- [フォルダーと複数のファイルに対してクエリを実行する](query-folders-multiple-csv-files.md)

- [特定のファイルに対してクエリを実行する](query-specific-files.md)

- [Parquet ファイルに対してクエリを実行する](query-parquet-files.md)

- [入れ子になっている型に対してクエリを実行する](query-parquet-nested-types.md)

- [JSON ファイルに対してクエリを実行する](query-json-files.md)

- [ビューの作成と使用](create-use-views.md)
