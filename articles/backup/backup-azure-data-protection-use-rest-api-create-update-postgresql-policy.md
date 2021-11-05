---
title: データ保護 REST API を使用して Azure PostgreSQL データベース用のバックアップ ポリシーを作成する
description: この記事では、REST API を使用して Azure PostgreSQL データベース用のバックアップ ポリシーを作成および管理する方法について学習します。
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: v-amallick
ms.assetid: 759ee63f-148b-464c-bfc4-c9e640b7da6b
ms.openlocfilehash: 2caa9d89ae69d9710f42dc4b3c958d6fa8873098
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092189"
---
# <a name="create-azure-data-protection-backup-policies-for-azure-postgresql-databases-using-rest-api"></a>REST API を使用して Azure PostgreSQL データベース用の Azure データ保護バックアップ ポリシーを作成する

バックアップ ポリシーを使用して、バックアップの保持期間とスケジュールを制御します。 Azure PostgreSQL データベース バックアップでは長期保有が提供され、日ごとのバックアップがサポートされます。

既存のバックアップ ポリシーを再利用して、コンテナーに PostgreSQL データベースのバックアップを構成したり、[REST API を使用して Azure Recovery Services コンテナー用のバックアップ ポリシーを作成](/rest/api/dataprotection/backup-policies/create-or-update)したりすることができます。

## <a name="understanding-postgresql-backup-policy"></a>PostgreSQL のバックアップ ポリシーについて

ディスク バックアップでは 1 日に複数回のバックアップを行うことができ、BLOB バックアップはトリガーのない "*継続的*" バックアップですが、PostgreSQL バックアップではアーカイブ保護が提供されます。 最初にコンテナーに送信されるバックアップ データは、定義された規則や "*ライフサイクル*" に従って、*Archive* レベルに移動することができます。 このコンテキストで、PostgreSQL のバックアップ ポリシー オブジェクトについて理解しましょう。

-  PolicyRule
   -  BackupRule
      -  BackupParameter
         -  BackupType (この場合はデータベースの完全バックアップ)
         -  初期データストア (バックアップを最初に保存する場所)
         -  トリガー (バックアップのトリガー方法)
            -  スケジュール ベース
            -  既定のタグ付け条件 (スケジュールされたすべてのバックアップの既定の "タグ"。 このタグにより、バックアップが保有規則にリンクされます)
   -  既定の保有規則 (初期データストアにおいて、既定ですべてのバックアップに適用される規則)

そのため、このオブジェクトでは、トリガーされるバックアップの種類、トリガー方法 (スケジュールを使用)、タグ付け対象、その保存先 (データストア)、およびデータストア内のバックアップ データのライフサイクルを定義します。 PostgreSQL の既定の PowerShell オブジェクトでは、"*完全*" バックアップを毎週トリガーし、それらがコンテナーに保存され、3 か月間格納されるようになっています。

*Archive* レベルをポリシーに追加する場合は、データをコンテナーからアーカイブに移動するタイミング、データをアーカイブに保存する期間、およびスケジュールされたバックアップの内、どのバックアップに "*アーカイブ可能*" のタグを付けるかを決定する必要があります。 そのため、"*保有規則*" を追加する必要があります。ここでは、コンテナー データストアからアーカイブ データストアへのバックアップ データのライフサイクルと、"*アーカイブ*" データストアに保持する期間を定義します。 その後、スケジュールされたバックアップを "_アーカイブ対象_" としてマークする 'タグ' を追加する必要があります。 結果の PowerShell オブジェクトは次のようになります。

-  PolicyRule
   -  BackupRule
      -  BackupParameter
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
         - ターゲット データストアにコピーする

PostgreSQL データベースをバックアップするためのポリシーを作成するには、次のようにします。

## <a name="create-a-policy"></a>ポリシーの作成

>[!IMPORTANT]
>現在、既存のポリシーの更新または変更はサポートされていません。 代わりに、必要な詳細情報を使用して新しいポリシーを作成し、関連するバックアップ インスタンスに割り当てることができます。

Azure Backup ポリシーを作成するには、次の *PUT* 操作を使用します。

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupPolicies/{policyName}?api-version=2021-01-01
```

`{policyName}` と `{vaultName}` は URI で指定します。 その他の情報は要求本文で指定します。

## <a name="create-the-request-body"></a>要求本文を作成する

たとえば、POstgreSQL バックアップ用のポリシーを作成するには、要求本文に次のコンポーネントが必要です。

|名前  |必須  |型  |説明  |
|---------|---------|---------|---------|
|properties     |   True      |  BaseBackupPolicy:[BackupPolicy](/rest/api/dataprotection/backup-policies/create-or-update#backuppolicy)      | BaseBackupPolicyResource のプロパティ        |

要求本文での定義の完全なリストについては、[バックアップ ポリシー REST API に関するドキュメント](/rest/api/dataprotection/backup-policies/create-or-update)を参照してください。

### <a name="example-request-body"></a>要求本文の例

次のようなポリシーです。

- 週ごとのバックアップのトリガーをスケジュールし、開始時刻を選択します (Time + P1W)。
- バックアップはコンテナーに直接転送されるので、データストアは ''_コンテナー ストア_'' です。
- バックアップは 3 か月間コンテナーに保持されます (P3M)。

```json
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

