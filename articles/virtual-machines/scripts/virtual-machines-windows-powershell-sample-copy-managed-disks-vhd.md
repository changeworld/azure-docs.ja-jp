---
title: Azure PowerShell スクリプト サンプル - 別の地域のストレージ アカウントに、マネージド ディスクの VHD をエクスポート/コピーする | Microsoft Docs
description: Azure PowerShell スクリプト サンプル - 同じまたは別の地域のストレージ アカウントに、マネージド ディスクの VHD をエクスポート/コピーする
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/17/2018
ms.author: ramankum
ms.openlocfilehash: 978ac07037e1b7e29d83cc3258df01c6f902cd36
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2018
ms.locfileid: "48045269"
---
# <a name="exportcopy-the-vhd-of-a-managed-disk-to-a-storage-account-in-different-region-with-powershell"></a>別の地域のストレージ アカウントに、マネージド ディスクの VHD を PowerShell でエクスポート/コピーする

このスクリプトでは、別の地域のストレージ アカウントに、マネージド ディスクの VHD をエクスポートします。 最初にマネージド ディスクの SAS URI を生成し、続いてそれを使用して、基盤となる VHD を別の地域のストレージ アカウントにコピーします。 このスクリプトを使用して、地域拡張のために、マネージド ディスクを別の地域にコピーします。  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.ps1 "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用してマネージド ディスクの SAS URI を生成し、SAS URI を使用して基盤となる VHD をストレージ アカウントにコピーします。 表内の各コマンドは、コマンド特定のドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [Grant-AzureRmDiskAccess](/powershell/module/azurerm.compute/grant-azurermdiskaccess) | 基盤となる VHD をストレージ アカウントにコピーするために使用される、マネージド ディスク用の SAS URI を生成します。 |
| [New-AzureStorageContext](/powershell/module/azure.storage/New-AzureStorageContext) | アカウント名とキーを使ってストレージ アカウントのコンテキストを作成します。 このコンテキストを使って、ストレージ アカウントに対する読み取り/書き込み操作を実行できます。 |
| [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/Start-AzureStorageBlobCopy) | スナップショットの基になる VHD をストレージ アカウントにコピーします。 |

## <a name="next-steps"></a>次の手順

[VHD からマネージド ディスクを作成する](virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fpowershell%2fmodule%2ftoc.json)

[マネージド ディスクから仮想マシンを作成する](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。