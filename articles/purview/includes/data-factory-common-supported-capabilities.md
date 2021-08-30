---
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 08/10/2021
ms.openlocfilehash: 20a9b85c2d68e1c9b3c33362f393eaec0e255ce7
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2021
ms.locfileid: "122201886"
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
| Azure File Storage | はい | 
| Azure SQL Database \* | はい | 
| Azure SQL Managed Instance \* | はい | 
| Azure Synapse Analytics \* | はい | 
| Azure Table Storage | はい |
| Amazon S3 | はい | 
| Hive \* | はい | 
| SAP テーブル *(SAP ECC または SAP S/4HANA に接続中)* | はい |
| SQL Server \* | はい | 
| Teradata \* | はい |

*\* Azure Purview は現在、系列またはスキャンのためのクエリやストアド プロシージャをサポートしていません。系列は、テーブルとビューのソースだけに制限されています。*

> [!Note]
> コピー アクティビティでは、系列の機能で特定のパフォーマンスのオーバーヘッドが発生します。 Purview で接続を設定している場合は、特定のコピー ジョブが完了するまでの時間が長くなることがあります。 この影響は多くの場合、とても無視できません。 コピー ジョブが完了するまでの時間が通常より大幅に長くなっている場合は、その時間の比較をサポートに連絡してください。

#### <a name="known-limitations-on-copy-activity-lineage"></a>コピー アクティビティの系列に関する既知の制限事項

現在、次のコピー アクティビティの機能を使用する場合、系列はまだサポートされていません。

- バイナリ形式を使用して Azure Data Lake Storage Gen1 にデータをコピーする。
- PolyBase または COPY ステートメントを使用して、Azure Synapse Analytics にデータをコピーする。
- バイナリ、区切りテキスト、Excel、JSON、および XML ファイルの圧縮設定。
- Azure SQL Database、Azure SQL Managed Instance、Azure Synapse Analytics、SQL Server、および SAP テーブルのソース パーティション オプション。
- ファイルベースのストアのソース パーティション検出オプション。
- ファイルごとの最大行数設定を使用して、ファイルベースのシンクにデータをコピーする。
- コピー中に列を追加する。

データ系列の追加では、次のコネクタのデータ資産スキーマ ([資産] -> [スキーマ] タブに表示) がレポートされます。

- Azure Blob、Azure File Storage、ADLS Gen1、ADLS Gen2、Amazon S3 の CSV ファイルと Parquet ファイル
- Azure Data Explorer、Azure SQL Database、Azure SQL Managed Instance、Azure Synapse Analytics、SQL Server、Teradata
