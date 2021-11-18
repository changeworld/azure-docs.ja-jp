---
title: Azure Data Protection REST APIを介してAzure PostgreSQLデータベースを復元する
description: Azure Data Protection REST APIを使用してAzure PostGreSQLデータベースを復元する方法を学びます。
ms.topic: conceptual
ms.date: 10/23/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: 0d30731af6366a859b86341b318b1f8d9b5663a2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714202"
---
# <a name="restore-azure-postgresql-databases-using-azure-data-protection-rest-api"></a>Azure Data Protection REST APIを介してAzure PostgreSQLデータベースを復元する

この記事では、Azure Backup によってバックアップされた Azure PostgreSQL サーバーに[Azure PostgreSQL データベース](../postgresql/overview.md#azure-database-for-postgresql---single-server)を復元する方法について説明します。

PaaS データベースの場合、既存のデータベースを (バックアップが作成された場所から) 置き換えることによって復元する、元の場所への復旧 (OLR) オプションはサポートされていません。 復旧ポイントから復元して、同じ Azure PostgreSQL サーバーまたは他の PostgreSQL サーバーに新しいデータベースを作成できます。 これは、Alternate-Location Recovery (ALR) と呼ばれ、ソース データベースと復元された (新しい) データベースの両方を保持するのに役立ちます。

この記事では、次の方法について学習します。

- PostgreSQL データベースを復元して新しく作成する

- 復元操作の状態を追跡する

## <a name="prerequisites"></a>前提条件

- [バックアップ コンテナーの作成](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [PostgreSQL データベース バックアップ ポリシーを作成する](backup-azure-data-protection-use-rest-api-create-update-postgresql-policy.md)

- [PostgreSQL データベースを構成する](backup-azure-data-protection-use-rest-api-backup-postgresql.md)

この例では、リソース グループ _testBkpVaultRG_ の下にある既存のバックアップ コンテナー _TestBkpVault_ を参照します。

## <a name="restore-a-backed-up-postgresql-database"></a>バックアップされた PostgreSQL データベースを復元する

### <a name="set-up-permissions"></a>アクセス許可の設定

バックアップボールトは、マネージド IDを使用して他のAzureリソースにアクセスします。 バックアップから復元するには、バックアップボールトのマネージド ID に、データベースを復元する Azure PostgreSQL サーバーに対する一連のアクセス許可が必要です。

ターゲット PostgreSQL サーバーでボールトのシステム割り当てマネージド ID に関連するアクセス許可を割り当てるには、[Azure PostgreSQL データベースをバックアップするために必要な一連のアクセス許可](./backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-restore)を参照してください。

回復ポイントをファイルとしてストレージ アカウントに復元するには、[以下](./restore-azure-database-postgresql.md#restore-permissions-on-the-target-storage-account)に言及されたバックアップボールトのシステム割り当てマネージド ID がターゲット ストレージ アカウントにアクセスできる必要があります。

### <a name="fetching-the-relevant-recovery-point"></a>適切な復元ポイントのフェッチ

バックアップ インスタンスで使用可能なすべての復旧ポイントの一覧を取得するには、[復旧ポイントの一覧取得](/rest/api/dataprotection/recovery-points/list) API を使用します。

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/recoveryPoints?api-version=2021-07-01
```

たとえば、この API は下記のように変換されます。

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149/recoveryPoints?api-version=2021-07-01
```

#### <a name="responses-for-list-of-recovery-points"></a>復旧ポイントの一覧の応答

*GET* 要求を送信すると、200 (OK) の応答と、個別の復旧ポイントの一覧および関連するすべての詳細が返されます。

|名前  |型  |説明  |
|---------|---------|---------|
|200 OK     |    [AzureBackupRecoveryPointResourceList](/rest/api/dataprotection/recovery-points/list#azurebackuprecoverypointresourcelist)     |   OK      |
|その他の状態コード     |    [CloudError](/rest/api/dataprotection/recovery-points/list#clouderror)     |     エラー応答では、操作が失敗した理由が示されています。    |

##### <a name="example-response-for-list-of-recovery-points"></a>復旧ポイントの一覧の応答例

```http
HTTP/1.1 200 OK
Content-Length: 53396
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 11999
x-ms-correlation-request-id: 41f7ef85-f31e-4db7-87ef-115e3ca65b93
x-ms-routing-request-id: SOUTHINDIA:20211022T200018Z:ba3bc1ce-c081-4895-a292-beeeb6eb22cc
Cache-Control: no-cache
Date: Fri, 22 Oct 2021 20:00:18 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "properties": {
        "objectType": "AzureBackupDiscreteRecoveryPoint",
        "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b",
        "recoveryPointTime": "2021-10-21T16:31:16.8316716Z",
        "recoveryPointType": "Full",
        "friendlyName": "794ead7c7661410da03997d210d469e7",
        "recoveryPointDataStoresDetails": [
          {
            "id": "9ea7eaf4-eeb8-4c8f-90a7-7f04b60bf075",
            "type": "VaultStore",
            "creationTime": "2021-10-21T16:31:16.8316716Z",
            "expiryTime": "2022-10-21T16:31:16.8316716Z",
            "metaData": null,
            "visible": true,
            "state": "COMMITTED",
            "rehydrationExpiryTime": null,
            "rehydrationStatus": null
          }
        ],
        "retentionTagName": "Default",
        "retentionTagVersion": "637212748405148394",
        "policyName": "osspol3",
        "policyVersion": null
      },
.
.
.
.
```

アーカイブ アクセス層から復旧ポイントをフェッチするには、_recoveryPointDataStoreDetails_ の _type_ 変数を _ArchiveStore_ として変更します。

上の一覧から関連する復旧ポイントを選択し、復元要求の準備に進みます。 上の一覧から _794ead7c7661410da03997d210d469e7_ という名前の復旧ポイントを選択して復元します。

### <a name="prepare-the-restore-request"></a>復元要求を準備する

PostgreSQL データベースにはさまざまな復元オプションがあります。 回復ポイントを別のデータベースとして復元したり、ファイルとして復元したりすることができます。 復旧ポイントはアーカイブ アクセス層にすることもできます。

#### <a name="restore-as-database"></a>データベースとして復元

作成する新しい PostgreSQL データベースの Azure Resource Managed ID (ARM ID) をコンストラクトします ([上記](#set-up-permissions)で詳しく説明したように、アクセス許可が割り当てられたターゲット PostgreSQL サーバーと、必要な PostgreSQL データベース名を指定します)。 たとえば、PostgreSQLデータベースは、異なるサブスクリプションを持つリソースグループ **targetrg** のターゲットPostgreSQLサーバー **targetossserver** の下で **emprestored21** という名前を付けることができます。

```http
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21"
```

次の要求本文には、復旧ポイント ID と復元ターゲットの詳細が含まれます。

```json
{
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "VaultStore",
    "restoreTargetInfo": {
      "objectType": "restoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "dataSourceInfo": {
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
        "resourceName": "emprestored21",
        "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceLocation": "westus",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
      },
      "dataSourceSetInfo": {
        "objectType": "DatasourceSet",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver",
        "resourceName": "targetossserver",
        "resourceType": "Microsoft.DBforPostgreSQL/servers",
        "resourceLocation": "westus",
        "resourceUri": "",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
      },
      "datasourceAuthCredentials": {
        "objectType": "SecretStoreBasedAuthCredentials",
        "secretStoreResource": {
          "secretStoreType": "AzureKeyVault",
          "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
          "value": null
        }
      },
      "restoreLocation": "westus"
    },
    "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b"
  }
}
```

アーカイブ ベースの復旧ポイントの場合は、次の手順を実行する必要があります。

1. アーカイブ データストアからボールトストアへのリヒドレートする。
1. ソース データストアを変更します。
1. リハイドレートの優先度を指定するための他のパラメーターを追加します。
1. リハイドレートされた復旧ポイントをボールトデータストアに保持する期間を指定します。

```json
{
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "ArchiveStore",
    "restoreTargetInfo": {
      "objectType": "restoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "dataSourceInfo": {
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
        "resourceName": "emprestored21",
        "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceLocation": "westus",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
      },
      "dataSourceSetInfo": {
        "objectType": "DatasourceSet",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver",
        "resourceName": "targetossserver",
        "resourceType": "Microsoft.DBforPostgreSQL/servers",
        "resourceLocation": "westus",
        "resourceUri": "",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
      },
      "datasourceAuthCredentials": {
        "objectType": "SecretStoreBasedAuthCredentials",
        "secretStoreResource": {
          "secretStoreType": "AzureKeyVault",
          "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
          "value": null
        }
      },
      "restoreLocation": "westus"
    },
    "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b",
    "rehydration_priority": "Standard",
    "rehydration_retention_duration": "P15D",
  }
}
```

#### <a name="restore-as-files"></a>ファイルとして復元

[上](#set-up-permissions)で詳しく説明したように、アクセス許可が割り当てられたストレージ アカウント内のコンテナーの URI をフェッチします。 たとえば、異なるサブスクリプションを持つストレージ アカウント **testossstorageaccount** の下で、コンテナーに **testcontainerrestore** という名前を付けられます。

```http
"https://testossstorageaccount.blob.core.windows.net/testcontainerrestore"
```

```json
{
  "objectType": "ValidateRestoreRequestObject",
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "VaultStore",
    "restoreTargetInfo": {
      "targetDetails": {
        "url": "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore",
        "filePrefix": "empdb11_postgresql-westus_1628853549768",
        "restoreTargetLocationType": "AzureBlobs"
      },
      "restoreLocation": "westus",
      "recoveryOption": "FailIfExists",
      "objectType": "RestoreFilesTargetInfo"
    },
    "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b"
  }
}
```

アーカイブベースの復旧ポイントの場合は、ソース データストアを変更し、リハイドレートの優先度と、リハイドレートされた復旧ポイントのリハイドレート期間 (日数) を以下に示すように追加します：

```json
{
  "objectType": "ValidateRestoreRequestObject",
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "ArchiveStore",
    "restoreTargetInfo": {
      "targetDetails": {
        "url": "https://testossstorageaccount.blob.core.windows.net/testcontainerrestore",
        "filePrefix": "empdb11_postgresql-westus_1628853549768",
        "restoreTargetLocationType": "AzureBlobs"
      },
      "restoreLocation": "westus",
      "recoveryOption": "FailIfExists",
      "objectType": "RestoreFilesTargetInfo"
    },
    "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b",
    "rehydration_priority": "Standard",
    "rehydration_retention_duration": "P15D",
  }
}
```

#### <a name="validate-restore-requests"></a>復元要求を検証する

要求本文の準備ができたら、[検証API](/rest/api/dataprotection/backup-instances/validate-for-restore)を使用して要求本文を検証します。 バックアップの検証 API と同様に、これは *POST* 操作です。

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/validateRestore?api-version=2021-07-01
```

たとえば、この API は下記のように変換されます。

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149/ValidateRestore?api-version=2021-07-01"
```

この POST API の要求本文の[詳細をご確認ください](/rest/api/dataprotection/backup-instances/validate-for-restore#request-body)。

##### <a name="request-body-to-validate-restore-request"></a>復元要求を検証する要求本文

[上のセクション](#create-a-request-body-for-restore-operations)の同じもののセクションを作成しました。 次に、オブジェクトの型を追加し、それを使用して検証操作をトリガーします。

```json
{
    "objectType": "ValidateRestoreRequestObject",
    "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b",
    "restoreRequestObject": {
      "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
      "sourceDataStoreType": "VaultStore",
      "restoreTargetInfo": {
        "objectType": "restoreTargetInfo",
        "recoveryOption": "FailIfExists",
        "dataSourceInfo": {
          "objectType": "Datasource",
          "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
          "resourceName": "emprestored21",
          "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
          "resourceLocation": "westus",
          "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
          "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
        },
        "dataSourceSetInfo": {
          "objectType": "DatasourceSet",
          "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver",
          "resourceName": "targetossserver",
          "resourceType": "Microsoft.DBforPostgreSQL/servers",
          "resourceLocation": "westus",
          "resourceUri": "",
          "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
        },
        "datasourceAuthCredentials": {
          "objectType": "SecretStoreBasedAuthCredentials",
          "secretStoreResource": {
            "secretStoreType": "AzureKeyVault",
            "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
            "value": null
          }
        },
        "restoreLocation": "westus"
      }
  }
}
```

##### <a name="response-to-validate-restore-requests"></a>復元の検証要求に対する応答

"_復元の検証要求_" は、"[非同期操作](../azure-resource-manager/management/async-operations.md)" です。 そのため、この操作は、個別に追跡する必要がある別の操作を作成します。

これにより、2 つの応答が返されます。別の操作が作成されたときは 202 (Accepted)、その操作が完了したときは 200 (OK) です。

|名前  |型  |説明  |
|---------|---------|---------|
|200 OK     |         |  検証要求の状態       |
|202 Accepted     |         |     承認済み    |

###### <a name="example-response-to-restore-validate-request"></a>復元の検証要求に対する応答の例

*POST* 操作が送信されると、最初の応答で 202 (Accepted) と _Azure-asyncOperation_ ヘッダーが返されます。

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 10
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-07-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: bae60c92-669d-45a4-aed9-8392cca7cc8d
x-ms-routing-request-id: CENTRALUSEUAP:20210708T205935Z:f51db7a4-9826-4084-aa3b-ae640dc78af6
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 20:59:35 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-07-01
X-Powered-By: ASP.NET
```

簡単な *GET* 要求を使用して、_Azure-AsyncOperation_ ヘッダーを追跡します。 要求が成功すると、200 (OK) と成功状態を示す応答が返されます。

```http
GET https://management.azure.com/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/providers/Microsoft.DataProtection/locations/westus/operationStatus/YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzY4NDNmZWZkLWU4ZTMtNDM4MC04ZTJhLWUzMTNjMmNhNjI1NA==?api-version=2021-07-01
```

```http
{
  "id": "/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/providers/Microsoft.DataProtection/locations/westus/operationStatus/YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzY4NDNmZWZkLWU4ZTMtNDM4MC04ZTJhLWUzMTNjMmNhNjI1NA==",
  "name": "YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzY4NDNmZWZkLWU4ZTMtNDM4MC04ZTJhLWUzMTNjMmNhNjI1NA==",
  "status": "Inprogress",
  "startTime": "2021-10-22T20:22:41.0305623Z",
  "endTime": "0001-01-01T00:00:00Z"
}
```

応答は、復元要求を送信する前に解決する必要があるエラーを示します。 次の例は、ターゲット データベースが以前のバージョンであるため、復元できない場合を表しています。

```http
---------- Response (1892 ms) ------------

HTTP/1.1 200 OK
Content-Length: 1236
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 11999
x-ms-correlation-request-id: 784764f8-941d-4f05-8d8c-c02d2c05f799
x-ms-routing-request-id: SOUTHINDIA:20211022T202725Z:e109a061-a09e-4f13-acd0-9b9833f851ac
Cache-Control: no-cache
Date: Fri, 22 Oct 2021 20:27:25 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/providers/Microsoft.DataProtection/locations/westus/operationStatus/YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzY4NDNmZWZkLWU4ZTMtNDM4MC04ZTJhLWUzMTNjMmNhNjI1NA==",
  "name": "YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzY4NDNmZWZkLWU4ZTMtNDM4MC04ZTJhLWUzMTNjMmNhNjI1NA==",
  "status": "Failed",
  "error": {
    "additionalInfo": [
      {
        "type": "UserFacingError",
        "info": {
          "message": "Restoring backups of a higher PostgreSQL version to a lower version is not supported.",
          "recommendedAction": [
            "Restore to the same or a higher PostgreSQL version from which the backup was taken."
          ],
          "code": "UserErrorRestoreToLowerVersion",
          "target": "",
          "innerError": {
            "code": "InnerErrorCodeUnavailable",
            "additionalInfo": {
              "DetailedNonLocalisedMessage": "Restoring backup from version:10 of PostgreSQL to 9.5 of PostgreSQL not supported, as the restore server version is lower."
            }
          },
          "isRetryable": false,
          "isUserError": false,
          "properties": {
            "ActivityId": "2a23524f-0217-4bc1-bbe8-1546d2e6204d-Ibz"
          }
        }
      }
    ],
    "code": "UserErrorRestoreToLowerVersion",
    "message": "Restoring backups of a higher PostgreSQL version to a lower version is not supported."
  },
  "startTime": "2021-10-22T20:22:41.0305623Z",
  "endTime": "2021-10-22T20:23:11Z"
}
```

エラーを修正して要求を再検証すると、200 (OK) は成功応答を返します。

```http
HTTP/1.1 200 OK
Content-Length: 443
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 11999
x-ms-correlation-request-id: 61d62dd8-8e1a-473c-bcc6-c6a7a19fb035
x-ms-routing-request-id: SOUTHINDIA:20211022T203846Z:89af04a6-4e91-4b64-8998-a369dc763408
Cache-Control: no-cache
Date: Fri, 22 Oct 2021 20:38:46 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/e3d2d341-4ddb-4c5d-9121-69b7e719485e/providers/Microsoft.DataProtection/locations/westus/operationStatus/YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzU0NDI4YzdhLTJjNWEtNDNiOC05ZjBjLTM2NmQ3ZWVjZDUxOQ==",
  "name": "YWJjMGRmMzQtNTY1NS00MGMyLTg4YmUtMTUyZDE3ZjdiNzMyOzU0NDI4YzdhLTJjNWEtNDNiOC05ZjBjLTM2NmQ3ZWVjZDUxOQ==",
  "status": "Succeeded",
  "startTime": "2021-10-22T20:28:24.3820169Z",
  "endTime": "2021-10-22T20:28:49Z"
}
```

#### <a name="trigger-restore-requests"></a>復元要求をトリガーする

復元のトリガー操作は ***POST*** API です。 復元のトリガー操作の[詳細をご確認ください](/rest/api/dataprotection/backup-instances/trigger-restore)。

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/restore?api-version=2021-07-01
```

たとえば、API は下記のように変換されます。

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149/restore?api-version=2021-07-01"
```

##### <a name="create-a-request-body-for-restore-operations"></a>復元操作のための要求本文を作成する

要求が検証されたら、同じ要求本文を少し変更したものを使用して "_復元要求_" をトリガーします。

###### <a name="example-request-body-for-restore"></a>復元の要求本文の例

_検証復元要求_ 本文からの唯一の変更は、開始時に _restoreRequest_ オブジェクトを削除することです。

```json
{
  "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
  "sourceDataStoreType": "VaultStore",
  "restoreTargetInfo": {
    "objectType": "restoreTargetInfo",
    "recoveryOption": "FailIfExists",
    "dataSourceInfo": {
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
      "resourceName": "emprestored21",
      "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
      "resourceLocation": "westus",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver/databases/emprestored21",
      "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
    },
    "dataSourceSetInfo": {
      "objectType": "DatasourceSet",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.DBforPostgreSQL/servers/targetossserver",
      "resourceName": "targetossserver",
      "resourceType": "Microsoft.DBforPostgreSQL/servers",
      "resourceLocation": "westus",
      "resourceUri": "",
      "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases"
    },
    "datasourceAuthCredentials": {
      "objectType": "SecretStoreBasedAuthCredentials",
      "secretStoreResource": {
        "secretStoreType": "AzureKeyVault",
        "uri": "https://vikottur-test.vault.azure.net/secrets/dbauth3",
        "value": null
      }
    },
    "restoreLocation": "westus"
  },
  "recoveryPointId": "eb006fde78cb47198be5a320fbe45e9b"
}
```

#### <a name="response-to-trigger-restore-requests"></a>復元要求のトリガーに対する応答

"_復元要求のトリガー_" は、"[非同期操作](../azure-resource-manager/management/async-operations.md)" です。 そのため、この操作では、個別に追跡する必要がある別の操作が作成されます。

これにより、2 つの応答が返されます。別の操作が作成されたときは 202 (Accepted)、その操作が完了したときは 200 (OK) です。

|名前  |型  |説明  |
|---------|---------|---------|
|200 OK     |         |  復元要求の状態       |
|202 Accepted     |         |     承認済み    |

##### <a name="example-response-to-trigger-restore-request"></a>復元要求のトリガーに対する応答の例

*POST* 操作が送信されると、最初の応答で 202 (Accepted) と _Azure-asyncOperation_ ヘッダーが返されます。

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 30
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-07-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1197
x-ms-correlation-request-id: 8661209c-5b6a-44fe-b676-4e2b9c296593
x-ms-routing-request-id: CENTRALUSEUAP:20210708T204652Z:69e3fa4b-c5d9-4601-9410-598006ada187
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 20:46:52 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-07-01
X-Powered-By: ASP.NET
```

簡単な *GET* 要求を使用して、_Azure-AsyncOperation_ ヘッダーを追跡します。 要求が成功すると、200 (OK) とジョブ ID が返されます。復元要求を完了するには、それをさらに追跡する必要があります。

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-07-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==",
  "status": "Succeeded",
  "startTime": "2021-07-08T20:46:52.4110868Z",
  "endTime": "2021-07-08T20:46:56Z",
  "properties": {
    "jobId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852",
    "objectType": "OperationJobExtendedInfo"
  }
}
```

#### <a name="track-jobs"></a>ジョブを追跡する

"_復元要求のトリガー_" によって、復元ジョブがトリガーされました。 結果のジョブ ID を追跡するには、[GET Jobs API](/rest/api/dataprotection/jobs/get) を使用します。

簡単な *GET* コマンドを使用して、上の [復元トリガーに対する応答](#example-response-to-trigger-restore-request)に存在する _JobId_ を追跡します。

```http
 GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852?api-version=2021-07-01
```

上記のジョブの状態は、復元ジョブが完了したと示されます。

## <a name="next-steps"></a>次の手順

- [Azure PostgreSQL Backup の概要](backup-azure-database-postgresql-overview.md)