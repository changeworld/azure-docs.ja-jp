---
title: テナント間でギャラリー イメージを共有する
description: Shared Image Gallery を使用して Azure テナント間で共有されるイメージを使用して、スケール セットを作成する方法について説明します。
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.date: 04/05/2019
ms.reviewer: akjosh
ms.custom: akjosh, devx-track-azurecli
ms.openlocfilehash: b6b5e48997da03c7f180c62a9142c99b05c95b46
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444039"
---
# <a name="share-images-across-tenants-with-shared-image-gallery"></a>Shared Image Gallery を使用してテナント間でイメージを共有する

[!INCLUDE [virtual-machines-share-images-across-tenants](../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-scale-set-using-azure-cli"></a>Azure CLI を使用したスケール セットの作成

appID、アプリ キー、およびテナント 1 の ID を使用して、テナント 1 のサービス プリンシパルにサインインします。 `az account show --query "tenantId"` を使用して、必要に応じてテナント ID を取得できます。

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
appID、アプリ キー、およびテナント 2 の ID を使用して、テナント 2 のサービス プリンシパルにサインインします。

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

スケール セットを作成する。 例の情報を自身のものに置き換えます。

```azurecli-interactive
az vmss create \
  -g myResourceGroup \
  -n myScaleSet \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>次のステップ

問題が生じた場合は、[共有イメージ ギャラリーに関するトラブルシューティング](../virtual-machines/troubleshooting-shared-images.md)を行うことができます。
