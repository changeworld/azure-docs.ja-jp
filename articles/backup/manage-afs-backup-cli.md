---
title: CLI を使用して Azure ファイル共有のバックアップを管理する
description: Azure CLI を使用して、Azure Backup サービスによってバックアップされた Azure ファイル共有を管理および監視する方法について説明します。
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: bf824b1868ad7c9e4df2cceeca101d82272e18d6
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294869"
---
# <a name="manage-azure-file-share-backups-with-azure-cli"></a>Azure CLI を使用して Azure ファイル共有のバックアップを管理する

Azure コマンド ライン インターフェイス (CLI) では、Azure リソースを管理するためのコマンド ライン エクスペリエンスが提供されます。 これは、Azure リソースを使用するためのカスタム オートメーションを構築するための優れたツールです。 この記事では、[Azure Backup サービス](https://docs.microsoft.com/azure/backup/backup-overview)によってバックアップされた Azure ファイル共有を管理および監視するために、以下のタスクを実行する方法について説明します。 これらの手順は、[Azure portal](https://portal.azure.com/) を使用して実行することもできます。

* [ジョブを監視する](#monitor-jobs)
* [ポリシーを変更する](#modify-policy)
* [ファイル共有の保護を停止する](#stop-protection-on-a-file-share)
* [ファイル共有の保護を再開する](#resume-protection-on-a-file-share)
* [ストレージ アカウントの登録を解除する](#unregister-a-storage-account)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0.18 以降を使用する必要があります。 CLI のバージョンを調べるには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、[Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) サービスによってバックアップされた Azure ファイル共有が既にあることを前提としています。 ない場合は、「[CLI を使用して Azure ファイル共有をバックアップする](backup-afs-cli.md)」を参照して、ファイル共有のバックアップを構成してください。 この記事では、次のリソースを使用します。

* **リソース グループ**: *azurefiles*
* **Recovery Services コンテナー**: *azurefilesvault*
* **ストレージ アカウント**: *afsaccount*
* **ファイル共有**: *azurefiles*

## <a name="monitor-jobs"></a>ジョブの監視

バックアップ操作または復元操作をトリガーすると、追跡用のジョブがバックアップ サービスによって作成されます。 完了したジョブまたは現在実行中のジョブを監視するには、[az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) コマンドレットを使用します。 CLI を使って、[現在実行中のジョブを中断する](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop)ことや、[ジョブが完了するまで待機する](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait)こともできます。

次の例では、*azurefilesvault* Recovery Services コンテナーに対するバックアップ ジョブの状態を表示します。

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>ポリシーの変更

[az backup item set-policy](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-set-policy) を使用してバックアップ ポリシーを変更し、バックアップの頻度または保持期間を変更することができます。

ポリシーを変更するには、次のパラメーターを定義します。

* **--container-name** は、ファイル共有がホストされているストレージ アカウントの名前です。 コンテナーの**名前**または**フレンドリ名**を取得するには、[az backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) コマンドを使用します。
* **--name** は、ポリシーを変更するファイル共有の名前です。 バックアップ項目の**名前**または**フレンドリ名**を取得するには、[az backup item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) コマンドを使用します。
* **--policy-name** は、ファイル共有に設定するバックアップ ポリシーの名前です。 [az backup policy list](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-list) を使用して、コンテナーのすべてのポリシーを表示できます。

次の例では、*afsaccount* ストレージ アカウントに存在する *azurefiles* ファイル共有に対して *schedule2* バックアップ ポリシーを設定します。

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

また、次の 2 つの追加パラメーターを指定することにより、コンテナーと項目の "フレンドリ名" を使用して、上記のコマンドを実行することもできます。

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

出力の **Name** 属性は、"ポリシー変更" 操作のためにバックアップ サービスによって作成されたジョブの名前に対応しています。 ジョブの状態を追跡するには、[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) コマンドレットを使用します。

## <a name="stop-protection-on-a-file-share"></a>ファイル共有の保護を停止する

Azure ファイル共有の保護を停止するには、次の 2 つの方法があります。

* 以降のすべてのバックアップ ジョブを停止し、すべての復旧ポイントを "*削除*" します
* 以降のすべてのバックアップ ジョブを停止しますが、復旧ポイントは "*そのまま*" にします

Azure Backup によって作成された基になるスナップショットが保持されるため、ストレージに復旧ポイントをそのまま残す場合にはコストが伴う可能性があります。 一方、復旧ポイントを残す利点は、必要に応じて後でファイル共有を復元できることです。 復旧ポイントを保持するためのコストについては、「 [価格の詳細](https://azure.microsoft.com/pricing/details/storage/files)」を参照してください。 すべての復旧ポイントを削除するように選択した場合、ファイル共有を復元することはできません。

ファイル共有の保護を停止するには、次のパラメーターを定義します。

* **--container-name** は、ファイル共有がホストされているストレージ アカウントの名前です。 コンテナーの**名前**または**フレンドリ名**を取得するには、[az backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) コマンドを使用します。
* **--item-name** は、保護を停止するファイル共有の名前です。 バックアップ項目の**名前**または**フレンドリ名**を取得するには、[az backup item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) コマンドを使用します。

### <a name="stop-protection-and-retain-recovery-points"></a>保護を停止して復旧ポイントを保持する

データを保持したまま保護を停止するには、[az backup protection disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) コマンドレットを使用します。

次の例では、*azurefiles* ファイル共有の保護を停止しますが、すべての復旧ポイントを保持します。

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

また、次の 2 つの追加パラメーターを指定することにより、コンテナーと項目の "フレンドリ名" を使用して、上記のコマンドを実行することもできます。

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

出力の **Name** 属性は、"保護停止" 操作のためにバックアップ サービスによって作成されたジョブの名前に対応しています。 ジョブの状態を追跡するには、[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) コマンドレットを使用します。

### <a name="stop-protection-without-retaining-recovery-points"></a>復旧ポイントを保持しないで保護を停止する

復旧ポイントを保持しないで保護を停止するには、[az backup protection disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) コマンドレットを使用し、**delete-backup-data オプション**を **true** に設定します。

次の例では、復旧ポイントを保持しないで、*azurefiles* ファイル共有の保護を停止します。

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

また、次の 2 つの追加パラメーターを指定することにより、コンテナーと項目の "フレンドリ名" を使用して、上記のコマンドを実行することもできます。

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>ファイル共有の保護を再開する

Azure ファイル共有の保護を停止したが、復旧ポイントを保持していた場合は、後で保護を再開できます。 復旧ポイントを保持していない場合は、保護を再開できません。

ファイル共有の保護を再開するには、次のパラメーターを定義します。

* **--container-name** は、ファイル共有がホストされているストレージ アカウントの名前です。 コンテナーの**名前**または**フレンドリ名**を取得するには、[az backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) コマンドを使用します。
* **--item-name** は、保護を再開するファイル共有の名前です。 バックアップ項目の**名前**または**フレンドリ名**を取得するには、[az backup item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) コマンドを使用します。
* **--policy-name** は、ファイル共有の保護を再開するバックアップ ポリシーの名前です。

次の例では、[az backup protection resume](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) コマンドレットを使用し、*schedule1* バックアップ ポリシーを使用して *azurefiles* ファイル共有の保護を再開します。

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

また、次の 2 つの追加パラメーターを指定することにより、コンテナーと項目の "フレンドリ名" を使用して、上記のコマンドを実行することもできます。

* **--backup-management-type**: *azurestorage*
* **--workload-type**: *azurefileshare*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

出力の **Name** 属性は、"保護再開" 操作のためにバックアップ サービスによって作成されたジョブの名前に対応しています。 ジョブの状態を追跡するには、[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) コマンドレットを使用します。

## <a name="unregister-a-storage-account"></a>ストレージ アカウントの登録を解除する

特定のストレージ アカウントのファイル共有を別の Recovery Services コンテナーを使用して保護する場合は、まず最初に、そのストレージ アカウント内の[すべてのファイル共有の保護を停止](#stop-protection-on-a-file-share)します。 次に、現在、保護に使用されている Recovery Services コンテナーからアカウントの登録を解除します。

ストレージ アカウントの登録を解除するには、コンテナー名を指定する必要があります。 コンテナーの**名前**または**フレンドリ名**を取得するには、[az backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) コマンドを使用します。

次の例では、[az backup container unregister](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-unregister) コマンドレットを使用して、*azurefilesvault* から *afsaccount* ストレージ アカウントの登録を解除します。

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

また、次の追加パラメーターを指定することにより、コンテナーの "フレンドリ名" を使用して、上記のコマンドレットを実行することもできます。

* **--backup-management-type**: *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>次のステップ

詳細については、[Azure ファイル共有のバックアップと復元に関する障害のトラブルシューティング](troubleshoot-azure-files.md)に関する記事を参照してください
