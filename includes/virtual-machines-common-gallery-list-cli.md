---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f2e252a000c5633b2cc1ef34683d7d2c7c133c03
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47047037"
---
## <a name="using-rbac-to-share-images"></a>RBAC を使用してイメージを共有する

ロール ベース アクセス制御 (RBAC) を使用して、サブスクリプション全体でイメージを共有できます。 イメージ バージョンに対して読み取りアクセス許可を保持しているユーザーは、サブスクリプション間でも、そのイメージ バージョンを使用して仮想マシンをデプロイできます。

RBAC を使用してリソースを共有する方法の詳細については、「[RBAC と Azure CLI を使用してアクセスを管理する](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)」を参照してください。


## <a name="list-information"></a>リストの情報

[az sig list](/cli/azure/sig#az-sig-list) を使用して、利用可能なイメージ ギャラリーに関する場所、ステータス、およびその他の情報を取得します。

```azurecli-interactive 
az sig list -o table
```

[az sig image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list) を使用して、OS の種類とステータスに関する情報を含め、ギャラリーにあるイメージ定義を一覧表示します。

```azurecli-interactive 
az sig image-definition list -g myGalleryRG -r myGallery -o table
```

[az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list) を使用して、ギャラリーにある共有イメージ バージョンを一覧表示します。

```azurecli-interactive
az sig image-version list -g myGalleryRG -r myGallery -i myGalleryImage -o table
```

[az sig image-version show](/cli/azure/sig/image-version#az-sig-image-version-show) を使用して、イメージ バージョンの ID を取得します。

```
az sig image-version show \
-g myGalleryRG \     
-r myGallery \     
-i myGalleryImage \     
--gallery-image-version-name 1.0.0 \     
--query "id"
```