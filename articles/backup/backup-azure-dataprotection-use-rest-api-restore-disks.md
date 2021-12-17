---
title: Azure Data Protection REST API を使用して Azure ディスクを復元する
description: この記事では、Azure Data Protection REST API を使用して Azure ディスクを復元する方法について説明します。
ms.topic: conceptual
ms.date: 10/06/2021
ms.assetid: 30f4e7ff-2a55-4a85-be44-55feedc24607
ms.openlocfilehash: d8898b407fdcbea154d9282ff7964a3d0fa0264c
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620899"
---
# <a name="restore-azure-disks-using-azure-data-protection-rest-api"></a>Azure Data Protection REST API を使用して Azure ディスクを復元する

この記事では、Azure Backup を使用して[ディスク](disk-backup-overview.md)を復元する方法について説明します。

>[!Note]
>- 現在、既存のソース ディスクを (バックアップが作成された場所から) 置き換えることによって復元する、元の場所への復旧 (OLR) オプションはサポートされていません。
>- 復旧ポイントから復元して、ソース ディスクと同じリソース グループか、または他の任意のリソース グループに新しいディスクを作成できます。 これは別の場所への復旧 (ALR) と呼ばれています。

この記事では、次の方法について学習します。

- 復元して新しいディスクを作成する

- 復元操作の状態を追跡する

## <a name="prerequisites"></a>前提条件

