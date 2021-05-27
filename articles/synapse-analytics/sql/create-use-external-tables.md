---
title: Synapse SQL プールで外部テーブルを作成および使用する
description: このセクションでは、Synapse SQL プールで外部テーブルを作成および使用する方法を学習します。
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: 69b900d8695b9fa9f237411d75f0c77856f6d0eb
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110378062"
---
# <a name="create-and-use-native-external-tables-using-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics の SQL プールを使用してネイティブの外部テーブルを作成および使用する

このセクションでは、Synapse SQL プールで[ネイティブの外部テーブル](develop-tables-external-tables.md)を作成および使用する方法を学習します。 ネイティブの外部テーブルは、外部データ ソースの定義に `TYPE=HADOOP` が含まれている外部テーブルと比較して、パフォーマンス高くなっています。 これは、ネイティブの外部テーブルでは、ネイティブ コードを使用して外部データにアクセスするためです。 

外部テーブルは、Synapse SQL プールの外部データへのアクセスを制御する場合に便利です。 外部テーブルは、Power BI などのツールを Synapse SQL プールと組み合わせて使用する場合にも便利です。 外部テーブルは、次の 2 種類のストレージにアクセスできます。
- パブリック ストレージ。ユーザーは、パブリック ストレージ ファイルにアクセスします。
- 保護されたストレージ。ユーザーは、Synapse ワークスペースの SAS 資格情報、Azure AD ID、またはマネージド ID を使用して、ストレージ ファイルにアクセスします。

> [!NOTE]
>  専用 SQL プールでは、Parquet ネイティブの外部テーブルのみを使用できます。 この機能はすべてのリージョンで使用できるわけではないため、ネイティブ Parquet 外部テーブルは専用 SQL プールでの限定パブリック プレビュー段階にあります。 ご自身の専用プールでネイティブ Parquet 外部テーブルを使用できるかどうかを確認するためにパブリック プレビューに参加したい場合は、Microsoft テクニカル アカウント マネージャーまたはクラウド ソリューション アーキテクトにお問い合わせください。 一般提供されている機能を専用 SQL プールで使用する場合、または CSV または ORC ファイルにアクセスする必要がある場合は、Hadoop 外部テーブルを使用します。 ネイティブの外部テーブルは、サーバーレス SQL プールで一般提供されています。
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
