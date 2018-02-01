---
title: "接続されていない Azure 管理ディスクおよび非管理対象ディスクを見つけて削除する | Microsoft Docs"
description: "Azure PowerShell を使用して、接続されていない Azure 管理ディスクおよび非管理対象ディスク (VHD/ページ BLOB) を見つけて削除する方法。"
services: virtual-machines-windows
documentationcenter: 
author: ramankumarlive
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: ramankum
ms.openlocfilehash: a846d3578d40b19762f185381c92bdf8e225b185
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/20/2018
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks"></a>接続されていない Azure 管理ディスクおよび非管理対象ディスクを見つけて削除する
Azure 内の仮想マシンを削除しても、そのマシンに接続されているディスクは既定では削除されません。 これにより、誤って削除された仮想マシンによるデータ損失は防止されますが、接続されていないディスクに引き続き不必要な費用がかかります。 この記事を使用して、接続されていないすべてのディスクを見つけて削除し、コストを節約してください。 


## <a name="find-and-delete-unattached-managed-disks"></a>接続されていない管理ディスクを見つけて削除する 

次のスクリプトは、*ManagedBy* プロパティを使用して、接続されていない[管理ディスク](managed-disks-overview.md)を見つける方法を示しています。 これは、接続されていない管理ディスクを見つけるためにサブスクリプション内のすべての管理ディスクをループ処理し、*ManagedBy* プロパティが null であることをチェックします。 *ManagedBy* プロパティは、管理ディスクが接続されている仮想マシンのリソース ID を格納します。

まず、接続されていないすべてのディスクを表示するために、*deleteUnattachedDisks* 変数を 0 に設定してこのスクリプトを実行することを強くお勧めします。 接続されていないディスクを確認したら、接続されていないすべてのディスクを削除するために *deleteUnattachedDisks* を 1 に設定してこのスクリプトを実行します。

```azurepowershell-interactive

# Set deleteUnattachedDisks=1 if you want to delete unattached Managed Disks
# Set deleteUnattachedDisks=0 if you want to see the Id of the unattached Managed Disks
$deleteUnattachedDisks=0

$managedDisks = Get-AzureRmDisk

foreach ($md in $managedDisks) {
    
    # ManagedBy property stores the Id of the VM to which Managed Disk is attached to
    # If ManagedBy property is $null then it means that the Managed Disk is not attached to a VM
    if($md.ManagedBy -eq $null){

        if($deleteUnattachedDisks -eq 1){
            
            Write-Host "Deleting unattached Managed Disk with Id: $($md.Id)"

            $md | Remove-AzureRmDisk -Force

            Write-Host "Deleted unattached Managed Disk with Id: $($md.Id) "

        }else{

            $md.Id

        }
           
    }
     
 } 
```
## <a name="find-and-delete-unattached-unmanaged-disks"></a>接続されていない非管理対象ディスクを見つけて削除する 

非管理対象ディスクは、[Azure Storage アカウント](../../storage/common/storage-create-storage-account.md)で [ページ BLOB] (/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) として格納される VHD ファイルです。 次のスクリプトは、*LeaseStatus* プロパティを使用して、接続されていない非管理対象ディスク (ページ BLOB) を見つける方法を示しています。 これは、非管理対象ディスクを見つけるためにサブスクリプション内のすべてのストレージ アカウントのすべての非管理対象ディスクをループ処理し、*LeaseStatus* プロパティがロック解除されているかどうかをチェックします。 非管理対象ディスクが仮想マシンに接続されている場合、*LeaseStatus* プロパティはロック済みに設定されています。

まず、接続されていないすべてのディスクを表示するために、*deleteUnattachedVHDs* 変数を 0 に設定してこのスクリプトを実行することを強くお勧めします。 接続されていないディスクを確認したら、接続されていないすべてのディスクを削除するために *deleteUnattachedVHDs* を 1 に設定してこのスクリプトを実行します。


```azurepowershell-interactive
   
# Set deleteUnattachedVHDs=1 if you want to delete unattached VHDs
# Set deleteUnattachedVHDs=0 if you want to see the Uri of the unattached VHDs
$deleteUnattachedVHDs=1

$storageAccounts = Get-AzureRmStorageAccount

foreach($storageAccount in $storageAccounts){

    $storageKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.StorageAccountName)[0].Value

    $context = New-AzureStorageContext -StorageAccountName $storageAccount.StorageAccountName -StorageAccountKey $storageKey

    $containers = Get-AzureStorageContainer -Context $context

    foreach($container in $containers){

        $blobs = Get-AzureStorageBlob -Container $container.Name -Context $context

        #Fetch all the Page blobs with extension .vhd as only Page blobs can be attached as disk to Azure VMs
        $blobs | Where-Object {$_.BlobType -eq 'PageBlob' -and $_.Name.EndsWith('.vhd')} | ForEach-Object { 
        
            #If a Page blob is not attached as disk then LeaseStatus will be unlocked
            if($_.ICloudBlob.Properties.LeaseStatus -eq 'Unlocked'){
              
                  if($deleteUnattachedVHDs -eq 1){

                        Write-Host "Deleting unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"

                        $_ | Remove-AzureStorageBlob -Force

                        Write-Host "Deleted unattached VHD with Uri: $($_.ICloudBlob.Uri.AbsoluteUri)"
                  }
                  else{

                        $_.ICloudBlob.Uri.AbsoluteUri

                  }

            }
        
        }

    }

}

```

## <a name="next-steps"></a>次の手順

[ストレージ アカウントの削除](../../storage/common/storage-create-storage-account.md)




