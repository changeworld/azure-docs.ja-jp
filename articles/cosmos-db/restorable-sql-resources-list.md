---
title: REST API を使用した Azure Cosmos DB での復元可能 SQL API リソースの一覧表示
description: 指定されたタイムスタンプと場所にあるアカウントに存在するデータベースとコンテナーの結合の一覧を返します。 これは、指定されたタイムスタンプと場所に存在するリソースを検証するシナリオで役立ちます。
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: e31423ae5afd4955ebe7acf1d456496f15f14a6b
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537291"
---
# <a name="list-restorable-sql-api-resources-in-azure-cosmos-db-using-rest-api"></a>REST API を使用した Azure Cosmos DB での復元可能 SQL API リソースの一覧表示

> [!IMPORTANT]
> Azure Cosmos DB の特定の時点への復元機能 (継続的バックアップ モード) は、現在、パブリック プレビューの段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

指定されたタイムスタンプと場所にあるアカウントに存在するデータベースとコンテナーの結合の一覧を返します。 これは、指定されたタイムスタンプと場所に存在するリソースを検証するシナリオで役立ちます。 この API には `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` 権限が必要です。

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview
```

省略可能なパラメーターを使用します。

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
```

## <a name="uri-parameters"></a>URI パラメーター

| 名前 | / | 必須 | Type | 説明 |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True |string| 復元可能なデータベース アカウントの instanceId GUID。 |
| **location** | path | True | string| 単語間にスペースがあり、各単語が大文字になった Azure Cosmos DB 領域。 |
| **subscriptionId** | path | True | string| ターゲット サブスクリプションの ID。 |
| **api-version** | query | True | string | この操作に使用する API バージョン。 |
| **restoreLocation** | query | |string| 復元可能なリソースが配置されている場所。 |
| **restoreTimestampInUtc** | query | |string| 復元可能リソースが存在したときのタイムスタンプ。 |

## <a name="responses"></a>Responses

| 名前 | Type | 説明 |
| --- | --- | --- |
| 200 OK | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult)| 操作は正常に完了しました。 |
| その他の状態コード | [DefaultErrorResponse](#defaulterrorresponse)| 操作に失敗した理由を説明するエラー応答。 |

## <a name="examples"></a>例

### <a name="cosmosdbrestorablesqlresourcelist"></a>CosmosDBRestorableSqlResourceList

**サンプル要求**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**応答のサンプル**

状態コード:200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Container1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Container1",
        "Container2"
      ]
    },
    {
      "databaseName": "Database3",
      "collectionNames": []
    }
  ]
}
```

## <a name="definitions"></a>定義

|定義  | 説明| | --- || --- | | [DatabaseRestoreResource](#databaserestoreresource) | 復元する特定のデータベース。 | | [DefaultErrorResponse](#defaulterrorresponse) | サービスからのエラー応答。 | | [ErrorResponse](#errorresponse) | エラー応答。 | | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult) | リスト操作の応答。復元可能な SQL リソースが含まれます。 |

### <a name="databaserestoreresource"></a><a id="databaserestoreresource"></a>DatabaseRestoreResource

復元する特定のデータベース。

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| collectionNames |string[]| 復元に使用できるコレクションの名前。 |
| databaseName |string| 復元に使用できるデータベースの名前。 |

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

### <a name="restorablesqlresourceslistresult"></a><a id="restorablesqlresourceslistresult"></a>RestorableSqlResourcesListResult

リスト操作の応答。復元可能な SQL リソースが含まれます。

| **名前** | **Type** | **説明** |
| --- | --- | --- |
| value |[DatabaseRestoreResource](#databaserestoreresource)[]| データベース名とコレクション名を含む、復元可能な SQL リソースの一覧。 |

## <a name="next-steps"></a>次のステップ

* REST API を使用して Azure Cosmos SQL API の[復元可能データベースを一覧表示](restorable-sql-databases-list.md)します。
* 継続的バックアップ モードの[リソース モデル](continuous-backup-restore-resource-model.md)。