---
title: Azure Data Protection REST API を使用してストレージ アカウントの BLOB を復元する
description: この記事では、REST API を使用してストレージアカウントの BLOB を復元する方法について説明します。
ms.topic: conceptual
ms.date: 07/09/2021
ms.assetid: 9b8d21e6-3e23-4345-bb2b-e21040996afd
ms.openlocfilehash: 0ba444126026dc77d6e9b963edb6bed3d108c97b
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598746"
---
# <a name="restore-azure-blobs-to-point-in-time-using-azure-data-protection-rest-api"></a>Azure Data Protection REST API を使用して任意の時点に Azure BLOB を復元する

この記事では、Azure Backup を使用して [BLOB](blob-backup-overview.md) を任意の時点に復元する方法について説明します。

> [!IMPORTANT]
> Azure Backup を使用した Azure BLOB の復元に進む前に、[重要なポイント](blob-restore.md#before-you-start)を確認してください。

この記事では、次の方法について学習します。

- Azure BLOB を任意の時点に復元する

- 復元操作の状態を追跡する

## <a name="prerequisites"></a>前提条件

- [バックアップ コンテナーの作成](backup-azure-dataprotection-use-rest-api-create-update-backup-vault.md)

- [BLOB バックアップ ポリシーの作成](backup-azure-dataprotection-use-rest-api-create-update-blob-policy.md)

- [BLOB バックアップの作成](backup-azure-dataprotection-use-rest-api-backup-blobs.md)

この例では、リソース グループ _testBkpVaultRG_ の下にある既存のバックアップ コンテナー _TestBkpVault_ と、そこに含まれる "msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d" という名前のストレージ アカウント内の BLOB について説明します。

## <a name="restoring-azure-blobs-within-a-storage-account"></a>ストレージ アカウント内での Azure BLOB の復元

### <a name="fetching-the-valid-time-range-for-restore"></a>復元の有効な時間範囲をフェッチする

BLOB の運用バックアップは継続的に行うので、注意すべき復元元ポイントはありません。 代わりに、BLOB を任意の時点に復元できる有効な時間範囲をフェッチする必要があります。 この例では、過去 30 日以内に復元する有効な時間範囲を確認しましょう。

復元可能な時間範囲は、[復元可能な時間範囲の検索](/rest/api/dataprotection/restorable-time-ranges/find) API を使用して一覧表示できます。 これは、ストレージ アカウント内の BLOB の継続的バックアップの範囲を計算する操作をトリガーする *POST* API です。

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/findRestorableTimeRanges?api-version=2021-01-01
```

この例では、これを次のように変えます。

```http
POST https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d/findRestorableTimeRanges?api-version=2021-01-01
```

#### <a name="create-the-request-body-to-fetch-valid-time-ranges-for-restore"></a>復元の有効な時間範囲をフェッチする要求本文を作成する

有効な時間範囲を計算する操作をトリガーする場合、要求本文のコンポーネントは次のようになります。

|**名前**  |**Type**  |**説明**  |
|---------|---------|---------|
|sourceDatastoreType     |   [RestoreSourceDataStoreType](/rest/api/dataprotection/restorable-time-ranges/find#restoresourcedatastoretype)      |    復元するデータを含むデータストア     |
|startTime     |    String     |  復元範囲の一覧表示要求の開始時刻。 ISO 8601 形式です。       |
|endTime     |    String     |    復元範囲の一覧表示要求の終了時刻。 ISO 8601 形式です。     |

##### <a name="example-request-body-to-fetch-valid-time-range"></a>有効な時間範囲をフェッチする要求本文の例

次の要求本文では、復元できる連続データの時間範囲をフェッチするために必要なプロパティを定義しています。 BLOB バックアップはストレージ アカウント内に存在するため、データストアは "運用" データストアです。 検索プロセスを絞り込んで利用可能な時間範囲を返すのに役立つ、開始時刻と終了時刻を指定できます。

```json
{
  "sourceDataStoreType": "OperationalStore",
  "startTime": "",
  "endTime": ""
}
```

#### <a name="responses-for-fetch-valid-time-ranges"></a>有効な時間範囲のフェッチに対する応答

*POST* 要求を送信すると、200(OK) と、指定した開始時刻および終了時刻内で復元に利用できる時間範囲が応答として返されます。

|**名前**  |**Type**  |**説明**  |
|---------|---------|---------|
|200(OK)     |   [AzureBackupFindRestorableTimeRangesResponseResource](/rest/api/dataprotection/restorable-time-ranges/find#azurebackupfindrestorabletimerangesresponseresource)      |    OK     |
|その他の状態コード     |    [CloudError](/rest/api/dataprotection/restorable-time-ranges/find#clouderror)     |  操作が失敗した理由を説明するエラー応答。       |

##### <a name="example-response-for-fetch-valid-time-ranges"></a>有効な時間範囲のフェッチに対する応答の例

```http
HTTP/1.1 200 OK
Content-Length: 379
Content-Type: application/json
Expires: -1
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id:
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: a2b7c2d9-01f5-499a-b521-55da4862c79a
x-ms-routing-request-id: CENTRALUSEUAP:20210708T184646Z:4996a2bf-2df8-48a7-9b53-a552466a27f7
Cache-Control: no-cache
Date: Thu, 08 Jul 2021 18:46:45 GMT
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET

{
  "id": "msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d",
  "type": "Microsoft.DataProtection/backupVaults/backupInstances/findRestorableTimeRanges",
  "properties": {
    "restorableTimeRanges": [
      {
        "startTime": "2021-07-06T18:46:45.947728Z",
        "endTime": "2021-07-08T18:46:45.9932408Z",
        "objectType": "RestorableTimeRange"
      }
    ],
    "objectType": "AzureBackupFindRestorableTimeRangesResponse"
  }
}
```

### <a name="preparing-the-restore-request"></a>復元要求の準備

同じストレージ アカウントに復元する特定の時点が固定されると、復元のための複数のオプションを利用できます。

#### <a name="restoring-all-the-blobs-to-a-point-in-time"></a>任意の時点へすべての BLOB を復元する

このオプションを使用すると、ストレージ アカウント内のすべてのブロック BLOB が、選択した時点までロールバックすることで復元されます。 大量のデータが含まれている、または高チャーンが監視されているストレージ アカウントの場合、復元に時間がかかることがあります。

##### <a name="constructing-the-request-body-for-point-in-time-restore-of-all-blobs"></a>すべての BLOB を任意の時点に復元するための要求本文を作成する

このシナリオで注意すべき重要な点は次のとおりです。

- 復元は同じストレージ アカウントに対して実行されます。つまり、復元のターゲット オブジェクトはソース データソースと同じになります。 これは下の復元ターゲット情報セクションに反映されています。
- これらは継続的バックアップであるため、復元時間は特定の時点であり、個別の復旧ポイントではありません。
- すべての BLOB が復元されます。
- バックアップが存在するソース データストアは、同じストレージ アカウントです。 そのため、ソース データストアは "運用" データストアです。

```json
{
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryTimeBasedRestoreRequest",
    "restoreTargetInfo": {
      "objectType": "RestoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "restoreLocation": "westus",
      "datasourceInfo": {
        "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
        "resourceLocation": "westus",
        "resourceName": "msblobbackup",
        "resourceType": "Microsoft.Storage/storageAccounts",
        "resourceUri": ""
      }
    },
    "sourceDataStoreType": "OperationalStore",
    "recoveryPointTime": "2021-07-08T00:00:00.0000000Z"
  }
}
```

#### <a name="restoring-few-containers-to-a-point-in-time"></a>いくつかのコンテナーを任意の時点に復元する

このオプションを使用すると、コンテナーを最大 10 個まで選択して復元するか、またはプレフィックスの一致を使用して BLOB のサブセットを復元することができます。 単一のコンテナー内または複数のコンテナー全体の BLOB の最大 10 個の辞書式範囲を復元して、以前の特定の時点の状態にそれらの BLOB を戻すことができます。 プレフィックスを使用する場合は、次の点に注意してください。

- コンテナー名と BLOB プレフィックスを区切るには、スラッシュ (/) を使用できます
- 指定した範囲の開始は範囲に含まれますが、指定した範囲の終了は含まれません。

プレフィックスを使用して BLOB 範囲を復元する方法の[詳細を確認](blob-restore.md#use-prefix-match-for-restoring-blobs)します。

##### <a name="constructing-the-request-body-for-point-in-time-restore-of-selected-containers-or-few-blobs"></a>選択したコンテナーまたはいくつかの BLOB を任意の時点に復元するための要求本文を構築する

このシナリオで注意すべき重要な点は次のとおりです。

- 復元は同じストレージ アカウントに対して実行されます。つまり、復元のターゲット オブジェクトはソース データソースと同じになります。 これは下の復元ターゲット情報セクションに反映されています。
- これらは継続的バックアップであるため、復元時間は特定の時点であり、個別の復旧ポイントではありません。
- ストレージ アカウント内のいくつかの項目が復元されます。 それらはコンテナーか、またはプレフィックス パターンを持つ BLOB です。
- ソース データストア (バックアップが存在する場所) は、同じストレージ アカウントです。 そのため、ソース データストアは "運用" データストアです。

```json
{
  "restoreRequestObject": {
    "objectType": "AzureBackupRecoveryTimeBasedRestoreRequest",
    "restoreTargetInfo": {
      "objectType": "ItemLevelRestoreTargetInfo",
      "recoveryOption": "FailIfExists",
      "restoreLocation": "westus",
      "restoreCriteria": [
        {
          "objectType": "RangeBasedItemLevelRestoreCriteria",
          "minMatchingValue": "Container1",
          "maxMatchingValue": "Container10-0"
        }
      ],
      "datasourceInfo": {
        "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
        "objectType": "Datasource",
        "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
        "resourceLocation": "westus",
        "resourceName": "msblobbackup",
        "resourceType": "Microsoft.Storage/storageAccounts",
        "resourceUri": ""
      }
    },
    "sourceDataStoreType": "OperationalStore",
    "recoveryPointTime": "2021-07-08T00:00:00.0000000Z"
  }
}
```

#### <a name="validating-restore-requests"></a>復元要求を検証する

要求本文を準備したら、[復元の検証 API](/rest/api/dataprotection/backup-instances/validate-for-restore) を使用して検証できます。 バックアップの検証 API と同様に、これは *POST* 操作です。

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/validateRestore?api-version=2021-01-01
```

