---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/28/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: fc60d1266f4aad4c337611f0fa32fe8293744559
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792277"
---
## <a name="list-information"></a>リストの情報

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





