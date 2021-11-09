---
title: チュートリアル - Azure PowerShell を使用して Azure ディスクを管理する
description: このチュートリアルでは、Azure PowerShell を使用して、仮想マシン用の Azure ディスクの作成と管理を行う方法について説明します
author: roygara
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.date: 10/08/2021
ms.custom: template-tutorial, devx-track-azurepowershell
ms.openlocfilehash: a1ea898dd246977bbb7284a18349a265efd7655d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063120"
---
# <a name="tutorial-manage-disks-with-azure-powershell"></a>チュートリアル: Azure PowerShell を使用してディスクを管理する

Azure Virtual Machines (VM) では、オペレーティング システム (OS)、アプリケーション、およびデータを格納するためにディスクを使用します。 VM を作成するときは、予測されるワークロードに適したディスクのサイズと構成を選択することが重要です。

このチュートリアルでは、VM ディスクのデプロイと管理について説明します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * データ ディスクの作成、アタッチ、および初期化
> * ディスクの状態の確認
> * ディスクの初期化
> * ディスクの拡張とアップグレード
> * ディスクのデタッチと削除

## <a name="prerequisites"></a>前提条件

アクティブなサブスクリプションを含む Azure アカウントが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-vm"></a>VM の作成

このチュートリアルの演習では、VM が必要です。 このセクションの手順に従って作成してください。

開始する前に、サンプルコードの最初の行にある `$azRegion` 変数を見つけて、目的のリージョンを反映するように値を更新します。 たとえば、**米国中部** リージョンを指定するには、`$azRegion = "Central US"` を使用します。 次に、コードを使用して、新しいリソース グループ内に VM をデプロイします。 VM のローカル管理者アカウントのユーザー名とパスワードの値を入力するように求められます。

```azurepowershell-interactive
$azRegion = "[Your Region]"
$azResourceGroup = "myDemoResourceGroup"
$azVMName = "myDemoVM"
$azDataDiskName = "myDemoDataDisk"

New-AzVm `
    -Location $azRegion `
    -ResourceGroupName $azResourceGroup `
    -Name $azVMName `
    -Size "Standard_D2s_v3" `
    -VirtualNetworkName "myDemoVnet" `
    -SubnetName "myDemoSubnet" `
    -SecurityGroupName "myDemoNetworkSecurityGroup" `
    -PublicIpAddressName "myDemoPublicIpAddress"
```

VM が正常に作成されたことを出力で確認することができます。

```Output
ResourceGroupName        : myDemoResourceGroup
Id                       : /subscriptions/{GUID}/resourceGroups/myDemoResourceGroup/providers/Microsoft.Compute/virtualMachines/myDemoTestVM
VmId                     : [{GUID}]
Name                     : myDemoVM
Type                     : Microsoft.Compute/virtualMachines
Location                 : centralus
Tags                     : {}
HardwareProfile          : {VmSize}
NetworkProfile           : {NetworkInterfaces}
OSProfile                : {ComputerName, AdminUsername, WindowsConfiguration, AllowExtensionOperations, RequireGuestProvisionSignal}
ProvisioningState        : Succeeded
StorageProfile           : {ImageReference, OsDisk, DataDisks}
FullyQualifiedDomainName : mydemovm-abc123.Central US.cloudapp.azure.com
```

VM がプロビジョニングされ、2 つのディスクが自動的に作成されてアタッチされます。

- **オペレーティング システム ディスク**。仮想マシンのオペレーティング システムをホストします。
- **一時ディスク**。主に、一時的なデータ処理などの操作に使用されます。

## <a name="add-a-data-disk"></a>データ ディスクの追加

可能な場合は、OS 関連データからアプリケーションとユーザーのデータを分離することをお勧めします。 ユーザーまたはアプリケーションのデータを VM に保存する必要がある場合は、通常、追加のデータ ディスクを作成してアタッチします。

このセクションの手順に従って、VM 上にデータ ディスクを作成し、アタッチして初期化します。

### <a name="create-the-data-disk"></a>データ ディスクの作成

このセクションでは、データ ディスクの作成について説明します。

1. データ ディスクを作成する前に、まずディスク オブジェクトを作成する必要があります。 次のコード サンプルでは、[New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) コマンドレットを使用して、ディスク オブジェクトを構成します。

    ```azurepowershell-interactive
    $diskConfig = New-AzDiskConfig `
        -Location $azRegion `
        -CreateOption Empty `
        -DiskSizeGB 128 `
        -SkuName "Standard_LRS"
    ```

