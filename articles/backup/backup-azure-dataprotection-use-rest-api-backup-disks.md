---
title: Azure データ保護 REST API を使用した Azure ディスクのバックアップ
description: この記事では、REST API を使用して Azure ディスクのバックアップ操作を構成、開始、および管理する方法について説明します。
ms.topic: conceptual
ms.date: 10/06/2021
ms.assetid: 6050a941-89d7-4b27-9976-69898cc34cde
ms.openlocfilehash: e01a0e528c3274ac6ddefe000311e2db7523bb5e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130264714"
---
# <a name="back-up-azure-disks-using-azure-data-protection-via-rest-api"></a>REST API と Azure データ保護を使用した Azure ディスクのバックアップ

この記事では、REST API を使用して Azure ディスクのバックアップを管理する方法について説明します。

Azure ディスク バックアップの使用可能なリージョン、サポートされるシナリオ、制限事項については、[サポート マトリックス](disk-backup-support-matrix.md)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

- [バックアップ コンテナーの作成](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [ディスク バックアップ ポリシーの作成](backup-azure-dataprotection-use-rest-api-create-update-disk-policy.md)

## <a name="configure-backup"></a>バックアップの構成

### <a name="key-entities-involved"></a>関連する主なエンティティ

コンテナーとポリシーを作成したら、Azure ディスクを保護するために考慮する必要がある重要なポイントが 3 つあります。

#### <a name="disk-to-be-protected"></a>保護するディスク

保護するディスクの ARM ID と場所をメモしておきます。 これは、ディスクの識別子として機能します。

```http
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup"
```

#### <a name="snapshot-resource-group"></a>スナップショット リソース グループ

ディスクのスナップショットは、サブスクリプション内のリソース グループに格納されます。 ガイドラインとして、Azure Backup サービスで使用されるスナップショット データストアとして専用のリソース グループを作成することをお勧めします。 専用のリソース グループを使用すると、リソース グループに対するアクセス許可を制限できるため、バックアップ データを安全かつ簡単に管理できます。 ディスクのスナップショットを配置するリソース グループの ARM ID をメモしておきます。

```http
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/snapshot-rg"
```

#### <a name="backup-vault"></a>バックアップ資格情報コンテナー

バックアップ資格情報コンテナーには、バックアップを有効にするために、ディスクに対するアクセス許可が必要です。 コンテナーのシステム割り当てマネージド ID は、そのようなアクセス許可を割り当てるために使用されます。

### <a name="assign-permissions"></a>アクセス許可の割り当て

コンテナー (コンテナーの MSI で表される) や関連するディスク、ディスク RG には、RBAC を介していくつかのアクセス許可を割り当てる必要があります。 この操作は、Azure portal または CLI で行うことができます。 関連するアクセス許可を割り当てるには、[マネージド ディスクのバックアップを構成するための前提条件](./backup-managed-disks-ps.md#assign-permissions)に関する記事を参照してください。

### <a name="prepare-the-request-to-configure-backup"></a>バックアップを構成するための要求の準備

関連するアクセス許可がコンテナーとディスクに設定され、コンテナーとポリシーが構成されたら、バックアップを構成するための要求を準備できます。 次に、Azure ディスクのバックアップを構成するための要求本文を示します。 Azure ディスクの Azure Resource Manager ID (ARM ID) とその詳細については、「datasourceinfo」セクションで説明されています。また、ポリシーに関する情報は「policyinfo」セクションにあります。そこでは、ポリシー パラメーターの 1 つとしてスナップショット リソース グループが提供されています。

```json
{
  "backupInstance": {
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Compute/",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup",
      "resourceLocation": "westUS",
      "resourceName": "msdiskbackup",
      "resourceType": "Microsoft.Compute/disks",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/DiskBackup-Policy",
      "policyParameters": {
        "dataStoreParametersList": [
          {
            "dataStoreType": "OperationalStore",
            "objectType": "AzureOperationalStoreParameters",
            "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/snapshot-rg"
          }
        ]
      }
    },
    "objectType": "BackupInstance"
  }
}
```

### <a name="validate-the-request-to-configure-backup"></a>バックアップを構成するための要求の検証

バックアップを構成するための要求が成功するかどうかを検証するには、[バックアップ API に対する検証](/rest/api/dataprotection/backup-instances/validate-for-backup)を使用します。 応答を使用して必要な前提条件を実行し、その後でバックアップ要求の構成を送信できます。

バックアップ要求の検証は _POST_ 操作であり、URI には `{subscriptionId}`、`{vaultName}`、`{vaultresourceGroupName}` パラメーターがあります。

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.DataProtection/backupVaults/{backupVaultName}/validateForBackup?api-version=2021-01-01
```

たとえば、この API は下記のように変換されます。

```http
POST https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/validateForBackup?api-version=2021-01-01
```

以前に準備した[要求本文](#prepare-the-request-to-configure-backup)を使用して、保護対象の Azure ディスクの詳細が指定されます。

#### <a name="example-request-body"></a>要求本文の例

```json
{
  "backupInstance": {
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Compute/disks",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup",
      "resourceLocation": "westUS",
      "resourceName": "msdiskbackup",
      "resourceType": "Microsoft.Compute/disks",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/DiskBackup-Policy",
      "policyParameters": {
        "dataStoreParametersList": [
          {
            "dataStoreType": "OperationalStore",
            "objectType": "AzureOperationalStoreParameters",
            "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/snapshot-rg"
          }
        ]
      }
    },
    "objectType": "BackupInstance"
  }
}
```

#### <a name="responses-for-backup-request-validation"></a>バックアップ要求の検証の応答

バックアップ要求の検証は、[非同期操作](../azure-resource-manager/management/async-operations.md)です。 そのため、この操作では、個別に追跡する必要がある別の操作が作成されます。

これにより、2 つの応答が返されます。別の操作が作成されたときは 202 (Accepted)、その操作が完了したときは 200 (OK) です。

|名前  |Type  |説明  |
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
x-ms-routing-request-id: CENTRALUSEUAP:20210830T142949Z:0be72802-02ad-485d-b91f-4aadd92c059c
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
            "Delete the backup instance SharedDataDisk from the Backup vault /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault to re-protect the datasource in any other vault."
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

###### <a name="tracking-response"></a>応答の追跡

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
x-ms-routing-request-id: CENTRALUSEUAP:20210707T124850Z:105f2105-6db1-44bf-8a34-45972a8ba861
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
              "DetailedNonLocalisedMessage": "Validate for Protection failed. Exception Message: The client 'a8b24f84-f43c-45b3-aa54-e3f6d54d31a6' with object id 'a8b24f84-f43c-45b3-aa54-e3f6d54d31a6' does not have authorization to perform action 'Microsoft.Authorization/roleAssignments/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup/providers/Microsoft.Authorization' or the scope is invalid. If access was recently granted, please refresh your credentials."
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

すべてのアクセス許可を付与している場合、検証要求を再送信し、結果の操作を追跡します。すべての条件が満たされている場合、成功応答として 200 (OK) が返されます。

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

要求が検証されたら、同じものを[バックアップ インスタンス API の作成](/rest/api/dataprotection/backup-instances/create-or-update)に送信できます。 バックアップ インスタンスは、バックアップ コンテナー内の Azure Backup のデータ保護サービスで保護された項目を表します。 ここで、Azure ディスクはバックアップ インスタンスであり、上記で検証したものと同じ要求本文に少し追加するだけで使用できます。

バックアップ インスタンスには一意の名前を使用します。 そのため、リソース名と一意識別子の組み合わせを使用することをお勧めします。 たとえば、次の操作では、_msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed_ を使用して、バックアップ インスタンス名としてマークします。

バックアップ インスタンスを作成または更新するには、次の ***PUT*** 操作を使用します。

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/{BkpvaultName}/backupInstances/{UniqueBackupInstanceName}?api-version=2021-01-01
```

たとえば、この API は下記のように変換されます。

```http
 PUT https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed?api-version=2021-01-01
```

#### <a name="create-the-request-for-configure-backup"></a>バックアップを構成するための要求の作成

バックアップ インスタンスを作成する場合、要求本文のコンポーネントは次のようになります。

|名前  |Type  |説明  |
|---------|---------|---------|
|properties     |  [BackupInstance](/rest/api/dataprotection/backup-instances/create-or-update#backupinstance)       |     BackupInstanceResource プロパティ    |

##### <a name="example-request-for-configure-backup"></a>バックアップを構成するための要求の例

[上記](#configure-backup)で説明した一意の名前を持つ、バックアップ要求を検証するために使用したものと同じ要求本文を使用します。

```json
{
  "name": "msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed",
  "type": "Microsoft.DataProtection/backupvaults/backupInstances",
  "properties": {
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Compute/disks",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup",
      "resourceLocation": "westUS",
      "resourceName": "msdiskbackup",
      "resourceType": "Microsoft.Compute/disks",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/DiskBackup-Policy",
      "policyParameters": {
        "dataStoreParametersList": [
          {
            "dataStoreType": "OperationalStore",
            "objectType": "AzureOperationalStoreParameters",
            "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/snapshot-rg"
          }
        ]
      }
    },
    "objectType": "BackupInstance"
  }
}
```

#### <a name="responses-to-configure-backup-request"></a>バックアップ要求を構成するための応答

_バックアップ インスタンス要求_ の作成は、[非同期操作](../azure-resource-manager/management/async-operations.md)です。 そのため、この操作では、個別に追跡する必要がある別の操作が作成されます。

2 つの応答が返されます。つまり、バックアップ インスタンスが作成され、保護が構成されると、201 (Created) が返され、構成が完了すると 200 (OK) が返されます。

|名前  |Type  |説明  |
|---------|---------|---------|
|201 Created   |   [バックアップ インスタンス](/rest/api/dataprotection/backup-instances/create-or-update#backupinstanceresource)      |  バックアップインスタンスが作成され、保護の構成中です      |
|200 OK     |    [バックアップ インスタンス](/rest/api/dataprotection/backup-instances/create-or-update#backupinstanceresource)     |     保護が構成されています    |
| その他の状態コード |    [CloudError](/rest/api/dataprotection/backup-instances/validate-for-backup#clouderror)    |    操作に失敗した理由を説明するエラー応答    |

##### <a name="example-responses-to-configure-backup-request"></a>バックアップ要求を構成するための応答の例

バックアップ インスタンスを作成するための *PUT* 要求を送信すると、Azure-asyncOperation ヘッダーとともに、初期応答 201 (Created) が返されます。 要求本文には、すべてのバックアップ インスタンスのプロパティが含まれています。

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
x-ms-routing-request-id: CENTRALUSEUAP:20210707T170219Z:9e897266-5d86-4d13-b298-6561c60cf043
Cache-Control: no-cache
Date: Wed, 07 Jul 2021 17:02:18 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed",
  "name": "msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed",
  "type": "Microsoft.DataProtection/backupVaults/backupInstances",
  "properties": {
    "friendlyName": "msdiskbackup",
    "dataSourceInfo": {
      "datasourceType": "Microsoft.Compute/disks",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup",
      "resourceLocation": "westUS",
      "resourceName": "msdiskbackup",
      "resourceType": "Microsoft.Compute/disks",
      "resourceUri": ""
    },
    "policyInfo": {
      "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/DiskBackup-Policy",
      "policyParameters": {
        "dataStoreParametersList": [
          {
            "dataStoreType": "OperationalStore",
            "objectType": "AzureOperationalStoreParameters",
            "resourceGroupId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/snapshot-rg"
          }
        ]
      },
    "protectionStatus": {
      "status": "ConfiguringProtection"
    },
    "currentProtectionState": "ConfiguringProtection",
    "provisioningState": "Provisioning",
    "objectType": "BackupInstance"
  }
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

Azure ディスクの保護を停止し、バックアップ データも削除するには、[削除操作](/rest/api/dataprotection/backup-instances/delete)を実行します。

保護の停止とデータの削除は、*DELETE* 操作です。

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}?api-version=2021-01-01
```

たとえば、この API は下記のように変換されます。

```http
DELETE "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed?api-version=2021-01-01"
```

#### <a name="responses-for-delete-protection"></a>保護の削除の応答

保護の *DELETE* は [非同期操作](../azure-resource-manager/management/async-operations.md)です。 そのため、この操作では、個別に追跡する必要がある別の操作が作成されます。

これにより、2 つの応答が返されます。別の操作が作成されたときは 202 (Accepted)、その操作が完了したときは 200 (OK) です。

|名前  |Type  |説明  |
|---------|---------|---------|
|200 OK     |         |  削除要求の状態       |
|202 Accepted     |         |     承認済み    |

##### <a name="example-responses-for-delete-protection"></a>保護の削除の応答の例

*DELETE* 要求を送信すると、Azure-asyncOperation ヘッダーと共に、202 (Accepted) が初期応答として返されます。

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
x-ms-routing-request-id: CENTRALUSEUAP:20210708T071330Z:5c3a9f3e-53aa-4d5d-bf9a-20de5601b090
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 07:13:29 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzE1ZjM4YjQ5LWZhMGQtNDMxOC1iYjQ5LTExMDJjNjUzNjM5Zg==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

単純な *GET* 要求を使用して、_Azure-AsyncOperation_ ヘッダーを追跡します。 要求が成功すると、200 OK と成功状態を示す応答が返されます。

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

## <a name="next-steps"></a>次のステップ

[Azure ディスク バックアップからデータを復元します](backup-azure-arm-userestapi-restoreazurevms.md)。

Azure Backup REST API について詳しくは、次の記事をご覧ください。

- [Azure Data Protection プロバイダー REST API](/rest/api/dataprotection/)
- [Azure Rest API の開始](/rest/api/azure/)