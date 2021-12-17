---
title: Azure PowerShell を使用して Azure Stack Edge デバイスに VM をデプロイする
description: Azure PowerShell を使用して Azure Stack Edge デバイスに仮想マシンを作成および管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/26/2021
ms.author: alkohli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 32d2102bad49a3023df9b1d25f5b5378a3b30ad0
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2021
ms.locfileid: "114721497"
---
# <a name="deploy-vms-on-your-azure-stack-edge-device-via-azure-powershell"></a>Azure PowerShell を使用して Azure Stack Edge デバイスに VM をデプロイする

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure PowerShell を使用して、Azure Stack Edge デバイスに仮想マシン (VM) を作成し、管理する方法について説明します。

## <a name="vm-deployment-workflow"></a>VM デプロイのワークフロー

VM のデプロイのワークフローの概要は次のとおりです。

1. デバイスのローカル Azure Resource Manager に接続します。
1. デバイス上の組み込みサブスクリプションを特定します。
1. VM イメージを用意します。
1. 組み込みサブスクリプションにリソース グループを作成します。 このリソース グループに、VM とすべての関連リソースが含まれます。  
1. VM イメージの作成に使用する VHD を格納するためのローカル ストレージ アカウントをデバイス上に作成します。
1. Windows/Linux ソース イメージをストレージ アカウントにアップロードして、マネージド ディスクを作成します。
1. マネージド ディスクを使用して VM イメージを作成します。
1. デバイス ポートでコンピューティングを有効にして、仮想スイッチを作成します。
1. これにより、コンピューティングを有効にしたポートに接続された仮想スイッチを使用して仮想ネットワークが作成されます。
1. VM を作成します。これには、以前に作成した VM イメージ、仮想ネットワーク、および、仮想ネットワーク内で通信するための仮想ネットワーク インターフェイスを使用します。次に、リモートから VM にアクセスするためのパブリック IP アドレスを割り当てます。 必要に応じて、VM により多くのストレージを提供するためにデータ ディスクを含ます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]


## <a name="query-for-a-built-in-subscription-on-the-device"></a>デバイスで組み込みサブスクリプションに対するクエリを実行する

Azure Resource Manager では、ユーザーが表示できる固定サブスクリプションが 1 つだけサポートされています。 このサブスクリプションはデバイスごとに一意であり、サブスクリプション名もサブスクリプション ID も変更できません。

サブスクリプションには、VM の作成に必要なすべてのリソースが含まれています。 

> [!IMPORTANT]
> サブスクリプションは、Azure portal から VM を有効にしたときに作成され、デバイス上のローカルに存在します。

サブスクリプションは、VM のデプロイに使用されます。

### <a name="az"></a>[Az](#tab/az)

1.  サブスクリプションを一覧表示するには、次のコマンドを実行します。

    ```powershell
    Get-AzSubscription
    ```
    
    出力例を次に示します。

    ```output
    PS C:\WINDOWS\system32> Get-AzSubscription
    
    Name                          Id                                   TenantId
    ----                          --                                   --------
    Default Provider Subscription ...                                  ...
    
    
    PS C:\WINDOWS\system32>
    ```
        
1. デバイスで実行されている登録済みのリソース プロバイダーの一覧を取得します。 この一覧には、通常、コンピューティング、ネットワーク、およびストレージが含まれます。

    ```powershell
    Get-AzResourceProvider
    ```

    > [!NOTE]
    > リソース プロバイダーは事前登録されており、修正または変更することはできません。
    
    出力例を次に示します。

    ```output
    PS C:\WINDOWS\system32>  Get-AzResourceProvider
        
    ProviderNamespace : Microsoft.AzureBridge
    RegistrationState : Registered
    ResourceTypes     : {locations, operations, locations/ingestionJobs}
    Locations         : {DBELocal}
    
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

1.  サブスクリプションを一覧表示するには、次のコマンドを実行します。

    ```powershell
    Get-AzureRmSubscription
    ```
    
    出力例を次に示します。

    ```output
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription         ...                 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
1. デバイスで実行されている登録済みのリソース プロバイダーの一覧を取得します。 この一覧には、通常、コンピューティング、ネットワーク、およびストレージが含まれます。

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > リソース プロバイダーは事前登録されており、修正または変更することはできません。
    
    出力例を次に示します。

    ```output
    PS C:\Windows\system32> Get-AzureRmResourceProvider
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
---
   
## <a name="create-a-resource-group"></a>リソース グループを作成する

まず、新しい Azure リソース グループを作成し、ストレージ アカウント、ディスク、ネットワーク インターフェイス、マネージド ディスクなど、すべての VM 関連リソースの論理コンテナーとしてこれを使用します。

> [!IMPORTANT]
> すべてのリソースは、デバイスと同じ場所に作成され、その場所は **DBELocal** に設定されます。

### <a name="az"></a>[Az](#tab/az)

1. パラメーターを設定する。

    ```powershell
    $ResourceGroupName = "<Resource group name>" 
    ```
1. VM 用に作成するリソースのためのリソース グループを作成します。
   
    ```powershell
    New-AzResourceGroup -Name $ResourceGroupName -Location DBELocal
    ```

    出力例を次に示します。
    
    ```output
    PS C:\WINDOWS\system32> New-AzResourceGroup -Name myaseazrg -Location DBELocal
    
    ResourceGroupName : myaseazrg
    Location          : dbelocal
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/.../resourceGroups/myaseazrg
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

