---
title: "Azure CLI サンプル スクリプト - 管理ディスクを同じまたは別のサブスクリプションにコピー (移動) する| Microsoft Docs"
description: "Azure CLI サンプル スクリプト - 管理ディスクを同じまたは別のサブスクリプションにコピー (移動) する"
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
ms.openlocfilehash: 90228d66150cf961557b6dbeed11308932677d83
ms.contentlocale: ja-jp
ms.lasthandoff: 05/23/2017

---

# <a name="copy-managed-disks-to-same-or-different-subscription-with-cli"></a>CLI を使用した同じまたは別のサブスクリプションへの管理ディスクのコピー

このスクリプトは、同じサブスクリプションまたは同じリージョン内の別のサブスクリプションに、管理ディスクをコピーします。 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/storage/copy-managed-disks-to-same-or-different-subscription/copy-managed-disks-to-same-or-different-subscription.sh "Copy managed disk")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは次のコマンドを使用して、ソースの管理ディスクの ID を使用して対象のサブスクリプションに新しい管理ディスクを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk#show) | 管理ディスクの名前とリソース グループのプロパティを使用して、その管理ディスクのすべてのプロパティを取得します。 ID プロパティを使用して、別のサブスクリプションにその管理ディスクをコピーします。  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#create) | 親管理ディスクの名前と ID を使用して別のサブスクリプションに新しい管理ディスクを作成することで、管理ディスクをコピーします。  |

## <a name="next-steps"></a>次のステップ

[管理ディスクから仮想マシンを作成する](./../../virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の仮想マシンと管理ディスクの CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../../virtual-machines/linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。

