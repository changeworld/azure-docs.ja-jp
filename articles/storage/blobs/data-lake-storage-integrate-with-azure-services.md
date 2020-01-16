---
title: Azure Data Lake Storage と Azure のサービスを統合する | Microsoft Docs
description: Azure Data Lake Storage Gen2 と統合される Azure のサービスについて説明します。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/01/2019
ms.author: normesta
ms.openlocfilehash: de57776ea3bcc7486c5c26182c7e2a65e643d81c
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863004"
---
# <a name="integrate-azure-data-lake-storage-with-azure-services"></a>Azure Data Lake Storage と Azure のサービスを統合する

Azure のサービスを使用して、データを取り込み、分析を実行し、ビジュアル表現を作成することができます。 この記事では、サポートされている Azure のサービスの一覧を示し、これらのサービスを Azure Data Lake Storage Gen2 で使用するときに役立つ記事へのリンクを提供します。

## <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 がサポートされている Azure のサービス

次の表では、Azure Data Lake Storage Gen2 がサポートされている Azure のサービスを示します。

| Azure サービス |  関連記事 |
|---------------|-------------------|
|Azure Data Factory | [Load data into Azure Data Lake Storage Gen2 with Azure Data Factory (Azure Data Factory を使用して Azure Data Lake Storage Gen2 内にデータを読み込む)](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks | [Azure Databricks で使用する](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [クイック スタート:Azure Databricks を使用して Azure Data Lake Storage Gen2 のデータを分析する](data-lake-storage-quickstart-create-databricks-account.md) <br>[チュートリアル:Azure Databricks を使用してデータの抽出、変換、読み込みを行う](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[チュートリアル:Spark を使用して Azure Databricks で Data Lake Storage Gen2 のデータにアクセスする](data-lake-storage-use-databricks-spark.md) |
|Azure Event Hubs のキャプチャ| [Azure Event Hubs で Azure Blob Storage または Azure Data Lake Storage にイベントをキャプチャする](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Logic Apps | [概要 - Azure Logic Apps とは](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|[Azure ストレージ サービスのデータにアクセスする](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Azure Cognitive Search | [Azure Data Lake Storage Gen2 ドキュメントのインデックスと検索 (プレビュー)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Stream Analytics| [クイック スタート:Azure Portal を使用して Stream Analytics ジョブを作成する](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Azure Data Lake Gen2 に出力する](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2) |
|Data Box|  [Azure Data Box を使用してオンプレミス HDFS ストアから Azure Storage にデータを移行する](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight | [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Data Lake Storage Gen2 で HDFS CLI を使用する](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[チュートリアル:Azure HDInsight の Apache Hive を使用したデータの抽出、変換、および読み込み](data-lake-storage-tutorial-extract-transform-load-hive.md) |
|IoT Hub | [IoT Hub メッセージ ルーティングを使用して device-to-cloud メッセージを別のエンドポイントに送信する](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|  [Power BI を使用して Data Lake Storage Gen2 のデータを分析する](data-lake-storage-use-power-bi.md) |
|SQL Data Warehouse | [Azure SQL Data Warehouse で使用する](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|SQL Server Integration Services (SSIS) | [Azure Storage 接続マネージャー](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|

## <a name="next-steps"></a>次のステップ

- データ レイクのデータを処理するために使用できるツールの詳細について確認します。 「[Data Lake Storage Gen2 を使用してビッグ データの要件に対応する](data-lake-storage-data-scenarios.md)」を参照してください。

- Data Lake Storage Gen2 の詳細と、その使用を開始する方法について確認します。 「[Azure Data Lake Storage Gen2 の概要](data-lake-storage-introduction.md)」を参照してください
