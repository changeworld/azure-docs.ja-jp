---
title: CLI スクリプト - 低速クエリ ログをダウンロードする - Azure Database for MySQL
description: この Azure CLI のサンプル スクリプトでは、Azure Database for MySQL サーバーのログを有効にし、ダウンロードする方法を説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 15d22ea3e0d16d377847ba85acfa41f5ae1043a6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791695"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure CLI で Azure Database for MySQL サーバーの低速のクエリ ログを有効にしてダウンロードする
このCLI のサンプル スクリプトでは、単一の Azure Database for MySQL サーバーの、低速クエリ ログを有効にしてダウンロードします。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="sample-script"></a>サンプル スクリプト
このサンプル スクリプトでは、強調表示された行を編集して、管理者のユーザー名とパスワードを自分のユーザー名とパスワードに更新します。 `az monitor` コマンドの &lt;log_file_name&gt; を、ご自分のサーバーのログ ファイル名に置き換えます。
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプトの実行後は、次のコマンドを使用してリソース グループとそこに関連付けられているすべてのリソースを削除してください。 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトで使用されているコマンドを次の表にまとめました。

| **コマンド** | **注** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) | データベースのホストとなる MySQL サーバーを作成します。 |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) | サーバーの構成値を一覧表示します。 |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) | サーバーの構成を更新します。 |
| [az mysql server-logs list](/cli/azure/mysql/server-logs#az_mysql_server_logs_list) | サーバーのログ ファイルを一覧表示します。 |
| [az mysql server-logs download](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) | ログ ファイルをダウンロードします。 |
| [az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ
- Azure CLI に関する詳細を読む: [Azure CLI ドキュメント](/cli/azure)
- さらに他のスクリプトも試す: [Azure Database for MySQL 用 Azure CLI サンプル](../sample-scripts-azure-cli.md)
