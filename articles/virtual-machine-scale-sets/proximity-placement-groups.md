---
title: 仮想マシン スケール セットの近接配置グループのプレビュー | Microsoft Docs
description: Azure の Windows 仮想マシンに近接通信配置グループを作成して使用する方法について説明します。
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/01/2019
ms.author: cynthn
ms.openlocfilehash: 6a4f145c6431e98bbe9575f128ace30a23a1b972
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850676"
---
# <a name="preview-creating-and-using-proximity-placement-groups-using-powershell"></a>更新:PowerShell を使用した近接配置グループの作成と使用

VM を可能な限り近くに置き、待ち時間を可能な限り短くするためには、[近接通信配置グループ](co-location.md#preview-proximity-placement-groups)内にスケール セットをデプロイする必要があります。

近接通信配置グループは、Azure コンピューティング リソースが互いに近い場所に物理的に配置されるようにする目的で使用される論理グループです。 近接通信配置グループは、短い待ち時間が要件となるワークロードに役立ちます。

> [!IMPORTANT]
> 近接通信配置グループは現在のところ、パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
>
> プレビュー期間中、近接通信配置グループは、**東日本**、**オーストラリア東部**、**インド中部**では利用できません。


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


## <a name="create-a-scale-set"></a>スケール セットを作成する

[New-AzVMSS](https://docs.microsoft.com/powershell/module/az.compute/new-azvmss) を使用してスケール セットを作成するときに近接通信配置グループ ID を参照する目的で、`-ProximityPlacementGroup $ppg.Id` を使用し、近接通信配置グループでスケールを作成します。

```azurepowershell-interactive
$scalesetName = "myVM"

New-AzVmss `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -VMScaleSetName $scalesetName `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicyMode "Automatic" `
  -ProximityPlacementGroup $ppg.Id
```

[Get-AzProximityPlacementGroup](/powershell/module/az.compute/get-azproximityplacementgroup) を使用して、近接通信配置グループ内のインスタンスを表示できます。

```azurepowershell-interactive
  Get-AzProximityPlacementGroup `
   -ResourceId $ppg.Id | Format-Table `
   -Wrap `
   -Property VirtualMachineScaleSets
```

## <a name="next-steps"></a>次の手順

[Azure CLI](../virtual-machines/linux/proximity-placement-groups.md) を使用して近接通信配置グループを作成することもできます。