---
title: Azure CLI:BACPAC ファイルを Azure SQL Database のデータベースにインポートする
description: BACPAC ファイルを Azure SQL Database のデータベースにインポートするための Azure CLI サンプル スクリプト
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: load & move data
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 05/24/2019
ms.openlocfilehash: 9e3638c9823ed0fbae61a0dc5a93dff2e211d3f3
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84022643"
---
# <a name="use-cli-to-import-a-bacpac-file-into-a-database-in-sql-database"></a>CLI を使用して BACPAC ファイルを SQL Database のデータベースにインポートする

この Azure CLI サンプル スクリプトは、SQL Database のデータベースに *.bacpac* ファイルからデータベースをインポートします。  

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-script"></a>サンプル スクリプト

### <a name="sign-in-to-azure"></a>Azure へのサインイン

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>スクリプトを実行する

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/import-from-bacpac/import-from-bacpac.sh "Create SQL Database")]

### <a name="clean-up-deployment"></a>デプロイのクリーンアップ

リソース グループと、それに関連付けられているすべてのリソースを削除するには、次のコマンドを使用します。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>サンプル リファレンス

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| | |
|---|---|
| [az sql server](/cli/azure/sql/server) | サーバーのコマンド。 |
| [az sql db import](/cli/azure/sql/db#az-sql-db-import) | データベースのインポート コマンド。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の SQL Database 用の CLI サンプル スクリプトは、[Azure SQL Database のドキュメント](../../azure-sql/database/az-cli-script-samples-content-guide.md)のページにあります。
