---
title: Azure CLI による Azure Managed Disks の復元
description: Azure CLI を使用して、Azure Managed Disks を復元する方法について説明します。
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: 87ca3597b7ad73aa1ac0ba5cff6c75b5ec880750
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114287502"
---
# <a name="restore-azure-managed-disks-using-azure-cli"></a>Azure CLI を使用して Azure Managed Disks を復元する

この記事では、Azure CLI を使用して、Azure Backup によって作成された復元ポイントから [Azure Managed Disks](../virtual-machines/managed-disks-overview.md) を復元する方法について説明します。

> [!IMPORTANT]
> Azure マネージド ディスクの CLI によるバックアップと復元のサポートはプレビュー段階であり、Az 2.15.0 バージョン以降で拡張機能として提供されます。 この拡張機能は、**az dataprotection** コマンドを実行すると自動的にインストールされます。 拡張機能の[詳細を参照してください](/cli/azure/azure-cli-extensions-overview)。

現在、バックアップが作成された既存のソース ディスクを置き換えることによって復元する、元の場所への復旧 (OLR) オプションはサポートされていません。 復旧ポイントから復元して、ソース ディスクと同じリソース グループか、または他の任意のリソース グループに新しいディスクを作成できます。 これは別の場所への復旧 (ALR) と呼ばれています。

この記事では、次の方法について学習します。

- 復元して新しいディスクを作成する

- 復元操作の状態を追跡する

この例では、リソース グループ _testBkpVaultRG_ の下にある既存のバックアップ コンテナー _TestBkpVault_ を参照します。

## <a name="restore-to-create-a-new-disk"></a>復元して新しいディスクを作成する

### <a name="setting-up-permissions"></a>アクセス許可の設定

バックアップ コンテナーは、マネージド ID を使用して他の Azure リソースにアクセスします。 バックアップから復元するには、バックアップ コンテナーのマネージド ID に、ディスクを復元する先のリソース グループに対する一連のアクセス許可が必要になります。

バックアップ コンテナーでは、リソースあたり 1 つに制限され、このリソースのライフサイクルに関連付けられているシステム割り当てマネージド ID を使用します。 マネージド ID には、Azure ロールベースのアクセス制御 (Azure RBAC) を使用してアクセス許可を付与できます。 マネージド ID は、Azure リソースでのみ使用できる特殊な種類のサービス プリンシパルです。 [マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) の詳細を確認してください。

