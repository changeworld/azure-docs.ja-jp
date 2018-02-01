---
title: "接続されていない Azure 管理ディスクおよび非管理対象ディスクを見つけて削除する | Microsoft Docs"
description: "Azure CLI を使用して、接続されていない Azure 管理対象ディスクと非管理対象ディスク (VHD/ページ BLOB) を見つけて削除する方法"
services: virtual-machines-linux
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: 9ada768cd4128b9dd6949b5a96c557496c6bb11c
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>接続されていない Azure 管理ディスクおよび非管理対象ディスクを見つけて削除する
Azure 内の仮想マシンを削除しても、そのマシンに接続されているディスクは既定では削除されません。 これにより、誤って削除された仮想マシンによるデータ損失は防止されますが、接続されていないディスクに引き続き不必要な費用がかかります。 この記事を使用して、接続されていないすべてのディスクを見つけて削除し、コストを節約してください。 


## <a name="find-and-delete-unattached-managed-disks"></a>接続されていない管理ディスクを見つけて削除する 

次のスクリプトでは、ManagedBy プロパティを使用して、接続されていない管理ディスクを見つける方法を示しています。  これは、サブスクリプション内のすべての管理ディスクをループし、*ManagedBy* プロパティが null であるかどうかをチェックして、接続されていない管理ディスクを見つけます。 *ManagedBy* プロパティは、管理ディスクが接続されている仮想マシンのリソース ID を格納します。 

まず、接続されていないすべてのディスクを表示するために、*deleteUnattachedDisks* 変数を 0 に設定してこのスクリプトを実行することを強くお勧めします。 接続されていないディスクを確認したら、接続されていないすべてのディスクを削除するために *deleteUnattachedDisks* を 1 に設定してこのスクリプトを実行します。

 ```azurecli

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
deleteUnattachedDisks=0

unattachedDiskIds=$(az disk list --query '[?managedBy==`null`].[id]' -o tsv)
for id in ${unattachedDiskIds[@]}
do
    if (( $deleteUnattachedDisks == 1 ))
    then

        echo "Deleting unattached Managed Disk with Id: "$id
        az disk delete --ids $id --yes
        echo "Deleted unattached Managed Disk with Id: "$id

    else
        echo $id
    fi
done

```
## <a name="find-and-delete-unattached-unmanaged-disks"></a>接続されていない非管理対象ディスクを見つけて削除する 

非管理対象ディスクは、[Azure Storage アカウント](../../storage/common/storage-create-storage-account.md)で[ページ BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) として格納された VHD ファイルです。 次のスクリプトでは、LeaseStatus プロパティを使用して、接続されていない非管理対象ディスク (ページ BLOB) を見つける方法を示します。 これは、非管理対象ディスクを見つけるためにサブスクリプション内のすべてのストレージ アカウントのすべての非管理対象ディスクをループ処理し、*LeaseStatus* プロパティがロック解除されているかどうかをチェックします。 非管理対象ディスクが仮想マシンに接続されている場合、*LeaseStatus* プロパティはロック済みに設定されています。 

まず、接続されていないすべてのディスクを表示するために、*deleteUnattachedVHDs* 変数を 0 に設定してこのスクリプトを実行することを強くお勧めします。 接続されていないディスクを確認したら、接続されていないすべてのディスクを削除するために *deleteUnattachedVHDs* を 1 に設定してこのスクリプトを実行します。


 ```azurecli
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the details of the unattached VHDs
deleteUnattachedVHDs=1

storageAccountIds=$(az storage account list --query [].[id] -o tsv)

for id in ${storageAccountIds[@]}
do
    connectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
    containers=$(az storage container list --connection-string $connectionString --query [].[name] -o tsv)

    for container in ${containers[@]}
    do 
        
        blobs=$(az storage blob list -c $container --connection-string $connectionString --query "[?properties.blobType=='PageBlob' && ends_with(name,'.vhd')].[name]" -o tsv)
        
        for blob in ${blobs[@]}
        do
            leaseStatus=$(az storage blob show -n $blob -c $container --connection-string $connectionString --query "properties.lease.status" -o tsv)
            
            if [ "$leaseStatus" == "unlocked" ]
            then 

                if (( $deleteUnattachedVHDs == 1 ))
                then 

                    echo "Deleting VHD: "$blob" in container: "$container" in storage account: "$id

                    az storage blob delete --delete-snapshots include  -n $blob -c $container --connection-string $connectionString

                    echo "Deleted VHD: "$blob" in container: "$container" in storage account: "$id
                else
                    echo "StorageAccountId: "$id" container: "$container" VHD: "$blob
                fi

            fi
        done
    done
done 

```

## <a name="next-steps"></a>次の手順

[ストレージ アカウントの削除](../../storage/common/storage-create-storage-account.md)



