---
title: CLI スクリプト - サーバーのパラメーターを変更する - Azure Database for MariaDB
description: このサンプル CLI スクリプトは、すべての利用可能なサーバー構成を一覧表示し、Azure Database for MariaDB の構成を更新します。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 3504f1221c501b997b04d9c81c721aba2903fba6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777061"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Azure CLI を使用して Azure Database for MariaDB サーバーの構成を一覧表示および更新する
このサンプル CLI スクリプトは、Azure Database for MariaDB サーバーのすべての利用可能な構成パラメーターとその許容値を一覧表示し、*innodb_lock_wait_timeout* を既定値以外の値に設定します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="sample-script"></a>サンプル スクリプト
このサンプル スクリプトでは、強調表示された行を編集して、管理者のユーザー名とパスワードを自分のユーザー名とパスワードに更新します。
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプトの実行後は、次のコマンドを使用してリソース グループとそこに関連付けられているすべてのリソースを削除してください。
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトで使用されているコマンドを次の表にまとめました。

| **コマンド** | **注** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az mariadb server create](/cli/azure/mariadb/server#az_mariadb_server_create) | データベースのホストとなる MariaDB サーバーを作成します。 |
| [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_list) | Azure Database for MariaDB サーバーの構成を一覧表示します。 |
| [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_set) | Azure Database for MariaDB サーバーの構成を更新します。 |
| [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az_mariadb_server_configuration_show) | Azure Database for MariaDB サーバーの構成を表示します。 |
| [az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ
- Azure CLI に関する詳細を読む: [Azure CLI ドキュメント](/cli/azure)
- さらに他のスクリプトも試す: [Azure Database for MariaDB 用の Azure CLI サンプル](../sample-scripts-azure-cli.md)
- サーバー パラメーターの詳細については、[Azure Database for MariaDB のサーバー パラメーターを構成する方法](../howto-server-parameters.md)に関するページを参照してください。
