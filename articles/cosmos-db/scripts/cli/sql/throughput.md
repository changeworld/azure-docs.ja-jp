---
title: Azure Cosmos DB Core (SQL) API リソースに対するスループット (RU/秒) 操作の Azure CLI スクリプト
description: Azure Cosmos DB Core (SQL) API リソースに対するスループット (RU/秒) 操作の Azure CLI スクリプト
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 2df31e5903785b6e25ea79a107a53084849c66fe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789089"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-container-for-azure-cosmos-db-core-sql-api"></a>Azure Cosmos DB Core (SQL) API のデータベースまたはコンテナーに対する Azure CLI でのスループット (RU/秒) 操作
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.12.1 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="sample-script"></a>サンプル スクリプト

このスクリプトでは、共有スループットがある Core (SQL) API データベースと専用スループットがある Core (SQL) API コンテナーが作成され、その後、データベースとコンテナーの両方のスループットが更新されます。 その後、スクリプトは標準スループットから自動スケーリング スループットに移行し、移行後に自動スケーリング スループットの値を読み取ります。

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/throughput.sh "Throughput operations for a SQL database and container.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Azure Cosmos DB アカウントを作成します。 |
| [az cosmosdb sql database create](/cli/azure/cosmosdb/sql/database#az_cosmosdb_sql_database_create) | Azure Cosmos Core (SQL) データベースを作成します。 |
| [az cosmosdb sql container create](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_create) | Azure Cosmos Core (SQL) コンテナーを作成します。 |
| [az cosmosdb sql database throughput update](/cli/azure/cosmosdb/sql/database/throughput#az_cosmosdb_sql_database_throughput_update) | Azure Cosmos Core (SQL) データベースのスループットを更新します。 |
| [az cosmosdb sql container throughput update](/cli/azure/cosmosdb/sql/container/throughput#az_cosmosdb_sql_container_throughput_update) | Azure Cosmos Core (SQL) コンテナーのスループットを更新します。 |
| [az cosmosdb sql database throughput migrate](/cli/azure/cosmosdb/sql/database/throughput#az_cosmosdb_sql_database_throughput_migrate) | Azure Cosmos Core (SQL) データベースのスループットを移行します。 |
| [az cosmosdb sql container throughput migrate](/cli/azure/cosmosdb/sql/container/throughput#az_cosmosdb_sql_container_throughput_migrate) | Azure Cosmos Core (SQL) コンテナーのスループットを移行します。 |
| [az group delete](/cli/azure/resource#az_resource_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB CLI の詳細については、[Azure Cosmos DB CLI のドキュメント](/cli/azure/cosmosdb)を参照してください。

Azure Cosmos DB CLI のすべてのサンプル スクリプトについては、[Azure Cosmos DB CLI GitHub リポジトリ](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)をご覧ください。
