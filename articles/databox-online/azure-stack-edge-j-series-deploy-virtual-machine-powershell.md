---
title: Azure PowerShell を使用して Azure Stack Edge Pro GPU デバイスに VM をデプロイする
description: Azure PowerShell を使用して Azure Stack Edge Pro GPU デバイスに仮想マシン (VM) を作成し、それを管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: aa492acdedc2d131d28c894031de2181e87a2f3e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "90890701"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell"></a>Azure PowerShell を使用して Azure Stack Edge Pro GPU デバイスに VM をデプロイする

<!--[!INCLUDE [azure-stack-edge-gateway-deploy-vm-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]-->

このチュートリアルでは、Azure PowerShell を使用して、Azure Stack Edge Pro デバイスに VM を作成し、それを管理する方法について説明します。

## <a name="vm-deployment-workflow"></a>VM デプロイのワークフロー

次の図にデプロイのワークフローを示します。

![VM デプロイのワークフロー](media/azure-stack-edge-j-series-deploy-virtual-machine-powershell/vm-workflow_r.svg)

## <a name="prerequisites"></a>前提条件

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]



## <a name="query-for-built-in-subscription-on-the-device"></a>デバイスで組み込みサブスクリプションに対するクエリを実行する

Azure Resource Manager では、ユーザーが表示できる固定サブスクリプションが 1 つだけサポートされています。 このサブスクリプションはデバイスごとに一意であり、このサブスクリプション名またはサブスクリプション ID は変更できません。

このサブスクリプションには、VM の作成に必要なすべてのリソースが含まれています。 

> [!IMPORTANT]
> このサブスクリプションは、Azure サブスクリプションに接続または関連付けされておらず、お使いのデバイスでローカルに存在します。

このサブスクリプションは、VM のデプロイに使用されます。

1.  このサブスクリプションを一覧表示するには、次のように入力します。

    ```powershell
    Get-AzureRmSubscription
    ```
    
    サンプル出力を次に示します。

    ```powershell
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription A4257FDE-B946-4E01-ADE7-674760B8D1A3 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
3.  デバイスで実行されている登録済みのリソース プロバイダーの一覧を取得します。 この一覧には、通常、Compute、Network、および Storage が含まれます。

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > リソース プロバイダーは事前登録されており、修正または変更することはできません。
    
    サンプル出力を次に示します。

    ```powershell
    Get-AzureRmResourceProvider
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    ZoneMappings      :
    ```
    
## <a name="create-a-resource-group"></a>リソース グループを作成する

[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループは、ストレージ アカウント、ディスク、マネージド ディスクなどの Azure リソースをデプロイおよび管理するのに使用する論理コンテナーです。

> [!IMPORTANT]
> すべてのリソースは、デバイスと同じ場所に作成され、その場所は **DBELocal** に設定されます。

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

サンプル出力を次に示します。

```powershell
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

前の手順で作成したリソース グループを使用して、新しいストレージ アカウントを作成します。 これは、VM の仮想ディスク イメージのアップロードに使用される**ローカル ストレージ アカウント**です。

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Azure Resource Manager を使用して作成できるのは、ローカル冗長ストレージ (Standard_LRS または Premium_LRS) などのローカル ストレージ アカウントのみです。 階層型ストレージ アカウントを作成する場合は、[Azure Stack Edge Pro でのストレージ アカウントの追加と接続](azure-stack-edge-j-series-deploy-add-storage-accounts.md)に関するページの手順を参照してください。

サンプル出力を次に示します。

```powershell
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
Location               : DBELocal
Sku                    : Microsoft.Azure.Management.Storage.Models.Sku
Kind                   : Storage
Encryption             : Microsoft.Azure.Management.Storage.Models.Encryption
AccessTier             :
CreationTime           : 11/13/2019 9:43:49 PM
CustomDomain           :
Identity               :
LastGeoFailoverTime    :
PrimaryEndpoints       : Microsoft.Azure.Management.Storage.Models.Endpoints
PrimaryLocation        : DBELocal
ProvisioningState      : Succeeded
SecondaryEndpoints     :
SecondaryLocation      :
StatusOfPrimary        : Available
StatusOfSecondary      :
Tags                   :
EnableHttpsTrafficOnly : False
NetworkRuleSet         :
Context                : Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext
ExtendedProperties     : {}
```

ストレージ アカウント キーを取得するには、`Get-AzureRmStorageAccountKey` コマンドを実行します。 このコマンドのサンプル出力を次に示します。

```powershell
PS C:\Users\Administrator> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: my-resource-ase
Name:myasestoracct

KeyName Value
------- -----
key1 /IjVJN+sSf7FMKiiPLlDm8mc9P4wtcmhhbnCa7...
key2 gd34TcaDzDgsY9JtDNMUgLDOItUU0Qur3CBo6Q...
```