この例では、これを次のように変えます。

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d/validateRestore?api-version=2021-01-01"
```

この POST API の要求本文の詳細については、[こちら](/rest/api/dataprotection/backup-instances/validate-for-restore#request-body)を参照してください。 前のセクションでは、[すべての BLOB の復元](#constructing-the-request-body-for-point-in-time-restore-of-all-blobs)と、[いくつかの項目の復元](#constructing-the-request-body-for-point-in-time-restore-of-selected-containers-or-few-blobs)のシナリオに対して同じ構成を行いました。 同じものを使用して検証操作をトリガーします。

##### <a name="response-to-validate-restore-requests"></a>復元の検証要求に対する応答

復元の検証要求は、[非同期操作](../azure-resource-manager/management/async-operations.md)です。 つまり、この操作では、個別に追跡する必要がある別の操作が作成されます。

これにより、2 つの応答が返されます。別の操作が作成されたときは 202 (Accepted)、その操作が完了したときは 200 (OK) です。

|名前  |型  |説明  |
|---------|---------|---------|
|200 OK     |         |  検証要求の状態       |
|202 Accepted     |         |     承認済み    |

###### <a name="example-response-to-restore-validate-request"></a>復元の検証要求に対する応答の例

*POST* 操作が送信されると、最初に 202 Accepted と Azure-asyncOperation ヘッダーが応答として返されます。

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

単純な *GET* 要求を使用して、Azure-AsyncOperation ヘッダーを追跡します。 要求が成功すると、200 OK と成功状態の応答が返されます。

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

#### <a name="triggering-restore-requests"></a>復元要求をトリガーする

復元のトリガー操作は ***POST*** API です。 復元のトリガー操作の詳細については、[こちら](/rest/api/dataprotection/backup-instances/trigger-restore)を参照してください。

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DataProtection/backupVaults/{vaultName}/backupInstances/{backupInstanceName}/restore?api-version=2021-01-01
```