出力例を次に示します。

```output
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```
---

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

前の手順で作成したリソース グループを使用して、新しいストレージ アカウントを作成します。 これは、VM の仮想ディスク イメージのアップロードに使用するローカル ストレージ アカウントです。

### <a name="az"></a>[Az](#tab/az)

1. パラメーターを設定する。

    ```powershell
    $StorageAccountName = "<Storage account name>"    
    ```

1. デバイス上に新しいローカル ストレージ アカウントを作成します。

    ```powershell
    New-AzStorageAccount -Name $StorageAccountName -ResourceGroupName $ResourceGroupName -Location DBELocal -SkuName Standard_LRS
    ```
    
    > [!NOTE]
    > Azure Resource Manager を使用して、ローカル冗長ストレージ (Standard または Premium) などのローカル ストレージ アカウントのみを作成できます。 階層化ストレージ アカウントを作成するには、[GPU が搭載された Azure Stack Edge Pro でストレージ アカウントを使用してデータを転送する方法に関するチュートリアル](azure-stack-edge-gpu-deploy-add-storage-accounts.md)を参照してください。

    出力例を次に示します。
    
    ```output
    PS C:\WINDOWS\system32> New-AzStorageAccount -Name myaseazsa -ResourceGroupName myaseazrg -Location DBELocal -SkuName Standard_LRS
    
    StorageAccountName ResourceGroupName PrimaryLocation SkuName      Kind    AccessTier CreationTime
    ------------------ ----------------- --------------- -------      ----    ---------- ------------
    myaseazsa          myaseazrg         DBELocal        Standard_LRS Storage            6/10/2021 11:45...
    
    PS C:\WINDOWS\system32>
    ```

1. 前のステップで作成したアカウント用のストレージ アカウント キーを取得します。 求められたら、リソース グループ名とストレージ アカウント名を指定します。

    ```powershell
    Get-AzStorageAccountKey
    ``` 

    出力例を次に示します。

    ```output
    PS C:\WINDOWS\system32> Get-AzStorageAccountKey
    
    cmdlet Get-AzStorageAccountKey at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    ResourceGroupName: myaseazrg
    Name: myaseazsa
    
    KeyName Value                                                                                    Permissions
    ------- -----                                         ------
    key1    gv3OF57tuPDyzBNc1M7fhil2UAiiwnhTT6zgiwE3TlF/CD217Cvw2YCPcrKF47joNKRvzp44leUe5HtVkGx8RQ==   Full
    key2    kmEynIs3xnpmSxWbU41h5a7DZD7v4gGV3yXa2NbPbmhrPt10+QmE5PkOxxypeSqbqzd9si+ArNvbsqIRuLH2Lw==   Full
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Azure Resource Manager を使用して、ローカル冗長ストレージ (Standard または Premium) などのローカル ストレージ アカウントのみを作成できます。 階層化ストレージ アカウントを作成するには、[GPU が搭載された Azure Stack Edge Pro でストレージ アカウントを使用してデータを転送する方法に関するチュートリアル](./azure-stack-edge-gpu-deploy-add-storage-accounts.md)を参照してください。

出力例を次に示します。

```output
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/.../resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
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

ストレージ アカウント キーを取得するには、`Get-AzureRmStorageAccountKey` コマンドを実行します。 出力例を次に示します。

