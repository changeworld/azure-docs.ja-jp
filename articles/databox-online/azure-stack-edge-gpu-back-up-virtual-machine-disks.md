---
title: PowerShell を使用して Azure Stack Edge Pro GPU デバイス上の VM ディスクをバックアップする
description: Azure Stack Edge Pro GPU デバイスで実行されている仮想マシン ディスク上のデータをバックアップする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/25/2021
ms.author: alkohli
ms.openlocfilehash: 93d12db01c71d078ab25cf0745d5ae661220153c
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/30/2021
ms.locfileid: "113106583"
---
# <a name="back-up-vm-disks-on-azure-stack-edge-pro-gpu-via-azure-powershell"></a>Azure PowerShell を使用して Azure Stack Edge Pro GPU の VM ディスクをバックアップする

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure PowerShell を使用して、Azure Stack Edge Pro GPU デバイス上の仮想マシン ディスクのバックアップを作成する方法について説明します。

> [!IMPORTANT]
> この手順は、停止している VM に使用することを目的としています。 実行中の VM をバックアップする場合は、サードパーティ製のバックアップ ツールを使用することをお勧めします。

## <a name="workflow"></a>ワークフロー

次の手順では、デバイス上の VM ディスクをバックアップするためのワークフローの概要を示します。

1. VM を停止します。
1. VM ディスクのスナップショットを作成します。
1. そのスナップショットを VHD としてローカル ストレージ アカウントにコピーします。
1. その VHD を外部ターゲットにアップロードします。

## <a name="prerequisites"></a>前提条件

VM をバックアップする前に、次のことを確認してください。

- デバイスへの接続に使用するクライアントにアクセスできる。
    - クライアントで[サポート対象の OS](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) が実行されている。
    - クライアントは、[デバイスの Azure Resource Manager への接続](azure-stack-edge-gpu-connect-resource-manager.md)の手順に従って、デバイスのローカル Azure Resource Manager に接続するよう構成されている。

## <a name="verify-connection-to-local-azure-resource-manager"></a>ローカル Azure Resource Manager への接続を確認する

[!INCLUDE [azure-stack-edge-gateway-verify-azure-resource-manager-connection](../../includes/azure-stack-edge-gateway-verify-azure-resource-manager-connection.md)]

## <a name="back-up-a-vm-disk"></a>VM ディスクをバックアップする

### <a name="az"></a>[Az](#tab/az)

1. デバイスで実行されている VM の一覧を取得します。 停止する VM と、対応するリソース グループを識別します。

    ```powershell
    Get-AzVM
    ```

    出力例を次に示します。

    ```powershell
    PS C:\Users\user> Get-AzVM
    
    ResourceGroupName            Name Location         VmSize OsType                NIC
    -----------------            ---- --------         ------ ------                ---
    MYASEAZRG                  myazvm dbelocal Standard_D1_v2  Linux           myaznic1
    MYASERG           myasewindowsvm2 dbelocal Standard_D1_v2  Linux myasewindowsvm2nic
    
    PS C:\Users\user> 
    ```

1. パラメーターを設定する。

    ```powershell
    $ResourceGroupName = "<Resource group name>"
    $VmName = "<VM name>"
    ```   
1. VM を停止します。

    ```powershell
    Stop-AzVM –ResourceGroupName $ResourceGroupName -Name $VmName
    ```

    出力例を次に示します。

    ```powershell
    PS C:\Users\user> Stop-AzVM -ResourceGroupName myaserg -Name myasewindowsvm2      
    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
        
    OperationId : 8a2fa7ea-99d0-4f9f-b8ca-e37389cd8413
    Status      : Succeeded
    StartTime   : 6/28/2021 11:51:33 AM
    EndTime     : 6/28/2021 11:51:50 AM
    Error       :
    
    PS C:\Users\user>
    ```

    Azure portal から VM を停止することもできます。
 

