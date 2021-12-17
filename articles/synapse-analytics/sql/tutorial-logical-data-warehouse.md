---
title: 'チュートリアル: サーバーレス SQL プールを使用して論理データ ウェアハウスを作成する'
description: このチュートリアルでは、サーバーレス SQL プールを使用して、Azure データ ソース上に論理データ ウェアハウスを簡単に作成する方法を説明します。
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 08/20/2021
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: de0374f2ea26e3fa1dc7d25e7c837187f8914918
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254740"
---
# <a name="tutorial-create-logical-data-warehouse-with-serverless-sql-pool"></a>チュートリアル: サーバーレス SQL プールを使用して論理データ ウェアハウスを作成する

このチュートリアルでは、Azure ストレージと Azure Cosmos DB 上に論理データ ウェアハウス (LDW) を作成する方法について説明します。

LDW は、Azure Data Lake Storage (ADLS)、Azure Cosmos DB 分析ストレージ、Azure Blob Storage などの Azure データ ソース上に構築されるリレーショナル レイヤーです。

## <a name="create-an-ldw-database"></a>LDW データベースを作成する

外部のデータ ソースを参照する外部テーブルとビューの格納先となるカスタム データベースを作成する必要があります。

```sql
CREATE DATABASE Ldw
      COLLATE Latin1_General_100_BIN2_UTF8;
```

この照合順序によって、Parquet と Cosmos DB の読み取り中に最適なパフォーマンスが得られます。 データベースの照合順序を指定しない場合は、列の定義でこの照合順序を指定してください。

## <a name="configure-data-sources-and-formats"></a>データ ソースと形式を構成する

まず、データ ソースを構成し、リモートに格納されるデータのファイル形式を指定する必要があります。

### <a name="create-data-source"></a>データ ソースの作成

データ ソースとは、データの格納場所とデータ ソースに対する認証方法を記述した接続文字列情報のことです。

次に示したのは、パブリックな [ECDC COVID 19 Azure オープン データセット](../../open-datasets/dataset-ecdc-covid-cases.md)を参照するデータ ソース定義の一例です。

```sql
CREATE EXTERNAL DATA SOURCE ecdc_cases WITH (
    LOCATION = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/'
);
```

データ ソースの所有者によって匿名アクセスが許可されている場合や、呼び出し元の Azure AD ID に明示的なアクセス権が与えられている場合、呼び出し元は、資格情報なしでデータ ソースにアクセスすることができます。

外部データ ソース上のデータへのアクセス中に使用されるカスタムの資格情報を明示的に定義できます。
- Synapse ワークスペースの[マネージド ID](develop-storage-files-storage-access-control.md?tabs=managed-identity)
- Azure ストレージの [Shared Access Signature](develop-storage-files-storage-access-control.md?tabs=shared-access-signature)
- Cosmos DB 分析ストレージを読み取ることができる読み取り専用の Cosmos DB アカウントキー。

前提条件として、データベースにはマスター キーを作成する必要があります。
```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'Setup you password - you need to create master key only once';
```

次の外部データ ソースでは、Synapse SQL プールがストレージ内のデータにアクセスするために、ワークスペースのマネージド ID を使用する必要があります。

```sql
CREATE DATABASE SCOPED CREDENTIAL WorkspaceIdentity
WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE ecdc_cases WITH (
    LOCATION = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/',
    CREDENTIAL = WorkspaceIdentity
);
```

Cosmos DB 分析ストレージにアクセスするためには、読み取り専用 Cosmos DB アカウント キーが含まれた資格情報を定義する必要があります。

```sql
CREATE DATABASE SCOPED CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE',
     SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
```

Synapse 管理者ロールを持つすべてのユーザーは、これらの資格情報を使用して、Azure Data Lake Storage または Cosmos DB 分析ストレージにアクセスできます。 Synapse 管理者ロールを持たない権限の低いユーザーがいる場合は、次のデータベース スコープの資格情報を参照するための明示的なアクセス許可を付与する必要があります。

