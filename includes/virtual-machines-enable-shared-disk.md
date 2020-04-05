---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 26e76731f663ac9038bc87182d52c4bd245f1b6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "77471698"
---
## <a name="limitations"></a>制限事項

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>ディスク サイズ

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-an-azure-shared-disk"></a>Azure 共有ディスクをデプロイする

共有ディスク機能が有効になっている管理ディスクをデプロイするには、新しいプロパティ `maxShares` を使用し、`>1`値を定義します。 これにより、複数の VM 間でディスクを共有できるようになります。

> [!IMPORTANT]
> `maxShares` の値は、ディスクがすべての VM からマウント解除されている場合にのみ設定または変更できます。 [に使用できる値については、](#disk-sizes)ディスクのサイズ`maxShares` を参照してください。

次のテンプレートを使用する前に、`[parameters('dataDiskName')]`、`[resourceGroup().location]`、`[parameters('dataDiskSizeGB')]`、および `[parameters('maxShares')]` を実際の値に置き換えてください。

```json
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "Premium_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

### <a name="using-azure-shared-disks-with-your-vms"></a>VM での Azure 共有ディスクの使用

`maxShares>1` を使用して共有ディスクをデプロイしたら、そのディスクを1つ以上の VM にマウントできます。

> [!IMPORTANT]
> ディスクを共有するすべての VM は、同じ [近接通信配置グループ](../articles/virtual-machines/windows/proximity-placement-groups.md)にデプロイする必要があります。

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"
$ppgName = "myPPG"
$ppg = New-AzProximityPlacementGroup `
   -Location $location `
   -Name $ppgName `
   -ResourceGroupName $resourceGroup `
   -ProximityPlacementGroupType Standard

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -ProximityPlacementGroup $ppg.Id

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>サポートされている SCSI PR コマンド

クラスター内の VM に共有ディスクをマウントしたら、SCSI PR を使用して、クォーラムを確立し、ディスクに読み取り/書き込みを行うことができます。 Azure 共有ディスクを使用する場合は、次の PR コマンドを使用できます。

ディスクを操作するには、次のようにして、「永続的な予約アクション」リストから開始します。

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

PR_RESERVE、PR_PREEMPT_RESERVATION、または PR_RELEASE_RESERVATION を使用する場合は、次のいずれかの永続的な予約の種類を指定します。

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

PR_RESERVE、PR_REGISTER_AND_IGNORE、PR_REGISTER_KEY、PR_PREEMPT_RESERVATION、PR_CLEAR_RESERVATION、または PR_RELEASE-RESERVATION を使用する場合は、永続的な予約キーを指定する必要もあります。


## <a name="next-steps"></a>次のステップ

共有ディスクの使用に関心がある場合は、[プレビューにサインアップします](https://aka.ms/AzureSharedDiskPreviewSignUp)。