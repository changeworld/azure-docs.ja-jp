---
title: Windows VHD の特殊化されたイメージから Azure Stack Edge Pro GPU デバイス用の VM イメージを作成する
description: Windows VHD または VHDX から起動する特殊化されたイメージから、VM イメージを作成する方法について説明します。 この特殊化されたイメージを使用して、Azure Stack Edge Pro GPU デバイスにデプロイされた VM で使用する VM イメージを作成します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
ms.openlocfilehash: d03aeb9759fb321b580fa65e06dc09ccde4a44a0
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106555877"
---
# <a name="deploy-a-vm-from-a-specialized-image-on-your-azure-stack-edge-pro-device-via-azure-powershell"></a>Azure PowerShell を使用して、Azure Stack Edge Pro デバイス上に特殊化されたイメージから VM をデプロイする 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、特殊化されたイメージから Azure Stack Edge Pro デバイスに仮想マシン (VM) をデプロイするために必要な手順について説明します。 

## <a name="about-specialized-images"></a>特殊化されたイメージについて

Windows VHD または VHDX を使用すると、"*特殊化された*" イメージまたは "*一般化された*" イメージを作成できます。 次の表は、"*特殊化*" されたイメージと "*一般化*" されたイメージの主な違いをまとめたものです。


|イメージの種類  |一般化されたイメージ  |専用イメージ  |
|---------|---------|---------|
|移行先     |どのシステムにもデプロイされる         | 特定のシステムが対象        |
|ブート後のセットアップ     | VM の初回ブート時にセットアップが必要。          | セットアップは不要。 <br> VM はプラットフォームによってオンにされる。        |
|構成     |ホスト名、管理者ユーザー、その他の VM 固有の設定が必要。         |事前に構成済み。         |
|用途     |同じイメージから複数の新しい VM を作成する。         |特定のマシンを移行する、または以前のバックアップから VM を復元する。         |


この記事では、特殊化されたイメージからのデプロイに必要な手順を取り上げます。 一般化されたイメージからデプロイするには、お使いのデバイスでの[一般化された Windows VHD の使用](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)に関するページを参照してください。


## <a name="vm-image-workflow"></a>VM イメージのワークフロー

特殊化されたイメージから VM をデプロイするための大まかなワークフローは次のとおりです。

1. Azure Stack Edge Pro GPU デバイス上のローカル ストレージ アカウントに VHD をコピーします。
1. VHD から新しいマネージド ディスクを作成します。
1. マネージド ディスクから新しい仮想マシンを作成し、マネージド ディスクをアタッチします。


## <a name="prerequisites"></a>前提条件

PowerShell を使用してデバイス上に VM をデプロイする前に、以下を確認してください。

- デバイスへの接続に使用するクライアントにアクセスできること。
    - クライアントが、[サポートされている OS](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) を実行していること。
    - [Azure Resource Manager へのデバイスの接続](azure-stack-edge-gpu-connect-resource-manager.md)に関する記事の手順に従って、クライアントがデバイスのローカル Azure Resource Manager に接続できるように構成されていること。

## <a name="verify-the-local-azure-resource-manager-connection"></a>ローカル Azure Resource Manager への接続を確認する

クライアントがローカル Azure Resource Manager に接続できることを確認します。 

1. ローカル デバイス API を呼び出して認証を行います。

    ```powershell
    Login-AzureRMAccount -EnvironmentName <Environment Name>
    ```

2. Azure Resource Manager を介して接続するには、ユーザー名 `EdgeArmUser` とパスワードを指定します。 パスワードを忘れた場合は、[Azure Resource Manager のパスワードをリセットし](azure-stack-edge-gpu-set-azure-resource-manager-password.md)、そのパスワードを使用してサインインします。
 

## <a name="deploy-vm-from-specialized-image"></a>特殊化されたイメージから VM をデプロイする

