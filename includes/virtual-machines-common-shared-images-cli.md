---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 09/13/2018
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 830deb7569772b610b7e6abde649830b7ad67a92
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47046629"
---
## <a name="before-you-begin"></a>開始する前に

この記事の例を完了するには、汎用 VM の既存のマネージド イメージを保持している必要があります。 詳細については、「[チュートリアル: Azure CLI 2.0 を使用して Azure VM のカスタム イメージを作成する](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images)」を参照してください。 

## <a name="preview-register-the-feature"></a>プレビュー: 機能を登録する

共有イメージ ギャラリーはプレビュー段階ですが、使用するには、事前に機能を登録しておく必要があります。 共有イメージ ギャラリー機能を登録するには、次のコマンドを実行します。

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name GalleryPreview
az provider register -n Microsoft.Compute
```

機能の登録には数分かかる場合があります。 次のコマンドを使用して、進行状況を確認できます。

```azurecli-interactive
az provider show -n Microsoft.Compute
```

## <a name="create-an-image-gallery"></a>イメージ ギャラリーを作成する 

イメージ ギャラリーは、イメージの共有を有効にするために使用されるプライマリ リソースです。 ギャラリー名は、お使いのサブスクリプション内で一意にする必要があります。 [az sig create](/cli/azure/sig#az-sig-create) を使用してイメージ ギャラリーを作成します。 次の例では、*myGalleryRG* に *myGallery* という名前のギャラリーを作成します。

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create -g myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>イメージ定義を作成する

[az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create) を使用して、ギャラリー内に最初のイメージ定義を作成します。

```azurecli-interactive 
az sig image-definition create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```

## <a name="create-an-image-version"></a>イメージ バージョンを作成する 
 
[az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) を使用して、必要に応じてイメージのバージョンを作成します。 イメージ バージョンを作成するためにベースラインとして使用する、マネージド イメージの ID を渡す必要があります。 [az image list](/cli/azure/image?view#az-image-list) を使用して、リソース グループ内にあるイメージに関する情報を取得できます。 次の例では、イメージのバージョンは *1.0.0* であり*米国中西部*, *米国中南部*、および米国東部 2* リージョンに、合計 5 個のレプリカを作成しています。

```azurecli-interactive 
az sig image-version create \
   -g myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1" \
   --replica-count 5 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