```output
PS C:\windows\system32> Get-AzureRmStorageAccountKey

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
---

## <a name="add-the-blob-uri-to-the-host-file"></a>BLOB URI をホスト ファイルに追加する

[Azure Stack Edge デバイスでの Azure Resource Manager への接続](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)に関するページの「**手順 5: エンドポイント名の解決のためにホスト ファイルを変更する**」で、Azure Blob Storage への接続に使用している BLOB URI は、クライアントの hosts ファイルに既に追加しました。 このエントリは、BLOB URI を追加するために使用されました。

`<Device IP address>` `<storage name>.blob.<appliance name>.<dnsdomain>`

## <a name="install-certificates"></a>証明書をインストールする

HTTPS をお使いの場合は、デバイスに適切な証明書をインストールする必要があります。 ここでは、BLOB エンドポイント証明書をインストールします。 詳細については、[GPU デバイスが搭載された Azure Stack Edge Pro での証明書の使用](azure-stack-edge-gpu-manage-certificates.md)に関するページを参照してください。

## <a name="upload-a-vhd"></a>VHD のアップロード

これまでに作成したローカル ストレージ アカウントのページ BLOB に、使用するディスク イメージをコピーします。 [AzCopy](../storage/common/storage-use-azcopy-v10.md) などのツールを使用して、ストレージ アカウントに仮想ハード ディスク (VHD) をアップロードできます。 


### <a name="az"></a>[Az](#tab/az)

AzCopy 10 で次のコマンドを使用します。  

1. AzCopy 用の API の適切なバージョンなど、いくつかのパラメーターを設定します。 この例では、AzCopy 10 を使用しました。

    ```powershell
    $Env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2019-07-07"    
    $ContainerName = <Container name>
    $ResourceGroupName = <Resource group name>
    $StorageAccountName = <Storage account name>
    $VHDPath = "Full VHD Path"
    $VHDFile = <VHD file name>
    ```
1. ソース (この場合はローカル システム) から、前のステップでデバイスに作成したストレージ アカウントに、VHD をコピーします。

    ```powershell
    $StorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value    
    $endPoint = (Get-AzStorageAccount -name $StorageAccountName -ResourceGroupName $ResourceGroupName).PrimaryEndpoints.Blob    
    $StorageAccountContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endpoint    
    $StorageAccountSAS = New-AzStorageAccountSASToken -Service Blob -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly        
    <Path to azcopy.exe> cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"    
    ```

    出力例を次に示します。 
    
    ```output
    PS C:\windows\system32> $ContainerName = "testcontainer1"
    PS C:\windows\system32> $ResourceGroupName = "myaseazrg"
    PS C:\windows\system32> $StorageAccountName = "myaseazsa"
    PS C:\windows\system32> $VHDPath = "C:\Users\alkohli\Downloads\Ubuntu1604"           
    PS C:\windows\system32> $VHDFile = "ubuntu13.vhd"
    
    PS C:\windows\system32> $StorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value
    PS C:\windows\system32> $endPoint = (Get-AzStorageAccount -name $StorageAccountName -ResourceGroupName $ResourceGroupName).PrimaryEndpoints.Blob
    PS C:\windows\system32> $StorageAccountContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endpoint
    PS C:\windows\system32> $StorageAccountSAS = New-AzStorageAccountSASToken -Service Blob -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly

    PS C:\windows\system32> C:\azcopy\azcopy_windows_amd64_10.10.0\azcopy.exe cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"
    INFO: Scanning...
    INFO: Any empty folders will not be processed, because source and/or destination doesn't have full folder support
    
    Job 72a5e3dd-9210-3e43-6691-6bebd4875760 has started
    Log file is located at: C:\Users\alkohli\.azcopy\72a5e3dd-9210-3e43-6691-6bebd4875760.log
    
    INFO: azcopy.exe: A newer version 10.11.0 is available to download
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

AzCopy 10 で次のコマンドを使用します。  

```powershell
$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName <ResourceGroupName> -Name <StorageAccountName>)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name <StorageAccountName> -ResourceGroupName <ResourceGroupName>).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName <StorageAccountName> -StorageAccountKey <StorageAccountKey> -Endpoint <Endpoint>

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context <StorageAccountContext> -Protocol HttpsOnly

<AzCopy exe path> cp "Full VHD path" "<BlobEndPoint>/<ContainerName><StorageAccountSAS>"
```

出力例を次に示します。 

```output
$ContainerName = <Container name>
$ResourceGroupName = <Resource group name>
$StorageAccountName = <Storage account name>
$VHDPath = "Full VHD path"
$VHDFile = <VHD file name>

$StorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName)[0].Value

$endPoint = (Get-AzureRmStorageAccount -name $StorageAccountName -resourcegroupname $ResourceGroupName).PrimaryEndpoints.Blob

$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -Endpoint $endPoint

$StorageAccountSAS = New-AzureStorageAccountSASToken -Service Blob,File,Queue,Table -ResourceType Container,Service,Object -Permission "acdlrw" -Context $StorageAccountContext -Protocol HttpsOnly

C:\AzCopy.exe  cp "$VHDPath\$VHDFile" "$endPoint$ContainerName$StorageAccountSAS"
```
---

