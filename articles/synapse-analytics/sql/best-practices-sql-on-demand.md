---
title: Azure Synapse Analytics の SQL オンデマンド (プレビュー) のベスト プラクティス
description: Synapse SQL オンデマンド (プレビュー) を操作する場合に知っておくべき推奨事項とベスト プラクティス。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 0015beadfea61fc31bf3f37232105b9cfd2ced71
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692160"
---
# <a name="best-practices-for-sql-on-demand-preview-in-azure-synapse-analytics"></a>Azure Synapse Analytics の SQL オンデマンド (プレビュー) のベスト プラクティス

この記事では、SQL オンデマンド (プレビュー) の使用に関する一連のベスト プラクティスについて説明します。 SQL オンデマンドは、Azure Synapse Analytics 内の追加リソースです。

## <a name="general-considerations"></a>一般的な考慮事項

SQL オンデマンドを使用すると、Azure ストレージ アカウント内のファイルに対してクエリを実行できます。 ローカル ストレージやインジェストの機能は備えていません。 したがって、クエリが対象とするすべてのファイルは SQL オンデマンドの外部にあります。 ストレージからのファイルの読み取りに関連するものはすべて、クエリのパフォーマンスに影響を与える可能性があります。

## <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Azure Storage アカウントと SQL オンデマンドの併置

待機時間を最小限に抑えるには、Azure ストレージ アカウントと SQL オンデマンド エンドポイントを併置します。 ワークスペースの作成中にプロビジョニングされたストレージ アカウントとエンドポイントは同じリージョンに配置されます。

最適なパフォーマンスを得るために、SQL オンデマンドを使用して他のストレージ アカウントにアクセスする場合は、それらが同じリージョンにあることを確認してください。 同じリージョンにない場合、リモートとエンドポイントのリージョン間でデータをネットワーク転送するときの待機時間が長くなります。

## <a name="azure-storage-throttling"></a>Azure Storage の帯域幅調整

複数のアプリケーションとサービスがストレージ アカウントにアクセスする場合があります。 ストレージの帯域幅調整は、アプリケーション、サービス、および SQL オンデマンドのワークロードによって生成される IOPS またはスループットの合計がストレージ アカウントの制限を超えると発生します。 その結果、クエリのパフォーマンスに多大な悪影響が生じます。

帯域幅調整が検出された場合、SQL オンデマンドには、このシナリオの処理が組み込まれています。 SQL オンデマンドは、帯域幅調整が解決されるまで、より遅いペースでストレージへの要求を行います。

> [!TIP]
> 最適なクエリ実行のために、クエリの実行中は他のワークロードでストレージ アカウントに負荷をかけないでください。

## <a name="prepare-files-for-querying"></a>クエリ用のファイルを準備する

可能であれば、ファイルを準備してパフォーマンスを向上させることができます。

- CSV を Parquet に変換 - Parquet は列形式です。 圧縮されているため、同じデータを含む CSV ファイルよりもファイル サイズは小さくなります。 SQL オンデマンドで読み取るために必要な時間とストレージ要求も少なくなります。
- クエリが 1 つの大きなファイルを対象としている場合は、複数の小さなファイルに分割すると効果があります。
- CSV ファイルのサイズを 10 GB 未満にしてください。
- 1 つの OPENROWSET パスまたは外部テーブル LOCATION に対して、ファイルのサイズを同じにすることをお勧めします。
- さまざまなフォルダーまたはファイル名にパーティションを格納することによって、データをパーティション分割します。「[filename および filepath 関数を使用して特定のパーティションを対象にする](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)」を確認してください。

## <a name="push-wildcards-to-lower-levels-in-path"></a>ワイルドカードをパスの下位レベルにプッシュする

