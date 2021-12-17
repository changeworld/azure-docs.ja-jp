---
title: サーバーレス SQL プールでビューを作成および使用する
description: このセクションでは、ビューを作成および使用して、サーバーレス SQL プール クエリをラップする方法を学習します。 ビューを使用すると、これらのクエリを再利用できます。 ビューは、Power BI などのツールをサーバーレス SQL プールと組み合わせて使用する場合にも必要になります。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/20/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2f6bfda53dc1e15488960dd72e8e71c88a82ab64
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013139"
---
# <a name="create-and-use-views-using-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics のサーバーレス SQL プールを使用してビューを作成および使用する

このセクションでは、ビューを作成および使用して、サーバーレス SQL プール クエリをラップする方法を学習します。 ビューを使用すると、これらのクエリを再利用できます。 ビューは、Power BI などのツールをサーバーレス SQL プールと組み合わせて使用する場合にも必要になります。

## <a name="prerequisites"></a>前提条件

最初の手順では、ビューが作成されるデータベースを作成し、そのデータベースで[設定スクリプト](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)を実行して、Azure Storage での認証に必要なオブジェクトを初期化します。 この記事のクエリはすべて、サンプル データベースで実行されます。

## <a name="views-over-external-data"></a>外部データのビュー

ビューは、通常の SQL Server ビューを作成するのと同じ方法で作成できます。 次のクエリによって、*population.csv* ファイルを読み取るビューが作成されます。

> [!NOTE]
> クエリの最初の行 ([mydbname]) は、自分で作成したデータベースを使用するように変更してください。

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'csv/population/population.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV', 
        FIELDTERMINATOR =',', 
        ROWTERMINATOR = '\n'
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r];
```

ビューには、`DATA_SOURCE` としてストレージのルート URL を備えた `EXTERNAL DATA SOURCE` が使用され、相対ファイル パスがファイルに追加されます。

### <a name="delta-lake-views"></a>Delta Lake のビュー

Delta Lake フォルダーでビューを作成する場合、`BULK` オプションの後ろに、ファイルのパスの代わりにルート フォルダーの場所を指定する必要があります。

> [!div class="mx-imgBorder"]
>![ECDC COVID-19 Delta Lake フォルダー](./media/shared/covid-delta-lake-studio.png)

Delta Lake フォルダーからデータを読み取る `OPENROWSET` 関数では、フォルダーの構造を調べて自動的にファイルの場所を特定します。

```sql
create or alter view CovidDeltaLake
as
select *
from openrowset(
           bulk 'covid',
           data_source = 'DeltaLakeStorage',
           format = 'delta'
    ) with (
           date_rep date,
           cases int,
           geo_id varchar(6)
           ) as rows
```

既知の問題については、[Synapse サーバーレス SQL プールのセルフヘルプ ページ](resources-self-help-sql-on-demand.md#delta-lake)で確認してください。

## <a name="partitioned-views"></a>パーティション ビュー

階層フォルダー構造内にパーティション分割された一連のファイルがある場合は、ファイル パスのワイルドカードを使用してパーティション パターンを記述できます。 `FILEPATH` 関数を使用して、フォルダー パスの一部をパーティション分割列として公開します。

```sql
CREATE VIEW TaxiView
AS SELECT *, nyc.filepath(1) AS [year], nyc.filepath(2) AS [month]
FROM
    OPENROWSET(
        BULK 'parquet/taxi/year=*/month=*/*.parquet',
        DATA_SOURCE = 'sqlondemanddemo',
        FORMAT='PARQUET'
    ) AS nyc
```

パーティション分割列のフィルターを使用してこのビューに対してクエリを実行すると、パーティション分割されたビューで、フォルダー パーティションが除外されます。 これにより、クエリのパフォーマンスが向上する場合があります。

### <a name="delta-lake-partitioned-views"></a>Delta Lake パーティション ビュー

Delta Lake ストレージでパーティション ビューを作成する場合、ルートになる Delta Lake フォルダーを指定するだけでよく、`FILEPATH` 関数で明示的にパーティション分割列を表示する必要はありません。

```sql
CREATE OR ALTER VIEW YellowTaxiView
AS SELECT *
FROM  
    OPENROWSET(
        BULK 'yellow',
        DATA_SOURCE = 'DeltaLakeStorage',
        FORMAT='DELTA'
    ) nyc
```

`OPENROWSET` 関数では、作業を実行している Delta Lake フォルダーの構造を調べ、パーティション分割列を自動的に特定し、表示します。 クエリの `WHERE` 句でパーティション分割列を指定した場合、パーティションの除去が自動的に実行されます。

`OPENROWSET` 関数の中にある、`DeltaLakeStorage` データ ソースに指定された `LOCATION` URI を連結したフォルダー名 (この例では `yellow`) は、`_delta_log` というサブフォルダーを含むルート Delta Lake フォルダーへの参照となっている必要があります。

> [!div class="mx-imgBorder"]
>![Yellow Taxi Delta Lake フォルダー](./media/shared/yellow-taxi-delta-lake.png)

既知の問題については、[Synapse サーバーレス SQL プールのセルフヘルプ ページ](resources-self-help-sql-on-demand.md#delta-lake)で確認してください。

## <a name="json-views"></a>JSON ビュー

ファイルからフェッチされた結果セット上で追加の処理を行う必要がある場合は、このビューが最適です。 1 つの例として、JSON ドキュメントから値を抽出するために JSON 関数を適用する必要がある、JSON ファイルの解析があります。

```sql
CREATE OR ALTER VIEW CovidCases
AS 
select
    *
from openrowset(
        bulk 'latest/ecdc_cases.jsonl',
        data_source = 'covid',
        format = 'csv',
        fieldterminator ='0x0b',
        fieldquote = '0x0b'
    ) with (doc nvarchar(max)) as rows
    cross apply openjson (doc)
        with (  date_rep datetime2,
                cases int,
                fatal int '$.deaths',
                country varchar(100) '$.countries_and_territories')
```

`OPENJSON` 関数により、テキスト形式で 1 行に 1 つの JSON ドキュメントを含む JSONL ファイルから各行が解析されます。

## <a name="cosmosdb-view"></a>CosmosDB ビュー

CosmosDB 分析ストレージがコンテナーで有効になっている場合は、Azure CosmosDB コンテナー上にこのビューを作成できます。 CosmosDB アカウント名、データベース名、コンテナー名を、ビューの一部として追加する必要があります。また、読み取り専用アクセス キーを、ビューが参照するデータベース スコープ資格情報に配置する必要があります。

```sql
CREATE DATABASE SCOPED CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
GO
CREATE OR ALTER VIEW Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

[Synapse Link を使用した CosmosDB コンテナーのクエリの詳細については、こちら](query-cosmos-db-analytical-store.md)を参照してください。

## <a name="use-a-view"></a>ビューの使用

ビューは、SQL Server クエリ内でビューを使用するのと同じ方法でクエリ内で使用できます。

次のクエリは、「[ビューの作成](#views-over-external-data)」で作成した *population_csv* ビューの使用方法を示しています。 これにより、国/地域名が 2019 年の人口の降順に返されます。

> [!NOTE]
> クエリの最初の行 ([mydbname]) は、自分で作成したデータベースを使用するように変更してください。

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationView
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>次のステップ

さまざまな種類のファイルに対するクエリの実行については、[単一の CSV ファイルに対するクエリの実行](query-single-csv-file.md)、[Parquet ファイルに対するクエリの実行](query-parquet-files.md)、および [JSON ファイルに対するクエリの実行](query-json-files.md)に関するページを参照してください。