2. VM ディスクのスナップショットを作成し、そのスナップショットをローカル リソース グループに保存します。 この手順は、OS とデータ ディスクの両方で使用できます。

   1. デバイス上、または特定のリソース グループ内のディスクの一覧を取得します。 バックアップするディスクの名前をメモしておきます。

        ```powershell
        $Disk = Get-AzDisk -ResourceGroupName $ResourceGroupName
        $Disk.Name
        ```   
        出力例を次に示します。

        ```output
        PS C:\Users\user> $Disk = Get-AzDisk -ResourceGroupName myaserg
        PS C:\Users\user> $Disk.Name
        myasewindowsvm2_disk1_2a066432056446669368969835d5e3b3
        myazdisk1
        myvmdisk2
        PS C:\Users\user>
        ```
   1. VM スナップショットのターゲットとして機能するローカル リソース グループを作成します。 場所は `dbelocal` と設定します。

        ```powershell
        New-AzResourceGroup -ResourceGroupName <Resource group name> -Location dbelocal 
        ``` 

        ```output
        PS C:\Users\user> New-AzResourceGroup -ResourceGroupName myaseazrg1 -Location dbelocal
        
        ResourceGroupName : myaseazrg1
        Location          : dbelocal
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/.../resourceGroups/myaseazrg1
        
        PS C:\Users\user>
        ```

   1. パラメーターを設定する。

      ```powershell
      $DiskResourceGroup = <Disk resource group>
      $DiskName = <Disk name>
      $SnapshotName = <Snapshot name>
      $DestinationRG = <Snapshot destination resource group>
      ```

   3. スナップショットの構成を設定し、スナップショットを作成します。

        ```powershell
        $Disk = Get-AzDisk -ResourceGroupName $DiskResourceGroup -DiskName $DiskName
        $SnapshotConfig = New-AzSnapshotConfig -SourceUri $Disk.Id -CreateOption Copy -Location 'dbelocal'
        $Snapshot = New-AzSnapshot -Snapshot $SnapshotConfig -SnapshotName $SnapshotName -ResourceGroupName $DestinationRG
        ```
        スナップショットがターゲット リソース グループに作成されていることを確認します。

        ```powershell
        Get-AzSnapshot -ResourceGroupName $DestinationRG
        ```
        出力例を次に示します。

        ```output
        PS C:\Users\user> $DiskResourceGroup = "myaserg"
        PS C:\Users\user> $DiskName = "myazdisk1"
        PS C:\Users\user> $SnapshotName = "myasdisk1ss"
        PS C:\Users\user> $DestinationRG = "myaseazrg1"
        PS C:\Users\user> $Disk = Get-AzDisk -ResourceGroupName $DiskResourceGroup -DiskName $DiskName
        PS C:\Users\user> $SnapshotConfig = New-AzSnapshotConfig -SourceUri $Disk.Id -CreateOption Copy -Location 'dbelocal'
        PS C:\Users\user> $Snapshot=New-AzSnapshot -Snapshot $SnapshotConfig -SnapshotName $SnapshotName -ResourceGroupName $DestinationRG
        PS C:\Users\user> Get-AzSnapshot -ResourceGroupName $DestinationRG
                
        ResourceGroupName            : myaseazrg1
        ManagedBy                    :
        Sku                          : Microsoft.Azure.Management.Compute.Models.SnapshotSku
        TimeCreated                  : 6/28/2021 6:57:40 PM
        OsType                       :
        HyperVGeneration             :
        CreationData                 : Microsoft.Azure.Management.Compute.Models.CreationDat
                                       a
        DiskSizeGB                   : 10
        DiskSizeBytes                : 10737418240
        UniqueId                     : fbc1cfac-8bbb-44d8-8aa4-9e8811950fcc
        EncryptionSettingsCollection :
        ProvisioningState            : Succeeded
        Incremental                  : False
        Encryption                   : Microsoft.Azure.Management.Compute.Models.Encryption
        Id                           : /subscriptions/.../r
                                       esourceGroups/myaseazrg1/providers/Microsoft.Compute/
                                       snapshots/myasdisk1ss
        Name                         : myasdisk1ss
        Type                         : Microsoft.Compute/snapshots
        Location                     : DBELocal
        Tags                         : {}
                
        PS C:\Users\user>
        ```

### <a name="azurerm"></a>[AzureRM](#tab/azurerm)

