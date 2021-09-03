---
title: データ保護 REST API を使用して BLOB のバックアップ ポリシーを作成する
description: この記事では、REST API を使用して BLOB 用のバックアップ ポリシーを作成および管理する方法について説明します。
ms.topic: conceptual
ms.date: 07/09/2021
ms.assetid: 472d6a4f-7914-454b-b8e4-062e8b556de3
ms.openlocfilehash: 96c76eb592b0fb7b94aa8da6c4f975878dc8c913
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471564"
---
# <a name="create-azure-data-protection-backup-policies-for-blobs-using-rest-api"></a>REST API を使用して BLOB 用の Azure データ保護バックアップ ポリシーを作成する

> [!IMPORTANT]
> ポリシーの作成と Azure BLOB のバックアップの構成に進む前に、[こちらのセクション](blob-backup-configure-manage.md#before-you-start)をお読みください。

通常、バックアップ ポリシーを使用してバックアップの保持期間とスケジュールを制御します。 BLOB の運用バックアップは継続的な性質のものであるため、バックアップを実行するスケジュールは必要ありません。 ポリシーは、基本的に保持期間を指定するために必要です。 バックアップ ポリシーを再利用して、複数のストレージ アカウントのコンテナーへのバックアップを構成できます。

>[!NOTE]
>長期間を対象にした復元の場合、復元操作の完了に時間がかかることがあります。 さらに、一連のデータの復元にかかる時間は、復元期間中に行われた書き込みと削除の操作回数に基づきます。 たとえば、100 万オブジェクトを持つアカウントで毎日 3,000 オブジェクトが追加され、毎日 1,000 オブジェクトが削除される場合、過去 30 日間のポイントまで復元するのに約 2 時間必要になります。 過去 90 日間の保有期間と復元は、この変更率のアカウントには推奨されません。

Azure Recovery Services コンテナー用のバックアップ ポリシーを作成する手順の概要については、ポリシーの [REST API に関するドキュメント](/rest/api/dataprotection/backup-policies/create-or-update)を参照してください。 ストレージ アカウントで BLOB 用のポリシーを作成するためのリファレンスとして、このドキュメントを使用してください。

## <a name="create-a-policy"></a>ポリシーの作成

> [!IMPORTANT]
> 現在、既存のポリシーの更新または変更はサポートされていません。 代わりに、必要な詳細情報を使用して新しいポリシーを作成し、関連するバックアップ インスタンスに割り当てます。

Azure Backup ポリシーを作成するには、次の *PUT* 操作を使用します

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupPolicies/{policyName}?api-version=2021-01-01
```

`{policyName}` と `{vaultName}` は URI で指定します。 その他の情報は要求本文で指定します。

## <a name="create-the-request-body"></a>要求本文を作成する

たとえば、BLOB のバックアップに対するポリシーを作成する場合、要求本文のコンポーネントは次のようになります。

|名前  |必須  |型  |説明  |
|---------|---------|---------|---------|
|properties     |   True      |  BaseBackupPolicy:[BackupPolicy](/rest/api/dataprotection/backup-policies/create-or-update#backuppolicy)      | BaseBackupPolicyResource のプロパティ        |

要求本文での定義の完全な一覧については、[バックアップ ポリシー REST API に関するドキュメント](/rest/api/dataprotection/backup-policies/create-or-update)をご覧ください。

### <a name="example-request-body"></a>要求本文の例

次の要求本文では、BLOB のバックアップに対するバックアップ ポリシーが定義されています。

次のようなポリシーです。

- 保持期間は 30 日です。
- バックアップはローカルであり、バックアップ コンテナーにデータは格納されないため、データストアは "操作ストア" です。

```json
{
  "properties": {
    "datasourceTypes": [
      "Microsoft.Storage/storageAccounts/blobServices"
    ],
    "objectType": "BackupPolicy",
    "policyRules": [
      {
        "name": "Default",
        "objectType": "AzureRetentionRule",
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
        ]
      }
    ]
  }
}
```

> [!IMPORTANT]
> 日時形式は、DateTime のみがサポートされます。 Time 形式のみはサポートされません。

## <a name="responses"></a>Responses

バックアップ ポリシーの作成と更新は同期操作であり、操作が正常に終了すると OK が返されます。

|名前  |型  |説明  |
|---------|---------|---------|
|200 OK     |     [BaseBackupPolicyResource](/rest/api/dataprotection/backup-policies/create-or-update#basebackuppolicyresource)     |  OK       |

### <a name="example-responses"></a>応答の例

操作が完了すると、応答本文にポリシーの内容が含まれる 200 (OK) が返されます。

```json
{
  "id": "/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups//TestBkpVaultRG/providers/Microsoft.RecoveryServices/vaults/testBkpVault/backupPolicies/TestBlobPolicy",
  "name": "TestBlobPolicy",
  "type": "Microsoft.DataProtection/backupVaults/backupPolicies",
  "properties": {
    "policyRules": [
      {
        "lifecycles": [
          {
            "deleteAfter": {
              "objectType": "AbsoluteDeleteOption",
              "duration": "P30D"
            },
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
      "Microsoft.Storage/storageAccounts/blobServices"
    ],
    "objectType": "BackupPolicy"
  }
}
```

## <a name="next-steps"></a>次のステップ

ストレージ アカウントで BLOB の保護を有効にします。

Azure Backup REST API について詳しくは、次のドキュメントをご覧ください。

- [Azure Data Protection REST API](/rest/api/dataprotection/)
- [Azure Rest API の開始](/rest/api/azure/)
