---
title: "Packer を使用して Windows Azure VM のイメージを作成する方法 | Microsoft Docs"
description: "Packer を使用して Azure に Windows 仮想マシンのイメージを作成する方法について説明します。"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/13/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 035286e74e66cf1eb12a51551fa55a2a0ea5517c
ms.contentlocale: ja-jp
ms.lasthandoff: 06/15/2017

---

# <a name="how-to-use-packer-to-create-windows-virtual-machine-images-in-azure"></a>Packer を使用して Azure に Windows 仮想マシンのイメージを作成する方法
Azure の各仮想マシン (VM) は、Windows ディストリビューションと OS のバージョンを定義するイメージから作成されます。 イメージには、プリインストールされているアプリケーションと構成を含めることができます。 Azure Marketplace には、ほとんどの OS およびアプリケーション環境用の自社製およびサード パーティ製のイメージが数多く用意されています。また、ニーズに合わせて独自のイメージを作成することもできます。 この記事では、オープン ソース ツール [Packer](https://www.packer.io/) を使用して Azure に独自のイメージを定義およびビルドする方法について、詳しく説明します。


## <a name="create-supporting-azure-resources"></a>関連する Azure リソースを作成する
ビルド プロセス中、Packer はソース VM をビルドする際に一時的な Azure リソースを作成します。 イメージとして使用するためにそのソース VM をキャプチャするには、リソース グループおよびストレージ アカウントを定義する必要があります。 Packer のビルド プロセスからの出力は、このリソース グループおよびストレージ アカウントに格納されます。

最初に、[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用してリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```powershell
$rgName = "myResourceGroup"
$location = "East US"
New-AzureRmResourceGroup -Name $rgName -Location $location
```

次に、[New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) を使用してストレージ アカウントを作成します。 ストレージ アカウント名は一意である必要があり、長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。 次の例では、*mystorageaccount* という名前のストレージ アカウントを作成します。

```powershell
$storageAccountName = "mystorageaccount"
New-AzureRmStorageAccount -ResourceGroupName $rgName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"
```


## <a name="create-azure-credentials"></a>Azure 資格情報を作成する
Packer はサービス プリンシパルを使用して Azure で認証されます。 Azure のサービス プリンシパルは、アプリケーション、サービス、および Packer などのオートメーション ツールで使用できるセキュリティ ID です。 Azure で実行できる操作やサービス プリンシパルを設定するアクセス許可をコントロールおよび定義します。

[New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) を使用してサービス プリンシパルを作成し、そのサービス プリンシパルにアクセス許可を割り当て、[New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) を使用してリソースを作成および管理します。

```powershell
$sp = New-AzureRmADServicePrincipal -DisplayName "Azure Packer" -Password "P@ssw0rd!"
Sleep 20
New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $sp.ApplicationId
```

Azure に対して認証するには、[Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) を使用して Azure のテナントとサブスクリプション ID も取得する必要があります。

```powershell
$sub = Get-AzureRmSubscription
$sub.TenantId
$sub.SubscriptionId
```

次の手順でこれら 2 つの ID を使用します。


## <a name="define-packer-template"></a>Packer テンプレートを定義する
イメージをビルドするには、テンプレートを JSON ファイルとして作成します。 テンプレートでは、実際のビルド プロセスを実行するビルダーとプロビジョナーを定義します。 Packer には [Azure 用のプロビジョナー](https://www.packer.io/docs/builders/azure.html)が用意されており、前の手順で作成したサービス プリンシパルの資格情報などの Azure リソースを定義できます。

*windows.json* というファイルを作成し、次のコンテンツを貼り付けます。 次に対応する実際の値を入力します。

| パラメーター       | 入手場所 |
|-----------------|----------------------------------------------------|
| *client_id*      | `$sp.applicationId` でサービス プリンシパル ID を表示します |
| *client_secret*  | `$securePassword` で指定したパスワード |
| *tenant_id*      | `$sub.TenantId` コマンドからの出力 |
| *subscription_id* | `$sub.SubscriptionId` コマンドからの出力 |
| *object_id*       | `$sp.Id` でサービス プリンシパル オブジェクト ID を表示します |
| *storage_account* | `$storageAccountName` で指定した名前 |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "0831b578-8ab6-40b9-a581-9a880a94aab1",
    "client_secret": "P@ssw0rd!",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "object_id": "a7dfb070-0d5b-47ac-b9a5-cf214fff0ae2",

    "resource_group_name": "myResourceGroup",
    "storage_account": "mystorageaccount",

    "capture_container_name": "images",
    "capture_name_prefix": "packer",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": "true",
    "winrm_insecure": "true",
    "winrm_timeout": "3m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force}",
      "& $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /shutdown /quiet"
    ]
  }]
}
```

このテンプレートは、Windows Server 2016 VM を作成し、IIS をインストールした後、Sysprep で VM を一般化します。


## <a name="build-packer-image"></a>Packer イメージをビルドする
まだローカル コンピューターに Packer がインストールされていない場合は、[手順に従って Packer をインストールしてください](https://www.packer.io/docs/install/index.html)。

次のように Packer テンプレート ファイルを指定してイメージをビルドします。

```bash
./packer build windows.json
```

上記のコマンドの出力例は次のとおりです。

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> Location          : 'East US'
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> DeploymentName    : 'pkrdpbxpchwtl43'
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> DeploymentName    : 'pkrdpbxpchwtl43'
==> azure-arm: Getting the certificate's URL ...
==> azure-arm:  -> Key Vault Name        : 'pkrkvbxpchwtl43'
==> azure-arm:  -> Key Vault Secret Name : 'packerKeyVaultSecret'
==> azure-arm:  -> Certificate URL       : 'https://pkrkvbxpchwtl43.vault.azure.net/secrets/packerKeyVaultSecret/6c12261b552c48ebadb7d4a88d99d011'
==> azure-arm: Setting the certificate's URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> DeploymentName    : 'pkrdpbxpchwtl43'
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> DeploymentName    : 'pkrdpbxpchwtl43'
==> azure-arm: Getting the VM's IP address ...
==> azure-arm:  -> ResourceGroupName   : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> PublicIPAddressName : 'packerPublicIP'
==> azure-arm:  -> NicName             : 'packerNic'
==> azure-arm:  -> Network Connection  : 'PublicEndpoint'
==> azure-arm:  -> IP Address          : '40.121.160.214'
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /tmp/packer-powershell-provisioner759984171
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version="1.1.0.1" xmlns="http://schemas.microsoft.com/powershell/2004/04"><Obj S="progress" RefId="0"><TN RefId="0"><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N="SourceId">1</
I64><PR N="Record"><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine's properties ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> ComputeName       : 'pkrvmbxpchwtl43'
==> azure-arm:  -> OS Disk           : 'https://mystorageaccount.blob.core.windows.net/images/pkrosbxpchwtl43.vhd'
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> ComputeName       : 'pkrvmbxpchwtl43'
==> azure-arm: Capturing image ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm:  -> ComputeName       : 'pkrvmbxpchwtl43'
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : 'packer-Resource-Group-bxpchwtl43'
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : 'https://mystorageaccount.blob.core.windows.net/images/pkrosbxpchwtl43.vhd'
Build 'azure-arm' finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

StorageAccountLocation: eastus
OSDiskUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.5b77b243-26d2-425c-9c57-0ba6b99ef968.vhd
OSDiskUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.5b77b243-26d2-425c-9c57-0ba6b99ef968.vhd?se=2017-07-13T22%3A25%3A02Z&sig=BYAbOXakavYV%2FbdWYfGqUIdsz2GXivbk0hxG0
5Mc09k%3D&sp=r&sr=b&sv=2015-02-21
TemplateUri: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.5b77b243-26d2-425c-9c57-0ba6b99ef968.json
TemplateUriReadOnlySas: https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-vmTemplate.5b77b243-26d2-425c-9c57-0ba6b99ef968.json?se=2017-07-13T22%3A25%3A02Z&sig=wDMO3aSifbWLSISwoUOfkDMc5z7iKbGV
3us64gGvvlw%3D&sp=r&sr=b&sv=2015-02-21
```

