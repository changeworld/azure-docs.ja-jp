---
title: BLOB のアーカイブ
titleSuffix: Azure Storage
description: アーカイブ アクセス層に BLOB を作成する方法、または既存の BLOB をアーカイブ アクセス層に移動する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/25/2021
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: d83cb752eaaa2ed3edf58d3d1f31d6d7c5af76cd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433139"
---
# <a name="archive-a-blob"></a>BLOB のアーカイブ

アーカイブ アクセス層は、めったにアクセスしない BLOB データを保存するためのオフライン層です。 アーカイブ アクセス層は保存コストが最も安いですが、オンライン層 (ホットおよびクール) と比べてデータ取得コストが高く、待機時間が長いです。 データは、少なくとも 180 日間、アーカイブ アクセス層に保持される必要があります。そうでない場合、早期削除料金の対象になります。 アーカイブ アクセス層の詳細については、「[アーカイブ アクセス層](access-tiers-overview.md#archive-access-tier)」を参照してください。

BLOB は、アーカイブ アクセス層にある間は、読み取りも変更もできません。 アーカイブ アクセス層の BLOB を読み取る、またはダウンロードするには、まずオンライン層 (ホットまたはクール アクセス層) にリハイドレートする必要があります。 アーカイブ アクセス層のデータは、リハイドレート操作に指定した優先度に応じて、リハイドレートするのに最大 15 時間かかる場合があります。 BLOB のリハイドレートの詳しい情報は、「[アーカイブ層からの BLOB のリハイドレートの概要](archive-rehydrate-overview.md)」をご覧ください。

> [!CAUTION]
> アーカイブ アクセス層の BLOB はオフラインです。つまり、リハイドレートされるまで読み取りや変更を行うことはできません。 リハイドレート プロセスには数時間かかり、コストがかかります。 アーカイブ アクセス層にデータを移動する前に、BLOB データをオフラインにすることがワークフローに影響しないか検討してください。

Azure portal、PowerShell、Azure CLI、またはいずれかの Azure Storage クライアント ライブラリを使用して、データのアーカイブを管理できます。

## <a name="archive-blobs-on-upload"></a>アップロード時に BLOB をアーカイブする

アップロード時に 1 つ以上の BLOB をアーカイブするには、アーカイブ アクセス層に BLOB を直接作成します。

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal からアップロード時に BLOB または BLOB のセットをアーカイブするには、次の手順を実行します。

1. ターゲット コンテナーに移動します。
1. **[アップロード]** ボタンを選択します。
1. アップロードするファイルを選択します。
1. **[詳細設定]** セクションを展開し、 **[アクセス層]** を *[アーカイブ]* に設定します。
1. **[アップロード]** ボタンを選択します。

    :::image type="content" source="media/archive-blob/upload-blobs-archive-portal.png" alt-text="Azure portal で BLOB をアーカイブ アクセス層にアップロードする方法を示すスクリーンショット":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して BLOB または BLOB のセットをアーカイブするには、次の例に示すように [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) コマンドを呼び出します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurepowershell
$rgName = <resource-group>
$storageAccount = <storage-account>
$containerName = <container>

# Get context object
$ctx = New-AzStorageContext -StorageAccountName $storageAccount -UseConnectedAccount

# Create new container.
New-AzStorageContainer -Name $containerName -Context $ctx

# Upload a single file named blob1.txt to the Archive tier.
Set-AzStorageBlobContent -Container $containerName `
    -File "blob1.txt" `
    -Blob "blob1.txt" `
    -Context $ctx `
    -StandardBlobTier Archive

# Upload the contents of a sample-blobs directory to the Archive tier, recursively.
Get-ChildItem -Path "C:\sample-blobs" -File -Recurse | 
    Set-AzStorageBlobContent -Container $containerName `
        -Context $ctx `
        -StandardBlobTier Archive
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してアップロード時に単一の BLOB をアーカイブするには、次の例に示すように [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) コマンドを呼び出します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --file <file> \
    --tier Archive \
    --auth-mode login
```

Azure CLI を使用してアップロード時に BLOB のセットをアーカイブするには、次の例に示すように [az storage blob upload-batch](/cli/azure/storage/blob#az_storage_blob_upload_batch) コマンドを呼び出します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli
az storage blob upload-batch \
    --destination <container> \
    --source <source-directory> \
    --account-name <storage-account> \
    --tier Archive \
    --auth-mode login
```

---

## <a name="archive-an-existing-blob"></a>既存の BLOB をアーカイブする

既存の BLOB をアーカイブ アクセス層に移動するには、次の 2 つの方法があります。

- [Set Blob Tier](/rest/api/storageservices/set-blob-tier) (BLOB の層を設定) 操作を使用して、BLOB のアクセス層を変更できます。 **Set Blob Tier** によって、1 つの BLOB が 1 つの階層から別の階層に移動します。

    **Set Blob Tier** で BLOB をアーカイブ アクセス層に移動する場合、BLOB をリハイドレートするまで BLOB のデータの読み取りや変更ができないことに注意してください。 早期削除期間が経過する前に BLOB のデータを読み取りまたは変更する必要がある場合は、**Copy Blob** 操作を使用して、BLOB のコピーをアーカイブ アクセス層に作成することを検討してください。

- [Copy Blob](/rest/api/storageservices/copy-blob) (BLOB のコピー) 操作を使用して、オンライン層の BLOB をアーカイブ アクセス層にコピーできます。 **Copy Blob** (BLOB のコピー) 操作を呼び出して、オンライン層 (ホットまたはクール) からアーカイブ アクセス層に BLOB をコピーすることができます。 ソース BLOB はオンライン層に残り、オンライン層でデータの読み取りまたは変更を続けることができます。

### <a name="archive-an-existing-blob-by-changing-its-tier"></a>層を変更して既存の BLOB をアーカイブする

**Set Blob Tier** (BLOB の層を設定) 操作を使用して、BLOB をホット層またはクール層からアーカイブ アクセス層に移動します。 **Set Blob Tier** 操作は、早期削除期間が経過する前にアーカイブされたデータにアクセスする必要がないシナリオに最適です。

**Set Blob Tier** 操作では、1 つの BLOB の層が変更されます。 最適なパフォーマンスで一連の BLOB をアーカイブ アクセス層に移動するには、一括アーカイブ操作を実行することをお勧めします。 一括アーカイブ操作では、1 つのトランザクションで、**Set Blob Tier** 呼び出しのバッチをサービスに送信します。 詳細については、「[一括アーカイブ](#bulk-archive)」を参照してください。  

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal で既存の BLOB をアーカイブ アクセス層に移動するには、次の手順を実行します。

1. BLOB のコンテナーに移動します。
1. アーカイブする BLOB を選択します。
1. **[層の変更]** ボタンを選択します。
1. **[アクセス層]** ドロップダウンから *[アーカイブ]* を選択します。
1. **[保存]** を選択します。

    :::image type="content" source="media/archive-blob/set-blob-tier-archive-portal.png" alt-text="Azure portal で BLOB の層をアーカイブに変更する方法を示すスクリーンショット":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して BLOB の階層をアーカイブからホットまたはクールに変更するには、BLOB の **BlobClient** プロパティを使用して、BLOB への .NET 参照を取得し、その参照で **SetAccessTier** メソッドを呼び出します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

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

# Change the blob's access tier to Archive.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Archive", $null)
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI で BLOB の層をホットまたはクールからアーカイブに変更するには、[az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) コマンドを呼び出します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```azurecli
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --tier Archive \
    --auth-mode login
```

---

### <a name="archive-an-existing-blob-with-a-copy-operation"></a>コピー操作により既存の BLOB をアーカイブする

[Copy Blob](/rest/api/storageservices/copy-blob) 操作を使用して、ホットまたはクール層からアーカイブ アクセス層に BLOB をコピーします。 ソース BLOB はホットまたはクール層に残り、コピー先の BLOB がアーカイブ アクセス層に作成されます。

**Copy Blob** 操作は、早期削除期間が経過する前にアーカイブされたデータを読み取りまたは変更することが必要な可能性があるシナリオに最適です。 アーカイブされた BLOB をリハイドレートしなくても、ソース BLOB のデータにアクセスできます。

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

該当なし

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して BLOB をオンライン層からアーカイブ アクセス層にコピーするには、[Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) コマンドを呼び出し、アーカイブ アクセス層を指定します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

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
    -StandardBlobTier Archive `
    -Context $ctx
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して BLOB をオンライン層からアーカイブ アクセス層にコピーするには、[az storage blob copy start](/cli/azure/storage/blob/copy#az_storage_blob_copy_start) コマンドを呼び出し、アーカイブ アクセス層を指定します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

```azurecli
az storage blob copy start \
    --source-container <source-container> \
    --source-blob <source-blob> \
    --destination-container <dest-container> \
    --destination-blob <dest-blob> \
    --account-name <storage-account> \
    --tier Archive \
    --auth-mode login
```

---

## <a name="bulk-archive"></a>一括アーカイブ

多数の BLOB をアーカイブ アクセス層に移動する場合は、最適なパフォーマンスを得るためにバッチ操作を使用します。 バッチ操作では、1 つの要求で複数の API 呼び出しがサービスに送信されます。 [Blob Batch](/rest/api/storageservices/blob-batch) 操作でサポートされるサブ操作には、[Delete Blob](/rest/api/storageservices/delete-blob) と [Set Blob Tier](/rest/api/storageservices/set-blob-tier) があります。

バッチ操作で BLOB をアーカイブするには、Azure Storage クライアント ライブラリのいずれかを使用します。 次のコード例は、.NET クライアント ライブラリで基本的なバッチ操作を実行する方法を示しています。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/AccessTiers.cs" id="Snippet_BulkArchiveContainerContents":::

バッチ操作で層を変更する方法を示す詳細なサンプル アプリケーションについては、「[AzBulkSetBlobTier](/samples/azure/azbulksetblobtier/azbulksetblobtier/)」を参照してください。

## <a name="see-also"></a>関連項目

- [BLOB データのホット、クール、アーカイブ アクセス層](access-tiers-overview.md)
- [アーカイブ アクセス層からの BLOB のリハイドレート](archive-rehydrate-overview.md)
- [アーカイブ済み BLOB をオンライン層にリハイドレートする](archive-rehydrate-to-online-tier.md)
