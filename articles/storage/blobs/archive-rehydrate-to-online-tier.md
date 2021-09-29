---
title: アーカイブ済み BLOB をオンライン層にリハイドレートする
titleSuffix: Azure Storage
description: アーカイブ層にある BLOB を読み取る前に、ホット層またはクール層にリハイドレートする必要があります。 BLOB は、アーカイブ層からオンライン層にコピーするか、アーカイブ層からホット層またはクール層に変更することでリハイドレートできます。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/25/2021
ms.author: tamram
ms.reviewer: fryu
ms.custom: devx-track-azurepowershell
ms.subservice: blobs
ms.openlocfilehash: a1681640b97d6ee183eefd7f65edd9b2d944f0f2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128633870"
---
# <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>アーカイブ済み BLOB をオンライン層にリハイドレートする

アーカイブ層にある BLOB を読み取るには、最初に BLOB をホット層またはクール層にリハイドレートする必要があります。 BLOB は、次の 2 つの方法のいずれかを使用してリハイドレートできます。

- [BLOB のコピー](/rest/api/storageservices/copy-blob)または [URL からの BLOB のコピー](/rest/api/storageservices/copy-blob-from-url)操作でホット層またはクール層に新しい BLOB をコピーする。 Microsoft ではほとんどのシナリオでこのオプションをお勧めします。
- [BLOB 層の設定](/rest/api/storageservices/set-blob-tier)操作で層をアーカイブからホットまたはクールに変更する。

リハイドレート操作が完了するまでに最大 15 時間かかる場合があります。 Azure Event Grid を構成してリハイドレートが完了するときにイベントを起動し、応答としてアプリケーション コードを実行できます。 BLOB リハイドレート操作が完了したときに Azure 関数を実行するイベントを処理する方法については、「[BLOB リハイドレート イベントに応答して Azure 関数を実行する](archive-rehydrate-handle-event.md)」を参照してください。

## <a name="rehydrate-a-blob-with-a-copy-operation"></a>コピー操作を使用して BLOB をリハイドレートする

アーカイブ層をオンライン層にコピーしてアーカイブ層から BLOB をリハイドレートするには、PowerShell、Azure CLI、または Azure Storage クライアント ライブラリのいずれかを使用します。 アーカイブ済み BLOB をオンライン層にコピーする場合、ソース BLOB とコピー先 BLOB の名前は異なる必要があります。

コピー操作が完了すると、コピー先 BLOB がアーカイブ層に表示されます。 コピー先 BLOB は、コピー操作で指定したオンライン層にリハイドレートされます。 コピー先 BLOB が完全にリハイドレートされた場合、新しいオンライン層で使用できます。

次の例は、PowerShell または Azure CLI を使用してアーカイブ済み BLOB をコピーする方法を示します。

### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

該当なし

### <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用してアーカイブ済み BLOB をオンライン層にコピーするには [Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) コマンドを呼び出し、ターゲット層とリハイドレートの優先度を指定します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```powershell
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

# Copy the source blob to a new destination blob in hot tier with standard priority.
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

アーカイブからホットまたはクールの操作に層を変更して BLOB をリハイドレートするには、Azure portal、PowerShell、または Azure CLI を使用します。

### <a name="azure-portal"></a>[Azure portal](#tab/portal)

Azure portal で BLOB の層をアーカイブからホットまたはクールに変更するには、次の手順に従います。

1. Azure portal でリハイドレートする BLOB を検索します。
1. ページの右側にある **[すべて表示]** ボタンを選択します。
1. **[層の変更]** を選択します。
1. **[アクセス層]** ドロップダウンからターゲット アクセス層を選択します。
1. **[リハイドレートの優先度]** ドロップダウンから、必要なリハイドレートの優先度を選択します。 通常、リハイドレートの優先度を *高* に設定すると、リハイドレートが速くなりますが、コストも高くなります。

    :::image type="content" source="media/archive-rehydrate-to-online-tier/rehydrate-change-tier-portal.png" alt-text="Azure portal でアーカイブ層から BLOB をリハイドレートする方法を示すスクリーンショット":::

