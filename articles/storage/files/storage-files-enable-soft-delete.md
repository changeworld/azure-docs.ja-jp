---
title: 論理的な削除を有効にする - Azure ファイル共有
description: データを復旧したり誤削除を回避したりできるように、Azure ファイル共有に対して論理的な削除を有効にする方法について説明します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
services: storage
ms.openlocfilehash: 314d1ee8ee957b21679735594ad2f7d7f50f4d38
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2021
ms.locfileid: "112118321"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Azure ファイル共有で論理的な削除を有効にする
Azure Files では、アプリケーションまたは他のストレージ アカウント ユーザーによってデータが誤って削除されたときに、データをより簡単に復旧できるように、ファイル共有での論理的な削除が提供されています。 論理的な削除の詳細については、[Azure ファイル共有の誤削除を回避する方法](storage-files-prevent-file-share-deletion.md)に関するページをご覧ください。

## <a name="applies-to"></a>適用対象
| ファイル共有の種類 | SMB | NFS |
|-|:-:|:-:|
| Standard ファイル共有 (GPv2)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Standard ファイル共有 (GPv2)、GRS/GZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Premium ファイル共有 (FileStorage)、LRS/ZRS | ![はい](../media/icons/yes-icon.png) | ![いいえ](../media/icons/no-icon.png) |

## <a name="prerequisites"></a>前提条件
- Azure PowerShell を使用する場合は、[最新バージョンをインストールしてください](/powershell/azure/install-az-ps)。
- Azure CLI を使用する場合は、[最新バージョンをインストールしてください](/cli/azure/install-azure-cli)。

## <a name="getting-started"></a>はじめに
以下のセクションでは、既存のストレージ アカウントで Azure ファイル共有の論理的な削除を有効にして使用する方法について説明します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. ストレージ アカウントに移動し、 **[データ ストレージ]** の下にある **[ファイル共有]** を選択します。
1. **[論理的な削除]** の横の **[有効]** を選択します。
1. **[Soft delete for all file shares]\(すべてのファイル共有の論理的な削除\)** に対して **[有効]** を選択します。
1. **[File share retention period in days]\(ファイル共有の保有期間の日数\)** を選択し、任意の数を入力します。
1. **[保存]** を選択し、データ保有設定を確認します。

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-enable-soft-delete-new-ui.png" alt-text="ストレージ アカウントの論理的な削除の設定ペインのスクリーンショット。[ファイル共有] の [論理的な削除] セクション、有効化トグル、[set a retention period]\(保有期間を設定\)、[保存] が強調表示されています。これにより、ストレージ アカウント内のすべてのファイル共有に対して論理的な削除が有効になります。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
論理的な削除を有効にするには、すべての Azure ファイル共有の設定 (別名 `FileService` プロパティ) を更新する必要があります。 次の例では、ストレージ アカウント内のすべてのファイル共有に対して論理的な削除が有効になります。 忘れずに、`<resource-group>` および `<storage-account>` を実際の環境の適切な値に置き換えてください。

```PowerShell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"

Update-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -EnableShareDeleteRetentionPolicy $true `
    -ShareRetentionDays 7
```

次のコマンドを使用すると、論理的な削除が有効になっているかどうかを確認して、その保有ポリシーを表示できます。

```PowerShell
Get-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
論理的な削除を有効にするには、ファイル クライアントのサービスのプロパティを更新する必要があります。 次の例では、ストレージ アカウント内のすべてのファイル共有に対して論理的な削除が有効になります。 忘れずに、`<resource-group>` および `<storage-account>` を実際の環境の適切な値に置き換えてください。

```bash
resourceGroupName="<resource-group>"
storageAccountName="<storage-account>"

az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --enable-delete-retention true \
    --delete-retention-days 7
```

次のコマンドを使用すると、論理的な削除が有効になっているかどうかを確認して、その保有ポリシーを表示できます。

```bash
az storage account file-service-properties show \
    -resource-group $resourceGroupName \
    -account-name $storageAccountName
```
---

## <a name="restore-soft-deleted-file-share"></a>論理的に削除されたファイル共有を復元する

# <a name="portal"></a>[ポータル](#tab/azure-portal)
論理的に削除されたファイル共有を復元するには、次のようにします。

1. ストレージ アカウントに移動し、 **[ファイル共有]** を選択します。
1. [ファイル共有] ブレードで、 **[Show deleted shares]\(削除された共有を表示\)** を有効にして、論理的に削除されているすべての共有を表示します。

    これにより、現在 **[削除済み]** の状態になっているすべての共有が表示されます。

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="[状態] 列 ([名前] 列の隣の列) が [削除済み] に設定されている場合、ファイル共有は論理的に削除された状態になっています。また、指定した保有期間が経過すると、完全に削除されます。":::

1. 共有を選択し、 **[削除の取り消し]** を選択すると、共有が復元されます。

    共有の状態が **[アクティブ]** に切り替わるので、共有が復元されたことを確認できます。

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="[状態] 列 ([名前] 列の隣の列) が [アクティブ] に設定されている場合、ファイル共有は復元されています。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
論理的に削除されたファイル共有を復元するには、まず共有の `-DeletedShareVersion` 値を取得する必要があります。 この値を取得するには、次のコマンドを使用して、ストレージ アカウントの削除されたすべての共有を一覧表示します。

```PowerShell
Get-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -IncludeDeleted
```

復元する共有を特定したら、次のコマンドでそれを使用して復元できます。

```PowerShell
Restore-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -DeletedShareVersion 01D5E2783BDCDA97 # replace with your deleted version number
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
論理的に削除されたファイル共有を復元するには、まず共有の `--deleted-version` 値を取得する必要があります。 この値を取得するには、次のコマンドを使用して、ストレージ アカウントの削除されたすべての共有を一覧表示します。

```bash
az storage share-rm list \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --include-deleted
```

復元する共有を特定したら、次のコマンドでそれを使用して復元できます。

```bash
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

---

## <a name="disable-soft-delete"></a>論理的な削除の無効化
論理的な削除の使用を停止する場合は、こちらの手順に従います。 論理的に削除されたファイル共有を完全に削除するには、その削除を取り消し、論理的な削除を無効にしてから、それをもう一度削除する必要があります。 

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. ストレージ アカウントに移動し、 **[データ ストレージ]** の下にある **[ファイル共有]** を選択します。
1. **[論理的な削除]** の横にあるリンクを選択します。
1. **[Soft delete for all file shares]\(すべてのファイル共有の論理的な削除\)** に対して **[無効]** を選択します。
1. **[保存]** を選択し、データ保有設定を確認します。

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-disable-soft-delete.png" alt-text="論理的な削除を無効にすると、ストレージ アカウント内のすべてのファイル共有を、必要なときにすぐ完全に削除することができます。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
次のコマンドを使用して、ストレージ アカウントで論理的な削除を無効にすることができます。

```PowerShell
Update-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -EnableShareDeleteRetentionPolicy $false
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
次のコマンドを使用して、ストレージ アカウントで論理的な削除を無効にすることができます。

```bash
az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --enable-delete-retention false
```

---

## <a name="next-steps"></a>次のステップ
別の形式のデータ保護と復旧の詳細については、「[Azure Files の共有スナップショットの概要](storage-snapshots-files.md)」記事を参照してください。
