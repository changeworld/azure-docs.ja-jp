---
title: アーカイブ済み BLOB をオンライン層にリハイドレートする
titleSuffix: Azure Storage
description: アーカイブ層にある BLOB を読み取るには、事前にその BLOB をホットまたはクール層にリハイドレートしておく必要があります。 BLOB をリハイドレートするには、それをアーカイブ層からオンライン層にコピーするか、それの層をアーカイブからホットまたはクールに変更します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/01/2021
ms.author: tamram
ms.reviewer: fryu
ms.custom: devx-track-azurepowershell
ms.subservice: blobs
ms.openlocfilehash: 0e24c058239d57cba1c66ebff06ba9d482bcb594
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433196"
---
# <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>アーカイブ済み BLOB をオンライン層にリハイドレートする

アーカイブ層にある BLOB を読み取るには、まず、その BLOB をオンライン層 (ホットまたはクール層) にリハイドレートする必要があります。 BLOB は、次の 2 つの方法のいずれかを使用してリハイドレートできます。

- [[BLOB のコピー]](/rest/api/storageservices/copy-blob) 操作で、ホットまたはクール層の新しい BLOB にコピーする。 Microsoft ではほとんどのシナリオでこのオプションを推奨します。
- [[BLOB 層の設定]](/rest/api/storageservices/set-blob-tier) 操作を使用して、それの層をアーカイブからホットまたはクールに変更する。

BLOB のリハイドレート時には、操作の優先度を標準の優先度または高優先度のいずれかに指定できます。 標準の優先度のリハイドレート操作が完了するまでに最大 15 時間かかることがあります。 高優先度の操作は、標準の優先度の要求よりも優先され、サイズが 10 GB 未満のオブジェクトの場合は 1 時間未満で完了することがあります。 操作が保留中である場合に、リハイドレートの優先度を "*標準*" から "*高*" に変更できます。

Azure Event Grid を構成してリハイドレートが完了するときにイベントを起動し、応答としてアプリケーション コードを実行できます。 BLOB リハイドレート操作が完了したときに Azure 関数を実行するイベントを処理する方法については、「[BLOB リハイドレート イベントに応答して Azure 関数を実行する](archive-rehydrate-handle-event.md)」を参照してください。

BLOB のリハイドレートの詳細については、「[アーカイブ層からの BLOB のリハイドレート](archive-rehydrate-overview.md)」を参照してください。

## <a name="rehydrate-a-blob-with-a-copy-operation"></a>コピー操作を使用して BLOB をリハイドレートする

BLOB をアーカイブ層からオンライン層にコピーしてリハイドレートするには、PowerShell、Azure CLI、またはいずれかの Azure Storage クライアント ライブラリを使用します。 アーカイブ済み BLOB をオンライン層にコピーする場合、ソース BLOB とコピー先 BLOB の名前は異なる必要があります。

コピー操作が完了すると、コピー先 BLOB がアーカイブ層に表示されます。 コピー先 BLOB は、コピー操作で指定したオンライン層にリハイドレートされます。 コピー先 BLOB が完全にリハイドレートされた場合、新しいオンライン層で使用できます。

次の例は、PowerShell または Azure CLI を使用してアーカイブ済み BLOB をコピーする方法を示します。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

該当なし

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してアーカイブ済み BLOB をオンライン層にコピーするには [Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) コマンドを呼び出し、ターゲット層とリハイドレートの優先度を指定します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

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
    -RehydratePriority Standard `
    -Context $ctx
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してアーカイブ済み BLOB をオンライン層にコピーするには [az storage blob copy start](/cli/azure/storage/blob/copy#az_storage_blob_copy_start) コマンドを呼び出し、ターゲット層とリハイドレートの優先度を指定します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```azurecli
az storage blob copy start \
    --source-container <source-container> \
    --source-blob <source-blob> \
    --destination-container <dest-container> \
    --destination-blob <dest-blob> \
    --account-name <storage-account> \
    --tier hot \
    --rehydrate-priority standard \
    --auth-mode login
