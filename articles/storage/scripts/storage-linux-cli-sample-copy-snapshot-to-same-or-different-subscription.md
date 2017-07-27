---
title: "Azure CLI サンプル スクリプト - CLI を使用して管理ディスクのスナップショットを同じまたは別のサブスクリプションにコピー (移動) する | Microsoft Docs"
description: "Azure CLI サンプル スクリプト - CLI を使用して管理ディスクのスナップショットを同じまたは別のサブスクリプションにコピー (移動) する"
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: be747170a0d8a7a6defd790a3f8a122c4d397671
ms.openlocfilehash: 7f09b204bd1901f426143e597399950d3c7dd110
ms.contentlocale: ja-jp
ms.lasthandoff: 05/23/2017

---

# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>CLI を使用して管理ディスクのスナップショットを同じまたは別のサブスクリプションにコピーする

このスクリプトは、同じまたは別のサブスクリプションに、管理ディスクのスナップショットをコピーします。 このスクリプトを使用すると、親スナップショットと同じリージョン内の別のサブスクリプションにスナップショットを移動できます。


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/storage/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは次のコマンドを使用して、ソースのスナップショットの ID を使用して対象のサブスクリプションにスナップショットを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#show) | スナップショットの名前とリソース グループのプロパティを使用して、そのスナップショットのすべてのプロパティを取得します。 ID プロパティを使用して、別のサブスクリプションにそのスナップショットをコピーします。  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot#create) | 親スナップショットの名前と ID を使用して別のサブスクリプションにスナップショットを作成することで、スナップショットをコピーします。  |

## <a name="next-steps"></a>次のステップ

[スナップショットから仮想マシンを作成する](./../../virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の仮想マシンと管理ディスクの CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../../virtual-machines/linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。

