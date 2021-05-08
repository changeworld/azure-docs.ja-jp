---
title: サーバーレス SQL プールのベスト プラクティス
description: サーバーレス SQL プールの使用に関する推奨事項とベストプラクティス。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a2656d5c23a465856eee1e84d2c4f6900b21ec41
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477470"
---
# <a name="best-practices-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics のサーバーレス SQL プールのベスト プラクティス

この記事では、サーバーレス SQL プールの使用に関する一連のベスト プラクティスについて説明します。 サーバーレス SQL プールは、Azure Synapse Analytics 内のリソースです。

サーバーレス SQL プールを使用すると、Azure ストレージ アカウント内のファイルに対してクエリを実行できます。 ローカル ストレージやインジェストの機能は備えていません。 したがって、クエリが対象とするすべてのファイルは、サーバーレス SQL プールの外部にあります。 ストレージからのファイルの読み取りに関連するものはすべて、クエリのパフォーマンスに影響を与える可能性があります。

一般的なガイドラインを次に示します。
- クライアント アプリケーションがサーバーレス SQL プールと併置されていることを確認します。
  - Azure の外部でクライアント アプリケーション (Power BI Desktop、SSMS、ADS など) を使用している場合は、クライアント コンピューターに近いリージョンでサーバーレス プールを使用していることを確認してください。