>[!IMPORTANT]
>時刻形式では _DateTime_ のみがサポートされ、_Time_ はサポートされません。 時刻は、バックアップの完了時刻ではなく、バックアップの開始時刻を示します。

上記の **JSON** を 2 つの変更で更新しましょう。複数の曜日にバックアップするようにし、PostgreSQL データベース バックアップの長期保有のために ''*アーカイブ*'' データストアを追加します。

次の例では、毎週日曜日、水曜日、および金曜日にバックアップが行われるように、週単位のバックアップを変更しています。 スケジュールの日付配列は日付を表し、その日付の曜日は曜日として扱われます。 また、これらのスケジュールが毎週繰り返されるように指定する必要があります。 そのため、スケジュールの間隔は *1*、間隔の種類は *Weekly* となっています。

**スケジュールされたトリガー:**

```json
"trigger": {
        "objectType": "ScheduleBasedTriggerContext",
        "schedule": {
          "repeatingTimeIntervals": [
            "R/2021-08-15T22:00:00+00:00/P1W",
            "R/2021-08-18T22:00:00+00:00/P1W",
            "R/2021-08-20T22:00:00+00:00/P1W"
          ],
          "timeZone": "UTC"
        }
```

"*アーカイブ*" 保護を追加したい場合は、ポリシー JSON を以下のように変更する必要があります。

上記の JSON には、既定の保有規則のもと、初期データストアのライフサイクルが設定されています。 このシナリオでは、3 か月後にバックアップ データを削除する規則となっています。 データを "*アーカイブ*" データストアに "*移動*" する (つまり、バックアップ データを最初にアーカイブ データストアにコピーした後にコンテナー データストアで削除する) タイミングを定義する新しい保有規則を追加する必要があります。 また、この規則では、データをアーカイブ* データストアに保持する期間も定義する必要があります。 この新しい規則に **Monthly** という名前を付けましょう。これは、コンテナー データストアにバックアップを 6 か月間保持してから、アーカイブ データストアにコピーする必要があると定義するものです。 その後、コンテナー データストアで ''*削除*'' し、アーカイブ データストアに 24 か月間データを保持してから、アーカイブ データストア内のデータを削除します。

**保有ライフサイクル:**

```json
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
    },
    {
      "isDefault": false,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P6M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": {
            "copyAfter": {
              "objectType": "CopyOnExpiryOption"
            },
            "dataStore": {
              "dataStoreType": "ArchiveStore",
              "objectType": "DataStoreInfoBase"
            }
          }
        },
        {
          "deleteAfter": {
            "duration": "P24M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "ArchiveStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": null
        }
      ],
      "name": "Monthly",
      "objectType": "AzureRetentionRule"
    }
```

保有規則を追加するたびに、ポリシーの *Trigger* プロパティに対応するタグを追加する必要があります。 次の例では、適用予定の対応する保有規則とまったく同じ名前の条件 (月の最初に成功したバックアップ) とともに、新しい "*タグ*" を作成します。 

この例では、タグ条件の名前は *Monthly* である必要があります。

**タグ付けの条件:**

```json
{
  "criteria": [
    {
      "absoluteCriteria": [
        "FirstOfMonth"
      ],
      "objectType": "ScheduleBasedBackupCriteria"
    }
  ],
  "isDefault": false,
  "tagInfo": {
    "tagName": "Monthly"
  },
  "taggingPriority": 15
}
```

すべての変更を含めた後、ポリシー JSON は次のように表示されます。

```json
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
            "R/2021-08-15T22:00:00+00:00/P1W",
            "R/2021-08-18T22:00:00+00:00/P1W",
            "R/2021-08-20T22:00:00+00:00/P1W"
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
          },
          {
            "criteria": [
              {
                "absoluteCriteria": [
                  "FirstOfMonth"
                ],
                "objectType": "ScheduleBasedBackupCriteria"
              }
            ],
            "isDefault": false,
            "tagInfo": {
              "tagName": "Monthly"
            },
            "taggingPriority": 15
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
    },
    {
      "isDefault": false,
      "lifecycles": [
        {
          "deleteAfter": {
            "duration": "P6M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "VaultStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": {
            "copyAfter": {
              "objectType": "CopyOnExpiryOption"
            },
            "dataStore": {
              "dataStoreType": "ArchiveStore",
              "objectType": "DataStoreInfoBase"
            }
          }
        },
        {
          "deleteAfter": {
            "duration": "P24M",
            "objectType": "AbsoluteDeleteOption"
          },
          "sourceDataStore": {
            "dataStoreType": "ArchiveStore",
            "objectType": "DataStoreInfoBase"
          },
          "targetDataStoreCopySettings": null
        }
      ],
      "name": "Monthly",
      "objectType": "AzureRetentionRule"
    }
  ]
}

```

