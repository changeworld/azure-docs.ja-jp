---
title: 仮想マシン スケール セットの VM を管理する | Microsoft Docs
description: Azure PowerShell を利用し、仮想マシン スケール セットで仮想マシンを管理する
services: virtual-machine-scale-sets
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2016
ms.author: davidmu

---
# 仮想マシン スケール セットで仮想マシンを管理する
この記事では、仮想マシン スケール セットで仮想マシン リソースを管理する方法を紹介します。

スケール セットで仮想マシンを管理するためのあらゆる作業で、管理するマシンのインスタンス ID が必要になります。[Azure リソース エクスプローラー](https://resources.azure.com)を利用し、スケール セットの仮想マシンのインスタンス ID を検索できます。リソース エクスプローラーを利用し、完了した作業の状態を確認することもできます。

最新バージョンの Azure PowerShell をインストールし、使用するサブスクリプションを選択して、Azure アカウントにサインインする方法については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」を参照してください。

## 仮想マシン スケール セットに関する情報を表示する
スケール セットに関する全般情報を取得できます。これは、インスタンス ビューとも呼ばれています。あるいは、セットのリソースに関する情報など、より具体的な情報を取得できます。

このコマンドで、*resource group name* を仮想マシン スケール セットが含まれているリソース グループの名前に置き換え、*scale set name* を仮想マシン スケール セットの名前に置き換えます。それから、このコマンドを実行します。

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

次のような結果が返されます。

    Id                                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                                        : myvmss1
    Type                                        : Microsoft.Compute/virtualMachineScaleSets
    Location                                    : centralus
    Sku                                         :
      Name                                      : Standard_A0
      Tier                                      : Standard
      Capacity                                  : 3
    UpgradePolicy                               :
      Mode                                      : Manual
    VirtualMachineProfile                       :
      OsProfile                                 :
        ComputerNamePrefix                      : vmss1
        AdminUsername                           : admin1
        WindowsConfiguration                    :
          ProvisionVMAgent                      : True
          EnableAutomaticUpdates                : True
    StorageProfile                              :
      ImageReference                            :
        Publisher                               : MicrosoftWindowsServer
        Offer                                   : WindowsServer
        Sku                                     : 2012-R2-Datacenter
        Version                                 : latest
      OsDisk                                    :
        Name                                    : vmssosdisk
        Caching                                 : ReadOnly
        CreateOption                            : FromImage
        VhdContainers[0]                        : https://astore.blob.core.windows.net/vmss
        VhdContainers[1]                        : https://gstore.blob.core.windows.net/vmss
        VhdContainers[2]                        : https://mstore.blob.core.windows.net/vmss
        VhdContainers[3]                        : https://sstore.blob.core.windows.net/vmss
        VhdContainers[4]                        : https://ystore.blob.core.windows.net/vmss
    NetworkProfile                              :
      NetworkInterfaceConfigurations[0]         :
        Name                                    : mync1
        Primary                                 : True
        IpConfigurations[0]                     :
          Name                                  : ip1
          Subnet                                :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/virtualNetworks/myvn1/subnets/mysn1
          LoadBalancerBackendAddressPools[0]    :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/backendAddressPools/bepool1
        LoadBalancerInboundNatPools[0]          :
            Id                                  : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Network/loadBalancers/mylb1/inboundNatPools/natpool1
    ExtensionProfile                            :
      Extensions[0]                             :
        Name                                    : Microsoft.Insights.VMDiagnosticsSettings
        Publisher                               : Microsoft.Azure.Diagnostics
        Type                                    : IaaSDiagnostics
        TypeHandlerVersion                      : 1.5
        AutoUpgradeMinorVersion                 : True
        Settings                                : {"xmlCfg":"...","storageAccount":"astore"}
    ProvisioningState                           : Succeeded

このコマンドで、*resource group name* を仮想マシン スケール セットが含まれているリソース グループの名前に置き換え、*scale set name* を仮想マシン スケール セットの名前に置き換え、*InstanceId* を情報の取得元となる仮想マシンのインスタンス ID に置き換えます。それから、このコマンドを実行します。

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

次のような結果が返されます。

    Id                            : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/
                                    virtualMachineScaleSets/myvmss1/virtualMachines/0
    Name                          : myvmss1_0
    Type                          : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location                      : centralus
    InstanceId                    : 0
    Sku                           :
      Name                        : Standard_A0
      Tier                        : Standard
    LatestModelApplied            : True
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : 4.0.20160617
      OsDisk                      :
        OsType                    : Windows
        Name                      : vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8
        Vhd                       :
          Uri                     : https://astore.blob.core.windows.net/vmss/vmssosdisk-os-0-e11cad52959b4b76a8d9f26c5190c4f8.vhd
        Caching                   : ReadOnly
        CreateOption              : FromImage
    OsProfile                     :
      ComputerName                : myvmss1-0
      AdminUsername               : admin1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
        EnableAutomaticUpdates    : True
    NetworkProfile                :
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/
                                    myvmss1/virtualMachines/0/networkInterfaces/mync1
    ProvisioningState             : Succeeded
    Resources[0]                  :
      Id                          : /subscriptions/{sub-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachines/
                                    myvmss1_0/extensions/Microsoft.Insights.VMDiagnosticsSettings
      Name                        : Microsoft.Insights.VMDiagnosticsSettings
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Azure.Diagnostics
      VirtualMachineExtensionType : IaaSDiagnostics
      TypeHandlerVersion          : 1.5
      AutoUpgradeMinorVersion     : True
      Settings                    : {"xmlCfg":"...","storageAccount":"astore"}
      ProvisioningState           : Succeeded

## スケール セットで仮想マシンを起動する
このコマンドで、*resource group name* を仮想マシン スケール セットが含まれているリソース グループの名前に置き換え、*scale set name* をスケール セットの名前に置き換え、*InstanceId* を起動する仮想マシンの ID に置き換えます。それから、このコマンドを実行します。

    Start-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

リソース エクスプローラーで、インスタンスの状態が **running** (実行中) であることがわかります。

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

-InstanceId パラメーターを使用しなければ、セット内のすべての仮想マシンを開始できます。

## スケール セットで仮想マシンを停止する
このコマンドで、*resource group name* を仮想マシン スケール セットが含まれているリソース グループの名前に置き換え、*scale set name* をスケール セットの名前に置き換え、*InstanceId* を停止する仮想マシンの ID に置き換えます。それから、このコマンドを実行します。

    Stop-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

リソース エクスプローラーで、インスタンスの状態が **deallocated** (割り当て解除) であることがわかります。

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]

仮想マシンを停止して割当てを解除しない場合、-StayProvisioned パラメーターを使用します。-InstanceId パラメーターを使用しなければ、セット内のすべての仮想マシンを停止できます。

## スケール セットで仮想マシンを再起動する
このコマンドで、*resource group name* を仮想マシン スケール セットが含まれているリソース グループの名前に置き換え、*scale set name* をスケール セットの名前に置き換え、*InstanceId* を再起動する仮想マシンの ID に置き換えます。それから、このコマンドを実行します。

    Restart-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #

-InstanceId パラメーターを使用しなければ、セット内のすべての仮想マシンを再起動できます。

## スケール セットから仮想マシンを削除する
このコマンドで、*resource group name* を仮想マシン スケール セットが含まれているリソース グループの名前に置き換え、*scale set name* をスケール セットの名前に置き換え、*InstanceId* をスケール セットから削除する仮想マシンの ID に置き換えます。それから、このコマンドを実行します。

    Remove-AzureRmVmss -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId #

-InstanceId パラメーターを使用しなければ、すべての仮想マシンをスケール セットから削除できます。

<!---HONumber=AcomDC_0720_2016-->