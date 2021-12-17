---
title: Synapse SQL プールで外部テーブルを作成および使用する
description: このセクションでは、Synapse SQL プールで外部テーブルを作成および使用する方法を学習します。
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 07/23/2021
ms.author: vvasic
ms.reviewer: jrasnick, wiassaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: e06c0924cb028736d7a6313aa7950ac8d389b490
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013159"
---
# <a name="create-and-use-native-external-tables-using-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics の SQL プールを使用してネイティブの外部テーブルを作成および使用する

このセクションでは、Synapse SQL プールで[ネイティブの外部テーブル](develop-tables-external-tables.md)を作成および使用する方法を学習します。 ネイティブの外部テーブルは、外部データ ソースの定義に `TYPE=HADOOP` が含まれている外部テーブルと比較して、パフォーマンスが高くなっています。 これは、ネイティブの外部テーブルでは、ネイティブ コードを使用して外部データにアクセスするためです。 

外部テーブルは、Synapse SQL プールの外部データへのアクセスを制御する場合に便利です。 外部テーブルは、Power BI などのツールを Synapse SQL プールと組み合わせて使用する場合にも便利です。 外部テーブルは、次の 2 種類のストレージにアクセスできます。
- パブリック ストレージ。ユーザーは、パブリック ストレージ ファイルにアクセスします。
- 保護されたストレージ。ユーザーは、Synapse ワークスペースの SAS 資格情報、Azure AD ID、またはマネージド ID を使用して、ストレージ ファイルにアクセスします。

> [!NOTE]
> 専用 SQL プールで、Parquet のファイルの種類のネイティブ外部テーブルのみ使用できます。この機能は **パブリック プレビュー** 段階です。 一般提供されている Parquet リーダー機能を専用 SQL プールで使用する場合、または CSV または ORC ファイルにアクセスする必要がある場合は、Hadoop 外部テーブルを使用します。 ネイティブの外部テーブルは、サーバーレス SQL プールで一般提供されています。
> ネイティブおよび Hadoop の外部テーブルの違いの詳細については、[Synapse SQL で外部テーブルを使用する](develop-tables-external-tables.md)方法に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

最初の手順として、テーブルが作成されるデータベースを作成します。 次に、このサンプルで使用する次のオブジェクトを作成します。
- データベース スコープ資格情報 `sqlondemand`。SAS で保護された `https://sqlondemandstorage.blob.core.windows.net` Azure ストレージ アカウントへのアクセスを有効にします。

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',  
    SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
    ```

- 外部データ ソース `sqlondemanddemo`。SAS キーで保護されたデモ ストレージ アカウントを参照し、外部データ ソース `nyctlc`。`https://azureopendatastorage.blob.core.windows.net/nyctlc/` という場所で一般公開されている Azure ストレージ アカウントを参照します。

    ```sql
    CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
        LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
        CREDENTIAL = sqlondemand
    );
    GO
    CREATE EXTERNAL DATA SOURCE nyctlc
    WITH ( LOCATION = 'https://azureopendatastorage.blob.core.windows.net/nyctlc/')
    GO
    CREATE EXTERNAL DATA SOURCE DeltaLakeStorage
    WITH ( location = 'https://sqlondemandstorage.blob.core.windows.net/delta-lake/' );
    ```

- ファイル形式 `QuotedCSVWithHeaderFormat` および `ParquetFormat`。CSV ファイルおよび parquet ファイルの種類を記述します。

    ```sql
    CREATE EXTERNAL FILE FORMAT QuotedCsvWithHeaderFormat
    WITH (  
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = ',', STRING_DELIMITER = '"', FIRST_ROW = 2   )
    );
    GO
    CREATE EXTERNAL FILE FORMAT ParquetFormat WITH (  FORMAT_TYPE = PARQUET );
    GO
    CREATE EXTERNAL FILE FORMAT DeltaLakeFormat WITH (  FORMAT_TYPE = DELTA );
    GO
    ```

この記事のクエリは、サンプル データベースで実行され、これらのオブジェクトを使用します。 

## <a name="external-table-on-a-file"></a>ファイル上の外部テーブル

特定の Azure AD ID または SAS キーを持つユーザーにアクセス可能な Azure ストレージ アカウントのデータにアクセスする外部テーブルを作成できます。 外部テーブルは、通常の SQL Server 外部テーブルを作成するのと同じ方法で作成できます。 

次のクエリでは、`sqlondemanddemo` データ ソースを使用して参照され、`sqlondemand` と呼ばれるデータベース スコープの資格情報で保護される、SynapseSQL のデモ Azure ストレージ アカウントから *population.csv* ファイルを読み取る外部テーブルを作成します。 

データ ソースとデータベース スコープ資格情報は、[セットアップ スクリプト](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)で作成されます。

> [!NOTE]
> クエリの最初の行 ([mydbname]) は、自分で作成したデータベースを使用するように変更してください。 

```sql
USE [mydbname];
GO
CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
)
WITH (
    LOCATION = 'csv/population/population.csv',
    DATA_SOURCE = sqlondemanddemo,
    FILE_FORMAT = QuotedCSVWithHeaderFormat
);
```

現在、ネイティブの CSV テーブルはサーバーレス SQL プールでのみ使用できます。

## <a name="external-table-on-a-set-of-files"></a>ファイルのセット上の外部テーブル

Azure ストレージに配置されたファイルのセットからデータを読み取る外部テーブルを作成できます。

