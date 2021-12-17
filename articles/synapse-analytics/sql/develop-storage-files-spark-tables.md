---
title: サーバーレス SQL プールで Apache Spark の外部テーブル定義を同期する
description: サーバーレス SQL プールを使用して Spark テーブルに対してクエリを実行する方法の概要
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 10/05/2021
author: maburd
ms.author: maburd
ms.reviewer: jrasnick, wiassaf
ms.openlocfilehash: d26dfd80e6faf0292cea35c82bc2ec529dc481ab
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129996002"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-serverless-sql-pool"></a>サーバーレス SQL プールで Apache Spark for Azure Synapse の外部テーブル定義を同期する

サーバーレス SQL プールにより、Apache Spark からメタデータを自動的に同期することができます。 サーバーレス Apache Spark プール内にあるデータベースごとに、サーバーレス SQL プール データベースが作成されます。 

Parquet または CSV に基づく、Azure Storage に配置された各 Spark 外部テーブルに対して、サーバーレス SQL プール データベースに外部テーブルが作成されます。 そのため、Spark プールをシャットダウンしても、サーバーレス SQL プールから Spark 外部テーブルに対するクエリを引き続き実行できます。

テーブルが Spark でパーティション分割されている場合、ストレージ内のファイルはフォルダーごとに整理されます。 サーバーレス SQL プールでは、パーティション メタデータが使用され、クエリに関連するフォルダーとファイルのみが対象になります。

メタデータの同期は、Azure Synapse ワークスペースにプロビジョニングされた各サーバーレス Apache Spark プールに対して自動的に構成されます。 Spark 外部テーブルに対するクエリをすぐに開始できます。

Spark の Azure Storage に配置された各 Parquet または CSV 外部テーブルは、サーバーレス SQL プール データベースに対応する dbo スキーマの外部テーブルで表されます。 

Spark 外部テーブルのクエリの場合は、外部 [spark_table] を対象とするクエリを実行します。 次の例を実行する前に、ファイルが配置されている[ストレージ アカウントへの適切なアクセス許可](develop-storage-files-storage-access-control.md)を持っていることを確認してください。

```sql
SELECT * FROM [db].dbo.[spark_table]
```

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Apache Spark データ型から SQL データ型へのマッピング

Apache Spark データ型から SQL データ型へのマッピングの詳細については、「[Azure Synapse Analytics の共有メタデータ テーブル](../metadata/table.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

ストレージ アクセス制御の詳細については、[ストレージ アクセス制御](develop-storage-files-storage-access-control.md)に関する記事をご覧ください。
