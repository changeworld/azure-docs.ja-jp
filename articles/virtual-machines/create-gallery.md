---
title: Shared Image Gallery を作成する
description: Azure で共有イメージ ギャラリーを作成する方法を説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/15/2021
ms.author: cynthn
ms.custom: template-how-to
ms.openlocfilehash: c7462886c2f85e07f060caa7a11a39a782e38cea
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452309"
---
# <a name="create-a-gallery-for-storing-and-sharing-images"></a>イメージを格納および共有するためのギャラリーを作成する


[共有イメージ ギャラリー](./shared-image-galleries.md)により、組織全体でのカスタム イメージの共有が簡素化されます。 カスタム イメージは Marketplace のイメージに似ていますが、カスタム イメージは自分で作成します。 カスタム イメージは、デプロイ タスク (アプリケーションのプリロード、アプリケーションの構成、その他の OS 構成など) のブートストラップを実行するために使用できます。 

共有イメージ ギャラリーを使用すると、組織内、リージョン内またはリージョン間、AAD テナント内で、他のユーザーとご自身のカスタム VM イメージを共有できます。 どのイメージを共有するか、どのリージョンでそのイメージを使用できるようにするか、および、だれと共有するかを選択することができます。 複数のギャラリーを作成することで、共有イメージを論理的にグループ化できます。 

ギャラリーは最上位リソースで、完全な Azure ロールベースのアクセス制御 (Azure RBAC) が可能です。 イメージのバージョン管理もできるため、Azure リージョンの別のセットに各イメージのバージョンをレプリケートできます。 ギャラリーは、マネージド イメージでのみ機能します。

共有イメージ ギャラリー機能には、リソースの種類が複数あります。 

[!INCLUDE [virtual-machines-shared-image-gallery-resources](./includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="create-a-gallery"></a>ギャラリーの作成

イメージ ギャラリーは、イメージの共有を有効にするために使用されるプライマリ リソースです。 ギャラリー名で許可されている文字は、英字 (大文字または小文字)、数字、ドット、およびピリオドです。 ギャラリー名にダッシュを含めることはできません。 ギャラリー名は、お使いのサブスクリプション内で一意にする必要があります。 

次の中からギャラリーの作成方法を選択してください。

### <a name="portal"></a>[ポータル](#tab/portal)

次の例では、*myGalleryRG* リソース グループに *myGallery* という名前のギャラリーを作成します。

1. Azure Portal ( https://portal.azure.com ) にサインインします。
1. 検索ボックスで **共有イメージ ギャラリー** という種類を使用して、結果で **[共有イメージ ギャラリー]** を選択します。
1. **[共有イメージ ギャラリー]** ページで、 **[追加]** をクリックします。
1. **[Shared Image Gallery の作成]** ページで、適切なサブスクリプションを選択します。
1. **[リソース グループ]** で、 **[新規作成]** を選択し、名前として「*myGalleryRG*」を入力します。
1. **[名前]** に、ギャラリー名として「*myGallery*」と入力します。
1. **[リージョン]** は既定値のままにしておきます。
1. 「*テスト用の自分のイメージ ギャラリー*」など、ギャラリーの簡単な説明を入力し、 **[確認および作成]** をクリックすることができます。
1. 検証に合格した後、 **[作成]** を選択します。
1. デプロイが完了したら、 **[リソースに移動]** を選択します。

### <a name="cli"></a>[CLI](#tab/cli)

[az sig create](/cli/azure/sig#az_sig_create) を使用してイメージ ギャラリーを作成します。 次の例では、*myGalleryRG* という名前のリソース グループを "*米国東部*" に作成し、*myGallery* という名前のギャラリーを作成します。

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

イメージ ギャラリーは、[New-AzGallery](/powershell/module/az.compute/new-azgallery) を使用して作成します。 次の例では、*myGalleryRG* リソース グループに *myGallery* という名前のギャラリーを作成します。

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
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
    "description": "Shared Image Gallery for my organization"
  },
  "location": "eastus",
}
```
---

## <a name="next-steps"></a>次のステップ

[イメージ定義とイメージ バージョン](image-version.md)を作成します。

[Azure Image Builder (プレビュー)](./image-builder-overview.md) は、イメージ バージョンの作成の自動化に役立ちます。イメージ バージョンの更新や、[既存のイメージ バージョンからの新しいイメージ バージョンの作成](./windows/image-builder-gallery-update-image-version.md)に使用することさえできます。 

共有イメージ ギャラリー リソースは、テンプレートを使用して作成することもできます。 いくつかの Azure クイック スタート テンプレートが用意されています。 

- [共有イメージ ギャラリーを作成する](https://azure.microsoft.com/resources/templates/sig-create/)
- [共有イメージ ギャラリーにイメージ定義を作成する](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [共有イメージ ギャラリーにイメージのバージョンを作成する](https://azure.microsoft.com/resources/templates/sig-image-version-create/)