1. **[保存]** を選択します。

### <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell で BLOB の層をアーカイブからホットまたはクールに変更するには、BLOB の **ICloudBlob** プロパティを使用して .NET リファレンスを BLOB に返してから、そのリファレンスで **SetStandardBlobTier** メソッドを呼び出します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```powershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container>"
$blobName = "<archived-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Change the blob's access tier to hot with standard priority.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Hot", $null, "High")
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI で BLOB の層をアーカイブからホットまたはクールに変更するには、[az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) コマンドを呼び出します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```azurecli
az storage blob set-tier \
    --container-name <container> \
    --name <archived-blob> \
    --tier Hot \
    --account-name <account-name> \
    --rehydrate-priority High \
    --auth-mode login
```

---

## <a name="rehydrate-a-large-number-of-blobs"></a>多数の BLOB をリハイドレートする

多数の BLOB を一度にリハイドレートするには、[BLOB バッチ](/rest/api/storageservices/blob-batch)操作を呼び出し、一括操作として [BLOB 層の設定](/rest/api/storageservices/set-blob-tier)を呼び出します。 バッチ操作の実行方法を示すコード例については、「[AzBulkSetBlobTier](/samples/azure/azbulksetblobtier/azbulksetblobtier/)」を参照してください。

## <a name="check-the-status-of-a-rehydration-operation"></a>リハイドレート操作の状態を確認する

BLOB のリハイドレート中、Azure portal、PowerShell、または Azure CLI を使用して、その状態とリハイドレートの優先度を確認できます。 状態プロパティは、リハイドレート層のターゲット層に応じて *rehydrate-pending-to-hot* または *rehydrate-pending-to-cool* を返します。 リハイドレートの優先度プロパティは *標準* または *高* を返します。

アーカイブされた BLOB のリハイドレートには最大 15 時間かかる場合があります。また、リハイドレートが完了したかどうかを判断するために BLOB の状態を繰り返しポーリングする処理は非効率的です。 Azure Event Grid を使用して、リハイドレートが完了すると発生するイベントをキャプチャすると、パフォーマンスが向上し、コストが最適化されます。 BLOB リハイドレートでイベントが発生した場合に Azure 関数を実行する方法については、「[BLOB のリハイドレート イベントに応答して Azure 関数を実行する](archive-rehydrate-handle-event.md)」を参照してください。

### <a name="azure-portal"></a>[Azure portal](#tab/portal)

Azure portal で保留中のリハイドレート操作の状態と優先度を確認するには、BLOB の **[層の変更]** ダイアログを表示します。

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-status-portal.png" alt-text="Azure portal で BLOB のリハイドレートの状態を示すスクリーンショット":::

リハイドレートが完了すると、Azure portal では完全にリハイドレートされた BLOB がターゲットのオンライン層に表示されます。

:::image type="content" source="media/archive-rehydrate-to-online-tier/set-blob-tier-rehydrated.png" alt-text="クール層のリハイドレートされた BLOB およびイベント ハンドラーによって書き込まれたログ BLOB を示すスクリーンショット":::

### <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell で保留中のリハイドレート操作の状態と優先度を確認するには、[Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) コマンドを呼び出し、BLOB の **ArchiveStatus** および **RehydratePriority** プロパティを確認します。 リハイドレートがコピー操作の場合は、コピー先 BLOB でこれらのプロパティを確認します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```powershell
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
    --name <destination-blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

---

## <a name="see-also"></a>関連項目

- [Azure Blob Storage: ホット、クール、アーカイブ アクセス層](storage-blob-storage-tiers.md)。
- [アーカイブ層からの BLOB のリハイドレートの概要](archive-rehydrate-overview.md)
- [BLOB リハイドレート イベントに応答して Azure 関数を実行する](archive-rehydrate-handle-event.md)
- [Blob Storage のイベント処理](storage-blob-event-overview.md)
