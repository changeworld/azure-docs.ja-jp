---
title: CLI スクリプト - Azure Database for MySQL - フレキシブル サーバーの復元
description: この Azure CLI サンプル スクリプトでは、単一 Azure Database for MySQL - フレキシブル サーバーを過去の特定時点の状態に復元する方法を示します。
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: f109da4924f04cd97fbe9cd816c2da45ce5c0e2d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844157"
---
# <a name="restore-an-azure-database-for-mysql---flexible-server-using-azure-cli"></a>Azure CLI を使用して Azure Database for MySQL - フレキシブル サーバーを復元する

Azure Database for MySQL - フレキシブル サーバーを使用すると、サーバーのバックアップが自動的に作成されて、リージョン内のローカル冗長ストレージに安全に格納されます。

この CLI サンプル スクリプトでは、[ポイントインタイム リストア](../concepts-backup-restore.md)が実行され、フレキシブル サーバーのバックアップから新しいサーバーが作成されます。 

新しいフレキシブル サーバーは元のサーバーの構成で作成され、ソース サーバーから仮想ネットワークやファイアウォールなどのタグと設定も継承します。 復元されたサーバーのコンピューティング レベルとストレージ レベル、構成、セキュリティの設定は、復元が完了した後で変更できます。

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="sample-script"></a>サンプル スクリプト

スクリプトの強調表示されている行を編集し、変数にご自分の値を指定します。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/backup-restore/restore-server.sh?highlight=7,10-12 "Perform point-in-time-restore of a source server to a new server.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

サンプル スクリプトの実行後、次のコード スニペットを利用してリソースをクリーンアップできます。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/backup-restore/clean-up-resources.sh?highlight=4-5 "Clean up resources.")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| **コマンド** | **注** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|すべてのリソースを格納するリソース グループを作成します。|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|データベースのホストとなるフレキシブル サーバーを作成します。|
|[az mysql flexible-server restore](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_restore)|バックアップからフレキシブル サーバーを復元します。|
|[az mysql flexible-server show](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_show)|フレキシブル サーバーの詳細を取得します。|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|フレキシブル サーバーを削除します。|
|[az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。|

## <a name="next-steps"></a>次のステップ

- 他のスクリプトを試す: [Azure Database for MySQL - フレキシブル サーバーの Azure CLI サンプル](../sample-scripts-azure-cli.md)
- Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。