---
title: デタッチされたディスクによる仮想マシンのデプロイのトラブルシューティング | Microsoft Docs
description: デタッチされたディスクによる仮想マシンのデプロイのトラブルシューティング
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2019
ms.author: vaaga
ms.openlocfilehash: e049a2b914cbf9c4f0ca0f3a1dd0281d58f881b2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477089"
---
# <a name="troubleshoot-virtual-machine-deployment-due-to-detached-disks"></a>デタッチされたディスクによる仮想マシンのデプロイのトラブルシューティング

## <a name="symptom"></a>症状

以前データ ディスクのデタッチに失敗した仮想マシンを更新しようとすると、このエラー コードが表示されることがあります。

```
Code=\"AttachDiskWhileBeingDetached\" 
Message=\"Cannot attach data disk '{disk ID}' to virtual machine '{vmName}' because the disk is currently being detached or the last detach  operation failed. Please wait until the disk is completely detached, and then try again or delete/detach the disk explicitly again\” 
```

## <a name="cause"></a>原因

このエラーは、最後のデタッチ操作が失敗したデータ ディスクを再アタッチしようとした場合に発生します。 この状態を回避する最善の方法は、障害が発生したディスクをデタッチすることです。

## <a name="solution-1-powershell"></a>解決策 1: Powershell

### <a name="step-1-get-the-virtual-machine-and-disk-details"></a>手順 1:仮想マシンとディスクの詳細情報を取得する

```azurepowershell-interactive
PS D:> $vm = Get-AzureRmVM -ResourceGroupName "Example Resource Group" -Name "ERGVM999999" 

PS D:> $vm.StorageProfile.DataDisks 
lun          : 0
name         : f94901ef-75ee-4477-9ad6-1c74da50e7ef
createOption : Attach
caching      : ReadOnly
managedDisk  : @{storageAccountType=Premium_LRS; id=/subscriptions/<subscription ID>/resourceGroups/<Resource Group name>}
diskSizeGB   : 8
toBeDetached : False 
```

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>手順 2:障害が発生したディスクのフラグを "true" に設定します。

障害が発生したディスクの配列インデックスを取得し、障害が発生したディスク (**AttachDiskWhileBeingDetached** エラーが発生したディスク) の **toBeDetached** フラグを "true" に設定します。 この設定は、仮想マシンからディスクをデタッチすることを意味します。 障害が発生したディスクの名前は **errorMessage** にあります。

> !注:Get および Put 呼び出しに指定される API バージョンは、2019-03-01 以上である必要があります。

```azurepowershell-interactive
PS D:> $vm.StorageProfile.DataDisks[0].ToBeDetached = $true 
```

または、次のコマンドを使用してこのディスクをデタッチすることもできます。これは、2019 年 3 月 1 日より前の API バージョンを使用するユーザーに役立ちます。

```azurepowershell-interactive
PS D:> Remove-AzureRmVMDataDisk -VM $vm -Name "<disk ID>" 
```

### <a name="step-3-update-the-virtual-machine"></a>手順 3:仮想マシンを更新する

```azurepowershell-interactive
PS D:> Update-AzureRmVM -ResourceGroupName "Example Resource Group" -VM $vm 
```

## <a name="solution-2-rest"></a>解決策 2: REST

### <a name="step-1-get-the-virtual-machine-payload"></a>手順 1:仮想マシンのペイロードを取得します。

```azurepowershell-interactive
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?$expand=instanceView&api-version=2019-03-01
```

### <a name="step-2-set-the-flag-for-failing-disks-to-true"></a>手順 2:障害が発生したディスクのフラグを "true" に設定します。

手順 1 で返されたペイロードで、障害が発生したディスクの **toBeDetached** フラグを true に設定します。 注意:Get および Put 呼び出しに指定される API バージョンは、`2019-03-01` 以上である必要があります。

**要求本文のサンプル**

```azurepowershell-interactive
{
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D2_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      },
      "dataDisks": [
        {
          "diskSizeGB": 1023,
          "createOption": "Empty",
        "name": "f94901ef-75ee-4477-9ad6-1c74da50e7ef",
          "lun": 0,
          "toBeDetached": true
        },
        {
          "diskSizeGB": 1023,
          "createOption": "Empty",
          "lun": 1,
          "toBeDetached": false
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

または、障害が発生したデータ ディスクを上記のペイロードから除去することもできます。これは、2019 年 3 月 1 日より前の API バージョンを使用するユーザーに役立ちます。

### <a name="step-3-update-the-virtual-machine"></a>手順 3:仮想マシンを更新する

手順 2 で設定した要求本文のペイロードを使用して、仮想マシンを次のように更新します。

```azurepowershell-interactive
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2019-03-01
```

**応答のサンプル:**

```azurepowershell-interactive
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "type": "Microsoft.Compute/virtualMachines",
  "properties": {
    "osProfile": {
      "adminUsername": "{your-username}",
      "secrets": [],
      "computerName": "myVM",
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/nsgExistingNic",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "osType": "Windows",
        "caching": "ReadWrite",
        "createOption": "FromImage",
        "name": "myVMosdisk",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        }
      },
      "dataDisks": [
        {
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Standard_LRS"
          },
          "createOption": "Empty",
          "lun": 0,
          "diskSizeGB": 1023,
        "name": "f94901ef-75ee-4477-9ad6-1c74da50e7ef",
          "toBeDetached": true
        },
        {
          "caching": "None",
          "managedDisk": {
            "storageAccountType": "Standard_LRS"
          },
          "createOption": "Empty",
          "lun": 1,
          "diskSizeGB": 1023,
          "toBeDetached": false
        }
      ]
    },
    "vmId": "3906fef9-a1e5-4b83-a8a8-540858b41df0",
    "hardwareProfile": {
      "vmSize": "Standard_D2_v2"
    },
    "provisioningState": "Updating"
  },
  "name": "myVM",
  "location": "westus"
}
```

## <a name="next-steps"></a>次の手順

VM への接続の問題が発生した場合は、[Azure VM への RDP 接続のトラブルシューティング](troubleshoot-rdp-connection.md)に関する記事をご覧ください。

VM で実行されているアプリケーションへのアクセスに関する問題については、[Windows VM でのアプリケーションの接続の問題のトラブルシューティング](troubleshoot-app-connection.md)に関する記事をご覧ください。
