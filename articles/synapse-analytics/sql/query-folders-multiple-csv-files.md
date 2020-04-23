---
title: SQL オンデマンド (プレビュー) を使用して、フォルダーと複数の CSV ファイルに対してクエリを実行する
description: SQL オンデマンド (プレビュー) では、Windows OS で使用されるワイルドカードとよく似たワイルドカードを使用して複数のファイルまたはフォルダーを読み取ることができます。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 8f8af7fab7113e38b91c3f5f1bcc41b4e4fba2c1
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457367"
---
# <a name="query-folders-and-multiple-csv-files"></a>フォルダーと複数の CSV ファイルに対してクエリを実行する  

この記事では、Azure Synapse Analytics の SQL オンデマンド (プレビュー) を使用してクエリを作成する方法について説明します。

SQL オンデマンドでは、ワイルドカードを使用して複数のファイルまたはフォルダーを読み取ることができます。これは、Windows OS で使用されるワイルドカードとよく似ています。 しかし、複数のワイルドカードを使用できるため、柔軟性がより高くなります。

## <a name="prerequisites"></a>前提条件

この記事の残りの部分を読む前に、次の記事を必ず確認してください。

- [初回セットアップ](query-data-storage.md#first-time-setup)
- [前提条件](query-data-storage.md#prerequisites)

## <a name="read-multiple-files-in-folder"></a>フォルダー内の複数のファイルを読み取る

フォルダー *csv/taxi* を使用してサンプル クエリを実行します。 これには、2016 年 7 月から 2018 年 6 月までの NYC のタクシーのデータ (イエロー タクシーの運行記録) が含まれています。

*csv/taxi* 内のファイルには、次のように年と月に基づいた名前が付けられています。

- yellow_tripdata_2016-07.csv
- yellow_tripdata_2016-08.csv
- yellow_tripdata_2016-09.csv
- ...
- yellow_tripdata_2018-04.csv
- yellow_tripdata_2018-05.csv
- yellow_tripdata_2018-06.csv

各ファイルの構造は次のとおりです。
        
    [First 10 rows of the CSV file](./media/querying-folders-and-multiple-csv-files/nyc-taxi.png)

## <a name="read-all-files-in-folder"></a>フォルダー内のすべてのファイルを読み取る
    
次の例では、*csv/taxi* フォルダーから NYC イエロー タクシーのすべてのデータ ファイルを読み取り、年間の合計乗客数および乗車回数を返します。 また、集計関数の使用方法も示します。

```sql
SELECT 
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/*.*',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
           trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
           payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 1 回の OPENROWSET でアクセスされるファイルはすべて同じ構造である (つまり、列数とデータ型が同じである) 必要があります。

### <a name="read-subset-of-files-in-folder"></a>フォルダー内のファイルのサブセットを読み取る

次の例では、ワイルドカードを使用して、*csv/taxi* フォルダーから NYC イエロー タクシーの 2017 年のデータ ファイルを読み取り、支払いの種類ごとの合計料金を返します。

```sql
SELECT 
    payment_type,  
    SUM(fare_amount) AS fare_total
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY payment_type
ORDER BY payment_type;
```

> [!NOTE]
> 1 回の OPENROWSET でアクセスされるファイルはすべて同じ構造である (つまり、列数とデータ型が同じである) 必要があります。

## <a name="read-folders"></a>フォルダーを読み取る

OPENROWSET には、フォルダーへのパスを指定することもできます。 以下のセクションでは、このクエリの種類について説明します。

### <a name="read-all-files-from-specific-folder"></a>特定のフォルダーからすべてのファイルを読み取る

「[フォルダー内のすべてのファイルを読み取る](#read-all-files-in-folder)」で示したように、ファイル レベルのワイルドカードを使用して、フォルダー内のすべてのファイルを読み取ることができます。 しかし、フォルダーに対してクエリを実行し、そのフォルダー内のすべてのファイルを使用する方法があります。

OPENROWSET で指定するパスがフォルダーを指す場合、そのフォルダー内のすべてのファイルが、クエリのソースとして使用されます。 次のクエリは、*csv/taxi* フォルダー内のすべてのファイルを読み取ります。

> [!NOTE]
> 次のクエリで、パスの最後に / があることに注意してください。 これはフォルダーを表します。 / を省略すると、クエリは *taxi* という名前のファイルに対して実行されます。

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/taxi/',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 1 回の OPENROWSET でアクセスされるファイルはすべて同じ構造である (つまり、列数とデータ型が同じである) 必要があります。

### <a name="read-all-files-from-multiple-folders"></a>複数のフォルダーからすべてのファイルを読み取る

ワイルドカードを使用して複数のフォルダーからファイルを読み取ることができます。 次のクエリでは、*csv* フォルダー内にある、*t* で始まり、*i*で終わる名前を持つすべてのフォルダーからすべてのファイルを読み取ります。

> [!NOTE]
> 次のクエリで、パスの最後に / があることに注意してください。 これはフォルダーを表します。 / を省略すると、クエリは *t&ast;i* という名前のファイルに対して実行されます。

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/', 
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 1 回の OPENROWSET でアクセスされるファイルはすべて同じ構造である (つまり、列数とデータ型が同じである) 必要があります。

条件に一致するフォルダーは 1 つだけであるため、クエリの結果は、「[フォルダー内のすべてのファイルを読み取る](#read-all-files-in-folder)」場合と同じです。

## <a name="multiple-wildcards"></a>複数のワイルドカード

複数のワイルドカードを異なるパス レベルで使用できます。 たとえば、前述のクエリを強化して、名前が *t* で始まり、*i* で終わるすべてのフォルダーから、2017 年のデータを含むファイルのみを読み取るようにすることができます。

> [!NOTE]
> 次のクエリで、パスの最後に / があることに注意してください。 これはフォルダーを表します。 / を省略すると、クエリは *t&ast;i* という名前のファイルに対して実行されます。
> クエリあたりのワイルドカードの上限数は 10 個です。

```sql
SELECT
    YEAR(pickup_datetime) as [year],
    SUM(passenger_count) AS passengers_total,
    COUNT(*) AS [rides_total]
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/t*i/yellow_tripdata_2017-*.csv',
        FORMAT = 'CSV', 
        FIRSTROW = 2
    )
    WITH (
        vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
        pickup_datetime DATETIME2, 
        dropoff_datetime DATETIME2,
        passenger_count INT,
        trip_distance FLOAT,
        rate_code INT,
        store_and_fwd_flag VARCHAR(100) COLLATE Latin1_General_BIN2,
        pickup_location_id INT,
        dropoff_location_id INT,
        payment_type INT,
        fare_amount FLOAT,
        extra FLOAT,
        mta_tax FLOAT,
        tip_amount FLOAT,
        tolls_amount FLOAT,
        improvement_surcharge FLOAT,
        total_amount FLOAT
    ) AS nyc
GROUP BY
    YEAR(pickup_datetime)
ORDER BY
    YEAR(pickup_datetime);
```

> [!NOTE]
> 1 回の OPENROWSET でアクセスされるファイルはすべて同じ構造である (つまり、列数とデータ型が同じである) 必要があります。

条件に一致するフォルダーは 1 つだけであるため、クエリの結果は、「[フォルダー内のファイルのサブセットを読み取る](#read-subset-of-files-in-folder)」場合および「[特定のフォルダーからすべてのファイルを読み取る](#read-all-files-from-specific-folder)」場合と同じです。 より複雑なワイルドカードの使用シナリオについては、[Parquet ファイルに対するクエリ](query-parquet-files.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

詳細については、[特定のファイルに対するクエリ](query-specific-files.md)に関する記事を参照してください。
