---
title: CLI スクリプト - VNet に Azure Database for MySQL - フレキシブル サーバーを作成する
description: この Azure CLI サンプル スクリプトは、VNet に Azure Database for MySQL - フレキシブル サーバーを作成し (プライベート アクセス接続方法)、VNet 内の VM からそのサーバーに接続する方法を示します。
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: 4d7972293652d765cdb1686ff7759a41104f8229
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844242"
---
# <a name="create-an-azure-database-for-mysql---flexible-server-in-a-vnet-using-azure-cli"></a>Azure CLI を使用して VNet に Azure Database for MySQL - フレキシブル サーバーを作成する

このサンプル CLI スクリプトを使用すると、VNet に Azure Database for MySQL - フレキシブル サーバーが作成され ([プライベート アクセス接続方法](../concepts-networking-vnet.md))、VNet 内の VM からサーバーに接続されます。

> [!NOTE] 
> サーバーの作成後に接続方法を変更することはできません。 たとえば、作成時に "*プライベート アクセス (VNet 統合)* " を使用してサーバーを作成した場合、作成後に "*パブリック アクセス (許可された IP アドレス)* " に変更することはできません。 接続方法の詳細については、[ネットワークの概念](../concepts-networking.md)に関するページを参照してください。

[!INCLUDE [flexible-server-free-trial-note](../../includes/flexible-server-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment](../../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 

## <a name="sample-script"></a>サンプル スクリプト

スクリプトの強調表示されている行を編集し、変数にご自分の値を指定します。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-private-access/create-connect-server-in-vnet.sh?highlight=7,10 "Create and Connect to an Azure Database for MySQL - Flexible Server (General Purpose SKU) in VNet")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

サンプル スクリプトの実行後、次のコード スニペットを利用してリソースをクリーンアップできます。

[!code-azurecli-interactive[main](../../../../cli_scripts/mysql/flexible-server/create-server-private-access/clean-up-resources.sh "Clean up resources.")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| **コマンド** | **注** |
|---|---|
|[az group create](/cli/azure/group#az_group_create)|すべてのリソースを格納するリソース グループを作成します。|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|データベースをホストするフレキシブル サーバーを作成します。|
|[az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)|VNet 内にサブネットを作成します。|
|[az vm create](/cli/azure/vm#az_vm_create)|Azure 仮想マシンを作成します。|
|[az vm open-port](/cli/azure/vm#az_vm_open_port)|指定したポートで受信トラフィックへの VM を開きます。|
|[az group delete](/cli/azure/group#az_group_delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。|

## <a name="next-steps"></a>次のステップ

- 他のスクリプトを試す: [Azure Database for MySQL - フレキシブル サーバーの Azure CLI サンプル](../sample-scripts-azure-cli.md)
- Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。