1. ディスク オブジェクトが作成されたら、[New-AzDisk](/powershell/module/az.compute/new-azdisk) コマンドレットを使用してデータ ディスクをプロビジョニングします。

    ```azurepowershell-interactive
    $dataDisk = New-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $azDataDiskName `
        -Disk $diskConfig
    ```

    ディスクが作成されたことを確認するには、[Get-AzDisk](/powershell/module/az.compute/get-azdisk) コマンドレットを使用します。

    ```azurepowershell-interactive
    Get-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $azDataDiskName
    ```

    この例では、ディスクが作成されたことを出力で確認することができます。 `DiskState` および `ManagedBy` のプロパティ値で、ディスクがまだアタッチされていないことを確認します。

    ```Output
    ResourceGroupName            : myDemoResourceGroup
    ManagedBy                    :
    ManagedByExtended            : {}
    OsType                       :
    DiskSizeGB                   : 128
    DiskSizeBytes                : 137438953472
    ProvisioningState            : Succeeded
    DiskIOPSReadWrite            : 500
    DiskMBpsReadWrite            : 60
    DiskState                    : Unattached
    Name                         : myDemoDataDisk
    ```

### <a name="attach-the-data-disk"></a>データ ディスクのアタッチ

VM がデータ ディスクにアクセスするには、まずデータ ディスクをその VM にアタッチする必要があります。 このセクションの手順を実行して、VM の参照を作成し、ディスクを接続して、VM の構成を更新します。

1. データ ディスクをアタッチする VM を取得します。 次のサンプル コードでは、[Get-AzVM](/powershell/module/az.compute/get-azvm) コマンドレットを使用して、VM への参照を作成します。

    ```azurepowershell-interactive
    $vm = Get-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

