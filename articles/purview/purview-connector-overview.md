---
title: Purview コネクタの概要
description: この記事では、Purview でサポートされているさまざまなデータ ストアと機能について説明します
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 08b22af8743082bab1d547205e51917cb9d92a11
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695772"
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
||[Azure SQL Database マネージド インスタンス](register-scan-azure-sql-database-managed-instance.md)|はい| はい| いいえ| はい| はい| ○|
||[Azure Synapse Analytics (以前の SQL DW)](register-scan-azure-synapse-analytics.md)|○| はい| いいえ| はい| はい| ○|
|データベース|[Oracle DB](register-scan-oracle-source.md)|はい| はい| いいえ| いいえ| いいえ| はい|
||[SQL Server](register-scan-on-premises-sql-server.md)|○| はい| いいえ| はい| はい| はい|
||[Teradata](register-scan-teradata-source.md)|はい| はい| いいえ| いいえ| いいえ| はい|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|はい| はい| いいえ| いいえ| いいえ| はい|
|サービスとアプリ|[SAP ECC](register-scan-sapecc-source.md)|はい| はい| いいえ| はい| はい| はい|
||[SAP S4HANA](register-scan-saps4hana-source.md)|はい| はい| いいえ| はい| はい| はい|

## <a name="next-steps"></a>次のステップ

- [Azure Blob Storage ソースの登録とスキャン](register-scan-azure-blob-storage-source.md)