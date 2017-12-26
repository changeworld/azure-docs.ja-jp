---
title: "Azure Stack の VM ディスクを管理する | Microsoft Docs"
description: "Azure Stack での仮想マシンのディスクをプロビジョニングします。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/14/2017
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: 0c36e2eaaf2d266842b2b7de0b0c8dc0ed1e0145
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2017
---
# <a name="virtual-machine-disk-storage-for-azure-stack"></a>Azure Stack での仮想マシン ディスク ストレージ

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack では、仮想マシンのオペレーティング システム (OS) ディスクとデータ ディスクの両方を[管理されていないディスク](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks)として使用することをサポートします。 管理されていないディスクを使用するには、[ストレージ アカウント](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)を作成し、ストレージ アカウント内のコンテナーにページ BLOB としてディスクを格納します。 これらのディスクは、VM ディスクと呼ばれます。

パフォーマンスを改善し、Azure Stack システムの管理コストを削減するために、個別のコンテナーに各 VM ディスクを配置することをお勧めします。 1 つのコンテナーには、OS ディスクまたはデータ ディスクのどちらかを保持し、同時に両方を保持しないでください。 ただし、両方を同じコンテナーに配置することを妨げる制限はありません。

VM に 1 つまたは複数のデータ ディスクを追加する場合は、これらのディスクを保持するための場所として追加のコンテナーを使用することを計画してください。 データ ディスクと同じように、別の VM の OS ディスクも、独自のコンテナーに配置してください。

複数の VM を作成するときに、新しい VM に対して同じストレージ アカウントを再利用できます。 作成するコンテナーのみを一意にする必要があります。  

VM にディスクを追加するには、ユーザー ポータルまたは PowerShell を使用します。

