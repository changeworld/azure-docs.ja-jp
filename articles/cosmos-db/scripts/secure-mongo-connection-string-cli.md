---
title: "Azure CLI スクリプト - MongoDB アプリ用の Azure Cosmos DB 接続文字列を取得する | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - MongoDB アプリ用の Azure Cosmos DB 接続文字列を取得する"
services: cosmosdb
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmosdb
ms.custom: sample
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 27c790db456c3e650a624b46df77d4cdce202fa7
ms.contentlocale: ja-jp
ms.lasthandoff: 05/15/2017

---

# <a name="get-an-azure-cosmos-db-connection-string-for-mongodb-apps-using-the-azure-cli"></a>Azure CLI を使用して MongoDB アプリ用の Azure Cosmos DB 接続文字列を取得する

このサンプルでは、Azure CLI を使用して MongoDB アプリ用の Azure Cosmos DB 接続文字列を取得します。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[メイン](../../../cli_scripts/cosmosdb/secure-cosmosdb-get-mongodb-connection-string/secure-cosmosdb-get-mongodb-connection-string.sh?highlight=36-39 "MongoDB アプリ用の Azure Cosmos DB 接続文字列を取得する")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az cosmosdb update](/cli/azure/cosmosdb/name#update) | Azure Cosmos DB アカウントを更新します。 |
| [az cosmosdb list-connection-strings](/cli/azure/cosmosdb/list-connection-strings) | アカウント用の接続文字列を取得します。|
| [az group delete](/cli/azure/resource#delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

Azure Cosmos DB のその他の CLI サンプル スクリプトについては、[Azure Cosmos DB CLI のドキュメント](../cli-samples.md)をご覧ください。

