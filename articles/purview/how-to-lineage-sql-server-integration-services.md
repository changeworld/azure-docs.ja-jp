---
title: SQL Server Integration Services のデータ系列
description: この記事では、SQL Server Integration Services からのデータ系列の抽出について説明します。
author: chugugrace
ms.author: chugu
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/30/2021
ms.openlocfilehash: 2bdcdf79ee28bf6a3c233decd6dceafce74276ef
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258367"
---
# <a name="how-to-get-lineage-from-sql-server-integration-services-ssis-into-azure-purview"></a>SQL Server Integration Services (SSIS) から Azure Purview にデータ系列を取り込む方法

この記事では、Azure Purview における SQL Server Integration Services (SSIS) のデータ系列の側面について説明します。

## <a name="prerequisites"></a>前提条件

- [SQL Server Integration Services ワークロードをクラウドにリフト アンド シフトする](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)

## <a name="supported-scenarios"></a>サポートされるシナリオ

現在のサポート範囲には、Azure Data Factory の SSIS 統合ランタイムによって実行される SSIS パッケージからのデータ系列の抽出が含まれます。

オンプレミスの SSIS のデータ系列の抽出はまだサポートされていません。

### <a name="supported-data-stores"></a>サポートされているデータ ストア

| データ ストア | サポートされています |
| ------------------- | ------------------- |
| Azure Blob Storage | はい |
| Azure Data Lake Storage Gen1 | はい |
| Azure Data Lake Storage Gen2 | はい |
| Azure Files | はい |
| Azure SQL Database \* | はい |
| Azure SQL Managed Instance \*| はい |
| Azure Synapse Analytics \* | はい |
| SQL Server \* | はい |

*\* Azure Purview は現在、系列またはスキャンのためのクエリやストアド プロシージャをサポートしていません。系列は、テーブルとビューのソースだけに制限されています。*


## <a name="how-to-bring-ssis-lineage-into-purview"></a>SSIS のデータ系列を Purview に取り込む方法

### <a name="step-1-connect-a-data-factory-to-azure-purview"></a>手順 1. [Data Factory を Azure Purview に接続する](how-to-link-azure-data-factory.md)

### <a name="step-2-trigger-ssis-activity-execution-in-azure-data-factory"></a>手順 2. Azure Data Factory で SSIS アクティビティの実行をトリガーする

[SSIS パッケージの実行アクティビティを使用して SSIS パッケージを実行する](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)か、[ADF SSIS 統合ランタイムで Transact-SQL を使用して SSIS パッケージを実行する](../data-factory/how-to-invoke-ssis-package-stored-procedure-activity.md)ことができます。  

SSIS パッケージの実行アクティビティの実行が完了したら、[Data Factory のアクティビティ モニター](../data-factory/monitor-visually.md#monitor-activity-runs)のアクティビティ出力からデータ系列レポートの状態を確認できます。
:::image type="content" source="media/how-to-lineage-sql-server-integration-services/activity-report-lineage-status.png" alt-text="SSIS の状態":::

### <a name="step-3-browse-lineage-information-in-your-azure-purview-account"></a>手順 3. Azure Purview アカウントでデータ系列情報を参照する

- 資産の種類として [SQL Server Integration Services] を選択すると、Data Catalog を参照できます。

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/browse-assets-1.png" alt-text="資産の参照" lightbox="media/how-to-lineage-sql-server-integration-services/browse-assets-1.png":::
:::image type="content" source="media/how-to-lineage-sql-server-integration-services/browse-assets-2.png" alt-text="資産の参照、SSIS":::
:::image type="content" source="media/how-to-lineage-sql-server-integration-services/browse-assets-3.png" alt-text=" パッケージ":::

- キーワードを使用して Data Catalog を検索することもできます。

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/search-assets.png" alt-text="SSIS の検索" lightbox="media/how-to-lineage-sql-server-integration-services/search-assets.png":::

- SSIS パッケージの実行アクティビティのデータ系列情報を表示し、Data Factory で開いてアクティビティ設定を表示/編集できます。

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/lineage.png" alt-text="SSIS のデータ系列の表示" lightbox="media/how-to-lineage-sql-server-integration-services/lineage.png":::

- 1 つのデータ ソースを選択して、変換元の列を変換先の列にマップする方法を詳細に確認できます。

:::image type="content" source="media/how-to-lineage-sql-server-integration-services/lineage-drill-in.png" alt-text="SSIS のデータ系列の表示、詳細確認" lightbox="media/how-to-lineage-sql-server-integration-services/lineage-drill-in.png":::

## <a name="next-steps"></a>次のステップ

- [SQL Server Integration Services ワークロードをクラウドにリフト アンド シフトする](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)
- [Azure Purview におけるデータ系列について](catalog-lineage-user-guide.md)
- [プッシュ自動系列への Azure Data Factory のリンク](how-to-link-azure-data-factory.md)