1. デバイスで実行されている VM の一覧を取得します。 停止する VM を特定します。

    ```powershell
    Get-AzureRMVM
    ```

    出力例を次に示します。

    ```powershell
    PS C:\Users\user> Get-AzureRMVM

    ResourceGroupName    Name         Location  VmSize         OsType   NIC         ProvisioningState Zone
    -----------------    ----         --------  ------         ------   ---         ----------------- ----
    MYASERG           myasewindowsvm1 dbelocal Standard_D1_v2  Linux myasewindowsvm1nic  Succeeded
    MYASERG1            myaselinuxvm1 dbelocal Standard_D1_v2  Linux   myaselinuxvm1nic  Succeeded
    MYASERG2             myasetestvm1 dbelocal Standard_D1_v2  Linux    myasetestvm1nic  Succeeded

    PS C:\Users\user>
    ```
    
1. VM を停止します。

    ```powershell
    Stop-AzureRMVM –ResourceGroupName <Resource group name> -Name <VM name>
    ```

    出力例を次に示します。

    ```powershell
    PS C:\Users\user> Stop-AzureRMVM -ResourceGroupName myaserg2 -Name myasetestvm1

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
    
    OperationId :
    Status      : Succeeded
    StartTime   : 4/9/2021 8:43:47 AM
    EndTime     : 4/9/2021 8:44:27 AM
    Error       :
    
    PS C:\Users\user>
    ```
    Azure portal から VM を停止することもできます。
 

2. VM ディスクのスナップショットを作成し、そのスナップショットをローカル リソース グループに保存します。 この手順は、OS とデータ ディスクの両方で使用できます。

   1. デバイス上、または特定のリソース グループ内のディスクの一覧を取得します。 バックアップするディスクの名前をメモしておきます。

        ```powershell
        Get-AzureRMDisk -ResourceGroupName <Resource group name>
        ```   
        出力例を次に示します。

        ```powershell
        PS C:\Users\user> $Disk = Get-AzureRMDisk -ResourceGroupName myaserg2
        PS C:\Users\user> $Disk.Name
        myasetestdisk1
        myasetestvm1_disk1_0ed91809927f4023b7aceb6eeca51c05
        PS C:\Users\user>
        ```
   1. VM スナップショットのターゲットとして機能するローカル リソース グループを作成します。

        ```powershell
        PS C:\Users\user> New-AzureRmResourceGroup -ResourceGroupName myaserg3 -Location dbelocal
    
        ResourceGroupName : myaserg3
        Location          : dbelocal
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/.../resourceGroups/myaserg3
        
        PS C:\Users\user>
        ```

   1. パラメーターを設定する。

      ```powershell
      $DiskResourceGroup = <Disk resource group>
      $DiskName = <Disk name>
      $SnapshotName = <Snapshot name>
      $DestinationRG = <Snapshot destination resource group>
      ```

   3. スナップショットの構成を設定し、スナップショットを作成します。

        ```powershell
        $Disk = Get-AzureRmDisk -ResourceGroupName $DiskResourceGroup -DiskName $DiskName
        $SnapshotConfig = New-AzureRmSnapshotConfig -SourceUri $Disk.Id -CreateOption Copy -Location 'dbelocal'
        $Snapshot = New-AzureRmSnapshot -Snapshot $SnapshotConfig -SnapshotName $SnapshotName -ResourceGroupName $DestinationRG
        ```
        スナップショットがターゲット リソース グループに作成されていることを確認します。

        ```powershell
        Get-AzureRMSnapshot -ResourceGroupName $DestinationRG
        ```
        出力例を次に示します。

        ```powershell
        PS C:\Users\user> $DiskResourceGroup = "myaserg2"
        PS C:\Users\user> $DiskName = "myasetestdisk1"
        PS C:\Users\user> $SnapshotName = "myasetestdisk1_ss"
        PS C:\Users\user> $DestinationRG = "myaserg3"
        PS C:\Users\user> $Disk = Get-AzureRmDisk -ResourceGroupName $DiskResourceGroup -DiskName $DiskName
        PS C:\Users\user> $SnapshotConfig = New-AzureRmSnapshotConfig -SourceUri $Disk.Id -CreateOption Copy -Location 'dbelocal'
        PS C:\Users\user> $Snapshot=New-AzureRmSnapshot -Snapshot $SnapshotConfig -SnapshotName $SnapshotName -ResourceGroupName $DestinationRG
        PS C:\Users\user> Get-AzureRMSnapshot -ResourceGroupName $DestinationRG
        
        ResourceGroupName  : myaserg3
        ManagedBy          :
        Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
        TimeCreated        : 4/9/2021 4:23:21 PM
        OsType             :
        CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
        DiskSizeGB         : 10
        EncryptionSettings :
        ProvisioningState  : Succeeded
        Id                 : /subscriptions/.../resourceGroups/myaserg3/providers/Microsoft.Compute/snapshots/myasetestdisk1_ss
        Name               : myasetestdisk1_ss
        Type               : Microsoft.Compute/snapshots
        Location           : DBELocal
        Tags               : {}
        
        PS C:\Users\user>
        ```
