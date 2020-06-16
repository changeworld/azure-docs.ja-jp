---
title: Azure Cosmos DB 用の Gremlin データベースとグラフを保護するリソース ロックを作成する
description: Azure Cosmos DB 用の Gremlin データベースとグラフを保護するリソース ロックを作成する
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 06/03/2020
ms.openlocfilehash: e4aba05df23ce4048ba711680086407cb764a24a
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2020
ms.locfileid: "84514605"
---
# <a name="create-a-resource-lock-for-azure-cosmos-gremlin-api-database-and-graph-using-azure-cli"></a>Azure CLI を使用して Azure Cosmos Gremlin API のデータベースとグラフを保護するリソース ロックを作成する

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用することを選択した場合、このトピックでは、Azure CLI のバージョン 2.6.0 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

> [!IMPORTANT]
> 先に `disableKeyBasedMetadataWriteAccess` プロパティを有効にして Cosmos DB アカウントをロックしておかないと、ユーザーが Gremlin SDK または Azure portal を使って行った変更に対してリソース ロックが機能しません。 このプロパティを有効にする方法の詳細については、「[Cosmos SDK からの変更の防止](../../../role-based-access-control.md#preventing-changes-from-cosmos-sdk)」を参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/lock.sh "Create a resource lock for an Azure Cosmos DB Gremlin API database and graph.")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | Notes |
|---|---|
| [az lock create](/cli/azure/lock#az-lock-create) | ロックを作成します。 |
| [az lock list](/cli/azure/lock#az-lock-list) | ロック情報を一覧表示します。 |
| [az lock show](/cli/azure/lock#az-lock-show) | ロックのプロパティを表示します。 |
| [az lock delete](/cli/azure/lock#az-lock-delete) | ロックを削除します。 |

## <a name="next-steps"></a>次のステップ

-[リソースをロックして予期せぬ変更を予防する](../../../../azure-resource-manager/management/lock-resources.md)

-[Azure Cosmos DB CLI のドキュメント](/cli/azure/cosmosdb)。

-[Azure Cosmos DB CLI の GitHub リポジトリ](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)。
