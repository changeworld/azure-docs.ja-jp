---
title: イメージ バージョンからマネージド ディスクを作成する
description: 共有イメージ ギャラリーのイメージ バージョンからマネージド ディスクを作成します。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/06/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.openlocfilehash: 35edcfb4bdb0715245f4a3190fb22638b1162429
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370986"
---
# <a name="create-a-managed-disk-from-an-image-version"></a>イメージ バージョンからマネージド ディスクを作成する

必要に応じて、Shared Image Gallery に格納されているイメージ バージョンからマネージド ディスクを作成できます。


## <a name="cli"></a>CLI

`source` 変数をイメージ バージョンの ID に設定してから、[az disk create](/cli/azure/disk#az_disk_create) を使用してマネージド ディスクを作成します。 


[az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list) を使用して、イメージ バージョンの一覧を表示できます。 この例では、 *myImageDefinition* イメージ定義の一部であるイメージ バージョンを、 *myGallery* イメージ ギャラリーですべて検索しています。

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```


この例では、 *myManagedDisk* という名前のマネージド ディスクを、 *EastUS* リージョンの *myResourceGroup* という名前のリソース グループ内に作成します。 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDisk --gallery-image-reference $source 
```

ディスクがデータ ディスクの場合は、`--gallery-image-reference-lun` を追加して LUN を指定します。

## <a name="powershell"></a>PowerShell

[Get-AzResource](/powershell/module/az.resources/get-azresource) を使用して、すべてのイメージ バージョンを一覧表示できます。 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```



必要な情報がすべて用意できたら、[Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) を使用して、使用するソース イメージ バージョンを取得し、それを変数に割り当てることができます。 この例では、`myImageDefinition` 定義の `1.0.0` イメージ バージョンを、`myGallery` ソース ギャラリーの `myResourceGroup` リソース グループに取得しています。

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

ディスク情報のいくつかの変数を設定します。

```azurepowershell-interactive
$location = "East US"
$resourceGroup = "myResourceGroup"
$diskName = "myDisk"
```

ソース イメージ バージョンの ID を使用して、ディスク構成を作成し、次にディスクを作成します。 `-GalleryImageReference` については、ソースがデータ ディスクの場合にのみ LUN が必要です。

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location $location `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=1}
```

ディスクを作成します。

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName $resourceGroup `
   -DiskName $diskName
```

## <a name="next-steps"></a>次の手順

[Azure CLI](image-version-managed-image-cli.md) または [PowerShell](image-version-managed-image-powershell.md) を使用して、マネージド ディスクからイメージ バージョンを作成することもできます。