```sql
CREATE EXTERNAL TABLE Taxi (
     vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
     pickup_datetime DATETIME2, 
     dropoff_datetime DATETIME2,
     passenger_count INT,
     trip_distance FLOAT,
     fare_amount FLOAT,
     tip_amount FLOAT,
     tolls_amount FLOAT,
     total_amount FLOAT
) WITH (
         LOCATION = 'yellow/puYear=*/puMonth=*/*.parquet',
         DATA_SOURCE = nyctlc,
         FILE_FORMAT = ParquetFormat
);
```

外部テーブルによってファイルが参照される条件となるパターンを指定できます。 このパターンは、Parquet および CSV のテーブルに対してのみ必要です。 Delta Lake 形式を使用している場合、指定する必要があるのはルート フォルダーだけで、パターンは外部テーブルによって自動的に検索されます。

> [!NOTE]
> テーブルはパーティション分割されたフォルダー構造で作成されますが、一部のパーティションの削除を利用することはできません。 なんらかの条件 (この場合は特定の年や月など) を満たしていないファイルをスキップしてパフォーマンスを向上させる場合は、[外部データに関するビュー](create-use-views.md#partitioned-views)を使用します。

## <a name="external-table-on-appendable-files"></a>追加可能なファイル上の外部テーブル

外部テーブルで参照されるファイルは、クエリの実行中に変更しないでください。 長時間実行されているクエリでは、SQL プールが読み取りを再試行したり、ファイルの一部を読み取ったり、ファイルを複数回読み取ったりしている場合があります。 ファイル コンテンツを変更すると、正しくない結果になる可能性があります。 したがって、クエリの実行中にファイルの変更時刻を検出すると、SQL プールはクエリに失敗します。
場合によっては、継続的に追加されるファイルへのテーブルの作成が必要になることがあります。 継続的に追加されるファイルが原因でクエリが失敗しないようにするには、`TABLE_OPTIONS` の設定を使用して、不整合な可能性のある読み取りが外部テーブルで無視されるように指定できます。


```sql
CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
)
WITH (
    LOCATION = 'csv/population/population.csv',
    DATA_SOURCE = sqlondemanddemo,
    FILE_FORMAT = QuotedCSVWithHeaderFormat,
    TABLE_OPTIONS = N'{"READ_OPTIONS":["ALLOW_INCONSISTENT_READS"]}'
);
```

`ALLOW_INCONSISTENT_READS` 読み取りオプションを指定すると、クエリ ライフサイクルでのファイルの変更時刻のチェックが無効になり、外部テーブルによって参照されるファイルで使用可能なものをすべて読み取ります。 追加可能なファイルでは、既存のコンテンツは更新されず、新しい行だけが追加されます。 そのため、更新可能なファイルと比較して、正しくない結果になる可能性が最小限に抑えられます。 このオプションを使用すると、エラーを処理する必要なく、頻繁に追加されるファイルを読み取ることができるようになります。

このオプションは、CSV ファイル形式で作成された外部テーブルでのみ使用できます。

> [!NOTE]
> オプション名が示すように、テーブルの作成者は、結果が不整合になる可能性があるリスクを受け入れることになります。 追加可能なファイルでは、テーブルを自己結合して、基になるファイルを強制的に複数回読み取った場合に、正しくない結果になる可能性があります。 ほとんどの "クラシック" クエリでは、クエリの実行中に追加された一部の行は外部テーブルで無視されます。

## <a name="delta-lake-external-table"></a>Delta Lake 外部テーブル

外部テーブルは、Delta Lake フォルダーの上に作成できます。 [1 つのファイル](#external-table-on-a-file)または[ファイル セット](#external-table-on-a-set-of-files)で作成された外部テーブルと、Delta Lake 形式で作成された外部テーブルの唯一の違いは、Delta Lake 外部テーブルでは Delta Lake 構造を含むフォルダーを参照する必要がある点です。

> [!div class="mx-imgBorder"]
>![ECDC COVID-19 Delta Lake フォルダー](./media/shared/covid-delta-lake-studio.png)

Delta Lake フォルダーに作成されたテーブル定義の例を次に示します。

```sql
CREATE EXTERNAL TABLE Covid (
     date_rep date,
     cases int,
     geo_id varchar(6)
) WITH (
        LOCATION = 'covid', --> the root folder containing the Delta Lake files
        data_source = DeltaLakeStorage,
        FILE_FORMAT = DeltaLakeFormat
);
```

パーティション分割されたフォルダーに外部テーブルを作成することはできません。 その他の既知の問題については、[Synapse サーバーレス SQL プールのセルフヘルプ ページ](resources-self-help-sql-on-demand.md#delta-lake)をご覧ください。

## <a name="use-an-external-table"></a>外部テーブルを使用する

[外部テーブル](develop-tables-external-tables.md)は、SQL Server クエリ内で外部テーブルを使用するのと同じ方法で、クエリ内で使用できます。

次のクエリでは、前のセクションで作成した *population* 外部テーブルの使用について説明しています。 これにより、国/地域名が 2019 年の人口の降順に返されます。

> [!NOTE]
> クエリの最初の行 ([mydbname]) は、自分で作成したデータベースを使用するように変更してください。

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

このクエリのパフォーマンスは、リージョンによって異なる場合があります。 ワークスペースが、これらのサンプルで使用されている Azure ストレージ アカウントと同じリージョンに配置されていない可能性があります。 実稼働ワークロードの場合は、Synapse ワークスペースと Azure Storage を同じリージョンに配置します。

## <a name="next-steps"></a>次の手順

クエリの結果をストレージに格納する方法については、[クエリ結果をストレージに格納する方法](../sql/create-external-table-as-select.md)に関する記事を参照してください。
