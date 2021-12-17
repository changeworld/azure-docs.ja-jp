---
title: CLI スクリプト - Azure Database for MySQL - フレキシブル サーバーのサーバー パラメーターの一覧表示と変更
description: この Azure CLI サンプル スクリプトは、Azure Database for MySQL - フレキシブル サーバーのサーバー パラメーターを一覧表示し、変更する方法を示しています。
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 4316669346f8bedec7a674da3e7114b95dd8c4ca
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844281"
---
# <a name="list-and-change-server-parameters-of-an-azure-database-for-mysql---flexible-server-using-azure-cli"></a>Azure CLI を使用した Azure Database for MySQL - フレキシブル サーバーのサーバー パラメーターの一覧表示と変更

このサンプル CLI スクリプトを使用すると、Azure Database for MySQL - フレキシブル サーバーに使用できる [サーバー パラメーター](../concepts-server-parameters.md)とそれに使用できる値がすべて一覧表示され、*max_connections* とグローバル *time_zone* パラメーターが既定値以外の値に設定されます。

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="sample-script"></a>サンプル スクリプト

スクリプトの強調表示されている行を編集し、変数にご自分の値を指定します。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/manage-server/change-server-parameters.sh?highlight=7,10-11 "Change server parameters for Azure Database for MySQL - Flexible Server.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

サンプル スクリプトの実行後、次のコード スニペットを利用してリソースをクリーンアップできます。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/manage-server/clean-up-resources.sh?highlight=4 "Clean up resources.")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| **コマンド** | **注** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|すべてのリソースを格納するリソース グループを作成します。|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|データベースをホストするフレキシブル サーバーを作成します。|
|[az mysql flexible-server parameter list](/cli/azure/mysql/flexible-server/parameter#az_mysql_flexible_server_parameter_list)|フレキシブル サーバーのパラメーター値を一覧表示します。|
|[az mysql flexible-server parameter set](/cli/azure/mysql/flexible-server/parameter#az_mysql_flexible_server_parameter_set)|フレキシブル サーバーのパラメーターを更新します。|
|[az mysql flexible-server parameter show](/cli/azure/mysql/flexible-server/parameter#az_mysql_flexible_server_parameter_show)|フレキシブル サーバーの特定のパラメーター値を取得します。|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|フレキシブル サーバーを削除します。|
|[az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。|

## <a name="next-steps"></a>次のステップ

- 他のスクリプトを試す: [Azure Database for MySQL - フレキシブル サーバーの Azure CLI サンプル](../sample-scripts-azure-cli.md)
- Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。