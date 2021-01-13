---
title: Purview コネクタの概要
description: この記事では、Purview でサポートされているさまざまなデータ ストアと機能について説明します
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 88fb9c823df6ae5df345911ccce1c579009fba02
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2021
ms.locfileid: "96780214"
---
# <a name="supported-data-stores"></a>サポートされているデータ ストア

Purview では、次のデータ ストアがサポートされています。 各データ ストアをクリックすると、サポートされている機能と対応する構成の詳細を確認できます。

## <a name="purview-data-sources"></a>Purview のデータ ソース

|**カテゴリ**|  **Data Store (データ ストア)**  |**メタデータの抽出**|**フル スキャン**|**増分スキャン**|**スコープ スキャン**|**分類**|**系列**|
|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| はい| ○| ○| ○| ○| ○|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|はい| ○| ○| ○| ○| ○|
||[Azure Data Explorer](register-scan-azure-data-explorer.md)|○| ○| ○| ○| ○| ○|
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)|○| ○| ○| ○| ○| ○|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)|○| ○| ○| ○| ○| ○|
||[Azure SQL Database](register-scan-azure-sql-database.md)|○| ○| いいえ| ○| ○| ○|
||[Azure SQL Database マネージド インスタンス](register-scan-azure-sql-database-managed-instance.md)|はい| ○| いいえ| ○| ○| ○|
||[Azure Synapse Analytics (以前の SQL DW)](register-scan-azure-synapse-analytics.md)|○| ○| いいえ| ○| ○| ○|
|データベース|[SQL Server](register-scan-on-premises-sql-server.md)|○| ○| いいえ| ○| ○| ○|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|○| はい| いいえ| いいえ| いいえ| はい|

## <a name="next-steps"></a>次のステップ

- [Azure Blob Storage ソースの登録とスキャン](register-scan-azure-blob-storage-source.md)