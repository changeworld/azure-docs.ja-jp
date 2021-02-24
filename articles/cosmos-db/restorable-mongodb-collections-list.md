---
title: Azure Cosmos DB MongoDB API で復元可能なコレクションの一覧表示 - REST API
description: 特定のデータベースにあるすべての Azure Cosmos DB MongoDB コレクションで変更が実行されたすべてのイベント フィードを表示します。 これは、コンテナーが誤って削除された場合に役立ちます。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: c19de134f40c58a687dcf6bac6ac156e46cef7da
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537545"
---
# <a name="list-restorable-collections-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>REST API を使用した MongoDB 用 Azure Cosmos DB API での復元可能なコレクションの一覧表示

> [!IMPORTANT]
> Azure Cosmos DB の特定の時点への復元機能 (継続的バックアップ モード) は、現在、パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

特定のデータベースにあるすべての MongoDB 用 Azure Cosmos DB API コレクションで変更が実行されたすべてのイベント フィードを表示します。 これは、コンテナーが誤って削除された場合に役立ちます。 この API には `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 権限が必要です

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview
```

省略可能なパラメーターを使用します。

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid={restorableMongodbDatabaseRid}
```

## <a name="uri-parameters"></a>URI パラメーター

| 名前 | / | 必須 | Type | 説明 |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |string| 復元可能なデータベース アカウントの instanceId GUID。 |
| **location** | path | True | string| 単語間にスペースがあり、各単語が大文字になった Azure Cosmos DB 領域。 |
| **subscriptionId** | path | True | string| ターゲット サブスクリプションの ID。 |
| **api-version** | query | True | string | この操作に使用する API バージョン。 |
| **restorableMongodbDatabaseRid** | query | |string| MongoDB 用 Azure Cosmos DB API データベースのリソース ID。 |

## <a name="responses"></a>Responses

| 名前 | Type | 説明 |
| --- | --- | --- |
| 200 OK | [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult)| 操作は正常に完了しました。 |
| その他の状態コード | [DefaultErrorResponse](#defaulterrorresponse)| 操作に失敗した理由を説明するエラー応答。|

## <a name="examples"></a>例

### <a name="cosmosdbrestorablemongodbcollectionlist"></a>CosmosDBRestorableMongodbCollectionList

**サンプル要求**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid=PD5DALigDgw=
```

**応答のサンプル**

状態コード:200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbCollections",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Collection1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>定義

|定義  | 説明|
| --- | --- |
| [DefaultErrorResponse](#defaulterrorresponse) | サービスからのエラー応答。 |
| [ErrorResponse](#errorresponse) | エラー応答。 |
| [[OperationType]](#operationtype) | イベントの操作の種類を示す列挙。 |
| [リソース](#resource) | MongoDB 用 Azure Cosmos DB API コレクション イベントのリソース |
| [RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult) | MongoDB 用 Azure Cosmos DB API コレクション イベント |
| [RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties) | MongoDB 用 Azure Cosmos DB API コレクション イベントのプロパティ |
| [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult) | リスト操作の応答。コレクション イベントとそのプロパティが含まれます。 |

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
| 作成 |string|コレクション作成イベント|
| 削除 |string|コレクション削除イベント|
| Replace |string|コレクション変更イベント|

### <a name="resource"></a><a id="resource"></a>リソース

Azure Cosmos DB MongoDB コレクション イベントのリソース

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| _rid |string| システムによって生成されるプロパティ。 一意識別子。 |
| eventTimestamp |string| このコレクション イベントが発生した時刻。 |
| operationType |[[OperationType]](#operationtype)|  このコレクション イベントの操作の種類。 |
| ownerId |string| MongoDB コレクションの名前。|
| ownerResourceId |string| MongoDB コレクションのリソース ID。 |

### <a name="restorablemongodbcollectiongetresult"></a><a id="restorablemongodbcollectiongetresult"></a>RestorableMongodbCollectionGetResult

Azure Cosmos DB MongoDB コレクション イベント

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| id |string| Azure Resource Manager のリソースの一意リソース識別子。 |
| name |string| Azure Resource Manager リソースの名前。 |
| properties |[RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties)| コレクション イベントのプロパティ。 |
| type |string| Azure リソースの種類。 |

### <a name="restorablemongodbcollectionproperties"></a><a id="restorablemongodbcollectionproperties"></a>RestorableMongodbCollectionProperties

Azure Cosmos DB MongoDB コレクション イベントのプロパティ

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| resource | [リソース](#resource)| MongoDB 用 Azure Cosmos DB API コレクション イベントのリソース |

### <a name="restorablemongodbcollectionslistresult"></a><a id="restorablemongodbcollectionslistresult"></a>RestorableMongodbCollectionsListResult

リスト操作の応答。コレクション イベントとそのプロパティが含まれます。

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| value |[RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult)[]| MongoDB 用 Azure Cosmos DB API コレクション イベントとそのプロパティの一覧。 |

## <a name="next-steps"></a>次のステップ

* REST API を使用した MongoDB 用 Azure Cosmos DB API での[復元可能なデータベースの一覧表示](restorable-mongodb-databases-list.md)
* 継続的バックアップ モードの[リソース モデル](continuous-backup-restore-resource-model.md)。