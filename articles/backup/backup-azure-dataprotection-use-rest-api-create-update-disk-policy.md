---
title: データ保護 REST API を使用してディスク用のバックアップ ポリシーを作成する
description: この記事では、REST API を使用してディスク用のバックアップ ポリシーを作成および管理する方法について説明します。
ms.topic: conceptual
ms.date: 10/06/2021
ms.assetid: ecc107c0-311c-42d0-a094-654d7ee30443
ms.openlocfilehash: 0df0b23d921076959718e79d5674aa8d561639e3
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620838"
---
# <a name="create-azure-data-protection-backup-policies-for-disks-using-rest-api"></a>REST API を使用してディスク用の Azure データ保護バックアップ ポリシーを作成する

バックアップ ポリシーを使用して、バックアップの保持期間とスケジュールを制御します。 Azure ディスク バックアップでは、1 日に複数のバックアップを作成できます。

バックアップ ポリシーを再利用して、コンテナーに複数の Azure ディスクのバックアップを構成したり、[REST API を使用して Azure Recovery Services コンテナー用のバックアップ ポリシーを作成](/rest/api/dataprotection/backup-policies/create-or-update)したりすることができます。

ディスク バックアップ用ポリシーを作成するには、次のようにします。

## <a name="create-a-policy"></a>ポリシーの作成

>[!IMPORTANT]
>現在、既存のポリシーの更新または変更はサポートされていません。 代わりに、必要な詳細情報を使用して新しいポリシーを作成し、関連するバックアップ インスタンスに割り当てることができます。

Azure Backup ポリシーを作成するには、次の *PUT* 操作を使用します。

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupPolicies/{policyName}?api-version=2021-01-01
```

`{policyName}` と `{vaultName}` は URI で指定します。 その他の情報は要求本文で指定します。

## <a name="create-the-request-body"></a>要求本文を作成する

たとえば、ディスク バックアップ用のポリシーを作成するには、要求本文に次のコンポーネントが必要です。

|名前  |必須  |型  |説明  |
|---------|---------|---------|---------|
|properties     |   True      |  BaseBackupPolicy:[BackupPolicy](/rest/api/dataprotection/backup-policies/create-or-update#backuppolicy)      | BaseBackupPolicyResource のプロパティ        |

要求本文での定義の完全な一覧については、[バックアップ ポリシー REST API に関するドキュメント](/rest/api/dataprotection/backup-policies/create-or-update)をご覧ください。

### <a name="example-request-body"></a>要求本文の例

次のようなポリシーです。

- 4 時間ごとにスケジュールされたトリガー (PT4H)。 その場合、ほぼ 4 時間ごとにバックアップが作成されるので、バックアップが 1 日に均等に分散されます。
- トリガーの間隔は、4、6、8、または 12 時間ごとから選択できます。 1 日に 1 回のバックアップをスケジュールするには、**P1D** を使用します。 バックアップは、1 日に 1 回規定の時刻にトリガーされます。
- バックアップはローカルであり、バックアップ コンテナーにデータは格納されないため、データストアは "_運用ストア_" です。 運用ストアでは、各バックアップ インスタンスは 7 日間 (P7D) 保存されます。

```json
{
"properties": {
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
}
```

>[!IMPORTANT]
>日時形式は、DateTime のみがサポートされます。 Time のみはサポートされません。 時刻は、バックアップの完了時刻ではなく、バックアップの開始時刻を示します。

バックアップ操作を完了するために必要な時間は、ディスクのサイズや連続するバックアップ間のチャーン率など、さまざまな要因によって変わってきます。 ただし、Azure ディスク バックアップは[増分スナップショット](../virtual-machines/disks-incremental-snapshots.md)を使用するエージェントレス バックアップであり、実稼働アプリのパフォーマンスには影響しません。

ポリシー作成の詳細については、[Azure ディスク バックアップ ポリシー](backup-managed-disks.md#create-backup-policy)に関するドキュメントを参照してください。

## <a name="responses"></a>Responses

バックアップ ポリシーの作成と更新は同期操作であり、操作が正常に終了すると OK が返されます。

|名前  |型  |説明  |
|---------|---------|---------|
|200 OK     |     [BaseBackupPolicyResource](/rest/api/dataprotection/backup-policies/create-or-update#basebackuppolicyresource)     |  OK       |

### <a name="example-responses"></a>応答の例

操作が完了すると、応答本文にポリシーの内容が含まれる 200 (OK) が返されます。

```json
{
    "id": "/subscriptions/73307177-bb00-4801-bd11-894b2f2d5162/resourceGroups/RG-BV/providers/Microsoft.DataProtection/backupVaults/BV-JPE-GRS/backupPolicies/DiskBackupPolicy-03",
    "name": "DiskBackupPolicy-03",
    "type": "Microsoft.DataProtection/backupVaults/backupPolicies",
    "properties": {
        "policyRules": [
            {
                "backupParameters": {
                    "backupType": "Incremental",
                    "objectType": "AzureBackupParams"
                },
                "trigger": {
                    "schedule": {
                        "repeatingTimeIntervals": [
                            "R/2021-07-01T19:00:00+00:00/P1D"
                        ],
                      },
                    "taggingCriteria": [
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
                    "dataStoreType": "OperationalStore",
                    "objectType": "DataStoreInfoBase"
                },
                "name": "BackupDaily",
                "objectType": "AzureBackupRule"
            },
            {
                "lifecycles": [
                    {
                        "deleteAfter": {
                            "objectType": "AbsoluteDeleteOption",
                            "duration": "P7D"
                        },
                        "targetDataStoreCopySettings": [],
                        "sourceDataStore": {
                            "dataStoreType": "OperationalStore",
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
            "Microsoft.Compute/disks"
        ],
        "objectType": "BackupPolicy"
    }
}
```

## <a name="next-steps"></a>次のステップ

[Azure ディスクの保護を有効にする](backup-azure-dataprotection-use-rest-api-backup-disks.md)

Azure Backup REST API について詳しくは、次の記事をご覧ください。

- [Azure Data Protection REST API](/rest/api/dataprotection/)
- [Azure Rest API の開始](/rest/api/azure/)
