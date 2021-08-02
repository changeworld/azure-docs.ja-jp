---
title: Purview コネクタの概要
description: この記事では、Purview でサポートされているさまざまなデータ ストアと機能について説明します
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 05/17/2021
ms.openlocfilehash: 09ed5297bb6432640d6cce7674c2246088823d37
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111750607"
---
# <a name="supported-data-stores"></a>サポートされているデータ ストア

Purview では、次のデータ ストアがサポートされています。 各データ ストアをクリックすると、サポートされている機能と対応する構成の詳細を確認できます。

## <a name="purview-data-sources"></a>Purview のデータ ソース

|**カテゴリ**|  **Data Store (データ ストア)**  |**メタデータの抽出**|**フル スキャン**|**増分スキャン**|**スコープ スキャン**|**分類**|**系列**|
|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| はい| はい| はい| はい| はい| ○|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|はい| はい| はい| はい| はい| ○|
||[Azure Data Explorer](register-scan-azure-data-explorer.md)|○| はい| はい| はい| はい| ○|
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)|○| はい| はい| はい| はい| ○|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)|○| はい| はい| はい| はい| ○|
||[Azure SQL Database](register-scan-azure-sql-database.md)|○| はい| いいえ| はい| はい| ○|
||[Azure SQL Database マネージド インスタンス](register-scan-azure-sql-database-managed-instance.md)|はい| はい| いいえ| はい| はい| はい|
||[Azure 専用 SQL プール (以前の SQL DW)](register-scan-azure-synapse-analytics.md)|はい| はい| いいえ| はい| はい| はい|
||[Azure Synapse Analytics (ワークスペース)](register-scan-synapse-workspace.md)|はい| はい| いいえ| はい| はい| ○|
|データベース|[Hive メタストア DB](register-scan-oracle-source.md)|はい| はい| いいえ| いいえ| いいえ| はい|
||[Oracle DB](register-scan-oracle-source.md)|はい| はい| いいえ| いいえ| いいえ| はい|
||[SQL Server](register-scan-on-premises-sql-server.md)|○| はい| いいえ| はい| はい| はい|
||[Teradata](register-scan-teradata-source.md)|はい| はい| いいえ| いいえ| いいえ| はい|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|はい| はい| いいえ| いいえ| いいえ| はい|
|サービスとアプリ|[SAP ECC](register-scan-sapecc-source.md)|はい| はい| いいえ| はい| はい| はい|
||[SAP S4HANA](register-scan-saps4hana-source.md)|はい| はい| いいえ| はい| はい| はい|
|マルチクラウド|[Amazon S3](register-scan-amazon-s3.md)|はい| はい| いいえ| いいえ| いいえ| はい|

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
