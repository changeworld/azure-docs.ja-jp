---
title: SQL オンデマンド (プレビュー) で外部テーブルを作成および使用する
description: このセクションでは、SQL オンデマンド (プレビュー) で外部テーブルを作成および使用する方法を学習します。 外部テーブルは、SQL オンデマンドで外部データへのアクセスを制御する場合や、Power BI などのツールを SQL オンデマンドと組み合わせて使用する場合に便利です。
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: a708e5e713e62ab5b7ca70b61235a84830b051cd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420796"
---
# <a name="create-and-use-external-tables-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Azure Synapse Analytics を使用して SQL オンデマンド (プレビュー) で外部テーブルを作成および使用する

このセクションでは、SQL オンデマンド (プレビュー) で外部テーブルを作成および使用する方法を学習します。 外部テーブルは、SQL オンデマンドで外部データへのアクセスを制御する場合や、Power BI などのツールを SQL オンデマンドと組み合わせて使用する場合に便利です。

## <a name="prerequisites"></a>前提条件

最初の手順として、以下の記事を確認し、SQL オンデマンド外部テーブルを作成および使用するための前提条件を満たしていることを確認します。

- [初回セットアップ](query-data-storage.md#first-time-setup)
- [前提条件](query-data-storage.md#prerequisites)

## <a name="create-an-external-table"></a>外部テーブルを作成する

外部テーブルは、通常の SQL Server 外部テーブルを作成するのと同じ方法で作成できます。 次のクエリでは、*population.csv* ファイルを読み取る外部テーブルを作成します。

> [!NOTE]
> クエリの最初の行 ([mydbname]) は、自分で作成したデータベースを使用するように変更してください。 データベースをまだ作成していない場合は、「[初回セットアップ](query-data-storage.md#first-time-setup)」を参照してください。

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [CsvDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
);
GO

CREATE EXTERNAL FILE FORMAT CSVFileFormat
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT,
    FORMAT_OPTIONS (
        FIELD_TERMINATOR = ',',
        STRING_DELIMITER = '"',
        FIRST_ROW = 2
    )
);
GO

CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
);
WITH (
    LOCATION = 'population/population.csv',
    DATA_SOURCE = CsvDataSource,
    FILE_FORMAT = CSVFileFormat
);
GO
```

## <a name="use-a-external-table"></a>外部テーブルを使用する

外部テーブルは、SQL Server クエリ内で外部テーブルを使用するのと同じ方法でクエリ内で使用できます。

次のクエリは、「[外部テーブルを作成する](#create-an-external-table)」で作成した *population_csv* 外部テーブルの使用方法を示しています。 これを実行すると、国名が 2019 年の人口の降順に返されます。

> [!NOTE]
> クエリの最初の行 ([mydbname]) は、自分で作成したデータベースを使用するように変更してください。 データベースをまだ作成していない場合は、「[初回セットアップ](query-data-storage.md#first-time-setup)」を参照してください。

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

## <a name="next-steps"></a>次のステップ

クエリの結果をストレージに格納する方法については、[クエリ結果をストレージに格納する方法](../sql/create-external-table-as-select.md)に関するページを参照してください。
