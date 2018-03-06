---
title: "Azure CLI スクリプト - サーバーの構成を変更する"
description: "このサンプル CLI スクリプトは、すべての利用可能なサーバー構成を一覧表示し、innodb_lock_wait_timeout の値を更新します。"
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: df169cfc4194337146201b0ad5bdf84d91a49898
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure CLI を使用して Azure Database for MySQL サーバーの構成を一覧表示および更新する
このサンプル CLI スクリプトは、Azure Database for MySQL サーバーのすべての利用可能な構成パラメーターと、許容値を一覧表示し、*innodb_lock_wait_timeout* を既定値以外の値に設定します。

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

CLI をローカルで実行する場合、この記事では、Azure CLI バージョン 2.0 以降が必要です。 `az --version` を実行してバージョンを確認します。 「[Install Azure CLI 2.0 (Azure CLI 2.0 のインストール)]( /cli/azure/install-azure-cli)」を参照して Azure CLI をインストールするか、お使いのバージョンをアップグレードしてください。 

## <a name="sample-script"></a>サンプル スクリプト
このサンプル スクリプトでは、強調表示された行を編集して、管理者のユーザー名とパスワードを自分のユーザー名とパスワードに更新します。
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/change-server-configurations.sh?highlight=18-19 "List and update configurations of Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプトの実行後は、次のコマンドを使用してリソース グループとそこに関連付けられているすべてのリソースを削除してください。 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/change-server-configurations/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトで使用されているコマンドを次の表にまとめました。

| **コマンド** | **メモ** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az mysql server create](/cli/azure/mysql/server#az_msql_server_create) | データベースのホストとなる MySQL サーバーを作成します。 |
| [az mysql server configuration list](/cli/azure/mysql/server/configuration#az_msql_server_configuration_list) | Azure Database for MySQL サーバーの構成を一覧表示します。 |
| [az mysql server configuration set](/cli/azure/mysql/server/configuration#az_msql_server_configuration_set) | Azure Database for MySQL サーバーの構成を更新します。 |
| [az mysql server configuration show](/cli/azure/mysql/server/configuration#az_msql_server_configuration_show) | Azure Database for MySQL サーバーの構成を表示します。 |
| [az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順
- Azure CLI に関する詳細を読む: [Azure CLI ドキュメント](/cli/azure/overview)
- 他のスクリプトを試す: [Azure Database for MySQL の Azure CLI サンプル](../sample-scripts-azure-cli.md)
- サーバー パラメーターの詳細については、「[Azure ポータルを使用して Azure Database for MySQL サーバーのサーバー パラメータを構成する方法](../howto-server-parameters.md)」を参照してください。
