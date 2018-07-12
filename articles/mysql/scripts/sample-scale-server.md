---
title: 'Azure CLI: Azure Database for MySQL サーバーのスケーリング'
description: このサンプル CLI スクリプトは、Azure Database for MySQL サーバーのメトリックを照会したうえで、異なるパフォーマンス レベルにスケーリングするものです。
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 04/05/2018
ms.openlocfilehash: 8c5a196afbcc607300dd69ada0a06a292a9c2c0c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38635110"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure CLI での Azure Database for MySQL サーバーの監視とスケーリング
このサンプル CLI スクリプトは、単一の Azure Database for MySQL サーバーのメトリックを照会したうえで、異なるパフォーマンス レベルにスケーリングするものです。

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

CLI をローカルで実行する場合、この記事では、Azure CLI バージョン 2.0 以降が必要です。 `az --version` を実行してバージョンを確認します。 「[Install Azure CLI 2.0 (Azure CLI 2.0 のインストール)]( /cli/azure/install-azure-cli)」を参照して Azure CLI をインストールするか、お使いのバージョンをアップグレードしてください。 

## <a name="sample-script"></a>サンプル スクリプト
このサンプル スクリプトでは、強調表示された行を編集して、管理者のユーザー名とパスワードを自分のユーザー名とパスワードに更新します。 `az monitor` コマンドで使用するサブスクリプション ID を、自分のサブスクリプション ID で置き換えます。[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh?highlight=18-19 "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプトの実行後は、次のコマンドを使用してリソース グループとそこに関連付けられているすべてのリソースを削除してください。 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトで使用されているコマンドを次の表にまとめました。

| **コマンド** | **メモ** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) | データベースのホストとなる MySQL サーバーを作成します。 |
| [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) | リソースのメトリック値を一覧表示します。 |
| [az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順
- Azure CLI に関する詳細を読む: [Azure CLI ドキュメント](/cli/azure)
- 他のスクリプトを試す: [Azure Database for MySQL の Azure CLI サンプル](../sample-scripts-azure-cli.md)
- スケーリングの詳細については、[サービス プラン](../concepts-service-tiers.md)に関するページおよび[コンピューティング ユニットとストレージ ユニット](../concepts-compute-unit-and-storage.md)に関するページを参照してください。
