---
title: CLI スクリプト - Azure Database for MySQL - フレキシブル サーバーでの読み取りレプリカの作成と管理
description: この Azure CLI サンプル スクリプトは、Azure Database for MySQL - フレキシブル サーバーで読み取りレプリカを作成および管理する方法を示しています
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 1155231ca8909352aee2354023a19ea66c7ffc4d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844114"
---
# <a name="create-and-manage-read-replicas-in-an-azure-database-for-mysql---flexible-server-using-azure-cli"></a>Azure CLI スクリプトを使用した Azure Database for MySQL - フレキシブル サーバーでの読み取りレプリカの作成と管理

この CLI サンプル スクリプトは、Azure Database for MySQL - フレキシブル サーバーで [読み取りレプリカ](../concepts-read-replicas.md)を作成して管理します。

>[!IMPORTANT]
>既存のレプリカがないソースのレプリカを作成すると、ソースは最初に、レプリケーションの準備をするために再起動します。 これを考慮して、これらの操作はオフピーク期間中に実行してください。

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="sample-script"></a>サンプル スクリプト

スクリプトの強調表示されている行を編集し、変数にご自分の値を指定します。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/read-replicas/create-manage-read-replicas.sh?highlight=7,10-12 "Create and manage Flexible Server Read Replicas.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

サンプル スクリプトの実行後、次のコード スニペットを利用してリソースをクリーンアップできます。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/read-replicas/clean-up-resources.sh?highlight=4-5 "Clean up resources.")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| **コマンド** | **注** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|すべてのリソースを格納するリソース グループを作成します。|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|データベースのホストとなるフレキシブル サーバーを作成します。|
|[az mysql flexible-server replica create](/cli/azure/mysql/flexible-server/replica#az_mysql_flexible_server_replica_create)|サーバーの読み取りレプリカを作成します。|
|[az mysql flexible-server replica list](/cli/azure/mysql/flexible-server/replica#az_mysql_flexible_server_replica_list)|特定のサーバーのすべての読み取りレプリカを一覧表示します。|
|[az mysql flexible-server replica stop-replication](/cli/azure/mysql/flexible-server/replica#az_mysql_flexible_server_replica_stop_replication)|読み取りレプリカへのレプリケーションを停止し、読み取り/書き込みサーバーにします。|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|フレキシブル サーバーを削除します。|
|[az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。|

## <a name="next-steps"></a>次のステップ

- 他のスクリプトを試す: [Azure Database for MySQL - フレキシブル サーバーの Azure CLI サンプル](../sample-scripts-azure-cli.md)
- Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。