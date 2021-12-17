---
title: CLI スクリプト - Azure Database for MySQL - フレキシブル サーバーを再起動、停止、起動する
description: この Azure CLI のサンプル スクリプトでは、Azure Database for MySQL - フレキシブル サーバーを再起動、停止、または起動する方法を示します。
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 12a375eb7195af9b95db85723efe7a52b53c5356
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844115"
---
# <a name="restartstopstart-an-azure-database-for-mysql---flexible-server-using-azure-cli"></a>Azure CLI を使用して、Azure Database for MySQL - フレキシブル サーバーを再起動、停止、または起動する

このサンプルの CLI のスクリプトでは、Azure Database for MySQL - フレキシブル サーバーを再起動、起動および停止操作を実行します。 

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]


> [!IMPORTANT]
> サーバーを **停止** すると、それ以降連続して 7 日間は、その状態のままになります。 この期間内に手動で **開始** しないと、サーバーは 7 日の終わりに自動的に開始されます。 サーバーを使用していない場合は、再び **停止** することができます。

サーバーが停止されている間、サーバーで管理操作を実行することはできません。 サーバーの構成設定を変更するには、サーバーを起動する必要があります。 

停止および起動を実行する前に、[停止および起動の制限](../concepts-limitations.md#stopstart-operation)に関するページも参照してください。


[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="sample-script"></a>サンプル スクリプト

スクリプトの強調表示されている行を編集し、変数にご自分の値を指定します。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/manage-server/restart-start-stop.sh?highlight=7,10-11 "Create a server, perform restart / start / stop operations.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

サンプル スクリプトの実行後、次のコード スニペットを利用してリソースをクリーンアップできます。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/manage-server/clean-up-resources.sh?highlight=4 "Clean up resources.")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| **コマンド** | **注** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|すべてのリソースを格納するリソース グループを作成します。|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|データベースをホストするフレキシブル サーバーを作成します。|
|[az mysql flexible-server stop](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_stop)|フレキシブル サーバーを停止します。|
|[az mysql flexible-server start](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_start)|フレキシブル サーバーを起動します。|
|[az mysql flexible-server restart](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_restart)|フレキシブル サーバーを再起動します。|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|フレキシブル サーバーを削除します。|
|[az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。|

## <a name="next-steps"></a>次のステップ

- 他のスクリプトを試す: [Azure Database for MySQL - フレキシブル サーバーの Azure CLI サンプル](../sample-scripts-azure-cli.md)
- Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。