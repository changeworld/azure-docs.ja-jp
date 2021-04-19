---
title: PowerShell を使用してマーケットプレース購入プランの情報を検索および使用する
description: Azure PowerShell を使用して、Marketplace VM イメージ用にパブリッシャー、オファー、SKU、バージョンなどのイメージ URN および購入プラン パラメーターを検索します。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/17/2021
ms.author: cynthn
ms.custom: contperf-fy21q3
ms.openlocfilehash: 282eaa6e966ff458dfb1dfdd32ef6ec1f2cdc151
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443748"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>Azure PowerShell を使用して Azure Marketplace VM イメージを検索して使用する

この記事では、Azure PowerShell を使用して Azure Marketplace 内で VM イメージを検索する方法について説明します。 その後、VM の作成時に Marketplace イメージとプランの情報を指定できるようになります。

また、[Azure Marketplace](https://azuremarketplace.microsoft.com/) または [Azure CLI](../linux/cli-ps-findimage.md) を使用して、使用できるイメージとオファーを参照することもできます。 

## <a name="terminology"></a>用語

Azure Marketplace イメージには、次の属性があります。

* **発行元**: イメージを作成した組織です。 例: Canonical、MicrosoftWindowsServer
* **プラン**: 発行元によって作成された関連するイメージのグループ名です。 例: UbuntuServer、WindowsServer
* **SKU**: ディストリビューションのメジャー リリースなど、プランのインスタンス。 例: 18.04-LTS、2019-Datacenter
* **バージョン**: イメージの SKU のバージョン番号。 

これらの値は、個別に、またはイメージ *URN* として渡すことができます。その際、値はコロン (:) で区切って指定します。 例: *Publisher*:*Offer*:*Sku*:*Version*。 URN 内のバージョン番号を `latest` に置き換えると、イメージの最新バージョンを使用することができます。 

イメージの発行元が追加のライセンスや購入契約条件を提示している場合、イメージを使用するには、それらに事前に同意する必要があります。  詳細については、「[購入プランのご契約条件への同意](#accept-purchase-plan-terms)」を参照してください。

## <a name="list-images"></a>イメージの一覧表示

PowerShell を使用して、イメージの一覧を絞り込むことができます。 必要に応じて、変数の値を置き換えます。

1. [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) を使用して、イメージの発行元を一覧表示します。
    
    ```powershell
    $locName="<location>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```
1. 指定されたパブリッシャーについては、[Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) を使用してプランを一覧表示します。
    
    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```
1. 特定の発行元とプランについては、[Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) を使用して、利用可能な SKU を一覧表示します。
    
    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```
1. SKU の場合は、[Get-AzVMImage](/powershell/module/az.compute/get-azvmimage) を使用してイメージのバージョンを一覧表示します。

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    特定の古いバージョンではなく、最新のイメージを使用する場合は、`latest` を使用することもできます。


これで、選択した発行元、プラン、SKU、およびバージョンを URN (: で区切られた値) へと結合することができます。 [New-AzVM](/powershell/module/az.compute/new-azvm) コマンドレットで VM を作成するときに、この URN を `-Image` パラメーターを使用して渡します。 また、URN 内のバージョン番号を `latest` に置き換えて、最新バージョンのイメージを取得することもできます。

Resource Manager テンプレートを使って VM をデプロイする場合は、`imageReference` プロパティでイメージ パラメーターを個別に設定します。 [テンプレート リファレンス](/azure/templates/microsoft.compute/virtualmachines)をご覧ください。


## <a name="view-purchase-plan-properties"></a>購入プランのプロパティを表示する

Azure Marketplace の一部の VM イメージには、プログラムでデプロイする前に同意する必要がある追加のライセンスと購入契約条件があります。 サブスクリプションごとに 1 回、画像の契約条件に同意する必要があります。

イメージの購入プラン情報を表示するには、`Get-AzVMImage` コマンドレットを実行します。 出力内の `PurchasePlan` プロパティが `null` ではない場合、イメージには、プログラムによるデプロイの前に同意しなければならない使用条件があります。  

たとえば、*Windows Server 2016 Datacenter* イメージに追加条項がないのは、`PurchasePlan` 情報が `null` であるためです。

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

出力は次のようになります。

```output
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

下の例は、*Data Science Virtual Machine - Windows 2016* イメージの同様のコマンドを示しています。次の `PurchasePlan` プロパティが表示されます: `name`、`product`、および `publisher`。 イメージによっては、`promotion code` プロパティもあります。 このイメージをデプロイするには、次のセクションを参照して使用条件に同意し、プログラムによるデプロイを有効にします。

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

出力は次のようになります。

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

ライセンス条項を表示するには、[Get-AzMarketplaceterms](/powershell/module/az.marketplaceordering/get-azmarketplaceterms) コマンドレットを使用し、購入プラン パラメーターを渡します。 出力には、Marketplace イメージの使用条件へのリンクと、以前その使用条件に同意したかどうかが表示されます。 パラメーターの値は全小文字で入力してください。

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

出力は次のようになります。

```output
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

## <a name="accept-purchase-plan-terms"></a>購入プランの契約条件に同意する

[Set-AzMarketplaceterms](/powershell/module/az.marketplaceordering/set-azmarketplaceterms) コマンドレットを使用して、使用条件に同意するか、または拒否します。 使用条件に同意する必要があるのは、イメージのサブスクリプションごとに 1 回だけです。 パラメーターの値は全小文字で入力してください。 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```



```output
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```


## <a name="create-a-new-vm-from-a-marketplace-image"></a>Marketplace イメージから VM を作成する

使用するイメージに関する情報が既にある場合は、その情報を [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) コマンドレットに渡して、イメージ情報を VM 構成に追加することができます。 Marketplace で入手できるイメージの検索と一覧表示については、次のセクションを参照してください。

一部の有料イメージでは、[Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) を使用して購入プランの情報も提供する必要があります。 

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace image
$offerName = "windows-data-science-vm"
$skuName = "windows2016"
$version = "19.01.14"
$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version

# Set the Marketplace plan information, if needed
$publisherName = "microsoft-ads"
$productName = "windows-data-science-vm"
$planName = "windows2016"
$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

...
```

その後、VM 構成と他の構成オブジェクトを `New-AzVM` コマンドレットに渡します。 PowerShell で VM 構成を使用する詳細な例については、この[スクリプト](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine/create-vm-detailed/create-windows-vm-detailed.ps1)を参照してください。

イメージの使用条件への同意に関するメッセージが表示された場合は、前のセクション「[購入プランのご契約条件への同意](#accept-purchase-plan-terms)」を参照してください。

## <a name="create-a-new-vm-from-a-vhd-with-purchase-plan-information"></a>購入プラン情報を使用して VHD から新しい VM を作成する

Azure Marketplace イメージを使用して作成された既存の VHD がある場合は、その VHD から新しい VM を作成するときに、購入プランの情報を指定することが必要になる場合があります。

元の VM、または同じイメージから作成した他の VM がある場合は、Get-AzVM を使用して、そこからプラン名、発行元、および製品情報を取得できます。 この例では、*myResourceGroup* リソース グループの *myVM* という名前の VM を取得し、購入プラン情報を表示します。

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

元の VM が削除される前にプラン情報を取得しなかった場合は、[サポート リクエスト](https://ms.portal.azure.com/#create/Microsoft.Support)を提出できます。 VM 名、サブスクリプション ID、および削除操作のタイム スタンプが必要になります。

VHD を使用して VM を作成するには、「[特殊化した VHD から VM を作成する](create-vm-specialized.md)」の記事を参照して、[Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) を使用して以下のように VM の設定にプラン情報を追加する行を追加します。

```azurepowershell-interactive
$vmConfig = Set-AzVMPlan `
   -VM $vmConfig `
   -Publisher "publisherName" `
   -Product "productName" `
   -Name "planName"
```


## <a name="next-steps"></a>次のステップ

基本イメージ情報を使用し、`New-AzVM` コマンドレットを使って仮想マシンをすばやく作成するには、「[PowerShell で Windows 仮想マシンを作成する](quick-create-powershell.md)」をご覧ください。

Azure Marketplace イメージを使用して Shared Image Gallery にカスタム イメージを作成する方法の詳細については、[イメージ作成時に Azure Marketplace 購入プラン情報を提供する](../marketplace-images.md)方法に関する記事を参照してください。