## <a name="add-blob-uri-to-hosts-file"></a>hosts ファイルに BLOB の URI を追加する

「[エンドポイントの名前解決のためにホスト ファイルを変更する](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)」で、BLOB ストレージへの接続に使用しているクライアントのホスト ファイルに、BLOB の URI を既に追加しました。 これは、BLOB の URI のエントリでした。

\<Azure consistent network services VIP \> \<storage name\>.blob.\<appliance name\>.\<dnsdomain\>


## <a name="install-certificates"></a>証明書をインストールする

*https* をお使いの場合は、デバイスに適切な証明書をインストールする必要があります。 この場合は、BLOB エンドポイント証明書をインストールします。 詳細については、[証明書を管理する](azure-stack-edge-j-series-manage-certificates.md)方法に関するページで、証明書を作成およびアップロードする方法を参照してください。

## <a name="upload-a-vhd"></a>VHD のアップロード

前の手順で作成したローカル ストレージ アカウントのページ BLOB に、使用するディスク イメージをコピーします。 [AzCopy](../storage/common/storage-use-azcopy-v10.md) などのツールを使用して、前の手順で作成したストレージ アカウントに VHD をアップロードできます。 

AzCopy を使用する前に、Azure Stack Edge Pro デバイスで使用している BLOB ストレージ REST API バージョンで使用できるように、[AzCopy が正しく構成されている](#configure-azcopy)ことを確認してください。

```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> [!NOTE]
> VHD からマネージド ディスクを作成するページに `BlobType` を設定します。 AzCopy を使用して階層型ストレージ アカウントに書き込むときにブロックするように `BlobType` を設定します。

ディスク イメージは Marketplace からダウンロードできます。 詳細な手順については、[Azure Marketplace からの仮想ディスク イメージの取得](azure-stack-edge-j-series-create-virtual-machine-image.md)に関するページを参照してください。

AzCopy 7.3 を使用したサンプル出力を次に示します。 このコマンドの詳細については、[AzCopy を使用したストレージ アカウントへの VHD ファイルのアップロード](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md)に関するページを参照してください。


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```


## <a name="create-managed-disks-from-the-vhd"></a>VHD からマネージド ディスクを作成する

アップロードした VHD からマネージド ディスクを作成します。

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
サンプル出力を次に示します。 
<code>
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import –SourceUri http://</code><code>sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd</code> 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

サンプル出力を次に示します。 このコマンドレットの詳細については、「[New-AzureRmDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0)」を参照してください。

```powershell
Tags               :
New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

ResourceGroupName  : rg191113014333
ManagedBy          :
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              :
TimeCreated        : 11/13/2019 1:49:07 PM
OsType             :
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 30
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```

## <a name="create-a-vm-image-from-the-image-managed-disk"></a>イメージのマネージド ディスクから VM イメージを作成する

次のコマンドを使用して、マネージド ディスクから VM イメージを作成します。 \< \> 内の値を、選択した名前に置き換えます。

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 

The supported OS types are Linux and Windows.

For OS Type=Linux, for example:
Set-AzureRmImageOsDisk -Image $imageConfig -OsType 'Linux' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

サンプル出力を次に示します。 このコマンドレットの詳細については、「[New-AzureRmImage](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0)」を参照してください。

```powershell
New-AzureRmImage -Image Microsoft.Azure.Commands.Compute.Automation.Models.PSImage -ImageName ig191113014333  -ResourceGroupName rg191113014333
ResourceGroupName    : rg191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
Id                   : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micr
                       osoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```

## <a name="create-vm-with-previously-created-resources"></a>事前に作成しておいたリソースを使用して VM を作成する

VM を作成してデプロイする前に、1 つの仮想ネットワークを作成し、仮想ネットワーク インターフェイスを関連付ける必要があります。

> [!IMPORTANT]
> 仮想ネットワークと仮想ネットワーク インターフェイスを作成するときは、次の規則が適用されます。
> - (リソース グループ間の場合も) 1 つの VNet のみを作成でき、これは、アドレス空間に関して論理ネットワークと正確に一致する必要があります。
> -   VNet で許可されるサブネットは 1 つだけです。 サブネットは、VNet と完全に同じアドレス空間である必要があります。
> -   VNIC の作成時には静的割り当て方法のみが許可され、ユーザーはプライベート IP アドレスを指定する必要があります。

 
**VNet を作成する**

```powershell
$subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name <Subnet name> -AddressPrefix <Address Prefix>
$aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName <Resource group name> -Name <Vnet name> -Location DBELocal -AddressPrefix <Address prefix> -Subnet $subNetId
```

**VNet のサブネット ID を使用して VNIC を作成する**

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

これらのコマンドのサンプル出力は次のとおりです。

```powershell
PS C:\Users\Administrator> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\Users\Administrator> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\Users\Administrator> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\Users\Administrator> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\Users\Administrator> $Nic

PS C:\Users\Administrator> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/DbeSystemRG/providers/Microsoft.Netw
                              ork/virtualNetworks/vSwitch1/subnets/subnet123",
                                    "ResourceNavigationLinks": [],
                                    "ServiceEndpoints": []
                                  },
                                  "ProvisioningState": "Succeeded",
                                  "PrivateIpAddressVersion": "IPv4",
                                  "LoadBalancerBackendAddressPools": [],
                                  "LoadBalancerInboundNatRules": [],
                                  "Primary": true,
                                  "ApplicationGatewayBackendAddressPools": [],
                                  "ApplicationSecurityGroups": []
                                }
                              ]
DnsSettings                 : {
                                "DnsServers": [],
                                "AppliedDnsServers": []
                              }
EnableIPForwarding          : False
EnableAcceleratedNetworking : False
NetworkSecurityGroup        : null
Primary                     : True
MacAddress                  : 00155D18E432                :
```

必要に応じて、VM の VNIC を作成するときに、パブリック IP を渡すことができます。 このインスタンスでは、パブリック IP によってプライベート IP が返されます。 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```


**VM を作成します**

これで、VM イメージを使用して VM を作成し、先ほど作成した仮想ネットワークに接続できるようになりました。

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)

You will use this username, password to login to the VM, once it is created and powered up.

$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```

## <a name="connect-to-a-vm"></a>VM への接続

Windows と Linux のどちらの VM を作成したかによって、接続する手順が異なる場合があります。

### <a name="connect-to-linux-vm"></a>Linux VM への接続

Linux VM に接続するには、これらの手順に従います。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>Windows VM への接続

Windows VM に接続するには、これらの手順に従います。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]


