---
title: Azure Cosmos DB 用の MongoDB API のデータベースとコンテナーを作成する
description: Azure Cosmos DB 用の MongoDB API のデータベースとコンテナーを作成する
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 8adc99dea470c68781f35922c354716cdcc01a90
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763606"
---
# <a name="create-a-database-and-collection-for-mongodb-api-for-azure-cosmos-db-using-azure-cli"></a>Azure CLI を使用して Azure Cosmos DB 用の MongoDB API の データベースとコレクションを作成する
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.9.1 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/create.sh "Create an Azure Cosmos DB MongoDB API account, database, and collection.")]

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
| [az cosmosdb mongodb database create](/cli/azure/cosmosdb/mongodb/database#az_cosmosdb_mongodb_database_create) | Azure Cosmos MongoDB API データベースを作成します。 |
| [az cosmosdb mongodb collection create](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_create) | Azure Cosmos MongoDB API コレクションを作成します。 |
| [az group delete](/cli/azure/resource#az_resource_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB CLI の詳細については、[Azure Cosmos DB CLI のドキュメント](/cli/azure/cosmosdb)を参照してください。

Azure Cosmos DB CLI のすべてのサンプル スクリプトについては、[Azure Cosmos DB CLI GitHub リポジトリ](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)をご覧ください。
