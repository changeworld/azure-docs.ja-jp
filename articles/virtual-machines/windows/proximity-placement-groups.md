---
title: Windows VM に近接通信配置グループを使用する | Microsoft Docs
description: Azure の Windows 仮想マシンに近接通信配置グループを作成して使用する方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 9e9b8dddf7f48fe672a37b0343d215e0735ed760
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302500"
---
# <a name="preview-deploy-vms-to-proximity-placement-groups-using-powershell"></a>更新:PowerShell を使用し、近接通信配置グループに VM をデプロイする


VM を可能な限り近くに置き、待ち時間を可能な限り短くするためには、[近接通信配置グループ](co-location.md#preview-proximity-placement-groups)内に VM をデプロイしてください。

近接通信配置グループは、Azure コンピューティング リソースが互いに近い場所に物理的に配置されるようにする目的で使用される論理グループです。 近接通信配置グループは、短い待ち時間が要件となるワークロードに役立ちます。

> [!IMPORTANT]
> 近接通信配置グループは現在のところ、パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
>
> プレビュー期間中、近接通信配置グループは、**日本東部**、**オーストラリア東部**、**インド中部**で利用できません。


## <a name="create-a-proximity-placement-group"></a>近接通信配置グループの作成
[New-AzProximityPlacementGroup](https://docs.microsoft.com/powershell/module/az.compute/new-azproximityplacementgroup) コマンドレットを使用し、近接通信配置グループを作成します。 

```azurepowershell-interactive
$resourceGroup = "myPPGResourceGroup"
$location = "East US"
$ppgName = "myPPG"
New-AzResourceGroup -Name $resourceGroup -Location $location
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard
```

## <a name="list-proximity-placement-groups"></a>近接通信配置グループの一覧表示

[Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) コマンドレットを使用し、近接通信配置グループをすべて一覧表示できます。

```azurepowershell-interactive
Get-AzProximityPlacementGroup
```


## <a name="create-a-vm"></a>VM の作成

[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) を使用して VM を作成するときに近接通信配置グループ ID を参照する目的で、`-ProximityPlacementGroup $ppg.Id` を使用し、近接通信配置グループで VM を作成します。

```azurepowershell-interactive
$vmName = "myVM"

New-AzVm `
  -ResourceGroupName $resourceGroup `
  -Name $vmName `
  -Location $location `
  -OpenPorts 3389 `
  -ProximityPlacementGroup $ppg.Id
```

[Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) を使用し、近接通信配置グループの VM を表示できます。

```azurepowershell-interactive
Get-AzProximityPlacementGroup -ResourceId $ppg.Id |
    Format-Table -Property VirtualMachines -Wrap
```

## <a name="availability-sets"></a>可用性セット
近接通信配置グループでは、可用性セットも作成できます。 同じ `-ProximityPlacementGroup` パラメーターを [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) コマンドレットで使用すると、可用性セットが作成されます。可用性セットで作成された VM はすべて、同じ近接通信配置グループでも作成されます。

## <a name="scale-sets"></a>スケール セット

また、近接通信配置グループにスケール セットを作成できます。 同じ `-ProximityPlacementGroup` パラメーターを [New-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) で使用すると、スケール セットが作成されます。すべてのインスタンスが同じ近接通信配置グループで作成されます。

## <a name="next-steps"></a>次の手順

[Azure CLI](../linux/proximity-placement-groups.md) を使用し、近接通信配置グループを作成することもできます。
