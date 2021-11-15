---
title: イメージ定義とイメージ バージョンを作成する
description: Azure Compute Gallery でイメージを作成する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/31/2021
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: e52457a67e2c653d1fa4e191ee568b35cb7e365f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471518"
---
# <a name="create-an-image-definition-and-an-image-version"></a>イメージ定義とイメージ バージョンを作成する 

[Azure Compute Gallery](shared-image-galleries.md) (旧称 Shared Image Gallery) により、組織全体でカスタム イメージを簡単に共有できます。 カスタム イメージは Marketplace のイメージに似ていますが、カスタム イメージは自分で作成します。 カスタム イメージは、デプロイ タスク (アプリケーションのプリロード、アプリケーションの構成、その他の OS 構成など) のブートストラップを実行するために使用できます。 

Azure Compute Gallery を使用すると、組織内、リージョン内またはリージョン間、AAD テナント内で、他のユーザーとご自身のカスタム VM イメージを共有できます。 どのイメージを共有するか、どのリージョンでそのイメージを使用できるようにするか、および、だれと共有するかを選択することができます。 イメージを論理的にグループ化するために、複数のギャラリーを作成できます。 

Azure Compute Gallery 機能には、次のような複数のリソースの種類があります。

[!INCLUDE [virtual-machines-shared-image-gallery-resources](./includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="before-you-begin"></a>開始する前に

この記事を完了するには、Azure Compute Gallery が既に存在し、イメージのソースが Azure で使用可能である必要があります。 イメージ ソースは、次のものとすることができます。
- サブスクリプション内の VM。 [特殊な VM と一般の VM](shared-image-galleries.md#generalized-and-specialized-images) の両方からイメージをキャプチャできます。 
- マネージド イメージ
- マネージド OS ディスクとデータ ディスク。
- ストレージ アカウント内の VHD としての OS ディスクとデータ ディスク。
- 同じギャラリー内、または同じサブスクリプションの別のギャラリー内の他のイメージ バージョン。

イメージにデータ ディスクが含まれる場合、データ ディスクのサイズが 1 TB を超えることはできません。

イメージ定義名は、大文字または小文字、数字、ドット、ダッシュおよびピリオドで構成できます。 イメージ定義に指定できる値の詳細については、[イメージ定義](shared-image-galleries.md#image-definitions)に関するページを参照してください。

イメージ バージョンで許可されている文字は、数字とピリオドです。 数字は、32 ビット整数の範囲内になっている必要があります。 形式:*MajorVersion*.*MinorVersion*.*Patch*。

この記事の作業に出現するリソース名は適宜置き換えてください。

## <a name="create-an-image"></a>イメージを作成する 

イメージ定義とイメージ バージョンを作成するには、次のオプションを選択します。

### <a name="portal"></a>[ポータル](#tab/portal)

ポータルで VM からイメージを作成するには、[VM のイメージのキャプチャ](capture-image-portal.md)に関するページを参照してください。 

VM 以外のソースを使用してイメージを作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) に移動し、**Azure Compute Gallery** を検索して選択します。
1. 一覧から使うギャラリーを選びます。
1. ギャラリーのページで、ページの上部にある **[追加]** を選択し、ドロップダウンから **[VM image definition]\(VM イメージ定義\)** を選択します。 
1. **[Add new image definition to Azure Compute Gallery]\(Azure Compute Gallery に新しいイメージ定義を追加\)** ページの **[基本]** タブで **[リージョン]** を選択します。 
1. **[イメージの定義名]** で、「*myImageDefinition*」などの名前を入力します。
1. **[オペレーティング システム]** では、ソースに基づいて適切なオプションを選択します。  
1. **[VM の生成]** では、ソースに基づいてオプションを選択します。 ほとんどの場合、これは *[Gen 1]* になります。 詳細については、[第 2 世代 VM に対するサポート](generation-2.md)に関するページを参照してください。
1. **[オペレーティング システムの状態]** では、ソースに基づいて適切なオプションを選択します。 詳細については、[一般化と特殊化](shared-image-galleries.md#generalized-and-specialized-images)に関するページを参照してください。
1. **[Publisher]\(発行元\)** に、一意の名前を入力します (*myPublisher* など)。 
1. **[オファー]** に、一意の名前を入力します (*myOffer* など)。
1. **[SKU]** に、一意の名前を入力します (*mySKU* など)。
1. ページの下部で **[確認および作成]** を選択します。
1. イメージ定義の検証に合格した後、 **[作成]** を選択します。
1. デプロイが完了したら、 **[リソースに移動]** を選択します。
1. イメージ定義のページの **[Get started]\(作業の開始\)** タブから **[Create a version]\(バージョンの作成\)** を選択します。
1. **[リージョン]** でイメージを作成するリージョンを選択します。 場合によっては、ソースはイメージが作成されるのと同じリージョンに存在する必要があります。 後のドロップダウン リストにソースが表示されない場合は、イメージのリージョンを変更してみてください。 イメージは、後でいつでも他のリージョンにレプリケートできます。
1. **[バージョン番号]** には、「*1.0.0*」のような数値を入力します。 イメージ バージョン名では、整数を使用する *major*.*minor*.*patch* という形式に従う必要があります。 
1. **[ソース]** で、ソースに使用するファイルの種類をドロップダウンから選択します。 各種類のソースの具体的な詳細については、次の表を参照してください。

    | ソース | その他のフィールド |
    |---|---|
    | ディスクまたはスナップショット | - **OS ディスク** の場合、ドロップダウンからディスクまたはスナップショットを選択します。 <br> - データ ディスクを追加するには、LUN 番号を入力し、ドロップダウンからデータ ディスクを選択します。 |
    | イメージ バージョン | - ドロップダウンから **ソース ギャラリー** を選択します。 <br> - ドロップダウンから正しいイメージ定義を選択します。 <br>- ドロップダウンから、使用する既存のイメージ バージョンを選択します。 |
    | マネージド イメージ | \- ドロップダウンから **ソース イメージ** を選択します。 <br>マネージド イメージは、 **[インスタンスの詳細]** で選択したのと同じリージョンにある必要があります。
    | ストレージ アカウント内の VHD | **[参照]** を選択して、VHD のストレージ アカウントを選択します。 |

1. **[最新から除外]** は、既定値の *[いいえ]* のままにします (ただし、バージョン番号の代わりに `latest` を使用して VM を作成するときにこのバージョンを使用したくない場合を除きます)。
1. **[有効期限の終了日]** で、このバージョンの使用の停止を検討している日付をカレンダーから選択します。
1. **[レプリケーション]** タブで、ドロップダウンからストレージの種類を選択します。
1. **[既定のレプリカ数]** を設定すると、追加するリージョンごとにこれをオーバーライドできます。 
1. ソース リージョンにレプリケートする必要があります。そのため、一覧の最初のレプリカは、イメージを作成したリージョンになります。 レプリカをさらに追加するには、ドロップダウンからリージョンを選択し、必要に応じてレプリカ数を調整します。
1. 完了したら、 **[確認および作成]** を選択します。 Azure で構成が検証されます。
1. イメージ バージョンの検証に合格したら、 **[作成]** を選択します。
1. デプロイが完了したら、 **[リソースに移動]** を選択します。

イメージをすべてのターゲット リージョンにレプリケートするにはしばらく時間がかかる場合があります。

ポータルから既存の VM をイメージとしてキャプチャすることもできます。 詳細については、[ポータルでの VM のイメージの作成](capture-image-portal.md)に関するページを参照してください。

### <a name="cli"></a>[CLI](#tab/cli)

イメージ定義では、イメージの論理グループを作成します。 これは、その中に作成されるイメージ バージョンに関する情報を管理するために使用されます。

[az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create) を使用して、ギャラリー内にイメージ定義を作成します。 イメージ定義の種類が適切であることを確認します。 (Windows の場合は Sysprep、Linux の場合は waagent -deprovision を使用して) VM を一般化している場合は、`--os-state generalized` を使用して、一般化されたイメージ定義を作成する必要があります。 既存のユーザー アカウントを削除せずに VM を使用する場合は、`--os-state specialized` を使用して、特殊化されたイメージ定義を作成します。

イメージ定義に指定できるパラメーターの詳細については、[イメージ定義](shared-image-galleries.md#image-definitions)に関するページを参照してください。

この例では、イメージ定義は *myImageDefinition* という名前で、[特殊化された](shared-image-galleries.md#generalized-and-specialized-images) Linux OS イメージ用です。 Windows OS を使用してイメージの定義を作成するには、`--os-type Windows` を使用します。 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

> [!NOTE]
> サードパーティの Marketplace イメージに由来するイメージを含むイメージ定義の場合、プラン情報は、サードパーティ イメージのプラン情報と正確に一致している必要があります。 イメージ定義にプラン情報を含めるには、イメージ定義を作成するときに、`--plan-name`、`--plan-product`、`--plan-publisher` を追加します。
>

**イメージ バージョンの作成**

[az sig image version create](/cli/azure/sig/image-version#az_sig_image_version_create) を使用してイメージ バージョンを作成します。  

イメージを作成するための構文は、ソースとして使用する内容に応じて変わります。 OS ソースが 1 つしかない場合は、ソースの種類を混在させることができます。 また、データ ディスクごとに異なるソースを使用することもできます。

| source  | パラメーター セット |
|---|---|
| **OS ディスク**| |
| VM ID を使用する VM| `--managed-image <Resource ID of the VM>` |
| マネージド イメージまたは別のイメージバージョン | `--managed-image <Resource ID of the managed image or image version` |
| スナップショットまたはマネージド ディスク | `--os-snapshot <Resource ID of the snapshot or managed disk>` |
| ストレージ アカウント内の VHD | `--os-vhd-uri <URI> --os-vhd-storage-account <storage account name>`.  | 
| **データ ディスク** |
| スナップショットまたはマネージド ディスク | `--data-snapshots <Resource ID of the snapshot or managed disk> --data-snapshot-luns <LUN number>` |
| ストレージ アカウント内の VHD | `--data-vhds-sa <storageaccountname> --data-vhds-uris <URI> --data-vhds-luns <LUN number>` |

イメージにさまざまなソースを指定する方法の詳細な例については、[az sig image-version create の例](/cli/azure/sig/image-version#az_sig_image_version_create-examples)を参照してください。

次の例では、**VM** からイメージを作成します。 イメージのバージョンは *1.0.0* であり、ゾーン冗長ストレージを使用して "*米国中西部*" リージョンに 2 個のレプリカ、"*米国中南部*" リージョンに 1 個のレプリカ、および "*米国東部 2*" リージョンに 1 個のレプリカを作成しています。 レプリケーション リージョンには、ソース VM が配置されているリージョンが含まれている必要があります。

イメージを作成する前に VM を停止 (または割り当て解除) することをお勧めします。

この例の `--managed-image` の値を、VM の ID で置き換えます。

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> 同じマネージド イメージを使用して別のイメージ バージョンを作成する前に、そのイメージ バージョンが構築とレプリケーションを完全に完了するまで待つ必要があります。
>
> また、イメージ バージョンを作成するときに、`--storage-account-type  premium_lrs` を追加してイメージを Premium ストレージに格納することも、`--storage-account-type  standard_zrs` を追加して[ゾーン冗長ストレージ](../storage/common/storage-redundancy.md)に格納することもできます。

### <a name="powershell"></a>[PowerShell](#tab/powershell)

イメージ定義では、イメージの論理グループを作成します。 イメージ定義を作成する際は、正しい情報がすべて含まれていることを確認してください。 (Windows の場合は Sysprep、Linux の場合は waagent -deprovision を使用して) イメージのソースを一般化している場合は、`-OsState generalized` を使用してイメージ定義を作成する必要があります。 ソースを一般化していない場合、`-OsState specialized` を利用してイメージ定義を作成します。

イメージ定義に指定できる値の詳細については、[イメージ定義](./shared-image-galleries.md#image-definitions)に関するページを参照してください。

イメージの定義は、[New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) を使用して作成します。 

この例では、イメージ定義は *myImageDefinition* という名前で、Windows を実行する特殊な VM 用です。 Linux を使用してイメージの定義を作成するには、`-OsType Linux` を使用します。 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

> [!NOTE]
> サードパーティ イメージに由来するイメージを含むイメージ定義の場合、プラン情報は、サードパーティ イメージのプラン情報と正確に一致している必要があります。 イメージ定義にプラン情報を含めるには、イメージ定義を作成するときに、`-PurchasePlanName`、`-PurchasePlanProduct`、`-PurchasePlanPublisher` を追加します。
>

**イメージ バージョンを作成する**

[New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) を使用してイメージ バージョンを作成します。 

イメージを作成するための構文は、ソースとして使用する内容に応じて変わります。 

| source  | パラメーター セット |
|---|---|
| **OS ディスク**| |
| VM ID を使用する VM| `-SourceImageId <Resource ID of the VM>` |
| マネージド イメージまたは別のイメージバージョン | `-SourceImageId <Resource ID of the managed image or image version` |
| スナップショットまたはマネージド ディスク | `-OSDiskImage <Resource ID of the snapshot or managed disk>` |
| **データ ディスク** |
| スナップショットまたはマネージド ディスク | `-DataDiskImage @{Source = @{Id=<source_id>}; Lun=<LUN>; SizeInGB = <Size in GB>; HostCaching = <Caching> }` |


次の例では、VM からイメージ バージョンを作成します。 [Stop-AzVM](/powershell/module/az.compute/stop-azvm) を利用し、イメージを作成する前に VM を停止 (または割り当て解除) することをお勧めします。

この例のイメージ バージョンは *1.0.0* で、"*米国中西部*" と "*米国中南部*" の両方のデータセンターにレプリケートされます。 レプリケーションのターゲット リージョンを選択するときに、レプリケーションのターゲットとして、"*ソース*" リージョンも含める必要があることに注意してください。


```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $sourceVm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `  
   -asJob 
```

すべてのターゲット リージョンにイメージがレプリケートされるまでにしばらく時間がかかることがあるため、進行状況を追跡できるようジョブを作成しました。 ジョブの進行状況を確認するには、「`$job.State`」と入力します。

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> 同じマネージド イメージを使用して別のイメージ バージョンを作成する前に、そのイメージ バージョンが構築とレプリケーションを完全に完了するまで待つ必要があります。
>
> また、イメージ バージョンを作成するときに、`-StorageAccountType Premium_LRS` を追加してイメージを Premium ストレージに格納することも、`-StorageAccountType Standard_ZRS` を追加して[ゾーン冗長ストレージ](../storage/common/storage-redundancy.md)に格納することもできます。
>

### <a name="rest"></a>[REST](#tab/rest)

[REST API](/rest/api/compute/gallery-images/create-or-update) を使用してイメージ定義を作成する

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}/images/{galleryDefinitionName}?api-version=2019-12-01

{
    "location": "eastus",
    "properties": {
        "hyperVGeneration": "V1",
        "identifier": {
            "offer": "myOffer",
            "publisher": "myPublisher",
            "sku": "mySKU"
        },
        "osState": "Specialized",
        "osType": "Linux",
    },
}
```

[REST API](/rest/api/compute/gallery-image-versions/create-or-update) を使用してイメージ バージョンを作成します。 この例では、VM からイメージ バージョンを作成します。 別のソースを使用するには、ソースのリソース ID を渡します (たとえば、OS ディスク スナップショットの ID を渡します)。

```rest
# @name imageVersion
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}/images/{galleryDefinitionName}/versions/{galleryImageVersionName}?api-version=2019-12-01

{
    "location": "{region}",
    "properties": {
        "publishingProfile": {
            "endOfLifeDate": "2024-12-02T00:00:00+00:00",
            "replicaCount": 1,
            "storageAccountType": "Standard_ZRS",
            "targetRegions": [
                {
                    "name": "eastus",
                    "regionalReplicaCount": 2,
                    "storageAccountType": "Standard_LRS",
                },
                {
                    "name": "westus2",
                }
            ]
        },
        "storageProfile": {
            "source": {
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}"
            }
        }
    }
}
```

---


## <a name="next-steps"></a>次のステップ

購入プラン情報を提供する方法については、[イメージ作成時の Azure Marketplace 購入プラン情報の提供](marketplace-images.md)に関する記事を参照してください。
