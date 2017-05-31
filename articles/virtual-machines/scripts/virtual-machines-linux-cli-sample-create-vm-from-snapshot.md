---
title: "Azure CLI のサンプル スクリプト - スナップショットから VM を作成する | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - スナップショットから VM を作成します"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: b5e33d7dcee5b5241de796d632a9b6069b991f7b
ms.contentlocale: ja-jp
ms.lasthandoff: 05/16/2017

---

# <a name="create-a-virtual-machine-from-a-snapshot-with-cli"></a>CLI でスナップショットから仮想マシンを作成する

このスクリプトは、OS ディスクのスナップショットから仮想マシンを作成します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[メイン](../../../cli_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.sh "スナップショットから VM を作成する")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、管理ディスク、仮想マシン、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#show) | スナップショット名とリソース グループ名を使用して、スナップショットを取得します。 返されたオブジェクトの Id プロパティは、管理ディスクを作成するために作成されます。  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#create) | スナップショット ID、ディスク名、記憶域の種類、およびサイズを使用して、スナップショットから管理ディスクを作成します。  |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | 管理 OS ディスクを使用して VM を作成する |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。

