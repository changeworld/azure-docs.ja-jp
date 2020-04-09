---
title: 'Azure Databricks からさまざまなデータ ソースに接続する '
description: Azure Databricks から Azure SQL Database、Azure Data Lake Store、Blob Storage、Cosmos DB、Event Hubs、Azure SQL Data Warehouse に接続する方法について説明します。
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: 79a821a4c8fe4cb2d048f0dcb0a6e091462a1779
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548801"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Azure Databricks からデータ ソースに接続する

この記事では、Azure Databricks に接続できる Azure 内のさまざまなデータ ソースへのリンクを示します。 リンク先の例に従って、Azure データ ソース (Azure Blob Storage や Azure Event Hubs など) のデータを Azure Databricks クラスターに抽出して分析ジョブを実行します。 

## <a name="prerequisites"></a>前提条件

* Azure Databricks ワークスペースと Spark クラスターを用意する必要があります。 手順については、[Azure Databricks のクイックスタート](quickstart-create-databricks-workspace-portal.md)に関するページをご覧ください。

## <a name="data-sources-for-azure-databricks"></a>Azure Databricks のデータ ソース

次の一覧は、Azure Databricks で使用できる Azure 内のデータ ソースを示しています。 Azure Databricks で使用できるデータ ソースの完全な一覧については、[Azure Databricks のデータ ソース](/azure/databricks/data/data-sources/index)に関する記事を参照してください。

- [Azure SQL データベース](/azure/databricks/data/data-sources/sql-databases)

    このリンクでは、JDBC を使用して SQL データベースに接続するための DataFrame API と、JDBC インターフェイス経由の読み取りの並列処理を制御する方法を示します。 このトピックでは、Scala API の詳細な使用例の他に、最後に Python と Spark SQL の簡潔な例を示します。
- [Azure Data Lake Storage](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    このリンクでは、Azure Active Directory サービス プリンシパルを使用して Azure Data Lake Storage での認証を行う方法の例が提供されます。 Azure Databricks から Azure Data Lake Storage 内のデータにアクセスする方法も示されます。

- [Azure Blob Storage](/azure/databricks/data/data-sources/azure/azure-storage)

    このリンクは、特定のコンテナーのアクセス キーまたは SAS を使用して Azure Databricks から Azure Blob ストレージに直接アクセスする方法の例を示します。 このリンクでは、RDD API を使用して Azure Databricks から Azure Blob Storage にアクセスする方法も示します。

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    このリンクは、Azure Databricks から [Azure Cosmos DB Spark コネクタ](https://github.com/Azure/azure-cosmosdb-spark)を使用して Azure Cosmos DB のデータにアクセスする方法を示します。

- [Azure Event Hubs](/azure/event-hubs/event-hubs-spark-connector)

    このリンクは、Azure Databricks から [Azure Event Hubs Spark コネクタ](https://github.com/Azure/azure-event-hubs-spark)を使用して Azure Event Hubs のデータにアクセスする方法を示します。

- [Azure SQL Data Warehouse](/azure/synapse-analytics/sql-data-warehouse/)

    このリンクは、Azure SQL Data Warehouse コネクタを使用して Azure Databricks から接続する方法を示します。
    

## <a name="next-steps"></a>次のステップ

Azure Databricks にデータをインポートできるソースについては、[Azure Databricks のデータ ソース](/azure/databricks/data/data-sources/index)に関するページをご覧ください。


