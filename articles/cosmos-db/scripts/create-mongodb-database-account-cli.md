---
title: "Azure CLI スクリプト - Azure Cosmos DB の MongoDB API アカウント、データベース、およびコレクションを作成する |Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Azure Cosmos DB の MongoDB API アカウント、データベース、およびコレクションを作成する"
services: cosmos-db
documentationcenter: cosmosdb
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: cosmosdb
ms.workload: database
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: 416fb332681b79ac6152d4c900547d600e8f6d1b
ms.contentlocale: ja-jp
ms.lasthandoff: 05/15/2017

---

# <a name="azure-cosmos-db-create-an-mongodb-api-account-using-the-azure-cli"></a>Azure Cosmos DB: Azure CLI を使用して MongoDB API アカウントを作成する

この CLI サンプル スクリプトでは、Azure Cosmos DB の MongoDB API アカウント、データベース、およびコレクションを作成します。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[メイン](../../../cli_scripts/cosmosdb/create-cosmosdb-mongodb-account/create-cosmosdb-mongodb-account.sh?highlight=15-35 "Azure Cosmos DB の MongoDB API アカウント、データベース、およびコレクションを作成する")]

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
| [az cosmosdb create](/cli/azure/cosmosdb#create) | Azure Cosmos DB アカウントを作成します。 |
| [az group delete](/cli/azure/resource#delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

Azure Cosmos DB のその他の CLI サンプル スクリプトについては、[Azure Cosmos DB CLI のドキュメント](../cli-samples.md)をご覧ください。