## <a name="create-a-managed-disk-from-the-vhd"></a>VHD からマネージド ディスクを作成する

ここでは、アップロードした VHD からマネージド ディスクを作成します。

### <a name="az"></a>[Az](#tab/az)

1. パラメーターを設定する。

    ```powershell
    $DiskName = "<Managed disk name>"
    ```

1. アップロードした VHD からマネージド ディスクを作成します。 VHD のソース URL を取得するには、Storage Explorer で VHD が格納されているストレージ アカウント内のコンテナーに移動します。 VHD を選択し、右クリックして **[プロパティ]** を選択します。 **[BLOB のプロパティ]** ダイアログで、 **[URI]** を選択します。 

    ```powershell
    $StorageAccountId = (Get-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName).Id    
    $DiskConfig = New-AzDiskConfig -Location DBELocal -StorageAccountId $StorageAccountId -CreateOption Import -SourceUri "Source URL for your VHD"
    New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $DiskConfig
    ```
    出力例を次に示します。
    
    ```output
    PS C:\WINDOWS\system32> $DiskName = "myazmd"
    PS C:\WINDOWS\system32> $StorageAccountId = (Get-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName).Id
    PS C:\WINDOWS\system32> $DiskConfig = New-AzDiskConfig -Location DBELocal -StorageAccountId $StorageAccountId -CreateOption Import -SourceUri "https://myaseazsa.blob.myasegpu.wdshcsso.com/testcontainer1/ubuntu13.vhd"
    PS C:\WINDOWS\system32> New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $DiskConfig
    
    ResourceGroupName            : myaseazrg
    ManagedBy                    :
    Sku                          : Microsoft.Azure.Management.Compute.Models.DiskSku
    Zones                        :
    TimeCreated                  : 6/24/2021 12:19:56 PM
    OsType                       :
    HyperVGeneration             :
    CreationData                 : Microsoft.Azure.Management.Compute.Models.CreationDat
                                   a
    DiskSizeGB                   : 30
    DiskSizeBytes                : 32212254720
    UniqueId                     : 53743801-cbf2-4d2f-acb4-971d037a9395
    EncryptionSettingsCollection :
    ProvisioningState            : Succeeded
    DiskIOPSReadWrite            : 500
    DiskMBpsReadWrite            : 60
    DiskState                    : Unattached
    Encryption                   : Microsoft.Azure.Management.Compute.Models.Encryption
    Id                           : /subscriptions/.../r
                                   esourceGroups/myaseazrg/providers/Microsoft.Compute/d
                                   isks/myazmd
    Name                         : myazmd
    Type                         : Microsoft.Compute/disks
    Location                     : DBELocal
    Tags                         : {}
    
    PS C:\WINDOWS\system32>
    ```
 

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
出力例を次に示します。 

```output
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import –SourceUri http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd
```
 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

出力例を次に示します。 このコマンドレットの詳細については、「[New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0&preserve-view=true)」を参照してください。

```output
Tags               : New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

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
Id                 : /subscriptions/.../resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```
---

## <a name="create-a-vm-image-from-the-managed-disk"></a>マネージド ディスクから VM イメージを作成する

次に、マネージド ディスクから VM イメージを作成します。

### <a name="az"></a>[Az](#tab/az)

1. パラメーターを設定する。

    ```powershell
    $DiskSize = "<Size greater than or equal to size of source managed disk>"
    $OsType = "<linux or windows>" 
    $ImageName = "<Image name>"
    ```
