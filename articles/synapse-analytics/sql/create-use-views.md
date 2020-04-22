---
title: SQL オンデマンド (プレビュー) でビューを作成および使用する
description: このセクションでは、ビューを作成および使用して、SQL オンデマンド (プレビュー) クエリをラップする方法を学習します。 ビューを使用すると、これらのクエリを再利用できます。 ビューは、Power BI などのツールを SQL オンデマンドと組み合わせて使用する場合にも必要になります。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0f5323193706fdd00739be6c71a4fe12cfedf21b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420776"
---
# <a name="create-and-use-views-in-sql-on-demand-preview-using-azure-synapse-analytics"></a>Azure Synapse Analytics を使用して SQL オンデマンド (プレビュー) でビューを作成および使用する

このセクションでは、ビューを作成および使用して、SQL オンデマンド (プレビュー) クエリをラップする方法を学習します。 ビューを使用すると、これらのクエリを再利用できます。 ビューは、Power BI などのツールを SQL オンデマンドと組み合わせて使用する場合にも必要になります。

## <a name="prerequisites"></a>前提条件

最初の手順として、以下の記事を確認し、SQL オンデマンド ビューを作成および使用するための前提条件を満たしていることを確認します。

- [初回セットアップ](query-data-storage.md#first-time-setup)
- [前提条件](query-data-storage.md#prerequisites)

## <a name="create-a-view"></a>ビューの作成

ビューは、通常の SQL Server ビューを作成するのと同じ方法で作成できます。 次のクエリでは、*population.csv* ファイルを読み取るビューを作成します。

> [!NOTE]
> クエリの最初の行 ([mydbname]) は、自分で作成したデータベースを使用するように変更してください。 データベースをまだ作成していない場合は、「[初回セットアップ](query-data-storage.md#first-time-setup)」を参照してください。

```sql
USE [mydbname];
GO

DROP VIEW IF EXISTS populationView;
GO

CREATE VIEW populationView AS
SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv',
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

## <a name="use-a-view"></a>ビューの使用

ビューは、SQL Server クエリ内でビューを使用するのと同じ方法でクエリ内で使用できます。

次のクエリは、「[ビューの作成](#create-a-view)」で作成した *population_csv* ビューの使用方法を示しています。 これを実行すると、国名が 2019 年の人口の降順に返されます。

> [!NOTE]
> クエリの最初の行 ([mydbname]) は、自分で作成したデータベースを使用するように変更してください。 データベースをまだ作成していない場合は、「[初回セットアップ](query-data-storage.md#first-time-setup)」を参照してください。

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
