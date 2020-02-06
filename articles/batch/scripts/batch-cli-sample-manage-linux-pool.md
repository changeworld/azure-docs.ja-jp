---
title: Azure CLI のサンプル スクリプト - Batch での Linux プール
description: このスクリプトでは、Azure Batch で Linux コンピューティング ノードのプールを作成して管理するために使用できる Azure CLI のコマンドの一部を示します。
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: labrenne
ms.openlocfilehash: 7f896f7f6493cff8c25d4ccfd5a15f5edb11258f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024485"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>CLI の例:Azure Batch での Linux プールの作成と管理

このスクリプトでは、Azure Batch で Linux コンピューティング ノードのプールを作成して管理するために使用できる Azure CLI のコマンドの一部を示します。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイック スタートを実施するには、Azure CLI バージョン 2.0.20 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Linux Virtual Machine Pool")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループと、それに関連付けられているすべてのリソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | Notes |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az batch account create](/cli/azure/batch/account#az-batch-account-create) | Batch アカウントを作成します。 |
| [az batch account login](/cli/azure/batch/account#az-batch-account-login) | さらに CLI と対話できるように、指定された Batch アカウントを認証します。  |
| [az batch pool node-agent-skus list](../batch-linux-nodes.md#list-of-virtual-machine-images) | 使用できるノード エージェント SKU とイメージの情報を一覧表示します。  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-create) | コンピューティング ノードのプールを作成します。  |
| [az batch pool resize](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-resize) | 指定したプールで実行されている VM の数を変更します。  |
| [az batch pool show](https://docs.microsoft.com/cli/azure/batch/pool#az-batch-pool-show) | プールのプロパティを表示します。  |
| [az batch node list](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-list) | 指定したプール内のすべてのコンピューティング ノードを一覧表示します。  |
| [az batch node reboot](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-reboot) | 指定したコンピューティング ノードを再起動します。  |
| [az batch node delete](https://docs.microsoft.com/cli/azure/batch/node#az-batch-node-delete) | 指定したプールから、一覧表示されているノードを削除します。  |
| [az group delete](/cli/azure/group#az-group-delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。
