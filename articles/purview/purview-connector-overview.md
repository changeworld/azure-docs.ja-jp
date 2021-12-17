---
title: Purview コネクタの概要
description: この記事では、Purview でサポートされているさまざまなデータ ストアと機能について説明します
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 11/05/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 59ba1b5bba00eb84d9a4e5dd20cb9f81771097f1
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026389"
---
# <a name="supported-data-stores"></a>サポートされているデータ ストア

Purview では、次のデータ ストアがサポートされています。 各データ ストアを選択すると、サポートされている機能と対応する構成の詳細を確認できます。

## <a name="purview-data-sources"></a>Purview のデータ ソース

|**カテゴリ**|  **Data Store (データ ストア)**  |**技術メタデータ** |**分類** |**系列** | **アクセス ポリシー** |
|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| [はい](register-scan-azure-blob-storage-source.md#register) | [あり](register-scan-azure-blob-storage-source.md#scan)| 上限* | [あり](how-to-access-policies-storage.md) |
||    [Azure Cosmos DB](register-scan-azure-cosmos-database.md)| [あり](register-scan-azure-cosmos-database.md#register) | [あり](register-scan-azure-cosmos-database.md#scan)|いいえ*|いいえ|
||    [Azure Data Explorer](register-scan-azure-data-explorer.md)| [はい](register-scan-azure-data-explorer.md#register) | [あり](register-scan-azure-data-explorer.md#scan)| いいえ* | いいえ |
||    [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)| [あり](register-scan-adls-gen1.md#register) | [あり](register-scan-adls-gen1.md#scan)| 上限* | No |
||    [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)| [あり](register-scan-adls-gen2.md#register) | [あり](register-scan-adls-gen2.md#scan)| 上限* | [あり](how-to-access-policies-storage.md) |
|| [Azure Database for MySQL](register-scan-azure-mysql-database.md) | [あり](register-scan-azure-mysql-database.md#register) | [あり](register-scan-azure-mysql-database.md#scan) | いいえ* | No |
|| [Azure Database for PostgreSQL](register-scan-azure-postgresql.md) | [あり](register-scan-azure-postgresql.md#register) | [あり](register-scan-azure-postgresql.md#scan) | いいえ* | No |
||    [Azure 専用 SQL プール (以前の SQL DW)](register-scan-azure-synapse-analytics.md)| [あり](register-scan-azure-synapse-analytics.md#register) | [あり](register-scan-azure-synapse-analytics.md#scan)| いいえ* | いいえ |
||    [Azure Files](register-scan-azure-files-storage-source.md)|[あり](register-scan-azure-files-storage-source.md#register) | [あり](register-scan-azure-files-storage-source.md#scan) | 上限* |  いいえ |
||    [Azure SQL Database](register-scan-azure-sql-database.md)| [あり](register-scan-azure-sql-database.md#register) |[あり](register-scan-azure-sql-database.md#scan)| いいえ* | No |
||    [Azure SQL Database マネージド インスタンス](register-scan-azure-sql-database-managed-instance.md)|  [あり](register-scan-azure-sql-database-managed-instance.md#scan) | [あり](register-scan-azure-sql-database-managed-instance.md#scan) | いいえ* | No |
||    [Azure Synapse Analytics (ワークスペース)](register-scan-synapse-workspace.md)| [あり](register-scan-synapse-workspace.md#register) | [あり](register-scan-synapse-workspace.md#scan)| [はい - Synapse パイプライン](how-to-lineage-azure-synapse-analytics.md)| No|
|データベース| [Amazon RDS](register-scan-amazon-rds.md) | [あり](register-scan-amazon-rds.md#register-an-amazon-rds-data-source) | [あり](register-scan-amazon-rds.md#scan-an-amazon-rds-database) | いいえ | いいえ |
||    [Cassandra](register-scan-cassandra-source.md)|[あり](register-scan-cassandra-source.md#register) | いいえ | [あり](how-to-lineage-cassandra.md)| いいえ|
||    [Google BigQuery](register-scan-google-bigquery-source.md)| [はい](register-scan-google-bigquery-source.md#register)| いいえ | [あり](how-to-lineage-google-bigquery.md)| いいえ|
|| [Hive メタストア データベース](register-scan-hive-metastore-source.md) | [あり](register-scan-hive-metastore-source.md#register) | いいえ | はい* | いいえ|
|| [MySQL](register-scan-mysql.md) | [あり](register-scan-mysql.md#register) | いいえ | [あり](register-scan-mysql.md#scan) | いいえ |
|| [Oracle](register-scan-oracle-source.md) | [あり](register-scan-oracle-source.md#register)|  いいえ | [はい*](how-to-lineage-oracle.md) | No|
|| [PostgreSQL](register-scan-postgresql.md) | [あり](register-scan-postgresql.md#register) | いいえ | [あり](register-scan-postgresql.md#scan) | いいえ |
||    [SQL Server](register-scan-on-premises-sql-server.md)| [はい](register-scan-on-premises-sql-server.md#register) |[あり](register-scan-on-premises-sql-server.md#scan) | いいえ* | No|
||    [Teradata](register-scan-teradata-source.md)| [はい](register-scan-teradata-source.md#register)|  いいえ | [はい*](how-to-lineage-teradata.md) | いいえ|
|ファイル|[Amazon S3](register-scan-amazon-s3.md)|[はい](register-scan-amazon-s3.md)| [あり](register-scan-amazon-s3.md)| 上限* | No|
|サービスとアプリ|    [Erwin](register-scan-erwin-source.md)| [はい](register-scan-erwin-source.md#register)| いいえ | [あり](how-to-lineage-erwin.md)| いいえ|
||    [Looker](register-scan-looker-source.md)| [はい](register-scan-looker-source.md#register)| いいえ | [あり](how-to-lineage-looker.md)| いいえ|
||    [Power BI](register-scan-power-bi-tenant.md)| [はい](register-scan-power-bi-tenant.md#register)| いいえ | [あり](how-to-lineage-powerbi.md)| いいえ|
|| [Salesforce](register-scan-salesforce.md) | [あり](register-scan-salesforce.md#register) | いいえ | いいえ | いいえ |
||    [SAP ECC](register-scan-sapecc-source.md)| [はい](register-scan-sapecc-source.md#register) | いいえ | [はい*](how-to-lineage-sapecc.md) | No|
|| [SAP S/4HANa](register-scan-saps4hana-source.md) | [あり](register-scan-saps4hana-source.md#register)| いいえ | [はい*](how-to-lineage-sapecc.md) | No|

\* データ ソース内の資産の系列に加えて、データセットが [Data Factory Copy](how-to-link-azure-data-factory.md) または [Synapse パイプライン](how-to-lineage-azure-synapse-analytics.md)でソースまたはシンクとして使用される場合、系列はサポートされています。

> [!NOTE]
> 現時点で、Purview ではその名前に `/`、`\`、または `#` を含む資産をスキャンできません。 スキャンの範囲を指定して、資産名にそれらの文字が含まれる資産をスキャンしないようにするには、「[Azure SQL Database の登録とスキャン](register-scan-azure-sql-database.md#creating-the-scan)」の例を使用します。

## <a name="scan-regions"></a>スキャンのリージョン
Purview スキャナーが実行されるすべての Azure データ ソース (データセンター) リージョンの一覧を次に示します。 Azure データ ソースがこの一覧にないリージョンにある場合、スキャナーは Purview インスタンスのリージョンで実行されます。

### <a name="purview-scanner-regions"></a>Purview スキャナーのリージョン

- EastUs
- EastUs2 
- SouthCentralUS
- WestUs
- WestUs2
- SoutheastAsia
- 西ヨーロッパ
- NorthEurope
- UkSouth
- AustraliaEast
- CanadaCentral
- BrazilSouth
- CentralIndia
- JapanEast
- SouthAfricaNorth
- FranceCentral
- KoreaCentral
- CentralUS
- NorthCentralUS
- EastAsia
- WestCentralUS
- AustraliaSoutheast

## <a name="next-steps"></a>次のステップ

- [Azure Blob Storage ソースの登録とスキャン](register-scan-azure-blob-storage-source.md)