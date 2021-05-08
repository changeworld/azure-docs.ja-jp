---
title: CLI スクリプト - サーバーをスケーリングする - Azure Database for MySQL
description: このサンプル CLI スクリプトは、Azure Database for MySQL サーバーのメトリックを照会したうえで、異なるパフォーマンス レベルにスケーリングするものです。
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: e7dea1cb99ea8ce941b16739bdbd258843877e89
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791650"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Azure CLI での Azure Database for MySQL サーバーの監視とスケーリング
このサンプル CLI スクリプトは、メトリックのクエリ後、単一の Azure Database for MySQL サーバーのコンピューティングとストレージをスケーリングします。 コンピューティングはスケールアップまたはスケールダウンすることができます。 ストレージはスケールアップすることのみが可能です。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="sample-script"></a>サンプル スクリプト
サブスクリプション ID を使用してスクリプトを更新してください。
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプトの実行後は、次のコマンドを使用してリソース グループとそこに関連付けられているすべてのリソースを削除してください。 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトで使用されているコマンドを次の表にまとめました。

| **コマンド** | **注** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) | データベースのホストとなる MySQL サーバーを作成します。 |
| [az mysql server update](/cli/azure/mysql/server#az_mysql_server_update) | MySQL サーバーのプロパティを更新します。 |
| [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) | リソースのメトリック値を一覧表示します。 |
| [az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ
- [Azure Database for MySQL コンピューティングとストレージ](../concepts-pricing-tiers.md)についての詳細情報
- さらに他のスクリプトも試す: [Azure Database for MySQL 用 Azure CLI サンプル](../sample-scripts-azure-cli.md)
- [Azure CLI](/cli/azure) の詳細について学習します
