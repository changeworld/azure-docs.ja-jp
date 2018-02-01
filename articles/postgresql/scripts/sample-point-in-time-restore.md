---
title: "Azure CLI スクリプト: Azure Database for PostgreSQL サーバーを復元する"
description: "この Azure CLI サンプル スクリプトでは、Azure Database for MySQL サーバーとそのデータベースを過去の特定時点の状態に復元する方法を示します。"
services: postgresql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 01/12/2018
ms.openlocfilehash: dfc53ae10055b0e8583fb0c705e605bbbb999760
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2018
---
# <a name="restore-an-azure-database-for-postgresql-server-using-azure-cli"></a>Azure CLI を使用した Azure Database for PostgreSQL サーバーの復元
この CLI サンプル スクリプトでは、単一の Azure Database for PostgreSQL サーバーを過去の特定時点の状態に復元します。

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このサンプルでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="sample-script"></a>サンプル スクリプト
このサンプル スクリプトでは、強調表示された行を変更して、管理者のユーザー名とパスワードをカスタマイズします。 az モニター コマンドで使用するサブスクリプション ID を、ご自分のサブスクリプション ID で置き換えます。
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/backup-restore.sh?highlight=15-16 "Restore Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| **コマンド** | **メモ** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az postgresql server create](/cli/azure/postgresql/server#az_msql_server_create) | データベースをホストする PostgreSQL サーバーを作成します。 |
| [az postgresql server restore](/cli/azure/postgresql/server#az_msql_server_restore) | バックアップからサーバーを復元します。 |
| [az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順
- Azure CLI に関する詳細を読む: [Azure CLI ドキュメント](/cli/azure/overview)
- 他のスクリプトを試す: [Azure Database for PostgreSQL の Azure CLI サンプル](../sample-scripts-azure-cli.md)
- [Azure Portal を使用した Azure Database for PostgreSQL サーバーのバックアップと復元方法](../howto-restore-server-portal.md)
