---
title: "Azure CLI サンプル スクリプト - スナップショットから管理ディスクを作成する | Microsoft Docs"
description: "Azure CLI サンプル スクリプト - スナップショットから管理ディスクを作成する"
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
ms.openlocfilehash: 5d0c06f0359916ddb2230c67013679bbe8e8aff7
ms.contentlocale: ja-jp
ms.lasthandoff: 05/23/2017

---

# <a name="create-a-managed-disk-from-a-snapshot-with-cli"></a>CLIでスナップショットから管理ディスクを作成する

このスクリプトは、スナップショットから管理ディスクを作成します。 このスクリプトを使用して、OS またはデータ ディスクのスナップショットから仮想マシンを復元します。 OS およびデータ管理ディスクをそれぞれのスナップショットから作成してから、管理ディスクを接続することで新しい仮想マシンを作成します。 スナップショットから作成されたデータ ディスクを接続することで既存の VM のデータ ディスクを復元することもできます。


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/storage/create-managed-disks-from-snapshot/create-managed-disks-from-snapshot.sh "スナップショットから管理ディスクを作成する")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、以下のコマンドを使ってスナップショットから管理ディスクを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az snapshot show](https://docs.microsoft.com/cli/azure/snapshot#show) | スナップショットの名前とリソース グループのプロパティを使用して、そのスナップショットのすべてのプロパティを取得します。 Id プロパティは管理ディスクを作成するために使用されます。  |
| [az disk create](https://docs.microsoft.com/cli/azure/disk#create) | 管理対象スナップショット Id を使用して管理ディスクを作成します |

## <a name="next-steps"></a>次のステップ

[管理ディスクを OS ディスクとして接続することで仮想マシンを作成する](./../../virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json)

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の仮想マシンと管理ディスクの CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../../virtual-machines/linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)にあります。

