---
title: クイック スタート - Azure PowerShell を使用して Windows IaaS VM を暗号化する | Microsoft Docs
description: このクイック スタートでは、Azure PowerShell を使用して Azure 上で Windows IaaS VM を暗号化する方法について説明します。
services: security
documentationcenter: na
author: mestew
manager: MBaldwin
ms.assetid: c8abd340-5ed4-42ec-b83f-4d679b61494d
ms.service: security
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2018
ms.author: mstewart
ms.custom: seodec18
ms.openlocfilehash: ee2a4be97b2b56f9c659639a34e821e37c188828
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53087866"
---
# <a name="quickstart-encrypt-a-windows-iaas-vm-with-azure-powershell"></a>クイック スタート: Azure PowerShell を使用して Windows IaaS VM を暗号化する

Azure Disk Encryption を使用して、Windows と Linux の IaaS 仮想マシンのディスクを暗号化できます。 このソリューションは、ディスクの暗号化キーとシークレットを制御および管理できるように、Azure Key Vault と統合されています。 Azure Disk Encryption を使用することで、保存時の VM を業界標準の暗号化テクノロジで確実にセキュリティ保護できます。 このクイック スタートでは、Windows Server 2016 の VM を作成し、OS ディスクを暗号化します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

- Windows PowerShell ISE
- [最新バージョンの Azure PowerShell](/powershell/azure/install-azurerm-ps) をインストールするか、最新バージョンに更新します。
    - AzureRM モジュールのバージョン 6.0.0 以降が必要です。 `Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path`
- [Azure Disk Encryption の前提条件スクリプト](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1)のコピー。
    - このスクリプトが既にある場合、最近変更されているので新しいコピーをダウンロードします。 
    - **Ctrl + A** キーを使用してすべてのテキストを選択し、**Ctrl + C** キーを使用してすべてのテキストをメモ帳にコピーします。
    - ファイルうを **ADEPrereqScript.ps1** という名前で保存します。


## <a name="sign-in-to-azure"></a>Azure へのサインイン

1. **[Windows PowerShell ISE]** を右クリックし、**[管理者として実行]** をクリックします。
1. **[管理者: Windows PowerShell ISE]** ウィンドウで、**[表示]** をクリックし、**[スクリプト ウィンドウの表示]** をクリックします。
1. スクリプト ウィンドウで、次のコマンドレットを入力します。 

     ```azurepowershell
      Connect-AzureRMAccount
     ```

1. **[スクリプトの実行]** の緑色の矢印をクリックするか、または F5 キーを押します。 
2. 対話型サインインを使用して、Azure アカウントへの接続を終了します。
3. 次の PowerShell スクリプトの実行用に返される**サブスクリプション ID** をコピーします。 

## <a name="bkmk_PrereqScript"></a> Azure Disk Encryption の前提条件スクリプトを実行する
 **ADEPrereqScript.ps1** では、リソース グループとキー コンテナーが作成され、キー コンテナーのアクセス ポリシーが設定されます。 また、このスクリプトでは、誤って削除されるのを防ぐため、キー コンテナーに対するリソース ロックも作成されます。  

1. **[管理者: Windows PowerShell ISE]** ウィンドウで、**[ファイル]** をクリックし、**[開く]** をクリックします。 **ADEPrereqScript.ps1** ファイルに移動してダブルクリックします。 スクリプトがスクリプト ウィンドウで開かれます。
2. **[スクリプトの実行]** の緑色の矢印をクリックするか F5 キーを押して、スクリプトを実行します。 
3. 新しい**リソース グループ**と新しい**キー コンテナー**の名前を入力します。 後でリソース グループを削除するので、このクイック スタートでは既存のリソース グループまたはキー コンテナーを使用しないでください。 
4. リソースを作成する場所を入力します (例: **EastUS**)。 場所の一覧を取得するには、`Get-AzureRMLocation` を使用します。
5. お使いの**サブスクリプション ID** をコピーします。 `Get-AzureRMSubscription` を使用してサブスクリプション ID を取得できます。  
6. **[スクリプトの実行]** の緑色の矢印をクリックします。 
7. 後で使用するので、返された **DiskEncryptionKeyVaultUrl** と **DiskEncryptionKeyVaultId** をコピーしておきます。

