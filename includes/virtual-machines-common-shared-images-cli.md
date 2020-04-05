---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 57736a3cd553e83294d5290867e261b626cb035f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "66814890"
---
## <a name="before-you-begin"></a>開始する前に

この記事の例を完了するには、汎用 VM の既存のマネージド イメージを保持している必要があります。 詳細については、「[チュートリアル:Azure CLI を使用して Azure VM のカスタム イメージを作成する](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-custom-images)を参照してください。 マネージド イメージにデータ ディスクが含まれている場合、データ ディスクのサイズが 1 TB を超えることはできません。

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

CLI をインストールしてローカルで使用する場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-an-image-gallery"></a>イメージ ギャラリーを作成する 

イメージ ギャラリーは、イメージの共有を有効にするために使用されるプライマリ リソースです。 ギャラリー名で許可されている文字は、英字 (大文字または小文字)、数字、ドット、およびピリオドです。 ギャラリー名にダッシュを含めることはできません。   ギャラリー名は、お使いのサブスクリプション内で一意にする必要があります。 

[az sig create](/cli/azure/sig#az-sig-create) を使用してイメージ ギャラリーを作成します。 次の例では、*myGalleryRG* に *myGallery* という名前のギャラリーを作成します。

```azurecli-interactive
az group create --name myGalleryRG --location WestCentralUS
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>イメージ定義を作成する

イメージ定義では、イメージの論理グループを作成します。 これは、その中に作成されるイメージ バージョンに関する情報を管理するために使用されます。 イメージ定義名は、大文字または小文字、数字、ドット、ダッシュおよびピリオドで構成できます。 イメージ定義に指定できる値の詳細については、[イメージ定義](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries#image-definitions)に関するページを参照してください。

[az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create) を使用して、ギャラリー内に最初のイメージ定義を作成します。

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku 16.04-LTS \
   --os-type Linux 
```


## <a name="create-an-image-version"></a>イメージ バージョンを作成する 

[az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) を使用して、必要に応じてイメージのバージョンを作成します。 イメージ バージョンを作成するためにベースラインとして使用する、マネージド イメージの ID を渡す必要があります。 [az image list](/cli/azure/image?view#az-image-list) を使用して、リソース グループ内にあるイメージに関する情報を取得できます。 

イメージ バージョンで許可されている文字は、数字とピリオドです。 数字は、32 ビット整数の範囲内になっている必要があります。 形式:*MajorVersion*.*MinorVersion*.*Patch*。

この例では、イメージのバージョンは *1.0.0* であり、ゾーン冗長ストレージを使用して "*米国中西部*" リージョンに 2 個のレプリカ、"*米国中南部*" リージョンに 1 個のレプリカ、および "*米国東部 2*" リージョンに 1 個のレプリカを作成しています。


```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "WestCentralUS" "SouthCentralUS=1" "EastUS2=1=Standard_ZRS" \
   --replica-count 2 \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

> [!NOTE]
> 同じマネージド イメージを使用して別のイメージ バージョンを作成する前に、そのイメージ バージョンが構築とレプリケーションを完全に完了するまで待つ必要があります。
>
> イメージ バージョンを作成するときに、`--storage-account-type standard_zrs` を追加することによって、すべてのイメージ バージョンのレプリカを[ゾーン冗長ストレージ](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs)に格納することもできます。
>

## <a name="share-the-gallery"></a>ギャラリーを共有する

他のユーザーとは、ギャラリー レベルで共有することをお勧めします。 ギャラリーのオブジェクト ID を取得するには、[az sig show](/cli/azure/sig#az-sig-show) を使用します。

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

電子メール アドレスおよび [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) と共に、オブジェクト ID をスコープとして使用して、ユーザーに共有イメージ ギャラリーへのアクセスを付与します。

```azurecli-interactive
az role assignment create --role "Reader" --assignee <email address> --scope <gallery ID>
```


