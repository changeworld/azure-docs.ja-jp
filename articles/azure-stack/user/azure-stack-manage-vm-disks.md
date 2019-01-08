---
title: Azure Stack の VM ディスクを管理する | Microsoft Docs
description: Azure Stack で仮想マシンのディスクをプロビジョニングします。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/03/2018
ms.author: mabrigg
ms.reviewer: jiahan
ms.openlocfilehash: 2e3cec4564c509cd225a9bcd43185f6f5b344e8c
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52833459"
---
# <a name="provision-virtual-machine-disk-storage-in-azure-stack"></a>Azure Stack で仮想マシンのディスク ストレージをプロビジョニングする

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

この記事では、Azure Stack ポータルまたは PowerShell を使用して、仮想マシンのディスク記憶域をプロビジョニングする方法について説明します。

## <a name="overview"></a>概要

バージョン 1808 以降、Azure Stack では、マネージド ディスクとアンマネージド ディスクの両方を、仮想マシンで、オペレーティング システム (OS) ディスクおよびデータ ディスクとして使用できます。 バージョン 1808 より前は、アンマネージド ディスクのみがサポートされています。 

**[マネージド ディスク](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#managed-disks)** を使用すると、VM ディスクに関連付けられているストレージ アカウントを管理できるため、Azure IaaS VM のディスク管理が簡素化されます。 Azure Stack では、必要なディスクのサイズを指定するだけで、ディスクの作成と管理が自動的に行われます。

**[アンマネージド ディスク](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks)** については、そのディスクを格納する[ストレージ アカウント](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)を作成する必要があります。 作成したディスクは VM ディスクと呼ばれ、ストレージ アカウントのコンテナーに格納されます。

 

### <a name="best-practice-guidelines"></a>ベスト プラクティス ガイドライン

パフォーマンスを改善し、全体的なコストを削減するために、個別のコンテナーに各 VM ディスクを配置することをお勧めします。 1 つのコンテナーには、OS ディスクまたはデータ ディスクのどちらかを保持し、同時に両方を保持しないでください。 (ただし、両方の種類のディスクを同じコンテナーに配置できないわけではありません。)

VM に 1 つ以上のデータ ディスクを追加する場合は、追加コンテナーを、これらのディスクを格納する場所として使用してください。 追加 VM の OS ディスクも、独自のコンテナーに配置する必要があります。

複数の VM を作成するときは、新しい仮想マシンごとに同じストレージ アカウントを再利用できます。 作成するコンテナーのみを一意にする必要があります。

### <a name="adding-new-disks"></a>新しいディスクを追加する

次の表は、ポータルおよび PowerShell を使用してディスクを追加する方法をまとめたものです。

| 方法 | オプション
|-|-|
|[ユーザー ポータル](#use-the-portal-to-add-additional-disks-to-a-vm)|- 既存の VM に新しいデータ ディスクを追加します。 新しいディスクは、Azure Stack によって作成されます。 </br> </br>- 前にプロビジョニングした VM に既存のディスク (.vhd) ファイルを追加します。 これを行うには、.vhd ファイルを準備して、そのファイルを Azure Stack にアップロードする必要があります。 |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | - OS ディスクがある新しい VM を作成し、同時にその VM に 1 つまたは複数のデータ ディスクを追加します。 |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>ポータルを使用してディスクを VM に追加する

ポータルを使ってマーケットプレース項目のほとんどを対象とする VM を作成する場合、既定では、OS ディスクのみが作成されます。

VM の作成後、ポータルを使って次の操作を行うことができます。
* 新しいデータ ディスクを作成し、VM に接続する。
* 既存のデータ ディスクを作成し、VM にアップロードする。

追加するアンマネージド ディスクはそれぞれ、個別のコンテナーに配置する必要があります。

>[!NOTE]  
>Azure によって作成および管理されているディスクは、[マネージド ディスク](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)と呼ばれます。

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>ポータルを使用して新しいデータ ディスクを作成して接続する

1.  ポータルで **[仮想マシン]** を選択します。    
    ![例: VM ダッシュボード](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  以前にプロビジョニングされている仮想マシンを選択します。   
    ![例: ダッシュボードで VM を選択する](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  仮想マシンで、**[ディスク]** > **[新しいディスクの接続]** を選択します。       
    ![例: 新しいディスクを VM に接続する](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  **[新しいディスクの接続]** ウィンドウで、**[場所]** を選択します。 既定では、場所は、OS ディスクを保持するコンテナーに設定されます。      
    ![例: ディスクの場所を設定する](media/azure-stack-manage-vm-disks/disk-location.png)

5.  使用する**ストレージ アカウント**を選択します。 次に、データ ディスクを配置する**コンテナー**を選択します。 必要に応じて、**[コンテナー]** ページで新しいコンテナーを作成できます。 その後、新しいディスクの場所をそのコンテナーに変更できます。 ディスクごとに別のコンテナーを使用する場合は、データ ディスクの分散配置することでパフォーマンスを向上させることができます。 **[選択]** を選択して、選択内容を保存します。     
    ![例: コンテナーを選択する](media/azure-stack-manage-vm-disks/select-container.png)

6.  **[新しいディスクの接続]** ページで、ディスクの **[名前]**、**[種類]**、**[サイズ]**、および **[ホスト キャッシュ]** 設定を更新します。 **[OK]** を選択して、VM の新しいディスク構成を保存します。  
    ![例: ディスクの接続を完了する](media/azure-stack-manage-vm-disks/complete-disk-attach.png)  

7.  Azure Stack でディスクが作成され、仮想マシンに接続されると、仮想マシンのディスク設定の **[データ ディスク]** に新しいディスクが表示されます。   
    ![例: ディスクを表示する](media/azure-stack-manage-vm-disks/view-data-disk.png)


### <a name="attach-an-existing-data-disk-to-a-vm"></a>VM に既存のデータ ディスクを接続する

1.  VM のデータ ディスクとして使用する [.Vhd ファイルを準備します](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd)。 VM で使用するストレージ アカウントで、.vhd ファイルを接続するアカウントに .vhd ファイルをアップロードします。

  .vhd ファイルを保持するために使用するコンテナーは、OS ディスクを保持するコンテナーとは別のコンテナーにすることを計画してください。   
  ![例: VHD ファイルをアップロードする](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  .vhd ファイルをアップロードしたら、VM に VHD をアタッチできます。 左側のメニューで **[仮想マシン]** を選択します。  
 ![例: ダッシュボードで VM を選択する](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  一覧から仮想マシンを選択します。    
  ![例: ダッシュボードで VM を選択する](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  仮想マシンのページで、**[ディスク]** > **[既存のディスクの接続]** を選択します。   
  ![例: 既存のディスクを接続する](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  **[既存のディスクの接続]** ページで、**[VHD ファイル]** を選択します。 **[ストレージ アカウント]** ページが開きます。    
  ![例: VHD ファイルを選択する](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  **[ストレージ アカウント]** で、使用するアカウントを選択し、前にアップロードした .vhd ファイルを保持するコンテナーを選択します。 .vhd ファイルを選択し、**[選択]** を選択して、選択内容を保存します。    
  ![例: コンテナーを選択する](media/azure-stack-manage-vm-disks/select-container2.png)

7.  **[既存のディスクの接続]** の **[VHD ファイル]** に、選択したファイルが表示されます。 ディスクの **[ホスト キャッシュ]** 設定を更新し、**[OK]** を選択して VM の新しいディスク構成を保存します。    
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

最後の 3 つのコマンドは、*$VirtualMachine* に格納されている仮想マシンにデータ ディスクを追加します。 各コマンドは、ディスクの名前、場所、および追加のプロパティを指定します。 各ディスクの URI は、*$DataDiskVhdUri01* *$DataDiskVhdUri02*、および *$DataDiskVhdUri03* に格納されます。
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


  次の 3 つのコマンドは、*$VirtualMachine* に格納されている仮想マシンにデータ ディスクを追加します。 各コマンドは、ディスクの名前、場所、および追加のプロパティを指定します。 各ディスクの URI は、*$DataDiskVhdUri01* *$DataDiskVhdUri02*、および *$DataDiskVhdUri03* に格納されます。
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

## <a name="next-steps"></a>次の手順
Azure Stack 仮想マシンの詳細については、「[Azure Stack の仮想マシンに関する考慮事項](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations)」に進んでください。