この例では、これを次のように変えます。

```http
POST "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d/restore?api-version=2021-01-01"
```

##### <a name="creating-a-request-body-for-restore-operations"></a>復元操作のための要求本文を作成する

要求が検証されたら、同じ要求本文を使用して、小さな変更を加えるだけで復元要求をトリガーできます。

###### <a name="example-request-body-for-all-blobs-restore"></a>すべての BLOB を復元する要求本文の例

復元の検証の要求本文から、冒頭の "restoreRequest" オブジェクトを削除するだけです。

```json
{
  "objectType": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "restoreTargetInfo": {
    "objectType": "RestoreTargetInfo",
    "recoveryOption": "FailIfExists",
    "restoreLocation": "westus",
    "datasourceInfo": {
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceLocation": "westus",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceUri": ""
    }
  },
  "sourceDataStoreType": "OperationalStore",
  "recoveryPointTime": "2021-07-08T00:00:00Z"
}
```

###### <a name="example-request-body-for-items-or-few-blobs-restore"></a>項目またはいくつかの BLOB を復元する要求本文の例

復元の検証の要求本文から、冒頭の "restoreRequest" オブジェクトを削除するだけです。

```json
{
  "objectType": "AzureBackupRecoveryTimeBasedRestoreRequest",
  "restoreTargetInfo": {
    "objectType": "ItemLevelRestoreTargetInfo",
    "recoveryOption": "FailIfExists",
    "restoreLocation": "westus",
    "restoreCriteria": [
      {
        "objectType": "RangeBasedItemLevelRestoreCriteria",
        "minMatchingValue": "Container1",
        "maxMatchingValue": "Container2"
      }
    ],
    "datasourceInfo": {
      "datasourceType": "Microsoft.Storage/storageAccounts/blobServices",
      "objectType": "Datasource",
      "resourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "resourceLocation": "westus",
      "resourceName": "msblobbackup",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceUri": ""
    }
  },
  "sourceDataStoreType": "OperationalStore",
  "recoveryPointTime": "2021-07-08T00:00:00.0000000Z"
}
```

