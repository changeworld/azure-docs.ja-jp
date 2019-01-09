---
title: Azure CLI サンプル スクリプト - CLI を使用してマネージド ディスクのスナップショットを同じまたは別のサブスクリプションにコピー (移動) する | Microsoft Docs
description: Azure CLI サンプル スクリプト - CLI を使用してマネージド ディスクのスナップショットを同じまたは別のサブスクリプションにコピー (移動) する
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: c8c9f2ab2bed2e05f540422e47fdc634cb011187
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2018
ms.locfileid: "53583274"
---
# <a name="copy-snapshot-of-a-managed-disk-to-same-or-different-subscription-with-cli"></a>CLI を使用してマネージド ディスクのスナップショットを同じまたは別のサブスクリプションにコピーする

このスクリプトは、同じまたは別のサブスクリプションに、マネージド ディスクのスナップショットをコピーします。 このスクリプトを使用すると、親スナップショットと同じリージョン内の別のサブスクリプションにスナップショットを移動できます。


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.sh "Copy snapshot")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、コピー元スナップショットの ID を使って、コピー先のサブスクリプションにスナップショットを作成します。そのために、以下のコマンドが使われています。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_show) | スナップショットの名前とリソース グループのプロパティを使用して、そのスナップショットのすべてのプロパティを取得します。 ID プロパティを使用して、別のサブスクリプションにそのスナップショットをコピーします。  |
| [az snapshot create](https://docs.microsoft.com/cli/azure/snapshot#az_snapshot_create) | 親スナップショットの名前と ID を使用して別のサブスクリプションにスナップショットを作成することで、スナップショットをコピーします。  |

## <a name="next-steps"></a>次の手順

[スナップショットから仮想マシンを作成する](./virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の仮想マシンとマネージド ディスクの CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)にあります。
