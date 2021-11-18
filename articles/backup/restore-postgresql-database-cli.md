---
title: Azure CLI を使用して Azure PostgreSQL データベースを復元する
description: Azure CLI を使用して Azure PostgreSQL データベースを復元する方法を説明します。
ms.topic: conceptual
ms.date: 10/25/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 3b748c8279740b4ef3a4e3b97b6acbc4a5aae6c2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707390"
---
# <a name="restore-azure-postgresql-databases-using-azure-cli"></a>Azure CLI を使用して Azure PostgreSQL データベースを復元する

この記事では、Azure Backup によってバックアップされた Azure PostgreSQL サーバーに [Azure PostgreSQL データベース](../postgresql/overview.md#azure-database-for-postgresql---single-server)を復元する方法について説明します。

PaaS データベースの場合、既存のデータベースを (バックアップが作成された場所から) 置き換えることによって復元する、元の場所への復旧 (OLR) オプションはサポートされていません。 復旧ポイントから復元して、同じ Azure PostgreSQL サーバーまたは他の PostgreSQL サーバーに新しいデータベースを作成できます。 これは、Alternate-Location Recovery (ALR) と呼ばれ、ソース データベースと復元された (新しい) データベースの両方を保持するのに役立ちます。

この記事では、次の方法について学習します。

- PostgreSQL データベースを復元して新しく作成する

- 復元操作の状態を追跡する

この例では、リソース グループ _testBkpVaultRG_ の下にある既存のバックアップ コンテナー _TestBkpVault_ を参照します。

## <a name="restore-a-backed-up-postgresql-database"></a>バックアップされた PostgreSQL データベースを復元する

### <a name="set-up-permissions"></a>アクセス許可の設定

バックアップボールトは、マネージド IDを使用して他のAzureリソースにアクセスします。 バックアップから復元するには、バックアップボールトのマネージド ID に、データベースを復元する Azure PostgreSQL サーバーに対する一連のアクセス許可が必要です。

ターゲット PostgreSQL サーバーでコンテナーのシステム割り当てマネージド ID に関連するアクセス許可を割り当てるには、[Azure PostgreSQL データベースをバックアップするために必要な一連のアクセス許可](./backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-restore)に関する記事を参照してください。

回復ポイントをファイルとしてストレージ アカウントに復元するには、[バックアップ コンテナーのシステム割り当てマネージド ID がターゲット ストレージ アカウントにアクセスできる必要](./restore-azure-database-postgresql.md#restore-permissions-on-the-target-storage-account)があります。

### <a name="fetch-the-relevant-recovery-point"></a>適切な回復ポイントのフェッチ

コンテナー内のすべてのバックアップ インスタンスを一覧表示するには、[az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list) コマンドを使用してした後、[az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_show) コマンドを使用して、関連するインスタンスをフェッチします。 または、大規模なシナリオでは、[az dataprotection backup-instance list-from-resourcegraph](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph) コマンドを使用することで、複数のコンテナーおよびサブスクリプション全体を対象に、バックアップ インスタンスを一覧表示できます。

```azurecli
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureDatabaseForPostgreSQL -subscriptions "xxxxxxxx-xxxx-xxxx-xxxx"

  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
    "extendedLocation": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
    "identity": null,
    "kind": "",
    "location": "",
    "managedBy": "",
    "name": "testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
    "plan": null,
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
        "resourceLocation": "westus",
        "resourceName": "postgres",
        "resourceProperties": null,
        "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceUri": ""
      },
      "dataSourceProperties": null,
      "dataSourceSetInfo": {
        "baseUri": null,
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "objectType": "DatasourceSet",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
        "resourceLocation": "westus",
        "resourceName": "testpostgresql",
        "resourceProperties": null,
        "resourceType": "Microsoft.DBforPostgreSQL/servers",
        "resourceUri": ""
      },
      "datasourceAuthCredentials": {
        "objectType": "SecretStoreBasedAuthCredentials",
        "secretStoreResource": {
          "secretStoreType": "AzureKeyVault",
          "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
          "value": null
        }
      },
      "friendlyName": "testpostgresql\\empdb11",
      "objectType": "BackupInstance",
      "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
        "policyParameters": null,
        "policyVersion": ""
      },
      "protectionErrorDetails": null,
      "protectionStatus": {
        "errorDetails": null,
        "status": "ProtectionConfigured"
      },
      "provisioningState": "Succeeded",
      "validationType": null
    },
    "protectionState": "ProtectionConfigured",
    "resourceGroup": "testBkpVaultRG",
    "sku": null,
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "tags": null,
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "microsoft.dataprotection/backupvaults/backupinstances",
    "vaultName": "testBkpVault",
    "zones": null
  }
.
.
.
.
.
```

インスタンスが特定されたら、[az dataprotection recovery-point list](/cli/azure/dataprotection/recovery-point?view=azure-cli-latest&preserve-view=true#az_dataprotection_recovery_point_list) コマンドを使用して、関連する復旧ポイントをフェッチします。

```azurecli
az dataprotection recovery-point list --backup-instance-name testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149 -g testBkpVaultRG --vault-name TestBkpVault

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149/recoveryPoints/9da55e757af94261afa009b43cd3222a",
  "name": "9da55e757af94261afa009b43cd3222a",
  "properties": {
    "friendlyName": "2031fdb43a914114b6ce644eb6fcb5ce",
    "objectType": "AzureBackupDiscreteRecoveryPoint",
    "policyName": "oss-clitest-policy",
    "policyVersion": null,
    "recoveryPointDataStoresDetails": [
      {
        "creationTime": "2021-09-13T15:17:41.209845+00:00",
        "expiryTime": null,
        "id": "beddea84-7b30-42a5-a752-7c75baf96a52",
        "metaData": "{\"objectType\":\"PostgresBackupMetadata\",\"version\":\"1.0\",\"postgresVersion\":\"11\",\"dbName\":\"postgres\",\"serverName\":\"testpostgresql\",\"serverFQDN\":\"testpostgresql.postgres.database.azure.com\",\"usernameUsed\":\"backupadmin@testpostgresql\",\"backupToolPath\":\"postgresql-11.6-1\\\\bin\\\\pg_dump.exe\",\"backupType\":\"Full\",\"backupDumpFormat\":\"CUSTOM\",\"backupToolArgsFormat\":\"--no-acl --no-owner --serializable-deferrable --no-tablespaces --quote-all-identifiers -Fc -d postgres://{0}:{1}@{2}:5432/{3}?sslmode=verify-full&sslrootcert=E:\\\\approot\\\\Plugins\\\\Postgres\\\\..\\\\..\\\\postgres-root.crt\",\"storageUnits\":{\"1\":\"DbBackupDumpData\"},\"streamNamesInFirstStorageUnit\":[\"dbbkpdmpdatastream-1631546260050\"],\"pitId\":\"2031fdb43a914114b6ce644eb6fcb5ce\",\"bytesTransferred\":2063,\"dataSourceSize\":8442527,\"backupToolVersion\":\"11\"}",
        "rehydrationExpiryTime": null,
        "rehydrationStatus": null,
        "state": "COMMITTED",
        "type": "VaultStore",
        "visible": true
      }
    ],
    "recoveryPointId": "9da55e757af94261afa009b43cd3222a",
    "recoveryPointTime": "2021-09-13T15:17:41.209845+00:00",
    "recoveryPointType": "Full",
    "retentionTagName": "default",
    "retentionTagVersion": "637671427933449525"
  },
  "resourceGroup": "testBkpVaultRG",
  "systemData": null,
  "type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints"
}
```

アーカイブ アクセス層から復旧ポイントをフェッチする必要がある場合は、_recoveryPointDataStoreDetails_ の _type_ 変数を _ArchiveStore_ にします。

### <a name="prepare-the-restore-request"></a>復元要求を準備する

PostgreSQL データベースにはさまざまな復元オプションがあります。 回復ポイントを別のデータベースとして復元したり、ファイルとして復元したりすることができます。 復旧ポイントはアーカイブ アクセス層にすることもできます。

#### <a name="restore-as-database"></a>データベースとして復元

作成する新しい PostgreSQL データベースの Azure Resource Manager ID (ARM ID) ([上記](#set-up-permissions)で詳しく説明したように、アクセス許可が割り当てられたターゲット PostgreSQL サーバーを使用) と、必要な PostgreSQL データベース名を作成します。 たとえば、PostgreSQLデータベースは、異なるサブスクリプションを持つリソースグループ **targetrg** のターゲット PostgreSQL サーバー **targetossserver** の下で **emprestored21** という名前を付けることができます。

```azurecli
$targetOssId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21"
```

すべての関連する詳細情報を使用して復元要求を準備するには、[az dataprotection backup-instance restore initialize-for-data-recovery](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery) コマンドを使用します。

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore VaultStore --target-resource-id $targetOssId --recovery-point-id 9da55e757af94261afa009b43cd3222a --secret-store-type AzureKeyVault --secret-store-uri "https://restoreoss-test.vault.azure.net/secrets/dbauth3" > OssRestoreReq.JSON
```

アーカイブ ベースの復旧ポイントの場合は、次の手順を実行する必要があります。

1. アーカイブ データストアからコンテナー ストアへリハイドレートします
1. ソース データストアを変更します。
1. リハイドレートの優先度を指定するための他のパラメーターを追加します。
1. リハイドレートされた復旧ポイントをコンテナー データ ストアに保持する期間を指定します。
1. この復旧ポイントからデータベースとして復元します。

上記のすべての操作に対する要求を一度に準備するには、次のコマンドを使用します。

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore ArchiveStore --target-resource-id $targetOssId --recovery-point-id 9da55e757af94261afa009b43cd3222a --secret-store-type AzureKeyVault --secret-store-uri "https://restoreoss-test.vault.azure.net/secrets/dbauth3" --rehydration-priority Standard --rehydration-duration 12 > OssRestoreFromArchiveReq.JSON
```

#### <a name="restore-as-files"></a>ファイルとして復元

[上](#set-up-permissions)で詳しく説明したように、アクセス許可が割り当てられたストレージ アカウント内のコンテナーの URI をフェッチします。 たとえば、異なるサブスクリプションを持つストレージ アカウント **testossstorageaccount** の下にある、**testcontainerrestore** という名前のコンテナーです。

```azurecli
$contURI = "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore"
```

すべての関連する詳細情報を使用して復元要求を準備するには、[az dataprotection backup-instance restore initialize-for-data-recovery-as-files](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_initialize_for_data_recovery_as_files) コマンドを使用します。

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery-as-files --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore VaultStore -target-blob-container-url $contURI --target-file-name "empdb11_postgresql-westus_1628853549768" --recovery-point-id 9da55e757af94261afa009b43cd3222a > OssRestoreAsFilesReq.JSON
```

アーカイブベースの復旧ポイントの場合は、ソース データストアを変更し、リハイドレートの優先度と、リハイドレートされた復旧ポイントのリハイドレート期間 (日数) を以下に示すように追加します。

```azurecli
az dataprotection backup-instance restore initialize-for-data-recovery-as-files --datasource-type AzureDatabaseForPostgreSQL  --restore-location {location} --source-datastore ArchiveStore -target-blob-container-url $contURI --target-file-name "empdb11_postgresql-westus_1628853549768" --recovery-point-id 9da55e757af94261afa009b43cd3222a --rehydration-priority Standard --rehydration-duration 12 > OssRestoreAsFilesReq.JSON
```

[az dataprotection backup-instance validate-for-restore](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_validate_for_restore) コマンドを使用して、JSON ファイルが新しいリソースの作成に成功するかどうかを検証することもできます。

### <a name="trigger-the-restore"></a>復元のトリガー

上で準備した要求で復元操作をトリガーするには、[az dataprotection backup-instance restore trigger](/cli/azure/dataprotection/backup-instance/restore?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_restore_trigger) コマンドを使用します。

```azurecli-interactive
az dataprotection backup-instance restore trigger -g testBkpVaultRG --vault-name TestBkpVault --backup-instance-name testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149 --parameters OssRestoreReq.JSON
```

## <a name="tracking-job"></a>ジョブの追跡

[az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list) コマンドを使用して、すべてのジョブを追跡します。 すべてのジョブを一覧表示し、特定のジョブの詳細を取得できます。

_Az.ResourceGraph_ を使用して、すべての Backup コンテナーを対象に、全ジョブを追跡することもできます。 バックアップ コンテナー全体を対象に関連するジョブを取得するには、[az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) コマンドを使用します。

```azurecli
az dataprotection job list-from-resourcegraph --datasource-type AzureDatabaseForPostgreSQL --operation Restore
```

## <a name="next-steps"></a>次の手順

- [Azure PostgreSQL Backup の概要](backup-azure-database-postgresql-overview.md)