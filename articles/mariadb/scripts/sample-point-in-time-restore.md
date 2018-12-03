---
title: Azure CLI スクリプト - Azure Database for MariaDB サーバーの復元
description: この Azure CLI サンプル スクリプトでは、Azure Database for MariaDB サーバーとそのデータベースを過去の特定の時点に復元する方法を示します。
services: mariadb
author: ajlam
ms.author: andrela
editor: jasonwhowell
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: 5c7e3f96488ef5142c19920b7f14282fe3c89b9a
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585188"
---
# <a name="restore-an-azure-database-for-mariadb-server-using-azure-cli"></a>Azure CLI を使用して Azure Database for MariaDB サーバーを復元する
この CLI サンプル スクリプトでは、単一の Azure Database for MariaDB サーバーを過去の特定の時点に復元します。

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

CLI をローカルで実行する場合、この記事では、Azure CLI バージョン 2.0 以降が必要です。 `az --version` を実行してバージョンを確認します。 「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照して Azure CLI をインストールするか、お使いのバージョンをアップグレードしてください。 

## <a name="sample-script"></a>サンプル スクリプト
このサンプル スクリプトでは、強調表示された行を編集して、管理者のユーザー名とパスワードを自分のユーザー名とパスワードに更新します。 `az monitor` コマンドで使用するサブスクリプション ID を、ご自分のサブスクリプション ID で置き換えます。
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/backup-restore-pitr/backup-restore.sh?highlight=15-16 "Restore Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプトの実行後は、次のコマンドを使用してリソース グループとそこに関連付けられているすべてのリソースを削除してください。 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/backup-restore-pitr/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトで使用されているコマンドを次の表にまとめました。

| **コマンド** | **メモ** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | データベースのホストとなる MariaDB サーバーを作成します。 |
| [az mariadb server restore](/cli/azure/mariadb/server#az-mariadb-server-restore) | バックアップからサーバーを復元します。 |
| [az group delete](/cli/azure/group#az-group-delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順
- Azure CLI に関する詳細を読む: [Azure CLI ドキュメント](/cli/azure)
- 「[Azure Database for MariaDB 用の Azure CLI サンプル](../sample-scripts-azure-cli.md)」にあるその他のスクリプトを試す