---

## <a name="copy-the-snapshot-into-a-local-storage-account"></a>ローカル ストレージ アカウントにスナップショットをコピーする

デバイス上のローカル ストレージ アカウントにスナップショットをコピーします。 

### <a name="az"></a>[Az](#tab/az)

1. パラメーターを設定する。 

    ```powershell
    $StorageAccountRG = <Local storage account resource group>
    $StorageAccountName = <Storage account name>
    $StorageEndpointSuffix = <Connection string in format: DeviceName.DnsDomain.com>
    $DestStorageContainer = <Destination storage container>
    $DestFileName = <Blob file name> 
    ```

1. デバイス上にローカル ストレージ アカウントを作成します。 

    ```powershell
    New-AzStorageAccount -Name  $StorageAccountName -ResourceGroupName $StorageAccountRG -Location DBELocal -SkuName Standard_LRS
    ```

    出力例を次に示します。 

    ```output
    PS C:\Users\user> New-AzStorageAccount -Name  $StorageAccountName -ResourceGroupName $StorageAccountRG -Location DBELocal -SkuName Standard_LRS
    
    StorageAccountName ResourceGroupName PrimaryLocation SkuName      Kind    AccessTier
    ------------------ ----------------- --------------- -------      ----    ----------
    myaseazsa1         myaseazrg2        DBELocal        Standard_LRS Storage
    PS C:\Users\user>
    ```

