---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 12/10/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 3ec5b9c6357f0d075ddd9b0fd5c8a88ee2846209
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192752"
---
## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。


## <a name="preview-register-the-feature"></a>更新:機能を登録する

共有イメージ ギャラリーはプレビュー段階ですが、使用するには、事前に機能を登録しておく必要があります。 共有イメージ ギャラリー機能を登録するには、次のコマンドを実行します。

```azurepowershell-interactive
Register-AzureRmProviderFeature `
   -FeatureName GalleryPreview `
   -ProviderNamespace Microsoft.Compute
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="get-the-managed-image"></a>マネージド イメージを取得する

リソース グループで利用できるイメージは、[Get-AzureRmImage](/powershell/module/AzureRM.Compute/get-azurermimage) を使用して一覧表示できます。 イメージの名前とそれが属しているリソース グループがわかったら、もう一度 `Get-AzureRmImage` を使用してイメージ オブジェクトを取得し、後で使用できるよう変数に格納することができます。 この例では、*myImage* という名前のイメージを "myResourceGroup" リソース グループから取得し、変数 *$managedImage* に割り当てています。 

```azurepowershell-interactive
$managedImage = Get-AzureRmImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>イメージ ギャラリーを作成する 

イメージ ギャラリーは、イメージの共有を有効にするために使用されるプライマリ リソースです。 ギャラリー名は、お使いのサブスクリプション内で一意にする必要があります。 イメージ ギャラリーは、[New-AzureRmGallery](/powershell/module/AzureRM.Compute/new-azurermgallery) を使用して作成します。 次の例では、*myGalleryRG* リソース グループに *myGallery* という名前のギャラリーを作成します。

```azurepowershell-interactive
$resourceGroup = New-AzureRMResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzureRmGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   
## <a name="create-an-image-definition"></a>イメージ定義を作成する 

ギャラリー イメージの定義は、[New-AzureRmGalleryImageDefinition](/powershell/module/azurerm.compute/new-azurermgalleryimageversion) を使用して作成します。 この例では、ギャラリー イメージが *myGalleryImage* という名前になっています。

```azurepowershell-interactive
$galleryImage = New-AzureRmGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

今後のリリースでは、**-Publisher**、**-Offer**、**-Sku** の各値を使用してイメージ定義を検索、指定し、一致するイメージ定義から最新のイメージ バージョンを使用して VM を作成できるようになります。 たとえば、以下に示したのは 3 つのイメージ定義とその値です。

|イメージの定義|発行元|プラン|SKU|
|---|---|---|---|
|myImage1|myPublisher|myOffer|mySku|
|myImage2|myPublisher|standardOffer|mySku|
|myImage3|テスト|standardOffer|testSku|

この 3 つは、それぞれ固有の値を有しています。 今後のリリースでは、特定のイメージの最新バージョンを要求する目的で、これらの値を組み合わせることが可能となります。 

```powershell
# The following should set the source image as myImage1 from the table above
$vmConfig = Set-AzureRmVMSourceImage `
   -VM $vmConfig `
   -PublisherName myPublisher `
   -Offer myOffer `
   -Skus mySku 
```

現在、[Azure Marketplace イメージ](../articles/virtual-machines/windows/cli-ps-findimage.md)では、それらを指定して VM を作成できますが、それと似ています。 このことを踏まえて、イメージ定義ごとに、これらの値の組み合わせが一意となるようにしてください。 3 つの値のうち 1 つまたは 2 つを共有するイメージ バージョンを保有することはできますが、3 つ全部を共有するイメージ バージョンを保有することはできません。 

##<a name="create-an-image-version"></a>イメージ バージョンを作成する

イメージ バージョンは、[New-AzureRmGalleryImageVersion](/powershell/module/AzureRM.Compute/new-azurermgalleryimageversion) を使用してマネージド イメージから作成します。 この例のイメージ バージョンは *1.0.0* で、"*米国中西部*" と "*米国中南部*" の両方のデータセンターにレプリケートされます。


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzureRmGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-01-01' `
   -asJob 
```

すべてのターゲット リージョンにイメージがレプリケートされるまでにしばらく時間がかかることがあるため、進行状況を追跡できるようジョブを作成しました。 ジョブの進行状況を確認するには、「`$job.State`」と入力します。

```azurepowershell-interactive
$job.State
```

