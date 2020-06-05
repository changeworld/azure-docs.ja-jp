---
title: SQL オンデマンド (プレビュー) を使用して Parquet の入れ子にされた型に対するクエリを実行する
description: この記事では、入れ子にされた Parquet 型に対してクエリを実行する方法について説明します。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 82edee84317b5d542bf65e29514286f96c18bbcc
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744238"
---
# <a name="query-parquet-nested-types-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics の SQL オンデマンド (プレビュー) を使用して Parquet の入れ子にされた型に対してクエリを実行する

この記事では、Azure Synapse Analytics の SQL オンデマンド (プレビュー) を使用してクエリを作成する方法について説明します。  このクエリでは、Parquet の入れ子にされた型が読み取られます。

## <a name="prerequisites"></a>前提条件

最初の手順では、参照するデータソースを使用して**データベースを作成**します。 次に、そのデータベースで[セットアップ スクリプト](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)を実行して、オブジェクトを初期化します。 このセットアップ スクリプトにより、データ ソース、データベース スコープの資格情報、これらのサンプルで使用される外部ファイル形式が作成されます。

## <a name="project-nested-or-repeated-data"></a>入れ子にされたデータまたは繰り返しのデータを射影する

次のクエリでは、*justSimpleArray.parquet* ファイルが読み取られます。 入れ子にされたデータまたは繰り返しデータを含む Parquet ファイルからすべての列が射影されます。

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="access-elements-from-nested-columns"></a>入れ子にされた列から要素にアクセスする

次のクエリでは、*structExample.parquet* ファイルが読み取られ、入れ子にされた列の要素の表示方法が示されます。

```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK 'parquet/nested/structExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    )
    WITH (
        -- you can see original n"sted columns values by uncommenting lines below
        --DateStruct VARCHAR(8000),
        [DateStruct.Date] DATE,
        --TimeStruct VARCHAR(8000),
        [TimeStruct.Time] TIME,
        --TimestampStruct VARCHAR(8000),
        [TimestampStruct.Timestamp] DATETIME2,
        --DecimalStruct VARCHAR(8000),
        [DecimalStruct.Decimal] DECIMAL(18, 5),
        --FloatStruct VARCHAR(8000),
        [FloatStruct.Float] FLOAT
    ) AS [r];
```

## <a name="access-elements-from-repeated-columns"></a>繰り返される列から要素にアクセスする

次のクエリでは、*justSimpleArray.parquet* ファイルが読み取られ、さらに [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を使用して、配列やマップなど、繰り返される列内から**スカラー**要素が取得されます。

```sql
SELECT
    *,
    JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
    JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
    JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM
    OPENROWSET(
        BULK 'parquet/nested/justSimpleArray.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

次のクエリでは、*mapExample.parquet* ファイルが読み取られ、さらに [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) を使用して、配列やマップなど、繰り返される列内から**非スカラー**要素が取得されます。

```sql
SELECT
    MapOfPersons,
    JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM
    OPENROWSET(
        BULK 'parquet/nested/mapExample.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) AS [r];
```

## <a name="next-steps"></a>次のステップ

次の記事では、[JSON ファイルに対してクエリを実行](query-json-files.md)する方法について説明します。
