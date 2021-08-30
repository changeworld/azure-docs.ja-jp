---
title: Azure CLI を使用した Azure Blob のバックアップ
description: Azure CLI を使用して Azure Blob をバックアップする方法について説明します。
ms.topic: conceptual
ms.date: 08/06/2021
ms.openlocfilehash: e8ffd1d369df816bd1020b0b2ec2c1696e6c433e
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181512"
---
# <a name="back-up-azure-blobs-in-a-storage-account-using-azure-cli"></a>Azure CLI を使用してストレージ アカウント内の Azure BLOB をバックアップする

この記事では、Azure CLI を使用して [Azure Blob](./blob-backup-overview.md) をバックアップする方法について説明します。

> [!IMPORTANT]
> Azure BLOB の CLI によるバックアップと復元のサポートはプレビュー段階であり、Az 2.15.0 バージョン以降で拡張機能として提供されます。 この拡張機能は、**az dataprotection** コマンドを実行すると自動的にインストールされます。 拡張機能の[詳細を参照してください](/cli/azure/azure-cli-extensions-overview)。

この記事では、次の方法について学習します。

- 開始する前に

- バックアップ コンテナーの作成

- バックアップ ポリシーの作成

- Azure Blob のバックアップ構成

- オンデマンド バックアップ ジョブを実行する

Azure BLOB の使用可能なリージョン、サポートされるシナリオ、制限事項については、[サポート マトリックス](blob-backup-support-matrix.md)を参照してください。

## <a name="before-you-start"></a>開始する前に

