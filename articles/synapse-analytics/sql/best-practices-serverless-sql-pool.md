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
ms.openlocfilehash: 7611d9067422a0e2e342bbafeb315b5b1545a212
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545192"
---
# <a name="best-practices-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics のサーバーレス SQL プールのベスト プラクティス

この記事では、サーバーレス SQL プールの使用に関する一連のベスト プラクティスについて説明します。 サーバーレス SQL プールは、Azure Synapse Analytics 内のリソースです。 専用 SQL プールを使用している場合は、[専用 SQL プールのベスト プラクティス](best-practices-dedicated-sql-pool.md)に関する記事で、具体的なガイダンスをご覧ください。

サーバーレス SQL プールでは、Azure Storage アカウントのファイルに対するクエリを実行できます。 ローカル ストレージやインジェストの機能は備えていません。 クエリの対象となるすべてのファイルは、サーバーレス SQL プールの外部にあります。 ストレージ上のファイルの読み取りに関係するものはすべて、クエリのパフォーマンスに影響を与える可能性があります。

一般的なガイドラインを次に示します。

- クライアント アプリケーションは、サーバーレス SQL プールと併置します。
  - クライアント アプリケーションを Azure 外で使用する場合は、サーバーレス SQL プールを、クライアント コンピューターに近いリージョンで使用します。 クライアント アプリケーションには、たとえば、Power BI Desktop、SQL Server Management Studio、Azure Data Studio などがあります。