#### <a name="response-to-trigger-restore-requests"></a>復元要求のトリガーに対する応答

復元要求のトリガーは、[非同期操作](../azure-resource-manager/management/async-operations.md)です。 つまり、この操作では、個別に追跡する必要がある別の操作が作成されます。

これにより、2 つの応答が返されます。別の操作が作成されたときは 202 (Accepted)、その操作が完了したときは 200 (OK) です。

|名前  |型  |説明  |
|---------|---------|---------|
|200 OK     |         |  復元要求の状態       |
|202 Accepted     |         |     承認済み    |

##### <a name="example-response-to-trigger-restore-request"></a>復元要求のトリガーに対する応答の例

*POST* 操作が送信されると、最初に 202 Accepted と Azure-asyncOperation ヘッダーが応答として返されます。

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

単純な *GET* 要求を使用して、Azure-AsyncOperation ヘッダーを追跡します。 要求が成功すると、200 OK とジョブ ID が返されます。復元要求を完了するには、それをさらに追跡する必要があります。

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

#### <a name="tracking-jobs"></a>ジョブの追跡

復元要求のトリガーによってトリガーされた復元ジョブとその結果のジョブ ID を、[GET Jobs API](/rest/api/dataprotection/jobs/get) を使用して追跡する必要があります。

単純な GET コマンドを使用して、上記の[復元のトリガーに対する応答](#example-response-to-trigger-restore-request)で返されたジョブ ID を追跡します。

```http
 GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852?api-version=2021-01-01

{
  "properties": {
    "activityID": "4195ca6c-e02d-11eb-b0b1-70bc105e2242",
    "subscriptionId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx",
    "backupInstanceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupInstances/msblobbackup-f2df34eb-5628-4570-87b2-0331d797c67d",
    "policyId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupPolicies/BlobBackup-Policy",
    "dataSourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
    "vaultName": "BV-JPE-GRS",
    "backupInstanceFriendlyName": "msblobbackup",
    "policyName": "BlobBackup-Policy",
    "sourceResourceGroup": "RG-BlobBackup",
    "dataSourceName": "msblobbackup",
    "progressEnabled": false,
    "etag": "W/\"datetime'2021-07-08T20%3A48%3A36.6999667Z'\"",
    "sourceSubscriptionID": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx",
    "dataSourceLocation": "westus",
    "startTime": "2021-07-08T20:44:19.5467125Z",
    "endTime": "2021-07-08T20:48:35.8297312Z",
    "dataSourceType": "Microsoft.Storage/storageAccounts/blobServices",
    "operationCategory": "Restore",
    "operation": "Restore",
    "status": "Completed",
    "isUserTriggered": true,
    "supportedActions": [
      ""
    ],
    "duration": "PT4M16.2830187S",
    "extendedInfo": {
      "sourceRecoverPoint": {
        "recoveryPointTime": "2021-07-08T00:00:00Z"
      },
      "recoveryDestination": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/RG-BlobBackup/providers/Microsoft.Storage/storageAccounts/msblobbackup",
      "subTasks": [
        {
          "taskId": 1,
          "taskName": "Trigger Restore",
          "taskStatus": "Completed"
        }
      ]
    }
  },
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourceGroups/TestBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/testBkpVault/backupJobs/c4bd49a1-0645-4eec-b207-feb818962852",
  "name": "c4bd49a1-0645-4eec-b207-feb818962852",
  "type": "Microsoft.DataProtection/BackupVaults/backupJobs"
}
```

上記のジョブの状態は、復元ジョブが完了し、すべての BLOB が指定された時点に復元されていることを示しています。

## <a name="next-steps"></a>次のステップ

[Azure BLOB バックアップの概要](blob-backup-overview.md)

Azure Backup REST API について詳しくは、次のドキュメントをご覧ください。

- [Azure Data Protection プロバイダー REST API](/rest/api/dataprotection/)
- [Azure Rest API の開始](/rest/api/azure/)