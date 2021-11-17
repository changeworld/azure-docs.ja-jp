---
title: 特殊化されたイメージ バージョンから VM を作成する
description: Azure Compute Gallery 内の特殊化されたイメージ バージョンを使用して VM を作成します。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/05/2021
ms.author: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: b500a7e02167f5373eb2dbdeb4bdb708c5b69966
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474498"
---
# <a name="create-a-vm-using-a-specialized-image-version"></a>特殊化されたイメージ バージョンを使用して VM を作成する 

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM 

Azure Compute Gallery (旧称 Shared Image Gallery) に格納されている[特殊化されたイメージ バージョン](./shared-image-galleries.md#generalized-and-specialized-images)から VM を作成します。 一般化されたイメージ バージョンを使用して VM を作成する場合は、[一般化されたイメージ バージョンからの VM の作成](vm-generalized-image-version.md)に関する記事をご覧ください。

> [!IMPORTANT]
> 
> 特殊化されたイメージから新しい VM を作成すると、新しい VM は、元の VM のコンピューター名を保持します。 その他のコンピューター固有の情報 (CMID など) も保持されます。場合によっては、この重複情報によって問題が発生する可能性があります。 VM をコピーするときは、アプリケーションが依存するコンピューター固有の情報の種類を把握しておいてください。  

必要に応じて、これらの例のリソース名を置き換えてください。 

### <a name="portal"></a>[ポータル](#tab/portal)

これで、1 つ以上の新しい VM を作成できるようになりました。 この例では、"*米国東部*" のデータセンターにある *myResourceGroup* に、*myVM* という名前の VM を作成します。

1. イメージ定義に移動します。 リソース フィルターを使用すると、使用可能なすべてのイメージ定義を表示できます。
1. イメージ定義のページで、ページの上部にあるメニューから **[VM の作成]** を選択します。
1. **[リソース グループ]** で、 **[新規作成]** を選択し、名前として「*myResourceGroup*」と入力します。
1. **[仮想マシン名]** に、「*myVM*」と入力します。
1. **[リージョン]** で、 *[米国東部]* を選択します。
1. **[可用性オプション]** で、既定の *[インフラストラクチャ冗長は必要ありません]* のままにします。
1. イメージ定義のページから操作を開始していれば、 **[イメージ]** の値に `latest` イメージ バージョンが自動的に入力されます。
1. **[サイズ]** では、利用可能なサイズの一覧から VM サイズを選択し、 **[選択]** を選択します。
1. ソース VM のユーザー名と資格情報が使用されるため、 **[管理者アカウント]** のユーザー名は灰色表示されます。
1. VM へのリモート アクセスを許可する場合、 **[パブリック受信ポート]** で、 **[選択したポートを許可する]** を選択し、ドロップダウンから **[SSH (22)]** または **[RDP (3389)]** を選択します。 VM へのリモート アクセスを許可しない場合、 **[パブリック受信ポート]** で、 **[なし]** を選択したままにします。
1. 完了したら、ページの下部にある **[Review + create]\(確認と作成\)** ボタンを選択します。
1. VM が検証に合格したら、ページの下部にある **[作成]** を選択し、デプロイを開始します。


### <a name="cli"></a>[CLI](#tab/cli)

[az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) を使用してギャラリー内のイメージ定義を一覧表示すると、定義の名前と ID を確認できます。

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

イメージが特殊化されたイメージであることを示す --specialized パラメーターを指定した [az vm create](/cli/azure/vm#az_vm_create) を使用して、VM を作成します。 

`--image` にイメージ定義 ID を指定して、使用可能なイメージの最新バージョンから VM を作成します。 また、`--image` にイメージ バージョン ID を指定して、特定のバージョンから VM を作成することもできます。 

この例では、*myImageDefinition* イメージの最新バージョンから VM を作成しています。

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

特殊化されたイメージ バージョンが用意できたら、[New-AzVM](/powershell/module/az.compute/new-azvm) コマンドレットを使用して新しい VM を 1 つ以上作成できます。 

この例では、イメージ定義 ID を使用して、新しい VM で最新バージョンのイメージが使用されるようにしています。 `Set-AzVMSourceImage -Id` にイメージ バージョン ID を使用して、特定のバージョンを使用することも可能です。 たとえば、イメージ バージョン *1.0.0* を使用するには、次のように入力します: `Set-AzVMSourceImage -Id "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`。 

特定のイメージ バージョンを使用すると、その特定のイメージ バージョンが使用できない場合 (リージョンから削除されるなどして)、オートメーションが失敗する可能性があることに注意してください。 特定のイメージ バージョンが必要な場合を除き、新しい VM の作成にはイメージ定義 ID の使用をお勧めします。

この例では、必要に応じてリソース名を置き換えてください。 


```azurepowershell-interactive

# Create some variables for the new VM.

$resourceGroup = "mySIGSpecializedRG"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition


# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.

$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2 | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig

```

---

**次の手順**

Azure Compute Gallery リソースは、テンプレートを使用して作成することもできます。 いくつかの Azure クイック スタート テンプレートが用意されています。 

- [Azure Compute Gallery の作成](https://azure.microsoft.com/resources/templates/sig-create/)
- [Azure Compute Gallery でのイメージ定義の作成](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Azure Compute Gallery でのイメージ バージョンの作成](https://azure.microsoft.com/resources/templates/sig-image-version-create/)
