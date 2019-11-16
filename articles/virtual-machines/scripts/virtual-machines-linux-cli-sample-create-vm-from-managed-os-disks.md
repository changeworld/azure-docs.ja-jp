---
title: Azure CLI のサンプル スクリプト - マネージド ディスクを OS ディスクとして接続することで VM を作成します
description: Azure CLI のサンプル スクリプト - マネージド ディスクを OS ディスクとして接続することで VM を作成します
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
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
ms.openlocfilehash: 05263df1327eff49d0c9ec84316447ba563695ca
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039156"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-cli"></a>CLI で既存の管理 OS ディスクを使用して仮想マシンを作成する

このスクリプトは、既存のマネージド ディスクを OS ディスクとして接続することで仮想マシンを作成します。 このスクリプトは、次のシナリオで使用します。
* 別のサブスクリプションのマネージド ディスクからコピーされた既存の管理 OS ディスクから VM を作成する
* 特化された VHD ファイルから作成された既存のマネージド ディスクから VM を作成する 
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

このスクリプトは、次のコマンドを使用して、マネージド ディスクのプロパティを取得し、マネージド ディスクを新しい VM に接続して VM を作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [az disk show](https://docs.microsoft.com/cli/azure/disk) | ディスク名とリソース グループ名を使用して、マネージド ディスクのプロパティを取得します。 Id プロパティは、マネージド ディスクを新しい VM に接続するために使用されます。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | 管理 OS ディスクを使用して VM を作成する |
## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。
