---
title: VHD からスナップショットを作成して、同一のマネージド ディスクを複数作成する - PowerShell サンプル
description: Azure PowerShell サンプル スクリプト - VHD からスナップショットを作成してまったく同じ複数のマネージド ディスクを短時間で作成する
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: de89ff77d09124449b64f664c60c72f870319b93
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2020
ms.locfileid: "75613807"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>PowerShell で VHD からスナップショットを作成してまったく同じ複数のマネージド ディスクを短時間で作成する

このスクリプトでは、同じまたは別のサブスクリプションのストレージ アカウントに VHD ファイルからスナップショットを作成します。 sysprep された汎用的な VHD ではなく特殊化された VHD をインポートしてスナップショットを作成し、そのスナップショットを使ってまったく同じ複数のマネージド ディスクを短時間で作成するには、このスクリプトを使います。 また、データ VHD をスナップショットにインポートし、そのスナップショットを使って複数のマネージド ディスクを短時間で作成する際にも使うことができます。 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="next-steps"></a>次のステップ

[スナップショットからマネージド ディスクを作成する](virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[マネージド ディスクを OS ディスクとして接続することで仮想マシンを作成する](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
