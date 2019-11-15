---
title: CLI の例 - Azure SQL データベースを移動する - SQL エラスティック プール
description: SQL エラスティック プールの SQL データベースを移動する Azure CLI サンプル スクリプト
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 06/25/2019
ms.openlocfilehash: d974685040d2a5759ca7719e82be02a7ec8950c5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691593"
---
# <a name="use-cli-to-move-an-azure-sql-database-in-a-sql-elastic-pool"></a>CLI を使用し、SQL エラスティック プールの Azure SQL データベースを移動する

この Azure CLI のサンプル スクリプトは、2 つのエラスティック プールを作成し、一方の SQL エラスティック プールからもう一方の SQL エラスティック プールに Azure SQL データベースを移動し、その後、データベースをエラスティック プールから単一のデータベース コンピューティング サイズに移動します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Move database between pools")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを使用して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az-sql-server-create) | 単一のデータベースとエラスティック プールをホストする SQL Database サーバーを作成します。 |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create) | エラスティック プールを作成します。 |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create) | 単一のデータベースまたはエラスティック プール内のデータベースを作成します。 |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) | データベースのプロパティを更新するか、エラスティック プールに対して、エラスティック プールから、またはエラスティック プール間でデータベースを移動します。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の SQL Database 用の CLI サンプル スクリプトは、[Azure SQL Database のドキュメント](../sql-database-cli-samples.md)のページにあります。