以下のセクションでは、特殊化されたイメージから VM をデプロイする手順について詳しく説明します。

## <a name="copy-vhd-to-local-storage-account-on-device"></a>デバイス上のローカル ストレージ アカウントに VHD をコピーする

VHD をローカル ストレージ アカウントにコピーするには、次の手順に従います。

1. 自分の Azure Stack Edge のローカル BLOB ストレージ アカウントにソース VHD をコピーします。 

1. 結果の URI をメモしておきます。 この URI は、後の手順で使用します。
    
    ローカル ストレージ アカウントを作成してアクセスする方法については、「[Azure PowerShell を使用して Azure Stack Edge デバイスに VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)」の「[ストレージ アカウントの作成](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#create-a-storage-account)」から「[VHD のアップロード](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd)」までのセクションを参照してください。 

## <a name="create-a-managed-disk-from-vhd"></a>VHD からマネージド ディスクを作成する

先ほどストレージ アカウントにアップロードした VHD からマネージド ディスクを作成するには、次の手順に従います。

1. パラメーターを設定する。

    ```powershell
    $VhdURI = <URI of VHD in local storage account>
    $DiskRG = <managed disk resource group>
    $DiskName = <managed disk name>    
    ```
    出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> $VHDURI = "https://myasevmsa.blob.myasegpudev.wdshcsso.com/vhds/WindowsServer2016Datacenter.vhd"
    PS C:\WINDOWS\system32> $DiskRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $DiskName = "myasemd1"
    ```
1. 新しいマネージド ディスクを作成します。

    ```powershell
    $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VhdURI
    $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    ```

    出力例を次に示します。 ここでの場所は、ローカル ストレージ アカウントの場所に設定され、Azure Stack Edge Pro GPU デバイス上のすべてのローカル ストレージ アカウントに対して常に `DBELocal` となります。 

    ```powershell
    PS C:\WINDOWS\system32> $DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri $VHDURI
    PS C:\WINDOWS\system32> $disk = New-AzureRMDisk -ResourceGroupName $DiskRG -DiskName $DiskName -Disk $DiskConfig
    PS C:\WINDOWS\system32>    
    ```
## <a name="create-a-vm-from-managed-disk"></a>マネージド ディスクから VM を作成する

マネージド ディスクから VM を作成するには、次の手順に従います。

1. パラメーターを設定する。

    ```powershell
    $NicRG = <NIC resource group>
    $NicName = <NIC name>
    $IPConfigName = <IP config name>
    $PrivateIP = <IP address> #Optional
    
    $VMRG = <VM resource group>
    $VMName = <VM name>
    $VMSize = <VM size> 
    ```

    >[!NOTE]
    > `PrivateIP` パラメーターは省略可能です。 静的 IP を割り当てるには、このパラメーターを使用します。そうしない場合の既定値は DHCP を使用した動的 IP となります。

    出力例を次に示します。 必要に応じて、リソースに対して個別のリソース グループを作成して指定することもできますが、この例では、すべての VM リソースに対して同じリソース グループが指定されています。

    ```powershell
    PS C:\WINDOWS\system32> $NicRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $NicName = "myasevmnic1"
    PS C:\WINDOWS\system32> $IPConfigName = "myaseipconfig1" 

    PS C:\WINDOWS\system32> $VMRG = "myasevm1rg"
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMSize = "Standard_D1_v2"   
    ```

1. 仮想ネットワークの情報を取得し、新しいネットワーク インターフェイスを作成します。

    このサンプルでは、既定のリソース グループ `ASERG` に関連付けられている既定の仮想ネットワーク `ASEVNET` 上に 1 つのネットワーク インターフェイスを作成することを前提にしています。 必要に応じて、別の仮想ネットワークを指定することも、複数のネットワーク インターフェイスを作成することもできます。 詳細については、[Azure portal を使用した VM へのネットワーク インターフェイスの追加](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md)に関する記事を参照してください。

    ```powershell
    $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id [-PrivateIpAddress $PrivateIP]
    $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    ```

    出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> $armVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG
    PS C:\WINDOWS\system32> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName -SubnetId $armVN.Subnets[0].Id
    PS C:\WINDOWS\system32> $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $NicRG -Location DBELocal -IpConfiguration $ipConfig
    WARNING: The output object type of this cmdlet will be modified in a future release.
    PS C:\WINDOWS\system32>    
    ```
1. 新しい VM 構成オブジェクトを作成します。

    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    ```

    
1. VM にネットワーク インターフェイスを追加します。

    ```powershell
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    ```

1. VM 上の OS ディスクのプロパティを設定します。

    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach –[Windows/Linux]
    ```
    このコマンドの最後のフラグは、VM で使用している OS に応じて `-Windows` または `-Linux` のいずれかになります。

1. VM を作成します。

    ```powershell
    New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm 
    ```

    出力例を次に示します。 

    ```powershell
    PS C:\WINDOWS\system32> $vmConfig = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
    PS C:\WINDOWS\system32> $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
    PS C:\WINDOWS\system32> $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -StorageAccountType StandardLRS -CreateOption Attach -Windows
    PS C:\WINDOWS\system32> New-AzureRmVM -ResourceGroupName $VMRG -Location DBELocal -VM $vm
    WARNING: Since the VM is created using premium storage or managed disk, existing standard storage account, myasevmsa, is used for
    boot diagnostics.    
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK        
    PS C:\WINDOWS\system32>
    ```

## <a name="delete-vm-and-resources"></a>VM とリソースを削除する

この記事では、リソース グループを 1 つだけ使用して、すべての VM リソースを作成しました。 そのリソース グループを削除すると、VM とすべての関連リソースが削除されます。 

1. 最初に、リソース グループの下に作成されているすべてのリソースを表示します。

    ```powershell
    Get-AzureRmResource -ResourceGroupName <Resource group name>
    ```
    出力例を次に示します。
    
    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResource -ResourceGroupName myasevm1rg
    
    
    Name              : myasemd1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/disks
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/disk
                        s/myasemd1
    
    Name              : myasetestvm1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Compute/virt
                        ualMachines/myasetestvm1
    
    Name              : myasevmnic1
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Network/networkInterfaces
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Network/netw
                        orkInterfaces/myasevmnic1
    
    Name              : myasevmsa
    ResourceGroupName : myasevm1rg
    ResourceType      : Microsoft.Storage/storageaccounts
    Location          : dbelocal
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myasevm1rg/providers/Microsoft.Storage/stor
                        ageaccounts/myasevmsa
    
    PS C:\WINDOWS\system32>
    ```

1. リソース グループとすべての関連リソースを削除します。

    ```powershell
    Remove-AzureRmResourceGroup -ResourceGroupName <Resource group name>
    ```
    出力例を次に示します。
    
    ```powershell
    PS C:\WINDOWS\system32> Remove-AzureRmResourceGroup -ResourceGroupName myasevm1rg
    
    Confirm
    Are you sure you want to remove resource group 'myasevm1rg'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    True
    PS C:\WINDOWS\system32>
    ```

1. リソース グループが削除されたことを確認します。 デバイス上に存在するすべてのリソース グループを取得します。 

    ```powershell
    Get-AzureRmResourceGroup
    ```
    出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> Get-AzureRmResourceGroup

    ResourceGroupName : ase-image-resourcegroup
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ase-image-resourcegroup
    
    ResourceGroupName : ASERG
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/ASERG
    
    ResourceGroupName : myaserg
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg
        
    PS C:\WINDOWS\system32>
    ```

## <a name="next-steps"></a>次のステップ

デプロイの性質に応じて、次のいずれかの手順を選択できます。

- [Azure PowerShell を使用して、一般化されたイメージから VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  
- [Azure portal を使用して VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