```

---

## <a name="rehydrate-a-blob-by-changing-its-tier"></a>BLOB の階層を変更して BLOB をリハイドレートする

層をアーカイブからホットまたはクールに変更して、BLOB をリハイドレートするには、Azure portal、PowerShell、または Azure CLI を使用します。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal で BLOB の層をアーカイブからホットまたはクールに変更するには、次の手順を行います。

1. Azure portal でリハイドレートする BLOB を検索します。
1. ページの右側にある **[すべて表示]** ボタンを選択します。
1. **[層の変更]** を選択します。
1. **[アクセス層]** ドロップダウンからターゲット アクセス層を選択します。
1. **[リハイドレートの優先度]** ドロップダウンから、必要なリハイドレートの優先度を選択します。 通常、リハイドレートの優先度を *高* に設定すると、リハイドレートが速くなりますが、コストも高くなります。

    :::image type="content" source="media/archive-rehydrate-to-online-tier/rehydrate-change-tier-portal.png" alt-text="Azure portal でアーカイブ層から BLOB をリハイドレートする方法を示すスクリーンショット ":::

1. **[保存]** を選択します。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell で BLOB の層をアーカイブからホットまたはクールに変更するには、BLOB の **BlobClient** プロパティを使用して BLOB への .NET リファレンスを返してから、そのリファレンスに対して **SetAccessTier** を呼び出します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```azurepowershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container>"
$blobName = "<archived-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Change the blob's access tier to Hot with Standard priority.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Hot", $null, "Standard")
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI で BLOB の層をアーカイブからホットまたはクールに変更するには、[az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) コマンドを呼び出します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```azurecli
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <archived-blob> \
    --tier Hot \
    --rehydrate-priority Standard \
    --auth-mode login
```

---

## <a name="bulk-rehydrate-a-set-of-blobs"></a>一連の BLOB を一括でリハイドレートする

多数の BLOB を一度にリハイドレートするには、[BLOB バッチ](/rest/api/storageservices/blob-batch)操作を呼び出し、一括操作として [BLOB 層の設定](/rest/api/storageservices/set-blob-tier)を呼び出します。 バッチ操作の実行方法を示すコード例については、「[AzBulkSetBlobTier](/samples/azure/azbulksetblobtier/azbulksetblobtier/)」を参照してください。

## <a name="check-the-status-of-a-rehydration-operation"></a>リハイドレート操作の状態を確認する

BLOB のリハイドレート中、Azure portal、PowerShell、または Azure CLI を使用して、その状態とリハイドレートの優先度を確認できます。 状態プロパティは、リハイドレート層のターゲット層に応じて *rehydrate-pending-to-hot* または *rehydrate-pending-to-cool* を返します。 リハイドレートの優先度プロパティは *標準* または *高* を返します。

アーカイブされた BLOB のリハイドレートには最大 15 時間かかる場合があります。また、リハイドレートが完了したかどうかを判断するために BLOB の状態を繰り返しポーリングする処理は非効率的です。 Azure Event Grid を使用して、リハイドレートが完了すると発生するイベントをキャプチャすると、パフォーマンスが向上し、コストが最適化されます。 BLOB リハイドレートでイベントが発生した場合に Azure 関数を実行する方法については、「[BLOB のリハイドレート イベントに応答して Azure 関数を実行する](archive-rehydrate-handle-event.md)」を参照してください。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal で保留中のリハイドレート操作の状態と優先度を確認するには、BLOB の **[層の変更]** ダイアログを表示します。

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-status-portal.png" alt-text="Azure portal で BLOB のリハイドレートの状態を示すスクリーンショット":::

リハイドレートが完了すると、Azure portal では完全にリハイドレートされた BLOB がターゲットのオンライン層に表示されます。

:::image type="content" source="media/archive-rehydrate-to-online-tier/set-blob-tier-rehydrated.png" alt-text="クール層のリハイドレートされた BLOB およびイベント ハンドラーによって書き込まれたログ BLOB を示すスクリーンショット":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell で保留中のリハイドレート操作の状態と優先度を確認するには、[Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) コマンドを呼び出し、BLOB の **ArchiveStatus** および **RehydratePriority** プロパティを確認します。 リハイドレートがコピー操作の場合は、コピー先 BLOB でこれらのプロパティを確認します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```azurepowershell
$rehydratingBlob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$rehydratingBlob.BlobProperties.ArchiveStatus
$rehydratingBlob.BlobProperties.RehydratePriority
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI で保留中のリハイドレート操作の状態と優先度を確認するには、[az storage blob show](/cli/azure/storage/blob#az_storage_blob_show) コマンドを呼び出し、コピー先 BLOB の **rehydrationStatus** プロパティおよび **rehydratePriority** プロパティを確認します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```azurecli
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

---

## <a name="change-the-rehydration-priority-of-a-pending-operation"></a>保留中の操作のリハイドレート優先度を変更する

