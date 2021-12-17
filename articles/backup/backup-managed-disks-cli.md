---
title: Azure CLI を使用して Azure マネージド ディスクをバックアップする
description: Azure CLI を使用して Azure マネージド ディスクをバックアップする方法について説明します。
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: ce1e4b3f88e844165581c95f74955de04686855b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128659701"
---
# <a name="back-up-azure-managed-disks-using-azure-cli"></a>Azure CLI を使用して Azure マネージド ディスクをバックアップする

この記事では、Azure CLI を使用して [Azure マネージド ディスク](../virtual-machines/managed-disks-overview.md)をバックアップする方法について説明します。

> [!IMPORTANT]
> Azure マネージド ディスクの CLI によるバックアップと復元のサポートはプレビュー段階であり、Az 2.15.0 バージョン以降で拡張機能として提供されます。 この拡張機能は、**az dataprotection** コマンドを実行すると自動的にインストールされます。 拡張機能の[詳細を参照してください](/cli/azure/azure-cli-extensions-overview)。

この記事では、次の方法について学習します。

- バックアップ コンテナーの作成

- バックアップ ポリシーの作成

- Azure ディスクのバックアップを構成する

- オンデマンド バックアップ ジョブを実行する

Azure ディスク バックアップの使用可能なリージョン、サポートされるシナリオ、制限事項については、[サポート マトリックス](disk-backup-support-matrix.md)に関するページをご覧ください。

## <a name="create-a-backup-vault"></a>バックアップ コンテナーの作成

バックアップ コンテナーは、Azure Database for PostgreSQL サーバー、ストレージ アカウントの BLOB、Azure ディスクなど、Azure Backup によってサポートされる新しい各種ワークロードのバックアップ データを格納する Azure のストレージ エンティティです。 バックアップ コンテナーを使用すると、管理オーバーヘッドを最小限に抑えながら、バックアップ データを簡単に整理できます。 バックアップ コンテナーは、Azure の Azure Resource Manager モデルに基づいており、強化されたバックアップ データの保護機能を提供します。

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

コンテナーを作成した後は、Azure ディスクを保護するためのバックアップ ポリシーを作成しましょう。

## <a name="create-a-backup-policy"></a>バックアップ ポリシーの作成

Azure ディスク バックアップのバックアップ ポリシーの内部構成要素を把握するには、[az dataprotection backup-policy get-default-policy-template](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_get_default_policy_template) コマンドを使用してポリシー テンプレートを取得します。 このコマンドにより、指定されたデータソースの種類の既定のポリシー テンプレートが返されます。 このポリシー テンプレートを使用して、新しいポリシーを作成します。

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureDisk

