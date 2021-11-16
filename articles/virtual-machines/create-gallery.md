---
title: リソースの共有に使用する Azure Compute Gallery を作成する
description: Azure Compute Gallery を作成する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/05/2021
ms.author: cynthn
ms.custom: template-how-to
ms.openlocfilehash: 924cce1b23cf86a6e29ef4fd28f862bf2fa12363
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459693"
---
# <a name="create-a-gallery-for-storing-and-sharing-resources"></a>リソースを格納および共有するためのギャラリーを作成する


[Azure Compute Gallery](./shared-image-galleries.md) (旧称 Shared Image Gallery) を使用すると、イメージやアプリケーション パッケージなどのリソースを組織全体で共有することができます。  

Azure Compute Gallery を使用すると、組織内、リージョン内またはリージョン間、AAD テナント内で、他のユーザーとカスタム VM イメージとアプリケーション パッケージを共有できます。 何を共有するか、どのリージョンで使用できるようにするか、および、だれと共有するかを選択することができます。 複数のギャラリーを作成することで、リソースを論理的にグループ化できます。 

ギャラリーは最上位リソースで、完全な Azure ロールベースのアクセス制御 (Azure RBAC) が可能です。 

## <a name="create-a-gallery"></a>ギャラリーの作成

ギャラリー名で許可されている文字は、英字 (大文字または小文字)、数字、ドット、およびピリオドです。 ギャラリー名にダッシュを含めることはできません。 ギャラリー名は、お使いのサブスクリプション内で一意にする必要があります。 

次の中からギャラリーの作成方法を選択してください。

### <a name="portal"></a>[ポータル](#tab/portal)

次の例では、*myGalleryRG* リソース グループに *myGallery* という名前のギャラリーを作成します。

1. Azure Portal ( https://portal.azure.com ) にサインインします。
1. 検索ボックスに **Azure Compute Gallery** と入力し、結果から **Azure Compute Gallery** を選択します。
1. **[Azure Compute Gallery]** ページで、 **[追加]** をクリックします。
1. **[Azure Compute Gallery の作成]** ページで、適切なサブスクリプションを選択します。
1. **[リソース グループ]** で、 **[新規作成]** を選択し、名前として「*myGalleryRG*」を入力します。
1. **[名前]** に、ギャラリー名として「*myGallery*」と入力します。
1. **[リージョン]** は既定値のままにしておきます。
1. 「*テスト用のギャラリー*」など、ギャラリーの簡単な説明を入力し、 **[確認および作成]** をクリックすることができます。
1. 検証に合格した後、 **[作成]** を選択します。
1. デプロイが完了したら、 **[リソースに移動]** を選択します。

### <a name="cli"></a>[CLI](#tab/cli)

[az sig create](/cli/azure/sig#az_sig_create) を使用してギャラリーを作成します。 次の例では、*myGalleryRG* という名前のリソース グループを "*米国東部*" に作成し、*myGallery* という名前のギャラリーを作成します。

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

ギャラリーは、[New-AzGallery](/powershell/module/az.compute/new-azgallery) を使用して作成します。 次の例では、*myGalleryRG* リソース グループに *myGallery* という名前のギャラリーを作成します。

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Azure Compute Gallery for my organization' 
```


### <a name="rest"></a>[REST](#tab/rest)

[REST API](/rest/api/resources/resource-groups/create-or-update) を使用してリソース グループを作成します。

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2021-04-01

{
  "location": "eastus"
}
```

[REST API](/rest/api/compute/galleries/create-or-update) を使用してギャラリーを作成します。

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}?api-version=2019-12-01

{
  "properties": {
    "description": "Azure Compute Gallery for my organization"
  },
  "location": "eastus",
}
```
---

## <a name="next-steps"></a>次のステップ

- [イメージ定義とイメージ バージョン](image-version.md)を作成します。

- ギャラリーで [VM アプリケーションを作成](vm-applications-how-to.md)します。

- テンプレートを使用して [Azure Compute Gallery を作成する](https://azure.microsoft.com/resources/templates/sig-create/) こともできます。

- [Azure Image Builder](./image-builder-overview.md) は、イメージ バージョンの作成の自動化に役立ちます。イメージ バージョンの更新や、[既存のイメージ バージョンからの新しいイメージ バージョンの作成](./windows/image-builder-gallery-update-image-version.md)に使用することもできます。 