```sql
GRANT REFERENCES ON DATABASE SCOPED CREDENTIAL::WorkspaceIdentity TO <user>
GO
GRANT REFERENCES ON DATABASE SCOPED CREDENTIAL::MyCosmosDbAccountCredential TO <user>
GO
```

詳細については、[DATABASE SCOPED CREDENTIAL アクセス許可の付与](/sql/t-sql/statements/grant-database-scoped-credential-transact-sql)に関するページを参照してください。

### <a name="define-external-file-formats"></a>外部ファイルの形式を定義する

外部ファイルの形式は、外部データ ソース上に格納されるファイルの構造を定義します。 外部ファイルの形式として、Parquet と CSV を定義できます。

```sql
CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
GO
CREATE EXTERNAL FILE FORMAT CsvFormat WITH (  FORMAT_TYPE = DELIMITEDTEXT );
```

詳細については、[こちらの記事](develop-tables-external-tables.md?tabs=native#syntax-for-create-external-file-format)をご覧ください。

## <a name="explore-your-data"></a>データを調査する

データ ソースを設定したら、`OPENROWSET` 関数を使用してデータを調査できます。 [OPENROWSET](develop-openrowset.md) 関数は、リモート データ ソース (ファイルなど) の内容を読み取って行のセットとして返します。

```sql
select top 10  *
from openrowset(bulk 'latest/ecdc_cases.parquet',
                data_source = 'ecdc_cases',
                format='parquet') as a
```

`OPENROWSET` 関数からは、外部のファイルまたはコンテナーに含まれている列についての情報が得られます。これにより、外部テーブルとビューのスキーマを定義することができます。

## <a name="create-external-tables-on-azure-storage"></a>Azure ストレージに外部テーブルを作成する

スキーマが見つかったら、外部データ ソース上に外部テーブルとビューを作成できます。 テーブルとビューは、データベース スキーマで整理することをお勧めします。 Azure Data Lake Storage 内の ECDC COVID データ セットにアクセスするすべてのオブジェクトの配置先となるスキーマは、次のクエリで作成できます。

```sql
create schema ecdc_adls;
```

データベース スキーマは、スキーマごとにオブジェクトをグループ化したりアクセス許可を定義したりするための有効な手段となります。 

スキーマの定義後、ファイルを参照する外部テーブルを作成できます。
次の外部テーブルは、Azure ストレージに格納された ECDC COVID Parquet ファイルを参照しています。

```sql
create external table ecdc_adls.cases (
    date_rep                   date,
    day                        smallint,
    month                      smallint,
    year                       smallint,
    cases                      smallint,
    deaths                     smallint,
    countries_and_territories  varchar(256),
    geo_id                     varchar(60),
    country_territory_code     varchar(16),
    pop_data_2018              int,
    continent_exp              varchar(32),
    load_date                  datetime2(7),
    iso_country                varchar(16)
) with (
    data_source= ecdc_cases,
    location = 'latest/ecdc_cases.parquet',
    file_format = ParquetFormat
);
```

クエリのパフォーマンスを最適化するために、文字列と数値の列には、可能な限り小さい型を使用してください。

## <a name="create-views-on-azure-cosmos-db"></a>Azure Cosmos DB 上にビューを作成する

外部データには、外部テーブルの代わりにビューを作成することもできます。 

前の例に示したテーブルと同様、ビューはそれぞれ異なるスキーマに配置することができます。

```sql
create schema ecdc_cosmosdb;
```

これで、Cosmos DB コンテナーを参照するスキーマにビューを作成できるようになりました。

```sql
CREATE OR ALTER VIEW ecdc_cosmosdb.Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) WITH
     ( date_rep varchar(20), 
       cases bigint,
       geo_id varchar(6) 
     ) as rows
```

スキーマの `WITH` 定義では、パフォーマンスを最適化するために、可能な限り小さい型を使用する必要があります。

> [!NOTE]
> Azure Cosmos DB アカウント キーは、別個の資格情報に格納し、その資格情報を `OPENROWSET` 関数から参照する必要があります。
> ビュー定義でアカウント キーを保持しないでください。

## <a name="access-and-permissions"></a>アクセスおよびアクセス許可

最後の手順として、LDW にアクセスできるデータベース ユーザーを作成し、外部テーブルとビューのデータを選択するためのアクセス許可をそれらのユーザーに与える必要があります。
次のスクリプトでは、Azure AD ID を使用して認証される新しいユーザーを追加する方法を確認できます。

```sql
CREATE USER [jovan@contoso.com] FROM EXTERNAL PROVIDER;
GO
```

Azure AD プリンシパルの代わりに、ログイン名とパスワードで認証する SQL プリンシパルを作成できます。

```sql
CREATE LOGIN [jovan] WITH PASSWORD = 'My Very strong Password ! 1234';
CREATE USER [jovan] FROM LOGIN [jovan];
```

どちらの場合も、ユーザーにアクセス許可を割り当てることができます。

```sql
DENY ADMINISTER DATABASE BULK OPERATIONS TO [jovan@contoso.com]
GO
GRANT SELECT ON SCHEMA::ecdc_adls TO [jovan@contoso.com]
GO
GRANT SELECT ON OBJECT::ecdc_cosmosDB.cases TO [jovan@contoso.com]
GO
GRANT REFERENCES ON DATABASE SCOPED CREDENTIAL::MyCosmosDbAccountCredential TO [jovan@contoso.com]
GO
```

セキュリティ規則は、セキュリティ ポリシーによって異なります。 一般的なガイドラインを次に示します。
- 自分が用意した外部テーブルとビューを使用することによってのみデータの読み取りを許可する必要があるため、新しいユーザーには、`ADMINISTER DATABASE BULK OPERATIONS` アクセス許可を拒否します。
- `SELECT` アクセス許可は、ユーザーに使用を許可すべきテーブルに限定して付与するようにします。
- ビューを使用してデータへのアクセスを提供する場合、外部データ ソースへのアクセスに使用される資格情報に対し、`REFERENCES` アクセス許可を付与します。

このユーザーには、外部データのクエリに必要な最小限のアクセス許可が与えられています。 アクセス許可、外部テーブル、ビューを設定できるパワーユーザーを作成したい場合は、`CONTROL` アクセス許可をユーザーに割り当ててください。

```sql
GRANT CONTROL TO [jovan@contoso.com]
```

### <a name="role-based-security"></a>ロール ベース セキュリティ

個々の用途にアクセス許可を割り当てる代わりに、ロールにユーザーを整理し、ロール レベルでアクセス許可を管理することをお勧めします。
次のコード サンプルでは、COVID-19 ケースを分析できるユーザーを表す新しいロールを作成し、このロールに 3 人のユーザーを追加します。

```sql
CREATE ROLE CovidAnalyst;

ALTER ROLE CovidAnalyst ADD MEMBER [jovan@contoso.com];
ALTER ROLE CovidAnalyst ADD MEMBER [milan@contoso.com];
ALTER ROLE CovidAnalyst ADD MEMBER [petar@contoso.com];
```

グループに属しているすべてのユーザーにアクセス許可を割り当てることができます。

```sql
GRANT SELECT ON SCHEMA::ecdc_cosmosdb TO [CovidAnalyst];
GO
DENY SELECT ON SCHEMA::ecdc_adls TO [CovidAnalyst];
GO
DENY ADMINISTER DATABASE BULK OPERATIONS TO [CovidAnalyst];
```

このロール ベース セキュリティのアクセス制御により、セキュリティ規則の管理を簡略化できる場合があります。

## <a name="next-steps"></a>次のステップ

- サーバーレス SQL プールを Power BI Desktop に接続してレポートを作成する方法については、[サーバーレス SQL プールの Power BI Desktop への接続とレポートの作成](tutorial-connect-power-bi-desktop.md)に関する記事をご覧ください。
- サーバーレス SQL プールで外部テーブルを使用する方法については、「[Synapse SQL で外部テーブルを使用する](develop-tables-external-tables.md?tabs=sql-pool)」をご覧ください。
