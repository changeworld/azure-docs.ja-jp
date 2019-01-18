---
title: 'Azure CLI スクリプト: Azure Database for PostgreSQL でサーバーのログをダウンロードする'
description: この Azure CLI のサンプル スクリプトでは、Azure Database for PostgreSQL サーバーのログを有効にし、ダウンロードする方法を説明します。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: cd4346c738e1c1c52ee181f764f827b1b1060650
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547203"
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-postgresql-server-using-azure-cli"></a>Azure CLI で Azure Database for PostgreSQL サーバーの低速クエリ ログを有効にしてダウンロードする
この CLI のサンプル スクリプトでは、単一の Azure Database for PostgreSQL サーバーの、低速クエリ ログを有効にしてダウンロードします。

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

CLI をローカルで実行する場合、この記事では、Azure CLI バージョン 2.0 以降が必要です。 `az --version` を実行してバージョンを確認します。 「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照して Azure CLI をインストールするか、お使いのバージョンをアップグレードしてください。

## <a name="sample-script"></a>サンプル スクリプト
このサンプル スクリプトでは、強調表示された行を編集して、管理者のユーザー名とパスワードを自分のユーザー名とパスワードに更新します。 `az monitor` コマンドの &lt;log_file_name&gt; を、ご自分のサーバーのログ ファイル名に置き換えます。
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプトの実行後は、次のコマンドを使用してリソース グループとそこに関連付けられているすべてのリソースを削除してください。 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/server-logs/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトで使用されているコマンドを次の表にまとめました。

| **コマンド** | **メモ** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az postgres server create](/cli/azure/postgres/server#az_msql_server_create) | データベースをホストする PostgreSQL サーバーを作成します。 |
| [az postgres server configuration list](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_list) | サーバーの構成値を一覧表示します。 |
| [az postgres server configuration set](/cli/azure/postgres/server/configuration#az_postgres_server_configuration_set) | サーバーの構成を更新します。 |
| [az postgres server-logs list](/cli/azure/postgres/server-logs#az_postgres_server_logs_list) | サーバーのログ ファイルを一覧表示します。 |
| [az postgres server-logs download](/cli/azure/postgres/server-logs#az_postgres_server_logs_download) | ログ ファイルをダウンロードします。 |
| [az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順
- Azure CLI に関する詳細を読む: [Azure CLI ドキュメント](/cli/azure)
- さらに他のスクリプトも試す: [Azure Database for PostgreSQL 用 Azure CLI サンプル](../sample-scripts-azure-cli.md)
- [Azure Portal でのサーバー ログの構成とアクセス](../howto-configure-server-logs-in-portal.md)
