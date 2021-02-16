---
title: REST API を使用した MongoDB 用 Azure Cosmos DB API での復元可能なデータベースの一覧表示
description: 復元可能なアカウントの元にあるすべての Azure Cosmos DB MongoDB データベースで実行されたすべての変更のイベント フィードを表示します。 これは、データベースが誤って削除された場合に、削除時点を取得するために役立ちます。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 5ebb0a23822074f61a16bf1d7652dba589399542
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537512"
---
# <a name="list-restorable-databases-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>REST API を使用した MongoDB 用 Azure Cosmos DB API での復元可能なデータベースの一覧表示

> [!IMPORTANT]
> Azure Cosmos DB の特定の時点への復元機能 (継続的バックアップ モード) は、現在、パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

復元可能なアカウントの元にあるすべての Azure Cosmos DB MongoDB データベースで実行されたすべての変更のイベント フィードを表示します。 これは、データベースが誤って削除された場合に、削除時点を取得するために役立ちます。 この API には `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 権限が必要です

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbDatabases?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>URI パラメーター

| 名前 | / | 必須 | Type | 説明 |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |string| 復元可能なデータベース アカウントの instanceId GUID。 |
| **location** | path | True | string| 単語間にスペースがあり、各単語が大文字になった Azure Cosmos DB 領域。 |
| **subscriptionId** | path | True | string| ターゲット サブスクリプションの ID。 |
| **api-version** | query | True | string | この操作に使用する API バージョン。 |

## <a name="responses"></a>Responses

| 名前 | Type | 説明 |
| --- | --- | --- |
| 200 OK | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult)| 操作は正常に完了しました。 |
| その他の状態コード | [DefaultErrorResponse](#defaulterrorresponse)| 操作に失敗した理由を説明するエラー応答。|

## <a name="examples"></a>例

### <a name="cosmosdbrestorablemongodbdatabaselist"></a>CosmosDBRestorableMongodbDatabaseList

**サンプル要求**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases?api-version=2020-06-01-preview
```

**応答のサンプル**

状態コード:200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableMongodbDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Create"
        }
      }
    },
    {
      "id": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Delete"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>定義

|定義  | 説明| | --- || --- | | [DefaultErrorResponse](#defaulterrorresponse) | サービスからのエラー応答。 | | [ErrorResponse](#errorresponse) | エラー応答。 | | [OperationType](#operationtype) | イベントの操作の種類を示す列挙。 | | [Resource](#resource) | MongoDB 用 Azure Cosmos DB API データベース イベントのリソース | | [RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult) | MongoDB 用 Azure Cosmos DB API データベース イベント | | [RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties) | MongoDB 用 Azure Cosmos DB API データベース イベントのプロパティ | | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult) | リスト操作の応答。MongoDB 用 Azure Cosmos DB API データベース イベントとそのプロパティが含まれます。 |

### <a name="defaulterrorresponse"></a><a id="defaulterrorresponse"></a>DefaultErrorResponse

サービスからのエラー応答。

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| エラー | [ErrorResponse](#errorresponse)| エラー応答。 |

### <a name="errorresponse"></a><a id="errorresponse"></a>ErrorResponse

エラー応答。

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| code |string| エラー コード。 |
| message |string| 操作が失敗した理由を示すエラー メッセージ。 |

### <a name="operationtype"></a><a id="operationtype"></a>OperationType

イベントの操作の種類を示す列挙。

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| 作成 |string|データベース作成イベント|
| 削除 |string|データベース削除イベント|
| Replace |string|データベース変更イベント|

### <a name="resource"></a><a id="resource"></a>リソース

MongoDB 用 Azure Cosmos DB API データベース イベントのリソース

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| _rid |string| システムによって生成されるプロパティ。 一意識別子。 |
| eventTimestamp |string| このデータベース イベントが発生した時刻。 |
| operationType |[[OperationType]](#operationtype)| このデータベース イベントの操作の種類。  |
| ownerId |string| MongoDB 用 Azure Cosmos DB API データベースの名前。|
| ownerResourceId |string| MongoDB 用 Azure Cosmos DB API データベースのリソース ID。 |

### <a name="restorablemongodbdatabasegetresult"></a><a id="restorablemongodbdatabasegetresult"></a>RestorableMongodbDatabaseGetResult

MongoDB 用 Azure Cosmos DB API データベース イベント

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| id |string| Azure Resource Manager のリソースの一意リソース識別子。 |
| name |string| Azure Resource Manager リソースの名前。 |
| properties |[RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties)| MongoDB 用 Azure Cosmos DB API データベース イベントのプロパティ。 |
| type |string| Azure リソースの種類。 |

### <a name="restorablemongodbdatabaseproperties"></a><a id="restorablemongodbdatabaseproperties"></a>RestorableMongodbDatabaseProperties

MongoDB 用 Azure Cosmos DB API データベース イベントのプロパティ

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| resource |[リソース](#resource)| MongoDB 用 Azure Cosmos DB API データベース イベントのリソース |

### <a name="restorablemongodbdatabaseslistresult"></a><a id="restorablemongodbdatabaseslistresult"></a>RestorableMongodbDatabasesListResult

リスト操作の応答。データベース イベントとそのプロパティが含まれます。

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| value |[RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult)[]| データベース イベントとそのプロパティの一覧。 |

## <a name="next-steps"></a>次のステップ

* REST API を使用した MongoDB 用 Azure Cosmos DB API での[復元可能なリソースの一覧表示](restorable-mongodb-resources-list.md)
* REST API を使用した MongoDB 用 Azure Cosmos DB API での[復元可能なコレクションの一覧表示](restorable-mongodb-collections-list.md)
* 継続的バックアップ モードの[リソース モデル](continuous-backup-restore-resource-model.md)。