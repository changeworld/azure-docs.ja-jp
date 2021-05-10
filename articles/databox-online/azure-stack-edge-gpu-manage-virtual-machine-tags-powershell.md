---
title: Azure PowerShell を使用して Azure Stack Edge Pro GPU デバイスで VM タグを管理する
description: Azure PowerShell を使用して Azure Stack Edge で実行する仮想マシンの仮想マシン タグを作成および管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/06/2021
ms.author: alkohli
ms.openlocfilehash: be4348359e6b53c3e7454e9ab7c1af8ce8a7020a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305559"
---
# <a name="manage-vm-tags-on-azure-stack-edge-via-azure-powershell"></a>Azure PowerShell を使用して Azure Stack Edge で VM タグを管理する

この記事では、Azure PowerShell を使用して、Azure Stack Edge Pro GPU デバイスで実行されている仮想マシン (VM) にタグを付ける方法について説明します。

## <a name="about-tags"></a>タグについて

タグは、リソースまたはリソースグ ループに割り当てることができるユーザー定義のキーと値のペアです。 デバイスで実行されている VM にタグを適用して、分類別で論理的に整理することができます。 タグは、リソースの作成時に付けたり、既存のリソースに追加したりすることができます。 たとえば、組織内のエンジニアリング部門によって使用されているすべての VM に名前 `Organization` と値 `Engineering` を適用できます。

タグの詳細については、[AzureRM PowerShell を使用してタグを管理](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true)する方法を参照してください。

## <a name="prerequisites"></a>必須コンポーネント

PowerShell を使用してデバイス上に VM をデプロイする前に、以下を確認してください。

- デバイスへの接続に使用するクライアントにアクセスできる。
    - クライアントは[サポートされている OS](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) を実行している。
    - クライアントは、[デバイスの Azure Resource Manager への接続](azure-stack-edge-gpu-connect-resource-manager.md)の手順に従って、デバイスのローカル Azure Resource Manager に接続するよう構成されている。


## <a name="verify-connection-to-local-azure-resource-manager"></a>ローカル Azure Resource Manager への接続を確認する

[!INCLUDE [azure-stack-edge-gateway-verify-azure-resource-manager-connection](../../includes/azure-stack-edge-gateway-verify-azure-resource-manager-connection.md)]


## <a name="add-a-tag-to-a-vm"></a>VM にタグを追加する

1. パラメーターを設定する。

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
    $TagValue = <Tag Value>   
    ```

    出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```

2. VM オブジェクトとそのタグを取得します。

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $tags = $VirtualMachine.Tags
   ```

3. タグを追加し、VM を更新します。 VM の更新には数分かかる可能性があります。

    省略可能な **-Force** フラグを使用して、ユーザーに確認することなくコマンドを実行できます。

    ```powershell
    $tags.Add($TagName, $TagValue)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```

    出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $tags = $VirtualMachine.Tags
    PS C:\WINDOWS\system32> $tags.Add($TagName, $TagValue)
    PS C:\WINDOWS\system32> Set-AzureRmResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    
    Name              : myasetestvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
                        lMachines/myasetestvm1
    ResourceName      : myasetestvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg2
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {Organization}
    Properties        : @{vmId=958c0baa-e143-4d8a-82bd-9c6b1ba45e86; hardwareProfile=; storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    
    PS C:\WINDOWS\system32>
    ```

詳細については、[Add-AzureRMTag](/powershell/module/azurerm.tags/remove-azurermtag?view=azurermps-6.13.0&preserve-view=true) を参照してください。

## <a name="view-tags-of-a-vm"></a>VM のタグを表示する

デバイスで実行されている特定の仮想マシンに適用されたタグを表示できます。 

1. 表示するタグを持つ VM に関連付けられているパラメーターを定義します。

   ```powershell
   $VMName = <VM Name>
   $VMRG = <VM Resource Group>
   ```
    出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```
1. VM オブジェクトを取得し、そのタグを表示します。

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $VirtualMachine.Tags
   ```
    出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $VirtualMachine

    ResourceGroupName : myaserg2
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
    lMachines/myasetestvm1
    VmId              : 958c0baa-e143-4d8a-82bd-9c6b1ba45e86
    Name              : myasetestvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Sales"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
    
    PS C:\WINDOWS\system32>
    ```
## <a name="view-tags-for-all-resources"></a>すべてのリソースのタグを表示する

デバイスのローカル Azure Resource Manager サブスクリプション (Azure サブスクリプションとは異なります) のすべてのリソースの現在のタグの一覧を表示するには、`Get-AzureRMTag` コマンドを使用します。


複数の VM がデバイスで実行されており、すべての VM のすべてのタグを表示する場合の出力例を次に示します。

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag

Name         Count
----         -----
Organization 3

PS C:\WINDOWS\system32>
```

上記の出力は、デバイス上で実行されている VM に 3 つ `Organization` のタグがあることを示しています。

詳細を表示するには、`-Detailed` パラメーターを使用します。

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag -Detailed |fl

Name        : Organization
ValuesTable :
              Name         Count
              ===========  =====
              Engineering  2
              Sales        1

Count       : 3
Values      : {Engineering, Sales}

PS C:\WINDOWS\system32>
```

上記の出力は、3 つのタグのうち、2 つの VM は `Engineering` としてタグ付けされ、1 つは `Sales` に属するというタグが付けられています。

## <a name="remove-a-tag-from-a-vm"></a>タグを VM から削除する

1. パラメーターを設定する。 

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
   ``` 
    出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> $VMName = "myaselinuxvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg1"
    PS C:\WINDOWS\system32> $TagName = "Organization" 
    ```
2. VM オブジェクトを作成します。

    ```powershell
    $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
    $VirtualMachine   
    ```   

    出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    ResourceGroupName : myaserg1
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselinuxvm1
    VmId              : 290b3fdd-0c99-4905-9ea1-cf93cd6f25ee
    Name              : myaselinuxvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Engineering"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
                                                                              PS C:\WINDOWS\system32> 
    ```
3. タグを削除し、VM を更新します。 省略可能な `-Force` フラグを使用して、ユーザーの確認なしでコマンドを実行します。

    ```powershell
    $tags = $VirtualMachine.Tags
    $tags.Remove($TagName)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```
    出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> $tags = $Virtualmachine.Tags
    PS C:\WINDOWS\system32> $tags
    Key          Value
    ---          -----
    Organization Engineering
    PS C:\WINDOWS\system32> $tags.Remove($TagName)
    True
    PS C:\WINDOWS\system32> Set-AzureRMResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    Name              : myaselinuxvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGrou
                        ps/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselin
                        uxvm1
    ResourceName      : myaselinuxvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg1
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {}
    Properties        : @{vmId=290b3fdd-0c99-4905-9ea1-cf93cd6f25ee; hardwareProfile=;
                        storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    PS C:\WINDOWS\system32>
    ```


## <a name="next-steps"></a>次のステップ

[AzureRM PowerShell を使用してタグを管理する](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true)方法について確認します。
