---
title: クエリ結果をストレージに格納する
description: この記事では、SQL オンデマンド (プレビュー) を使用してクエリ結果をストレージに格納する方法を学習します。
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 462185feb2b9cbebd17ce9cba54c2b23deea6c75
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421646"
---
# <a name="store-query-results-to-storage-using-sql-on-demand-preview-using-azure-synapse-analytics"></a>Azure Synapse Analytics を使用して SQL オンデマンド (プレビュー) でクエリ結果をストレージに格納する

この記事では、SQL オンデマンド (プレビュー) を使用してクエリ結果をストレージに格納する方法を学習します。

## <a name="prerequisites"></a>前提条件

最初の手順として、以下の記事を確認し、前提条件を満たしていることを確認します。

- [初回セットアップ](query-data-storage.md#first-time-setup)
- [前提条件](query-data-storage.md#prerequisites)

## <a name="create-external-table-as-select"></a>CREATE EXTERNAL TABLE AS SELECT

CREATE EXTERNAL TABLE AS SELECT (CETAS) ステートメントを使用して、クエリ結果をストレージに格納することができます。

> [!NOTE]
> クエリの最初の行 ([mydbname]) は、自分で作成したデータベースを使用するように変更してください。 データベースをまだ作成していない場合は、「[初回セットアップ](query-data-storage.md#first-time-setup)」を参照してください。

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [MyDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT [ParquetFF] WITH (
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
);
GO

CREATE EXTERNAL TABLE [dbo].[PopulationCETAS] WITH (
        LOCATION = 'populationParquet/',
        DATA_SOURCE = [MyDataSource],
        FILE_FORMAT = [ParquetFF]
) AS
SELECT
    *
FROM
    OPENROWSET(
        BULK 'https://showdemoweu.dfs.core.windows.net/data/population_csv/population.csv',
        FORMAT='CSV'
    ) WITH (
        CountryCode varchar(4),
        CountryName varchar(64),
        Year int,
        PopulationCount int
    ) AS r;

```

## <a name="use-a-external-table-created"></a>作成された外部テーブルを使用する

CETAS で作成した外部テーブルは、通常の外部テーブルと同じように使用できます。

> [!NOTE]
> クエリの最初の行 ([mydbname]) は、自分で作成したデータベースを使用するように変更してください。 データベースをまだ作成していない場合は、「[初回セットアップ](query-data-storage.md#first-time-setup)」を参照してください。

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM PopulationCETAS
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## <a name="next-steps"></a>次のステップ

さまざまな種類のファイルに対するクエリの実行については、[単一の CSV ファイルに対するクエリの実行](query-single-csv-file.md)、[Parquet ファイルに対するクエリの実行](query-parquet-files.md)、および [JSON ファイルに対するクエリの実行](query-json-files.md)に関するページを参照してください。
