---
title: Azure CLI のサンプル スクリプト - 低優先度の Batch AI クラスターの作成 | Microsoft Docs
description: Azure CLI のサンプル スクリプト - 低優先度のノード (仮想マシン) から成る Batch AI クラスターの作成および管理
services: batch-ai
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.devlang: azurecli
ms.topic: sample
ms.tgt-pltfrm: multiple
ms.workload: na
ms.date: 07/26/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: c56cd7d25ce391b67798c7833894e248f37300bf
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53406608"
---
# <a name="cli-example-create-and-manage-a-batch-ai-cluster-of-low-priority-nodes"></a>CLI の例:低優先度のノードからなる Batch AI クラスターの作成および管理

[!INCLUDE [batch-ai-retiring](../../../includes/batch-ai-retiring.md)]

このスクリプトでは、低優先度のノード (仮想マシン) で構成される Batch AI クラスターを作成して管理するために、Azure CLI で使用できるコマンドの一部を示します。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイック スタートでは、Azure CLI バージョン 2.0.38 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 

## <a name="example-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/batch-ai/create-cluster/create-cluster-low-priority.sh "Create Batch AI cluster - low-priority nodes")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

次のコマンドを実行して、リソース グループと、それらに関連付けられているすべてのリソースを削除します。

```azurecli-interactive
# Remove resource group for the cluster.
az group delete --name myResourceGroup

# Remove resource group for the auto-storage account.
az group delete --name batchaiautostorage
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az batchai workspace create](/cli/azure/batchai/workspace#az-batchai-workspace-create) | Batch AI ワークスペースを作成します。 |
| [az batchai cluster create](/cli/azure/batchai/cluster#az-batchai-cluster-create) | Batch AI クラスターを作成します。 |
| [az batchai cluster show](/cli/azure/batchai/cluster#az-batchai-cluster-show) | Batch AI クラスターに関する情報が表示されます。 |
| [az batchai cluster node list](/cli/azure/batchai/cluster/node#az-batchai-cluster-show) | Batch AI クラスター内のノードが一覧表示されます。 |
| [az batchai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize) | Batch AI クラスターのサイズを変更します。  |
| [az group delete](/cli/azure/group#az-group-delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。
