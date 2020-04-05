---
title: CLI の例 - フェールオーバー グループ - Azure SQL Database マネージド インスタンス
description: Azure SQL Database のマネージド インスタンスを作成し、それをフェールオーバー グループに追加して、フェールオーバーをテストする Azure CLI スクリプトの例。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 792746ec3bfcf429afb7919458b9ac7ec8446b60
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061855"
---
# <a name="use-cli-to-add-an-azure-sql-database-managed-instance-to-a-failover-group"></a>CLI を使用して Azure SQL Database のマネージド インスタンスをフェールオーバー グループに追加する

この Azure CLI サンプルでは、2 つのマネージド インスタンスを作成し、それらをフェールオーバー グループに追加して、プライマリ マネージド インスタンスからセカンダリ マネージド インスタンスへのフェールオーバーをテストします。

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="sample-scripts"></a>サンプルのスクリプト

### <a name="sign-in-to-azure"></a>Azure へのサインイン

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>スクリプトを実行する

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを使用して、リソース グループと、それに関連付けられているすべてのリソースを削除します。 リソース グループは 2 回削除する必要があります。 最初にリソース グループを削除すると、マネージド インスタンスと仮想クラスターが削除されますが、その後、エラー メッセージ `az group delete : Long running operation failed with status 'Conflict'.` が表示されて失敗します。 az group delete コマンドをもう一度実行すると、残りのすべてのリソースと、リソース グループが削除されます。

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>サンプル リファレンス

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| | |
|---|---|
| [az network vnet](/cli/azure/network/vnet) | 仮想ネットワークのコマンド。  |
| [az network vnet subnet](/cli/azure/network/vnet/subnet) | 仮想ネットワーク サブネットのコマンド。 |
| [az network nsg](/cli/azure/network/nsg) | ネットワーク セキュリティ グループのコマンド。 |
| [az network nsg rule](/cli/azure/network/nsg/rule)| ネットワーク セキュリティ規則のコマンド。 |
| [az network route-table](/cli/azure/network/route-table) | ルート テーブルのコマンド。 |
| [az sql mi](/cli/azure/sql/mi) | マネージド インスタンスのコマンド。 |
| [az network public-ip](/cli/azure/network/public-ip) | ネットワーク パブリック IP アドレスのコマンド。 |
| [az network vnet-gateway](/cli/azure/network/vnet-gateway) | Virtual Network ゲートウェイのコマンド。 |
| [az sql instance-failover-group](/cli/azure/sql/instance-failover-group) | マネージド インスタンスのフェールオーバー グループ コマンド。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の SQL Database 用の CLI サンプル スクリプトは、[Azure SQL Database のドキュメント](../sql-database-cli-samples.md)のページにあります。