- [バックアップ コンテナーの作成](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [ディスク バックアップ ポリシーの作成](backup-azure-dataprotection-use-rest-api-create-update-disk-policy.md)

- [ディスク バックアップを構成する](backup-azure-dataprotection-use-rest-api-backup-disks.md)

この例では、リソース グループ _testBkpVaultRG_ の下にある既存のバックアップ コンテナー _TestBkpVault_ を参照します。ここで、Azure Disk の名前は _msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed_ です。

## <a name="restore-to-create-a-new-azure-disk"></a>復元して新しい Azure ディスクを作成する

### <a name="set-up-permissions"></a>アクセス許可の設定

バックアップ コンテナーは、マネージド ID を使用して他の Azure リソースにアクセスします。 バックアップから復元するには、バックアップ コンテナーのマネージド ID に、ディスクを復元する必要があるリソース グループに対する一連のアクセス許可が必要です。

バックアップ コンテナーでは、リソースあたり 1 つに制限され、このリソースのライフサイクルに関連付けられているシステム割り当てマネージド ID を使用します。 マネージド ID にアクセス許可を付与するには、Azure ロールベースのアクセス制御 (Azure RBAC) を使用します。 マネージド ID は、Azure リソースでのみ使用できる特定のサービス プリンシパルです。 [マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) の詳細を確認してください。

ディスクを復元または作成するターゲット リソース グループで、コンテナーのシステム割り当て済みマネージド ID に適切なアクセス許可を割り当てます。 [詳細については、こちらを参照してください](restore-managed-disks.md#restore-to-create-a-new-disk)。

### <a name="fetch-the-list-of-recovery-points"></a>復旧ポイントの一覧をフェッチする

バックアップ インスタンスで使用可能なすべての復旧ポイントの一覧を取得するには、[復旧ポイントの一覧取得](/rest/api/dataprotection/recovery-points/list) API を使用します。

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/recoveryPoints?api-version=2021-01-01
```

たとえば、この API は下記のように変換されます。

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/recoveryPoints?api-version=2021-01-01
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
Content-Length: 7550
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 11999
x-ms-correlation-request-id: f01e2448-bdc5-4971-aee4-2edd1945c719
x-ms-routing-request-id: CENTRALUSEUAP:20210830T173435Z:0063423e-8b5e-493e-bb2e-74b7c7947c6c
Cache-Control: no-cache
Date: Mon, 30 Aug 2021 17:34:34 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/recoveryPoints/a3d02fc3ab8a4c3a8cc26688c26d3356",
      "name": "a3d02fc3ab8a4c3a8cc26688c26d3356",
      "type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints",
      "properties": {
        "objectType": "AzureBackupDiscreteRecoveryPoint",
        "recoveryPointId": "a3d02fc3ab8a4c3a8cc26688c26d3356",
        "recoveryPointTime": "2021-08-30T10:02:11.6354913Z",
        "recoveryPointType": "Incremental",
        "friendlyName": "119ac243-a789-4a41-be24-0461bceb3888",
        "recoveryPointDataStoresDetails": [
          {
            "id": "13e7c1fe-005d-4b80-8532-c58d937132bb",
            "type": "OperationalStore",
            "creationTime": "2021-08-30T10:02:11.6354913Z",
            "expiryTime": "2021-09-06T10:02:11.6354913Z",
            "metaData": null,
            "visible": true,
            "state": "COMMITTED",
            "rehydrationExpiryTime": null,
            "rehydrationStatus": null
          }
        ],
        "retentionTagName": "Default",
        "retentionTagVersion": "637607428336647408",
        "policyName": "DiskBackupPolicy-03",
        "policyVersion": null
      }
    },
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/recoveryPoints/8f76ebc4c54847c09455d5785a150ce2",
      "name": "8f76ebc4c54847c09455d5785a150ce2",
      "type": "Microsoft.DataProtection/backupVaults/backupInstances/recoveryPoints",
      "properties": {
        "objectType": "AzureBackupDiscreteRecoveryPoint",
        "recoveryPointId": "8f76ebc4c54847c09455d5785a150ce2",
        "recoveryPointTime": "2021-08-29T10:01:50.7749008Z",
        "recoveryPointType": "Incremental",
        "friendlyName": "1ac5aa6b-3583-464f-9604-7490c04c2b22",
        "recoveryPointDataStoresDetails": [
          {
            "id": "13e7c1fe-005d-4b80-8532-c58d937132bb",
            "type": "OperationalStore",
            "creationTime": "2021-08-29T10:01:50.7749008Z",
            "expiryTime": "2021-09-05T10:01:50.7749008Z",
            "metaData": null,
            "visible": true,
            "state": "COMMITTED",
            "rehydrationExpiryTime": null,
            "rehydrationStatus": null
          }
        ],
        "retentionTagName": "Default",
        "retentionTagVersion": "637607428336647408",
        "policyName": "DiskBackupPolicy-03",
        "policyVersion": null
      }
    },
    .
    .
    .
    .
}
```

上の一覧から関連する復旧ポイントを選択し、復元要求の準備に進みます。 上の一覧から _a3d02fc3ab8a4c3a8cc26688c26d3356_ という名前の復旧ポイントを選択して復元します。

### <a name="prepare-the-restore-request"></a>復元要求を準備する

ターゲット リソース グループ ([上](#set-up-permissions)で説明したようにアクセス許可を割り当てたもの) と必要なディスク名を使用して作成される新しいディスクの Azure Resource Manager (ARM) ID を作成します。

たとえば、バックアップされたディスクとリージョンは同じでも異なるサブスクリプションの下に存在する _targetrg_ リソース グループの _APITestDisk2_ という名前のディスクを使用します。

#### <a name="construct-the-request-body-for-restore-request"></a>復元要求の要求本文を作成する

次の要求本文には、復旧ポイント ID と復元ターゲットの詳細が含まれます。

```json
{
  "recoveryPointId": "a3d02fc3ab8a4c3a8cc26688c26d3356",
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "OperationalStore",
    "restoreTargetInfo": {
      "objectType": "restoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "dataSourceInfo": {
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
        "resourceName": "APITestDisk2",
        "resourceType": "Microsoft.Compute/disks",
        "resourceLocation": "westUS",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
        "datasourceType": "Microsoft.Compute/disks"
      },
      "restoreLocation": "westUS"
    }
  }
}
```

#### <a name="validate-restore-requests"></a>復元要求を検証する

要求本文を準備したら、[復元の検証 API](/rest/api/dataprotection/backup-instances/validate-for-restore) を使用してそれを検証します。 バックアップの検証 API と同様に、これは *POST* 操作です。

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/validateRestore?api-version=2021-01-01
```

たとえば、この API は下記のように変換されます。

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/validateRestore?api-version=2021-01-01"
```

この POST API の要求本文の[詳細をご確認ください](/rest/api/dataprotection/backup-instances/validate-for-restore#request-body)。 

##### <a name="request-body-to-validate-restore-request"></a>復元要求を検証する要求本文

[上のセクション](#construct-the-request-body-for-restore-request)の同じもののセクションを作成しました。 次に、オブジェクトの型を追加し、それを使用して検証操作をトリガーします。

```json

{
  "objectType": "ValidateRestoreRequestObject",
  "recoveryPointId": "a3d02fc3ab8a4c3a8cc26688c26d3356",
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
    "sourceDataStoreType": "OperationalStore",
    "restoreTargetInfo": {
      "objectType": "restoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "dataSourceInfo": {
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
        "resourceName": "APITestDisk2",
        "resourceType": "Microsoft.Compute/disks",
        "resourceLocation": "westUS",
        "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
        "datasourceType": "Microsoft.Compute/disks"
      },
      "restoreLocation": "westUS"
    }
  }
}
```

##### <a name="response-to-validate-restore-requests"></a>復元の検証要求に対する応答

"_復元の検証要求_" は、"[非同期操作](../azure-resource-manager/management/async-operations.md)" です。 そのため、この操作では、個別に追跡する必要がある別の操作が作成されます。

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
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: bae60c92-669d-45a4-aed9-8392cca7cc8d
x-ms-routing-request-id: CENTRALUSEUAP:20210708T205935Z:f51db7a4-9826-4084-aa3b-ae640dc78af6
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 20:59:35 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

簡単な *GET* 要求を使用して、_Azure-AsyncOperation_ ヘッダーを追跡します。 要求が成功すると、200 (OK) と成功状態を示す応答が返されます。

```http
 GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==?api-version=2021-01-01

{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==",
  "name": "ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExOzVlNzMxZDBiLTQ3MDQtNDkzNS1hYmNjLWY4YWEzY2UzNTk1ZQ==",
  "status": "Succeeded",
  "startTime": "2021-07-08T20:59:35.0060264Z",
  "endTime": "2021-07-08T20:59:57Z"
}
```

#### <a name="trigger-restore-requests"></a>復元要求をトリガーする

復元のトリガー操作は ***POST*** API です。 復元のトリガー操作の[詳細をご確認ください](/rest/api/dataprotection/backup-instances/trigger-restore)。

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/restore?api-version=2021-01-01
```

たとえば、API は下記のように変換されます。

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed/restore?api-version=2021-01-01"
```

##### <a name="create-a-request-body-for-restore-operations"></a>復元操作のための要求本文を作成する

要求が検証されたら、同じ要求本文を少し変更したものを使用して "_復元要求_" をトリガーします。

###### <a name="example-request-body-for-restore"></a>復元の要求本文の例

復元の検証の要求本文から、先頭の _restoreRequest_ オブジェクトを削除し、オブジェクトの型を変更するだけです。

```json
{
  "objectType": "AzureBackupRecoveryPointBasedRestoreRequest",
  "recoveryPointId": "a3d02fc3ab8a4c3a8cc26688c26d3356",
  "sourceDataStoreType": "OperationalStore",
  "restoreTargetInfo": {
    "datasourceInfo": {
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
      "datasourceType": "Microsoft.Compute/disks",
      "resourceName": "APITestDisk2",
      "resourceType": "Microsoft.Compute/disks",
      "resourceLocation": "westUS",
      "objectType": "Datasource"
    },
    "restoreLocation": "westUS",
    "recoveryOption": "FailIfExists",
    "objectType": "RestoreTargetInfo"
  }
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
Azure-AsyncOperation: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-01-01
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1197
x-ms-correlation-request-id: 8661209c-5b6a-44fe-b676-4e2b9c296593
x-ms-routing-request-id: CENTRALUSEUAP:20210708T204652Z:69e3fa4b-c5d9-4601-9410-598006ada187
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 20:46:52 GMT
Location: https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationResults/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-01-01
X-Powered-By: ASP.NET
```

簡単な *GET* 要求を使用して、_Azure-AsyncOperation_ ヘッダーを追跡します。 要求が成功すると、200 (OK) とジョブ ID が返されます。復元要求を完了するには、それをさらに追跡する必要があります。

```http
GET https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/providers/Microsoft.DataProtection/locations/westus/operationStatus/ZmMzNDFmYWMtZWJlMS00NGJhLWE4YTgtMDNjYjI4Y2M5OTExO2Q1NDIzY2VjLTczYjYtNDY5ZC1hYmRjLTc1N2Q0ZTJmOGM5OQ==?api-version=2021-01-01

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

簡単な GET コマンドを使用して、上の [復元トリガーに対する応答](#example-response-to-trigger-restore-request)に存在する _JobId_ を追跡します。

```http
 GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852?api-version=2021-01-01

{
  "properties": {
    "activityID": "2881cc22-f527-4af4-9b34-46c6c7b72076-Ibz",
    "subscriptionId": "62b829ee-7936-40c9-a1c9-47a93f9f3965",
    "backupInstanceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msdiskbackup-2dc6eb5b-d008-4d68-9e49-7132d99da0ed",
    "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/DiskBackup-Policy",
    "dataSourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-DiskBackup/providers/Microsoft.Compute/disks/msdiskbackup",
    "vaultName": "testBkpVault",
    "backupInstanceFriendlyName": "msdiskbackup",
    "policyName": "DiskBackup-Policy",
    "sourceResourceGroup": "RG-DiskBackup",
    "dataSourceSetName": null,
    "dataSourceName": "msdiskbackup",
    "sourceDataStoreName": null,
    "destinationDataStoreName": null,
    "progressEnabled": false,
    "etag": "W/\"datetime'2021-08-26T07%3A18%3A16.157629Z'\"",
    "sourceSubscriptionID": "62b829ee-7936-40c9-a1c9-47a93f9f3965",
    "dataSourceLocation": "westUS",
    "startTime": "2021-08-26T07:12:09.940517Z",
    "endTime": "2021-08-26T07:18:15.6815066Z",
    "dataSourceType": "Microsoft.Compute/disks",
    "operationCategory": "Restore",
    "operation": "Restore",
    "status": "Completed",
    "restoreType": null,
    "isUserTriggered": true,
    "rehydrationPriority": null,
    "supportedActions": [
      ""
    ],
    "duration": "PT6M5.7409896S",
    "progressUrl": null,
    "errorDetails": null,
    "extendedInfo": {
      "backupInstanceState": null,
      "dataTransferedInBytes": null,
      "targetRecoverPoint": null,
      "sourceRecoverPoint": {
        "recoveryPointID": "3a512290ec6b43d6b9a644869f4a3b38",
        "recoveryPointTime": "2021-08-25T09:03:11.6889015Z"
      },
      "recoveryDestination": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/APITestDisk2",
      "subTasks": [
        {
          "taskId": 1,
          "taskName": "Trigger Restore",
          "taskStatus": "Completed",
          "taskProgress": null,
          "additionalDetails": null
        }
      ],
      "additionalDetails": null
    }
  },
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/3bc62c80-913f-47fa-b829-b7df476682be",
  "name": "3bc62c80-913f-47fa-b829-b7df476682be",
  "type": "Microsoft.DataProtection/BackupVaults/backupJobs"
}
```

上のジョブ状態は、復元ジョブが完了し、ディスクが指定したサブスクリプションとターゲット リソース グループに復旧されたことを示します。

## <a name="next-steps"></a>次の手順

[Azure ディスク バックアップの概要](disk-backup-overview.md)

Azure Backup REST API について詳しくは、次の記事をご覧ください。

- [Azure Data Protection プロバイダー REST API](/rest/api/dataprotection/)
- [Azure Rest API の開始](/rest/api/azure/)