---
title: スナップショットからマネージド ディスクを作成する (Linux) - PowerShell サンプル
description: Azure PowerShell サンプル スクリプト - スナップショットからマネージド ディスクを作成する
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 844bff9a363fb9734159e892006dd61440132a34
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079963"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell-linux"></a>PowerShell でスナップショットからマネージド ディスクを作成する (Linux)

このスクリプトでは、スナップショットからマネージド ディスクを作成します。 このスクリプトを使用して、OS またはデータ ディスクのスナップショットから仮想マシンを復元します。 OS およびデータのマネージド ディスクをそれぞれのスナップショットから作成してから、マネージド ディスクを接続することで新しい仮想マシンを作成します。 スナップショットから作成されたデータ ディスクを接続することで既存の VM のデータ ディスクを復元することもできます。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを使ってスナップショットからマネージド ディスクを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | スナップショットのプロパティを取得します。  |
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | ディスクの作成に使用するディスク構成を作成します。 この構成には、親スナップショットのリソース ID、親スナップショットと同じ場所、ストレージの種類が含まれます。  |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | パラメーターとして渡されたディスク構成、ディスク名、リソース グループ名を使ってディスクを作成します。 |

## <a name="next-steps"></a>次のステップ


[マネージド ディスクから仮想マシンを作成する](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
