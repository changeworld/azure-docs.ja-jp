---
title: Azure Data Protection REST API を使用して Azure PostgreSQL データベースをバックアップする
description: この記事では、REST API を使用して Azure PostgreSQL データベースのバックアップ操作を構成、開始、および管理する方法について説明します。
ms.topic: conceptual
ms.date: 10/22/2021
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.assetid: 55fa0a81-018f-4843-bef8-609a44c97dcd
ms.openlocfilehash: cc5decd55c9724b486e657dcac70f38eb8c875a5
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132719962"
---
# <a name="back-up-azure-postgresql-databases-using-azure-data-protection-via-rest-api"></a>REST API で Azure データ保護を使用して Azure PostgreSQL データベースをバックアップする

この記事では、REST API を使用して Azure PostgreSQL データベースのバックアップを管理する方法について説明します。

Azure PostgreSQL データベースのバックアップでサポートされるシナリオ、制限、認証メカニズムについては、[概要](backup-azure-database-postgresql-overview.md)に関するドキュメントをご覧ください。

## <a name="prerequisites"></a>前提条件

- [バックアップ コンテナーの作成](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [PostgreSQL バックアップ ポリシーを作成する](backup-azure-data-protection-use-rest-api-create-update-postgresql-policy.md)

## <a name="configure-backup"></a>バックアップの構成

コンテナーとポリシーを作成したら、Azure PostgreSQL データベースを保護するために考慮する必要がある重要なポイントが 3 つあります。

### <a name="key-entities-involved"></a>関連する主なエンティティ

#### <a name="postgresql-database-to-be-protected"></a>保護する PostgreSQL データベース

保護する PostgreSQL データベースの Azure Resource Manager ID (ARM ID) を取り込みます。 これは、データベースの識別子として機能します。 ここでは、別のサブスクリプションのリソース グループ **ossdemoRG** に存在する PostgreSQL サーバー **testposgresql** の下に、**empdb11** という名前のデータベースがある場合の例を使用します。 次の例では、bash を使用します。

```http
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11"
```

#### <a name="azure-key-vault"></a>Azure Key Vault

Azure Backup サービスでは、PostgreSQL データベースに接続するためのユーザー名とパスワードは格納されません。 代わりに、バックアップ管理者が "*キー*" をキー コンテナーにシードする必要があります。 その後、Azure Backup サービスはキー コンテナーにアクセスし、キーを読み取って、データベースにアクセスします。 関連するキーのシークレット識別子をメモします。 次の例では、bash を使用します。

```http
"https://testkeyvaulteus.vault.azure.net/secrets/ossdbkey"
```

#### <a name="backup-vault"></a>バックアップ資格情報コンテナー

バックアップ コンテナーを PostgreSQL サーバーに接続してから、キー コンテナーに存在するキーを使用してデータベースにアクセスする必要があります。 そのため、PostgreSQL サーバーとキー コンテナーへのアクセスが必要となります。 アクセス許可は、バックアップ コンテナーの MSI に付与されます。

PostgreSQL サーバー上のバックアップ コンテナーの MSI と Azure Key Vault (データベースのキーが格納されている) に付与する必要がある[適切な権限について確認してください](./backup-azure-database-postgresql-overview.md#set-of-permissions-needed-for-azure-postgresql-database-backup)。

### <a name="prepare-the-request-to-configure-backup"></a>バックアップを構成するための要求の準備

関連するアクセス許可がコンテナーと PostgreSQL データベースに設定され、コンテナーとポリシーが構成されたら、バックアップを構成するための要求を準備できます。 次に、Azure PostgreSQL データベースのバックアップを構成するための要求本文を示します。 Azure PostgreSQL データベースの Azure Resource Manager ID (ARM ID) とその詳細は _datasourceinfo_ セクションで記述されており、ポリシーに関する情報は _policyinfo_ セクションにあります。

```json
{
  "backupInstance": {
    "dataSourceInfo": {
          "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
          "resourceUri": "",
          "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
          "resourceName": "empdb11",
          "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
          "resourceLocation": "westUS",
          "objectType": "Datasource"
      },
      "dataSourceSetInfo": {
          "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
          "resourceUri": "",
          "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
          "resourceName": "testpostgresql",
          "resourceType": "Microsoft.DBforPostgreSQL/servers",
          "resourceLocation": "westUS",
          "objectType": "DatasourceSet"
      },
      "policyInfo": {
          "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
          "policyVersion": ""
      },
    "objectType": "BackupInstance"
  }
}
```

### <a name="validate-the-request-to-configure-backup"></a>バックアップを構成するための要求の検証

バックアップを構成するための要求が成功するかどうかを検証するには、[バックアップの検証 API](/rest/api/dataprotection/backup-instances/validate-for-backup) を使用します。 応答を使用して必要な前提条件を実行し、その後でバックアップ要求の構成を送信できます。

バックアップ要求の検証は _POST_ 操作であり、URI には `{subscriptionId}`、`{vaultName}`、`{vaultresourceGroupName}` パラメーターがあります。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.DataProtection/backupVaults/{backupVaultName}/validateForBackup?api-version=2021-01-01
```

たとえば、この API は下記のように変換されます。

```http
POST https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/validateForBackup?api-version=2021-01-01
```

以前に準備した[要求本文](#prepare-the-request-to-configure-backup)を使用して、保護対象の Azure PostgreSQL データベースの詳細を指定します。

#### <a name="example-request-body"></a>要求本文の例

```json
{
  "backupInstance": {
    "dataSourceInfo": {
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
        "resourceUri": "",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceName": "empdb11",
        "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceLocation": "westUS",
        "objectType": "Datasource"
    },
    "dataSourceSetInfo": {
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
        "resourceUri": "",
        "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
        "resourceName": "testpostgresql",
        "resourceType": "Microsoft.DBforPostgreSQL/servers",
        "resourceLocation": "westUS",
        "objectType": "DatasourceSet"
    },
    "policyInfo": {
        "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
        "policyVersion": ""
    },
    "objectType": "BackupInstance"
  }
}
```

#### <a name="responses-for-backup-request-validation"></a>バックアップ要求の検証の応答

バックアップ要求の検証は、[非同期操作](../azure-resource-manager/management/async-operations.md)です。 そのため、この操作では、個別に追跡する必要がある別の操作が作成されます。

これにより、2 つの応答が返されます。別の操作が作成されたときは 202 (Accepted)、その操作が完了したときは 200 (OK) です。

|名前  |型  |説明  |
|---------|---------|---------|
|202 Accepted     |         |  操作は非同期で実行されます      |
|200 OK     |   [OperationJobExtendedInfo](/rest/api/dataprotection/backup-instances/validate-for-backup#operationjobextendedinfo)      |     同意    |
| その他の状態コード |    [CloudError](/rest/api/dataprotection/backup-instances/validate-for-backup#clouderror)    |    操作に失敗した理由を説明するエラー応答    |

##### <a name="example-responses-for-validate-backup-request"></a>バックアップ要求の検証の応答の例

###### <a name="error-response"></a>エラー応答

指定されたディスクが既に保護されている場合、応答として HTTP 400 (Bad request) が返され、指定されたディスクがバックアップ コンテナーに対して保護されていることが詳細情報と共に示されます。

```http
HTTP/1.1 400 BadRequest
Content-Length: 1012
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 0c99ff0f-6c26-4ec7-899f-205435e89894
x-ms-routing-request-id: WESTUS:20210830T142949Z:0be72802-02ad-485d-b91f-4aadd92c059c
Cache-Control: no-cache
Date: Mon, 30 Aug 2021 14:29:49 GMT
X-Powered-By: ASP.NET

{
  "error": {
    "additionalInfo": [
      {
        "type": "UserFacingError",
        "info": {
          "message": "Datasource is already protected under the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault.",
          "recommendedAction": [
            "Delete the backup instance testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149 from the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault to re-protect the datasource in any other vault."
          ],
          "details": null,
          "code": "UserErrorDppDatasourceAlreadyProtected",
          "target": "",
          "innerError": null,
          "isRetryable": false,
          "isUserError": false,
          "properties": {
            "ActivityId": "0c99ff0f-6c26-4ec7-899f-205435e89894"
          }
        }
      }
    ],
    "code": "UserErrorDppDatasourceAlreadyProtected",
    "message": "Datasource is already protected under the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault.",
    "target": null,
    "details": null
  }
}
```

###### <a name="track-response"></a>応答を追跡する

データソースが保護されていない場合、API はさらに検証を続行し、追跡操作を作成します。

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 10
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1197
x-ms-correlation-request-id: 3e7cacb3-65cd-4b3c-8145-71fe90d57327
x-ms-routing-request-id: WESTUS:20210707T124850Z:105f2105-6db1-44bf-8a34-45972a8ba861
Cache-Control: no-cache
Date: Wed, 07 Jul 2021 12:48:50 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

単純な *GET* コマンドで、_Azure-AsyncOperation_ ヘッダーを使用して結果の操作を追跡します

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "status": "Inprogress",
  "startTime": "2021-07-07T12:48:50.3432229Z",
  "endTime": "0001-01-01T00:00:00"
}
```

完了すると 200(OK) が返され、応答本文には、アクセス許可など、満たす必要のある追加の要件が表示されます。

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzM2NDdhZDNjLTFiNGEtNDU4YS05MGJkLTQ4NThiYjRhMWFkYg==",
  "status": "Failed",
  "error": {
    "additionalInfo": [
      {
        "type": "UserFacingError",
        "info": {
          "message": "Appropriate permissions to perform the operation is missing.",
          "recommendedAction": [
            "Grant appropriate permissions to perform this operation as mentioned at https://aka.ms/UserErrorMissingRequiredPermissions and retry the operation."
          ],
          "code": "UserErrorMissingRequiredPermissions",
          "target": "",
          "innerError": {
            "code": "UserErrorMissingRequiredPermissions",
            "additionalInfo": {
              "DetailedNonLocalisedMessage": "Validate for Protection failed. Exception Message: The client 'a8b24f84-f43c-45b3-aa54-e3f6d54d31a6' with object id 'a8b24f84-f43c-45b3-aa54-e3f6d54d31a6' does not have authorization to perform action 'Microsoft.Authorization/roleAssignments/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/providers/Microsoft.Authorization' or the scope is invalid. If access was recently granted, please refresh your credentials."
            }
          },
          "isRetryable": false,
          "isUserError": false,
          "properties": {
            "ActivityId": "3e7cacb3-65cd-4b3c-8145-71fe90d57327"
          }
        }
      }
    ],
    "code": "UserErrorMissingRequiredPermissions",
    "message": "Appropriate permissions to perform the operation is missing."
  },
  "startTime": "2021-07-07T12:48:50.3432229Z",
  "endTime": "2021-07-07T12:49:22Z"
}
```

すべてのアクセス許可を付与した後、検証要求を再送信して、結果の操作を追跡します。 すべての条件が満たされた場合、成功応答として 200 (OK) が返されます。

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzlhMjk2YWM2LWRjNDMtNGRjZS1iZTU2LTRkZDNiMDhjZDlkOA==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzlhMjk2YWM2LWRjNDMtNGRjZS1iZTU2LTRkZDNiMDhjZDlkOA==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzlhMjk2YWM2LWRjNDMtNGRjZS1iZTU2LTRkZDNiMDhjZDlkOA==",
  "status": "Succeeded",
  "startTime": "2021-07-07T13:03:54.8627251Z",
  "endTime": "2021-07-07T13:04:06Z"
}
```

### <a name="configure-backup-request"></a>バックアップ要求の構成

要求が検証されたら、同じものを[バックアップ インスタンス API の作成](/rest/api/dataprotection/backup-instances/create-or-update)に送信できます。 バックアップ インスタンスは、バックアップ コンテナー内の Azure Backup のデータ保護サービスで保護された項目を表します。 ここで、Azure PostgreSQL データベースはバックアップ インスタンスであり、上で検証したものと同じ要求本文に少し追加するだけで使用できます。

バックアップ インスタンスには一意の名前を使用します。 そのため、リソース名と一意識別子の組み合わせを使用することをお勧めします。 たとえば、次の操作では、_testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149_ を使用して、それをバックアップ インスタンス名としてマークします。

バックアップ インスタンスを作成または更新するには、次の ***PUT*** 操作を使用します。

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/{BkpvaultName}/backupInstances/{UniqueBackupInstanceName}?api-version=2021-01-01
```

たとえば、この API は下記のように変換されます。

```http
 PUT https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149?api-version=2021-01-01
```

#### <a name="create-the-request-for-configure-backup"></a>バックアップを構成するための要求の作成

バックアップ インスタンスを作成する場合、要求本文のコンポーネントは次のようになります。

|名前  |型  |説明  |
|---------|---------|---------|
|properties     |  [BackupInstance](/rest/api/dataprotection/backup-instances/create-or-update#backupinstance)       |     BackupInstanceResource プロパティ    |

##### <a name="example-request-for-configure-backup"></a>バックアップを構成するための要求の例

[上記](#configure-backup)で説明した一意の名前を持つ、バックアップ要求を検証するために使用したものと同じ要求本文を使用します。

```json
{
  "name": "testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
  "type": "Microsoft.DataProtection/backupvaults/backupInstances",
  "properties": {
    "dataSourceInfo": {
            "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
            "resourceUri": "",
            "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceName": "empdb11",
            "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceLocation": "westUS",
            "objectType": "Datasource"
        },
        "dataSourceSetInfo": {
            "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
            "resourceUri": "",
            "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceName": "testpostgresql",
            "resourceType": "Microsoft.DBforPostgreSQL/servers",
            "resourceLocation": "westUS",
            "objectType": "DatasourceSet"
        },
        "policyInfo": {
            "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
            "policyVersion": ""
        }
    },
    "objectType": "BackupInstance"
  }
}
```

#### <a name="responses-to-configure-backup-request"></a>バックアップ要求を構成するための応答

_バックアップ インスタンス要求_ の作成は、[非同期操作](../azure-resource-manager/management/async-operations.md)です。 そのため、この操作では、個別に追跡する必要がある別の操作が作成されます。

2 つの応答が返されます。つまり、バックアップ インスタンスが作成され、保護が構成されると、201 (Created) が返され、構成が完了すると 200 (OK) が返されます。

|名前  |型  |説明  |
|---------|---------|---------|
|201 Created   |   [バックアップ インスタンス](/rest/api/dataprotection/backup-instances/create-or-update#backupinstanceresource)      |  バックアップインスタンスが作成され、保護の構成中です      |
|200 OK     |    [バックアップ インスタンス](/rest/api/dataprotection/backup-instances/create-or-update#backupinstanceresource)     |     保護が構成されています    |
| その他の状態コード |    [CloudError](/rest/api/dataprotection/backup-instances/validate-for-backup#clouderror)    |    操作に失敗した理由を説明するエラー応答    |

##### <a name="example-responses-to-configure-backup-request"></a>バックアップ要求を構成するための応答の例

バックアップ インスタンスを作成するための *PUT* 要求を送信すると、最初の応答は 201 (Created) と _Azure-asyncOperation_ ヘッダーです。 要求本文には、すべてのバックアップ インスタンスのプロパティが含まれています。

```http
HTTP/1.1 201 Created
Content-Length: 1149
Content-Type: application/json
Expires: -1
Pragma: no-cache
Retry-After: 15
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 5d9ccf1b-7ac1-456d-8ae3-36c93c0d2427
x-ms-routing-request-id: WESTUS:20210707T170219Z:9e897266-5d86-4d13-b298-6561c60cf043
Cache-Control: no-cache
Date: Wed, 07 Jul 2021 17:02:18 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
    "properties": {
        "friendlyName": "testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
        "dataSourceInfo": {
            "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql/databases/empdb11",
            "resourceUri": "",
            "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceName": "empdb11",
            "resourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceLocation": "westUS",
            "objectType": "Datasource"
        },
        "dataSourceSetInfo": {
            "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/ossdemoRG/providers/Microsoft.DBforPostgreSQL/servers/testpostgresql",
            "resourceUri": "",
            "datasourceType": "Microsoft.DBforPostgreSQL/servers/databases",
            "resourceName": "testpostgresql",
            "resourceType": "Microsoft.DBforPostgreSQL/servers",
            "resourceLocation": "westUS",
            "objectType": "DatasourceSet"
        },
        "policyInfo": {
            "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/osspol3",
            "policyVersion": ""
        },
        "protectionStatus": {
            "status": "ProtectionConfigured"
        },
        "currentProtectionState": "ProtectionConfigured",
        "provisioningState": "Succeeded",
        "objectType": "BackupInstance"
    },
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
    "name": "testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149",
    "type": "Microsoft.DataProtection/backupVaults/backupInstances"
}
```

次に、単純な *GET* コマンドで、_Azure-AsyncOperation_ ヘッダーを使用して、結果の操作を追跡します。

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==?api-version=2021-01-01
```

