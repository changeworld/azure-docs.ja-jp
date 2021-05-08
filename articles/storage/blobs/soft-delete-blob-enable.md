---
title: BLOB の論理的な削除の有効化
titleSuffix: Azure Storage
description: BLOB の論理的な削除を有効にして、不注意による削除や上書きから BLOB データを保護します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 11323f2aec05935b9dc45187ed54597e61af924d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554121"
---
# <a name="enable-soft-delete-for-blobs"></a>BLOB の論理的な削除の有効化

BLOB の論理的な削除を使用すると、削除されたデータがシステムに一定の期間保有されることにより、個々の BLOB とその複数バージョン、スナップショット、およびメタデータが誤った削除や上書きから保護されます。 保有期間中は、BLOB を削除時の状態に復元できます。 保有期間が過ぎると、BLOB は完全に削除されます。 BLOB の論理的な削除の詳細については、「[BLOB の論理的な削除](soft-delete-blob-overview.md)」を参照してください。

BLOB の論理的な削除は、BLOB データの包括的なデータ保護戦略の一部です。 データ保護に関する Microsoft の推奨事項の詳細については、「[データ保護の概要](data-protection-overview.md)」を参照してください。

## <a name="enable-blob-soft-delete"></a>BLOB の論理的な削除を有効にする

新しいストレージ アカウントでは、BLOB の論理的な削除は既定で無効になっています。 ストレージ アカウントでの論理的な削除は、Azure portal、PowerShell、または Azure CLI を使用して、いつでも有効と無効を切り替えることができます。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用してストレージ アカウントでの BLOB の論理的な削除を有効にするには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) のストレージ アカウントに移動します。
1. **[Blob service]** の下の **[データ保護]** オプションを見つけます。
1. **[Recovery]\(回復\)** セクションで、 **[Turn on soft delete for blobs]\(BLOB の論理的な削除を有効にする\)** を選択します。
1. 1 日から 365 日の間で保有期間を指定します。 Microsoft では、少なくとも 7 日間は保有することをお勧めします。
1. 変更内容を保存します。

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Azure portal で論理的な削除を有効にする方法を示すスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用して BLOB の論理的な削除を有効にするには、[Enable-AzStorageBlobDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) コマンドを呼び出し、保有期間を日数で指定します。

次の例では、BLOB の論理的な削除を有効にし、保有期間を 7 日間に設定しています。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

BLOB の論理的な削除の現在の設定を確認するには、[Get-AzStorageBlobServiceProperty](/powershell/module/az.storage/get-azstorageblobserviceproperty) コマンドを呼び出します。

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Azure CLI を使用して BLOB の論理的な削除を有効にするには、[az storage account blob-service-properties update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) コマンドを呼び出し、保有期間を日数で指定します。

次の例では、BLOB の論理的な削除を有効にし、保有期間を 7 日間に設定しています。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

BLOB の論理的な削除の現在の設定を確認するには、[az storage account blob-service-properties show](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_show) コマンドを呼び出します。

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>次の手順

- [BLOB の論理的な削除](soft-delete-blob-overview.md)
- [論理的に削除された BLOB を管理および復元する](soft-delete-blob-manage.md)
