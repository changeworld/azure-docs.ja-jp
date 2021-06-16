---
title: Azure CLI:エラスティック プール間でのデータベースの移動
description: Azure CLI のサンプル スクリプトを使用して 2 つのエラスティック プールを作成し、SQL Database のデータベースをエラスティック プール間で移動します。
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: arvindshmicrosoft
ms.author: arvindsh
ms.reviewer: mathoma
ms.date: 06/25/2019
ms.openlocfilehash: 73fa80ba18e4b008c2865a8db021f1e39904ca2e
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2021
ms.locfileid: "110707909"
---
# <a name="use-the-azure-cli-to-move-a-database-in-sql-database-in-a-sql-elastic-pool"></a>Azure CLI を使用して SQL Database のデータベースを SQL エラスティック プール間で移動する

この Azure CLI サンプル スクリプトは、2 つのエラスティック プールを作成し、一方の SQL エラスティック プールからもう一方の SQL エラスティック プールに SQL Database のプールされたデータベースを移動し、その後、そのプールされたデータベースを SQL エラスティック プールから取り出して単一データベースにします。

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

### <a name="sign-in-to-azure"></a>Azure へのサインイン

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>スクリプトを実行する

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Move database between pools")]

### <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを使用して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>サンプル リファレンス

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | 説明 |
|---|---|
| [az sql server](/cli/azure/sql/server) | サーバーのコマンド。 |
| [az sql elastic-pools](/cli/azure/sql/elastic-pool) | エラスティック プールのコマンド。 |
| [az sql db](/cli/azure/sql/db) | データベースのコマンド。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の SQL Database 用の CLI サンプル スクリプトは、[Azure SQL Database のドキュメント](../az-cli-script-samples-content-guide.md)のページにあります。
