---
title: Purview コネクタの概要
description: この記事では、Purview でサポートされているさまざまなデータ ストアと機能について説明します
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 58b8ef4959c54e8c597e3f54864caae77dfd1fa8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131061297"
---
# <a name="supported-data-stores"></a>サポートされているデータ ストア

Purview では、次のデータ ストアがサポートされています。 各データ ストアを選択すると、サポートされている機能と対応する構成の詳細を確認できます。

## <a name="purview-data-sources"></a>Purview のデータ ソース

|**カテゴリ**|  **Data Store (データ ストア)**  |**メタデータの抽出**|**フル スキャン**|**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| [はい](register-scan-azure-blob-storage-source.md#register) | [あり](register-scan-azure-blob-storage-source.md#scan)|[あり](register-scan-azure-blob-storage-source.md#scan) | [あり](register-scan-azure-blob-storage-source.md#scan)|[あり](register-scan-azure-blob-storage-source.md#scan)| はい | いいえ|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)| [あり](register-scan-azure-cosmos-database.md#register) | [あり](register-scan-azure-cosmos-database.md#scan)|[あり](register-scan-azure-cosmos-database.md#scan) | [あり](register-scan-azure-cosmos-database.md#scan)|[あり](register-scan-azure-cosmos-database.md#scan)|いいえ|いいえ|
||[Azure Data Explorer](register-scan-azure-data-explorer.md)| [はい](register-scan-azure-data-explorer.md#register) | [あり](register-scan-azure-data-explorer.md#scan) | [あり](register-scan-azure-data-explorer.md#scan) | [あり](register-scan-azure-data-explorer.md#scan)| [あり](register-scan-azure-data-explorer.md#scan)| いいえ | いいえ |
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)| [あり](register-scan-adls-gen1.md#register) | [あり](register-scan-adls-gen1.md#scan)|[あり](register-scan-adls-gen1.md#scan) | [あり](register-scan-adls-gen1.md#scan)|[あり](register-scan-adls-gen1.md#scan)| いいえ |[Data Factory のデータ系列](how-to-link-azure-data-factory.md) |
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)| [あり](register-scan-adls-gen2.md#register) | [あり](register-scan-adls-gen2.md#scan)|[あり](register-scan-adls-gen2.md#scan) | [あり](register-scan-adls-gen2.md#scan)|[あり](register-scan-adls-gen2.md#scan)| はい | [Data Factory のデータ系列](how-to-link-azure-data-factory.md) |
||[Azure 専用 SQL プール (以前の SQL DW)](register-scan-azure-synapse-analytics.md)| [あり](register-scan-azure-synapse-analytics.md#register) | [あり](register-scan-azure-synapse-analytics.md#scan)| [あり](register-scan-azure-synapse-analytics.md#scan)| [あり](register-scan-azure-synapse-analytics.md#scan)| [あり](register-scan-azure-synapse-analytics.md#scan)| いいえ | いいえ|
||[Azure Files](register-scan-azure-files-storage-source.md)|[あり](register-scan-azure-files-storage-source.md#register) | [あり](register-scan-azure-files-storage-source.md#scan) | [あり](register-scan-azure-files-storage-source.md#scan) | [あり](register-scan-azure-files-storage-source.md#scan) | [あり](register-scan-azure-files-storage-source.md#scan) | いいえ | いいえ |
||[Azure SQL Database](register-scan-azure-sql-database.md)| [あり](register-scan-azure-sql-database.md#register) | [あり](register-scan-azure-sql-database.md#scan)|[あり](register-scan-azure-sql-database.md#scan) | [あり](register-scan-azure-sql-database.md#scan)|[あり](register-scan-azure-sql-database.md#scan)| いいえ |[Data Factory のデータ系列](how-to-link-azure-data-factory.md)|
||[Azure SQL Database マネージド インスタンス](register-scan-azure-sql-database-managed-instance.md)| [あり](register-scan-azure-sql-database-managed-instance.md#register) | [あり](register-scan-azure-sql-database-managed-instance.md#scan)| [あり](register-scan-azure-sql-database-managed-instance.md#scan) | [あり](register-scan-azure-sql-database-managed-instance.md#scan) | [あり](register-scan-azure-sql-database-managed-instance.md#scan) | いいえ | [Data Factory のデータ系列](how-to-link-azure-data-factory.md) |
||[Azure Synapse Analytics (ワークスペース)](register-scan-synapse-workspace.md)| [あり](register-scan-synapse-workspace.md#register) | [あり](register-scan-synapse-workspace.md#scan)| [あり](register-scan-synapse-workspace.md#scan) | [あり](register-scan-synapse-workspace.md#scan)| [あり](register-scan-synapse-workspace.md#scan)| いいえ| [あり](how-to-lineage-azure-synapse-analytics.md)|
||[Azure Database for MySQL](register-scan-azure-mysql-database.md)| [あり](register-scan-azure-mysql-database.md#register) | [あり](register-scan-azure-mysql-database.md#scan)| [はい*](register-scan-azure-mysql-database.md#scan) | [あり](register-scan-azure-mysql-database.md#scan) | [あり](register-scan-azure-mysql-database.md#scan) | いいえ | [Data Factory のデータ系列](how-to-link-azure-data-factory.md) |
||[Azure Database for PostgreSQL](register-scan-azure-postgresql.md)| [あり](register-scan-azure-postgresql.md#register) | [あり](register-scan-azure-postgresql.md#scan)| [あり](register-scan-azure-postgresql.md#scan) | [あり](register-scan-azure-postgresql.md#scan) | [あり](register-scan-azure-postgresql.md#scan) | いいえ | [Data Factory のデータ系列](how-to-link-azure-data-factory.md) |
|データベース|[Cassandra](register-scan-cassandra-source.md)|[あり](register-scan-cassandra-source.md#register) | [あり](register-scan-cassandra-source.md#scan)| いいえ | いいえ | いいえ | いいえ| [あり](how-to-lineage-cassandra.md)|
||[Google BigQuery](register-scan-google-bigquery-source.md)| [はい](register-scan-google-bigquery-source.md#register)| [あり](register-scan-google-bigquery-source.md#scan)| いいえ | いいえ | いいえ | いいえ| [はい](how-to-lineage-google-bigquery.md)|
||[Hive メタストア DB](register-scan-hive-metastore-source.md)| [あり](register-scan-hive-metastore-source.md#register)| [あり](register-scan-hive-metastore-source.md#scan)| いいえ | いいえ | いいえ | いいえ| はい |
||[Oracle DB](register-scan-oracle-source.md)| [はい](register-scan-oracle-source.md#register)| [あり](register-scan-oracle-source.md#scan)| いいえ | いいえ | いいえ | いいえ| [はい](how-to-lineage-oracle.md)|
||[SQL Server](register-scan-on-premises-sql-server.md)| [はい](register-scan-on-premises-sql-server.md#register) | [あり](register-scan-on-premises-sql-server.md#scan) | [あり](register-scan-on-premises-sql-server.md#scan) | [あり](register-scan-on-premises-sql-server.md#scan) | [あり](register-scan-on-premises-sql-server.md#scan) | いいえ| [Data Factory のデータ系列](how-to-link-azure-data-factory.md) |
||[Teradata](register-scan-teradata-source.md)| [はい](register-scan-teradata-source.md#register)| [あり](register-scan-teradata-source.md#scan)| いいえ | いいえ | いいえ | いいえ| [はい](how-to-lineage-teradata.md)|
|ファイル|[Amazon S3](register-scan-amazon-s3.md)|[はい](register-scan-amazon-s3.md)| [あり](register-scan-amazon-s3.md)| [あり](register-scan-amazon-s3.md)| [あり](register-scan-amazon-s3.md)| [あり](register-scan-amazon-s3.md)| いいえ| はい|
|サービスとアプリ|[Erwin](register-scan-erwin-source.md)| [はい](register-scan-erwin-source.md#register)| [あり](register-scan-erwin-source.md#scan)| いいえ | いいえ | いいえ | いいえ| [はい](how-to-lineage-erwin.md)|
||[Looker](register-scan-looker-source.md)| [はい](register-scan-looker-source.md#register)| [あり](register-scan-looker-source.md#scan)| いいえ | いいえ | いいえ | いいえ| [はい](how-to-lineage-looker.md)|
||[Power BI](register-scan-power-bi-tenant.md)| [はい](register-scan-power-bi-tenant.md#register)| [あり](register-scan-power-bi-tenant.md#scan)| いいえ | いいえ | いいえ | いいえ| [はい](how-to-lineage-powerbi.md)|
||[SAP ECC](register-scan-sapecc-source.md)| [はい](register-scan-sapecc-source.md#register)| [あり](register-scan-sapecc-source.md#scan)| いいえ | いいえ | いいえ | いいえ| [あり](how-to-lineage-sapecc.md)|
||[SAP S4HANA](register-scan-saps4hana-source.md)| [あり](register-scan-saps4hana-source.md#register)| [あり](register-scan-saps4hana-source.md#scan)| いいえ | いいえ | いいえ | いいえ| [はい](how-to-lineage-sapecc.md)|

\* Purview では、増分スキャンのために Azure Database for MySQL の UPDATE_TIME メタデータが利用されます。 場合によっては、このフィールドがデータベースに保持されておらず、フルスキャンが実行されることがあります。 詳細については、MySQL の「[The INFORMATION_SCHEMA TABLES Table](https://dev.mysql.com/doc/refman/5.7/en/information-schema-tables-table.html)」(INFORMATION_SCHEMA TABLES テーブル) を参照してください。 

> [!NOTE]
> 現時点で、Purview ではその名前に `/`、`\`、または `#` を含む資産をスキャンできません。 スキャンの範囲を指定して、資産名にそれらの文字が含まれる資産をスキャンしないようにするには、「[Azure SQL Database の登録とスキャン](register-scan-azure-sql-database.md#creating-the-scan)」の例を使用します。

\* Purview では、増分スキャンのために Azure Database for MySQL の UPDATE_TIME メタデータが利用されます。 場合によっては、このフィールドがデータベースに保持されておらず、フルスキャンが実行されることがあります。 詳細については、MySQL の「[The INFORMATION_SCHEMA TABLES Table](https://dev.mysql.com/doc/refman/5.7/en/information-schema-tables-table.html)」(INFORMATION_SCHEMA TABLES テーブル) を参照してください。 

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