| 方法 | オプション
|-|-|
|[ユーザー ポータル](#use-the-portal-to-add-additional-disks-to-a-vm)|- 以前にプロビジョニングされた VM に新しいデータ ディスクを追加します。 新しいディスクは、Azure Stack によって作成されます。 </br> </br>- 以前にプロビジョニングされた VM に既存の .vhd ファイルをディスクとして追加します。 これを行うには、先に .vhd ファイルを準備して、Azure Stack に アップロードしておく必要があります。 |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | - OS ディスクがある新しい VM を作成し、同時にその VM に 1 つまたは複数のデータ ディスクを追加します。 |


## <a name="use-the-portal-to-add-additional-disks-to-a-vm"></a>ポータルを使用して VM に別のディスクを追加する
既定では、ポータルを使用してほとんどのマーケットプレース品目用の VM を作成した場合は、OS ディスクのみが作成されます。 Azure によって作成されたディスクは、管理ディスクと呼ばれます。

VM をプロビジョニングした後、ポータルを使用してその VM に新しいデータ ディスクまたは既存のデータ ディスクを追加できます。 各ディスクは、別々のコンテナーに配置する必要があります。 VM に追加するディスクは、管理されていないディスクと呼ばれます。

### <a name="use-the-portal-to-attach-a-new-data-disk-to-a-vm"></a>ポータルを使用して新しいデータ ディスクを VM に接続する

1.  ポータルで **[仮想マシン]**を選択します。    
    ![例: VM ダッシュボード](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  以前にプロビジョニングされている仮想マシンを選択します。   
    ![例: ダッシュボードで VM を選択する](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  仮想マシンで、**[ディスク]** > **[新しいディスクの接続]** をクリックします。       
    ![例: 新しいディスクを VM に接続する](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  **[新しいディスクの接続]** ウィンドウで、**[場所]** をクリックします。 既定では、場所は、OS ディスクを保持するコンテナーに設定されます。      
    ![例: ディスクの場所を設定する](media/azure-stack-manage-vm-disks/disk-location.png)

5.  使用する**ストレージ アカウント**を選択します。 次に、データ ディスクを配置する**コンテナー**を選択します。 必要に応じて、**[コンテナー]** ページで新しいコンテナーを作成できます。 その後、新しいディスクの場所をそのコンテナーに変更できます。 ディスクごとに別のコンテナーを使用する場合は、データ ディスクの分散配置することでパフォーマンスを向上させることができます。 **[選択]** をクリックして選択を保存します。     
    ![例: コンテナーを選択する](media/azure-stack-manage-vm-disks/select-container.png)

6.  **[新しいディスクの接続]**ページで、ディスクの **[名前]**、**[種類]**、**[サイズ]**、および**[ホスト キャッシュ]** 設定を更新します。 **[OK]** をクリックして、VM の新しいディスク構成を保存します。  
    ![例: ディスクの接続を完了する](media/azure-stack-manage-vm-disks/complete-disk-attach.png)  

7.  Azure Stack でディスクが作成され、仮想マシンに接続されると、仮想マシンのディスク設定の **[データ ディスク]** に新しいディスクが表示されます。   
    ![例: ディスクを表示する](media/azure-stack-manage-vm-disks/view-data-disk.png)


### <a name="attach-an-existing-data-disk-to-a-vm"></a>VM に既存のデータ ディスクを接続する
1.  VM のデータ ディスクとして使用する [.Vhd ファイルを準備します](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd)。 VM で使用するストレージ アカウントで、.vhd ファイルを接続するアカウントに .vhd ファイルをアップロードします。

  .vhd ファイルを保持するために使用するコンテナーは、OS ディスクを保持するコンテナーとは別のコンテナーにすることを計画してください。   
  ![例: VHD ファイルをアップロードする](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  .vhd ファイルをアップロードしたら、VM に VHD をアタッチできます。 左側のメニューの **[仮想マシン]** をクリックします。  
 ![例: ダッシュボードで VM を選択する](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  一覧から仮想マシンを選択します。    
  ![例: ダッシュボードで VM を選択する](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  仮想マシンのページで、**[ディスク]** > **[既存のディスクの接続]**をクリックします。   
  ![例: 既存のディスクを接続する](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  **[既存のディスクの接続]** ページで、**[VHD ファイル]** をクリックします。 **[ストレージ アカウント]** ページが開きます。    
  ![例: VHD ファイルを選択する](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  **[ストレージ アカウント]** で、使用するアカウントを選択し、先ほどアップロードした .vhd ファイルを保持するコンテナーを選択します。 .vhd ファイルを選択し、**[選択]** をクリックして選択内容を保存します。    
  ![例: コンテナーを選択する](media/azure-stack-manage-vm-disks/select-container2.png)

7.  **[既存のディスクの接続]** の **[VHD ファイル]** に、選択したファイルが表示されます。 ディスクの **[ホスト キャッシュ]** 設定を更新し、**[OK]** をクリックして VM の新しいディスク構成を保存します。    
  ![例: VHD ファイルを接続する](media/azure-stack-manage-vm-disks/attach-vhd.png)

8.  Azure Stack でディスクが作成され、仮想マシンに接続されると、仮想マシンのディスク設定の **[データ ディスク]** に新しいディスクが表示されます。   
  ![例: ディスクの接続を完了する](media/azure-stack-manage-vm-disks/complete-disk-attach.png)


## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>PowerShell を使用して複数の管理されていないディスクを VM に追加する
PowerShell を使用して VM をプロビジョニングし、新しいデータ ディスクを追加するか、既存の **.vhd** ファイルをデータ ディスクとして接続できます。

**Add-AzureRmVMDataDisk** コマンドレットは、仮想マシンにデータ ディスクを追加します。 データ ディスクは仮想マシンを作成するときに追加できます。または、既存のバーチャル マシンにデータ ディスクを追加できます。 別々のコンテナーにディスクを分散させるには、**VhdUri** パラメーターを指定します。

### <a name="add-data-disks-to-a-new-virtual-machine"></a>新しい仮想マシンにデータ ディスクを追加する
次の例では、PowerShell コマンドを使用して、3 つのデータ ディスクがある VM を作成します。各ディスクは別々のコンテナーに配置されます。

最初のコマンドは、仮想マシン オブジェクトを作成し、それを *$VirtualMachine* 変数に格納します。 このコマンドは、仮想マシンに名前とサイズを割り当てます。
  ```
  $VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"
  ```

次の 3 つのコマンドは、3 つのデータ ディスクのパスを、*$DataDiskVhdUri01* 変数、*$DataDiskVhdUri02* 変数、および *$DataDiskVhdUri03* 変数に割り当てます。 URL に異なるパス名を定義して、ディスクを別々のコンテナーに分散させます。     
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```

  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```

  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```

最後の 3 つのコマンドは、*$VirtualMachine* に格納されている仮想マシンにデータ ディスクを追加します。 各コマンドは、ディスクの名前、場所、および追加のプロパティを指定します。 各ディスクの URI は、*$DataDiskVhdUri01**$DataDiskVhdUri02*、および *$DataDiskVhdUri03* に格納されます。
  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                  -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                  -VhdUri $DataDiskVhdUri01 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                 -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                 -VhdUri $DataDiskVhdUri02 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                  -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                  -VhdUri $DataDiskVhdUri03 -CreateOption Empty
  ```

次の PowerShell コマンドを使用して、OS ディスクとネットワーク構成を VM に追加した後、新しい VM を起動します。
  ```
  #set variables
  $rgName = "myResourceGroup"
  $location = "local"
  #Set OS Disk
  $osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
  $osDiskName = "osDisk"

  $VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
      -CreateOption FromImage -Windows

  # Create a subnet configuration
  $subnetName = "mySubNet"
  $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

  # Create a vnet configuration
  $vnetName = "myVnetName"
  $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
      -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

  # Create a public IP
  $ipName = "myIP"
  $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
      -AllocationMethod Dynamic

  # Create a network security group cnfiguration
  $nsgName = "myNsg"
  $rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
      -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
      -SourceAddressPrefix Internet -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
      -Name $nsgName -SecurityRules $rdpRule

  # Create a NIC configuration
  $nicName = "myNicName"
  $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
  -Location $location -SubnetId $vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

  #Create the new VM
  $VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine | `
      Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
      -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
  New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
  ```



### <a name="add-data-disks-to-an-existing-virtual-machine"></a>データ ディスクを既存の仮想マシンに追加する
次の例では、PowerShell コマンドを使用して、既存の VM に 3 つのデータ ディスクを追加します。
最初のコマンドは、**Get AzureRmVM** コマンドレットを使用して、VirtualMachine という名前の仮想マシンを取得します。 コマンドは仮想マシンを変数 *$VirtualMachine* に保存します。
  ```
  $VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                  -Name "VirtualMachine"
  ```
次の 3 つのコマンドは、3 つのデータ ディスクのパスを、$DataDiskVhdUri01 変数、$DataDiskVhdUri02 変数、および $DataDiskVhdUri03 変数に割り当てます。  vhduri 内の異なるパス名は、ディスクを配置する別々のコンテナーを示します。
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```
  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```
  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```


  次の 3 つのコマンドは、*$VirtualMachine* に格納されている仮想マシンにデータ ディスクを追加します。 各コマンドは、ディスクの名前、場所、および追加のプロパティを指定します。 各ディスクの URI は、*$DataDiskVhdUri01**$DataDiskVhdUri02*、および *$DataDiskVhdUri03* に格納されます。
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                        -VhdUri $DataDiskVhdUri01 -LUN 0 `
                        -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                        -VhdUri $DataDiskVhdUri02 -LUN 1 `
                        -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                        -VhdUri $DataDiskVhdUri03 -LUN 2 `
                        -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
  ```


  最後のコマンドは、-*ResourceGroupName*内の *$VirtualMachine* に格納されている仮想マシンの状態を更新します。
  ```
  Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
  ```
<!-- Pending scripts  

## Distribute the data disks of an existing VM
If you have a VM with more than one disk in the same container, the service operator of the Azure Stack deployment might ask you to redistribute the disks into individual containers.

To do so, use the scripts from the following location in GitHub. These scripts can be used to move the data disks to different containers.
-->

## <a name="next-steps"></a>次のステップ
Azure Stack 仮想マシンの詳細については、「[Azure Stack の仮想マシンに関する考慮事項](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations)」に進んでください。
