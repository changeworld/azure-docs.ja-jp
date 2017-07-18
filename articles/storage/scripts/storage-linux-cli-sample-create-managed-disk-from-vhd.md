---
title: "Azure CLI サンプル スクリプト - 同じサブスクリプションのストレージ アカウントに VHD ファイルから管理ディスクを作成する | Microsoft Docs"
description: "Azure CLI サンプル スクリプト - 同じサブスクリプションのストレージ アカウントに VHD ファイルから管理ディスクを作成する"
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
ms.openlocfilehash: 2e0d668c1fc419abde95207654cab35ea8b058fb
ms.contentlocale: ja-jp
ms.lasthandoff: 05/23/2017

---

# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli"></a>CLI で同じサブスクリプションのストレージ アカウントに VHD ファイルから管理ディスクを作成する

このスクリプトは、同じサブスクリプションのストレージ アカウントに VHD ファイルから管理ディスクを作成します。 このスクリプトを使用して、特殊化された (汎用化/Sysprep されていない) VHD を管理対象 OS ディスクにインポートして仮想マシンを作成します。 または、このスクリプトを使用して、データ VHD を管理対象データ ディスクにインポートします。 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/storage/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "VHD から管理ディスクを作成する")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、以下のコマンドを使って VHD から管理ディスクを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az disk create](https://docs.microsoft.com/cli/azure/disk#create) | 同じサブスクリプション内のストレージ アカウントに VHD の URI を使用して管理ディスクを作成する |

## <a name="next-steps"></a>次のステップ

[管理ディスクを OS ディスクとして接続することで仮想マシンを作成する](./../../virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の仮想マシンおよび管理ディスクの CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../../virtual-machines/linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)にあります。