<!--Connect to the VM using the private IP that you passed during the VM creation.

Open an SSH session to connect with the IP address.

`ssh -l <username> <ip address>`

When prompted, provide the password that you used when creating the VM.

If you need to provide the SSH key, use this command.

ssh -i c:/users/Administrator/.ssh/id_rsa Administrator@5.5.41.236

If you used a public IP address during VM creation, you can use that IP to connect to the VM. To get the public IP: 

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
The public IP in this case will be the same as the private IP that you passed during virtual network interface creation.-->


## <a name="manage-vm"></a>VM を管理する

次のセクションでは、Azure Stack Edge Pro デバイス上に作成する VM に関する一般的ないくつかの操作について説明します。

### <a name="list-vms-running-on-the-device"></a>デバイスで実行されている VM を一覧表示する

Azure Stack Edge Pro デバイスで実行されているすべての VM の一覧を取得するには、次のコマンドを実行します。


`Get-AzureRmVM -ResourceGroupName <String> -Name <String>`
 

### <a name="turn-on-the-vm"></a>VM をオンにする

デバイスで実行されている仮想マシンをオンにするには、次のコマンドレットを実行します。


`Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>`


このコマンドレットの詳細については、「[Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0)」を参照してください。

### <a name="suspend-or-shut-down-the-vm"></a>VM を中断またはシャットダウンする

デバイスで実行されている仮想マシンを停止またはシャットダウンするには、次のコマンドレットを実行します。


```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```


このコマンドレットの詳細については、「[Stop-AzureRmVM コマンドレット](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0)」を参照してください。

### <a name="add-a-data-disk"></a>データ ディスクの追加

VM のワークロード要件が増加した場合は、データ ディスクの追加が必要になることがあります。

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="delete-the-vm"></a>VM の削除

デバイスから仮想マシンを削除するには、次のコマンドレットを実行します。

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

このコマンドレットの詳細については、「[Remove-AzureRmVm コマンドレット](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0)」を参照してください。


## <a name="supported-vm-sizes"></a>サポートされる VM のサイズ

VM のサイズにより、CPU、GPU、メモリなど、VM で利用できる計算リソースの量が決定されます。 仮想マシンは、ワークロードに適した VM サイズを使用して作成する必要があります。 すべてのコンピューターが同じハードウェア上で実行される場合でも、コンピューターのサイズによってディスク アクセスの制限が異なります。これは、VM 間のディスク アクセス全体を管理するのに役立ちます。 ワークロードが増えた場合は、既存の仮想マシンのサイズを変更することもできます。

Azure Stack Edge Pro デバイスでの作成については、次の Standard Dv2 シリーズ VM がサポートされています。

