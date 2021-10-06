---
title: CLI スクリプト - VNet に Azure Database for MySQL - フレキシブル サーバー (プレビュー) を作成する
description: この Azure CLI サンプル スクリプトは、VNet に Azure Database for MySQL - フレキシブル サーバーを作成し (プライベート アクセス接続方法)、VNet 内の VM からそのサーバーに接続する方法を示します。
author: shreyaaithal
ms.author: shaithal
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2021
ms.openlocfilehash: eef5a814fd25d30a96ad3fd445d30e9daeadc596
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128604660"
---
# <a name="create-an-azure-database-for-mysql---flexible-server-preview-in-a-vnet-using-azure-cli"></a>Azure CLI を使用して VNet に Azure Database for MySQL - フレキシブル サーバー (プレビュー) を作成する

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

| <bpt id="p1">**</bpt>コマンド<ept id="p1">**</ept> | <bpt id="p1">**</bpt>注<ept id="p1">**</ept> |
|---|---|
|<bpt id="p1">[</bpt>az group create<ept id="p1">](/cli/azure/group#az_group_create)</ept>|すべてのリソースを格納するリソース グループを作成します。|
|[az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create)|データベースをホストするフレキシブル サーバーを作成します。|
|<bpt id="p1">[</bpt>az network vnet subnet create<ept id="p1">](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create)</ept>|VNet 内にサブネットを作成します。|
|<bpt id="p1">[</bpt>az vm create<ept id="p1">](/cli/azure/vm#az_vm_create)</ept>|Azure 仮想マシンを作成します。|
|<bpt id="p1">[</bpt>az vm open-port<ept id="p1">](/cli/azure/vm#az_vm_open_port)</ept>|指定したポートで受信トラフィックへの VM を開きます。|
|<bpt id="p1">[</bpt>az group delete<ept id="p1">](/cli/azure/group#az_group_delete)</ept> | 入れ子になったリソースすべてを含むリソース グループを削除します。|

## <a name="next-steps"></a>次のステップ

- 他のスクリプトを試す: [Azure Database for MySQL - フレキシブル サーバー (プレビュー) の Azure CLI サンプル](../sample-scripts-azure-cli.md)
- Azure CLI の詳細については、<bpt id="p1">[</bpt>Azure CLI のドキュメント<ept id="p1">](/cli/azure)</ept>のページをご覧ください。