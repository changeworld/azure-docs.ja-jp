---
title: 別のリージョンのストレージ アカウントに、スナップショットを VHD としてエクスポート/コピーする - PowerShell のサンプル
description: Azure PowerShell サンプル スクリプト - 別のリージョンのストレージ アカウントに、スナップショットを VHD としてエクスポート/コピーする
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
ms.openlocfilehash: e8a141710b9011fc46eb8df594725bf76c3d69cf
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385131"
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>PowerShell を使用して別のリージョンのストレージ アカウントに管理スナップショットを VHD としてエクスポート/コピーする

このスクリプトでは、管理対象のスナップショットを別のリージョンのストレージ アカウントにエクスポートします。 最初にスナップショットの SAS URI を生成し、それを使用して別のリージョンのストレージ アカウントにコピーします。 このスクリプトを使用して、別のリージョンでマネージド ディスクのバックアップを維持し、ディザスター リカバリーに備えます。  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して管理されているスナップショットの SAS URI を生成し、SAS URI を使用してスナップショットをストレージ アカウントにコピーします。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | Notes |
|---|---|
| [Grant-AzSnapshotAccess](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | スナップショットの SAS URI を生成します。この URI を使ってストレージ アカウントにスナップショットをコピーします。 |
| [New-AzStorageContext](https://docs.microsoft.com/powershell/module/az.storage/new-azstoragecontext) | アカウント名とキーを使ってストレージ アカウントのコンテキストを作成します。 このコンテキストを使って、ストレージ アカウントに対する読み取り/書き込み操作を実行できます。 |
| [Start-AzStorageBlobCopy](https://docs.microsoft.com/powershell/module/az.storage/start-azstorageblobcopy) | スナップショットの基になる VHD をストレージ アカウントにコピーします。 |

## <a name="next-steps"></a>次のステップ

[VHD からマネージド ディスクを作成する](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[マネージド ディスクから仮想マシンを作成する](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
