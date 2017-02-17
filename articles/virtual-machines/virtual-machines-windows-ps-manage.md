---
title: "Resource Manager と PowerShell を使用した VM の管理 | Microsoft Docs"
description: "Azure Resource Manager と PowerShell を使用して仮想マシンを管理します。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 48930854-7888-4e4c-9efb-7d1971d4cc14
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: b54a95c4f81d9a981912e1b596a817dc6ad56334
ms.openlocfilehash: 52684fe3212454abbfb0cf9d1c67759fce9a1549


---
# <a name="manage-azure-virtual-machines-using-resource-manager-and-powershell"></a>Resource Manager と PowerShell を使用した Azure Virtual Machines の管理
## <a name="install-azure-powershell"></a>Azure PowerShell をインストールする
最新バージョンの Azure PowerShell をインストールし、サブスクリプションを選択して、ご利用のアカウントにサインインする方法については、「[Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」を参照してください。

## <a name="set-variables"></a>変数の設定
この記事のすべてのコマンドでは、仮想マシンがあるリソース グループの名前と、管理する仮想マシンの名前が必要です。 **$rgName** の値を、仮想マシンが含まれているリソース グループの名前に置き換えます。 **$vmName** の値を、VM の名前に置き換えます。 変数を作成します。

    $rgName = "resource-group-name"
    $vmName = "VM-name"

## <a name="display-information-about-a-virtual-machine"></a>仮想マシンに関する情報の表示
仮想マシンの情報を取得します。

    Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName

次のような結果が返されます。

    ResourceGroupName        : rg1
    Id                       : /subscriptions/{subscription-id}/resourceGroups/
                               rg1/providers/Microsoft.Compute/virtualMachines/vm1
    Name                     : vm1
    Type                     : Microsoft.Compute/virtualMachines
    Location                 : centralus
    Tags                     : {}
    AvailabilitySetReference : {
                                  "id": "/subscriptions/{subscription-id}/resourceGroups/
                                  rg1/providers/Microsoft.Compute/availabilitySets/av1"
                               }
    Extensions               : []
    HardwareProfile          : {
                                  "vmSize": "Standard_A0"
                               }
    InstanceView             : null
    NetworkProfile           : {
                                  "networkInterfaces": [
                                    {
                                      "properties.primary": null,
                                      "id": "/subscriptions/{subscription-id}/resourceGroups/
                                      rg1/providers/Microsoft.Network/networkInterfaces/nc1"
                                    }
                                  ]
                               }
    OSProfile                : {
                                  "computerName": "vm1",
                                  "adminUsername": "myaccount1",
                                  "adminPassword": null,
                                  "customData": null,
                                  "windowsConfiguration": {
                                    "provisionVMAgent": true,
                                    "enableAutomaticUpdates": true,
                                    "timeZone": null,
                                    "additionalUnattendContents": [],
                                    "winRM": null
                                  },
                                  "linuxConfiguration": null,
                                  "secrets": []
                               }
    Plan                     : null
    ProvisioningState        : Succeeded
    StorageProfile           : {
                                  "imageReference": {
                                    "publisher": "MicrosoftWindowsServer",
                                    "offer": "WindowsServer",
                                    "sku": "2012-R2-Datacenter",
                                    "version": "latest"
                                  },
                                  "osDisk": {
                                    "osType": "Windows",
                                    "encryptionSettings": null,
                                    "name": "osdisk",
                                    "vhd": {
                                      "Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
                                    }
                                    "image": null,
                                    "caching": "ReadWrite",
                                    "createOption": "FromImage"
                                  }
                                  "dataDisks": [],
                               }
    DataDiskNames            : {}
    NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/
                                rg1/providers/Microsoft.Network/networkInterfaces/nc1}

## <a name="stop-a-virtual-machine"></a>仮想マシンの停止
実行中の仮想マシンを停止します。

    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName

次のように確認が求められます。

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

仮想マシンを停止するには、「 **Y** 」と入力します。

数分後、次のような結果が返されます。

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:11:57 PM
    EndTime    : 9/13/2016 12:14:40 PM

## <a name="start-a-virtual-machine"></a>仮想マシンの起動
停止している場合は、仮想マシンを開始します。

    Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName

数分後、次のような結果が返されます。

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:32:55 PM
    EndTime    : 9/13/2016 12:35:09 PM

既に実行されている仮想マシンを再起動するには、次に説明するように **Restart-AzureRmVM** を使用します。

## <a name="restart-a-virtual-machine"></a>仮想マシンの再起動
実行中の仮想マシンを再起動します。

    Restart-AzureRmVM -ResourceGroupName $rgName -Name $vmName

次のような結果が返されます。

    StatusCode : Succeeded
    StartTime  : 9/13/2016 12:54:40 PM
    EndTime    : 9/13/2016 12:55:54 PM

## <a name="delete-a-virtual-machine"></a>仮想マシンの削除
仮想マシンを削除します。  

    Remove-AzureRmVM -ResourceGroupName $rgName –Name $vmName

> [!NOTE]
> **-Force** パラメーターを使用して確認プロンプトをスキップできます。
> 
> 

-Force パラメーターを使用していない場合は、次のように確認が求められます。

    Virtual machine removal operation
    This cmdlet will remove the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

次のような結果が返されます。

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## <a name="update-a-virtual-machine"></a>仮想マシンの更新
この例では、仮想マシンのサイズを更新する方法を示します。

    $vmSize = "Standard_A1"
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    $vm.HardwareProfile.vmSize = $vmSize
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm

次のような結果が返されます。

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

仮想マシンに使用できるサイズの一覧は、「 [Azure の仮想マシンのサイズ](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 」をご覧ください。


## <a name="next-steps"></a>次のステップ
デプロイに問題がある場合は、「[Troubleshoot common Azure deployment errors with Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)」(Azure Resource Manager を使用した Azure のデプロイで発生する一般的なエラーのトラブルシューティング) を参照してください。




<!--HONumber=Feb17_HO2-->


