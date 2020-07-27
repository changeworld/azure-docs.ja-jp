---
title: スナップショットからマネージド ディスクを作成する (Windows) - PowerShell サンプル
description: Azure PowerShell サンプル スクリプト - スナップショットからマネージド ディスクを作成する
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: d14f78b361c0b66b86886df7ffd7abbfc72cff35
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500992"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell-windows"></a>PowerShell でスナップショットからマネージド ディスクを作成する (Windows)

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

[マネージド ディスクから仮想マシンを作成する](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
