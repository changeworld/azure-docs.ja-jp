---
title: BLOB のアクセス層を設定する
titleSuffix: Azure Storage
description: BLOB のアクセス層をアップロードするときに指定する方法、または既存の BLOB のアクセス層を変更する方法について説明します。
author: tamram
ms.author: tamram
ms.date: 10/25/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: fryu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7cd01ff89ffdf01676a9a7c3ef5d41ac1bf138a6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441497"
---
# <a name="set-a-blobs-access-tier"></a>BLOB のアクセス層を設定する

BLOB のアクセス層は、次の方法で設定できます。

- ストレージ アカウントの既定のオンライン アクセス層 (ホットまたはクール) を設定する。 個々の BLOB の設定を明示的にオーバーライドしない限り、アカウント内の BLOB は、このアクセス層を継承します。
- アップロード時に BLOB の層を明示的に設定する。 ホット アクセス層、クール アクセス層、またはアーカイブ アクセス層で BLOB を作成できます。
- Set Blob Tier (BLOB の層を設定) 操作を使用して既存の BLOB の層を変更する。通常はホット アクセス層からクール アクセス層に移動します。
- Copy Blob (BLOB のコピー) 操作を使用して既存の BLOB をコピーする。通常はクール アクセス層からより高頻度のアクセス層に移動します。

この記事では、オンライン アクセス層 (ホットまたはクール) で BLOB を管理する方法について説明します。 BLOB をアーカイブ アクセス層に移動する方法の詳細については、「[BLOB をアーカイブする](archive-blob.md)」を参照してください。 アーカイブ アクセス層から BLOB をリハイドレートする方法の詳細については、「[アーカイブ済み BLOB をオンライン層にリハイドレートする](archive-rehydrate-to-online-tier.md)」を参照してください。

BLOB のアクセス層の詳細については、[BLOB データのホット、クール、アーカイブ アクセス層](access-tiers-overview.md)に関するページを参照してください。

## <a name="set-the-default-access-tier-for-a-storage-account"></a>ストレージ アカウントの既定のアクセス層を設定する

汎用 v2 ストレージ アカウントの既定のアクセス層の設定によって、新しい BLOB が既定で作成されるオンライン層が決まりす。 アカウントの作成時に、汎用 v2 ストレージ アカウントの既定のアクセス層を設定できます。または既存のアカウントの構成を更新して設定することもできます。

既存の汎用 v2 ストレージ アカウントの既定のアクセス層設定を変更すると、アクセス層が明示的に設定されていないアカウント内のすべての BLOB に変更が適用されます。 既定のアクセス層を変更すると、課金に影響する可能性があります。 詳細については、「[アカウントのデフォルト アクセス層の設定](access-tiers-overview.md#default-account-access-tier-setting)」を参照してください。

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal で、ストレージ アカウントの作成時にストレージ アカウントの既定のアクセス層を設定するには、次の手順に従います。

1. **[ストレージ アカウント]** ページに移動し、 **[作成]** ボタンを選択します。
1. **[基本]** タブに入力します。
1. **[詳細設定]** タブの **[BLOBストレージ]** で、 **[アクセス層]** を *[ホット]* または *[クール]* に設定します。 既定の設定は *[ホット]* です。
1. **[確認および作成]** を選択して、設定を確認し、ストレージ アカウントを作成します。

    :::image type="content" source="media/access-tiers-online-manage/set-default-access-tier-create-portal.png" alt-text="ストレージ アカウントの作成時に既定のアクセス層を設定する方法を示すスクリーンショット。":::

Azure portal で、既存のストレージ アカウントの既定のアクセス層を更新するには、次の手順に従います。

1. Azure portal 内で、ストレージ アカウントに移動します。
1. **[設定]** の下で **[構成]** を選択します。
1. **[BLOB アクセス層 (既定)]** 設定を見つけて、 *[ホット]* または *[クール]* を選択します。 このプロパティを以前に設定していない場合、既定の設定は *[ホット]* です。
1. 変更を保存します。

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してストレージ アカウントの既定のアクセス層設定を変更するには、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) コマンドを呼び出して、新しい既定のアクセス層を指定します。

```azurepowershell
$rgName = <resource-group>
$accountName = <storage-account>

# Change the storage account tier to Cool
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Cool
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell を使用してストレージ アカウントの既定のアクセス層設定を変更するには、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) コマンドを呼び出して、新しい既定のアクセス層を指定します。

```azurecli
# Change the storage account tier to Cool
az storage account update \
    --resource-group <resource-group> \
    --name <storage-account> \
    --access-tier Cool
