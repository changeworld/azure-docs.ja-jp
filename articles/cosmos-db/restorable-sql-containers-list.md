---
title: REST API を使用して Azure Cosmos DB で復元可能 SQL API コンテナーを一覧表示する
description: 特定のデータベースにあるすべての Azure Cosmos DB SQL コンテナーで行われたすべての変更のイベント フィードを表示します。 これは、コンテナーが誤って削除された場合に役立ちます。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: bba00b10d1a24dc29c1e986de1f0144c00350f5d
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537426"
---
# <a name="list-restorable-sql-api-containers-in-azure-cosmos-db-using-rest-api"></a>REST API を使用して Azure Cosmos DB で復元可能 SQL API コンテナーを一覧表示する

> [!IMPORTANT]
> Azure Cosmos DB のポイントインタイム リストア機能 (継続的バックアップ モード) は、現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

特定のデータベースにあるすべての Azure Cosmos DB SQL コンテナーで行われたすべての変更のイベント フィードを表示します。 これは、コンテナーが誤って削除された場合に役立ちます。 この API には `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 権限が必要です

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview
```

省略可能なパラメーターを使用します。

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid={restorableSqlDatabaseRid}
```

## <a name="uri-parameters"></a>URI パラメーター

| 名前 | / | 必須 | Type | 説明 |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |string| 復元可能なデータベース アカウントの InstanceId GUID。 |
| **location** | path | True | string| Azure Cosmos DB のリージョン。単語間にスペースがあり、各単語は大文字になっています。 |
| **subscriptionId** | path | True | string| ターゲット サブスクリプションの ID。 |
| **api-version** | query | True | string | この操作に使用する API バージョン。 |
| **restorableSqlDatabaseRid** | query | |string| SQL データベースのリソース ID。 |

## <a name="responses"></a>Responses

| 名前 | Type | 説明 |
| --- | --- | --- |
| 200 OK | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult)| 操作は正常に完了しました。 |
| その他の状態コード | [DefaultErrorResponse](#defaulterrorresponse)| 操作に失敗した理由を説明するエラー応答。 |

## <a name="examples"></a>例

### <a name="cosmosdbrestorablesqlcontainerlist"></a>CosmosDBRestorableSqlContainerList

**要求のサンプル**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid=3fu-hg==
```

**応答のサンプル**

状態コード:200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00003e00-0000-0700-0000-5f85338a0000\""
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/e85298a1-c631-4726-825e-a7ca092e9098",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "e85298a1-c631-4726-825e-a7ca092e9098",
      "properties": {
        "resource": {
          "_rid": "PrArcgAAAA==",
          "eventTimestamp": "2020-10-13T05:03:27Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Replace",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "defaultTtl": 12345,
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00004400-0000-0700-0000-5f85351f0000\""
          }
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>定義

|定義  | 説明| | --- || --- | | [Container](#container) | Azure Cosmos DB SQL コンテナー リソース オブジェクト | | [DefaultErrorResponse](#defaulterrorresponse) | サービスからのエラー応答。 | | [ErrorResponse](#errorresponse) | エラー応答。 | | [OperationType](#operationtype) | イベントの操作の種類を示す列挙。 | | [Resource](#resource) | Azure Cosmos DB SQL コンテナー イベントのリソース | | [RestorableSqlContainerGetResult](#restorablesqlcontainergetresult) | Azure Cosmos DB SQL コンテナー イベント | | [RestorableSqlContainerProperties](#restorablesqlcontainerproperties) | Azure Cosmos DB SQL コンテナー イベントのプロパティ | | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult) | リスト操作の応答。SQL コンテナー イベントとそのプロパティが含まれています。 |

### <a name="container"></a><a id="container"></a>Container

Azure Cosmos DB SQL コンテナー リソース オブジェクト

| **名前** | **Type** | **説明** | | --- || --- | ---| | _etag |string| オプティミスティック同時実行制御に必要なリソースの etag を表す、システムによって生成されるプロパティ。 | | _rid |string| システムによって生成されるプロパティ。 一意識別子。 | | _self |string| コンテナー リソースのアドレス指定可能なパスを指定する、システムによって生成されるプロパティ。 | | _ts | | リソースの最終更新タイムスタンプを示す、システムによって生成されるプロパティ。 | | ID |string| Azure Cosmos DB SQL コンテナーの名前 |

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
| 作成 |string|コンテナー作成イベント|
| 削除 |string|コンテナー削除イベント|
| Replace |string|コンテナー変更イベント|
| SystemOperation |string|システムによってトリガーされたコンテナー変更イベント。 このイベントはユーザーによって開始されません|

### <a name="resource"></a><a id="resource"></a>リソース

Azure Cosmos DB SQL コンテナー イベントのリソース

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| _rid |string| システムによって生成されるプロパティ。 一意識別子。 |
| container |[コンテナー](#container)| Azure Cosmos DB SQL コンテナー リソース オブジェクト |
| eventTimestamp |string| このコンテナー イベントが発生した時刻。 |
| operationType |[[OperationType]](#operationtype)| このコンテナー イベントの操作の種類。 |
| ownerId |string| SQL コンテナーの名前。 |
| ownerResourceId |string| SQL コンテナーのリソース ID。 |

### <a name="restorablesqlcontainergetresult"></a><a id="restorablesqlcontainergetresult"></a>RestorableSqlContainerGetResult

Azure Cosmos DB SQL コンテナー イベント

| **名前** | **Type** | **説明** |
| --- | --- | ---
| id |string| Azure Resource Manager リソースの一意のリソース識別子。 |
| name |string| Azure Resource Manager リソースの名前。 |
| properties |[RestorableSqlContainerProperties](#restorablesqlcontainerproperties)| SQL コンテナー イベントのプロパティ。 |
| type |string| Azure リソースの種類。 |

### <a name="restorablesqlcontainerproperties"></a><a id="restorablesqlcontainerproperties"></a>RestorableSqlContainerProperties

Azure Cosmos DB SQL コンテナー イベントのプロパティ

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| resource |[リソース](#resource)| Azure Cosmos DB SQL コンテナー イベントのリソース |

### <a name="restorablesqlcontainerslistresult"></a><a id="restorablesqlcontainerslistresult"></a>RestorableSqlContainersListResult

リスト操作の応答。SQL コンテナー イベントとそのプロパティが含まれています。

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| value |[RestorableSqlContainerGetResult](#restorablesqlcontainergetresult)[]| SQL コンテナー イベントとそのプロパティの一覧。 |

## <a name="next-steps"></a>次のステップ

* REST API を使用した MongoDB 用 Azure Cosmos DB API での[復元可能なデータベースの一覧表](restorable-mongodb-databases-list.md)。
* 継続的バックアップ モードの[リソース モデル](continuous-backup-restore-resource-model.md)。