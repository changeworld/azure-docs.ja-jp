---
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 09/27/2021
ms.openlocfilehash: 8c4baccdbfb0f6162e666d6901e0080202a3b7e9
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212347"
---
### <a name="copy-activity-support"></a>Copy アクティビティのサポート

| データ ストア | サポートされています | 
| ------------------- | ------------------- | 
| Azure Blob Storage | はい |
| Azure Cognitive Search | はい | 
| Azure Cosmos DB (SQL API) \* | はい | 
| Azure Cosmos DB の MongoDB 用 API \* | はい |
| Azure Data Explorer \* | はい | 
| Azure Data Lake Storage Gen1 | はい | 
| Azure Data Lake Storage Gen2 | はい | 
| Azure Database for Maria DB \* | はい | 
| Azure Database for MySQL \* | はい | 
| Azure Database for PostgreSQL \* | はい |
| Azure Files | はい | 
| Azure SQL Database \* | はい | 
| Azure SQL Managed Instance \* | はい | 
| Azure Synapse Analytics \* | はい | 
| Azure Table Storage | はい |
| Amazon S3 | はい | 
| Hive \* | はい | 
| Oracle \* | はい |
| SAP テーブル *(SAP ECC または SAP S/4HANA に接続中)* | はい |
| SQL Server \* | はい | 
| Teradata \* | はい |

*\* Azure Purview は現在、系列またはスキャンのためのクエリやストアド プロシージャをサポートしていません。系列は、テーブルとビューのソースだけに制限されています。*

セルフホステッド統合ランタイムを使用する場合は、以下に対する系列のサポートを備えた最小バージョンに注意してください。
- Oracle からのデータのコピー: バージョン 5.10 以降
- COPY コマンドまたは PolyBase による Azure Synapse Analytics へのデータのコピー: バージョン 5.10 以降

#### <a name="limitations-on-copy-activity-lineage"></a>コピー アクティビティの系列に関する制限事項

現在、次のコピー アクティビティの機能を使用する場合、系列はまだサポートされていません。

- バイナリ形式を使用して Azure Data Lake Storage Gen1 にデータをコピーする。
- バイナリ、区切りテキスト、Excel、JSON、および XML ファイルの圧縮設定。
- Azure SQL Database、Azure SQL Managed Instance、Azure Synapse Analytics、SQL Server、および SAP テーブルのソース パーティション オプション。
- ファイルごとの最大行数設定を使用して、ファイルベースのシンクにデータをコピーする。

データ系列の追加では、次のコネクタのデータ資産スキーマ ([資産] -> [スキーマ] タブに表示) がレポートされます。

- Azure Blob、Azure Files、ADLS Gen1、ADLS Gen2、Amazon S3 の CSV ファイルと Parquet ファイル
- Azure Data Explorer、Azure SQL Database、Azure SQL Managed Instance、Azure Synapse Analytics、SQL Server、Teradata

### <a name="data-flow-support"></a>Data Flow サポート

| データ ストア | サポートされています |
| ------------------- | ------------------- | 
| Azure Blob Storage | はい |
| Azure Cosmos DB (SQL API) \* | はい | 
| Azure Data Lake Storage Gen1 | はい |
| Azure Data Lake Storage Gen2 | はい |
| Azure Database for MySQL \* | はい | 
| Azure Database for PostgreSQL \* | はい |
| Azure SQL Database \* | はい |
| Azure SQL Managed Instance \* | はい | 
| Azure Synapse Analytics \* | はい |

*\* Azure Purview は現在、系列またはスキャンのためのクエリやストアド プロシージャをサポートしていません。系列は、テーブルとビューのソースだけに制限されています。*

#### <a name="limitations-on-data-flow-lineage"></a>データ フローの系列に関する制限事項

現在、データ フローの系列は、Purview の[リソース セット](../concept-resource-sets.md)と統合されていません。
