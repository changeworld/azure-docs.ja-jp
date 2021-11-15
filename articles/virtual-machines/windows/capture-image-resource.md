---
title: Azure で管理対象イメージを作成する
description: Azure で一般化された VM または VHD の管理対象イメージを作成します。 イメージを使って、マネージド ディスクを使う複数の VM を作成することができます。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/27/2018
ms.author: cynthn
ms.custom: legacy
ms.collection: windows
ms.openlocfilehash: 95f57b01f2c9e6bffd0cfc1f1d563605e320d6ba
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437013"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Azure で一般化された VM の管理対象イメージを作成する

**適用対象:** :heavy_check_mark: Windows VM 


ストレージ アカウントにマネージド ディスクまたはアンマネージド ディスクとして格納されている一般化された仮想マシン (VM) からマネージド イメージ リソースを作成できます。 イメージは複数の VM の作成に使用できます。 マネージド イメージの課金方法については、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/)」をご覧ください。 

1 つのマネージド イメージは、最大 20 個の同時デプロイをサポートします。 同じマネージド イメージから 20 個を超える VM を同時に作成しようとすると、1 つの VHD におけるストレージ パフォーマンスの制限によって、プロビジョニングのタイムアウトが発生する可能性があります。 20 個を超える VM を同時に作成するには、20 のコンカレント VM デプロイごとに 1 つのレプリカで構成された [Azure Compute Gallery](../shared-image-galleries.md) (旧称 Shared Image Gallery) イメージを使用します。

## <a name="prerequisites"></a>前提条件

イメージを作成するには、[一般化された](../generalize.md) VM が必要です。

## <a name="create-a-managed-image-in-the-portal"></a>ポータルで管理対象イメージを作成する 

1. [[Azure Portal]](https://portal.azure.com) に移動して、VM イメージを管理します。 **[仮想マシン]** を検索して選択します。

2. 一覧で VM を選択します。

3. VM の **[仮想マシン]** ページの上部のメニューで、 **[キャプチャ]** を選択します。

   **[イメージの作成]** ページが表示されます。

4. **[名前]** で、あらかじめ設定されている名前を受け入れるか、イメージに使用する名前を入力します。

5. **[リソース グループ]** で、 **[新規作成]** を選択して名前を入力するか、使用するリソースグループをドロップダウンリストから選択します。

6. イメージが作成された後にソース VM を削除する場合、 **[イメージの作成後、この仮想マシンを自動的に削除します]** を選択します。

7. いずれかの [可用性ゾーン](../../availability-zones/az-overview.md)でイメージを使用する機能が必要な場合、 **[Zone resiliency (ゾーンの回復性)]** に **[オン]** を選択します。

8. **[作成]** を選択してイメージを作成します。

作成したイメージは、リソース グループのリソースの一覧に **[イメージ]** リソースとして表示されます。



## <a name="create-an-image-of-a-vm-using-powershell"></a>PowerShell を使用して VM のイメージを作成する

 

VM からイメージを直接作成すると、OS ディスクやすべてのデータ ディスクなど、VM に関連付けられているすべてのディスクが、イメージに確実に含まれます。 この例では、マネージド ディスクを使用する VM から管理対象イメージを作成する方法を示します。

始める前に、Azure PowerShell モジュールの最新バージョンがあることを確認してください。 バージョンを確認するには、PowerShell で `Get-Module -ListAvailable Az` を実行してください。 アップグレードが必要な場合は、「[PowerShellGet を使用した Windows への Azure PowerShell のインストール](/powershell/azure/install-az-ps)」を参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成します。


> [!NOTE]
> イメージをゾーン冗長ストレージに格納する場合は、[可用性ゾーン](../../availability-zones/az-overview.md)をサポートするリージョンにストレージを作成し、イメージの構成に `-ZoneResilient` パラメーターを含める必要があります (`New-AzImageConfig` コマンド)。

VM イメージを作成するには、次の手順を実行します。

1. 変数をいくつか作成します。

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. VM の割り当てが解除されていることを確認します。

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. 仮想マシンの状態を **[一般化]** に設定します。 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. 仮想マシンを取得します。 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. イメージの設定を作成します。

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. イメージを作成します。

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Powershell を使ってマネージド ディスクからイメージを作成する

OS ディスクのみのイメージを作成する場合は、OS ディスクとしてマネージド ディスクの ID を指定します。

    
1. 変数をいくつか作成します。 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. VM を取得します。

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. マネージド ディスクの ID を取得します。

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. イメージの設定を作成します。

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. イメージを作成します。

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>PowerShell を使用してスナップショットからイメージを作成する

次の手順に従って、一般化された VM のスナップショットから管理対象イメージを作成できます。

    
1. 変数をいくつか作成します。 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. スナップショットを取得します。

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. イメージの設定を作成します。

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. イメージを作成します。

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>ストレージ アカウントを使用する VM からイメージを作成する

マネージド ディスクを使用しない VM からマネージド イメージを作成するには、ストレージ アカウントで、 https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer*/*vhdfilename.vhd* という形式の OS VHD の URI が必要です。 この例では、VHD は *vhdcontainer* という名前のコンテナーの *mystorageaccount* 内にあり、VHD ファイル名は *vhdfilename.vhd* です。


1.  変数をいくつか作成します。

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. VM を停止するか、VM の割り当てを解除します。

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. VM を一般化としてマークします。

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  一般化した OS VHD を使ってイメージを作成します。

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>次のステップ
- [管理イメージから VM を作成する](create-vm-generalized-managed.md)。 