Packer が VM をビルド、プロビジョナーを実行、およびデプロイメントをクリーンアップするのに数分かかります。


## <a name="create-azure-image"></a>Azure イメージを作成する
Packer ビルド プロセスからの出力は、指定したストレージ アカウントの仮想ハード ディスク (VHD) です。 [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage) を使用してこの VHD から Azure イメージを作成し、Packer ビルド出力の最後に記された `OSDiskUri` パスを指定します。 次の例では、`myImage` という名前のイメージを作成します。

```powershell
$osVhdUri = "https://mystorageaccount.blob.core.windows.net/system/Microsoft.Compute/Images/images/packer-osDisk.5b77b243-26d2-425c-9c57-0ba6b99ef968.vhd"
$imageName = "myImage"

$imageConfig = New-AzureRmImageConfig -Location $location
$imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig `
    -OsType "Windows" `
    -OsState "Generalized" `
    -BlobUri $osVhdUri
$image = New-AzureRmImage -ImageName $imageName `
    -ResourceGroupName $rgName `
    -Image $imageConfig
```

このイメージを使用して、Azure サブスクリプション全体に VM を作成できます。 VM を作成するリソース グループは、ソース イメージと同じリソース グループに限定されません。


## <a name="create-vm-from-azure-image"></a>Azure イメージから VM を作成する
次のように、[Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) を使用して VM の管理者のユーザー名とパスワードを設定します。

```powershell
$cred = Get-Credential
```

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して、イメージから VM を作成できるようになりました。 次の例では、*myImage* から *myVM* という名前の VM を作成します。

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName $rgName `
    -Location $location `
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$publicIP = New-AzureRmPublicIpAddress `
    -ResourceGroupName $rgName `
    -Location $location `
    -AllocationMethod "Static" `
    -IdleTimeoutInMinutes 4 `
    -Name "myPublicIP"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleWWW  `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1001 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80 `
    -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName $rgName `
    -Location $location `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleWeb

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
    -Name myNic `
    -ResourceGroupName $rgName `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $publicIP.Id `
    -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vmConfig
```

VM の作成には数分かかります。


## <a name="test-vm-and-iis"></a>VM と IIS をテストする
VM のパブリック IP アドレスを取得するには、[Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用します。 次の例では、先ほど作成した *myPublicIP* の IP アドレスを取得します。

```powershell
Get-AzureRmPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIP" | select "IpAddress"
```

このパブリック IP アドレスを Web ブラウザーに入力できます。

![IIS の既定のサイト](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>次のステップ
この例では、Packer を使用して IIS が既にインストールされた VM イメージを作成しました。 この VM イメージは、アプリを、Team Services、Ansible、Chef、Puppet でイメージから作成した VM にデプロイするなど、既存のデプロイ ワークフローとともに使用できます。

他の Windows ディストリビューション用の追加の Packer テンプレートの例については、[この GitHub リポジトリ](https://github.com/hashicorp/packer/tree/master/examples/azure)をご覧ください。
