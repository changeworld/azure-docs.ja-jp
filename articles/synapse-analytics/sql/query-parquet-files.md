---
title: サーバーレス SQL プール (プレビュー) を使用して Parquet ファイルに対してクエリを実行する
description: この記事では、サーバーレス SQL プール (プレビュー) を使用して Parquet ファイルに対してクエリを実行する方法について説明します。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3559b3724d14be6aade07c4884190afce30c0715
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93306858"
---
# <a name="query-parquet-files-using-serverless-sql-pool-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics でサーバーレス SQL プール (プレビュー) を使用して Parquet ファイルのクエリを実行する

この記事では、Parquet ファイルを読み取るサーバーレス SQL プール (プレビュー) を使用してクエリを作成する方法について説明します。

## <a name="quickstart-example"></a>クイック スタートの例

`OPENROWSET` 関数を使用すると、ファイルの URL を指定することによって、Parquet ファイルの内容を読み取ることができます。

### <a name="read-parquet-file"></a>parquet ファイルの読み取り

`PARQUET` ファイルの内容を確認する最も簡単な方法は、`OPENROWSET` 関数にファイルの URL を指定し、parquet の `FORMAT` を指定することです。 ファイルが一般公開されている場合、または Azure AD ID でこのファイルにアクセスできる場合は、次の例に示すようなクエリを使用して、ファイルの内容を表示することができます。

```sql
select top 10 *
from openrowset(
    bulk 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.parquet',
    format = 'parquet') as rows
```

このファイルにアクセスできることを確認してください。 ファイルが SAS キーまたはカスタム Azure ID で保護されている場合は、[SQL ログインのためのサーバーレベルの資格情報](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#server-scoped-credential)を設定する必要があります。

### <a name="data-source-usage"></a>データ ソースの使用状況

前の例では、ファイルへの完全なパスを使用しています。 別の方法として、ストレージのルート フォルダーを示す場所を持つ外部データ ソースを作成し、そのデータ ソースとファイルへの相対パスを `OPENROWSET` 関数で使用することができます。

```sql
create external data source covid
with ( location = 'https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases' );
go
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) as rows
```

データ ソースが SAS キーまたはカスタム ID で保護されている場合は、[データベーススコープ資格情報を使用してデータ ソース](develop-storage-files-storage-access-control.md?tabs=shared-access-signature#database-scoped-credential)を構成できます。

### <a name="explicitly-specify-schema"></a>スキーマを明示的に指定する

`OPENROWSET` を使用すると、`WITH` 句によってファイルから読み取る列を明示的に指定できます。

```sql
select top 10 *
from openrowset(
        bulk 'latest/ecdc_cases.parquet',
        data_source = 'covid',
        format = 'parquet'
    ) with ( date_rep date, cases int, geo_id varchar(6) ) as rows
```

次のセクションでは、さまざまな種類の PARQUET ファイルに対してクエリを実行する方法について説明します。

## <a name="prerequisites"></a>前提条件

最初の手順は、 [NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) ストレージ アカウントを参照するデータソースで **データベースを作成** することです。 次に、そのデータベースで[セットアップ スクリプト](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)を実行して、オブジェクトを初期化します。 このセットアップ スクリプトにより、これらのサンプルで使用されるデータ ソース、データベース スコープの資格情報、および外部ファイル形式が作成されます。

## <a name="dataset"></a>データセット

[NYC Yellow Taxi](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) データセットがこのサンプルで使用されます。 [CSV ファイルを読み取る](query-parquet-files.md)のと同じ方法で、Parquet ファイルに対してクエリを実行できます。 唯一の違いは、`FILEFORMAT` パラメーターを `PARQUET` に設定する必要があることです。 この記事の例では、Parquet ファイルの読み取りについて詳細を示します。

## <a name="query-set-of-parquet-files"></a>Parquet ファイルのセットに対してクエリを実行する

Parquet ファイルに対してクエリを実行する場合は、目的の列だけを指定できます。

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) WITH (
        tpepPickupDateTime DATETIME2,
        passengerCount INT
    ) AS nyc
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="automatic-schema-inference"></a>自動スキーマ推論

Parquet ファイルを読み取るときに、OPENROWSET WITH 句を使用する必要はありません。 列名とデータ型は、Parquet ファイルから自動的に読み取られます。

次のサンプルは、Parquet ファイルを対象にした自動スキーマ推論機能を示しています。 この場合はスキーマを指定しなくても 2017 年 9 月の行の数が返されます。

> [!NOTE]
> Parquet ファイルを読み取るときに OPENROWSET WITH 句で列を指定する必要はありません。 この場合、サーバーレス SQL プールのクエリ サービスにより、Parquet ファイル内のメタデータが利用され、名前によって列がバインドされます。

```sql
SELECT TOP 10 *
FROM  
    OPENROWSET(
        BULK 'puYear=2018/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) AS nyc
```

### <a name="query-partitioned-data"></a>パーティション分割されたデータに対してクエリを実行する

このサンプルで指定されたデータ セットは、個別のサブフォルダーに分割 (パーティション分割) されます。 filepath 関数を使用して、特定のパーティションをターゲットにすることができます。 この例では、2017 年の最初の 3 か月について、年、月、および payment_type 別の料金が示されています。

> [!NOTE]
> サーバーレス SQL プールのクエリは、Hive/Hadoop パーティション構成と互換性があります。

```sql
SELECT
        YEAR(tpepPickupDateTime),
        passengerCount,
        COUNT(*) AS cnt
FROM  
    OPENROWSET(
        BULK 'puYear=*/puMonth=*/*.snappy.parquet',
        DATA_SOURCE = 'YellowTaxi',
        FORMAT='PARQUET'
    ) nyc
WHERE
    nyc.filepath(1) = 2017
    AND nyc.filepath(2) IN (1, 2, 3)
    AND tpepPickupDateTime BETWEEN CAST('1/1/2017' AS datetime) AND CAST('3/31/2017' AS datetime)
GROUP BY
    passengerCount,
    YEAR(tpepPickupDateTime)
ORDER BY
    YEAR(tpepPickupDateTime),
    passengerCount;
```

## <a name="type-mapping"></a>型のマッピング

Parquet の型と SQL のネイティブ型のマッピングについては、[Parquet の型マッピング](develop-openrowset.md#type-mapping-for-parquet)に関するページを確認してください。

## <a name="next-steps"></a>次のステップ

次の記事に進んで、[Parquet の入れ子にされた型に対してクエリを実行](query-parquet-nested-types.md)する方法を学習してください。
