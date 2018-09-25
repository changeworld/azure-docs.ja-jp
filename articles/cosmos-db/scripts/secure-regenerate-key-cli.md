---
title: Azure CLI スクリプト - Azure Cosmos DB アカウント キーを再生成する | Microsoft Docs
description: Azure CLI のサンプル スクリプト - Azure Cosmos DB アカウント キーを再生成する
services: cosmos-db
documentationcenter: cosmosdb
author: SnehaGunda
manager: kfile
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 06/02/2017
ms.author: sngun
ms.openlocfilehash: fb6e821f65f07866725921c646b71af9238fb6e0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46975022"
---
# <a name="regenerate-an-azure-cosmos-db-account-key-using-the-azure-cli"></a>Azure CLI を使用して Azure Cosmos DB アカウント キーを再生成する

このサンプルでは、Azure CLI を使用して任意の種類の Azure Cosmos DB アカウント キーを再生成します。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/secure-cosmosdb-regenerate-keys/secure-cosmosdb-regenerate-keys.sh?highlight=27-31 "Regenerate Azure Cosmos DB account keys")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az cosmosdb create](https://docs.microsoft.com/cli/azure/cosmosdb#az-cosmosdb-create) | Azure Cosmos DB アカウントを更新します。 |
| [az cosmosdb regenerate-key](/cli/azure/cosmosdb#az-cosmosdb-regenerate-key) | Azure Cosmos DB アカウント キーを再生成します。 |
| [az group delete](https://docs.microsoft.com/cli/azure/group#az-group-delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

Azure Cosmos DB のその他の CLI サンプル スクリプトについては、[Azure Cosmos DB CLI のドキュメント](../cli-samples.md)をご覧ください。