操作が完了すると、応答本文で成功を示すメッセージと共に 200 (OK) が返されます。

```json
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzI1NWUwNmFlLTI5MjUtNDBkNy1iMjMxLTM0ZWZlMDA3NjdkYQ==",
  "status": "Succeeded",
  "startTime": "2021-07-07T17:02:19.0611871Z",
  "endTime": "2021-07-07T17:02:20Z"
}
```

### <a name="stop-protection-and-delete-data"></a>保護を停止してデータを削除する

Azure PostgreSQL データベースの保護を停止し、バックアップ データも削除するには、[削除操作](/rest/api/dataprotection/backup-instances/delete)を実行します。

保護の停止とデータの削除は、*DELETE* 操作です。

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}?api-version=2021-01-01
```

たとえば、この API は下記のように変換されます。

```http
DELETE "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/testpostgresql-empdb11-957d23b1-c679-4c94-ade6-c4d34635e149?api-version=2021-01-01"
```

#### <a name="responses-for-delete-protection"></a>保護の削除の応答

保護の *DELETE* は [非同期操作](../azure-resource-manager/management/async-operations.md)です。 そのため、この操作では、個別に追跡する必要がある別の操作が作成されます。

これにより、2 つの応答が返されます。別の操作が作成されたときは 202 (Accepted)、その操作が完了したときは 200 (OK) です。

|名前  |型  |説明  |
|---------|---------|---------|
|200 OK     |         |  削除要求の状態       |
|202 Accepted     |         |     承認済み    |

##### <a name="example-responses-for-delete-protection"></a>保護の削除の応答の例

*DELETE* 要求を送信すると、最初の応答は 202 (Accepted) と _Azure-asyncOperation_ ヘッダーです。

```http
HTTP/1.1 202 Accepted
Content-Length: 0
Expires: -1
Pragma: no-cache
Retry-After: 30
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-deletes: 14999
x-ms-correlation-request-id: fee7a361-b1b3-496d-b398-60fed030d5a7
x-ms-routing-request-id: WESTUS:20210708T071330Z:5c3a9f3e-53aa-4d5d-bf9a-20de5601b090
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 07:13:29 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

簡単な *GET* 要求を使用して、_Azure-AsyncOperation_ ヘッダーを追跡します。 要求が成功すると、200 (OK) と成功状態を示す応答が返されます。

```http
GET "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01"

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==",
  "status": "Succeeded",
  "startTime": "2021-07-08T07:13:30.23815Z",
  "endTime": "2021-07-08T07:13:46Z"
}
```

## <a name="next-steps"></a>次の手順

[Azure PostGreSQL データベースのバックアップからデータを復元する](restore-postgresql-database-use-rest-api.md)

Azure Backup REST API について詳しくは、次の記事をご覧ください。

- [Azure Data Protection プロバイダー REST API](/rest/api/dataprotection/)
- [Azure Rest API の開始](/rest/api/azure/)