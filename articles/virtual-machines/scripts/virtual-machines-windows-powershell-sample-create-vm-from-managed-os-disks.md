---
title: Azure PowerShell のサンプル スクリプト - マネージド ディスクを OS ディスクとして接続することで VM を作成する | Microsoft Docs
description: Azure PowerShell のサンプル スクリプト - マネージド ディスクを OS ディスクとして接続することで VM を作成します
services: virtual-machines-windows
documentationcenter: virtual-machines
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc
ms.openlocfilehash: 519eccdd1d1b4628f41e12616d3e82014a2d301b
ms.sourcegitcommit: 76797c962fa04d8af9a7b9153eaa042cf74b2699
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2018
ms.locfileid: "41918009"
---
# <a name="create-a-virtual-machine-using-an-existing-managed-os-disk-with-powershell"></a>PowerShell で既存の管理 OS ディスクを使用して仮想マシンを作成する

このスクリプトは、既存のマネージド ディスクを OS ディスクとして接続することで仮想マシンを作成します。 このスクリプトは、次のシナリオで使用します。
* 別のサブスクリプションのマネージド ディスクからコピーされた既存の管理 OS ディスクから VM を作成する
* 特化された VHD ファイルから作成された既存のマネージド ディスクから VM を作成する 
* スナップショットから作成された既存の管理 OS ディスクから VM を作成する 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from snapshot")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、次のコマンドを使用して、マネージド ディスクのプロパティを取得し、マネージド ディスクを新しい VM に接続して VM を作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [Get-AzureRmDisk](/powershell/module/azurerm.compute/Get-AzureRmDisk) | ディスクの名前とリソース グループに基づいてディスク オブジェクトを取得します。 返されたディスク オブジェクトの Id プロパティは、ディスクを新しい VM に接続するために使用されます。 |
| [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) | VM 構成を作成します。 この構成には、VM 名、オペレーティング システム、管理資格情報などの情報が含まれます。 この構成は、VM の作成時に使用されます。 |
| [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) | ディスクの Id プロパティを使用して、マネージド ディスクを OS ディスクとして新しいバーチャル マシンに接続します。 |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | パブリック IP アドレスを作成します。 |
| [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) | ネットワーク インターフェイスを作成します。 |
| [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) | 仮想マシンを作成します。 |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | リソース グループと、それに含まれているすべてのリソースを削除します。 |

マーケットプレース イメージの場合は、[Set-AzureRmVMPlan](https://docs.microsoft.com/en-us/powershell/module/azurerm.compute/set-azurermvmplan?view=azurermps-6.7.0) を使用してプラン情報を設定します。
```powershell
Set-AzureRmVMPlan -VM $VirtualMachine -Publisher $Publisher -Product $Product -Name $Bame
```

## <a name="next-steps"></a>次の手順

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
