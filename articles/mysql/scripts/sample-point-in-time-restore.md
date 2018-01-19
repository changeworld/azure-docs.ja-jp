---
title: "Azure CLI: Azure Database for MySQL サーバーの復元"
description: "この Azure CLI サンプル スクリプトでは、Azure Database for MySQL サーバーとそのデータベースを過去の特定時点の状態に復元する方法を示します。"
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
ms.openlocfilehash: ed04115ff7e6f83e0357f13d734e4be3738f4c6d
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
---
# <a name="restore-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure CLI を使用した Azure Database for MySQL サーバーの復元
この CLI サンプル スクリプトでは、単一の Azure Database for MySQL サーバーを過去の特定時点の状態に復元します。

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このサンプルでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="sample-script"></a>サンプル スクリプト
このサンプル スクリプトでは、強調表示された行を変更して、管理者のユーザー名とパスワードをカスタマイズします。 az モニター コマンドで使用するサブスクリプション ID を、ご自分のサブスクリプション ID で置き換えます。
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/backup-restore-pitr/backup-restore.sh?highlight=15-16 "Restore Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/backup-restore-pitr/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| **コマンド** | **メモ** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) | データベースのホストとなる MySQL サーバーを作成します。 |
| [az mysql server restore](/cli/azure/mysql/server#az_mysql_server_restore) | バックアップからサーバーを復元します。 |
| [az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順
- Azure CLI に関する詳細を読む: [Azure CLI ドキュメント](/cli/azure/overview)
- 他のスクリプトを試す: [Azure Database for MySQL の Azure CLI サンプル](../sample-scripts-azure-cli.md)
