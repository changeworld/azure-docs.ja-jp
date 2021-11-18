---
title: Azure CLI を使用して長期保有を指定した Azure Database for PostgreSQL をバックアップする
description: Azure CLI を使用して Azure Database for PostgreSQL をバックアップする方法について学習します。
ms.topic: conceptual
ms.date: 10/24/2021
ms.custom: devx-track-azurecli
ms.author: v-amallick
ms.openlocfilehash: 2f032091eb6a0e7807046bb59dfc37709fb4c272
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717774"
---
# <a name="back-up-azure-postgresql-databases-using-azure-cli"></a>Azure CLI を使用して Azure PostgreSQL データベースをバックアップする

この記事では、Azure CLI を使用して [Azure PostgreSQL データベース](../postgresql/overview.md#azure-database-for-postgresql---single-server)をバックアップする方法について説明します。

この記事では、次の方法について学習します。

-  バックアップ コンテナーの作成
-  バックアップ ポリシーの作成
-  Azure PostgreSQL データベースのバックアップを構成する
-  オンデマンド バックアップ ジョブを実行する

informgreSQL データベースでサポートされるシナリオと制限事項については、「[サポート マトリックス](backup-azure-database-postgresql-overview.md#support-matrix)」を参照してください。

## <a name="create-a-backup-vault"></a>バックアップ コンテナーの作成

バックアップ コンテナーは、Azure Database for PostgreSQL サーバー、ストレージ アカウントの BLOB、Azure ディスクなど、Azure Backup によってサポートされる新しい各種ワークロードのバックアップ データを格納する Azure のストレージ エンティティです。 バックアップ コンテナーは、管理オーバーヘッドを最小限に抑えながら、バックアップ データを整理するのに役立ちます。 バックアップ コンテナーは、Azure の Azure Resource Manager モデルに基づいており、強化されたバックアップ データの保護機能を提供します。

バックアップ コンテナーを作成する前に、コンテナー内のデータのストレージ冗長を選択します。 次に、そのストレージ冗長と場所を使用したバックアップ コンテナーの作成に進みます。

この記事では、_westus_ リージョンのリソース グループ _testBkpVaultRG_ に、バックアップ コンテナー _TestBkpVault_ を作成します。 バックアップ コンテナーを作成するには、[az dataprotection vault create](/cli/azure/dataprotection/backup-vault?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_vault_create) コマンドを使用します。 詳細については、「[バックアップ コンテナーの作成](./backup-vault-overview.md#create-a-backup-vault)」を参照してください。

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

コンテナーが作成された後、Azure PostgreSQL データベースを保護するバックアップ ポリシーを作成しましょう。

## <a name="create-a-backup-policy"></a>バックアップ ポリシーの作成

### <a name="understanding-postgresql-backup-policy"></a>PostGreSQL のバックアップ ポリシーについて

ディスク バックアップでは 1 日に複数回のバックアップを行うことができ、BLOB バックアップはトリガーのない "_継続的_" バックアップですが、PostgreSQL バックアップではアーカイブ保護が提供されます。 最初にコンテナーに送信されたバックアップ データはその後、定義された規則または "_ライフサイクル_" に従って、_Archive_ レベルに移動することができます。 このコンテキストで、PostgreSQL のバックアップ ポリシー オブジェクトについて理解しましょう。

- PolicyRule
   -  BackupRule
      - BackupParameter
        -  BackupType (この場合はデータベースの完全バックアップ)
        -  初期データストア (バックアップを最初に保存する場所)
        -  トリガー (バックアップのトリガー方法)
           -  スケジュール ベース
           -  既定のタグ付け条件 (スケジュールされたすべてのバックアップの既定の 'タグ'。 このタグにより、バックアップが保有規則にリンクされます)
   -  既定の保有規則 (初期データストアにおいて、既定ですべてのバックアップに適用される規則)

そのため、このオブジェクトでは、トリガーされるバックアップの種類、トリガー方法 (スケジュールを使用)、タグ付け対象、その保存先 (データストア)、およびデータストア内のバックアップ データのライフ サイクルを定義します。 PostgreSQL の既定の PowerShell オブジェクトでは、"*完全*" バックアップを毎週トリガーし、それらがコンテナーに保存され、3 か月間格納されるようになっています。

*Archive* レベルをポリシーに追加する場合は、データをコンテナーからアーカイブに移動するタイミング、データをアーカイブに保存する期間、およびスケジュールされたバックアップの内、どのバックアップに "_アーカイブ可能_" のタグを付けるかを決定する必要があります。 そのため、"*保有規則*" を追加する必要があります。ここでは、コンテナー データストアからアーカイブ データストアへのバックアップ データのライフサイクルと、"*アーカイブ*" データストアに保持する期間を定義します。 その後、スケジュールされたバックアップを "_アーカイブ対象_" としてマークする ''*タグ*'' を追加する必要があります。

結果の PowerShell オブジェクトは次のようになります。

-  PolicyRule
   -  BackupRule
      - BackupParameter
        -  BackupType (この場合はデータベースの完全バックアップ)
        -  初期データストア (バックアップを最初に保存する場所)
        -  トリガー (バックアップのトリガー方法)
           -  スケジュール ベース
           -  既定のタグ付け条件 (スケジュールされたすべてのバックアップの既定の 'タグ'。 このタグにより、バックアップが保有規則にリンクされます)
           -  新しい保有規則の新しいタグ付け条件 ('X' と同じ名前)
   -  既定の保有規則 (初期データストアにおいて、既定ですべてのバックアップに適用される規則)
   -  'X' という名前の新しい保有規則
      -  ライフサイクル
         -  ソース データストア
         -  ソース データストアで一定期間が経過した後削除する
         -  ターゲット データストアにコピーする

### <a name="retrieve-the-policy-template"></a>ポリシー テンプレートを取得する

Azure PostgreSQL データベース バックアップ用のバックアップ ポリシーの内部コンポーネントについて理解するには、[az dataprotection backup-policy get-default-policy-template](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_policy_get_default_policy_template) コマンドを使用してポリシー テンプレートを取得します。 このコマンドにより、指定されたデータソースの種類の既定のポリシー テンプレートが返されます。 このポリシー テンプレートを使用して、新しいポリシーを作成します。

```azurecli-interactive
az dataprotection backup-policy get-default-policy-template --datasource-type AzureDatabaseForPostgreSQL
{
  "datasourceTypes": [
    "Microsoft.DBforPostgreSQL/servers/databases"
  ],
  "name": "OssPolicy1",
  "objectType": "BackupPolicy",
  "policyRules": [
    {
      "backupParameters": {
        "backupType": "Full",
        "objectType": "AzureBackupParams"
      },
      "dataStore": {
        "dataStoreType": "VaultStore",
        "objectType": "DataStoreInfoBase"
      },
      "name": "BackupWeekly",
      "objectType": "AzureBackupRule",
      "trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2021-08-15T06:30:00+00:00/P1W"
          ],
          "timeZone": "UTC"
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
            "duration": "P3M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": []
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
  ]
}
```

ポリシー テンプレートは、トリガー (何がバックアップをトリガーするかを決定) とライフサイクル (バックアップをいつ削除、コピー、移動するかを決定) で構成されます。 Azure PostgreSQL データベース バックアップでは、トリガーの既定値はスケジュールされた週単位のトリガー (7 日に 1 回のバックアップ) で、各バックアップは 3 か月間保持されます。

**スケジュールされたトリガー:**

```json
"trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2021-08-15T06:30:00+00:00/P1W"
          ],
          "timeZone": "UTC"
        }
```

**既定の保有規則ライフサイクル:**

```json
 {
      "isDefault": true,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P3M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": []
        }
      ],
      "name": "Default",
      "objectType": "AzureRetentionRule"
    }
```

### <a name="modifying-the-policy-template"></a>ポリシー テンプレートの変更

> [!IMPORTANT]
> Azure PowerShell では、''_オブジェクト_'' をステージング場所として使用して、すべての変更を実行できます。 Azure CLI では、''*オブジェクト*'' の概念がないため、ファイルを使用する必要があります。 各編集操作は、新しいファイルにリダイレクトする必要があります。その場合、コンテンツが入力ファイルから読み取られ、出力ファイルにリダイレクトされます。 後でスクリプトで使用する際に、必要に応じてファイルの名前を変更できます。

#### <a name="modify-the-schedule"></a>スケジュールを変更する

既定のポリシー テンプレートでは、バックアップは週に 1 回行われます。 このスケジュールは、バックアップを週に複数日行うように変更することができます。 スケジュールを変更するには、[az dataprotection backup-policy trigger set](/cli/azure/dataprotection/backup-policy/trigger?view=azure-cli-latest#az_dataprotection_backup_policy_trigger_set&preserve-view=true) コマンドを使用します。

次の例では、毎週日曜日、水曜日、および金曜日にバックアップが行われるように、週単位のバックアップを変更しています。 スケジュールの日付配列は日付を表し、その日付の曜日は曜日として扱われます。 また、これらのスケジュールが毎週繰り返されるように指定する必要があります。 そのため、スケジュールの間隔は "1"、間隔の種類は "Weekly " となっています。

```azurecli
az dataprotection backup-policy trigger create-schedule --interval-type Weekly --interval-count 1 --schedule-days 2021-08-15T22:00:00 2021-08-18T22:00:00 2021-08-20T22:00:00
[
  "R/2021-08-15T22:00:00+00:00/P1W",
  "R/2021-08-18T22:00:00+00:00/P1W",
  "R/2021-08-20T22:00:00+00:00/P1W"
]

az dataprotection backup-policy trigger set --policy .\OSSPolicy.json  --schedule R/2021-08-15T22:00:00+00:00/P1W R/2021-08-18T22:00:00+00:00/P1W R/2021-08-20T22:00:00+00:00/P1W > EditedOSSPolicy.json
```

#### <a name="add-a-new-retention-rule"></a>新しい保有規則を追加する

"*アーカイブ*" 保護を追加する場合は、以下のようにポリシー テンプレートを変更する必要があります。

既定のテンプレートには、既定の保有規則のもと、初期データストアのライフサイクルが設定されています。 このシナリオでは、3 か月後にバックアップ データを削除する規則となっています。 データを "*アーカイブ*" データストアに "*移動*" する (つまり、バックアップ データを最初にアーカイブ データストアにコピーした後にコンテナー データストアで削除する) タイミングを定義する新しい保有規則を追加する必要があります。 また、この規則では、データを ''*アーカイブ*'' データストアに保持する期間を定義する必要があります。 [az dataprotection backup-policy retention-rule create-lifecycle](/cli/azure/dataprotection/backup-policy/retention-rule?view=azure-cli-latest#az_dataprotection_backup_policy_retention_rule_create_lifecycle&preserve-view=true) コマンドを使用して新しいライフサイクルを作成し、[az dataprotection backup-policy retention-rule set](/cli/azure/dataprotection/backup-policy/retention-rule?view=azure-cli-latest#az_dataprotection_backup_policy_retention_rule_set&preserve-view=true) コマンドを使用して新しい規則または既存の規則に関連付けます。

次の例では、*Monthly* という名前の新しい保有規則を作成します。この場合、毎月最初に成功したバックアップを 6 か月間コンテナーに保持し、Archive レベルに移動して 24 か月間 Archive レベルに保持する必要があります。

```azurecli
az dataprotection backup-policy retention-rule create-lifecycle --retention-duration-count 6 --retention-duration-type Months --source-datastore VaultStore --target-datastore ArchiveStore --copy-option CopyOnExpiryOption > VaultToArchiveLifeCycle.JSON

az dataprotection backup-policy retention-rule create-lifecycle --retention-duration-count 24 --retention-duration-type Months -source-datastore ArchiveStore > OnArchiveLifeCycle.JSON

az dataprotection backup-policy retention-rule set --lifecycles .\VaultToArchiveLifeCycle.JSON .\OnArchiveLifeCycle.JSON --name Monthly --policy .\EditedOSSPolicy.JSON > AddedRetentionRulePolicy.JSON
```

#### <a name="add-a-tag-and-the-relevant-criteria"></a>タグと関連条件を追加する

保有規則が作成されたら、バックアップ ポリシーの *Trigger* プロパティに、対応する "*タグ*" を作成する必要があります。 [az dataprotection backup-policy tag create-absolute-criteria](/cli/azure/dataprotection/backup-policy/tag?view=azure-cli-latest#az_dataprotection_backup_policy_tag_create_absolute_criteria&preserve-view=true) コマンドを使用して新しいタグ付け条件を作成し、[az dataprotection backup-policy tag set](/cli/azure/dataprotection/backup-policy/tag?view=azure-cli-latest#az_dataprotection_backup_policy_tag_set&preserve-view=true) コマンドを使用して既存のタグを更新するか、新しいタグを作成します。

次の例では、適用予定の対応する保有規則と同じ名前の条件 (月の最初に成功したバックアップ) とともに、新しい "*タグ*" を作成します。

この例では、タグ条件の名前は *Monthly* である必要があります。

```azurecli
az dataprotection backup-policy tag create-absolute-criteria --absolute-criteria FirstOfMonth > tagCriteria.JSON
az dataprotection backup-policy tag set --criteria .\tagCriteria.JSON --name Monthly --policy .\AddedRetentionRulePolicy.JSON > AddedRetentionRuleAndTag.JSON
```

週に複数回 (上記の例では毎週日曜日、水曜日、木曜日) バックアップするようにスケジュールが設定されており、日曜日と金曜日のバックアップをアーカイブする場合は、タグ付けの条件を以下のように [az dataprotection backup-policy tag create-generic-criteria](/cli/azure/dataprotection/backup-policy/tag?view=azure-cli-latest#az_dataprotection_backup_policy_tag_create_generic_criteria&preserve-view=true) コマンドを使用して変更できます。

```azurecli
az dataprotection backup-policy tag create-generic-criteria --days-of-week Sunday Friday > tagCriteria.JSON
az dataprotection backup-policy tag set --criteria .\tagCriteria.JSON --name Monthly --policy .\AddedRetentionRulePolicy.JSON > AddedRetentionRuleAndTag.JSON
```

### <a name="create-a-new-postgresql-backup-policy"></a>新しい PostgreSQL バックアップ ポリシーを作成する

要件に従ってテンプレートを変更したら、[az dataprotection backup-policy create](/cli/azure/dataprotection/backup-policy?view=azure-cli-latest#az_dataprotection_backup_policy_create&preserve-view=true) コマンドを使用して、変更したテンプレートを使ってポリシーを作成します。

```azurecli
az dataprotection backup-policy create --backup-policy-name FinalOSSPolicy --policy AddedRetentionRuleAndTag.JSON --resource-group testBkpVaultRG --vault-name TestBkpVault
```

## <a name="configure-backup"></a>バックアップの構成

コンテナーとポリシーを作成したら、Azure PostgreSQL データベースを保護するために考慮する必要がある重要なポイントが 3 つあります。

### <a name="key-entities-involved"></a>関連する主なエンティティ

#### <a name="postgresql-database-to-be-protected"></a>保護する PostGreSQL データベース

保護する PostgreSQL の Azure Resource Manager ID (ARM ID) を取り込みます。 これは、データベースの識別子として機能します。 ここでは、別のサブスクリプションのリソース グループ **ossrg** に存在する PostgreSQL サーバー **testposgresql** の下に、**empdb11** という名前のデータベースがある場合の例を使用します。

次の例では、bash を使用します。

```azurecli
ossId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/ossrg/providers/Microsoft.DBforPostgreSQL/servers/archive-postgresql-ccy/databases/empdb11"
```

#### <a name="azure-key-vault"></a>Azure Key Vault

Azure Backup サービスでは、PostgreSQL データベースに接続するためのユーザー名とパスワードは格納されません。 代わりに、バックアップ管理者がキー コンテナーに "*キー*" をシードする必要があります。その後、バックアップ サービスではキー コンテナーにアクセスし、キーを読み取り、データベースにアクセスします。 関連するキーのシークレット識別子をメモします。

次の例では、bash を使用します。

```azure-cli
keyURI="https://testkeyvaulteus.vault.azure.net/secrets/ossdbkey"
```

#### <a name="backup-vault"></a>バックアップ資格情報コンテナー

バックアップ コンテナーを PostgreSQL サーバーに接続してから、キー コンテナーに存在するキーを使用してデータベースにアクセスする必要があります。 そのため、PostgreSQL サーバーとキー コンテナーへのアクセスが必要となります。 アクセス許可は、バックアップ コンテナーの MSI に付与されます。

PostgreSQL サーバー上のバックアップ コンテナーの MSI と Azure Key Vault (データベースのキーが格納されている) に付与する必要がある[適切な権限について確認してください](./backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-backup)。

### <a name="prepare-the-request"></a>要求を準備する

関連するすべてのアクセス許可が設定されたら、2 つの手順でバックアップの構成が行われます。

1. [az dataprotection backup-instance initialize](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest#az_dataprotection_backup_instance_initialize&preserve-view=true) コマンドを使用し、関連するコンテナー、ポリシー、PostgreSQL データベースを使って関連する要求を準備します。
1. [az dataprotection backup-instance create](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest#az_dataprotection_backup_instance_create&preserve-view=true) コマンドを使用して、データベースを保護する要求を送信します。

```azurecli
az dataprotection backup-instance initialize --datasource-id $ossId --datasource-type AzureDatabaseForPostgreSQL -l <vault-location> --policy-id <policy_arm_id>  --secret-store-type AzureKeyVault --secret-store-uri $keyURI > OSSBkpInstance.JSON

az dataprotection backup-instance create --resource-group testBkpVaultRG --vault-name TestBkpVault TestBkpvault --backup-instance .\OSSBkpInstance.JSON
```

## <a name="run-an-on-demand-backup"></a>オンデマンド バックアップを実行する

バックアップのトリガー中に、保有規則を指定する必要があります。 ポリシーの保有規則を表示するには、ポリシー JSON ファイル内を移動して保有規則を探します。 次の例では、**Default** と **Monthly** という名前の 2 つの保有規則があります。 **Monthly** 規則はオンデマンド バックアップに使用します。

```azurecli
az dataprotection backup-policy show  -g ossdemorg --vault-name ossdemovault-1 --subscription e3d2d341-4ddb-4c5d-9121-69b7e719485e --name osspol5
{
  "id": "/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/resourceGroups/ossdemorg/providers/Microsoft.DataProtection/backupVaults/ossdemovault-1/backupPolicies/osspol5",
  "name": "osspol5",
  "properties": {
    "datasourceTypes": [
      "Microsoft.DBforPostgreSQL/servers/databases"
    ],
    "objectType": "BackupPolicy",
    "policyRules": [
      {
        "backupParameters": {
          "backupType": "Full",
          "objectType": "AzureBackupParams"
        },
        "dataStore": {
          "dataStoreType": "VaultStore",
          "objectType": "DataStoreInfoBase"
        },
        "name": "BackupWeekly",
        "objectType": "AzureBackupRule",
        "trigger": {
          "objectType": "ScheduleBasedTriggerContext",
          "schedule": {
            "repeatingTimeIntervals": [
              "R/2020-04-04T20:00:00+00:00/P1W",
              "R/2020-04-01T20:00:00+00:00/P1W"
            ],
            "timeZone": "UTC"
          },
          "taggingCriteria": [
            {
              "criteria": [
                {
                  "absoluteCriteria": [
                    "FirstOfMonth"
                  ],
                  "daysOfMonth": null,
                  "daysOfTheWeek": null,
                  "monthsOfYear": null,
                  "objectType": "ScheduleBasedBackupCriteria",
                  "scheduleTimes": null,
                  "weeksOfTheMonth": null
                }
              ],
              "isDefault": false,
              "tagInfo": {
                "eTag": null,
                "id": "Monthly_",
                "tagName": "Monthly"
              },
              "taggingPriority": 15
            },
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
        "isDefault": false,
        "lifecycles": [
          {
            "deleteAfter": {
              "duration": "P10Y",
              "objectType": "AbsoluteDeleteOption"
            },
            "sourceDataStore": {
              "dataStoreType": "VaultStore",
              "objectType": "DataStoreInfoBase"
            },
            "targetDataStoreCopySettings": []
          }
        ],
        "name": "Monthly",
        "objectType": "AzureRetentionRule"
      },
      {
        "isDefault": true,
        "lifecycles": [
          {
            "deleteAfter": {
              "duration": "P1Y",
              "objectType": "AbsoluteDeleteOption"
            },
            "sourceDataStore": {
              "dataStoreType": "VaultStore",
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
  "resourceGroup": "ossdemorg",
  "systemData": null,
  "type": "Microsoft.DataProtection/backupVaults/backupPolicies"
}
```

## <a name="trigger-an-on-demand-backup-using-command"></a>コマンドを使用してオンデマンド バックアップをトリガーする

[az dataprotection backup-instance adhoc-backup](/cli/azure/dataprotection/backup-instance?view=azure-cli-latest&preserve-view=true#az_dataprotection_backup_instance_adhoc_backup) コマンドを使用して、オンデマンド バックアップをトリガーします。

```azurecli
az dataprotection backup-instance adhoc-backup --name "ossrg-empdb11" --rule-name "Monthly" --resource-group testBkpVaultRG --vault-name TestBkpVault
```

## <a name="track-jobs"></a>ジョブを追跡する

[az dataprotection job list](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list) コマンドを使用して、すべてのジョブを追跡します。 すべてのジョブを一覧表示し、特定のジョブの詳細を取得できます。

_Az.ResourceGraph_ を使用して、すべてのバックアップ コンテナーにまたがるすべてのジョブを追跡することもできます。 [az dataprotection job list-from-resourcegraph](/cli/azure/dataprotection/job?view=azure-cli-latest&preserve-view=true#az_dataprotection_job_list_from_resourcegraph) コマンドを使用して、バックアップ コンテナーにまたがる関連するジョブを取り込みます。

```azurecli
az dataprotection job list-from-resourcegraph --datasource-type AzureDatabaseForPostgreSQL --status Completed
```

## <a name="next-steps"></a>次の手順

- [Azure CLI を使用して Azure PostgreSQL データベースを復元する](restore-postgresql-database-cli.md)