---
title: CLI スクリプト - Azure Database for MySQL - フレキシブル サーバーを作成し、パブリック アクセス接続を有効化する
description: この Azure CLI サンプル スクリプトでは、Azure Database for MySQL - フレキシブル サーバーを作成し、サーバー レベルのファイアウォール規則 (パブリック アクセス接続方法) を構成してそのサーバーに接続する方法を示します。
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 59df8d613e8753895fba77d3bfabefe476f49cf1
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844200"
---
# <a name="create-an-azure-database-for-mysql---flexible-server-and-enable-public-access-connectivity-using-azure-cli"></a>Azure CLI を使用して Azure Database for MySQL - フレキシブル サーバーを作成し、パブリック アクセス接続を有効化する

このサンプル CLI スクリプトでは、Azure Database for MySQL - フレキシブル サーバーを作成し、サーバー レベルのファイアウォール規則 ([パブリック アクセス接続方法](../concepts-networking-public.md)) を構成し、作成後にサーバーに接続します。 

スクリプトが正常に実行されると、すべての Azure サービスと構成済みの IP アドレスからこの MySQL フレキシブル サーバーにアクセスできるようになります。また、対話型モードでサーバーに接続されるようになります。

> [!NOTE] 
> サーバーの作成後に接続方法を変更することはできません。 たとえば、作成時に "*パブリック アクセス (許可された IP アドレス)* " を使用してサーバーを作成した場合、作成後に "*プライベート アクセス (VNet 統合)* " に変更することはできません。 接続方法の詳細については、[ネットワークの概念](../concepts-networking.md)に関するページをご覧ください。


[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="sample-script"></a>サンプル スクリプト

スクリプトの強調表示されている行を編集し、変数にご自分の値を指定します。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-public-access/create-connect-burstable-server-public-access.sh?highlight=8,11-12 "Create Flexible Server and enable public access.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

サンプル スクリプトの実行後、次のコード スニペットを利用してリソースをクリーンアップできます。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-public-access/clean-up-resources.sh?highlight=4 "Clean up resources.")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| **コマンド** | **注** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|すべてのリソースを格納するリソース グループを作成します。|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|データベースをホストするフレキシブル サーバーを作成します。|
|[az mysql flexible-server firewall-rule create](/cli/azure/mysql/flexible-server/firewall-rule#az_mysql_flexible_server_firewall_rule_create)|入力した IP アドレス範囲からフレキシブル サーバーとそのデータベースへのアクセスを許可するファイアウォール規則を作成します。|
|[az mysql flexible-server connect](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_connect)|フレキシブル サーバーに接続して、サーバーまたはデータベースの操作を実行します。|
|[az mysql flexible-server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete)|フレキシブル サーバーを削除します。|
|[az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。|

## <a name="next-steps"></a>次のステップ

- 他のスクリプトを試す: [Azure Database for MySQL - フレキシブル サーバーの Azure CLI サンプル](../sample-scripts-azure-cli.md)
- Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。