[ここ](restore-managed-disks.md#restore-to-create-a-new-disk)に記載されているように、ディスクを復元または作成するターゲット リソース グループで、コンテナーのシステム割り当て済みマネージド ID に適切なアクセス許可を割り当てます。

### <a name="fetching-the-relevant-recovery-point"></a>適切な復元ポイントのフェッチ

[az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list) コマンドを使用してコンテナー内のすべてのバックアップ インスタンスを一覧表示した後、[az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_show) コマンドを使用して、関連するインスタンスをフェッチします。 または、大規模なシナリオでは、[az dataprotection backup-instance list-from-resourcegraph](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph) を使用すると、複数のコンテナーおよび複数のサブスクリプション全体にわたるバックアップ インスタンスを一覧表示できます

```azurecli-interactive
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureDisk --datasource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk


[
  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
    "extendedLocation": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
    "identity": null,
    "kind": "",
    "location": "",
    "managedBy": "",
    "name": "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
    "plan": null,
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.Compute/disks",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
        "resourceLocation": "westus",
        "resourceName": "CLITestDisk",
        "resourceType": "Microsoft.Compute/disks",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk"
      },
      "dataSourceProperties": null,
      "dataSourceSetInfo": null,
      "datasourceAuthCredentials": null,
      "friendlyName": "CLITestDisk",
      "objectType": "BackupInstance",
      "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupPolicies/DiskPolicy",
        "policyParameters": {
          "dataStoreParametersList": [
            {
              "dataStoreType": "OperationalStore",
              "objectType": "AzureOperationalStoreParameters",
              "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/snapshotrg"
            }
          ]
        },
        "policyVersion": null
      },
      "protectionErrorDetails": null,
      "protectionStatus": {
        "errorDetails": null,
        "status": "ProtectionConfigured"
      },
      "provisioningState": "Succeeded"
    },
    "protectionState": "ProtectionConfigured",
    "resourceGroup": "testBkpVaultRG",
    "sku": null,
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "tags": null,
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "microsoft.dataprotection/backupvaults/backupinstances",
    "vaultName": "TestBkpVault",
    "zones": null
  }
]


```

インスタンスが特定されたら、[az dataprotection recovery-point list](/cli/azure/dataprotection/recovery-point?view=azure-cli-latest&preserve-view=true#az_dataprotection_recovery_point_list) コマンドを使用して、関連する復旧ポイントをフェッチします。

```azurecli-interactive
az dataprotection recovery-point list --backup-instance-name diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 -g testBkpVaultRG --vault-name TestBkpVault

{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166/recoveryPoints/5081ad8f1e6c4548ae89536d0d45c493",
"name": "5081ad8f1e6c4548ae89536d0d45c493",
"properties": {
"friendlyName": "0f598ced-cbfe-4169-b962-ee94b0210490",
"objectType": "AzureBackupDiscreteRecoveryPoint",
"policyName": "DiskPSPolicy2",
"policyVersion": null,
"recoveryPointDataStoresDetails": [
{
"creationTime": "2021-06-08T09:01:57.708319+00:00",
"expiryTime": "2021-06-15T09:01:57.708319+00:00",
"id": "c2ad4629-f2ef-49b6-b3f8-50f3eb5404f4",
"metaData": null,
"rehydrationExpiryTime": null,
"rehydrationStatus": null,
"state": "COMMITTED",
"type": "OperationalStore",
"visible": true
}
],
"recoveryPointId": "5081ad8f1e6c4548ae89536d0d45c493",
"recoveryPointTime": "2021-06-08T09:01:57.708319+00:00",
"recoveryPointType": "Incremental",
"retentionTagName": "Default",
"retentionTagVersion": "637553616953961153"
},
"resourceGroup": "testBkpVaultRG",
"systemData": null,
"type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints"
},
{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166/recoveryPoints/039322cc563049bcbdb77bd695d4c02c",
"name": "039322cc563049bcbdb77bd695d4c02c",
"properties": {
"friendlyName": "af6512b6-aa38-4966-b8e1-660c4eccdc0d",
"objectType": "AzureBackupDiscreteRecoveryPoint",
"policyName": "DiskPSPolicy2",
"policyVersion": null,
"recoveryPointDataStoresDetails": [
{
"creationTime": "2021-06-08T05:01:55.426507+00:00",
"expiryTime": "2021-06-15T05:01:55.426507+00:00",
"id": "c2ad4629-f2ef-49b6-b3f8-50f3eb5404f4",
"metaData": null,
"rehydrationExpiryTime": null,
"rehydrationStatus": null,
"state": "COMMITTED",
"type": "OperationalStore",
"visible": true
}
],
"recoveryPointId": "039322cc563049bcbdb77bd695d4c02c",
"recoveryPointTime": "2021-06-08T05:01:55.426507+00:00",
"recoveryPointType": "Incremental",
"retentionTagName": "Default",
"retentionTagVersion": "637553616953961153"
},
"resourceGroup": "testBkpVaultRG",
"systemData": null,
"type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints"
}
]
```

たとえば、次のクエリは最新の復旧ポイントを返します。

```azurecli-interactive
az dataprotection recovery-point list --backup-instance-name diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 -g testBkpVaultRG --vault-name TestBkpVault --query "[0].id"

"/subscriptions/62b829ee-7936-40c9-a1c9-47a93f9f3965/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/sarath-vault/backupInstances/clitest-clitest-3165cfe7-a932-11eb-9d24-9cfce85d4fae/recoveryPoints/5081ad8f1e6c4548ae89536d0d45c493"
```

### <a name="preparing-the-restore-request"></a>復元要求の準備

[上記](#setting-up-permissions)の説明にあるアクセス許可と、必要なディスク名が割り当てられているターゲット リソース グループを使用して、作成される新しいディスクの ARM ID を作成します。 ここでは、別のサブスクリプションのリソース グループ _targetrg_ にある _CLITestDisk2_ という名前のディスクを例に使用します。

```azurecli-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/CLITestDisk2
```

すべての関連する詳細情報を使用して復元要求を準備するには、[az dataprotection backup-instance restore initialize-for-data-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery) コマンドを使用します。

```azurecli-interactive
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureDisk --restore-location southeastasia --source-datastore OperationalStore --recovery-point-id /subscriptions/62b829ee-7936-40c9-a1c9-47a93f9f3965/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/sarath-vault/backupInstances/clitest-clitest-3165cfe7-a932-11eb-9d24-9cfce85d4fae/recoveryPoints/5081ad8f1e6c4548ae89536d0d45c493 --target-resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/CLITestDisk2 > restore.json
```

```json
{
  "object_type": "AzureBackupRecoveryPointBasedRestoreRequest",
  "recovery_point_id": "77594ce0470849e79b86a6875b726dca",
  "restore_target_info": {
    "datasource_info": {
      "datasource_type": "Microsoft.Compute/disks",
      "object_type": "Datasource",
      "resource_id": "//subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/CLITestDisk2",
      "resource_location": "southeastasia",
      "resource_name": "CLITestDisk2",
      "resource_type": "Microsoft.Compute/disks",
      "resource_uri": ""
    },
    "object_type": "RestoreTargetInfo",
    "recovery_option": "FailIfExists",
    "restore_location": "southeastasia"
  },
  "source_data_store_type": "OperationalStore"
}

```

[az dataprotection backup-instance validate-for-restore](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_validate_for_restore) コマンドを使用して、JSON ファイルが新しいリソースの作成に成功するかどうかを検証することもできます。

```azurecli-interactive
az dataprotection backup-instance validate-for-restore -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 --restore-request-object restore.json
```

### <a name="trigger-the-restore"></a>復元のトリガー

前に準備した要求で復元をトリガーするには、[az dataprotection backup-instance restore trigger](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_trigger) コマンドを使用します。

```azurecli-interactive
az dataprotection backup-instance restore trigger -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 --parameters restore.json
```

## <a name="tracking-job"></a>ジョブの追跡

[az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list) コマンドを使用して、すべてのジョブを追跡します。 すべてのジョブを一覧表示し、特定のジョブの詳細を取得できます。

Az.ResourceGraph を使用して、すべてのバックアップ コンテナーにわたるすべてのジョブを追跡することもできます。 関連するジョブを取得するには、[az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) コマンドを使用します。これを、バックアップ コンテナー全体にまたがって行うことも可能です。

```azurepowershell-interactive
az dataprotection job list-from-resourcegraph --datasource-type AzureDisk --operation Restore
```

## <a name="next-steps"></a>次の手順

[Azure ディスク バックアップに関する FAQ](./disk-backup-faq.yml)