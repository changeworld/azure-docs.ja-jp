---
title: Azure Storage BLOB のインベントリ レポートを有効にする
description: ストレージ アカウント内のご自身のコンテナー、BLOB、スナップショット、BLOB バージョンの概要を取得します。
services: storage
author: normesta
ms.service: storage
ms.date: 08/16/2021
ms.topic: how-to
ms.author: normesta
ms.reviewer: klaasl
ms.subservice: blobs
ms.openlocfilehash: ace430aa1e48d7e769f82d7258da241a0716a16d
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455647"
---
# <a name="enable-azure-storage-blob-inventory-reports"></a>Azure Storage BLOB のインベントリ レポートを有効にする

Azure Storage BLOB インベントリ機能により、ストレージ アカウント内のご自身のコンテナー、BLOB、スナップショット、BLOB バージョンの概要が提供されます。 インベントリ レポートを使用すると、ご自身の合計データ サイズ、経過日数、暗号化の状態、不変ポリシー、訴訟ホールドなど、BLOB とコンテナーのさまざまな属性を把握できます。 このレポートには、ビジネス要件とコンプライアンス要件に関するデータの概要が示されます。

BLOB インベントリ レポートの詳細については、[Azure Storage BLOB インベントリ](blob-inventory.md)に関するページをご覧ください。

