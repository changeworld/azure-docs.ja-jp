---
title: "Azure CLI: Azure Database for MySQL でサーバーのログをダウンロードする"
description: "この Azure CLI のサンプル スクリプトでは、Azure Database for MySQL サーバーのログを有効にし、ダウンロードする方法を説明します。"
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 01/11/2018
ms.openlocfilehash: b0d34009d189ab136dcb6f28fdccc49b6da9e108
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
---
# <a name="enable-and-download-server-slow-query-logs-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure CLI で Azure Database for MySQL サーバーの低速のクエリ ログを有効にしてダウンロードする
このCLI のサンプル スクリプトでは、単一の Azure Database for MySQL サーバーの、低速クエリ ログを有効にしてダウンロードします。

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このサンプルでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="sample-script"></a>サンプル スクリプト
このサンプル スクリプトでは、強調表示された行を変更して、管理者のユーザー名とパスワードをカスタマイズします。 az monitor コマンドの <log_file_name> を、自身のサーバーのログ ファイル名に置き換えます。
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/server-logs.sh?highlight=15-16 "Manipulate with server logs.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/server-logs/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| **コマンド** | **メモ** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az mysql server create](/cli/azure/mysql/server#az_msql_server_create) | データベースのホストとなる MySQL サーバーを作成します。 |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_list) | サーバーの構成値を一覧表示します。 |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_mysql_server_configuration_set) | サーバーの構成を更新します。 |
| [az mysql server-logs list](/cli/azure/mysql/server-logs#az_mysql_server_logs_list) | サーバーのログ ファイルを一覧表示します。 |
| [az mysql server-logs download](/cli/azure/mysql/server-logs#az_mysql_server_logs_download) | ログ ファイルをダウンロードします。 |
| [az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順
- Azure CLI に関する詳細を読む: [Azure CLI ドキュメント](/cli/azure/overview)
- 他のスクリプトを試す: [Azure Database for MySQL の Azure CLI サンプル](../sample-scripts-azure-cli.md)