{
  "datasourceTypes": [
    "Microsoft.Compute/disks"
  ],
  "name": "DiskPolicy",
  "objectType": "BackupPolicy",
  "policyRules": [
    {
      "backupParameters": {
        "backupType": "Incremental",
        "objectType": "AzureBackupParams"
      },
      "dataStore": {
        "dataStoreType": "OperationalStore",
        "objectType": "DataStoreInfoBase"
      },
      "name": "BackupHourly",
      "objectType": "AzureBackupRule",
      "trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2020-04-05T13:00:00+00:00/PT4H"
          ]
        },
        "taggingCriteria": [
          {
            "isDefault": true,
            "tagInfo": {
              "id": "Default_",
              "tagName": "Default"
            },
            "taggingPriority": 99
          }
        ]
      }
    },
    {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P7D",
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

ポリシー テンプレートは、トリガー (何がバックアップをトリガーするかを決定) とライフサイクル (バックアップをいつ削除、コピー、移動するかを決定) で構成されます。 Azure ディスク バックアップでは、トリガーの既定値は、4 時間ごとにスケジュールされたトリガー (PT4H) と、各バックアップを 7 日間保有することです。

**スケジュールされたトリガー:**

```json
"trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2020-04-05T13:00:00+00:00/PT4H"
          ]
        }

```

**既定の保有ライフサイクル:**

```json
"lifecycles": [
        {
          "deleteAfter": {
            "duration": "P7D",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "OperationalStore",
            "objectType": "DataStoreInfoBase"
          }
        }
      ]
```

Azure ディスク バックアップでは、1 日に複数のバックアップを作成できます。 頻繁にバックアップを行う必要がある場合は、4、6、8、または 12 時間の間隔でバックアップを作成できる **[時間単位]** のバックアップ頻度を選択します。 **[時間]** で選択した間隔に基づいてバックアップがスケジュールされます。

たとえば、 **[4 時間ごと]** を選択した場合、ほぼ 4 時間ごとにバックアップが作成されるので、バックアップが 1 日のうちで均等に分散されます。 1 日 1 回のバックアップで十分な場合は、 **[日単位]** のバックアップ頻度を選択します。 日単位のバックアップ頻度では、バックアップを作成する時刻を指定できます。

>[!IMPORTANT]
>この時刻は、バックアップが完了する時刻ではなく、バックアップの開始時刻を示しています。

バックアップ操作を完了するために必要な時間は、ディスクのサイズや連続するバックアップ間のチャーン率など、さまざまな要因によって変わってきます。 ただし、Azure ディスク バックアップは[増分スナップショット](../virtual-machines/disks-incremental-snapshots.md)を使用するエージェントレス バックアップであり、実稼働アプリのパフォーマンスには影響しません。

   >[!NOTE]
   >選択したコンテナーにグローバル冗長の設定が含まれる場合がありますが、Azure ディスク バックアップでは現在スナップショット データストアのみがサポートされています。 すべてのバックアップがサブスクリプションのリソース グループに格納され、バックアップ コンテナーのストレージにはコピーされません。

ポリシー作成の詳細については、[Azure ディスク バックアップ ポリシー](backup-managed-disks.md#create-backup-policy)に関するドキュメントを参照してください。

テンプレートが JSON ファイルとしてダウンロードされたら、必要に応じてスケジュールと保有期間を編集できます。 次に、結果の JSON を使用して新しいポリシーを作成します。 時間単位の頻度または保有期間を編集する場合は、[az dataprotection backup-policy trigger set](/cli/azure/dataprotection/backup-policy/trigger?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_trigger_set) コマンドまたは [az dataprotection backup-policy retention-rule set](/cli/azure/dataprotection/backup-policy/retention-rule?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_retention_rule_set) コマンドを使用します。 ポリシーの JSON に必要な値をすべて設定したら、[az dataprotection backup-policy create](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_create) コマンドを使用してポリシー オブジェクトから新しいポリシーを作成する手順に進みます。

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureDisk > policy.json
az dataprotection backup-policy create -g testBkpVaultRG --vault-name TestBkpVault -n mypolicy --policy policy.json

{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/mypolicy",
"name": "mypolicy",
"properties": {
"datasourceTypes": [
"Microsoft.Compute/disks"
],
"objectType": "BackupPolicy",
"policyRules": [
{
"backupParameters": {
"backupType": "Incremental",
"objectType": "AzureBackupParams"
},
"dataStore": {
"dataStoreType": "OperationalStore",
"objectType": "DataStoreInfoBase"
},
"name": "BackupHourly",
"objectType": "AzureBackupRule",
"trigger": {
"objectType": "ScheduleBasedTriggerContext",
"schedule": {
"repeatingTimeIntervals": [
"R/2020-04-05T13:00:00+00:00/PT4H"
]
},
"taggingCriteria": [
{
"criteria": null,
"isDefault": true,
"tagInfo": {
"eTag": null,
"id": "Default_",
"tagName": "Default"
},
"taggingPriority": 99
}
]
}
},
{
"isDefault": true,
"lifecycles": [
{
"deleteAfter": {
"duration": "P7D",
"objectType": "AbsoluteDeleteOption"
},
"sourceDataStore": {
"dataStoreType": "OperationalStore",
"objectType": "DataStoreInfoBase"
},
"targetDataStoreCopySettings": null
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

コンテナーとポリシーを作成したら、Azure ディスクを保護するために考慮する必要がある重要なポイントが 3 つあります。

### <a name="key-entities-involved"></a>関連する主なエンティティ

#### <a name="disk-to-be-protected"></a>保護するディスク

保護するディスクの ARM ID と場所を取り込みます。 これは、ディスクの識別子として機能します。 ここでは、別のサブスクリプションのリソース グループ _diskrg_ にある _CLITestDisk_ という名前のディスクを例に使用します。

```azurecli-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk"
```

#### <a name="snapshot-resource-group"></a>スナップショット リソース グループ

ディスクのスナップショットは、サブスクリプション内のリソース グループに格納されます。 ガイドラインとして、Azure Backup サービスで使用されるスナップショット データストアとして専用のリソース グループを作成することをお勧めします。 専用のリソース グループを使用すると、リソース グループに対するアクセス許可を制限できるため、バックアップ データを安全かつ簡単に管理できます。 ディスクのスナップショットを配置するリソース グループの ARM ID をメモしておきます。

```azurecli-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>バックアップ資格情報コンテナー

バックアップ コンテナーには、ディスクに対するアクセス許可と、スナップショットをトリガーしてそのライフサイクルを管理できるようにするためのスナップショット リソース グループが必要です。 コンテナーのシステム割り当てマネージド ID は、そのようなアクセス許可を割り当てるために使用されます。 [az dataprotection backup-vault update](/cli/azure/dataprotection/backup-vault?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_vault_update) コマンドを使用して、Recovery Services コンテナーのシステム割り当てマネージド ID を有効にします。

```azurecli-interactive
az dataprotection backup-vault update -g testBkpVaultRG --vault-name TestBkpVault --type SystemAssigned
```

### <a name="assign-permissions"></a>アクセス許可を割り当てる

コンテナー (コンテナーの MSI で表される) や関連するディスク、ディスク RG には、RBAC を介していくつかのアクセス許可を割り当てる必要があります。 この操作は、Azure portal または CLI で行うことができます。 関連するアクセス許可を割り当てるには、[マネージド ディスクのバックアップを構成するための前提条件](backup-managed-disks-ps.md#assign-permissions)に関する記事を参照してください。

### <a name="prepare-the-request"></a>要求を準備する

関連するすべてのアクセス許可が設定されたら、2 つの手順でバックアップの構成が行われます。 最初に、[az dataprotection backup-instance initialize](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_initialize) コマンドを使用して、関連するコンテナー、ポリシー、ディスク、スナップショット リソース グループを使用し、関連する要求を準備します。 initialize コマンドからは JSON ファイルが返されます。その後、スナップショット リソース グループの値を更新する必要があります。 次に、[az dataprotection backup-instance create](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_create) コマンドを使用してディスクを保護する要求を送信します。

```azurecli-interactive
az dataprotection backup-instance initialize --datasource-type AzureDisk  -l southeastasia --policy-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault/backupPolicies/mypolicy" --datasource-id "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk" > backup_instance.json
```

JSON ファイルを開き、```data_store_parameters_list``` セクションの ``` resource_group_id ``` にある **スナップショットのリソース グループ ID** を編集します。

```json
{
  "backup_instance_name": "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
  "properties": {
    "data_source_info": {
      "datasource_type": "Microsoft.Compute/disks",
      "object_type": "Datasource",
      "resource_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
      "resource_location": "southeastasia",
      "resource_name": "CLITestDisk",
      "resource_type": "Microsoft.Compute/disks",
      "resource_uri": ""
    },
    "data_source_set_info": null,
    "object_type": "BackupInstance",
    "policy_info": {
      "policy_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupPolicies/DiskPolicy",
      "policy_parameters": {
        "data_store_parameters_list": [
          {
            "data_store_type": "OperationalStore",
            "object_type": "AzureOperationalStoreParameters",
            "resource_group_id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/snapshotrg"
          }
        ]
      }
    }
  }
}
```

> [!NOTE]
> バックアップ インスタンスの名前は、一意の値となるようにクライアントによって生成されます。 これはデータソース名と一意の GUID に基づきます。 バックアップ インスタンスを一覧表示すると、バックアップ インスタンスの名前および関連するデータソース名を確認できます。

編集した JSON ファイルを使用して、Azure マネージド ディスクのバックアップ インスタンスを作成します。

```azurecli-interactive
az dataprotection backup-instance create -g testBkpVaultRG --vault-name TestBkpVault --backup-instance backup_instance.json


{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
  "name": "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
  "properties": {
    "currentProtectionState": "ProtectionConfigured",
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Compute/disks",
      "objectType": "Datasource",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
      "resourceLocation": "southeastasia",
      "resourceName": "CLITestDisk",
      "resourceType": "Microsoft.Compute/disks",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk"
    },
    "dataSourceSetInfo": null,
    "friendlyName": "CLITestDisk",
    "objectType": "BackupInstance",
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupPolicies/DiskPolicy",
      "policyParameters": {
        "dataStoreParametersList": [
          {
            "dataStoreType": "OperationalStore",
            "objectType": "AzureOperationalStoreParameters",
            "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/sarath-rg"
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
  "resourceGroup": "testBkpVaultRG",
  "systemData": null,
  "type": "Microsoft.DataProtection/backupVaults/backupInstances"
}
```

スケジュールされたポリシーの実行を待ちたくない場合は、バックアップ インスタンスの作成後に続けてオンデマンド バックアップをトリガーすることができます。

## <a name="run-an-on-demand-backup"></a>オンデマンド バックアップを実行する

[az dataprotection backup-instance list](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true) コマンドを使用してコンテナー内のすべてのバックアップ インスタンスを一覧表示した後、[az dataprotection backup-instance show](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true) コマンドを使用して、関連するインスタンスをフェッチします。 または、大規模なシナリオでは、[az dataprotection backup-instance list-from-resourcegraph](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_list_from_resourcegraph) コマンドを使用することで、複数のコンテナーおよび複数のサブスクリプション全体を対象に、バックアップ インスタンスを一覧表示できます。

```azurecli-interactive
az dataprotection backup-instance list-from-resourcegraph --datasource-type AzureDisk --datasource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk


[
  {
    "datasourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/CLITestDisk",
    "extendedLocation": null,
    "id": "//subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/testBkpVaultRG/providers/Microsoft.DataProtection/BackupVaults/TestBkpVault/backupInstances/diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166",
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

バックアップのトリガー中に、保持ルールを指定できます。 ポリシーの保有ルールを確認するには、ポリシーの JSON に目を通して保有ルールを探します。 下の例では、_default_ という名前のルールが表示されています。このルールをオンデマンド バックアップに使用します。

```JSON
{
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P7D",
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
```

[az dataprotection backup-instance adhoc-backup](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_adhoc_backup) コマンドを使用して、オンデマンド バックアップをトリガーします。

```azurecli-interactive
az dataprotection backup-instance adhoc-backup --name "diskrg-CLITestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166" --rule-name "Default" --resource-group "000pikumar" --vault-name "PratikPrivatePreviewVault1"
```

## <a name="tracking-jobs"></a>ジョブの追跡

[az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list) コマンドを使用して、すべてのジョブを追跡します。 すべてのジョブを一覧表示し、特定のジョブの詳細を取得できます。

Az.ResourceGraph を使用して、すべてのバックアップ コンテナーを対象に、全ジョブを追跡することもできます。 関連するジョブを取得するには、[az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) コマンドを使用します。これを、バックアップ コンテナー全体を対象に行うことも可能です。

```azurepowershell-interactive
az dataprotection job list-from-resourcegraph --datasource-type AzureDisk --status Completed
```

## <a name="next-steps"></a>次のステップ

[Azure CLI を使用して Azure Managed Disks を復元する](restore-managed-disks-cli.md)
