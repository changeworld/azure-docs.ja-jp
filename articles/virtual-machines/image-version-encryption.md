---
title: 独自のキーで暗号化されたイメージ バージョンを作成する
description: カスタマー マネージド暗号化キーを使用して、Azure Compute Gallery にイメージ バージョンを作成します。
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 7/1/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bcd214413eb4880219e18c4bb03e4430f31690fd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452055"
---
# <a name="use-customer-managed-keys-for-encrypting-images"></a>イメージの暗号化にカスタマー マネージド キーを使用する

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

Azure Compute Gallery (旧称 Shared Image Gallery) 内のイメージはスナップショットとして格納されるので、サーバー側暗号化によって自動的に暗号化されます。 サーバー側暗号化には、利用可能な最強のブロック暗号の 1 つである 256 ビット [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)が使用されます。 サーバー側暗号化は、FIPS 140-2 にも準拠しています。 Azure マネージド ディスクの基になっている暗号化モジュールについて詳しくは、「[暗号化 API:Next Generation (暗号化 API: 次世代)](/windows/desktop/seccng/cng-portal)」を参照してください。

イメージの暗号化にはプラットフォーム マネージド キーを利用することも、独自のキーを使用することもできます。 二重暗号化のために両方を併用することもできます。 独自のキーを使用して暗号化を管理する場合は、イメージ内のすべてのディスクの暗号化と暗号化解除に使用する *カスタマー マネージド キー* を指定できます。 

カスタマー マネージド キーを使用したサーバー側暗号化には、Azure Key Vault が使用されます。 [ご使用の RSA キー](../key-vault/keys/hsm-protected-keys.md)をキー コンテナーにインポートするか、Azure Key Vault で新しい RSA キーを生成することができます。

## <a name="prerequisites"></a>前提条件

この記事においては、イメージをレプリケートする各リージョンにディスク暗号化セットが既に用意されている必要があります。

- カスタマー マネージド キーのみを使用する場合は、[Azure portal](./disks-enable-customer-managed-keys-portal.md) または [PowerShell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-an-azure-key-vault-and-diskencryptionset-optionally-with-automatic-key-rotation) を使用してサーバー側の暗号化でカスタマー マネージド キーを有効にする方法に関する記事を参照してください。

- (二重暗号化のために) プラットフォーム マネージドおよびユーザー マネージドの両方のキーを使用する場合は、[Azure portal](./disks-enable-double-encryption-at-rest-portal.md) または [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md) を使用して保存時の二重暗号化を有効にする方法に関する記事を参照してください。

   > [!IMPORTANT]
   > Azure portal にアクセスするには、リンク [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) を使用する必要があります。 現時点では、このリンクを使用しないと、保存時の二重暗号化がパブリックの Azure portal に表示されません。

## <a name="limitations"></a>制限事項

Azure Compute Gallery 内のイメージの暗号化にカスタマー マネージド キーを使用する場合、こちらの制限が適用されます。 

- 暗号化キー セットは、お使いのイメージと同じサブスクリプション内にある必要があります。

- 暗号化キー セットはリージョン別のリソースであるため、各リージョンには異なる暗号化キー セットが必要になります。

- カスタマー マネージド キーを使用するイメージのコピーや共有はできません。 

- 独自のキーを使用してディスクやイメージを暗号化した後は、それらのディスクやイメージを暗号化するためにプラットフォーム マネージド キーを使用する方法に戻すことはできません。


## <a name="powershell"></a>PowerShell

イメージ バージョンのディスク暗号化セットを指定するには、`-TargetRegion` パラメーターを指定して [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) を使用します。 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$eastUS2osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet'}

$eastUS2dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet1';Lun=1}

$eastUS2dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet2';Lun=2}

$eastUS2DataDiskImageEncryptions = @($eastUS2dataDiskImageEncryption1,$eastUS2dataDiskImageEncryption2)

$encryption2 = @{OSDiskImage=$eastUS2osDiskImageEncryption;DataDiskImages=$eastUS2DataDiskImageEncryptions}

$region2 = @{Name='East US 2';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption2}

