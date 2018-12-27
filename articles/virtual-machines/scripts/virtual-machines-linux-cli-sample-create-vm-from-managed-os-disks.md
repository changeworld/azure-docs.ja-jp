---
title: Azure CLI のサンプル スクリプト - 管理ディスクを OS ディスクとして接続することで VM を作成する | Microsoft Docs
description: Azure CLI のサンプル スクリプト - 管理ディスクを OS ディスクとして接続することで VM を作成します
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 5d86710fd9173cd0bc3416fedec226f97f12d9d2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2018
ms.locfileid: "29847677"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-cli"></a>CLI で既存の管理 OS ディスクを使用して仮想マシンを作成する

このスクリプトは、既存の管理ディスクを OS ディスクとして接続することで仮想マシンを作成します。 このスクリプトは、次のシナリオで使用します。
* 別のサブスクリプションの管理ディスクからコピーされた既存の管理 OS ディスクから VM を作成する
* 特化された VHD ファイルから作成された既存の管理ディスクから VM を作成する 
* スナップショットから作成された既存の管理 OS ディスクから VM を作成する 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-attach-existing-managed-os-disk/create-vm-attach-existing-managed-os-disk.sh "Create VM from a managed disk")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、次のコマンドを使用して、管理ディスクのプロパティを取得し、管理ディスクを新しい VM に接続して VM を作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk#az_disk_show) | ディスク名とリソース グループ名を使用して、管理ディスクのプロパティを取得します。 Id プロパティは、管理ディスクを新しい VM に接続を使用するために使用されます。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) | 管理 OS ディスクを使用して VM を作成する |
## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。