1. 次に、[Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk) コマンドレットを使用して、データ ディスクを VM の構成にアタッチします。

    ```azurepowershell-interactive
    $vm = Add-AzVMDataDisk `
        -VM $vm `
        -Name $azDataDiskName `
        -CreateOption Attach `
        -ManagedDiskId $dataDisk.Id `
        -Lun 1
    ```

1. 最後に、[Update-AzVM](/powershell/module/az.compute/add-azvmdatadisk) コマンドレットを使用して、VM の構成を更新します。

    ```azurepowershell-interactive
    Update-AzVM `
        -ResourceGroupName $azResourceGroup `
        -VM $vm
    ```

    しばらくすると、正常にアタッチされたことが出力で確認できます。

    ```Output
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK
    ```

### <a name="initialize-the-data-disk"></a>データ ディスクの初期化

データ ディスクが VM にアタッチされたら、そのディスクを使用するように OS を構成する必要があります。 次のセクションでは、リモート VM に接続して、追加された最初のディスクを構成する方法に関するガイダンスを提供します。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. データ ディスクをアタッチした VM を見つけます。 リモート デスクトップ プロトコル (RDP) 接続を作成し、ローカル管理者としてサインインします。

1. リモート VM への RDP 接続を確立したら、Windows の **[スタート]** メニューを選択します。 検索ボックスに「**PowerShell**」と入力し、 **[Windows PowerShell]** を選択して PowerShell ウィンドウを開きます。

    [![リモート デスクトップ接続ウィンドウの画像。](media\tutorial-manage-data-disk\initialize-disk-sml.png)](media\tutorial-manage-data-disk\initialize-disk-lrg.png#lightbox)

1. 開いた PowerShell ウィンドウで、次のスクリプトを実行します。

    ```powershell
    Get-Disk | Where PartitionStyle -eq 'raw' |
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -AssignDriveLetter -UseMaximumSize |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDemoDataDisk" -Confirm:$false
    ```

    正常に初期化されたことを出力で確認することができます。

    ```Output
    DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining   Size
    ----------- --------------- ---------- --------- ------------ ----------------- -------------   ----
    F           myDemoDataDisk  NTFS       Fixed     Healthy      OK                    127.89 GB 128 GB
    ```

## <a name="expand-a-disk"></a>ディスクを拡張する

VM で使用可能なディスク領域が不足している場合は、Azure ディスクを拡張して追加のストレージ容量を提供できます。

一部のシナリオでは、データを OS ディスクに格納する必要があります。 たとえば、OS ドライブにコンポーネントをインストールするレガシ アプリケーションをサポートする必要がある場合があります。 また、より大きな OS ドライブを備えたオンプレミスの物理 PC または VM を移行する必要がある場合もあります。 このような場合、VM の OS ディスクを拡張することが必要になることがあります。

既存のディスクの縮小はサポートされておらず、データ損失に至る可能性があります。

### <a name="update-the-disks-size"></a>ディスクのサイズの更新

OS ディスクまたはデータ ディスクのサイズを変更するには、次の手順に従います。

1. `Get-AzVM` コマンドレットを使用してサイズを変更するディスクを含む VM を選択します。

    ```azurepowershell-interactive
     $vm = Get-AzVM `
       -ResourceGroupName $azResourceGroup `
       -Name $azVMName
    ```

1. VM のディスクのサイズを変更するには、先にその VM を停止する必要があります。 `Stop-AzVM` コマンドレットを使用して VM を停止します。 確認を求められます。

    > [!IMPORTANT]
    > VM のシャットダウンを開始する前に、失われる可能性がある重要なリソースやデータがないことを必ず確認してください。

    ```azurepowershell-interactive
    Stop-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    しばらくすると、マシンが正常に停止されたことを出力で確認することができます。

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:10:23 PM
    EndTime     : 9/13/2021 7:11:12 PM
    Error       :
    ```

1. VM が停止したら、`Get-AzDisk` コマンドレットを使用して、その VM にアタッチされている OS またはデータ ディスクへの参照を取得します。

    次の例では、VM の OS ディスクを選択します。

    ```azurepowershell-interactive
    $disk= Get-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $vm.StorageProfile.OsDisk.Name
    ```

    次の例では、VM の最初のデータ ディスクを選択します。

    ```azurepowershell-interactive
        $disk= Get-AzDisk `
            -ResourceGroupName $azResourceGroup `
            -DiskName $vm.StorageProfile.DataDisks[0].Name
    ```

1. ディスクへの参照が得られたので、ディスクのサイズを 250 GiB に設定します。

    > [!IMPORTANT]
    > 新しいサイズには、既存のディスク サイズより大きい値を指定する必要があります。 OS ディスクで許可される最大値は、4,095 GiB です。

    ```azurepowershell-interactive
    $disk.DiskSizeGB = 250
    ```

1. 次に、`Update-AzDisk` コマンドレットを使用してディスク イメージを更新します。

    ```azurepowershell-interactive
    Update-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -Disk $disk -DiskName $disk.Name
    ```

    ディスク イメージが更新され、ディスクの新しいサイズを出力で確認することができます。

    ```Output
    ResourceGroupName            : myDemoResourceGroup
    ManagedBy                    : /subscriptions/{GUID}/resourceGroups/myDemoResourceGroup/providers/Microsoft.Compute/virtualMachines/myDemoVM
    Sku                          : Microsoft.Azure.Management.Compute.Models.DiskSku
    TimeCreated                  : 9/135/2021 6:41:10 PM
    CreationData                 : Microsoft.Azure.Management.Compute.Models.CreationData
    DiskSizeGB                   : 250
    DiskSizeBytes                : 268435456000
    UniqueId                     : {GUID}
    ProvisioningState            : Succeeded
    DiskIOPSReadWrite            : 500
    DiskMBpsReadWrite            : 60
    DiskState                    : Reserved
    Encryption                   : Microsoft.Azure.Management.Compute.Models.Encryption
    Id                           : /subscriptions/{GUID}/resourceGroups/myDemoResourceGroup/providers/Microsoft.Compute/disks/myDemoDataDisk
    Name                         : myDemoDataDisk
    Type                         : Microsoft.Compute/disks
    Location                     : centralus

1. Finally, restart the VM with the `Start-AzVM` cmdlet.

    ```azurepowershell-interactive
    Start-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    しばらくすると、マシンが正常に起動されたことを出力で確認することができます。

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:44:54 PM
    EndTime     : 9/13/2021 7:45:15 PM
    Error       :
    ```

### <a name="expand-the-disk-volume-in-the-os"></a>OS 内のディスク ボリュームを拡張する

