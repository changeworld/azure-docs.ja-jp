---
title: コンテナーの論理的な削除を有効にして管理する
titleSuffix: Azure Storage
description: データが誤って変更または削除されたときに簡単に復旧できるように、コンテナーの論理的な削除を有効にします。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/06/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 208f4ff6b43a722e14a788d052350117aeac56dc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128562497"
---
# <a name="enable-and-manage-soft-delete-for-containers"></a>コンテナーの論理的な削除を有効にして管理する

コンテナーの論理的な削除により、誤って変更または削除されないようにデータを保護できます。 ストレージ アカウントに対してコンテナーの論理的な削除が有効になっている場合、コンテナーとその内容は、削除された後も、指定した保持期間中は復旧することができます。 コンテナーの論理的な削除の詳細については、「[コンテナーの論理的な削除](soft-delete-container-overview.md)」をご覧ください。

エンドツーエンドのデータ保護では、BLOB の論理的な削除と BLOB のバージョン管理も有効にすることをお勧めします。 BLOB の論理的な削除も有効にする方法については、[BLOB の論理的な削除の有効化と管理](soft-delete-blob-enable.md)に関する記事をご覧ください。 BLOB のバージョン管理を有効にする方法については、[BLOB のバージョン管理](versioning-overview.md)に関するページをご覧ください。

## <a name="enable-container-soft-delete"></a>コンテナーの論理的な削除を有効にする

ストレージ アカウントでのコンテナーの論理的な削除は、Azure portal、PowerShell、Azure CLI、Azure Resource Manager テンプレートのいずれかを使用して、いつでも有効または無効にすることができます。 Microsoft では、コンテナーの論理的な削除の保持期間を 7 日以上に設定することをお勧めします。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用してストレージ アカウントでのコンテナーの論理的な削除を有効にするには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) のストレージ アカウントに移動します。
1. **[データ管理]** の下の **[データ保護]** 設定を見つけます。
1. **[コンテナーの論理的な削除を有効にする]** を選択します。
1. 1 日から 365 日の間で保有期間を指定します。
1. 変更を保存します。

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-configure.png" alt-text="Azure portal でコンテナーの論理的な削除を有効にする方法を示すスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell でコンテナーの論理的な削除を有効するには、まず、[Az.Storage](https://www.powershellgallery.com/packages/Az.Storage) モジュールのバージョン 3.9.0 以降をインストールします。 次に、**Enable-AzStorageContainerDeleteRetentionPolicy** コマンドを呼び出して、保持期間の日数を指定します。 山かっこ内の値は、実際の値に置き換えてください。

```azurepowershell-interactive
Enable-AzStorageContainerDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

コンテナーの論理的な削除を無効にするには、**AzStorageContainerDeleteRetentionPolicy** コマンドを呼び出します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI でコンテナーの論理的な削除を有効にするには、最初に Azure CLI バージョン 2.26.0 以降をインストールします。 次に、[az storage account blob service properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) コマンドを呼び出して、保持期間の日数を指定します。 山かっこ内の値は、実際の値に置き換えてください。

```azurecli-interactive
az storage account blob-service-properties update \
    --enable-container-delete-retention true \
    --container-delete-retention-days 7 \
    --account-name <storage-account> \
    --resource-group <resource_group>
```

コンテナーの論理的な削除を無効にするには、`--enable-container-delete-retention` パラメーターに `false` を指定します。

# <a name="template"></a>[テンプレート](#tab/template)

Azure Resource Manager テンプレートを使用してコンテナーの論理的な削除を有効にするには、**containerDeleteRetentionPolicy** プロパティを設定するテンプレートを作成します。 次の手順は、Azure portal でテンプレートを作成する方法について説明しています。

1. Azure portal で、 **[リソースの作成]** を選択します。
1. **[Marketplace を検索]** で「**template deployment**」と入力し、**Enter** キーを押します。
1. **[テンプレートのデプロイ]** 、 **[作成]** 、 **[エディターで独自のテンプレートを作成する]** の順に選択します。
1. テンプレート エディターで、次の JSON を貼り付けます。 `<account-name>` プレースホルダーは、実際のストレージ アカウントの名前に置き換えます。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts/blobServices",
              "apiVersion": "2019-06-01",
              "name": "<account-name>/default",
              "properties": {
                  "containerDeleteRetentionPolicy": {
                      "enabled": true,
                      "days": 7
                  }
              }
          }
      ]
    }
    ```

1. 保持期間を指定します。 既定値は 7 です。
1. テンプレートを保存します。
1. アカウントのリソース グループを指定してから **[確認と作成]** ボタンを選択してテンプレートをデプロイし、コンテナーの論理的な削除を有効にします。

---

## <a name="view-soft-deleted-containers"></a>論理的に削除されたコンテナーを表示する

論理的な削除が有効になっている場合は、論理的に削除されたコンテナーを Azure portal で表示できます。 論理的に削除されたコンテナーは、指定した保持期間中は表示されます。 保持期間が経過すると、論理的に削除されたコンテナーは完全に削除され、表示されなくなります。

論理的に削除されたコンテナーを Azure portal で表示するには、次の手順を実行します。

1. Azure portal でストレージ アカウントに移動し、コンテナーの一覧を表示します。
1. 削除されたコンテナーが一覧に含まれるように [削除されたコンテナーを表示] を切り替えます。

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-list.png" alt-text="論理的に削除されたコンテナーを Azure portal で表示する方法を示すスクリーンショット":::

## <a name="restore-a-soft-deleted-container"></a>論理的に削除されたコンテナーを復元する

保持期間中は、論理的に削除されたコンテナーとその内容を復元できます。 論理的に削除されたコンテナーを Azure portal で復元するには、次の手順を実行します。

1. Azure portal でストレージ アカウントに移動し、コンテナーの一覧を表示します。
1. 復元するコンテナーのコンテキスト メニューを表示し、メニューから **[削除の取り消し]** を選択します。

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-restore.png" alt-text="論理的に削除されたコンテナーを Azure portal で復元する方法を示すスクリーンショット":::

## <a name="next-steps"></a>次の手順

- [コンテナーの論理的な削除](soft-delete-container-overview.md)
- [BLOB の論理的な削除](soft-delete-blob-overview.md)
- [BLOB バージョン管理](versioning-overview.md)