### <a name="dv2-series"></a>Dv2 シリーズ
|サイズ     |vCPU     |メモリ (GiB) | 一時ストレージ (GiB)  | OS ディスクの最大スループット (IOPS) | 一時ストレージの最大スループット (IOPS) | データ ディスクの最大スループット (IOPS) | 最大 NIC 数 |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standard_D1_v2** |1   |3.5 |50   |500 |3000  |4 / 4x500   |2 |
|**Standard_D2_v2** |2   |7   |100  |500 |6000  |8 / 8x500   |2 |
|**Standard_D3_v2** |4   |14  |200  |500 |12000 |16 / 16x500 |4 |
|**Standard_D4_v2** |8   |28  |400  |500 |24000 |32 / 32x500 |8 |
|**Standard_D5_v2** |16  |56  |800  |500 |48000 |64 / 64x500 |8 |

### <a name="dsv2-series"></a>DSv2 シリーズ
|サイズ     |vCPU     |メモリ (GiB) | 一時ストレージ (GiB)  | OS ディスクの最大スループット (IOPS) | 一時ストレージの最大スループット (IOPS) | データ ディスクの最大スループット (IOPS) | 最大 NIC 数 |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standard_DS1_v2** |1   |3.5 |7   |1000 |4000  |4 / 4x2300   |2 |
|**Standard_DS2_v2** |2   |7   |14  |1000 |8000  |8 / 8x2300   |2 |
|**Standard_DS3_v2** |4   |14  |28  |1000 |16000 |16 / 16x2300 |4 |
|**Standard_DS4_v2** |8   |28  |56  |1000 |32000 |32 / 32x2300 |8 |
|**Standard_DS5_v2** |16  |56  |112 |1000 |64000 |64 / 64x2300 |8 |

### <a name="dv2-series"></a>Dv2 シリーズ
|サイズ     |vCPU     |メモリ (GiB) | 一時ストレージ (GiB)  | OS ディスクの最大スループット (IOPS) | 一時ストレージの最大スループット (IOPS) | データ ディスクの最大スループット (IOPS) | 最大 NIC 数 |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standard_D11_v2** |2   |14  |100  |500 |6000   |8 / 8x500    |2 |
|**Standard_D12_v2** |4   |28  |200  |500 |12000  |16 / 16x500  |4 |
|**Standard_D13_v2** |8   |56  |400  |500 |24000  |32 / 32x500  |8 |
|**Standard_D14_v2** |16  |112 |800  |500 |48000  |64 / 64x500  |8 |


### <a name="dsv2-series"></a>DSv2 シリーズ
|サイズ     |vCPU     |メモリ (GiB) | 一時ストレージ (GiB)  | OS ディスクの最大スループット (IOPS) | 一時ストレージの最大スループット (IOPS) | データ ディスクの最大スループット (IOPS) | 最大 NIC 数 |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1000 |8000   |4 / 4x2300    |2 |
|**Standard_DS12_v2** |4   |28  |56   |1000 |16000  |8 / 8x2300    |4 |
|**Standard_DS13_v2** |8   |56  |112  |1000 |32000  |16 / 16x2300  |8 |
|**Standard_DS14_v2** |16  |112 |224  |1000 |64000  |32 / 32x2300  |8 |

詳細については、[汎用 VM サイズ上の Dv2 シリーズ](../virtual-machines/dv2-dsv2-series.md#dv2-series)に関するページを参照してください。

## <a name="unsupported-vm-operations-and-cmdlets"></a>サポートされていない VM 操作およびコマンドレット

拡張機能、スケール セット、可用性セット、スナップショットはサポートされていません。

## <a name="configure-azcopy"></a>AzCopy を構成する

最新バージョンの AzCopy をインストールする場合は、Azure Stack Edge Pro デバイスの BLOB ストレージ REST API バージョンと確実に一致するように AzCopy を構成する必要があります。

Azure Stack Edge Pro デバイスへのアクセスに使用するクライアントで、BLOB ストレージ REST API バージョンと一致するようにグローバル変数を設定します。

### <a name="on-windows-client"></a>Windows クライアントの場合 

`$Env:AZCOPY_DEFAULT_SERVICE_API_VERSION = "2017-11-09"`

### <a name="on-linux-client"></a>Linux クライアントの場合

`export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`

AzCopy の環境変数が正しく設定されているかどうかを確認するには、次の手順に従います。

1. "azcopy env" を実行します。
2. `AZCOPY_DEFAULT_SERVICE_API_VERSION` パラメーターを見つけます。 これには、前の手順で設定した値が含まれている必要があります。


## <a name="next-steps"></a>次のステップ

[Azure Resource Manager コマンドレット](https://docs.microsoft.com/powershell/module/azurerm.resources/?view=azurermps-6.13.0)
