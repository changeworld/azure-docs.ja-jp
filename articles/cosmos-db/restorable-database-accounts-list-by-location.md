---
title: Azure Cosmos DB REST API を使用した復元可能データベース アカウントの場所ごとの一覧表示
description: リージョン内においてサブスクリプションで使用できる、すべての復元可能な Azure Cosmos DB データベース アカウントを一覧表示します。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 26c07ddaf4db71961d4aeff50e482740d969f0ef
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537680"
---
# <a name="list-restorable-database-accounts-by-location-using-azure-cosmos-db-rest-api"></a>Azure Cosmos DB REST API を使用した復元可能データベース アカウントの場所ごとの一覧表示

> [!IMPORTANT]
> Azure Cosmos DB のポイントインタイム リストア機能 (継続的バックアップ モード) は、現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

リージョン内においてサブスクリプションで使用できる、すべての復元可能な Azure Cosmos DB データベース アカウントを一覧表示します。 この呼び出しには、`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` アクセス許可が必要です。

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts?api-version=2020-06-01-preview

```

## <a name="uri-parameters"></a>URI パラメーター

| 名前 | / | 必須 | Type | 説明 |
| --- | --- | --- | --- | --- |
| **location** | path | True | string| Azure Cosmos DB のリージョン。単語間にスペースがあり、各単語は大文字になっています。 |
| **subscriptionId** | path | True | string| ターゲット サブスクリプションの ID。 |
| **api-version** | query | True | string | この操作に使用する API バージョン。 |

## <a name="responses"></a>Responses

| 名前 | Type | 説明 |
| --- | --- | --- |
| 200 OK | [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult)| 操作は正常に完了しました。 |
| その他の状態コード | [DefaultErrorResponse](#defaulterrorresponse)| 操作に失敗した理由を説明するエラー応答。 |

## <a name="examples"></a>例

### <a name="cosmosdbdatabaseaccountlist"></a>CosmosDBDatabaseAccountList

**要求のサンプル**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

**応答のサンプル**

状態コード:200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "name": "d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "location": "West US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb1",
        "creationTime": "2020-04-11T21:56:15Z",
        "deletionTime": "2020-06-12T22:05:09Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "name": "4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "location": "West US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb2",
        "creationTime": "2020-05-01T08:05:18Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    }
  ]
}
```

## <a name="definitions"></a>定義

|定義  | 説明|
| --- | --- |
| [ApiType](#apitype) | 復元可能なデータベース アカウントの API の種類を示す列挙。 |
| [DefaultErrorResponse](#defaulterrorresponse) | サービスからのエラー応答。 |
| [ErrorResponse](#errorresponse) | エラー応答。 |
| [RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult) | Azure Cosmos DB の復元可能なデータベース アカウント。 |
| [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult) | リスト操作の応答。復元可能なデータベース アカウントとそのプロパティが含まれます。 |
| [RestorableLocationResource](#restorablelocationresource) | リージョンの復元可能なアカウントのプロパティ。 |

### <a name="apitype"></a><a id="apitype"></a>ApiType

復元可能なデータベース アカウントの API の種類を示す列挙。

| **名前** | **Type** |
| --- | --- |
| Cassandra |string|
| Gremlin |string|
| GremlinV2 |string|
| MongoDB |string|
| Sql |string|
| テーブル |string|

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

### <a name="restorabledatabaseaccountgetresult"></a><a id="restorabledatabaseaccountgetresult"></a>RestorableDatabaseAccountGetResult

Azure Cosmos DB の復元可能なデータベース アカウント。

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| id |string| Azure Resource Manager のリソースの一意識別子。 |
| location |string| リソースが属しているリソース グループの場所。 |
| name |string| Azure Resource Manager リソースの名前。 |
| properties.accountName |string| グローバル データベース アカウントの名前。 |
| properties.apiType |[ApiType](#apitype)| 復元可能なデータベース アカウントの API の種類。 |
| properties.creationTime |string| 復元可能なデータベース アカウントの作成時刻 (ISO-8601 形式)。 |
| properties.deletionTime |string| 復元可能なデータベース アカウントの削除時刻 (ISO-8601 形式)。 |
| properties.restorableLocations |[RestorableLocationResource](#restorablelocationresource)[]| データベース アカウントを復元できるリージョンの一覧。 |
| type |string| Azure リソースの種類。 |

### <a name="restorabledatabaseaccountslistresult"></a><a id="restorabledatabaseaccountslistresult"></a>RestorableDatabaseAccountsListResult

リスト操作の応答。復元可能なデータベース アカウントとそのプロパティが含まれます。

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| value |[RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult)[]| 復元可能なデータベース アカウントとそのプロパティの一覧。 |

### <a name="restorablelocationresource"></a><a id="restorablelocationresource"></a>RestorableLocationResource

リージョンの復元可能なアカウントのプロパティ。

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| creationTime |string| リージョンの復元可能なデータベース アカウントの作成時刻 (ISO-8601 形式)。 |
| deletionTime |string| リージョンの復元可能なデータベース アカウントの削除時刻 (ISO-8601 形式)。 |
| locationName |string| リージョンの復元可能なアカウントの場所。 |
| regionalDatabaseAccountInstanceId |string| リージョンの復元可能なアカウントのインスタンス ID。 |

## <a name="next-steps"></a>次のステップ

* REST API を使用した MongoDB 用 Azure Cosmos DB API での[復元可能なコレクションの一覧表示](restorable-database-accounts-list-by-location.md)
* 継続的バックアップ モードの[リソース モデル](continuous-backup-restore-resource-model.md)