1. VM イメージを作成します。 サポートされている OS の種類は、Linux と Windows です。

    ```powershell
    $imageConfig = New-AzImageConfig -Location DBELocal
    $ManagedDiskId = (Get-AzDisk -Name $DiskName -ResourceGroupName $ResourceGroupName).Id
    Set-AzImageOsDisk -Image $imageConfig -OsType $OsType -OsState 'Generalized' -DiskSizeGB $DiskSize -ManagedDiskId $ManagedDiskId 
    New-AzImage -Image $imageConfig -ImageName $ImageName -ResourceGroupName $ResourceGroupName  
    ```  
    出力例を次に示します。 

    ```output
    PS C:\WINDOWS\system32> $OsType = "linux"
    PS C:\WINDOWS\system32> $ImageName = "myaseazlinuxvmimage"
    PS C:\WINDOWS\system32> $DiskSize = 35
    PS C:\WINDOWS\system32> $imageConfig = New-AzImageConfig -Location DBELocal
    PS C:\WINDOWS\system32> $ManagedDiskId = (Get-AzDisk -Name $DiskName -ResourceGroupName $ResourceGroupName).Id
    PS C:\WINDOWS\system32> Set-AzImageOsDisk -Image $imageConfig -OsType $OsType -OsState 'Generalized' -DiskSizeGB $DiskSize -ManagedDiskId $ManagedDiskId
    
    ResourceGroupName    :
    SourceVirtualMachine :
    StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
    ProvisioningState    :
    HyperVGeneration     : V1
    Id                   :
    Name                 :
    Type                 :
    Location             : DBELocal
    Tags                 :
    
    PS C:\WINDOWS\system32> New-AzImage -Image $imageConfig -ImageName $ImageName -ResourceGroupName $ResourceGroupName
    
    ResourceGroupName    : myaseazrg
    SourceVirtualMachine :
    StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
    ProvisioningState    : Succeeded
    HyperVGeneration     : V1
    Id                   : /subscriptions/.../resourceG
                           roups/myaseazrg/providers/Microsoft.Compute/images/myaseazlin
                           uxvmimage
    Name                 : myaseazlinuxvmimage
    Type                 : Microsoft.Compute/images
    Location             : dbelocal
    Tags                 : {}
    
    PS C:\WINDOWS\system32> 
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

次のコマンドを実行します。 *\<Disk name>* 、 *\<OS type>* 、 *\<Disk size>* を実際の値で置き換えます。

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

サポートされている OS の種類は、Linux と Windows です。

出力例を次に示します。 このコマンドレットの詳細については、「[New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0&preserve-view=true)」を参照してください。

```output
PS C:\Windows\system32> New-AzImage -Image $imageConfig -ImageName ig191113014333    -ResourceGroupName RG191113014333
ResourceGroupName    : RG191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
HyperVGeneration     : V1
Id                   : /subscriptions/.../resourceGroups/RG191113014333/providers/Microsoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```
---

## <a name="create-your-vm-with-previously-created-resources"></a>事前に作成したリソースを使用して VM を作成する

VM を作成してデプロイする前に、1 つの仮想ネットワークを作成し、仮想ネットワーク インターフェイスを関連付ける必要があります。

> [!IMPORTANT]
> 次の規則が適用されます。
> - 仮想ネットワークは、リソース グループ全体でも 1 つしか作成できません。 仮想ネットワークのアドレス空間は、論理ネットワークとまったく同じである必要があります。
> - 仮想ネットワークが所有できるサブネットは 1 つに限られています。 サブネットのアドレス空間は、仮想ネットワークとまったく同じである必要があります。
> - 仮想ネットワーク インターフェイス カードを作成するときは、静的な割り当て方法のみを使用できます。 ユーザーは、プライベート IP アドレスを指定する必要があります。<!--Confirm w/ Neeraj given we can have both static and DHCP-->

### <a name="query-the-automatically-created-virtual-network"></a>自動的に作成された仮想ネットワークのクエリを実行する

デバイスのローカル UI からコンピューティングを有効にすると、`ASEVNET` という仮想ネットワークが `ASERG` リソース グループの下に自動的に作成されます。 

### <a name="az"></a>[Az](#tab/az)

次のコマンドを使用して、既存の仮想ネットワークのクエリを実行します。

```powershell
$ArmVn = Get-AzVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

次のコマンドを使用して、既存の仮想ネットワークのクエリを実行します。

```powershell
$aRmVN = Get-AzureRMVirtualNetwork -Name ASEVNET -ResourceGroupName ASERG 
```
---

### <a name="create-a-virtual-network-interface-card"></a>仮想ネットワーク インターフェイス カードの作成

仮想ネットワークのサブネット ID を使用して、仮想ネットワーク インターフェイス カードを作成します。

### <a name="az"></a>[Az](#tab/az)

1. パラメーターを設定する。

    ```powershell
    $IpConfigName = "<IP config name>"
    $NicName = "<Network interface name>"
    ```