標準の優先度のリハイドレート操作が保留中である場合は、BLOB のリハイドレートの優先度設定を "*標準*" から "*高*" に変更することで、その BLOB のリハイドレートをより迅速に行うことができます。

保留中の操作については、リハイドレートの優先度の設定を "*高*" から "*標準*" に下げることはできないことに注意してください。 また、リハイドレートの優先度を変更すると、課金に影響する場合があることに注意してください。 詳細については、「[アーカイブ層からの BLOB のリハイドレート](archive-rehydrate-overview.md)」を参照してください。

### <a name="change-the-rehydration-priority-for-a-pending-set-blob-tier-operation"></a>保留中の [BLOB 層の設定] 操作についてリハイドレートの優先度を変更する

標準の優先度である [[BLOB 層の設定]](/rest/api/storageservices/set-blob-tier) 操作が保留中であるときに、リハイドレートの優先度を変更するには、Azure portal、PowerShell、Azure CLI、またはいずれかの Azure Storage クライアントライブラリを使用します。

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用して保留中の操作についてリハイドレートの優先度を変更するには、次の手順を行います。

1. リハイドレートの優先度を変更する BLOB に移動し、BLOB を選択します。
1. **[層の変更]** ボタンを選択します。
1. **[層の変更]** ダイアログで、リハイドレートする BLOB のアクセス層をターゲットのオンライン アクセス層に設定します (ホットまたはクール)。 **[アーカイブの状態]** フィールドには、ターゲットのオンライン層が表示されます。
1. **[リハイドレートの優先度]** ドロップダウンで、優先度を "*高*" に設定します。
1. **[保存]** を選択します。

    :::image type="content" source="media/archive-rehydrate-to-online-tier/update-rehydration-priority-portal.png" alt-text="Azure portal でリハイドレートする BLOB のリハイドレート優先度を更新する方法を示すスクリーンショット":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell で保留中の操作のリハイドレーションの優先度を変更するには、[Az.Storage](https://www.powershellgallery.com/packages/Az.Storage)モジュールバージョン 3.12.0 以降がインストールされていることを確認します。 次に、サービスから BLOB のプロパティを取得します。 この手順は、使用するオブジェクトのプロパティ設定が最新のものであることを保証するのに必要です。 最後に、BLOB の **BlobClient** プロパティを使用して BLOB への .NET リファレンスを返してから、そのリファレンスに対して **SetAccessTier** メソッドを呼び出します。

```azurepowershell
# Get the blob from the service.
$rehydratingBlob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

# Verify that the current rehydration priority is Standard. 
if ($rehydratingBlob.BlobProperties.RehydratePriority -eq "Standard")
{
    # Change rehydration priority to High, using the same target tier.
    if ($rehydratingBlob.BlobProperties.ArchiveStatus -eq "rehydrate-pending-to-hot")
    {
        $rehydratingBlob.BlobClient.SetAccessTier("Hot", $null, "High")
        "Changing rehydration priority to High for blob moving to Hot tier."
    }
    
    if ($rehydratingBlob.BlobProperties.ArchiveStatus -eq "rehydrate-pending-to-cool")
    {
        $rehydratingBlob.BlobClient.SetAccessTier("Cool", $null, "High")
        "Changing rehydration priority to High for blob moving to Cool tier."
    }
}
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI で保留中の操作のリハイドレーションの優先度を変更するには、最初に Azure CLI バージョン 2.29.2 以降がインストールされていることを確認します。 Azure CLI のインストールに関する詳細については、「[Azure CLI をインストールする方法](/cli/azure/install-azure-cli)」をご覧ください。

次に、[az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) コマンドを、`--rehydrate-priority` パラメーターを *High* に設定して呼び出します。 ターゲット層 (ホットまたはクール) は、リハイドレート操作に対して元々指定してある層と同じものにする必要があります。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```azurecli
# Update the rehydration priority for a blob moving to the Hot tier.
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --tier Hot \
    --rehydrate-priority High \
    --auth-mode login

# Show the updated property values.
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

---

### <a name="change-the-rehydration-priority-for-a-pending-copy-blob-operation"></a>保留中の [BLOB のコピー] 操作のリハイドレート優先度を変更する

アーカイブされた BLOB をオンライン層にコピーして BLOB をリハイドレートすると、Azure Storage によって、アーカイブ層にコピー先 BLOB が直ちに作成されます。 次に、コピー先 BLOB は、コピー操作で指定された優先度を使用してターゲット層にリハイドレートされます。 アーカイブされた BLOB をコピー操作を使用してリハイドレートする方法の詳細については、「[アーカイブ済み BLOB をオンライン層にコピーする](archive-rehydrate-overview.md#copy-an-archived-blob-to-an-online-tier)」を参照してください。

アーカイブ層から標準の優先度を持つオンライン層へのコピー操作を実行するには、PowerShell、Azure CLI、またはいずれかの Azure Storage クライアント ライブラリを使用します。 詳細については、「[コピー操作を使用して BLOB をリハイドレートする](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-with-a-copy-operation)」を参照してください。 次に、保留中のリハイドレートについて、リハイドレートの優先度を "*標準*" から "*高*" に変更するには、コピー先 BLOB に対して **[BLOB 層の設定]** を呼び出して、ターゲット層を指定します。

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

コピー操作を開始すると、コピー元とコピー先の両方の BLOB がアーカイブ層にあることが Azure portal に表示されます。 コピー先 BLOB は、標準の優先度でリハイドレートされます。

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-properties-portal-standard-priority.png" alt-text="アーカイブ層にあるコピー先 BLOB と、優先度が標準になっているリハイドレートを示すスクリーンショット":::

コピー先 BLOB のリハイドレートの優先度を変更するには、次の手順を行います。

1. コピー先 BLOB を選択します。
1. **[層の変更]** ボタンを選択します。
1. **[層の変更]** ダイアログで、リハイドレートする BLOB のアクセス層をターゲットのオンライン アクセス層に設定します (ホットまたはクール)。 **[アーカイブの状態]** フィールドには、ターゲットのオンライン層が表示されます。
1. **[リハイドレートの優先度]** ドロップダウンで、優先度を "*高*" に設定します。
1. **[保存]** を選択します。

コピー先 BLOB のプロパティページに、優先度が高のリハイドレートが表示されるようになりました。

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-properties-portal-high-priority.png" alt-text="アーカイブ層にあるコピー先 BLOB と優先度が高になっているリハイドレートを示すスクリーンショット":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

コピー操作を開始したら、コピー先 BLOB のプロパティを確認します。 コピー先 BLOB がアーカイブ層にあって、標準の優先度でリハイドレートされていることがわかります。

```azurepowershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$destContainerName = "<container>"
$destBlobName = "<destination-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Get properties for the destination blob.
$destinationBlob = Get-AzStorageBlob -Container $destContainerName `
    -Blob $destBlobName `
    -Context $ctx

$destinationBlob.BlobProperties.AccessTier
$destinationBlob.BlobProperties.ArchiveStatus
$destinationBlob.BlobProperties.RehydratePriority
```

次に、PowerShell を介して **SetAccessTier** 呼び出して、コピー先 BLOB のリハイドレートの優先度を "*高*" に変更します。「[保留中の [BLOB 層の設定] 操作についてリハイドレートの優先度を変更する](#change-the-rehydration-priority-for-a-pending-set-blob-tier-operation)」を参照してください。 ターゲット層 (ホットまたはクール) は、リハイドレート操作に対して元々指定してある層と同じものにする必要があります。 プロパティをもう一度確認して、BLOB が高優先度でリハイドレートされるようになっていることを確認します。

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

コピー操作を開始したら、コピー先 BLOB のプロパティを確認します。 コピー先 BLOB がアーカイブ層にあって、標準の優先度でリハイドレートされていることがわかります。

```azurecli
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

次に、`--rehydrate-priority` パラメーターが "*高*" に設定された [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) コマンドを呼び出します。「[保留中の [BLOB 層の設定] 操作についてリハイドレートの優先度を変更する](#change-the-rehydration-priority-for-a-pending-set-blob-tier-operation)」を参照してください。 ターゲット層 (ホットまたはクール) は、リハイドレート操作に対して元々指定してある層と同じものにする必要があります。 プロパティをもう一度確認して、BLOB が高優先度でリハイドレートされるようになっていることを確認します。

---

## <a name="see-also"></a>関連項目

- [BLOB データのホット、クール、およびアーカイブ アクセス層](access-tiers-overview.md)。
- [アーカイブ層からの BLOB のリハイドレートの概要](archive-rehydrate-overview.md)
- [BLOB リハイドレート イベントに応答して Azure 関数を実行する](archive-rehydrate-handle-event.md)
- [Blob Storage のイベント処理](storage-blob-event-overview.md)