BLOB インベントリ レポートを有効にするには、1 つ以上のルールが含まれるポリシーを、ストレージ アカウントに追加します。 ポリシーを追加、編集、または削除するには、[Azure portal](https://portal.azure.com/) を使用します。

## <a name="enable-inventory-reports"></a>インベントリ レポートを有効にする

### <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure ポータル](https://portal.azure.com/)にサインインして、作業を開始します。

2. ストレージ アカウントを見つけて、アカウントの概要を表示します。

3. **[データ管理]** で、 **[BLOB インベントリ]** を選択します。

4. **[Add your first inventory rule]\(最初のインベントリルールを追加する\)** を選択します。

   **[ルールの追加]** ページが表示されます。

5. **[ルールの追加]** ページで、ご自身の新しいルールに名前を付けます。

6. コンテナーを選択します。

7. **[Object type to inventory]\(インベントリのオブジェクトの種類\)** で、BLOB とコンテナーのどちらのレポートを作成するかを選択します。

   **[BLOB]** を選択した場合は、 **[Blob subtype]\(BLOB のサブタイプ\)** の下で、レポートに含める BLOB の種類と、BLOB バージョンまたはスナップショット、あるいはその両方をインベントリ レポートに含めるかを選択します。

   > [!NOTE]
   > これらのオプションを有効にして新しいルールを保存するには、それぞれバージョンとスナップショットがアカウントで有効になっている必要があります。

8. ご自身のレポートに含めるフィールドと、そのレポートの形式を選択します。

9. レポートを生成する頻度を選択します。

9. 必要に応じて、ご自身のインベントリ レポート内で BLOB をフィルター処理するためのプレフィックスの一致を追加します。

10. **[保存]** を選択します。

    :::image type="content" source="./media/blob-inventory-how-to/portal-blob-inventory.png" alt-text="Azure portal を使用して BLOB インベントリ ルールを追加する方法を示すスクリーンショット":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell"></a>

Azure PowerShell モジュールを使用して、ポリシーを追加、編集、または削除することができます。

1. Windows PowerShell コマンド ウィンドウを開きます。

2. 最新の Azure PowerShell モジュールがあることを確認します。 「[Azure PowerShell モジュールをインストールする](/powershell/azure/install-Az-ps)」を参照してください。

3. `Connect-AzAccount` コマンドを使用して Azure サブスクリプションにサインインし、画面上の指示に従います。

   ```powershell
   Connect-AzAccount
   ```

4. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを設定します。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

5. 使用するストレージ アカウントを定義するストレージ アカウント コンテキストを取得します。

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   - `<resource-group-name>` プレースホルダーの値を、リソース グループの名前に置き換えます。

   - `<storage-account-name>` プレースホルダーの値は、実際のストレージ アカウントの名前に置き換えます。

6. [New-AzStorageBlobInventoryPolicyRule](/powershell/module/az.storage/new-azstorageblobinventorypolicyrule) コマンドを使用して、インベントリ ルールを作成します。 ルールごとにレポート フィールドが一覧表示されます。 レポート フィールドの完全な一覧については、[Azure Storage BLOB インベントリ](blob-inventory.md)に関するページをご覧ください。

   ```powershell
    $containerName = "my-container"

    $rule1 = New-AzStorageBlobInventoryPolicyRule -Name Test1 -Destination $containerName -Disabled -Format Csv -Schedule Daily -PrefixMatch con1,con2 `
                -ContainerSchemaField Name,Metadata,PublicAccess,Last-modified,LeaseStatus,LeaseState,LeaseDuration,HasImmutabilityPolicy,HasLegalHold

    $rule2 = New-AzStorageBlobInventoryPolicyRule -Name test2 -Destination $containerName -Format Parquet -Schedule Weekly  -BlobType blockBlob,appendBlob -PrefixMatch aaa,bbb `
                -BlobSchemaField name,Last-Modified,Metadata,LastAccessTime

    $rule3 = New-AzStorageBlobInventoryPolicyRule -Name Test3 -Destination $containerName -Format Parquet -Schedule Weekly -IncludeBlobVersion -IncludeSnapshot -BlobType blockBlob,appendBlob -PrefixMatch aaa,bbb `
                -BlobSchemaField name,Creation-Time,Last-Modified,Content-Length,Content-MD5,BlobType,AccessTier,AccessTierChangeTime,Expiry-Time,hdi_isfolder,Owner,Group,Permissions,Acl,Metadata,LastAccessTime

    $rule4 = New-AzStorageBlobInventoryPolicyRule -Name test4 -Destination $containerName -Format Csv -Schedule Weekly -BlobType blockBlob -BlobSchemaField Name,BlobType,Content-Length,Creation-Time

   ```

7. [Set-AzStorageBlobInventoryPolicy](/powershell/module/az.storage/set-azstorageblobinventorypolicy) を使用して、BLOB インベントリ ポリシーを作成します。 `-Rule` パラメーターを使用して、このコマンドにルールを渡します。

   ```powershell
   $policy = Set-AzStorageBlobInventoryPolicy -StorageAccount $storageAccount -Rule $rule1,$rule2,$rule3,$rule4  
   ```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

<a id="cli"></a>

[Azure コマンド ライン インターフェイス (CLI)](/cli/azure/) を使用して、ポリシーを追加、編集、または削除することができます。

1. まず、[Azure Cloud Shell](../../cloud-shell/overview.md) を開きます。または、Azure CLI をローカルに[インストール](/cli/azure/install-azure-cli)した場合は、Windows PowerShell などのコマンド コンソール アプリケーションを開きます。

2. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを設定します。

   ```azurecli
      az account set --subscription <subscription-id>
   ```

   `<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

3. JSON ドキュメント内でご自身のポリシーのルールを定義します。 `policy.json` という名前の JSON ファイルの例のコンテンツを次に示します。

    ```json
    {
    "enabled": true,
    "type": "Inventory",
    "rules": [
      {
        "enabled": true,
        "name": "inventoryPolicyRule2",
        "destination": "mycontainer",
        "definition": {
          "filters": {
            "blobTypes": [
              "blockBlob"
            ],
            "prefixMatch": [
              "inventoryprefix1",
              "inventoryprefix2"
            ],
            "includeSnapshots": true,
            "includeBlobVersions": true
          },
          "format": "Csv",
          "schedule": "Daily",
          "objectType": "Blob",
          "schemaFields": [
            "Name",
            "Creation-Time",
            "Last-Modified",
            "Content-Length",
            "Content-MD5",
            "BlobType",
            "AccessTier",
            "AccessTierChangeTime",
            "Snapshot",
            "VersionId",
            "IsCurrentVersion",
            "Metadata"
          ]
        }
      }
     ]
   }

   ```

4. [az storage account blob-inventory-policy](/cli/azure/storage/account/blob-inventory-policy#az_storage_account_blob_inventory_policy_create) create コマンドを使用して、BLOB インベントリ ポリシーを作成します。 `--policy` パラメーターを使用して、ご自身の JSON ドキュメントの名前を指定します。

   ```azurecli
   az storage account blob-inventory-policy create -g myresourcegroup --account-name mystorageaccount --policy @policy.json
   ```

---

## <a name="next-steps"></a>次のステップ

- [コンテナーごとの BLOB の数と合計サイズを計算する](calculate-blob-count-size.md)
- [Azure Blob Storage のライフサイクルを管理する](./lifecycle-management-overview.md)
