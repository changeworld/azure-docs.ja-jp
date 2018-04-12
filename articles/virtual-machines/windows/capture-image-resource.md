---
title: Azure で管理対象イメージを作成する | Microsoft Docs
description: Azure で一般化された VM または VHD の管理対象イメージを作成します。 イメージを使って、管理対象ディスクを使う複数の VM を作成することができます。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/06/2018
ms.author: cynthn
ms.openlocfilehash: 0b0bd48b95ad9393b4cd82081436e561326df6da
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Azure で一般化された VM の管理対象イメージを作成する

ストレージ アカウントに管理対象ディスクまたは非管理対象ディスクとして格納されている一般化された VM から管理対象イメージ リソースを作成できます。 イメージは複数の VM の作成に使用できます。 

## <a name="generalize-the-windows-vm-using-sysprep"></a>Sysprep を使用して Windows VM を一般化する

特に重要な点は、Sysprep がすべての個人アカウント情報を削除して、マシンをイメージとして使用できるように準備することです。 Sysprep の詳細については、「 [Sysprep の使用方法: 紹介](http://technet.microsoft.com/library/bb457073.aspx)」を参照してください。

コンピューター上で実行されるサーバー ロールが Sysprep でサポートされていることを確認します。 詳しくは、「 [Sysprep Support for Server Roles](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> 仮想マシンで sysprep を実行すると*一般化*されたと見なされ、再起動できなくなります。 VM の一般化プロセスは元に戻せません。 元の VM の機能を保持する場合は、[VM のコピー](create-vm-specialized.md#option-3-copy-an-existing-azure-vm)を作成し、コピーを一般化してください。 
>
> 初めて VHD を Azure にアップロードする前に Sysprep を実行する場合、Sysprep の実行前に [VM の準備](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ができていることを確認してください。  
> 
> 

1. Windows 仮想マシンへのサインイン
2. 管理者としてコマンド プロンプト ウィンドウを開きます。 ディレクトリを **%windir%\system32\sysprep** に変更し、`sysprep.exe` を実行します。
3. **[システム準備ツール]** ダイアログ ボックスで **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、**[一般化する]** チェック ボックスがオンになっていることを確認します。
4. **[シャットダウン オプション]** の **[シャットダウン]** を選択します。
5. Click **OK**.
   
    ![Sysprep の開始](./media/upload-generalized-managed/sysprepgeneral.png)
6. Sysprep は完了時に仮想マシンをシャットダウンします。 VM は再起動しないでください。


## <a name="create-a-managed-image-in-the-portal"></a>ポータルで管理対象イメージを作成する 

1. [ポータル](https://portal.azure.com)を開きます。
2. 左のメニューで、[仮想マシン] をクリックし、一覧から VM を選択します。
3. VM 用のページで、上部のメニューの **[キャプチャ]** をクリックします。
3. **[名前]** に、イメージに使用する名前を入力します。
4. **[リソース グループ]** で、**[新規作成]** を選択して名前を入力するか、または **[Use existing\(既存の値を使用する\)]** を選択してドロップダウン リストから使うリソース グループを選びます。
5. イメージが作成された後にソース VM を削除する場合、**[イメージの作成後、この仮想マシンを自動的に削除します]** を選択します。
6. 完了したら、**[作成]** をクリックします。
16. 作成したイメージは、リソース グループのリソースの一覧に **[イメージ]** リソースとして表示されます。



## <a name="create-an-image-of-a-vm-using-powershell"></a>Powershell を使って VM のイメージを作成する

VM からイメージを直接作成すると、OS ディスクやすべてのデータ ディスクなど、VM に関連付けられているすべてのディスクが、イメージに確実に含まれます。 この例では、管理対象ディスクを使用する VM から管理対象イメージを作成する方法を示します。


始める前に、AzureRM.Compute PowerShell モジュールの最新バージョンがあることを確認してください。 インストールするには次のコマンドを実行します。 (`Get-Module` を使って、使用中のバージョンを確認します。)

```azurepowershell-interactive
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
詳細については、[Azure PowerShell のバージョン管理に関するページ](/powershell/azure/overview)をご覧ください。


1. 変数をいくつか作成します。

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. VM の割り当てが解除されていることを確認します。

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. 仮想マシンの状態を **[一般化]**に設定します。 
   
    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. 仮想マシンを取得します。 

    ```azurepowershell-interactive
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. イメージの構成を作成します。

    ```azurepowershell-interactive
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. イメージを作成します。

    ```azurepowershell-interactive
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 
## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Powershell を使って管理対象ディスクからイメージを作成する

OS ディスクのイメージを作成したいだけの場合は、管理ディスクの ID を OS ディスクとして指定することで、イメージを作成することもできます。

    
1. 変数をいくつか作成します。 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. VM を取得します。

   ```azurepowershell-interactive
   $vm = Get-AzureRmVm -Name $vmName -ResourceGroupName $rgName
   ```

3. 管理対象ディスクの ID を取得します。

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. イメージの構成を作成します。

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. イメージを作成します。

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Powershell を使ってスナップショットからイメージを作成する

一般化された VM のスナップショットから管理対象イメージを作成できます。

    
1. 変数をいくつか作成します。 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. スナップショットを取得します。

   ```azurepowershell-interactive
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. イメージの構成を作成します。

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. イメージを作成します。

    ```azurepowershell-interactive
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-image-from-a-vhd-in-a-storage-account"></a>ストレージ アカウント内の VHD からイメージを作成する

ストレージ アカウントで、一般化された OS の VHD から管理対象イメージを作成します。 ストレージ アカウントでは、https://*mystorageaccount*.blob.core.windows.net/*container*/*vhd_filename.vhd* という形式の VHD の URI が必要です。 この例では、使用している VHD は *vhdcontainer* という名前のコンテナーの *mystorageaccount* 内にあり、VHD ファイル名は *osdisk.vhd* です。


1.  最初に、共通のパラメーターを設定します。

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. VM を停止し、割り当てを解除します。

    ```azurepowershell-interactive
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. VM を一般化としてマークします。

    ```azurepowershell-interactive
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized 
    ```
4.  一般化した OS VHD を使ってイメージを作成します。

    ```azurepowershell-interactive
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>次の手順
- これで、[一般化された管理対象イメージから VM を作成する](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)ことができるようになりました。  

