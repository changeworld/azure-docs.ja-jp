---
title: Azure Marketplace イメージを使用して Azure Stack Edge Pro GPU デバイスの VM イメージを作成する
description: Azure Marketplace イメージを使用して、Azure Stack Edge Pro GPU デバイスで使用する VM イメージを作成する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/14/2021
ms.author: alkohli
ms.openlocfilehash: 13d020ed0ea6310f49cbe37555fc2a9048b9f042
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2021
ms.locfileid: "112465388"
---
# <a name="use-azure-marketplace-image-to-create-vm-image-for-your-azure-stack-edge-pro-gpu"></a>Azure Marketplace イメージを使用して Azure Stack Edge Pro GPU の VM イメージを作成する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU デバイスで VM をデプロイするには、VM の作成に使用できるカスタム VM イメージを作成する必要があります。 この記事では、Azure Marketplace イメージから VM イメージを作成するために必要な手順について説明します。 その後、この VM イメージを使用して、Azure Stack Edge Pro GPU デバイスに VM をデプロイできます。

## <a name="vm-image-workflow"></a>VM イメージのワークフロー

以下の手順では、Azure Marketplace ワークフローを使用した VM イメージ ワークフローについて説明します。

1.  Azure CLI がインストールされている Azure Cloud Shell またはクライアントに接続します。
2.  Azure Marketplace を検索し、任意のイメージを特定します。
3.  Marketplace イメージから新しいマネージド ディスクを作成します。
4.  マネージド ディスクから Azure Storage アカウントに VHD をエクスポートします。
5.  マネージド ディスクをクリーンアップします。


詳細については、[Azure PowerShell を使用して Azure Stack Edge Pro デバイスに VM をデプロイする方法](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

Azure Stack Edge で Azure Marketplace イメージを使用する前に、次のいずれかの方法で Azure に接続していることを確認してください。

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment-no-header.md)]


## <a name="search-for-azure-marketplace-images"></a>Azure Marketplace イメージの検索

ここで、使用する Azure Marketplace イメージを特定します。 Azure Marketplace には、たくさん VM イメージがホストされています。 

検索条件に一致する最も一般的に使用されている Marketplace イメージを見つけるには、次のコマンドを実行します。  

```azurecli
az vm image list --all [--publisher <Publisher>] [--offer <Offer>] [--sku <SKU>]
```
最後の 3 つのフラグは省略可能ですが、除外すると長いリストが返されます。

クエリの例をいくつか以下に示します。

```azurecli
#Returns all images of type “Windows Server”
az vm image list --all --publisher "MicrosoftWindowsserver" --offer "WindowsServer" 

#Returns all Windows Server 2019 Datacenter images from West US published by Microsoft
az vm image list --all --location "westus" --publisher "MicrosoftWindowsserver" --offer "WindowsServer" --sku "2019-Datacenter"

#Returns all VM images from a publisher  
az vm image list --all --publisher "Canonical" 
```

特定の発行元、オファー、SKU の VM イメージが照会された場合の出力例を次に示します。

```output
PS /home/user> az vm image list --all --publisher "Canonical" --offer "UbuntuServer" --sku "12.04.4-LTS"
[
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201402270",
    "version": "12.04.201402270"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201404080",
    "version": "12.04.201404080"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201404280",
    "version": "12.04.201404280"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201405140",
    "version": "12.04.201405140"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201406060",
    "version": "12.04.201406060"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201406190",
    "version": "12.04.201406190"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201407020",
    "version": "12.04.201407020"
  },
  {
    "offer": "UbuntuServer",
    "publisher": "Canonical",
    "sku": "12.04.4-LTS",
    "urn": "Canonical:UbuntuServer:12.04.4-LTS:12.04.201407170",
    "version": "12.04.201407170"
  }
]
PS /home/user>
```

>[!IMPORTANT]
> Gen 1 イメージのみを使用します。 Gen 2 として指定されたイメージ (通常、SKU には "-g2" サフィックスが付いています) は、Azure Stack Edge では機能しません。

この例では、Windows Server 2019 Datacenter Core バージョン 2019.0.20190410 を選択します。 このイメージは、ユニバーサル リソース番号 ("URN") で識別します。 
 
:::image type="content" source="media/azure-stack-edge-create-virtual-machine-marketplace-image/marketplace-image-1.png" alt-text="Marketplace イメージの一覧":::

### <a name="commonly-used-marketplace-images"></a>一般的に使用されている Marketplace イメージ

最も一般的に使用されているいくつかのイメージの URN の一覧を次に示します。 特定の OS の最新バージョンのみが必要な場合は、URN のバージョン番号を "latest" に置き換えることができます (例: "MicrosoftWindowsServer:WindowsServer:2019-Datacenter:Latest")。 