新しいディスク サイズを利用するには、その前に OS 内のボリュームを拡張する必要があります。 次の手順に従って、ディスク ボリュームを拡張し、新しいディスク サイズを利用します。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. データ ディスクをアタッチした VM を見つけます。 リモート デスクトップ プロトコル (RDP) 接続を作成してサインインします。 管理者アカウントにアクセスできなくなった場合は、[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) コマンドレットを使用して、指定されたユーザー名とパスワードの資格情報オブジェクトを作成します。

1. リモート VM への RDP 接続を確立したら、Windows の **[スタート]** メニューを選択します。 検索ボックスに「**PowerShell**」と入力し、 **[Windows PowerShell]** を選択して PowerShell ウィンドウを開きます。

    [![リモート デスクトップ接続ウィンドウの画像。](media\tutorial-manage-data-disk\initialize-disk-sml.png)](media\tutorial-manage-data-disk\initialize-disk-lrg.png#lightbox)

1. PowerShell を開き、次のスクリプトを実行します。 必要に応じて、`-DriveLetter` 変数の値を変更します。 たとえば、**F:** ドライブのパーティションのサイズを変更するには、`$driveLetter = "F"` を使用します。

    ```powershell
    $driveLetter = "[Drive Letter]" `
    $size = (Get-PartitionSupportedSize -DriveLetter $driveLetter) `
    Resize-Partition `
        -DriveLetter $driveLetter `
        -Size $size.SizeMax
    ```

1. RDP ウィンドウを最小化し、Azure Cloud Shell に切り替えます。 `Get-AzDisk` コマンドレットを使用して、ディスクのサイズ変更が正常に行われたことを確認します。

    ```azurepowershell-interactive
    Get-AzDisk `
        -ResourceGroupName $azResourceGroup | Out-Host -Paging
    ```

## <a name="upgrade-a-disk"></a>ディスクをアップグレードする

組織のワークロードの変化に対応するには、いくつかの方法があります。 たとえば、需要の増加に対応するために、Standard HDD を Premium SSD にアップグレードすることを選択できます。

このセクションの手順に従って、マネージド ディスクを Standard から Premium にアップグレードします。

1. `Get-AzVM` コマンドレットを使用してアップグレードするディスクを含む VM を選択します。

    ```azurepowershell-interactive
     $vm = Get-AzVM `
       -ResourceGroupName $azResourceGroup `
       -Name $azVMName
    ```

1. VM のディスクをアップグレードするには、先にその VM を停止する必要があります。 `Stop-AzVM` コマンドレットを使用して VM を停止します。 確認を求められます。

    > [!IMPORTANT]
    > VM のシャットダウンを開始する前に、失われる可能性がある重要なリソースやデータがないことを必ず確認してください。

    ```azurepowershell-interactive
    Stop-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    しばらくすると、マシンが正常に停止されたことを出力で確認することができます。

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:10:23 PM
    EndTime     : 9/13/2021 7:11:12 PM
    Error       :
    ```

1. VM が停止したら、`Get-AzDisk` コマンドレットを使用して、その VM にアタッチされている OS またはデータ ディスクへの参照を取得します。

    次の例では、VM の OS ディスクを選択します。

    ```azurepowershell-interactive
    $disk= Get-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $vm.StorageProfile.OsDisk.Name
    ```

    次の例では、VM の最初のデータ ディスクを選択します。

    ```azurepowershell-interactive
        $disk= Get-AzDisk `
            -ResourceGroupName $azResourceGroup `
            -DiskName $vm.StorageProfile.DataDisks[0].Name
    ```

1. ディスクへの参照が得られたので、ディスクの SKU を **Premium_LRS** に設定します。

    ```azurepowershell-interactive
    $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new('Premium_LRS')
    ```

1. 次に、`Update-AzDisk` コマンドレットを使用してディスク イメージを更新します。

    ```azurepowershell-interactive
    Update-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -Disk $disk -DiskName $disk.Name
    ```

    ディスク イメージが更新されます。 次のコード例を使用して、ディスクの SKU がアップグレードされたことを検証します。

    ```azurepowershell-interactive
    $disk.Sku.Name
    ```

    ディスクの新しい SKU を出力で確認することができます。

    ```Output
    Premium_LRS
    ```

1. 最後に、`Start-AzVM` コマンドレットを使用して VM を再起動します。

    ```azurepowershell-interactive
    Start-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    しばらくすると、マシンが正常に起動されたことを出力で確認することができます。

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:44:54 PM
    EndTime     : 9/13/2021 7:45:15 PM
    Error       :
    ```

## <a name="detach-a-data-disk"></a>データ ディスクの切断

データ ディスクは、別の VM にアタッチする場合や、不要になった場合に VM からデタッチできます。 既定では、意図しないデータ損失を防ぐために、デタッチされたディスクは削除されません。 デタッチされたディスクは、削除されるまで引き続きストレージ料金が発生します。

1. まず、`Get-AzVM` コマンドレットを使用してディスクがアタッチされている VM を選択します。

    ```azurepowershell-interactive
    $vm = Get-AzVM `
       -ResourceGroupName $azResourceGroup `
       -Name $azVMName
    ```

1. 次に、`Remove-AzVMDataDisk` コマンドレットを使用して VM からディスクをデタッチします。

    ```azurepowershell-interactive
    Remove-AzVMDataDisk `
        -VM $vm `
        -Name $azDataDiskName
    ```

1. `Update-AzVM` コマンドレットを使用して VM の状態を更新し、データ ディスクを除去します。

    ```azurepowershell-interactive
    Update-AzVM `
        -ResourceGroupName $azResourceGroup `
        -VM $vm
    ```

    しばらくすると、VM が正常に更新されたことを出力で確認することができます。

    ```output
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK
    ```

## <a name="delete-a-data-disk"></a>データ ディスクの削除

VM を削除しても、VM にアタッチされているデータ ディスクはプロビジョニングされたままなので、削除するまで料金が引き続き発生します。 この既定の動作は、意図しない削除によってデータが失われるのを防ぐのに役立ちます。

次のサンプル PowerShell スクリプトを使用して、アタッチされていないディスクを削除できます。 `-ResourceGroupName` スイッチが `Get-AzDisk` コマンドレットで使用されているため、ディスクの取得は **myDemoResourceGroup** に限定されています。

```azurepowershell
# Get all disks in resource group $azResourceGroup
$allDisks = Get-AzDisk -ResourceGroupName $azResourceGroup

# Determine the number of disks in the collection
if($allDisks.Count -ne 0) {

    Write-Host "Found $($allDisks.Count) disks."

    # Iterate through the collection
    foreach ($disk in $allDisks) {

        # Use the disk's "ManagedBy" property to determine if it is unattached
        if($disk.ManagedBy -eq $null) {

            # Confirm that the disk can be deleted
            Write-Host "Deleting unattached disk $($disk.Name)."
            $confirm = Read-Host "Continue? (Y/N)"
            if ($confirm.ToUpper() -ne 'Y') { break }
            else {

                # Delete the disk
                $disk | Remove-AzDisk -Force 
                Write-Host "Unattached disk $($disk.Name) deleted."
            }
        }
    }
}
```

出力に示されているように、アタッチされていないデータ ディスクが削除されています。

```output
Name      : abcd1234-ab12-cd34-ef56-abcdef123456
StartTime : 9/13/2021 10:14:05 AM
EndTime   : 9/13/2021 10:14:35 AM
Status    : Succeeded
Error     :
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループ、VM、および関連するすべてのリソースは、不要になったら削除します。 次のサンプル PowerShell スクリプトを使用して、このチュートリアルで前に作成したリソース グループを削除できます。  

> [!CAUTION]
> リソース グループを削除する際には注意してください。 重要なデータの損失を回避するため、リソース グループを削除する前に、その中に重要なリソースまたはデータが含まれていないことを必ず確認してください。

```azurepowershell-interactive
    Remove-AzResourceGroup -Name $azResourceGroup
```

確認を求められます。 しばらくすると、`True` の応答によって **myDemoResourceGroup** が正常に削除されたことが確認できます。
    
```Output
Confirm
Are you sure you want to remove resource group 'myDemoResourceGroup'
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
True
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * データ ディスクの作成、アタッチ、および初期化
> * ディスクの状態の確認
> * ディスクの初期化
> * ディスクの拡張とアップグレード
> * ディスクのデタッチと削除

次のチュートリアルに進み、VM 構成を自動化する方法について学習してください。

> [!div class="nextstepaction"]
> [VM 構成の自動化](./tutorial-automate-vm-deployment.md)
