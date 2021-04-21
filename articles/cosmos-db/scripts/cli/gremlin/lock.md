---
title: Azure Cosmos DB 用の Gremlin データベースとグラフを保護するリソース ロックを作成する
description: Azure Cosmos DB 用の Gremlin データベースとグラフを保護するリソース ロックを作成する
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 19809d7150c8a461f97282d1583d0d870d6af8bb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770671"
---
# <a name="create-a-resource-lock-for-azure-cosmos-gremlin-api-database-and-graph-using-azure-cli"></a>Azure CLI を使用して Azure Cosmos Gremlin API のデータベースとグラフを保護するリソース ロックを作成する
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.9.1 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

> [!IMPORTANT]
> 先に `disableKeyBasedMetadataWriteAccess` プロパティを有効にして Cosmos DB アカウントをロックしておかないと、ユーザーが Gremlin SDK または Azure portal を使って行った変更に対してリソース ロックが機能しません。 このプロパティを有効にする方法の詳細については、[SDK からの変更の防止](../../../role-based-access-control.md#prevent-sdk-changes)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/lock.sh "Create a resource lock for an Azure Cosmos DB Gremlin API database and graph.")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az lock create](/cli/azure/lock#az_lock_create) | ロックを作成します。 |
| [az lock list](/cli/azure/lock#az_lock_list) | ロック情報を一覧表示します。 |
| [az lock show](/cli/azure/lock#az_lock_show) | ロックのプロパティを表示します。 |
| [az lock delete](/cli/azure/lock#az_lock_delete) | ロックを削除します。 |

## <a name="next-steps"></a>次のステップ

- [リソースのロックによる予期せぬ変更の防止](../../../../azure-resource-manager/management/lock-resources.md)

- [Azure Cosmos DB CLI のドキュメント](/cli/azure/cosmosdb)

- [Azure Cosmos DB CLI GitHub リポジトリ](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)。
