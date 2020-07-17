---
title: クエリでファイルのメタデータを使用する
description: OPENROWSET 関数では、ファイル名やフォルダー パスに基づいてデータのフィルター処理または分析を行えるように、クエリで使用されるすべてのファイルについてファイルおよびパスの情報が提供されます。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 40a8e2c153ec3d8e7b4007340b9433a38f9ccc89
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427547"
---
# <a name="using-file-metadata-in-queries"></a>クエリでファイルのメタデータを使用する

SQL オンデマンド クエリ サービスを使用すれば、[フォルダーと複数ファイルに対するクエリ](query-folders-multiple-csv-files.md)に関する記事で説明されているように、複数のファイルとフォルダーに対応できます。 この記事では、クエリ内でファイル名とフォルダー名に関するメタデータ情報を使用する方法について説明します。

場合によっては、結果セット内の特定の行に関連するファイル ソースまたはフォルダー ソースを把握しておく必要があります。

関数 `filepath` および `filename` を使用すれば、結果セット内にファイル名およびそのパスを返すことができます。 また、それらを使用することで、ファイル名やフォルダー パスに基づいてデータをフィルター処理することもできます。 これらの関数については、「[filename 関数](develop-storage-files-overview.md#filename-function)」と「[filepath 関数](develop-storage-files-overview.md#filepath-function)」の構文セクションで説明されています。 以下で、サンプルに沿って簡単に説明します。

## <a name="prerequisites"></a>前提条件

この記事の残りの部分を読む前に、次の前提条件を確認してください。

- [初回セットアップ](query-data-storage.md#first-time-setup)
- [前提条件](query-data-storage.md#prerequisites)

## <a name="functions"></a>関数

### <a name="filename"></a>ファイル名

この関数からは、行の生成元のファイル名が返されます。

次のサンプルでは、2017 年の過去 3 か月間について NYC イエロー タクシーのデータ ファイルが読み取られ、ファイルごとの乗車数が返されます。 クエリの OPENROWSET 部分では、読み取るファイルを指定します。

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
        FORMAT='PARQUET') AS [r]
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

次の例では、WHERE 句内に *filename ()* を使用して、読み取るファイルをフィルター処理する方法を示します。 クエリの OPENROWSET 部分のフォルダー全体にアクセスし、WHERE 句内のファイルをフィルター処理します。

結果は前の例と同じになります。

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2017/month=9/*.parquet',
    FORMAT='PARQUET') AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.parquet', 'yellow_tripdata_2017-11.parquet', 'yellow_tripdata_2017-12.parquet')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Filepath

filepath 関数からは、完全なパスまたは部分的なパスが返されます。

- パラメーターを指定せずに呼び出した場合、行の生成元である完全なファイル パスが返されます。
- パラメーターを指定して呼び出すと、パラメーターで指定した位置にあるワイルドカードと一致するパスの一部が返されます。 たとえば、パラメーター値 1 の場合は、最初のワイルドカードと一致するパスの一部が返されます。

次のサンプルでは、2017 年の過去 3 か月間について、NYC イエロー タクシーのデータ ファイルが読み取られます。 ファイル パスごとの乗車数が返されます。 クエリの OPENROWSET 部分では、読み取るファイルを指定します。

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-1*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT,
        pickup_datetime DATETIME2,
        dropoff_datetime DATETIME2,
        passenger_count SMALLINT,
        trip_distance FLOAT,
        rate_code SMALLINT,
        store_and_fwd_flag SMALLINT,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type SMALLINT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

次の例では、WHERE 句内に *filepath()* を使用して、読み取るファイルをフィルター処理する方法を示します。

クエリの OPENROWSET 部分にワイルドカードを使用し、WHERE 句内のファイルをフィルター処理することができます。 結果は前の例と同じになります。

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_*-*.csv',
        FORMAT = 'CSV',
        FIRSTROW = 2
    )
WITH (
    vendor_id INT,
    pickup_datetime DATETIME2,
    dropoff_datetime DATETIME2,
    passenger_count SMALLINT,
    trip_distance FLOAT,
    rate_code SMALLINT,
    store_and_fwd_flag SMALLINT,
    pickup_location_id INT,
    dropoff_location_id INT,
    payment_type SMALLINT,
    fare_amount FLOAT,
    extra FLOAT,
    mta_tax FLOAT,
    tip_amount FLOAT,
    tolls_amount FLOAT,
    improvement_surcharge FLOAT,
    total_amount FLOAT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>次のステップ

次の記事では、[Parquet ファイルに対してクエリを実行](query-parquet-files.md)する方法について説明します。
