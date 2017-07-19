---
title: "Azure CLI サンプル スクリプト - 別のリージョンのストレージ アカウントに、スナップショットを VHD としてエクスポート/コピーする | Microsoft Docs"
description: "Azure CLI サンプル スクリプト - 同じまたは別のサブスクリプションのストレージ アカウントに、スナップショットを VHD としてエクスポート/コピーする"
services: managed-disks-linux
documentationcenter: storage
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: managed-disks-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/19/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: be747170a0d8a7a6defd790a3f8a122c4d397671
ms.openlocfilehash: 6785b444e0a89ec3ee0c2cebd8a5af123bb367f3
ms.contentlocale: ja-jp
ms.lasthandoff: 05/23/2017

---

# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-cli"></a>CLI を使用して別のリージョンのストレージ アカウントに管理スナップショットを VHD としてエクスポート/コピーする

このスクリプトでは、管理スナップショットを別のリージョンのストレージ アカウントにエクスポートします。 最初にスナップショットの SAS URI を生成し、それを使用して別のリージョンのストレージ アカウントにコピーします。 このスクリプトを使用して、別のリージョンで管理ディスクのバックアップを維持し、ディザスター リカバリーに備えます。 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/storage/copy-snapshots-to-storage-account/copy-snapshots-to-storage-account.sh "Copy snapshot")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、次のコマンドを使用して管理スナップショットの SAS URI を生成し、SAS URI を使用してスナップショットをストレージ アカウントにコピーします。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az snapshot grant-access](https://docs.microsoft.com/cli/azure/snapshot#grant-access) | 基になる VHD ファイルのストレージ アカウントへのコピー、またはそのオンプレミスへのダウンロードに使用する、読み取り専用の SAS を生成します  |
| [az storage blob copy start](https://docs.microsoft.com/en-us/cli/azure/storage/blob/copy#start) | BLOB を、あるストレージ アカウントから別のストレージ アカウントに非同期的にコピーします |

## <a name="next-steps"></a>次のステップ

[VHD から管理ディスクを作成する](./../scripts/storage-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json)

[管理ディスクから仮想マシンを作成する](./../../virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の仮想マシンと管理ディスクの CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../../virtual-machines/linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。