- ストレージ (Azure Data Lake、Cosmos DB) とサーバーレス SQL プールが同じリージョンにあることを確認します。
- パーティション分割を使用して[ストレージ レイアウトを最適化](#prepare-files-for-querying)し、100 MB ～ 10 GB の範囲のファイルを保持します。
- 多数の結果が返される場合は、Synapse Studio ではなく、SSMS または ADS を使用していることを確認してください。 Synapse Studio は、大規模な結果セットを想定して設計されていない Web ツールです。 
- 文字列の列で結果をフィルター処理する場合は、いくつかの `BIN2_UTF8` 照合順序を使用してください。
- Power BI インポート モードまたは Azure Analysis Services を使用してクライアント側に結果をキャッシュし、定期的に更新してみてください。 サーバーレス SQL プールでは、複雑なクエリを使用している場合や大量のデータを処理している場合に、Power BI Direct Query モードで対話型のエクスペリエンスを提供することはできません。

## <a name="client-applications-and-network-connections"></a>クライアント アプリケーションとネットワーク接続

最適な接続を使用して、可能な限り近い Synapse ワークスペースにクライアント アプリケーションが接続されていることを確認します。
- Synapse ワークスペースとクライアントアプリケーションを併置します。 Power BI や Azure Analysis Services などのアプリケーションを使用している場合は、Synapse ワークスペースを配置しているのと同じリージョンにあることを確認します。 必要に応じて、クライアント アプリケーションとペアになっている別のワークスペースを作成します。 クライアント アプリケーションと Synapse ワークスペースを別のリージョンに配置すると、待機時間が長くなり、結果のストリーミング速度が低下する可能性があります。
- オンプレミスのアプリケーションからデータを読み取り中の場合は、Synapse ワークスペースが自分の場所に近いリージョンにあることを確認します。
- 大量のデータの読み取り中に、ネットワーク帯域幅の問題が発生していないことを確認します。
- 大量のデータを返すために、Synapse Studio を使用しないでください。 Synapse Studio は、HTTPS プロトコルを使用してデータを転送する Web ツールです。 大量のデータを読み取るには、Azure Data Studio または SQL Server Management Studio を使用します。

## <a name="storage-and-content-layout"></a>ストレージとコンテンツ レイアウト

### <a name="colocate-your-storage-and-serverless-sql-pool"></a>ストレージとサーバーレス SQL プールを併置する

待機時間を最小限に抑えるには、Azure Storage アカウントまたは CosmosDB 分析ストレージとサーバーレス SQL プールのエンドポイントを併置します。 ワークスペースの作成中にプロビジョニングされたストレージ アカウントとエンドポイントは同じリージョンに配置されます。

最適なパフォーマンスを得るために、サーバーレス SQL プールを使用して他のストレージ アカウントにアクセスする場合は、それらが同じリージョンにあることを確認してください。 同じリージョンにない場合、リモート リージョンとエンドポイントのリージョン間でデータをネットワーク転送するときの待機時間が長くなります。

### <a name="azure-storage-throttling"></a>Azure Storage の帯域幅調整

複数のアプリケーションとサービスがストレージ アカウントにアクセスする場合があります。 ストレージの帯域幅調整は、アプリケーション、サービス、およびサーバーレス SQL プールのワークロードによって生成される IOPS またはスループットの合計がストレージ アカウントの制限を超えると発生します。 その結果、クエリのパフォーマンスに多大な悪影響が生じます。

帯域幅調整が検出された場合、サーバーレス SQL プールに組み込まれている処理によって、それが解決されます。 サーバーレス SQL プールでは、帯域幅調整が解決されるまで、より遅いペースでストレージへの要求を行います。

> [!TIP]
> 最適なクエリ実行のために、クエリの実行中は他のワークロードでストレージ アカウントに負荷をかけないでください。

### <a name="azure-ad-pass-through-performance"></a>Azure AD パススルー パフォーマンス

サーバーレス SQL プールを使用すると、Azure Active Directory (Azure AD) パススルーまたは SAS 資格情報を使用してストレージ内のファイルにアクセスできます。 Azure AD パススルーを使用する場合、SAS と比較してパフォーマンスが低下する可能性があります。

パフォーマンスを向上させる必要がある場合は、SAS 資格情報を使用したストレージへのアクセスをお試しください。

### <a name="prepare-files-for-querying"></a>クエリ用のファイルを準備する

可能であれば、ファイルを準備してパフォーマンスを向上させることができます。

- 大きな CSV と JSON を Parquet に変換します。 Parquet は、列形式です。 圧縮されるため、同じデータを含む CSV または JSON ファイルよりもファイル サイズが小さくなります。 サーバーレス SQL プールでは、Parquet ファイルを読み取る場合にクエリで必要ない列と行をスキップできます。 サーバーレス SQL プールでは、読み取るために必要な時間が短くなりストレージ要求も少なくなります。
- クエリが 1 つの大きなファイルを対象としている場合は、複数の小さなファイルに分割すると効果があります。
- CSV ファイルのサイズは 100 MB から 10 GB の間に維持するようにしてください。
- 1 つの OPENROWSET パスまたは外部テーブル LOCATION に対して、ファイルのサイズを同じにすることをお勧めします。
- パーティションを異なるフォルダーまたはファイル名に格納して、データをパーティション分割します。 「[filename および filepath 関数を使用して特定のパーティションを対象にする](#use-filename-and-filepath-functions-to-target-specific-partitions)」を参照してください。

### <a name="colocate-your-cosmosdb-analytical-storage-and-serverless-sql-pool"></a>CosmosDB 分析ストレージとサーバーレス SQL プールを併置する

CosmosDB 分析ストレージが Synapse ワークスペースと同じリージョンに配置されていることを確認します。 リージョンをまたがるクエリでは、待機時間が長くなる可能性があります。 接続文字列の region プロパティを使用して、分析ストアが配置されるリージョンを明示的に指定します (「[サーバーレス SQL プールを使用したクエリ CosmosDb](query-cosmos-db-analytical-store.md#overview)」をご覧ください)。

```
'account=<database account name>;database=<database name>;region=<region name>'
```

## <a name="csv-optimizations"></a>CSV の最適化

### <a name="use-parser_version-20-to-query-csv-files"></a>CSV ファイルにクエリを実行するために PARSER_VERSION 2.0 を使用する

CSV ファイルに対してクエリを実行するときに、パフォーマンス最適化パーサーを使用できます。 詳細については、[PARSER_VERSION](develop-openrowset.md) に関する記事を参照してください。

### <a name="manually-create-statistics-for-csv-files"></a>CSV ファイルの統計を手動で作成する

サーバーレス SQL プールでは、統計に基づいて最適なクエリ実行プランを生成します。 必要時に、Parquet ファイルの列に対して統計が自動的に作成されます。 現時点では、CSV ファイルの列に対して統計は自動的には作成されず、クエリで使用する列 (特に、DISTINCT、JOIN、WHERE、ORDER BY、GROUP BY で使用される列) については、統計を手動で作成する必要があります。 詳細については、[サーバーレス SQL プールの統計情報](develop-tables-statistics.md#statistics-in-serverless-sql-pool)に関するトピックを確認してください。


## <a name="data-types"></a>データ型

### <a name="use-appropriate-data-types"></a>適切なデータ型を使用する

クエリで使用するデータ型は、パフォーマンスに影響します。 パフォーマンスは、次のガイドラインに従って向上させることができます。 

- 可能な最大値に対応する最小のデータ サイズを使用します。
  - 文字値の最大長が 30 文字の場合は、長さが 30 の文字データ型を使用します。
  - すべての文字列値が固定サイズの場合は、**char** または **nchar** を使用します。 それ以外の場合は、**varchar** または **nvarchar** を使用します。
  - 整数列の最大値が 500 の場合は、**smallint** を使用します。この値に対応する最小のデータ型であるためです。 整数のデータ型の範囲は、[この記事で](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true)ご確認いただけます。
- 可能であれば、**nvarchar** と **nchar** ではなく、**varchar** と **char** を使用します。
- 可能であれば、整数ベースのデータ型を使用します。 SORT、JOIN、および GROUP BY 操作は、文字データよりも整数で速く完了します。
- スキーマ推論を使用している場合は、「[推論されたデータ型を確認する](#check-inferred-data-types)」をご確認ください。

### <a name="check-inferred-data-types"></a>推論されたデータ型を確認する

[スキーマ推論](query-parquet-files.md#automatic-schema-inference)を使用すると、ファイル スキーマを知らなくてもクエリをすばやく作成し、データを探索することができます。 これは便利ですが、その引き換えに、推論されたデータ型が実際のデータ型よりも大きくなることがあります。 これは、適切なデータ型が使用されていることを確認できる十分な情報がソース ファイルにない場合に発生します。 たとえば、Parquet ファイルには最大文字列長のメタデータが含まれません。 このため、サーバーレス SQL プールでは、varchar(8000) と推測されます。

ご自分のクエリの結果のデータ型は、[sp_describe_first_results_set](/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true) を使用して確認できます。

推論されたデータ型を最適化する方法を次の例に示します。 この手順は、推論されたデータ型を示すために使用しています。 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

結果セットは次のとおりです。

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|INT|4|

クエリの推論されるデータ型がわかれば、適切なデータ型を指定できます。

```sql  
SELECT
    vendorID, tpepPickupDateTime, passengerCount
FROM 
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=2018/puMonth=*/*.snappy.parquet',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="filter-optimization"></a>フィルターの最適化

### <a name="push-wildcards-to-lower-levels-in-the-path"></a>パスの下位階層にワイルドカードを指定する

ご自分のパスでワイルドカードを使用して、[複数のファイルとフォルダー](query-data-storage.md#query-multiple-files-or-folders)にクエリを実行できます。 サーバーレス SQL プールでは、ストレージ API を使用して、ストレージ アカウントのファイルが最初の * から一覧表示されます。 指定したパスと一致しないファイルは除外します。 最初のワイルドカードまで、指定したパスに一致するファイルが多数ある場合は、ファイルの最初の一覧を減らすことで、パフォーマンスが向上する可能性があります。

### <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>filename および filepath 関数を使用して特定のパーティションを対象にする

多くの場合、データはパーティションに編成されます。 特定のフォルダーやファイルに対してクエリを実行するよう、サーバーレス SQL プールに指示することができます。 こうすることで、クエリで読み取りおよび処理する必要があるファイルの数とデータの量が減ります。 副次的効果として、パフォーマンスが向上します。

詳細については、[filename](query-data-storage.md#filename-function) 関数、[filepath](query-data-storage.md#filepath-function) 関数、および[特定のファイルにクエリを実行する](query-specific-files.md)例を参照してください。

> [!TIP]
> filepath 関数と filename 関数の結果は、常に適切なデータ型にキャストしてください。 文字データ型を使用する場合は、適切な長さを確実に使用します。

> [!NOTE]
> 現在、パーティションの削除、filepath および filename 用に使用される関数は、Apache Spark for Azure Synapse Analytics で作成された各テーブルに対して自動的に作成されたもの以外の外部テーブルに対してはサポートされていません。

格納されているデータがパーティション分割されていない場合は、パーティション分割を検討してください。 そうすると、それらの関数を使用して、それらのファイルを対象とするクエリを最適化することができます。 サーバーレス SQL プールから、[パーティション分割された Apache Spark for Azure Synapse テーブルに対してクエリを実行](develop-storage-files-spark-tables.md)すると、必要なファイルだけが自動的にクエリの対象となります。

### <a name="use-proper-collation-to-utilize-predicate-pushdown-for-character-columns"></a>適切な照合順序を使用して、文字型の列に述語のプッシュダウンを利用する

Parquet ファイル内のデータは、行グループに編成されます。 サーバーレス SQL プールでは、WHERE 句で指定した述語に基づいて行グループをスキップするため、IO が減少し、クエリのパフォーマンスが向上します。 

Parquet ファイルの文字型の列の述語プッシュダウンは Latin1_General_100_BIN2_UTF8 照合順序でのみサポートされていることに注意してください。 WITH 句を使用して、特定の列の照合順序を指定できます。 WITH 句を使用してこの照合順序を指定しない場合は、データベースの照合順序が使用されます。

## <a name="optimize-repeating-queries"></a>繰り返しクエリの最適化

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>CETAS を使用してクエリのパフォーマンスと結合を強化する

[CETAS](develop-tables-cetas.md) は、サーバーレス SQL プールで利用できる最も重要な機能の 1 つです。 CETAS は、外部テーブルのメタデータを作成し、SELECT クエリの結果をストレージ アカウント内の一連のファイルにエクスポートする並列操作です。

CETAS を使用して、結合された参照テーブルなど、頻繁に使用されるクエリの部分を新しいファイル セットに具現化できます。 次に、複数のクエリで共通の結合を繰り返す代わりに、この単一の外部テーブルに結合することができます。

CETAS によって Parquet ファイルが生成されると、最初のクエリがこの外部テーブルを対象としたときに、統計が自動的に作成されます。その結果、CETAS を使用して生成されたテーブルを対象とするクエリのパフォーマンスが向上します。

## <a name="next-steps"></a>次のステップ

[トラブルシューティング](resources-self-help-sql-on-demand.md)に関する記事で、一般的な問題と解決方法を確認します。 サーバーレス SQL プールではなく専用 SQL プールを使用している場合は、[専用 SQL プールのベストプラクティス](best-practices-dedicated-sql-pool.md)に関するページで特定のガイダンスについて確認してください。