```

---

## <a name="set-a-blobs-tier-on-upload"></a>アップロード時に BLOB の層を設定する

Azure Storage に BLOB をアップロードするときに、BLOB の層をアップロード時に設定するための 2 つのオプションがあります。

- BLOB を作成する層を明示的に指定できます。 この設定は、ストレージ アカウントの既定のアクセス層をオーバーライドします。 アップロード時に BLOB または BLOB のセットの層をホット、クール、またはアーカイブに設定できます。
- 層を指定せずに BLOB をアップロードすることができます。 この場合、ストレージ アカウントに指定された既定のアクセス層 (ホットまたはクール) に BLOB が作成されます。

次のセクションでは、BLOB をホットアクセス層またはクール アクセス層にアップロードするように指定する方法について説明します。 アップロード時に BLOB をアーカイブする方法の詳細については、「[アップロード時に BLOB をアーカイブする](archive-blob.md#archive-blobs-on-upload)」を参照してください。

### <a name="upload-a-blob-to-a-specific-online-tier"></a>特定のオンライン層に BLOB をアップロードする

ホット アクセス層またはクール アクセス層に BLOB を作成するには、BLOB を作成するときにその層を指定します。 アップロード時に指定されたアクセス層は、ストレージ アカウントの既定のアクセス層をオーバーライドします。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

BLOB または BLOB のセットを特定の層にアップロードするには、Azure portal で次の手順に従います。

1. ターゲット コンテナーに移動します。
1. **[アップロード]** ボタンを選択します。
1. アップロードするファイルを選択します。
1. **[詳細設定]** セクションを展開し、 **[アクセス層]** を *[ホット]* または *[クール]* に設定します。
1. **[アップロード]** ボタンを選択します。

    :::image type="content" source="media/access-tiers-online-manage/upload-blob-to-online-tier-portal.png" alt-text="Azure portal で BLOB をオンライン層にアップロードする方法を示すスクリーンショット":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して BLOB または BLOB のセットを特定の層にアップロードするには、次の例に示すように [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) コマンドを呼び出します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurepowershell
$rgName = <resource-group>
$storageAccount = <storage-account>
$containerName = <container>

# Get context object
$ctx = New-AzStorageContext -StorageAccountName $storageAccount -UseConnectedAccount

# Create new container.
New-AzStorageContainer -Name $containerName -Context $ctx

# Upload a single file named blob1.txt to the Cool tier.
Set-AzStorageBlobContent -Container $containerName `
    -File "blob1.txt" `
    -Blob "blob1.txt" `
    -Context $ctx `
    -StandardBlobTier Cool

# Upload the contents of a sample-blobs directory to the Cool tier, recursively.
Get-ChildItem -Path "C:\sample-blobs" -File -Recurse | 
    Set-AzStorageBlobContent -Container $containerName `
        -Context $ctx `
        -StandardBlobTier Cool
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して BLOB を特定の層にアップロードするには、次の例に示すように [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) コマンドを呼び出します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --file <file> \
    --tier Cool \
    --auth-mode login
```

