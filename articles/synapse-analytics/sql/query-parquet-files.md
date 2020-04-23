---
title: SQL オンデマンド (プレビュー) を使用して Parquet ファイルに対してクエリを実行する
description: この記事では、SQL オンデマンド (プレビュー) を使用して Parquet ファイルに対してクエリを実行する方法について説明します。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 0b272a8c8ce81fc40585014e5930f5d7b1b5f2c0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427591"
---
# <a name="query-parquet-files-using-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics の SQL オンデマンド (プレビュー) を使用して Parquet ファイルに対してクエリを実行する

この記事では、Parquet ファイルを読み取る SQL オンデマンド (プレビュー) を使用してクエリを作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事の残りの部分を読む前に、次の記事を確認してください。

- [初回セットアップ](query-data-storage.md#first-time-setup)
- [前提条件](query-data-storage.md#prerequisites)

## <a name="dataset"></a>データセット

CSV ファイルを読み取るのと同じ方法で、Parquet ファイルに対してクエリを実行できます。 唯一の違いは、FILEFORMAT パラメーターを PARQUET に設定する必要があることです。 この記事の例では、Parquet ファイルの読み取りについて詳細を示します。

> [!NOTE]
> Parquet ファイルを読み取るとき、OPENROWSET WITH 句内に列を指定する必要はありません。 SQL オンデマンドでは、Parquet ファイル内のメタデータが使用され、名前によって列がバインドされます。

サンプル クエリではフォルダー *parquet/taxi* を使用します。 これには、2016 年 7 月から 2018 年 6 月までの NYC のタクシーのデータ (イエロー タクシーの運行記録) が含まれています 。

データは年と月でパーティション分割され、フォルダー構造は次のようになります。

- year=2016
  - month=6
  - ...
  - month=12
- year=2017
  - month=1
  - ...
  - month=12
- year=2018
  - month=1
  - ...
  - month=6

## <a name="query-set-of-parquet-files"></a>Parquet ファイルのセットに対してクエリを実行する

Parquet ファイルに対してクエリを実行する場合は、目的の列だけを指定できます。

```sql
SELECT
        YEAR(pickup_datetime),
        passenger_count,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) WITH (
        pickup_datetime DATETIME2,
        passenger_count INT
    ) AS nyc
GROUP BY
    passenger_count,
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime),
    passenger_count;
```

## <a name="automatic-schema-inference"></a>自動スキーマ推論

Parquet ファイルを読み取るときに、OPENROWSET WITH 句を使用する必要はありません。 列名とデータ型は、Parquet ファイルから自動的に読み取られます。

次のサンプルは、Parquet ファイルを対象にした自動スキーマ推論機能を示しています。 この場合はスキーマを指定しなくても 2017 年 9 月の行の数が返されます。

> [!NOTE]
> Parquet ファイルを読み取るときに OPENROWSET WITH 句で列を指定する必要はありません。 この場合、SQL オンデマンド Query サービスでは Parquet ファイル内のメタデータが利用され、名前によって列がバインドされます。

```sql
SELECT
    COUNT_BIG(*)
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc;
```

### <a name="query-partitioned-data"></a>パーティション分割されたデータに対してクエリを実行する

このサンプルで指定されたデータ セットは、個別のサブフォルダーに分割 (パーティション分割) されます。 filepath 関数を使用して、特定のパーティションをターゲットにすることができます。 この例では、2017 年の最初の 3 か月について、年、月、および payment_type 別の料金が示されています。

> [!NOTE]
> SQL オンデマンド クエリは、Hive/Hadoop パーティション構成と互換性があります。

```sql
SELECT
    nyc.filepath(1) AS [year],
    nyc.filepath(2) AS [month],
    payment_type,
    SUM(fare_amount) AS fare_total
FROM
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND pickup_datetime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type
ORDER BY
    nyc.filepath(1),
    nyc.filepath(2),
    payment_type;
```

## <a name="type-mapping"></a>型のマッピング

Parquet ファイルには、すべての列の型の説明が含まれています。 次の表では、Parquet 型を SQL ネイティブ型にマップする方法について説明します。

| Parquet 型 | Parquet 論理型 (注釈) | SQL データ型 |
| --- | --- | --- |
| BOOLEAN | | bit |
| BINARY / BYTE_ARRAY | | varbinary |
| DOUBLE | | float |
| FLOAT | | real |
| INT32 | | INT |
| INT64 | | bigint |
| INT96 | |datetime2 |
| FIXED_LEN_BYTE_ARRAY | |binary |
| BINARY |UTF8 |varchar \*(UTF8 照合順序) |
| BINARY |STRING |varchar \*(UTF8 照合順序) |
| BINARY |ENUM|varchar \*(UTF8 照合順序) |
| BINARY |UUID |UNIQUEIDENTIFIER |
| BINARY |DECIMAL |decimal |
| BINARY |JSON |varchar(max) \*(UTF8 照合順序) |
| BINARY |BSON |varbinary(max) |
| FIXED_LEN_BYTE_ARRAY |DECIMAL |decimal |
| BYTE_ARRAY |INTERVAL |varchar(max)、標準化された形式にシリアル化 |
| INT32 |INT(8, true) |smallint |
| INT32 |INT(16, true) |smallint |
| INT32 |INT(32, true) |INT |
| INT32 |INT(8, false) |tinyint |
| INT32 |INT(16, false) |INT |
| INT32 |INT(32, false) |bigint |
| INT32 |DATE |date |
| INT32 |DECIMAL |decimal |
| INT32 |TIME (MILLIS)|time |
| INT64 |INT(64, true) |bigint |
| INT64 |INT(64, false) |decimal (20,0) |
| INT64 |DECIMAL |decimal |
| INT64 |TIME (MICROS / NANOS) |time |
|INT64 |TIMESTAMP (MILLIS / MICROS / NANOS) |datetime2 |
|[複合型](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#lists) |リスト |varchar(max)、JSON にシリアル化 |
|[複合型](https://github.com/apache/parquet-format/blob/master/LogicalTypes.md#maps)|MAP|varchar(max)、JSON にシリアル化 |

## <a name="next-steps"></a>次のステップ

次の記事に進んで、[Parquet の入れ子にされた型に対してクエリを実行](query-parquet-nested-types.md)する方法を学習してください。