- ストレージとサーバーレス SQL プールは同じリージョンに配置します。 ストレージには、たとえば、Azure Data Lake Storage や Azure Cosmos DB があります。
- パーティション分割を行い、ファイル サイズを 100 MB から 10 GB の範囲に収めることで、[ストレージのレイアウトを最適化](#prepare-files-for-querying)します。
- 大量の結果を返す場合は、Azure Synapse Studio ではなく、SQL Server Management Studio か Azure Data Studio を使用します。 Azure Synapse Studio は、大量の結果セットを扱うための Web ツールではありません。
- 文字列の列で結果を絞り込む場合は、照合順序に `BIN2_UTF8` を使用してみてください。
- Power BI インポート モードか Azure Analysis Services を使用してクライアント サイドに結果をキャッシュし、それを定期的に更新することを検討します。 複雑なクエリを使用する、あるいは大量のデータを処理する場合、サーバーレス SQL プールでは、Power BI Direct Query モードの対話型操作を使用できません。

## <a name="client-applications-and-network-connections"></a>クライアント アプリケーションとネットワーク接続

クライアント アプリケーションを、最適な接続条件で、できる限り近くにある Azure Synapse ワークスペースに接続します。
- クライアント アプリケーションは、Azure Synapse ワークスペースと併置します。 Power BI や Azure Analysis Service などのアプリケーションを使用する場合は、それらを Azure Synapse ワークスペースと同じリージョンに配置します。 必要に応じて、クライアント アプリケーションとペアになっている別のワークスペースを作成します。 クライアント アプリケーションと Azure Synapse ワークスペースを異なるリージョンに配置すると、待機時間が長くなったり、結果のストリーミングが遅くなったりする場合があります。
- オンプレミスのアプリケーションからデータを読み取る場合は、Azure Synapse ワークスペースを、自分の場所に近いリージョンに配置します。
- 大量のデータを読み取るとき、ネットワークの帯域幅に問題がないことを確認します。
- 大量のデータを返すのに Azure Synapse Studio を使用しないでください。 Azure Synapse Studio は HTTPS プロトコルを使用してデータを転送するための Web ツールです。 大量のデータを読み取るには、Azure Data Studio または SQL Server Management Studio を使用します。

## <a name="storage-and-content-layout"></a>ストレージとコンテンツ レイアウト

ここでは、サーバーレス SQL プールのストレージとコンテンツ レイアウトのベスト プラクティスを説明します。

### <a name="colocate-your-storage-and-serverless-sql-pool"></a>ストレージとサーバーレス SQL プールを併置する

待機時間をできる限り短くするには、Azure Storage アカウントまたは Azure Cosmos DB 分析ストレージを、サーバーレス SQL プールのエンドポイントと併置します。 ワークスペースの作成中にプロビジョニングされたストレージ アカウントとエンドポイントは同じリージョンに配置されます。

最適なパフォーマンスを得るために、サーバーレス SQL プールを使用して他のストレージ アカウントにアクセスする場合は、それらが同じリージョンにあることを確認してください。 同じリージョンにない場合、リモート リージョンとエンドポイントのリージョン間でデータをネットワーク転送するときの待機時間が長くなります。

### <a name="azure-storage-throttling"></a>Azure Storage の帯域幅調整

複数のアプリケーションとサービスがストレージ アカウントにアクセスする場合があります。 アプリケーション、サービスおよびサーバーレス SQL プール ワークロードで生成する IOPS またはスループットの合計が、ストレージ アカウントに設定された上限を超えると、ストレージのスロットリングが実行されます。 その結果、クエリのパフォーマンスに多大な悪影響が生じます。

帯域幅調整が検出された場合、サーバーレス SQL プールに組み込まれている処理によって、それが解決されます。 ストットリングが終わるまで、サーバーレス SQL プールで要求を送信するペースが遅くなります。

> [!TIP]
> 最適なクエリ実行のために、クエリの実行中は他のワークロードでストレージ アカウントに負荷をかけないでください。

### <a name="azure-ad-pass-through-authentication-performance"></a>Azure AD Pass-through Authentication のパフォーマンス

サーバーレス SQL プールでは、Azure Active Directory (Azure AD) Pass-through Authentication または共有アクセス シグネチャの資格情報を使用して、ストレージのファイルにアクセスできます。 Azure AD Pass-through Authentication を使用すると、共有アクセス シグネチャよりもパフォーマンスが悪くなる場合があります。

高いパフォーマンスが必要な場合は、共有アクセス シグネチャの資格情報を使用してストレージにアクセスしてみてください。

### <a name="prepare-files-for-querying"></a>クエリ用のファイルを準備する

可能であれば、ファイルを準備してパフォーマンスを向上させることができます。

- 大きな CSV および JSON ファイルを Parquet に変換します。 Parquet は、列形式です。 圧縮されるため、同じデータを含む CSV または JSON ファイルよりもファイル サイズが小さくなります。 サーバーレス SQL プールで Parquet ファイルを読み取るときは、クエリに必要ない列と行をスキップします。 サーバーレス SQL プールでは、これを読み取る所要時間が短く、ストレージへの要求も少なくて済みます。
- クエリが 1 つの大きなファイルを対象としている場合は、複数の小さなファイルに分割すると効果があります。
- CSV ファイルのサイズは 100 MB から 10 GB の間に維持するようにしてください。
- 1 つの OPENROWSET パスまたは外部テーブル LOCATION に対して、ファイルのサイズを同じにすることをお勧めします。
- パーティションを異なるフォルダーまたはファイル名に格納して、データをパーティション分割します。 「[filename および filepath 関数を使用して特定のパーティションを対象にする](#use-filename-and-filepath-functions-to-target-specific-partitions)」を参照してください。

### <a name="colocate-your-azure-cosmos-db-analytical-storage-and-serverless-sql-pool"></a>Azure Cosmos DB 分析ストレージとサーバーレス SQL プールを併置する

Azure Cosmos DB 分析ストレージは、Azure Synapse ワークスペースと同じリージョンに配置します。 リージョンをまたがるクエリでは、待機時間が長くなる可能性があります。 接続文字列のリージョン プロパティで、分析ストアを配置するリージョンを明示的に指定します ([サーバーレス SQL プールで Azure Cosmos DB に対するクエリを実行する方法](query-cosmos-db-analytical-store.md#overview)に関する記事をご覧ください):

```
'account=<database account name>;database=<database name>;region=<region name>'
```

## <a name="csv-optimizations"></a>CSV の最適化

ここでは、サーバーレス SQL プールで CSV ファイルを使用する際のベスト プラクティスを説明します。

### <a name="use-parser_version-20-to-query-csv-files"></a>CSV ファイルにクエリを実行するために PARSER_VERSION 2.0 を使用する

CSV ファイルに対してクエリを実行するときに、パフォーマンス最適化パーサーを使用できます。 詳細については、[PARSER_VERSION](develop-openrowset.md) に関する記事を参照してください。

### <a name="manually-create-statistics-for-csv-files"></a>CSV ファイルの統計を手動で作成する

サーバーレス SQL プールでは、統計に基づいて最適なクエリ実行プランを生成します。 Parquet ファイルの列の統計情報は、必要に応じて自動的に作成されます。 現在、CSV ファイルの列の統計情報は、自動作成されません。 クエリで使用する列の統計情報、特に DISTINCT、JOIN、WHERE、ORDER BY、GROUP BY で使用する統計情報は手動で作成します。 詳細については、[サーバーレス SQL プールの統計情報](develop-tables-statistics.md#statistics-in-serverless-sql-pool)に関するトピックを確認してください。

## <a name="data-types"></a>データ型

ここでは、サーバーレス SQL プールでデータ型を使用する際のベスト プラクティスを説明します。

### <a name="use-appropriate-data-types"></a>適切なデータ型を使用する

クエリで使用するデータ型はパフォーマンスとコンカレンシーに影響します。 パフォーマンスは、次のガイドラインに従って向上させることができます。 

- 想定する最大値に対応できる、できる限り小さいデータ サイズを使用します。
  - 文字値の最大長が 30 文字の場合は、長さが 30 の文字データ型を使用します。
  - 文字の列の値のサイズが固定である場合は、**char** か **nchar** を使用します。 それ以外の場合は、**varchar** または **nvarchar** を使用します。
  - 整数列の最大値が 500 の場合は、**smallint** を使用します。この値に対応する最小のデータ型であるためです。 整数のデータ型の範囲は、[この記事で](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true)ご確認いただけます。
- 可能であれば、**nvarchar** と **nchar** ではなく、**varchar** と **char** を使用します。
  - Parquet、Azure Cosmos DB、Delta Lake、UTF-8 でエンコードした CSV のデータを読み取る場合、一部の UTF8 照合順序では **varchar** 型を使用します。
  - Unicode 形式ではない (ASCII など) CSV ファイル からデータを読み取る場合は、UTF8 照合順序を使用せずに **varchar** 型を使用します。
  - UTF-16 の CSV ファイルからデータを読み取る場合は、**nvarchar** 型を使用します。
- 可能であれば、整数ベースのデータ型を使用します。 SORT、JOIN、および GROUP BY 操作は、文字データよりも整数で速く完了します。
- スキーマ推定機能を使用する場合は、[推定されたデータ型を確認](#check-inferred-data-types)し、可能であれば、サイズの小さなデータ型でそれらを明示的に上書きします。

### <a name="check-inferred-data-types"></a>推論されたデータ型を確認する

[スキーマ推論](query-parquet-files.md#automatic-schema-inference)を使用すると、ファイル スキーマを知らなくてもクエリをすばやく作成し、データを探索することができます。 この利便性の代償として、推定されたデータ型は、実際のデータ型よりもサイズが大きい場合があります。 適切なデータ型を使用するのに必要な十分な情報がソース ファイルにない場合に、このような不一致が起こります。 たとえば、Parquet ファイルには最大文字列長のメタデータが含まれません。 このため、サーバーレス SQL プールでは、varchar(8000) と推測されます。

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
|----------------|---------------------|----------|--------------------|-------------------|
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

ここでは、サーバーレス SQL プールでクエリを実行する際のベスト プラクティスを説明します。

### <a name="push-wildcards-to-lower-levels-in-the-path"></a>パスの下位階層にワイルドカードを指定する

ご自分のパスでワイルドカードを使用して、[複数のファイルとフォルダー](query-data-storage.md#query-multiple-files-or-folders)にクエリを実行できます。 サーバーレス SQL プールでは、ストレージ API を使用して、ストレージ アカウントにあるファイルのうち、アスタリスク (*) で始まる表現に該当するもののリストを表示します。 指定したパスと一致しないファイルは除外します。 最初のワイルドカードまで、指定したパスに一致するファイルが多数ある場合は、ファイルの最初の一覧を減らすことで、パフォーマンスが向上する可能性があります。

### <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>filename および filepath 関数を使用して特定のパーティションを対象にする

多くの場合、データはパーティションに編成されます。 特定のフォルダーやファイルに対してクエリを実行するよう、サーバーレス SQL プールに指示することができます。 そうすれば、クエリで読み込んで処理する必要があるファイルの数とデータの分量を減らせます。 副次的効果として、パフォーマンスが向上します。

詳細については、[filename](query-data-storage.md#filename-function) 関数、[filepath](query-data-storage.md#filepath-function) 関数、および[特定のファイルにクエリを実行する](query-specific-files.md)例を参照してください。

> [!TIP]
> filepath 関数と filename 関数の結果は、常に適切なデータ型にキャストしてください。 文字データ型を使用する場合は、適切な長さを確実に使用します。

現在、パーティションの削除、filepath および filename 用に使用される関数は、Apache Spark for Azure Synapse Analytics で作成された各テーブルに対して自動的に作成されたもの以外の外部テーブルに対してはサポートされていません。

格納されているデータがパーティション分割されていない場合は、パーティション分割を検討してください。 そうすると、それらの関数を使用して、それらのファイルを対象とするクエリを最適化することができます。 サーバーレス SQL プールから、[パーティション分割した Apache Spark for Azure Synapse のテーブルに対するクエリを実行する](develop-storage-files-spark-tables.md)と、自動的に、必要なファイルだけがクエリのターゲットになります。

### <a name="use-proper-collation-to-utilize-predicate-pushdown-for-character-columns"></a>適切な照合順序を使用して、文字型の列に述語のプッシュダウンを利用する

Parquet ファイルのデータは行グループにまとめられています。 サーバーレス SQL プールでは、WHERE 句で指定した述語に基づいて行グループをスキップし、IO を減らします。 その結果、クエリのパフォーマンスが向上します。

Parquet ファイルにある文字列の列の述語のプッシュダウンは、Latin1_General_100_BIN2_UTF8 照合順序でのみサポートしています。 WITH 句で特定の列の照合順序を指定できます。 照合順序を WITH 句で指定しない場合は、テータベースの照合順序を使用します。

## <a name="optimize-repeating-queries"></a>繰り返しクエリの最適化

ここでは、サーバーレス SQL プールで CETAS を使用する際のベスト プラクティスを説明します。

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>CETAS を使用してクエリのパフォーマンスと結合を強化する

[CETAS](develop-tables-cetas.md) は、サーバーレス SQL プールで利用できる最も重要な機能の 1 つです。 CETAS は、外部テーブルのメタデータを作成し、SELECT クエリの結果をストレージ アカウント内の一連のファイルにエクスポートする並列操作です。

CETAS を使用して、結合された参照テーブルなど、頻繁に使用されるクエリの部分を新しいファイル セットに具現化できます。 次に、複数のクエリで共通の結合を繰り返す代わりに、この単一の外部テーブルに結合することができます。

CETAS によって Parquet ファイルを生成しつつ、この外部テーブルが最初のクエリのターゲットになったときに、統計情報を自動的に作成します。 その結果、CETAS で生成するテーブルをターゲットにするそれ以降のクエリのパフォーマンスが向上します。

## <a name="next-steps"></a>次のステップ

[トラブルシューティング](resources-self-help-sql-on-demand.md)に関する記事で、一般的な問題と解決方法を確認します。 サーバーレス SQL プールではなく専用 SQL プールを使用している場合は、[専用 SQL プールのベスト プラクティス](best-practices-dedicated-sql-pool.md)に関する記事で、具体的なガイダンスをご覧ください。
