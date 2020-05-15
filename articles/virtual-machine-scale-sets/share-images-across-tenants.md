---
title: Azure のテナント間でギャラリー イメージを共有する
description: 共有イメージ ギャラリーを使用して、Azure テナント間で VM イメージを共有する方法について説明します。
author: cynthn
ms.author: cynthn
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 04/05/2019
ms.reviewer: akjosh
ms.custom: akjosh
ms.openlocfilehash: 5b86335ab8bcc3af75dbd8af39e3d913f2461e58
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119843"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Azure テナント間でギャラリー VM イメージを共有する

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

問題が生じた場合は、[共有イメージ ギャラリーに関するトラブルシューティング](troubleshooting-shared-images.md)を行うことができます。
