---
title: SQL オンデマンド (プレビュー) を使用して Spark テーブルに対してクエリを実行する
description: SQL オンデマンド (プレビュー) を使用して Spark テーブルに対してクエリを実行する方法の概要
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 41e31a322a3d771557474fdf5c318960822bcfe1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420076"
---
# <a name="query-spark-tables-with-azure-synapse-analytics-using-sql-on-demand-preview"></a>SQL オンデマンド (プレビュー) を使用して Azure Synapse Analytics で Spark テーブルに対してクエリを実行する

SQL オンデマンド (プレビュー) は、Synapse ワークスペース (プレビュー) 内の Spark プールのメタデータを自動的に同期することができます。 Spark プール (プレビュー) に存在するデータベースごとに、SQL オンデマンド データベースが作成されます。 Parquet または CSV に基づく各 Spark テーブルに対して、SQL オンデマンド データベースに外部テーブルが作成されます。 そのため、Spark プールをシャットダウンしても、SQL オンデマンドから Spark テーブルに対するクエリを実行できます。

テーブルが Spark でパーティション分割されている場合、ストレージ内のファイルはフォルダーごとに整理されます。 SQL オンデマンドでは、パーティション メタデータが使用され、クエリに関連するフォルダーとファイルのみが対象になります。

メタデータの同期は、Azure Synapse ワークスペースにプロビジョニングされた各 Spark プールに対して自動的に構成されます。 Spark テーブルに対するクエリをすぐに開始できます。

各 Spark テーブルは、SQL オンデマンド データベースに対応する dbo スキーマの外部テーブルで表されます。 Spark テーブルのクエリの場合は、外部 [spark_table] を対象とするクエリを実行します。 次の例を実行する前に、ファイルが配置されている[ストレージ アカウントへの適切なアクセス許可](develop-storage-files-storage-access-control.md)を持っていることを確認してください。

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="spark-data-types-to-sql-data-types-mapping"></a>Spark データ型から SQL データ型へのマッピング

| Spark データ型 | SQL データ型               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| ShortType       | smallint                    |
| IntegerType     | INT                         |
| LongType        | bigint                      |
| FloatType       | real                        |
| DoubleType      | float                       |
| DecimalType     | decimal                     |
| TimestampType   | datetime2                   |
| DateType        | date                        |
| StringType      | varchar(max)*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar(max)* (JSON へ)** |
| MapType         | varchar(max)* (JSON へ)** |
| StructType      | varchar(max)* (JSON へ)** |

\* 使用される照合順序は Latin1_General_100_BIN2_UTF8 です。

** ArrayType、MapType、および StructType は JSON として表されます。



## <a name="next-steps"></a>次のステップ

ストレージ アクセス制御の詳細については、[ストレージ アクセス制御](develop-storage-files-storage-access-control.md)に関する記事をご覧ください。
