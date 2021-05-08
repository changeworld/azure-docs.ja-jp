---
title: Azure Cosmos アカウントに対するリージョンの追加、フェールオーバー優先度の変更、フェールオーバーのトリガーを行う
description: Azure Cosmos アカウントに対するリージョンの追加、フェールオーバー優先度の変更、フェールオーバーのトリガーを行う
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 4a1a061945fe1c6c6a95eb62d286d40a158281ca
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770790"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Azure CLI を使用して Azure Cosmos アカウントに対するリージョンの追加、フェールオーバー優先度の変更、フェールオーバーのトリガーを行う
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.9.1 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="sample-script"></a>サンプル スクリプト

このスクリプトでは、3 つの操作を示します。

- 既存の Azure Cosmos アカウントにリージョンを追加します。
- リージョンのフェールオーバー優先度を変更します (自動フェールオーバーを使用するアカウントに適用されます)。
- プライマリ リージョンからセカンダリ リージョンへの手動フェールオーバーをトリガーします (手動フェールオーバーを使用するアカウントに適用されます)。

> [!NOTE]
> 他のプロパティを変更している間は、Cosmos アカウントに対するリージョンの追加操作または削除操作は実行できません。

> [!NOTE]
> このサンプルでは、SQL (Core) API アカウントを使用していますが、これらの操作は Cosmos DB のすべてのデータベース API で同一です。

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/regions.sh "Regional operations for Cosmos DB.")]

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
| [az cosmosdb update](/cli/azure/cosmosdb#az_cosmosdb_update) | Azure Cosmos DB アカウントを更新します (リージョンを追加または削除します)。 |
| [az cosmosdb failover-priority-change](/cli/azure/cosmosdb#az_cosmosdb_failover_priority_change) | Azure Cosmos アカウントのフェールオーバー優先度を変更するか、フェールオーバーをトリガーします。 |
| [az group delete](/cli/azure/resource#az_resource_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB CLI の詳細については、[Azure Cosmos DB CLI のドキュメント](/cli/azure/cosmosdb)を参照してください。

Azure Cosmos DB CLI のすべてのサンプル スクリプトについては、[Azure Cosmos DB CLI GitHub リポジトリ](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)をご覧ください。