| OS              | SKU                                     | Version               | URN                                                                                       |
|-----------------|-----------------------------------------|-----------------------|-------------------------------------------------------------------------------------------|
| Windows Server  | 2019 Datacenter                         | 17763.1879.2104091832 | MicrosoftWindowsServer:WindowsServer:2019-Datacenter:17763.1879.2104091832                |
| Windows Server  | 2019 Datacenter (30 GB スモール ディスク)      | 17763.1879.2104091832 | MicrosoftWindowsServer:WindowsServer:2019-Datacenter-smalldisk:17763.1879.2104091832      |
| Windows Server  | 2019 Datacenter Core                    | 17763.1879.2104091832 | MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core:17763.1879.2104091832           |
| Windows Server  | 2019 Datacenter Core (30 GB スモール ディスク) | 17763.1879.2104091832 | MicrosoftWindowsServer:WindowsServer:2019-Datacenter-Core-smalldisk:17763.1879.2104091832 |
| Windows デスクトップ | Windows 10 20H2 Pro                     | 19042.928.2104091209  | MicrosoftWindowsDesktop:Windows-10:20h2-pro:19042.928.2104091209                          |
| Ubuntu Server   | Canonical Ubuntu Server 18.04 LTS       | 18.04.202002180       | Canonical:UbuntuServer:18.04-LTS:18.04.202002180                                          |
| Ubuntu Server   | Canonical Ubuntu Server 16.04 LTS       | 16.04.202104160       | Canonical:UbuntuServer:16.04-LTS:16.04.202104160                                          |
| CentOS          | CentOS 8.1                              | 8.1.2020062400        | OpenLogic:CentOS:8_1:8.1.2020062400                                                       |
| CentOS          | CentOS 7.7                              | 7.7.2020062400        | OpenLogic:CentOS:7.7:7.7.2020062400                                                       |



## <a name="create-a-new-managed-disk-from-the-marketplace-image"></a>Marketplace イメージから新しいマネージド ディスクを作成します

選択した Marketplace イメージから Azure マネージド ディスクを作成します。 

1. パラメーターを設定する。

    ```azurecli
    $urn = <URN of the Marketplace image> #Example: “MicrosoftWindowsServer:WindowsServer:2019-Datacenter:Latest”
    $diskName = <disk name> #Name for new disk to be created
    $diskRG = <resource group> #Resource group that contains the new disk
    ```


1. ディスクを作成し、SAS アクセス URL を生成します。

    ```azurecli
    az disk create -g $diskRG -n $diskName --image-reference $urn
    $sas = az disk grant-access --duration-in-seconds 36000 --access-level Read --name $diskName --resource-group $diskRG
    $diskAccessSAS = ($sas | ConvertFrom-Json)[0].accessSas 
    ```

出力例を次に示します。

```output
PS /home/user> $urn = “MicrosoftWindowsServer:WindowsServer:2019-Datacenter:Latest”
PS /home/user> $diskName = "newmanageddisk1"
PS /home/user> $diskRG = "newrgmd1"
PS /home/user> az disk create -g $diskRG -n $diskName --image-reference $urn
{
  "burstingEnabled": null,
  "creationData": {
    "createOption": "FromImage",
    "galleryImageReference": null,
    "imageReference": {
      "id": "/Subscriptions/db4e2fdb-6d80-4e6e-b7cd-736098270664/Providers/Microsoft.Compute/Locations/eastus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2019-Datacenter/Versions/17763.1935.2105080716",
      "lun": null
    },
    "logicalSectorSize": null,
    "sourceResourceId": null,
    "sourceUniqueId": null,
    "sourceUri": null,
    "storageAccountId": null,
    "uploadSizeBytes": null
  },
  "diskAccessId": null,
  "diskIopsReadOnly": null,
  "diskIopsReadWrite": 500,
  "diskMBpsReadOnly": null,
  "diskMBpsReadWrite": 100,
  "diskSizeBytes": 136367308800,
  "diskSizeGb": 127,
  "diskState": "Unattached",
  "encryption": {
    "diskEncryptionSetId": null,
    "type": "EncryptionAtRestWithPlatformKey"
  },
  "encryptionSettingsCollection": null,
  "extendedLocation": null,
  "hyperVGeneration": "V1",
  "id": "/subscriptions/db4e2fdb-6d80-4e6e-b7cd-736098270664/resourceGroups/newrgmd1/providers/Microsoft.Compute/disks/NewManagedDisk1",
  "location": "eastus",
  "managedBy": null,
  "managedByExtended": null,
  "maxShares": null,
  "name": "NewManagedDisk1",
  "networkAccessPolicy": "AllowAll",
  "osType": "Windows",
  "propertyUpdatesInProgress": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "resourceGroup": "newrgmd1",
  "securityProfile": null,
  "shareInfo": null,
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "supportsHibernation": null,
  "tags": {},
  "tier": "P10",
  "timeCreated": "2021-06-08T00:39:34.205982+00:00",
  "type": "Microsoft.Compute/disks",
  "uniqueId": "1a649ad4-3b95-471e-89ef-1d2ed1f51525",
  "zones": null
}

PS /home/user> $sas = az disk grant-access --duration-in-seconds 36000 --access-level Read --name $diskName --resource-group $diskRG
PS /home/user>  $diskAccessSAS = ($sas | ConvertFrom-Json)[0].accessSas
PS /home/user>
```

