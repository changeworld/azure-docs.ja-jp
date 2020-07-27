---
title: 同じアカウントの VHD ファイルからマネージド ディスクを作成する (Linux) - CLI サンプル
description: Azure CLI サンプル スクリプト - 同じサブスクリプションのストレージ アカウントに VHD ファイルからマネージド ディスクを作成する
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
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
ms.openlocfilehash: bc02fb6801aa72d5c46d5fb0dc6498e5c1ded4ee
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86501519"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-the-same-subscription-with-cli-linux"></a>CLI で同じサブスクリプションのストレージ アカウントに VHD ファイルからマネージド ディスクを作成する (Linux)

このスクリプトは、同じサブスクリプションのストレージ アカウントに VHD ファイルからマネージド ディスクを作成します。 sysprep された汎用的な VHD ではなく特殊化された VHD を OS の管理ディスクにインポートして仮想マシンを作成するには、このスクリプトを使います。 または、このスクリプトを使用して、データ VHD をマネージド データ ディスクにインポートします。 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-managed-data-disks-from-vhd/create-managed-data-disks-from-vhd.sh "Create managed disk from VHD")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、以下のコマンドを使って VHD からマネージド ディスクを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az disk create](/cli/azure/disk) | 同じサブスクリプション内のストレージ アカウントに VHD の URI を使用してマネージド ディスクを作成する |

## <a name="next-steps"></a>次のステップ

[マネージド ディスクを OS ディスクとして接続することで仮想マシンを作成する](./virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

その他の仮想マシンとマネージド ディスクの CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)にあります。