![PowerShell ISE で実行されている Azure Disk Encryption 前提条件スクリプト](media/azure-security-disk-encryption/ade-prereq-script.PNG)


## <a name="create-a-virtual-machine"></a>仮想マシンの作成 
ディスクを暗号化できるように、仮想マシンを作成する必要があります。 スクリプトでは、8 GB の RAM と 30 GB の OS ディスクを使用して Windows Server 2016 VM が作成されます。 

1. スクリプトを **[管理者: Windows PowerShell ISE]** スクリプト ウィンドウにコピーし、先頭の 3 つの変数を変更します。 リソース グループと場所は、[前提条件スクリプト](#bkmk_PrereqScript)で使用したものと同じにする必要があります。  

   ```azurepowershell
    # Variables for common values
    $resourceGroup = "MySecureRG"
    $location = "EastUS"
    $vmName = "MySecureVM"
    
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."
    
    # Create a resource group
    #New-AzureRmResourceGroup -Name $resourceGroup -Location $location
    
    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
    
    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
      -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
    
    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
      -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
    
    # Create an inbound network security group rule for port 3389
    $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
      -DestinationPortRange 3389 -Access Allow
    
    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
      -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
    
    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
      -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    
    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D2_v3 | `
    Set-AzureRmVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter-smalldisk -Version latest | `
    Add-AzureRmVMNetworkInterface -Id $nic.Id
    
    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
   ```

2. **[スクリプトの実行]** の緑色の矢印をクリックして、VM を作成します。  


## <a name="encrypt-the-disk-of-the-vm"></a>VM のディスクを暗号化する
キー コンテナーと VM を作成して構成したので、**Set-AzureRmVmDiskEncryptionExtension** コマンドレットを使用してディスクを暗号化できます。 
 
1. VM のディスクを暗号化するには、次のコマンドレットを実行します。

    ```azurepowershell
     Set-AzureRmVmDiskEncryptionExtension -ResourceGroupName "MySecureRG" -VMName "MySecureVM" `
     -DiskEncryptionKeyVaultId "<Returned by the prerequisites script>" -DiskEncryptionKeyVaultUrl "<Returned by the prerequisites script>"
     ```


1. 暗号化が完了したら、次のコマンドレットを使用して、ディスクが暗号化されたことを確認できます。 

     ```azurepowershell
     Get-AzureRmVmDiskEncryptionStatus -ResourceGroupName "MySecureRG" -VMName "MySecureVM"
     ```
    ![Get-AzureRmVmDiskEncryptionStatus の出力](media/azure-security-disk-encryption/ade-get-encryption-status.PNG)
    
## <a name="clean-up-resources"></a>リソースのクリーンアップ
 **ADEPrereqScript.ps1** では、キー コンテナーに対するリソース ロックが作成されます。 このクイック スタートからリソースをクリーンアップするには、最初にリソース ロックを削除してから、リソース グループを削除する必要があります。 

1. キー コンテナーからリソース ロックを削除します

     ```azurepowershell
     $LockId =(Get-AzureRMResourceLock -ResourceGroupName "MySecureRG" -ResourceName "MySecureVault" -ResourceType "Microsoft.KeyVault/vaults").LockID 
     Remove-AzureRmResourceLock -LockID $LockId
      ```
    
2. リソース グループを削除します。 グループ内のすべてのリソースも削除されます。 
     ```azurepowershell
      Remove-AzureRmResourceGroup -Name "MySecureRG"
      ```

## <a name="next-steps"></a>次の手順
次の記事に進み、IaaS VM に対する Azure Disk Encryption の前提条件について詳しく学習してください。

> [!div class="nextstepaction"]
> [Azure Disk Encryption の前提条件](azure-security-disk-encryption-prerequisites.md)