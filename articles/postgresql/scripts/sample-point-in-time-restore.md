---
title: Azure CLI スクリプト - Azure Database for PostgreSQL サーバーを復元する
description: この Azure CLI サンプル スクリプトでは、Azure Database for PostgreSQL サーバーとそのデータベースを過去の特定時点の状態に復元する方法を示します。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: 6d95c06efa347618a7c571b65f01f147aeb5ef1f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778429"
---
# <a name="restore-an-azure-database-for-postgresql-server-using-azure-cli"></a>Azure CLI を使用した Azure Database for PostgreSQL サーバーの復元
この CLI サンプル スクリプトでは、単一の Azure Database for PostgreSQL サーバーを過去の特定時点の状態に復元します。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="sample-script"></a>サンプル スクリプト
このサンプル スクリプトでは、強調表示された行を編集して、管理者のユーザー名とパスワードを自分のユーザー名とパスワードに更新します。 `az monitor` コマンドで使用するサブスクリプション ID を、ご自分のサブスクリプション ID で置き換えます。
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/backup-restore.sh?highlight=15-16 "Restore Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプトの実行後は、次のコマンドを使用してリソース グループとそこに関連付けられているすべてのリソースを削除してください。 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトで使用されているコマンドを次の表にまとめました。

| **コマンド** | **注** |
|---|---|
| [az group create](/cli/azure/group) | すべてのリソースを格納するリソース グループを作成します。 |
| [az postgresql server create](/cli/azure/postgres/server#az_postgres_server_create) | データベースをホストする PostgreSQL サーバーを作成します。 |
| [az postgresql server restore](/cli/azure/postgres/server#az_postgres_server_restore) | バックアップからサーバーを復元します。 |
| [az group delete](/cli/azure/group) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ
- Azure CLI に関する詳細を読む: [Azure CLI ドキュメント](/cli/azure)
- さらに他のスクリプトも試す: [Azure Database for PostgreSQL 用 Azure CLI サンプル](../sample-scripts-azure-cli.md)
- [Azure Portal を使用した Azure Database for PostgreSQL サーバーのバックアップと復元方法](../howto-restore-server-portal.md)
