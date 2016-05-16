<properties
	pageTitle="仮想マシン スケール セットの VM を管理する | Microsoft Azure"
	description="Azure PowerShell を利用し、仮想マシン スケール セットで仮想マシンを管理する"
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="davidmu"/>

# 仮想マシン スケール セットで仮想マシンを管理する

この記事では、仮想マシン スケール セットで仮想マシン リソースを管理する方法を紹介します。

スケール セットで仮想マシンを管理するためのあらゆる作業で、管理するマシンのインスタンス ID が必要になります。[Azure リソース エクスプローラー](https://resources.azure.com)を利用し、スケール セットの仮想マシンのインスタンス ID を検索できます。リソース エクスプローラーを利用し、完了した作業の状態を確認することもできます。

最新バージョンの Azure PowerShell をインストールし、使用するサブスクリプションを選択して、Azure アカウントにサインインする方法については、「[Azure PowerShell のインストールと構成の方法](../powershell-install-configure.md)」を参照してください。

## 仮想マシン スケール セットに関する情報を表示する

スケール セットに関する全般情報を取得できます。これは、インスタンス ビューとも呼ばれています。あるいは、セットのリソースに関する情報など、より具体的な情報を取得できます。

このコマンドで、*resource group name* を仮想マシン スケール セットが含まれているリソース グループの名前に置き換え、*scale set name* を仮想マシン スケール セットの名前に置き換えます。それから、このコマンドを実行します。

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

次のような結果が返されます。

    Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
    UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
    VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
    ProvisioningState     : Succeeded
    OverProvision         :
    Id                    : /subscriptions/{subscription-id}/resourceGroups/myrg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                  : myvmss1
    Type                  : Microsoft.Compute/virtualMachineScaleSets
    Location              : centralus
    Tags                  :

このコマンドで、*resource group name* を仮想マシン スケール セットが含まれているリソース グループの名前に置き換え、*scale set name* を仮想マシン スケール セットの名前に置き換え、*InstanceId* を情報の取得元となる仮想マシンのインスタンス ID に置き換えます。それから、このコマンドを実行します。

    Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId #
        
次のような結果が返されます。

    InstanceId         : 1
    Sku                : Microsoft.Azure.Management.Compute.Models.Sku
    LatestModelApplied : True
    InstanceView       :
    HardwareProfile    :
    StorageProfile     : Microsoft.Azure.Management.Compute.Models.StorageProfile
    OsProfile          : Microsoft.Azure.Management.Compute.Models.OSProfile
    NetworkProfile     : Microsoft.Azure.Management.Compute.Models.NetworkProfile
    DiagnosticsProfile :
    AvailabilitySet    :
    ProvisioningState  : Succeeded
    LicenseType        :
    Plan               :
    Resources          :
    Id                 : /subscriptions/{subscription-id}/resourceGroups/myrg1/providers/Microsoft.
                         Compute/virtualMachineScaleSets/myvmss1/virtualMachines/1
    Name               : myvmss1_1
    Type               : Microsoft.Compute/virtualMachineScaleSets/virtualMachines
    Location           : centralus
    Tags               :
        
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

<!---HONumber=AcomDC_0504_2016-->