ご自分のパスでワイルドカードを使用して、[複数のファイルとフォルダー](develop-storage-files-overview.md#query-multiple-files-or-folders)にクエリを実行できます。 SQL オンデマンドでは、お使いのストレージ アカウントのファイルを、最初に * から、ストレージ API を使用して列挙します。指定されたパスと一致しないファイルは除外されます。 最初のワイルドカードまで、指定したパスに一致するファイルが多数ある場合、ファイルの最初の一覧を減らすとパフォーマンスが向上する可能性があります。

## <a name="use-appropriate-data-types"></a>適切なデータ型を使用する

お使いのクエリで使用するデータ型は、パフォーマンスに影響します。 パフォーマンスは、次のような場合に向上させることができます。 

- 可能な最大値に対応する最小のデータ サイズを使用します。
  - 文字値の最大長が 30 文字の場合は、長さが 30 の文字データ型を使用します。
  - すべての文字列値が固定サイズの場合は、char または nchar を使用します。 それ以外の場合は、varchar または nvarchar を使用します。
  - 整数列の最大値が 500 の場合は、この値に対応する最小のデータ型である smallint を使用します。 整数のデータ型の範囲は、[こちらで](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=sql-server-ver15)ご確認いただけます。
- 可能であれば、nvarchar と nchar ではなく、varchar と char を使用します。
- 可能であれば、整数ベースのデータ型を使用します。 sort、join、group by 操作は、文字データよりも整数で高速に実行されます。
- スキーマ推論を使用している場合は、「[推論されたデータ型を確認する](#check-inferred-data-types)」をご確認ください。

## <a name="check-inferred-data-types"></a>推論されたデータ型を確認する

[スキーマ推論](query-parquet-files.md#automatic-schema-inference)を使用すると、ファイル スキーマを知らなくてもクエリをすばやく作成し、データを探索することができます。 この快適さには、推論されたデータ型が実際よりも大きくなるという犠牲があります。 これは、適切なデータ型が使用されていることを確認できる十分な情報がソースファイルにない場合に発生します。 たとえば、Parquet ファイルは最大文字列長のメタデータを含まず、SQL オンデマンドで varchar (8000) として推論されます。 

ご自分のクエリの結果のデータ型は、[sp_describe_first_results_set](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15) を使用して確認できます。

推論されたデータ型を最適化する方法を次の例に示します。 プロシージャは、推論されたデータ型を示すために使用しています。 
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

結果セットは次のようになります。

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|INT|4|

クエリの推論されるデータ型がわかれば、適切なデータ型を指定できます。

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>fileinfo および filepath 関数を使用して特定のパーティションを対象にする

多くの場合、データはパーティションに編成されます。 特定のフォルダーやファイルに対してクエリを実行するよう、SQL オンデマンドに指示することができます。 この関数により、クエリが読み取って処理する必要があるファイルの数とデータの量が削減されます。 副次的効果として、パフォーマンスが向上します。

詳細については、[filename](develop-storage-files-overview.md#filename-function) 関数、[filepath](develop-storage-files-overview.md#filepath-function) 関数、および、[特定のファイルに対してクエリを実行する](query-specific-files.md)方法の例を参照してください。

> [!TIP]
> filepath 関数と fileinfo 関数の結果は、常に適切なデータ型にキャストしてください。 文字データ型を使用する場合は、適切な長さが使用されていることを確認します。

格納されたデータがパーティション分割されていない場合は、これらの関数を使用してファイルを対象とするクエリを最適化できるよう、データのパーティション分割を検討してください。 SQL オンデマンドから、[パーティション分割された Spark テーブルに対してクエリを実行](develop-storage-files-spark-tables.md)すると、必要なファイルのみが自動的にクエリの対象となります。

## <a name="use-cetas-to-enhance-query-performance-and-joins"></a>CETAS を使用してクエリのパフォーマンスと結合を強化する

[CETAS](develop-tables-cetas.md) は、SQL オンデマンドで利用できる最も重要な機能の 1 つです。 CETAS は、外部テーブルのメタデータを作成し、SELECT クエリの結果をストレージ アカウント内の一連のファイルにエクスポートする並列操作です。

CETAS を使用して、結合された参照テーブルなど、クエリの頻繁に使用される部分を新しいファイル セットに格納できます。 次に、複数のクエリで共通の結合を繰り返す代わりに、この単一の外部テーブルに結合することができます。

CETAS によって Parquet ファイルが生成されると、最初のクエリがこの外部テーブルを対象とするときに統計が自動的に作成され、結果としてパフォーマンスが向上します。

## <a name="next-steps"></a>次のステップ

[トラブルシューティング](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)に関する記事で、一般的な問題と解決方法を確認してください。 SQL オンデマンドではなく SQL プールを使用している場合の具体的なガイダンスについては、[SQL プール向けのベスト プラクティス](best-practices-sql-pool.md)に関する記事を参照してください。