1. 仮想ネットワーク インターフェイスを作成します。

    ```powershell
    $ipConfig = New-AzNetworkInterfaceIpConfig -Name $IpConfigName -SubnetId $aRmVN.Subnets[0].Id 
    $Nic = New-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName -Location DBELocal -IpConfiguration $IpConfig    
    ```

    既定では、IP は、コンピューティング対応のネットワークからネットワーク インターフェイスに動的に割り当てられます。 ネットワーク インターフェイスに静的 IP を割り当てる場合は、`-PrivateIpAddress parameter` を使用します。         

    出力例を次に示します。
    
    ```output
    PS C:\WINDOWS\system32> $IpConfigName = "myazipconfig1"
    PS C:\WINDOWS\system32> $NicName = "myaznic1"
    PS C:\WINDOWS\system32> $ipConfig = New-AzNetworkInterfaceIpConfig -Name $IpConfigName -SubnetId $aRmVN.Subnets[0].Id 
    PS C:\WINDOWS\system32> $ipConfig = New-AzNetworkInterfaceIpConfig -Name $IpConfigName -SubnetId $aRmVN.Subnets[0].Id
    PS C:\WINDOWS\system32> $Nic = New-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName -Location DBELocal -IpConfiguration $IpConfig
    PS C:\WINDOWS\system32> $Nic
        
    Name                        : myaznic1
    ResourceGroupName           : myaseazrg
    Location                    : dbelocal
    Id                          : /subscriptions/.../re
                                  sourceGroups/myaseazrg/providers/Microsoft.Network/net
                                  workInterfaces/myaznic1
    Etag                        : W/"0b20057b-2102-4f34-958b-656327c0fb1d"
    ResourceGuid                : e7d4131f-6f01-4492-9d4c-a8ff1af7244f
    ProvisioningState           : Succeeded
    Tags                        :
    VirtualMachine              : null
    IpConfigurations            : [
                                    {
                                      "Name": "myazipconfig1",
                                      "Etag":
                                  "W/\"0b20057b-2102-4f34-958b-656327c0fb1d\"",
                                      "Id": "/subscriptions/.../resourceGroups/myaseazrg/providers/Microsoft.
                                  Network/networkInterfaces/myaznic1/ipConfigurations/my
                                  azipconfig1",
                                      "PrivateIpAddress": "10.126.76.60",
                                      "PrivateIpAllocationMethod": "Dynamic",
                                      "Subnet": {
                                        "Delegations": [],
                                        "Id": "/subscriptions/.../resourceGroups/ASERG/providers/Microsoft.Ne
                                  twork/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                        "ServiceAssociationLinks": []
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
                                    "AppliedDnsServers": [],
                                    "InternalDomainNameSuffix": "auwlfcx0dhxurjgisct43fc
                                  ywb.a--x.internal.cloudapp.net"
                                  }
    EnableIPForwarding          : False
    EnableAcceleratedNetworking : False
    NetworkSecurityGroup        : null
    Primary                     :
    MacAddress                  : 001DD84A58D1
           
    PS C:\WINDOWS\system32>
    ```

必要に応じて、VM の仮想ネットワーク インターフェイス カードを作成するときに、パブリック IP を渡すことができます。 このインスタンスでは、パブリック IP によってプライベート IP が返されます。 

```powershell
New-AzPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

既定では、IP は、コンピューティング対応のネットワークからネットワーク インターフェイスに動的に割り当てられます。 ネットワーク インターフェイスに静的 IP を割り当てる場合は、`-PrivateIpAddress parameter` を使用します。 

出力例を次に示します。

```output
PS C:\windows\system32> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\windows\system32> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\windows\system32> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\windows\system32> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\windows\system32> $Nic

PS C:\windows\system32> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/.../resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/.../resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/.../resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/.../resourceGroups/DbeSystemRG/providers/Microsoft.Netw
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

