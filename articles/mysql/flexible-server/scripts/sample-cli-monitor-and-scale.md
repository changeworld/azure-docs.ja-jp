---
title: CLI スクリプト - Azure Database for MySQL - フレキシブル サーバー (プレビュー) を監視およびスケーリングする
description: この Azure CLI のサンプル スクリプトでは、変化するパフォーマンスのニーズを対応するために、1 つの Azure Database for MySQL - フレキシブル サーバーを監視およびスケールアップまたはダウンする方法を示します。
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 3ab338f1648753b89907180be78b3a687b370ded
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604678"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql---flexible-server-preview-using-azure-cli"></a>Azure CLI を使用して Azure Database for MySQL - フレキシブル サーバー (プレビュー) を監視およびスケーリングする

このサンプルの CLI スクリプトでは、対応するメトリックを照会後、1 つの Azure Database for MySQL - フレキシブル サーバーのコンピューティング、ストレージおよび IOPS をスケーリングする方法を示します。 コンピューティングと IOPS はスケールアップまたはダウンできるのに対し、ストレージはスケールアップのみ可能です。 

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="sample-script"></a>サンプル スクリプト

スクリプトの強調表示されている行を編集し、変数にご自分の値を指定します。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/monitor-and-scale/monitor-and-scale.sh?highlight=8,11-12 "Monitor your Flexible Server and scale Compute, Storage and IOPS.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

サンプル スクリプトの実行後、次のコード スニペットを利用してリソースをクリーンアップできます。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/monitor-and-scale/clean-up-resources.sh?highlight=4 "Clean up resources.")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| **コマンド** | **注** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|すべてのリソースを格納するリソース グループを作成します。|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|データベースをホストするフレキシブル サーバーを作成します。|
|[az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list)|リソース用の Azure Monitor のメトリック値を一覧表示します。|
|[az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)|フレキシブル サーバーのプロパティを更新します。|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|フレキシブル サーバーを削除します。|
|[az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。|

## <a name="next-steps"></a>次のステップ

- 他のスクリプトを試す: [Azure Database for MySQL - フレキシブル サーバー (プレビュー) の Azure CLI サンプル](../sample-scripts-azure-cli.md)
- Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。