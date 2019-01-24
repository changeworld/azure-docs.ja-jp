---
title: Azure CLI のサンプル スクリプト - オペレーティング システム ディスクのマウント | Microsoft Docs
description: Azure CLI のサンプル スクリプト - オペレーティング システム ディスクのマウント
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 5459ba31d30a86efbe40239f9531298c10cf9d98
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2019
ms.locfileid: "54413602"
---
# <a name="troubleshoot-a-vms-operating-system-disk"></a>VM オペレーティング システム ディスクのトラブルシューティング

このスクリプトでは、障害が発生したかまたは問題のある仮想マシンのオペレーティング システム ディスクを、2 つ目の仮想マシンにデータ ディスクとしてマウントします。 これは、ディスクの問題のトラブルシューティングやデータの復元に役立ちます。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Quick Create VM")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az vm show](https://docs.microsoft.com/cli/azure/vm#az_vm_show) | 仮想マシンの一覧を返します。 このサンプルでは、仮想マシンのオペレーティング システム ディスクを返すクエリ オプションを使用しています。 返された値は、変数名 "uri" に追加されます。 |
| [az vm delete](https://docs.microsoft.com/cli/azure/vm#az_vm_delete) | 仮想マシンを削除します。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm) | 仮想マシンを作成します。  |
| [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk) | ディスクを仮想マシンに接続します。 |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#az_vm_list_ip_addresses) | 仮想マシンの IP アドレスを返します。 |

## <a name="next-steps"></a>次の手順

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。
