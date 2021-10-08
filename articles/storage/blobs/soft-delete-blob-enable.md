---
title: BLOB の論理的な削除の有効化
titleSuffix: Azure Storage
description: BLOB の論理的な削除を有効にして、不注意による削除や上書きから BLOB データを保護します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5d5dbb4b3be5eec500a7a0845a9dbc568a113ca3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599109"
---
# <a name="enable-soft-delete-for-blobs"></a>BLOB の論理的な削除の有効化

BLOB の論理的な削除を使用すると、削除されたデータがシステムに一定の期間保有されることにより、個々の BLOB とその複数バージョン、スナップショット、およびメタデータが誤った削除や上書きから保護されます。 保持期間中は、BLOB を削除時の状態に復元できます。 保持期間が過ぎると、BLOB オブジェクトは完全に削除されます。 BLOB の論理的な削除の詳細については、「[BLOB の論理的な削除](soft-delete-blob-overview.md)」を参照してください。

BLOB の論理的な削除は、BLOB データの包括的なデータ保護戦略の一部です。 データ保護に関する Microsoft の推奨事項の詳細については、「[データ保護の概要](data-protection-overview.md)」を参照してください。

> [!NOTE]
> BLOB の論理的な削除では、階層型名前空間機能が有効になったアカウントの BLOB とディレクトリも保護されます。 階層型名前空間機能が有効になっているアカウントでの BLOB の論理的な削除は、現在パブリック プレビュー段階であり、すべての Azure リージョンでグローバルに利用できます。

新しいストレージ アカウントでは、BLOB の論理的な削除は既定で無効になっています。 ストレージ アカウントでの論理的な削除は、Azure portal、PowerShell、または Azure CLI を使用して、いつでも有効と無効を切り替えることができます。

## <a name="enable-blob-soft-delete"></a>BLOB の論理的な削除を有効にする

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用してストレージ アカウントでの BLOB の論理的な削除を有効にするには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) のストレージ アカウントに移動します。
1. **[Blob service]** の下の **[データ保護]** オプションを見つけます。
1. **[Recovery]\(回復\)** セクションで、 **[Turn on soft delete for blobs]\(BLOB の論理的な削除を有効にする\)** を選択します。
1. 1 日から 365 日の間で保有期間を指定します。 Microsoft では、少なくとも 7 日間は保有することをお勧めします。
1. 変更内容を保存します。

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Azure portal で論理的な削除を有効にする方法を示すスクリーンショット":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-CLI)

Azure CLI を使用して BLOB の論理的な削除を有効にするには、[az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) コマンドを呼び出し、保有期間を日数で指定します。

次の例では、BLOB の論理的な削除を有効にし、保有期間を 7 日間に設定しています。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

BLOB の論理的な削除の現在の設定を確認するには、[az storage account blob-service-properties show](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_show) コマンドを呼び出します。

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="enable-blob-soft-delete-hierarchical-namespace"></a>BLOB の論理的な削除を有効にする (階層型名前空間)

BLOB の論理的な削除では、階層型名前空間機能が有効になったアカウントの BLOB とディレクトリも保護されます。

> [!IMPORTANT]
> 階層型名前空間機能が有効になっているアカウントでの論理的な削除は、現在プレビュー段階であり、すべての Azure リージョンでグローバルに利用できます。
> ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。
>
>
> プレビューに登録するには、[こちらのフォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pUOVRVOUpDRUtHVUtDUUtMVTZUR0tUMjZWNy4u)を参照してください。

<a id="enable-blob-soft-delete-hierarchical-namespace"></a>

### <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用してストレージ アカウントでの BLOB の論理的な削除を有効にするには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) のストレージ アカウントに移動します。
1. **[データ管理]** の **[データ保護]** オプションを探します。
1. **[Recovery]\(回復\)** セクションで、 **[Enable soft delete for blobs]\(BLOB の論理的な削除を有効にする\)** を選択します。
1. 1 日から 365 日の間で保有期間を指定します。 Microsoft では、少なくとも 7 日間は保有することをお勧めします。
1. 変更内容を保存します。

> [!div class="mx-imgBorder"]
> ![階層型名前空間が有効になったアカウントでの論理的な削除を Azure portal で有効にする方法を示すスクリーンショット。](./media/soft-delete-blob-enable/blob-soft-delete-configuration-portal-hierarchical-namespace.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 最新の **PowershellGet** モジュールをインストールします。 次に、PowerShell コンソールを閉じてから再度開きます。

    ```powershell
    install-Module PowerShellGet -Repository PSGallery -Force
    ```

2. **Az.Storage** プレビュー モジュールをインストールします。

    ```powershell
    Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.7.1-preview -AllowClobber -AllowPrerelease -Force
    ```

    PowerShell モジュールのインストール方法の詳細については、「[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)」を参照してください。

3. ストレージ アカウント キー、接続文字列、Azure Active Directory (Azure AD) のいずれかを使用して、ストレージ アカウントの承認を取得します。 詳細については、「[アカウントに接続する](data-lake-storage-directory-file-acl-powershell.md#connect-to-the-account)」を参照してください。

   次の例では、ストレージ アカウント キーを使用して承認を取得します。

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
   ```

4. PowerShell を使用して BLOB の論理的な削除を有効にするには、[Enable-AzStorageDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstoragedeleteretentionpolicy) コマンドを使用し、保有期間を日数で指定します。

   次の例では、アカウントの論理的な削除を有効にし、保有期間を 4 日間に設定しています。

   ```powershell
   Enable-AzStorageDeleteRetentionPolicy -RetentionDays 4  -Context $ctx
   ```

5. BLOB の論理的な削除の現在の設定を確認するには、`Get-AzStorageServiceProperty` コマンドを使用します。

   ```powershell
    Get-AzStorageServiceProperty -ServiceType Blob -Context $ctx
   ```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-CLI)

1. [Azure Cloud Shell](../../cloud-shell/overview.md) を開きます。または、Azure CLI をローカルに[インストール](/cli/azure/install-azure-cli)した場合は、Windows PowerShell などのコマンド コンソール アプリケーションを開きます。

2. `storage-preview` 拡張機能をインストールします。

   ```azurecli
   az extension add -n storage-preview
   ```

3. ストレージ アカウントに接続します。 詳細については、「[アカウントに接続する](data-lake-storage-directory-file-acl-cli.md#connect-to-the-account)」を参照してください。

   > [!NOTE]
   > この記事に示す例は、Azure Active Directory (Azure AD) 認証を示しています。 認証方法の詳細については、「[Azure CLI を使用して BLOB またはキュー データへのアクセスを承認する](./authorize-data-operations-cli.md)」を参照してください。

4. Azure CLI を使用して論理的な削除を有効にするには、`az storage fs service-properties update` コマンドを呼び出し、保有期間を日数で指定します。

   次の例では、BLOB とディレクトリの論理的な削除を有効にし、保有期間を 5 日間に設定しています。

   ```azurecli
   az storage fs service-properties update --delete-retention --delete-retention-period 5 --auth-mode login
   ```

5. BLOB の論理的な削除の現在の設定を確認するには、`az storage fs service-properties update` コマンドを呼び出します。

   ```azurecli
   az storage fs service-properties update --delete-retention false --connection-string $con
   ```

---

## <a name="next-steps"></a>次の手順

- [BLOB の論理的な削除](soft-delete-blob-overview.md)
- [論理的に削除された BLOB を管理および復元する](soft-delete-blob-manage.md)
