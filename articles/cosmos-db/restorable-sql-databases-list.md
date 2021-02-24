---
title: REST API を使用して Azure Cosmos DB での復元可能な SQL API データベースを一覧表示する
description: 復元可能なアカウントにあるすべての Azure Cosmos DB SQL データベースに対して実行されたすべての変更のイベント フィードを表示します。 これは、データベースが誤って削除された場合に、削除時刻を取得するために役立ちます。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 218a39a2bf8e9269e43c4876c1654d94be886997
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539503"
---
# <a name="list-restorable-sql-api-databases-in-azure-cosmos-db-using-rest-api"></a>REST API を使用して Azure Cosmos DB での復元可能な SQL API データベースを一覧表示する

> [!IMPORTANT]
> Azure Cosmos DB のポイントインタイム リストア機能 (継続的バックアップ モード) は、現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

復元可能なアカウントにあるすべての Azure Cosmos DB SQL データベースに対して実行されたすべての変更のイベント フィードを表示します。 これは、データベースが誤って削除された場合に、削除時刻を取得するために役立ちます。 この API には `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 権限が必要です

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlDatabases?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>URI パラメーター

| 名前 | / | 必須 | Type | 説明 |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |string| 復元可能なデータベース アカウントの InstanceId GUID。 |
| **location** | path | True | string| Azure Cosmos DB のリージョン。単語間にスペースがあり、各単語は大文字になっています。 |
| **subscriptionId** | path | True | string| ターゲット サブスクリプションの ID。 |
| **api-version** | query | True | string | この操作に使用する API バージョン。 |

## <a name="responses"></a>Responses

| 名前 | Type | 説明 |
| --- | --- | --- |
| 200 OK | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult)| 操作は正常に完了しました。 |
| その他の状態コード | [DefaultErrorResponse](#defaulterrorresponse)| 操作に失敗した理由を説明するエラー応答。 |

## <a name="examples"></a>例

### <a name="cosmosdbrestorablesqldatabaselist"></a>CosmosDBRestorableSqlDatabaseList

**要求のサンプル**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases?api-version=2020-06-01-preview
```

**応答のサンプル**

状態コード:200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableSqlDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "operationType": "Create",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/"
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/",
            "_ts": 1599075903
          },
          "operationType": "Delete"
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "properties": {
        "resource": {
          "_rid": "aXFqUQAAAA==",
          "eventTimestamp": "2020-09-02T19:53:15Z",
          "ownerId": "Database2",
          "ownerResourceId": "0SziSg==",
          "database": {
            "id": "Database2",
            "_rid": "0SziSg==",
            "_self": "dbs/0SziSg==/",
            "_etag": "\"0000ca0a-0000-0700-0000-5f4ff82b0000\"",
            "_colls": "colls/",
            "_users": "users/"
          },
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>定義

|定義  | 説明| | --- || --- | | [Database](#database) | Azure Cosmos DB SQL データベース リソース オブジェクト | | [DefaultErrorResponse](#defaulterrorresponse) | サービスからのエラー応答。 | | [ErrorResponse](#errorresponse) | エラー応答。 | | [OperationType](#operationtype) | イベントの操作の種類を示す列挙。 | | [Resource](#resource) | Azure Cosmos DB SQL データベース イベントのリソース | | [RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult) | Azure Cosmos DB SQL データベース イベント | | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties) | Azure Cosmos DB SQL データベース イベントのプロパティ | | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult) | リスト操作の応答。SQL データベース イベントとそのプロパティが含まれています。 |

### <a name="database"></a><a id="database"></a>Database

Azure Cosmos DB SQL データベース リソース オブジェクト

| **名前** | **型** | **説明** | | --- || --- | ---| | _colls |string| コレクション リソースのアドレス指定可能なパスを指定する、システムによって生成されるプロパティ。 | | _etag |string| オプティミスティック同時実行制御に必要なリソースの etag を表す、システムによって生成されるプロパティ。 | | _rid |string| システムによって生成されるプロパティ。 一意識別子。 | | _self |string| データベース リソースのアドレス指定可能なパスを指定する、システムによって生成されるプロパティ。 | | _ts | | リソースの最終更新タイムスタンプを示す、システムによって生成されるプロパティ。 | | _users |string| ユーザーのリソースのアドレス指定可能なパスを指定する、システムによって生成されるプロパティ。 | | ID |string| Azure Cosmos DB SQL データベースの名前 |

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
| SystemOperation |string|システムによってトリガーされたデータベース変更イベント。 このイベントはユーザーによって開始されていません|

### <a name="resource"></a><a id="resource"></a>リソース

Azure Cosmos DB SQL データベース イベントのリソース

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| _rid |string| システムによって生成されるプロパティ。 一意識別子。 |
| database |[データベース](#database)| Azure Cosmos DB SQL データベース リソース オブジェクト |
| eventTimestamp |string| このデータベース イベントが発生した時刻。 |
| operationType |[[OperationType]](#operationtype)| このデータベース イベントの操作の種類。 |
| ownerId |string| SQL データベースの名前。 |
| ownerResourceId |string| SQL データベースのリソース ID。 |

### <a name="restorablesqldatabasegetresult"></a><a id="restorablesqldatabasegetresult"></a>RestorableSqlDatabaseGetResult

Azure Cosmos DB: SQL データベース イベント

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| id |string| Azure Resource Manager リソースの一意のリソース識別子。 |
| name |string| Azure Resource Manager リソースの名前。 |
| properties | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties)| SQL データベース イベントのプロパティ。 |
| type |string| Azure リソースの種類。 |

### <a name="restorablesqldatabaseproperties"></a><a id="restorablesqldatabaseproperties"></a>RestorableSqlDatabaseProperties

Azure Cosmos DB SQL データベース イベントのプロパティ

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| resource |[リソース](#resource)| Azure Cosmos DB SQL データベース イベントのリソース |

### <a name="restorablesqldatabaseslistresult"></a><a id="restorablesqldatabaseslistresult"></a>RestorableSqlDatabasesListResult

リスト操作の応答。SQL データベース イベントとそのプロパティが含まれています。

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| value |[RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult)[]| SQL データベース イベントとそのプロパティの一覧。 |

## <a name="next-steps"></a>次のステップ

* REST API を使用して Azure Cosmos DB SQL API で[復元可能コンテナーを一覧表示](restorable-sql-containers-list.md)します。
* 継続的バックアップ モードの[リソース モデル](continuous-backup-restore-resource-model.md)。