始める前に[前提条件](./blob-backup-configure-manage.md#before-you-start)と[サポート マトリックス](./blob-backup-support-matrix.md)を参照してください。

## <a name="create-a-backup-vault"></a>バックアップ コンテナーの作成

バックアップ コンテナーは、Azure Database for PostgreSQL サーバー、ストレージ アカウントの BLOB、Azure ディスクなど、Azure Backup によってサポートされるさまざまな新しいワークロードのバックアップ データを格納する Azure のストレージ エンティティです。 バックアップ コンテナーを使用すると、管理オーバーヘッドを最小限に抑えながら、バックアップ データを簡単に整理できます。 バックアップ コンテナーは、Azure の Azure Resource Manager モデルに基づいており、強化されたバックアップ データの保護機能を提供します。

バックアップ コンテナーを作成する前に、コンテナー内のデータのストレージ冗長を選択します。 次に、そのストレージ冗長と場所を使用したバックアップ コンテナーの作成に進みます。 この記事では、_westus_ リージョンのリソース グループ _testBkpVaultRG_ に、バックアップ コンテナー _TestBkpVault_ を作成します。 バックアップ コンテナーを作成するには、[az dataprotection vault create](/cli/azure/dataprotection/backup-vault?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_vault_create) コマンドを使用します。 詳細については、「[バックアップ コンテナーの作成](./backup-vault-overview.md#create-a-backup-vault)」を参照してください。

```azurecli-interactive
az dataprotection backup-vault create -g testBkpVaultRG --vault-name TestBkpVault -l westus --type SystemAssigned --storage-settings datastore-type="VaultStore" type="LocallyRedundant"

{
  "eTag": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault",
  "identity": {
    "principalId": "2ca1d5f7-38b3-4b61-aa45-8147d7e0edbc",
    "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "type": "SystemAssigned"
  },
  "location": "westus",
  "name": "TestBkpVault",
  "properties": {
    "provisioningState": "Succeeded",
    "storageSettings": [
      {
        "datastoreType": "VaultStore",
        "type": "LocallyRedundant"
      }
    ]
  },
  "resourceGroup": "testBkpVaultRG",
  "systemData": null,
  "tags": null,
  "type": "Microsoft.DataProtection/backupVaults"
}
```

> [!IMPORTANT]
> コンテナーのバックアップ ストレージの冗長性が表示されますが、冗長性は BLOB の運用バックアップには適用されません。 これは、バックアップが本質的にローカルであり、バックアップコンテナーにデータが格納されていないためです。 ここでのバックアップ コンテナーは、ストレージ アカウント内のブロック BLOB の保護を管理するために役立つ管理エンティティです。

コンテナーを作成した後は、ストレージアカウントの Azure Blob を保護するバックアップポリシーを作成してみましょう。

## <a name="create-a-backup-policy"></a>バックアップ ポリシーの作成

> [!IMPORTANT]
> ポリシーの作成と Azure BLOB のバックアップの構成に進む前に、[このセクション](blob-backup-configure-manage.md#before-you-start)を参照してください。

Azure Blob バックアップのバックアップ ポリシーの内部構成要素を理解するには、[az dataprotection backup-policy get-default-policy-template](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_get_default_policy_template) コマンドを使用してポリシー テンプレートを取得します。 このコマンドにより、指定されたデータソースの種類の既定のポリシー テンプレートが返されます。 このポリシー テンプレートを使用して、新しいポリシーを作成します。

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureBlob

{
  "datasourceTypes": [
    "Microsoft.Storage/storageAccounts/blobServices"
  ],
  "name": "BlobPolicy1",
  "objectType": "BackupPolicy",
  "policyRules": [
    {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P30D",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "OperationalStore",
            "objectType": "DataStoreInfoBase"
          }
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
  ]
}

```

このポリシー テンプレートは、ライフサイクル (バックアップをいつ削除、コピー、移動するかを決定) のみで構成されます。 BLOB の運用バックアップは継続的に行われるため、バックアップを実行するスケジュールは必要ありません。

```json
"policyRules": [
    {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P30D",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "OperationalStore",
            "objectType": "DataStoreInfoBase"
          }
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
  ]
```

> [!NOTE]
> 長期間を対象にした復元の場合、復元操作の完了に時間がかかることがあります。 また、一連のデータの復元にかかる時間は、復元期間中に行われた書き込み操作と削除操作の数に基づきます。 たとえば、100 万オブジェクトを持つアカウントで毎日 3,000 オブジェクトが追加され、毎日 1,000 オブジェクトが削除される場合、過去 30 日間のポイントまで復元するのに約 2 時間必要になります。<br><br>この変更率のアカウントでは、過去 90 日以上の保有期間と復元は推奨されません。

ポリシーの JSON に必要な値をすべて設定したら、[az dataprotection backup-policy create](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_create) コマンドを使用したポリシー オブジェクトからの新しいポリシーの作成に進みます。

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureBlob > policy.json
az dataprotection backup-policy create -g testBkpVaultRG --vault-name TestBkpVault -n BlobBackup-Policy --policy policy.json

{
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/BlobBackup-Policy",
    "name": "BlobBackup-Policy",
    "properties": {
      "datasourceTypes": [
        "Microsoft.Storage/storageAccounts/blobServices"
      ],
      "objectType": "BackupPolicy",
      "policyRules": [
        {
          "isDefault": true,
          "lifecycles": [
            {
              "deleteAfter": {
                "duration": "P2D",
                "objectType": "AbsoluteDeleteOption"
              },
              "sourceDataStore": {
                "dataStoreType": "OperationalStore",
                "objectType": "DataStoreInfoBase"
              },
              "targetDataStoreCopySettings": []
            }
          ],
          "name": "Default",
          "objectType": "AzureRetentionRule"
        }
      ]
    },
    "resourceGroup": "testBkpVaultRG",
    "systemData": null,
    "type": "Microsoft.DataProtection/backupVaults/backupPolicies"
  }
```

## <a name="configure-backup"></a>バックアップの構成

コンテナーとポリシーを作成したら、ストレージ アカウント内のすべての Azure BLOB を保護するためにユーザーが考慮しなければならない重要なポイントが 2 つあります。

### <a name="key-entities-involved"></a>関連する主なエンティティ

#### <a name="storage-account-that-contains-the-blobs-to-be-protected"></a>保護する BLOB が含まれるストレージ アカウント

保護する BLOB が含まれるストレージ アカウントの Azure Resource Manager ID を取得します。 これは、ストレージ アカウントの識別子として機能します。 ここでは、東南アジア地域に存在する別のサブスクリプションで、リソース グループ _blobrg_ の下に _CLITestSA_ という名前のストレージ アカウントがある場合を例にします。

```azurecli-interactive
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA"
```

#### <a name="backup-vault"></a>バックアップ資格情報コンテナー

バックアップ コンテナーからストレージ アカウント内に存在する BLOB のバックアップを有効にするには、ストレージ アカウントに対するアクセス許可が付与されている必要があります。 コンテナーのシステム割り当てマネージド ID は、そのようなアクセス許可を割り当てるために使用されます。

### <a name="assign-permissions"></a>アクセス許可の割り当て

コンテナー (コンテナー MSI と表示) と関連するストレージ アカウントに、RBAC 経由でいくつかのアクセス許可を割り当てる必要があります。 これらは、ポータルまたは PowerShell を使用して実行できます。 すべての[関連するアクセス許可](blob-backup-configure-manage.md#grant-permissions-to-the-backup-vault-on-storage-accounts)については、こちらをご覧ください。

### <a name="prepare-the-request"></a>要求を準備する

関連するすべてのアクセス許可が設定されたら、2 つの手順でバックアップの構成が行われます。 まず、[az dataprotection backup-instance initialize](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_initialize) コマンドを使用して、関連するコンテナー、ポリシー、ストレージ アカウントを使用し、関連する要求を準備します。 次に、[az dataprotection backup-instance create](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_create) コマンドを使用してディスクを保護する要求を送信します。

```azurecli-interactive
az dataprotection backup-instance initialize --datasource-type AzureBlob  -l southeastasia --policy-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/BlobBackup-Policy" --datasource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA" > backup_instance.json
```

```azurecli-interactive
az dataprotection backup-instance create -g testBkpVaultRG --vault-name TestBkpVault --backup-instance backup_instance.json

{
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupInstances/CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
    "name": "CLITestSA-CLITestSA-c3a2a98c-def8-44db-bd1d-ff6bc86ed036",
    "properties": {
      "currentProtectionState": "ProtectionConfigured",
      "dataSourceInfo": {
        "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
        "objectType": "Datasource",
        "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA",
        "resourceLocation": "southeastasia",
        "resourceName": "CLITestSA",
        "resourceType": "Microsoft.Storage/storageAccounts",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/blobrg/providers/Microsoft.Storage/storageAccounts/CLITestSA"
      },
      "dataSourceSetInfo": null,
      "friendlyName": "CLITestSA",
      "objectType": "BackupInstance",
      "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/BlobBackup-Policy",
        "policyParameters": {
          "dataStoreParametersList": [
            {
              "dataStoreType": "OperationalStore",
              "objectType": "AzureOperationalStoreParameters",
              "resourceGroupId": ""
            }
          ]
        },
        "policyVersion": ""
      },
      "protectionErrorDetails": null,
      "protectionStatus": {
        "errorDetails": null,
        "status": "ProtectionConfigured"
      },
      "provisioningState": "Succeeded"
    },
    "resourceGroup": "testBkpVaultRG",
    "systemData": null,
    "type": "Microsoft.DataProtection/backupVaults/backupInstances"
  }
```

> [!IMPORTANT]
> BLOB のバックアップ用にストレージ アカウントを構成すると、変更フィードや削除ロックなど、いくつかの機能が影響を受けます。 [詳細については、こちらを参照してください](blob-backup-configure-manage.md#effects-on-backed-up-storage-accounts)。

## <a name="next-steps"></a>次のステップ

[Azure CLI を使用した Azure Blob の復元](restore-blobs-storage-account-cli.md)