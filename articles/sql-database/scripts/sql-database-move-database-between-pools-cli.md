---
title: "Azure CLI スクリプト-SQL データベースとエラスティック プールの移動 | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Azure CLI を使用したエラスティック プール間での SQL データベースの移動"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 04/24/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: df4a62783cf3f8f644de850cb80bdad8352cf372
ms.contentlocale: ja-jp
ms.lasthandoff: 05/15/2017

---

# <a name="create-elastic-pools-and-move-databases-between-pools-and-out-of-a-pool-using-the-azure-cli"></a>Azure CLI を使用してエラスティック プールを作成し、データベースをプール間およびプールの外へ移動する

この CLI のサンプル スクリプトは、2 つのエラスティック プールを作成し、一方のエラスティック プールからもう一方のエラスティック プールにデータベースを移動し、その後、1 つのデータベースを 1 つのエラスティック プールから単一のデータベース パフォーマンス レベルに移動します。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "プール間でのデータベースの移動")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | データベースまたはエラスティック プールをホストする論理サーバーを作成します。 |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pools#create) | 論理サーバー内にエラスティック プールを作成します。 |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | 単一のデータベースまたはプールされているデータベースとして論理サーバーにデータベースを作成します。 |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#update) | データベースのプロパティを更新するか、エラスティック プールに対して、エラスティック プールから、またはエラスティック プール間でデータベースを移動します。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の SQL Database 用の CLI サンプル スクリプトは、[Azure SQL Database のドキュメント](../sql-database-cli-samples.md)のページにあります。