必要に応じて、VM の仮想ネットワーク インターフェイス カードを作成するときに、パブリック IP を渡すことができます。 このインスタンスでは、パブリック IP によってプライベート IP が返されます。 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```
---

### <a name="create-a-vm"></a>VM の作成

これで、VM イメージを使用して VM を作成し、先ほど作成した仮想ネットワークに接続できるようになりました。

### <a name="az"></a>[Az](#tab/az)

1. 作成する VM にサインインするためのユーザー名とパスワードを設定します。

    ```powershell
    $pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
    $cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)
    ```
    VM を作成して電源を入れたら、前のユーザー名とパスワードを使用してサインインします。

1. パラメーターを設定します。

    ```powershell
    $VmName = "<VM name>"
    $ComputerName = "<VM display name>"
    $OsDiskName = "<OS disk name>"
    ```
1. VM を作成します。

    ```powershell
    $VirtualMachine =  New-AzVMConfig -VmName $VmName -VMSize "Standard_D1_v2"
 
    $VirtualMachine =  Set-AzVMOperatingSystem -VM $VirtualMachine -Linux -ComputerName $ComputerName -Credential $cred
     
    $VirtualMachine =  Set-AzVmOsDisk -VM $VirtualMachine -Name $OsDiskName -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType Standard_LRS
     
    $nicID = (Get-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName).Id
     
    $VirtualMachine =  Add-AzVMNetworkInterface -Vm $VirtualMachine -Id $nicID
     
    $image = ( Get-AzImage -ResourceGroupName $ResourceGroupName -ImageName $ImageName).Id
     
    $VirtualMachine =  Set-AzVMSourceImage -VM $VirtualMachine -Id $image
     
    New-AzVM -ResourceGroupName $ResourceGroupName -Location DBELocal -VM $VirtualMachine -Verbose
    ```

    出力例を次に示します。
    
    ```powershell
    PS C:\WINDOWS\system32> $pass = ConvertTo-SecureString "Password1" -AsPlainText -Force;
    PS C:\WINDOWS\system32> $cred = New-Object System.Management.Automation.PSCredential("myazuser", $pass)
    PS C:\WINDOWS\system32> $VmName = "myazvm"
    >> $ComputerName = "myazvmfriendlyname"
    >> $OsDiskName = "myazosdisk1"
    PS C:\WINDOWS\system32> $VirtualMachine =  New-AzVMConfig -VmName $VmName -VMSize "Standard_D1_v2"
    PS C:\WINDOWS\system32> $VirtualMachine =  Set-AzVMOperatingSystem -VM $VirtualMachine -Linux -ComputerName $ComputerName -Credential $cred
    PS C:\WINDOWS\system32> $VirtualMachine =  Set-AzVmOsDisk -VM $VirtualMachine -Name $OsDiskName -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType Standard_LRS
    PS C:\WINDOWS\system32> $nicID = (Get-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName).Id
    PS C:\WINDOWS\system32> $nicID/subscriptions/.../resourceGroups/myaseazrg/providers/Microsoft.Network/networkInterfaces/myaznic1
    PS C:\WINDOWS\system32> $VirtualMachine =  Add-AzVMNetworkInterface -VM $VirtualMachine -Id $nicID
    PS C:\WINDOWS\system32> $image = ( Get-AzImage -ResourceGroupName $ResourceGroupName -ImageName $ImageName).Id
    PS C:\WINDOWS\system32> $VirtualMachine =  Set-AzVMSourceImage -VM $VirtualMachine -Id $image
    PS C:\WINDOWS\system32> New-AzVM -ResourceGroupName $ResourceGroupName -Location DBELocal -VM $VirtualMachine -Verbose
    WARNING: Since the VM is created using premium storage or managed disk, existing
    standard storage account, myaseazsa, is used for boot diagnostics.
    VERBOSE: Performing the operation "New" on target "myazvm".
    
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK
    ```
1. 作成した VM に割り当てられている IP を確認するには、作成した仮想ネットワーク インターフェイスのクエリを実行します。 `PrivateIPAddress` を見つけて、VM の IP をコピーします。 出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> $Nic

    Name                        : myaznic1
    ResourceGroupName           : myaseazrg
    Location                    : dbelocal
    Id                          : /subscriptions/.../re
                                  sourceGroups/myaseazrg/providers/Microsoft.Network/net
                                  workInterfaces/myaznic1
    Etag                        : W/"0b20057b-2102-4f34-958b-656327c0fb1d"
    ResourceGuid                : e7d4131f-6f01-4492-9d4c-a8ff1af7244f
    ProvisioningState           : Succeeded
    Tags                        :
    VirtualMachine              : null
    IpConfigurations            : [
                                    {
                                      "Name": "myazipconfig1",
                                      "Etag":
                                  "W/\"0b20057b-2102-4f34-958b-656327c0fb1d\"",
                                      "Id": "/subscriptions/.../resourceGroups/myaseazrg/providers/Microsoft.
                                  Network/networkInterfaces/myaznic1/ipConfigurations/my
                                  azipconfig1",
                                      "PrivateIpAddress": "10.126.76.60",
                                      "PrivateIpAllocationMethod": "Dynamic",
                                      "Subnet": {
                                        "Delegations": [],
                                        "Id": "/subscriptions/.../resourceGroups/ASERG/providers/Microsoft.Ne
                                  twork/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                        "ServiceAssociationLinks": []
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
                                    "AppliedDnsServers": [],
                                    "InternalDomainNameSuffix": "auwlfcx0dhxurjgisct43fc
                                  ywb.a--x.internal.cloudapp.net"
                                  }
    EnableIPForwarding          : False
    EnableAcceleratedNetworking : False
    NetworkSecurityGroup        : null
    Primary                     :
    MacAddress                  : 001DD84A58D1
      
    PS C:\WINDOWS\system32>
    ```


### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)
```

VM を作成して電源を入れたら、次のユーザー名とパスワードを使用してサインインします。

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```
---

## <a name="connect-to-the-vm"></a>VM に接続します

Windows VM と Linux VM のどちらを作成したかによって、接続手順が異なる場合があります。

## <a name="connect-to-a-linux-vm"></a>Linux VM に接続する

Linux VM に接続する場合は、次の手順に従います。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

VM の作成時にパブリック IP アドレスを使用した場合は、その IP を使用して VM に接続できます。 パブリック IP を取得するには、次のコマンドを実行します。 

### <a name="az"></a>[Az](#tab/az)

```powershell
$publicIp = Get-AzPublicIpAddress -Name $PublicIp -ResourceGroupName $ResourceGroupName
```
この場合、パブリック IP は仮想ネットワーク インターフェイスの作成時に渡したプライベート IP と同じになります。
  
### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
この場合、パブリック IP は仮想ネットワーク インターフェイスの作成時に渡したプライベート IP と同じになります。

---

## <a name="connect-to-a-windows-vm"></a>Windows VM に接続する

Windows VM に接続する場合は、次の手順に従います。

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]


## <a name="manage-the-vm"></a>VM の管理

次のセクションでは、Azure Stack Edge Pro デバイス上に作成できる一般的ないくつかの操作について説明します。

### <a name="list-vms-that-are-running-on-the-device"></a>デバイスで実行されている VM を一覧表示する

Azure Stack Edge デバイスで実行されているすべての VM の一覧を取得するには、次のコマンドを実行します。

### <a name="az"></a>[Az](#tab/az)

```powershell
Get-AzVM -ResourceGroupName <String> -Name <String>
```

このコマンドレットの詳細については、「[Get-AzVM](/powershell/module/az.compute/get-azvm?view=azps-6.1.0&preserve-view=true)」を参照してください。

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Get-AzureRmVM -ResourceGroupName <String> -Name <String>
```

---


### <a name="turn-on-the-vm"></a>VM をオンにする

デバイスで実行されている仮想マシンをオンにするには、次のコマンドレットを実行します。

### <a name="az"></a>[Az](#tab/az)

```powershell
Start-AzVM [-Name] <String> [-ResourceGroupName] <String>
```
このコマンドレットの詳細については、「[Start-AzVM](/powershell/module/az.compute/start-azvm?view=azps-5.9.0&preserve-view=true)」を参照してください。

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

このコマンドレットの詳細については、「[Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0&preserve-view=true)」を参照してください。

---

### <a name="suspend-or-shut-down-the-vm"></a>VM を中断またはシャットダウンする

デバイスで実行されている仮想マシンを停止またはシャットダウンするには、次のコマンドレットを実行します。

### <a name="az"></a>[Az](#tab/az)

```powershell
Stop-AzVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```

このコマンドレットの詳細については、[Stop-AzVM コマンドレット](/powershell/module/az.compute/stop-azvm?view=azps-5.9.0&preserve-view=true)に関するページを参照してください。

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```

このコマンドレットの詳細については、[Start-AzureRmVM コマンドレット](/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0&preserve-view=true)に関するページを参照してください。

---

### <a name="add-a-data-disk"></a>データ ディスクの追加

VM のワークロード要件が増加した場合は、データ ディスクの追加が必要になることがあります。 これを行うには、次のコマンドを実行します。

### <a name="az"></a>[Az](#tab/az)

```powershell
Add-AzRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```
---



### <a name="delete-the-vm"></a>VM の削除

デバイスから仮想マシンを削除するには、次のコマンドレットを実行します。

### <a name="az"></a>[Az](#tab/az)

```powershell
Remove-AzVM [-Name] <String> [-ResourceGroupName] <String>
```
このコマンドレットの詳細については、[Remove-AzVm コマンドレット](/powershell/module/az.compute/remove-azvm?view=azps-5.9.0&preserve-view=true)に関するページを参照してください。

### <a name="azurerm"></a>[AzureRM](#tab/azure-rm)

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```
このコマンドレットの詳細については、[Remove-AzureRmVm コマンドレット](/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0&preserve-view=true)に関するページを参照してください。

---

## <a name="next-steps"></a>次のステップ

[Azure Resource Manager コマンドレット](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
