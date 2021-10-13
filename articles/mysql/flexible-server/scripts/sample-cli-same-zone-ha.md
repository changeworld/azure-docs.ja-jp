---
title: CLI スクリプト - Azure Database for MySQL - フレキシブル サーバーで同一ゾーン高可用性を構成する (プレビュー)
description: この Azure CLI サンプル スクリプトでは Azure Database for MySQL フレキシブル サーバーで同一ゾーン高可用性を構成する方法を示します。
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 115baa8d181bd7ebef125e7a666216efe50680d2
ms.sourcegitcommit: ee5d9cdaf691f578f2e390101bf5350859d85c67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2021
ms.locfileid: "129740786"
---
# <a name="configure-same-zone-high-availability-in-an-azure-database-for-mysql---flexible-server-preview-using-azure-cli"></a>Azure CLI を使用して、Azure Database for MySQL - フレキシブル サーバー (プレビュー) で同一ゾーン高可用性を構成する

このサンプル CLI スクリプトでは、Azure Database for MySQL - フレキシブル サーバーで[同一ゾーン高可用性](../concepts-high-availability.md)を構成および管理します。 現在、同一ゾーン高可用性は、汎用およびメモリ最適化の価格レベルでのみサポートされています。


[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="sample-script"></a>サンプル スクリプト

スクリプトの中で強調表示されている行を編集し、変数に値を指定します。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/high-availability/same-zone-ha.sh?highlight=7,10-11 "Configure Same-Zone High Availability.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

サンプル スクリプトの実行後、次のコード スニペットを利用してリソースをクリーンアップできます。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/high-availability/clean-up-resources.sh?highlight=4 "Clean up resources.")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| **コマンド** | **注** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|すべてのリソースを格納するリソース グループを作成します。|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|データベースのホストとなるフレキシブル サーバーを作成します。|
|[az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)|フレキシブル サーバーを更新します。|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|フレキシブル サーバーを削除します。|
|[az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。|

## <a name="next-steps"></a>次のステップ

- 他のスクリプトを試す: [Azure Database for MySQL - フレキシブル サーバー (プレビュー) の Azure CLI サンプル](../sample-scripts-azure-cli.md)
- Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。
