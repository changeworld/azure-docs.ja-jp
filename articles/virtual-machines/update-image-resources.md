---
title: イメージ リソースの一覧表示、更新、削除
description: Azure Compute Gallery のイメージ リソースを一覧表示、更新、削除します。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/05/2021
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2c01020c266a357329c38023c9e9c34c59424088
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448769"
---
# <a name="list-update-and-delete-image-resources"></a>イメージ リソースの一覧表示、更新、削除 

Azure CLI または Azure PowerShell を使って、Azure Compute Gallery (旧称 Shared Image Gallery) のリソースを管理できます。

## <a name="list-information"></a>リストの情報

### <a name="cli"></a>[CLI](#tab/cli)

[az sig list](/cli/azure/sig#az_sig_list) を使用して、利用可能なイメージ ギャラリーに関する場所、ステータス、およびその他の情報を取得します。

```azurecli-interactive 
az sig list -o table
```

[az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) を使用して、OS の種類とステータスに関する情報を含め、ギャラリーにあるイメージ定義を一覧表示します。

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

[az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list) を使って、ギャラリーにあるイメージ バージョンを一覧表示します。

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

[az sig image-version show](/cli/azure/sig/image-version#az_sig_image_version_show) を使用して、イメージ バージョンの ID を取得します。

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

すべてのギャラリーを名前別に一覧表示します。

```azurepowershell-interactive
$galleries = Get-AzResource -ResourceType Microsoft.Compute/galleries
$galleries.Name
```

すべてのイメージ定義を名前別に一覧表示します。

```azurepowershell-interactive
$imageDefinitions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images
$imageDefinitions.Name
```

すべてのイメージ バージョンを名前別に一覧表示します。

```azurepowershell-interactive
$imageVersions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images/versions
$imageVersions.Name
```

イメージ バージョンを削除します。 この例では、*1.0.0* という名前のイメージ バージョンを削除しています。

```azurepowershell-interactive
Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -Name 1.0.0 `
   -ResourceGroupName myGalleryRG
```


---


## <a name="update-resources"></a>リソースの更新

### <a name="cli"></a>[CLI](#tab/cli)
更新可能な対象については、いくつか制限があります。 次の項目を更新できます。 

Azure Compute Gallery:
- 説明

イメージ定義:
- 推奨される vCPU:
- 推奨されるメモリ
- 説明
- 有効期限の終了日

イメージ バージョン:
- リージョンのレプリカ数
- ターゲット リージョン
- 最新バージョンからの除外
- 有効期限の終了日

レプリカ リージョンを追加する予定の場合は、ソース マネージド イメージを削除しないでください。 追加リージョンにイメージ バージョンをレプリケートするには、ソース マネージド イメージが必要です。 

[az sig update](/cli/azure/sig#az_sig_update) を使用してギャラリーの説明を更新します。 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


[az sig image-definition update](/cli/azure/sig/image-definition#az_sig_image_definition_update) を使用してイメージ定義の説明を更新します。

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

[az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update) を使用して、複製先のリージョンを追加するイメージ バージョンを更新します。 イメージが新しいリージョンに複製されるため、この変更には時間がかかります。

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

この例では、[az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update) を使用して、このイメージ バージョンが "*最新*" のイメージとして使用されないようにする方法を示します。

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

この例では、[az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update) を使用して、このイメージ バージョンを "*最新*" のイメージとして見なして含める方法を示します。

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)


更新可能な対象については、いくつか制限があります。 次の項目を更新できます。 

Azure Compute Gallery:
- 説明

イメージ定義:
- 推奨される vCPU:
- 推奨されるメモリ
- 説明
- 有効期限の終了日

イメージ バージョン:
- リージョンのレプリカ数
- ターゲット リージョン
- 最新バージョンからの除外
- 有効期限の終了日

レプリカ リージョンを追加する予定の場合は、ソース マネージド イメージを削除しないでください。 追加リージョンにイメージ バージョンをレプリケートするには、ソース マネージド イメージが必要です。 

ギャラリーの説明を更新するには、[Update-AzGallery](/powershell/module/az.compute/update-azgallery) を使用します。

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

この例では、[Update-AzGalleryImageDefinition](/powershell/module/az.compute/update-azgalleryimagedefinition) を使用して、イメージ定義の有効期限の日付を更新します。

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

この例では、[Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion) を使用して、このイメージ バージョンが "*最新*" のイメージとして使用されないようにする方法を示します。

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```

この例では、[Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion) を使用して、このイメージ バージョンを "*最新*" のイメージと見なして含める方法を示します。

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest:$false
```

---




## <a name="delete-resources"></a>リソースを削除する

最初にイメージのバージョンを削除することによって、逆の順序でリソースを削除する必要があります。 すべてのイメージのバージョンを削除した後、イメージの定義を削除できます。 すべてのイメージの定義を削除した後、ギャラリーを削除できます。 

### <a name="cli"></a>[CLI](#tab/cli)

[az sig image-version delete](/cli/azure/sig/image-version#az_sig_image_version_delete) を使用してイメージのバージョンを削除します。

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

[az sig image-definition delete](/cli/azure/sig/image-definition#az_sig_image_definition_delete) を使用してイメージの定義を削除します。

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


[az sig delete](/cli/azure/sig#az_sig_delete) を使ってギャラリーを削除します。

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)



```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```

---

## <a name="next-steps"></a>次のステップ

[Azure Image Builder (プレビュー)](./image-builder-overview.md) は、イメージ バージョンの作成の自動化に役立ちます。イメージ バージョンの更新や、[既存のイメージ バージョンからの新しいイメージ バージョンの作成](./linux/image-builder-gallery-update-image-version.md)に使用することさえできます。