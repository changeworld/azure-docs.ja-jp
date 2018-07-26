---
title: Azure マネージド ディスクのストレージを Standard から Premium に (または Premium から Standard に) 変換する | Microsoft Docs
description: Azure マネージド ディスクのストレージを Azure CLI を使用して Standard から Premium に (または Premium から Standard に) 変換する方法。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.openlocfilehash: 5c06de6b757da63bb35a4f094e5912e8dcd786f9
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005052"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Azure マネージド ディスクのストレージを Standard から Premium に (または Premium から Standard に) 変換する

マネージド ディスクには、[Premium SSD](../windows/premium-storage.md)、Standard SSD (プレビュー)、[Standard HDD](../windows/standard-storage.md) という 3 種類のストレージ オプションがあります。 パフォーマンス上のニーズに基づいて、これらのオプションを最小限のダウンタイムで簡単に切り替えることができます。 これはアンマネージド ディスクではサポートされていません。 ディスクの種類を簡単に切り替えるために、簡単に[マネージド ディスクに変換](convert-unmanaged-to-managed-disks.md)できます。

この記事では、マネージド ディスクを Azure CLI を使用して Standard から Premium に (または Premium から Standard に) 変換する方法を示します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli.md)」を参照してください。 

## <a name="before-you-begin"></a>開始する前に

* 変換作業は VM の再起動を伴うので、既に設定されているメンテナンス期間中に ディスク ストレージの移行をスケジュールしてください。 
* 管理されていないディスクを使用している場合は、まず[マネージド ディスクに変換](convert-unmanaged-to-managed-disks.md)してから、この記事を参照してストレージ オプションを切り替えてください。 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>VM のすべてのマネージド ディスクを Standard から Premium に (または Premium から Standard に) 変換する

次の例は、VM のすべてのディスクを Standard ストレージから Premium ストレージに切り替える方法を示しています。 Premium マネージド ディスクを使用するには、Premium Storage に対応している [VM のサイズ](sizes.md)を使用している必要があります。 この例は、Premium ストレージに対応するサイズへの切り替えも行います。

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>マネージド ディスクを Standard から Premium に (または Premium から Standard に) 変換する

開発/テスト ワークロードでは、コストを削減するために Standard ディスクと Premium ディスクを混在させたい場合があります。 これは、優れたパフォーマンスを必要とするディスクのみを Premium ストレージにアップグレードすることで実現できます。 次の例は、VM の 1 つのディスクを Standard から Premium に (または Premium から Standard に) 切り替える方法を示しています。 Premium マネージド ディスクを使用するには、Premium Storage に対応している [VM のサイズ](sizes.md)を使用している必要があります。 この例は、Premium ストレージに対応するサイズへの切り替えも行います。

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd-and-vice-versa"></a>マネージド ディスクを Standard HDD から standard SSD に (または standard SSD から Standard HDD に) 変換する

次の例は、VM の 1 つのディスクを Standard HDD から Standard SSD に切り替える方法を示しています。

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="convert-using-the-azure-portal"></a>Azure portal を使用して変換する

Azure portal を使用して、アンマネージド ディスクをマネージド ディスクに変換することもできます。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ポータルで VM の一覧から VM を選択します。
3. VM のブレードで、メニューから **[ディスク]** を選択します。
4. **[ディスク]** ブレードの上部で、**[編集]** を選択します。
5. VM が可用性セット内にある場合は、可用性セットを先に変換する必要があることを示す警告が **[マネージド ディスクへの移行]** ブレードに表示されます。 警告には、可用性セットを変換するためにクリックできるリンクが含まれています。 可用性セットが変換された後、または VM が可用性セットに含まれていない場合は、**[移行]** をクリックして、ディスクをマネージド ディスクに移行するプロセスを開始します。 

VM が停止し、移行の完了後に再起動します。

## <a name="next-steps"></a>次の手順

[スナップショット](snapshot-copy-managed-disk.md)を使用して、VM の読み取り専用コピーを取得します。

