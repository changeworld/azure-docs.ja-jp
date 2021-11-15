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
ms.openlocfilehash: 8e7ec847de0498d91e0f294a3a60eab3a4018d48
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853645"
---
# <a name="supported-data-stores"></a>サポートされているデータ ストア

Purview では、次のデータ ストアがサポートされています。 各データ ストアを選択すると、サポートされている機能と対応する構成の詳細を確認できます。

## <a name="purview-data-sources"></a>Purview のデータ ソース

|**カテゴリ**|  **Data Store (データ ストア)**  |**技術メタデータ** |**分類** | **アクセス ポリシー** |**系列** |
|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| [はい](register-scan-azure-blob-storage-source.md#register) | [あり](register-scan-azure-blob-storage-source.md#scan)| はい | 制限あり**|
||    [Azure Cosmos DB](register-scan-azure-cosmos-database.md)| [あり](register-scan-azure-cosmos-database.md#register) | [あり](register-scan-azure-cosmos-database.md#scan)|いいえ|いいえ**|
||    [Azure Data Explorer](register-scan-azure-data-explorer.md)| [はい](register-scan-azure-data-explorer.md#register) | [あり](register-scan-azure-data-explorer.md#scan)| いいえ | いいえ** |
||    [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)| [あり](register-scan-adls-gen1.md#register) | [あり](register-scan-adls-gen1.md#scan)| いいえ | 制限あり** |
||    [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)| [あり](register-scan-adls-gen2.md#register) | [あり](register-scan-adls-gen2.md#scan)| はい | 制限あり** |
||    [Azure 専用 SQL プール (以前の SQL DW)](register-scan-azure-synapse-analytics.md)| [あり](register-scan-azure-synapse-analytics.md#register) | [あり](register-scan-azure-synapse-analytics.md#scan)| いいえ | いいえ** |
||    [Azure Files](register-scan-azure-files-storage-source.md)|[あり](register-scan-azure-files-storage-source.md#register) | [あり](register-scan-azure-files-storage-source.md#scan) |  いいえ | 制限あり** |
||    [Azure SQL Database](register-scan-azure-sql-database.md)| [あり](register-scan-azure-sql-database.md#register) |[あり](register-scan-azure-sql-database.md#scan)| いいえ | いいえ** |
||    [Azure SQL Database マネージド インスタンス](register-scan-azure-sql-database-managed-instance.md)|  [あり](register-scan-azure-sql-database-managed-instance.md#scan) | [あり](register-scan-azure-sql-database-managed-instance.md#scan) | いいえ | いいえ** |
||    [Azure Synapse Analytics (ワークスペース)](register-scan-synapse-workspace.md)| [あり](register-scan-synapse-workspace.md#register) | [あり](register-scan-synapse-workspace.md#scan)| いいえ| [はい - Synapse パイプライン](how-to-lineage-azure-synapse-analytics.md)|
||    [Azure Database for MySQL](register-scan-azure-mysql-database.md)| [あり](register-scan-azure-mysql-database.md#register) | [あり](register-scan-azure-mysql-database.md#scan) | いいえ | いいえ** |
||    [Azure Database for PostgreSQL](register-scan-azure-postgresql.md)| [あり](register-scan-azure-postgresql.md#register) | [あり](register-scan-azure-postgresql.md#scan) | いいえ | いいえ** |
|データベース|    [Cassandra](register-scan-cassandra-source.md)|[あり](register-scan-cassandra-source.md#register) | いいえ | いいえ| [あり](how-to-lineage-cassandra.md)|
||    [Google BigQuery](register-scan-google-bigquery-source.md)| [はい](register-scan-google-bigquery-source.md#register)| いいえ | いいえ| [はい](how-to-lineage-google-bigquery.md)|
||    [Hive メタストア DB](register-scan-hive-metastore-source.md)| [あり](register-scan-hive-metastore-source.md#register) | いいえ | いいえ| はい** |
||    [Oracle DB](register-scan-oracle-source.md)| [はい](register-scan-oracle-source.md#register)|  いいえ | いいえ| [はい**](how-to-lineage-oracle.md)|
||    [SQL Server](register-scan-on-premises-sql-server.md)| [はい](register-scan-on-premises-sql-server.md#register) |[あり](register-scan-on-premises-sql-server.md#scan) | いいえ| いいえ** |
||    [Teradata](register-scan-teradata-source.md)| [はい](register-scan-teradata-source.md#register)|  いいえ | いいえ| [はい**](how-to-lineage-teradata.md)|
|ファイル|[Amazon S3](register-scan-amazon-s3.md)|[はい](register-scan-amazon-s3.md)| [あり](register-scan-amazon-s3.md)| いいえ| 制限あり** |
|サービスとアプリ|    [Erwin](register-scan-erwin-source.md)| [はい](register-scan-erwin-source.md#register)| いいえ | いいえ| [はい](how-to-lineage-erwin.md)|
||    [Looker](register-scan-looker-source.md)| [はい](register-scan-looker-source.md#register)| いいえ | いいえ| [はい](how-to-lineage-looker.md)|
||    [Power BI](register-scan-power-bi-tenant.md)| [はい](register-scan-power-bi-tenant.md#register)| いいえ | いいえ| [はい](how-to-lineage-powerbi.md)|
||    [SAP ECC](register-scan-sapecc-source.md)| [はい](register-scan-sapecc-source.md#register)| いいえ | いいえ| [はい**](how-to-lineage-sapecc.md)|
||    [SAP S4HANA](register-scan-saps4hana-source.md)| [あり](register-scan-saps4hana-source.md#register)| いいえ | いいえ| [はい**](how-to-lineage-sapecc.md)|

\* Purview では、増分スキャンのために Azure Database for MySQL の UPDATE_TIME メタデータが利用されます。 場合によっては、このフィールドがデータベースに保持されておらず、フルスキャンが実行されることがあります。 詳細については、MySQL の [INFORMATION_SCHEMA TABLES テーブル](https://dev.mysql.com/doc/refman/5.7/en/information-schema-tables-table.html)を参照してください。 

\** データセットが [Data Factory Copy アクティビティ](how-to-link-azure-data-factory.md)でソース/シンクとして使用される場合、系列はサポートされています 

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