Azure CLI を使用して BLOB のセットを特定の層にアップロードするには、次の例に示すように [az storage blob upload-batch](/cli/azure/storage/blob#az_storage_blob_upload_batch) コマンドを呼び出します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli
az storage blob upload-batch \
    --destination <container> \
    --source <source-directory> \
    --account-name <storage-account> \
    --tier Cool \
    --auth-mode login
```

---

### <a name="upload-a-blob-to-the-default-tier"></a>規定の層に BLOB をアップロードする

ストレージ アカウントには、新しい BLOB を作成するオンライン層を指定するための、デフォルト アクセス層の設定があります。 デフォルト アクセス層には、ホット、クールいずれかのアクセス層を設定できます。 この設定の動作は、ストレージ アカウントの種類によって若干異なります。

- 新しい汎用 v2 ストレージ アカウントの既定のアクセス層は、既定でホット アクセス層に設定されています。 デフォルト アクセス層の設定は、ストレージ アカウントの作成時および作成後に変更できます。
- レガシ BLOB Storage アカウントを作成するときには、ストレージ アカウントの作成時に既定のアクセス層設定をホットまたはクールに指定する必要があります。 ストレージ アカウントの既定のアクセス層の設定は、アカウントの作成後に変更できます。

明示的に層を割り当てていない BLOB では、アカウント のデフォルト アクセス層の設定によって層を判断します。 Azure portal、PowerShell、または Azure CLI を使用して、BLOB のアクセス層が推論されるかどうかを判断することができます。

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

アカウントのデフォルト アクセス層の設定によって BLOB のアクセス層を判断した場合、Azure portal では、アクセス層が **“Hot (inferred)” (ホット アクセス層 (推定))** または **“Cool (inferred)” (クール アクセス層 (推定))** と表示されます。

:::image type="content" source="media/access-tiers-online-manage/default-access-tier-portal.png" alt-text="Azure portal での BLOB の既定のアクセス層を示すスクリーンショット":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

BLOB のアクセス層と、それがから推論されるかどうかを Azure PowerShell から判断するには、BLOB を取得し、その **AccessTier** プロパティと **AccessTierInferred** プロパティを確認します。

```azurepowershell
$rgName = "<resource-group>"
$storageAccount = "<storage-account>"
$containerName = "<container>"
$blobName = "<blob>"

# Get the storage account context.
$ctx = New-AzStorageContext -StorageAccountName $storageAccount -UseConnectedAccount

# Get the blob from the service.
$blob = Get-AzStorageBlob -Context $ctx -Container $containerName -Blob $blobName

# Check the AccessTier and AccessTierInferred properties.
# If the access tier is inferred, that property returns true.
$blob.BlobProperties.AccessTier
$blob.BlobProperties.AccessTierInferred
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

BLOB のアクセス層と、それが推論されるかどうかを Azure CL から判断するには、BLOB を取得し、その **blobTier** プロパティと **blobTierInferred** プロパティを確認します。

```azurecli
az storage blob show \
    --container-name <container> \
    --name <blob> \
    --account-name <storage-account> \
    --query '[properties.blobTier, properties.blobTierInferred]' \
    --output tsv \
    --auth-mode login 
```

---

## <a name="move-a-blob-to-a-different-online-tier"></a>BLOB を別のオンライン層に移動する

次の 2 つの方法のいずれかを使用して、既存の BLOB の層を変更できます。

- 直接、または[ライフサイクル管理](access-tiers-overview.md#blob-lifecycle-management)ポリシーを通じて [Set Blob Tier](/rest/api/storageservices/set-blob-tier) (BLOB の層を設定) 操作を呼び出し、BLOB の層を変更します。
- [Copy Blob](/rest/api/storageservices/copy-blob) (BLOB のコピー) 操作を呼び出して、ある層から別の層に BLOB をコピーする。 この場合、ソース BLOB は元の層に残り、ターゲット層に新しい BLOB が作成されます。

これらの各オプションの詳細については、「[BLOB の層を設定または変更する](access-tiers-overview.md#setting-or-changing-a-blobs-tier)」を参照してください。

PowerShell、Azure CLI、またはいずれかの Azure Storage クライアント ライブラリを使用して、BLOB を別の層に移動します。

### <a name="change-a-blobs-tier"></a>BLOB の層を変更する

BLOB の層を変更するときは、その BLOB とそのすべてのデータをターゲット層に移動します。 高頻度アクセス用の層から低頻度アクセス用の層に BLOB の層を変更するときは、[Set Blob Tier](/rest/api/storageservices/set-blob-tier) (BLOB の層を設定) を呼び出すのが通常最も良い方法です。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal で BLOB の層をホットからクールに変更するには、次の手順に従います。

1. 層を変更する BLOB に移動します。
1. BLOB を選択し、 **[層の変更]** ボタンを選択します。
1. **[層の変更]** ダイアログで、ターゲット層を選択します。
1. **[保存]** ボタンを選択します。

    :::image type="content" source="media/access-tiers-online-manage/change-blob-tier-portal.png" alt-text="Azure portal で BLOB の層を変更する方法を示すスクリーンショット":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell で BLOB の層をホットからクールに変更するには、BLOB の **BlobClient** プロパティを使用して .NET リファレンスを BLOB に返してから、そのリファレンスで **SetAccessTier** メソッドを呼び出します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```azurepowershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container>"
$blobName = "<blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Change the blob's access tier to Cool.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Cool", $null, "Standard")
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI で BLOB の層ホットからクールに変更するには、[az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) コマンドを呼び出します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```azurecli
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --tier Cool \
    --auth-mode login
```

---

### <a name="copy-a-blob-to-a-different-online-tier"></a>BLOB を別のオンライン層にコピーする

BLOB を別の層にコピーするときは、その BLOB とそのすべてのデータをターゲット層に移動します。 BLOB をクールからホットに移動するかアーカイブ層から BLOB をリハイドレートするほとんどのシナリオでは、[Copy Blob](/rest/api/storageservices/copy-blob) を呼び出すことをお勧めします。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

該当なし

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して BLOB をクールからホットにコピーするには、[Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) コマンドを呼び出し、ターゲット層を指定します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```azurepowershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$srcContainerName = "<source-container>"
$destContainerName = "<dest-container>"
$srcBlobName = "<source-blob>"
$destBlobName = "<dest-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Copy the source blob to a new destination blob in Hot tier with Standard priority.
Start-AzStorageBlobCopy -SrcContainer $srcContainerName `
    -SrcBlob $srcBlobName `
    -DestContainer $destContainerName `
    -DestBlob $destBlobName `
    -StandardBlobTier Hot `
    -Context $ctx
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して BLOB をクールからホットにコピーするには、[az storage blob copy start](/cli/azure/storage/blob/copy#az_storage_blob_copy_start) コマンドを呼び出し、ターゲット層を指定します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```azurecli
az storage blob copy start \
    --source-container <source-container> \
    --source-blob <source-blob> \
    --destination-container <dest-container> \
    --destination-blob <dest-blob> \
    --account-name <storage-account> \
    --tier hot \
    --auth-mode login
```

---

## <a name="next-steps"></a>次の手順

- [Azure Storage アカウントの既定のアカウント アクセス層を変更する方法](../common/manage-account-default-access-tier.md)
- [アーカイブ済み BLOB をオンライン層にリハイドレートする](archive-rehydrate-to-online-tier.md)