1. 作成したローカル ストレージ アカウントにコンテナーを作成します。 

    ```powershell
    $keys = Get-AzStorageAccountKey -ResourceGroupName $StorageAccountRG -Name $StorageAccountName
    $keyValue = $keys[0].Value
    $storageContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $keyValue -Protocol Http -Endpoint $StorageEndpointSuffix;
    $container = New-AzStorageContainer -Name $DestStorageContainer -Context $storageContext -Permission Container -ErrorAction Ignore;    
    ```
    出力例を次に示します。

    ```output
    PS C:\Users\user> $StorageAccountRG = "myaseazrg2"
    PS C:\Users\user> $StorageAccountName = "myaseazsa1"
    PS C:\Users\user> $StorageEndpointSuffix = "myasegpu.wdshcsso.com"
    PS C:\Users\user> $DestStorageContainer = "testcont1"
    PS C:\Users\user> $DestFileName = "testfile1"

    PS C:\Users\user> $keys = Get-AzStorageAccountKey -ResourceGroupName $StorageAccountRG -Name $StorageAccountName
    PS C:\Users\user> $keyValue = $keys[0].Value
    PS C:\Users\user> $storageContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $keyValue -Protocol Http -Endpoint $StorageEndpointSuffix;                                                                                   
    PS C:\Users\user> $storagecontext
    
    StorageAccountName  : myaseazsa1
    BlobEndPoint        : http://myaseazsa1.blob.myasegpu.wdshcsso.com/
    TableEndPoint       : http://myaseazsa1.table.myasegpu.wdshcsso.com/
    QueueEndPoint       : http://myaseazsa1.queue.myasegpu.wdshcsso.com/
    FileEndPoint        : http://myaseazsa1.file.myasegpu.wdshcsso.com/
    Context             : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
    Name                :
    StorageAccount      : BlobEndpoint=http://myaseazsa1.blob.myasegpu.wdshcsso.com/;Que
                          ueEndpoint=http://myaseazsa1.queue.myasegpu.wdshcsso.com/;Tabl
                          eEndpoint=http://myaseazsa1.table.myasegpu.wdshcsso.com/;FileE
                          ndpoint=http://myaseazsa1.file.myasegpu.wdshcsso.com/;AccountN
                          ame=myaseazsa1;AccountKey=[key hidden]
    TableStorageAccount : BlobEndpoint=http://myaseazsa1.blob.myasegpu.wdshcsso.com/;Que
                          ueEndpoint=http://myaseazsa1.queue.myasegpu.wdshcsso.com/;Tabl
                          eEndpoint=http://myaseazsa1.table.myasegpu.wdshcsso.com/;FileE
                          ndpoint=http://myaseazsa1.file.myasegpu.wdshcsso.com/;DefaultE
                          ndpointsProtocol=https;AccountName=myaseazsa1;AccountKey=[key
                          hidden]
    Track2OauthToken    :
    EndPointSuffix      : myasegpu.wdshcsso.com/
    ConnectionString    : BlobEndpoint=http://myaseazsa1.blob.myasegpu.wdshcsso.com/;Que
                          ueEndpoint=http://myaseazsa1.queue.myasegpu.wdshcsso.com/;Tabl
                          eEndpoint=http://myaseazsa1.table.myasegpu.wdshcsso.com/;FileE
                          ndpoint=http://myaseazsa1.file.myasegpu.wdshcsso.com/;AccountN
                          ame=myaseazsa1;AccountKey=itOn5Awjh3hnoGKL7EDQ681zhIKG/szCt05Z
                          IWAxP/T22gwEXb5l0sKjI833Hqpc0MsBiSH2rM6NuuwnJyEO1Q==
    ExtendedProperties  : {}
    
    
    
    PS C:\Users\user> $container = New-AzStorageContainer -Name $DestStorageContainer -Context $storageContext -Permission Container -ErrorAction Ignore;
    PS C:\Users\user> $container
    Blob End Point: http://myaseazsa1.blob.myasegpu.wdshcsso.com/
    
    Name                 PublicAccess         LastModified
    ----                 ------------         ------------
    testcont1           Container            6/28/2021 2:46:03 PM +00:00
    
    PS C:\Users\user>
    ```    

    また、Azure Storage Explorer を使用して[ローカル ストレージ アカウントを作成](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#create-a-storage-account)してから、デバイスの[ローカル ストレージ アカウントにコンテナーを作成](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload)することもできます。 



1. ローカル ストレージ アカウントにスナップショットをダウンロードします。

   ```powershell
   $sassnapshot = Grant-AzSnapshotAccess -ResourceGroupName $DestinationRG -SnapshotName $SnapshotName -Access 'Read' -DurationInSecond 3600
   $destContext = New-AzStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $keyValue 
   Start-AzStorageBlobCopy -AbsoluteUri $sassnapshot.AccessSAS -DestContainer $DestStorageContainer -DestContext $destContext -DestBlob $DestFileName
   ```

    出力例を次に示します。

    ```output
    PS C:\Users\user> $sassnapshot
    
    AccessSAS : https://md-2.blob.myasegpu.wdshcsso.com/22615edc48654bb8b77e383d3a7649ac
    /abcd.vhd?sv=2017-04-17&sr=b&si=43ca8395-6942-496b-92d7-f0d6dc68ab63&sk=system-1&sig
    =K%2Bc34uq7%2BLcTetG%2Bj9loOH440e03vDkD24Ug0Gf%2Bex8%3D
       
    PS C:\Users\user> $destContext = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $keyValue
    PS C:\Users\user> $destContext
    
    
    StorageAccountName  : myaseazsa1
    BlobEndPoint        : https://myaseazsa1.blob.myasegpu.wdshcsso.com/
    TableEndPoint       : https://myaseazsa1.table.myasegpu.wdshcsso.com/
    QueueEndPoint       : https://myaseazsa1.queue.myasegpu.wdshcsso.com/
    FileEndPoint        : https://myaseazsa1.file.myasegpu.wdshcsso.com/
    Context             : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
    Name                :
    StorageAccount      : BlobEndpoint=https://myaseazsa1.blob.myasegpu.wdshcsso.com/;Qu
                          eueEndpoint=https://myaseazsa1.queue.myasegpu.wdshcsso.com/;Ta
                          bleEndpoint=https://myaseazsa1.table.myasegpu.wdshcsso.com/;Fi
                          leEndpoint=https://myaseazsa1.file.myasegpu.wdshcsso.com/;Acco
                          untName=myaseazsa1;AccountKey=[key hidden]
    TableStorageAccount : BlobEndpoint=https://myaseazsa1.blob.myasegpu.wdshcsso.com/;Qu
                          eueEndpoint=https://myaseazsa1.queue.myasegpu.wdshcsso.com/;Ta
                          bleEndpoint=https://myaseazsa1.table.myasegpu.wdshcsso.com/;Fi
                          leEndpoint=https://myaseazsa1.file.myasegpu.wdshcsso.com/;Defa
                          ultEndpointsProtocol=https;AccountName=myaseazsa1;AccountKey=[
                          key hidden]
    Track2OauthToken    :
    EndPointSuffix      : myasegpu.wdshcsso.com/
    ConnectionString    : BlobEndpoint=https://myaseazsa1.blob.myasegpu.wdshcsso.com/;Qu
                          eueEndpoint=https://myaseazsa1.queue.myasegpu.wdshcsso.com/;Ta
                          bleEndpoint=https://myaseazsa1.table.myasegpu.wdshcsso.com/;Fi
                          leEndpoint=https://myaseazsa1.file.myasegpu.wdshcsso.com/;Acco
                          untName=myaseazsa1;AccountKey=itOn5Awjh3hnoGKL7EDQ681zhIKG/szC
                          t05ZIWAxP/T22gwEXb5l0sKjI833Hqpc0MsBiSH2rM6NuuwnJyEO1Q==
    ExtendedProperties  : {}
            
    PS C:\Users\user> Start-AzStorageBlobCopy -AbsoluteUri $sassnapshot.AccessSAS -DestContainer $DestStorageContainer -DestContext $destContext -DestBlob $DestFileName    
    
       AccountName: myaseazsa1, ContainerName: testcont1
    
    Name                 BlobType  Length          ContentType                    LastMo
                                                                                  dified
    ----                 --------  ------          -----------                    ------
    testfile1            BlockBlob -1                                             202...
       
    PS C:\Users\user>
    ```

    また、Storage Explorer を使用して、スナップショットがストレージ アカウントに正しくコピーされたことを確認することもできます。

    ![ローカル ストレージ アカウントのコンテナー内のバックアップを示す Storage Explorer](media/azure-stack-edge-gpu-back-up-virtual-machine-disks/back-up-virtual-machine-disk-2.png)

### <a name="azurerm"></a>[AzureRM](#tab/azurerm)

デバイス上のローカル ストレージ アカウントにスナップショットをコピーします。 

1. パラメーターを設定する。 

    ```powershell
    $StorageAccountRG = <Local storage account resource group>
    $StorageAccountName = <Storage account name>
    $StorageEndpointSuffix = <Connection string in format: DeviceName.DnsDomain.com>
    $DestStorageContainer = <Destination storage container>
    $DestFileName = <Blob file name> 
    ```

1. デバイス上にローカル ストレージ アカウントを作成します。 

    ```powershell
    New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Storage account resource group> -Location DBELocal -SkuName Standard_LRS
    ```

    出力例を次に示します。 

    ```output
    PS C:\Users\user> New-AzureRmStorageAccount -Name myasesa4 -ResourceGroupName myaserg4 -Location DBELocal -SkuName Standard_LRS
    StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime        ProvisioningState EnableHttpsTrafficOnly
    ------------------ ----------------- -------- -------     ----    ---------- ------------        ----------------- ---------------
    myasesa4           myaserg4          DBELocal StandardLRS Storage            4/9/2021 6:02:56 PM Succeeded         False
    
    PS C:\Users\user>
    ```

1. 作成したローカル ストレージ アカウントにコンテナーを作成します。 

    ```powershell
    $keys = Get-AzureRmStorageAccountKey -ResourceGroupName $StorageAccountRG -Name $StorageAccountName
    $keyValue = $keys[0].Value
    $storageContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $keyValue -Protocol Http -Endpoint $StorageEndpointSuffix;
    $container = New-AzureStorageContainer -Name $DestStorageContainer -Context $storageContext -Permission Container -ErrorAction Ignore;    
    ```
    出力例を次に示します。

    ```output
    PS C:\Users\user> $StorageAccountName = "myasesa4"                                                              
    PS C:\Users\user> $StorageAccountRG = "myaserg4"
    PS C:\Users\user> $DestStorageContainer = "myasecont2"
    PS C:\Users\user> $StorageEndpointSuffix = "myasegpudev.wdshcsso.com"
    PS C:\Users\user> $DestFileName = "testfile1"

    PS C:\Users\user> $keys = Get-AzureRmStorageAccountKey -ResourceGroupName $StorageAccountRG -Name $StorageAccountName
    PS C:\Users\user> $keyValue = $keys[0].Value
    PS C:\Users\user> $storageContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $keyValue -Protocol Http -Endpoint $StorageEndpointSuffix;                                                                                   
    PS C:\Users\user> $storagecontext 
    StorageAccountName : myasesa4                                                                                      
    BlobEndPoint       : http://myasesa4.blob.myasegpudev.wdshcsso.com/                                                
    TableEndPoint      : http://myasesa4.table.myasegpudev.wdshcsso.com/
    QueueEndPoint      : http://myasesa4.queue.myasegpudev.wdshcsso.com/
    FileEndPoint       : http://myasesa4.file.myasegpudev.wdshcsso.com/
    Context            : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
    Name               :
    StorageAccount     : BlobEndpoint=http://myasesa4.blob.myasegpudev.wdshcsso.com/;QueueEndpoint=http://myasesa4.que
                         ue.myasegpudev.wdshcsso.com/;TableEndpoint=http://myasesa4.table.myasegpudev.wdshcsso.com/;Fi
                         leEndpoint=http://myasesa4.file.myasegpudev.wdshcsso.com/;AccountName=myasesa4;AccountKey=[ke
                         y hidden]
    EndPointSuffix     : myasegpudev.wdshcsso.com/
    ConnectionString   : BlobEndpoint=http://myasesa4.blob.myasegpudev.wdshcsso.com/;QueueEndpoint=http://myasesa4.que
                         ue.myasegpudev.wdshcsso.com/;TableEndpoint=http://myasesa4.table.myasegpudev.wdshcsso.com/;Fi
                         leEndpoint=http://myasesa4.file.myasegpudev.wdshcsso.com/;AccountName=myasesa4;AccountKey=GSK
                         FuTCJi5Dby6A6C1F4jB4bYS/gBNslb7/FAdlh/0VWUg3Vxd1kHsbwN8sw85pMqdKG1AajoeiwzhievHPnlQ==
    ExtendedProperties : {}
    
    PS C:\Users\user> $container = New-AzureStorageContainer -Name $DestStorageContainer -Context $storageContext -Permission Container -ErrorAction Ignore;
    PS C:\Users\user> $container
    Blob End Point: http://myasesa4.blob.myasegpudev.wdshcsso.com/
    
    Name                 PublicAccess         LastModified
    ----                 ------------         ------------
    myasecont2           Container            4/12/2021 4:46:03 PM +00:00
    
    PS C:\Users\user>
    ```    

    また、Azure Storage Explorer を使用して[ローカル ストレージ アカウントを作成](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#create-a-storage-account)してから、デバイスの[ローカル ストレージ アカウントにコンテナーを作成](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload)することもできます。 



1. ローカル ストレージ アカウントにスナップショットをダウンロードします。

   ```powershell
   $sassnapshot = Grant-AzureRmSnapshotAccess -ResourceGroupName $DestinationRG -SnapshotName $SnapshotName -Access 'Read' -DurationInSecond 3600
   $destContext = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $keyValue 
   Start-AzureStorageBlobCopy -AbsoluteUri $sassnapshot.AccessSAS -DestContainer $DestStorageContainer -DestContext $destContext -DestBlob $DestFileName
   ```

    出力例を次に示します。

    ```output
    PS C:\Users\user> $sassnapshot= Grant-AzureRmSnapshotAccess -ResourceGroupName $DestinationRG -SnapshotName $SnapshotName -Access 'Read' -DurationInSecond 3600
    PS C:\Users\user> $sassnapshot
       
    AccessSAS : https://md-2.blob.myasegpudev.wdshcsso.com/3d95ae10d9924e6fb84de408d071f22d/abcd.vhd?sv=2017-04-17&sr=
    b&si=2535bf98-f87f-4738-9142-594e3c1150fc&sk=system-1&sig=4wrtYzWg6ePWBdrXlodrVgT76q7PIueCbw3bbShKCGs%3D   
    
    PS C:\Users\user> $destContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $keyValue
    PS C:\Users\user> $destContext
        
    StorageAccountName : myasesa4
    BlobEndPoint       : https://myasesa4.blob.myasegpudev.wdshcsso.com/
    TableEndPoint      : https://myasesa4.table.myasegpudev.wdshcsso.com/
    QueueEndPoint      : https://myasesa4.queue.myasegpudev.wdshcsso.com/
    FileEndPoint       : https://myasesa4.file.myasegpudev.wdshcsso.com/
    Context            : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
    Name               :
    StorageAccount     : BlobEndpoint=https://myasesa4.blob.myasegpudev.wdshcsso.com/;QueueEndpoint=https://myasesa4.q
                         ueue.myasegpudev.wdshcsso.com/;TableEndpoint=https://myasesa4.table.myasegpudev.wdshcsso.com/;FileEndpoint=https://myasesa4.file.myasegpudev.wdshcsso.com/;AccountName=myasesa4;AccountKey=[key hidden]                                  EndPointSuffix     : myasegpudev.wdshcsso.com/                                                                     ConnectionString   : BlobEndpoint=https://myasesa4.blob.myasegpudev.wdshcsso.com/;QueueEndpoint=https://myasesa4.q
                         ueue.myasegpudev.wdshcsso.com/;TableEndpoint=https://myasesa4.table.myasegpudev.wdshcsso.com/
                         ;FileEndpoint=https://myasesa4.file.myasegpudev.wdshcsso.com/;AccountName=myasesa4;AccountKey
                         =GSKFuTCJi5Dby6A6C1F4jB4bYS/gBNslb7/FAdlh/0VWUg3Vxd1kHsbwN8sw85pMqdKG1AajoeiwzhievHPnlQ==
    ExtendedProperties : {}

    PS C:\Users\user> Start-AzureStorageBlobCopy -AbsoluteUri $sassnapshot.AccessSAS -DestContainer $DestStorageContainer -DestContext $destContext -DestBlob $DestFileName
    
    
       Container Uri: https://myasesa4.blob.myasegpudev.wdshcsso.com/myasecont2
    
    Name                 BlobType  Length          ContentType                    LastModified         AccessTier Snapshot Time
    ----                 --------  ------          -----------                    ------------         ---------- ----
    testfile1            BlockBlob -1                                             2021-04-12 17:01:58Z
 
    PS C:\Users\user>
    ```

    また、Storage Explorer を使用して、スナップショットがストレージ アカウントに正しくコピーされたことを確認することもできます。

    ![ローカル ストレージ アカウントのコンテナー内のバックアップを示す Storage Explorer](media/azure-stack-edge-gpu-back-up-virtual-machine-disks/back-up-virtual-machine-disk-1.png)

---

## <a name="download-vhd-to-external-target"></a>VHD を外部ターゲットにダウンロードする

バックアップを外部の場所に移動するために、Azure Storage Explorer または AzCopy を使用できます。

- 次の AzCopy コマンドを使用して、外部ターゲットに VHD をダウンロードします。

    ```powershell
    azcopy copy "https://<local storage account name>.blob.<device name>.<DNS domain>/<container name>/<filename><SAS query string>" <destination target>
    ```

- Azure Stack Edge で Azure Storage Explorer を設定して使用する場合は、「[アップロードに Storage Explorer を使用する](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload)」の手順を参照してください。

## <a name="next-steps"></a>次のステップ

[テンプレートを使用して、Azure Stack Edge Pro GPU デバイスに仮想マシンをデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)。