$targetRegion = @($region1, $region2)


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>VM の作成

Azure Compute Gallery から仮想マシン (VM) を作成し、カスタマー マネージド キーを使用してそのディスクを暗号化することができます。 構文は、イメージから[一般化](vm-generalized-image-version.md)または[特殊化](vm-specialized-image-version.md)された VM を作成する場合と同じです。 拡張パラメーター セットを使用し、`Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` を VM 構成に追加します。

データ ディスクの場合は、[Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk) を使用するときに `-DiskEncryptionSetId $setID` パラメーターを追加します。


## <a name="cli"></a>CLI 

イメージ バージョンのディスク暗号化セットを指定するには、`--target-region-encryption` パラメーターを指定して [az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create) を使用します。 `--target-region-encryption` の形式は、OS とデータ ディスクを暗号化するためのキーのコンマ区切りリストです。 `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>` のようになります。 

OS ディスクのソースがマネージド ディスクであるか VM である場合は、`--managed-image` を使用してイメージ バージョンのソースを指定します。 この例では、ソースは、OS ディスクと LUN 0 のデータ ディスクを備えたマネージド イメージです。 この OS ディスクは DiskEncryptionSet1 で暗号化され、データ ディスクは DiskEncryptionSet2 で暗号化されます。

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs eastus2 \
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

OS ディスクのソースがスナップショットである場合は、`--os-snapshot` を使用して OS ディスクを指定します。 イメージ バージョンにも含める必要があるデータ ディスク スナップショットがある場合は、それらを追加します。 `--data-snapshot-luns` を使用して LUN 指定し、`--data-snapshots` を使用してスナップショットを指定します。

この例では、ソースはディスク スナップショットです。 OS ディスクと、LUN 0 のデータ ディスクが存在します。 この OS ディスクは DiskEncryptionSet1 で暗号化され、データ ディスクは DiskEncryptionSet2 で暗号化されます。

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs eastus\
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>VM の作成

Azure Compute Gallery から VM を作成し、カスタマー マネージド キーを使用してディスクを暗号化することができます。 構文は、イメージから[一般化](vm-generalized-image-version.md)または[特殊化](vm-specialized-image-version.md)された VM を作成する場合と同じです。 `--os-disk-encryption-set` パラメーターに暗号化セットの ID を指定して追加するだけです。 データ ディスクの場合は、そのデータ ディスクのディスク暗号化セットのスペース区切りリストを指定して `--data-disk-encryption-sets` を追加します。


## <a name="portal"></a>ポータル

ポータルで自分のイメージ バージョンを作成する場合は、 **[暗号化]** タブを使用して、ストレージ暗号化セットを適用できます。

1. **[イメージ バージョンを作成する]** ページで、 **[暗号化]** タブを選択します。
2. **[暗号化の種類]** で、 **[カスタマー マネージド キーを使用した保存時の暗号化]** または **[Double encryption with platform-managed and customer-managed keys]** \(プラットフォーム マネージド キーとカスタマー マネージド キーを使用した二重暗号化\) を選択します。 
3. イメージ内のディスクごとに、 **[ディスク暗号化セット]** ドロップダウン リストから暗号化セットを選択します。 

### <a name="create-the-vm"></a>VM の作成

イメージのバージョンから VM を作成し、カスタマー マネージド キーを使用してディスクを暗号化することができます。 ポータルで VM を作成する場合は、 **[ディスク]** タブの **[暗号化の種類]** で、 **[カスタマー マネージド キーを使用した保存時の暗号化]** または **[Double encryption with platform-managed and customer-managed keys]\(プラットフォーム マネージド キーとカスタマー マネージド キーを使用した二重暗号化\)** を選択します。 次に、ドロップダウン リストから暗号化セットを選択します。

## <a name="next-steps"></a>次のステップ

[ディスクのサーバー側暗号化](./disk-encryption.md)について学習する。

購入プラン情報を提供する方法については、[イメージ作成時の Azure Marketplace 購入プラン情報の提供](marketplace-images.md)に関する記事を参照してください。