## <a name="export-a-vhd-from-the-managed-disk-to-azure-storage"></a>マネージド ディスクから Azure Storage に VHD をエクスポートする 

この手順では、マネージド ディスクから任意の Azure BLOB ストレージ アカウントに VHD をエクスポートします。 この VHD を使用して Azure Stack Edge に VM イメージを作成できます。

1. VHD がコピーされるコピー先のストレージ アカウントを設定します。
    
    ```azurecli
    $storageAccountName = <destination storage account name>
    $containerName = <destination container name>
    $destBlobName = <blobname.vhd> #Blob that will be created, including .vhd extension
    $storageAccountKey = <storage account key>
    ```

1. VHD をコピー先のストレージ アカウントにコピーします。

    ```azurecli
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    Start-AzureStorageBlobCopy -AbsoluteUri $diskAccessSAS -DestContainer $containerName -DestContext $destContext -DestBlob $destBlobName
    ```

    VHD のコピーには、完了までに数分かかります。 次のコマンドを実行して、コピーが完了したことを確認してから続行してください。 完了すると、[状態] フィールドに "成功" と表示されます。
    
    ```azurecli
    Get-AzureStorageBlobCopyState –Container $containerName –Context $destContext -Blob $destBlobName 
    ```

出力例を次に示します。

```output
PS /home/user> $storageAccountName = "edgeazurevmeus"
PS /home/user> $containerName = "azurevmmp"
PS /home/user> $destBlobName = "newblobmp.vhd"
PS /home/user> $storageAccountKey = "n9sCytWLdTBz0F4Sco9SkPGWp6BJBtf7BJBk79msf1PfxJGQdqSfu6TboZWZ10xyZdc4y+Att08cC9B79jB0YA=="

PS /home/user> $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
PS /home/user> Start-AzureStorageBlobCopy -AbsoluteUri $diskAccessSAS -DestContainer $containerName -DestContext $destContext -DestBlob $destBlobName

   AccountName: edgeazurevmeus, ContainerName: azurevmmp

Name                 BlobType  Length          ContentType                    LastModified         AccessTier SnapshotTime                 IsDeleted  VersionId
----                 --------  ------          -----------                    ------------         ---------- ------------                 ---------  ---------
newblobmp.vhd        PageBlob  -1                                             2021-06-08 00:50:10Z                                         False

PS /home/user> Get-AzureStorageBlobCopyState –Container $containerName –Context $destContext -Blob $destBlobName

CopyId                  : 24a1e3f5-886a-490d-9dd7-562bb4acff58
CompletionTime          :
Status                  : Pending
Source                  : https://md-lfn221fppr2c.blob.core.windows.net/d4tb2hp5ff2q/abcd?sv=2018-03-28&sr=b&si=4f588db1-9aac-44d9-9607-35497cc08a7f
BytesCopied             : 696254464
TotalBytes              : 136367309312
StatusDescription       :
DestinationSnapshotTime :

PS /home/user> Get-AzureStorageBlobCopyState –Container $containerName –Context $destContext -Blob $destBlobName

CopyId                  : 24a1e3f5-886a-490d-9dd7-562bb4acff58
CompletionTime          : 6/8/2021 12:57:26 AM +00:00
Status                  : Success
Source                  : https://md-lfn221fppr2c.blob.core.windows.net/d4tb2hp5ff2q/abcd?sv=2018-03-28&sr=b&si=4f588db1-9aac-44d9-9607-35497cc08a7f
BytesCopied             : 136367309312
TotalBytes              : 136367309312
StatusDescription       :
DestinationSnapshotTime :
```

## <a name="clean-up-the-managed-disk"></a>マネージド ディスクのクリーンアップ

作成したマネージド ディスクを削除するには、次の手順を実行します。

```azurecli
az disk revoke-access --name $diskName --resource-group $diskRG 
az disk delete --name $diskName --resource-group $diskRG --yes 
```
削除には、完了までに数分かかります。

## <a name="next-steps"></a>次のステップ

[Azure Stack Edge Pro GPU デバイスに VM をデプロイします](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)。