ポリシー作成の詳細については、[PostGreSQL データベース バックアップ ポリシー](backup-azure-database-postgresql.md#create-backup-policy)に関するドキュメントを参照してください。

## <a name="responses"></a>応答

バックアップ ポリシーの作成と更新は同期操作であり、操作が正常に終了すると _OK_ が返されます。

|名前  |型  |説明  |
|---------|---------|---------|
|200 OK     |     [BaseBackupPolicyResource](/rest/api/dataprotection/backup-policies/create-or-update#basebackuppolicyresource)     |  OK       |

### <a name="example-responses"></a>応答の例

操作が完了すると、応答本文にポリシーの内容が含まれる 200 (OK) が返されます。

```json
{
    "properties": {
        "policyRules": [
            {
                "backupParameters": {
                    "backupType": "Full",
                    "objectType": "AzureBackupParams"
                },
                "trigger": {
                    "schedule": {
                        "repeatingTimeIntervals": [
                            "R/2021-08-15T22:00:00+00:00/P1W",
                            "R/2021-08-18T22:00:00+00:00/P1W",
                            "R/2021-08-20T22:00:00+00:00/P1W"
                        ],
                        "timeZone": "UTC"
                    },
                    "taggingCriteria": [
                        {
                            "tagInfo": {
                                "tagName": "Monthly",
                                "id": "Monthly_"
                            },
                            "taggingPriority": 15,
                            "isDefault": false,
                            "criteria": [
                                {
                                    "absoluteCriteria": [
                                        "FirstOfMonth"
                                    ],
                                    "objectType": "ScheduleBasedBackupCriteria"
                                }
                            ]
                        },
                        {
                            "tagInfo": {
                                "tagName": "Default",
                                "id": "Default_"
                            },
                            "taggingPriority": 99,
                            "isDefault": true
                        }
                    ],
                    "objectType": "ScheduleBasedTriggerContext"
                },
                "dataStore": {
                    "dataStoreType": "VaultStore",
                    "objectType": "DataStoreInfoBase"
                },
                "name": "BackupWeekly",
                "objectType": "AzureBackupRule"
            },
            {
                "lifecycles": [
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P6M"
                        },
                        "targetDataStoreCopySettings": [
                            {
                                "dataStore": {
                                    "dataStoreType": "ArchiveStore",
                                    "objectType": "DataStoreInfoBase"
                                },
                                "copyAfter": {
                                    "objectType": "CopyOnExpiryOption"
                                }
                            }
                        ],
                        "sourceDataStore": {
                            "dataStoreType": "VaultStore",
                            "objectType": "DataStoreInfoBase"
                        }
                    },
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P24M"
                        },
                        "targetDataStoreCopySettings": [],
                        "sourceDataStore": {
                            "dataStoreType": "ArchiveStore",
                            "objectType": "DataStoreInfoBase"
                        }
                    }
                ],
                "isDefault": false,
                "name": "Monthly",
                "objectType": "AzureRetentionRule"
            },
            {
                "lifecycles": [
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P3M"
                        },
                        "targetDataStoreCopySettings": [],
                        "sourceDataStore": {
                            "dataStoreType": "VaultStore",
                            "objectType": "DataStoreInfoBase"
                        }
                    }
                ],
                "isDefault": true,
                "name": "Default",
                "objectType": "AzureRetentionRule"
            }
        ],
        "datasourceTypes": [
            "Microsoft.DBforPostgreSQL/servers/databases"
        ],
        "objectType": "BackupPolicy"
    },
    "id": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/DebRG1/providers/Microsoft.DataProtection/backupVaults/DebBackupVault/backupPolicies/OssPolicy1",
    "name": "OssPolicy1",
    "type": "Microsoft.DataProtection/backupVaults/backupPolicies"
}
```

## <a name="next-steps"></a>次のステップ

[Azure ディスクの保護を有効にする](backup-azure-dataprotection-use-rest-api-backup-disks.md)

Azure Backup REST API について詳しくは、次の記事をご覧ください。

- [Azure Data Protection REST API](/rest/api/dataprotection/)
- [Azure Rest API の開始](/rest/api/azure/)
