---
title: "Azure CLI スクリプト - Azure Cosmos DB のアカウント キーを取得する | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Azure Cosmos DB のアカウント キーを取得する"
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
ms.openlocfilehash: 21da79a674a55fad910b0b059508acc026f8bd84
ms.contentlocale: ja-jp
ms.lasthandoff: 05/15/2017

---

# <a name="get-account-keys-for-azure-cosmos-db-using-the-azure-cli"></a>Azure CLI を使用して Azure Cosmos DB のアカウント キーを取得する

このサンプルでは、任意の種類の Azure Cosmos DB アカウントのアカウント キーを取得します。  

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[メイン](../../../cli_scripts/cosmosdb/scale-cosmosdb-get-account-key/secure-cosmosdb-get-account-key.sh?highlight=22-25 "Azure Cosmos DB アカウント キーを取得する")]

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
| [az cosmosdb list-keys](/cli/azure/sql/server#create) | SQL Database をホストする論理サーバーを作成します。 |
| [az group delete](/cli/azure/resource#delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

Azure Cosmos DB のその他の CLI サンプル スクリプトについては、[Azure Cosmos DB CLI のドキュメント](../cli-samples.md)をご覧ください。

