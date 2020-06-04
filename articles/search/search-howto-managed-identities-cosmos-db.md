---
title: マネージド ID を使用して、Cosmos DB アカウントへの接続を設定する (プレビュー)
titleSuffix: Azure Cognitive Search
description: マネージド ID を使用して Cosmos DB アカウントへのインデクサー接続を設定する方法を学ぶ (プレビュー)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 3524f55f70ff42bd5ff800fb2bd7ab7b0e732596
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663290"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity-preview"></a>マネージド ID を使用して Cosmos DB データベースへのインデクサー接続を設定する (プレビュー)

> [!IMPORTANT] 
> マネージド ID を使用する、データ ソースへの接続の設定のサポートは現在限定的なパブリック プレビューの段階です。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。
> プレビュー機能に対するアクセスの要求は、[こちらのフォーム](https://aka.ms/azure-cognitive-search/mi-preview-request)に必要事項を入力して行うことができます。

このページでは、データ ソースのオブジェクト接続文字列で資格情報を指定する代わりに、マネージド ID を使用して Azure Cosmos DB データベースへのインデクサー接続を設定する方法を説明します。

この機能についてさらに学ぶ前に、インデクサーとは何かについて、およびデータ ソースに対してインデクサーを設定する方法について理解しておくことをお勧めします。 以下のリンクで詳しい情報を確認できます。
* [インデクサーの概要](search-indexer-overview.md)
* [Azure Cosmos DB インデクサー](search-howto-index-cosmosdb.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>マネージド ID を使用して接続を設定する

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - システム割り当てマネージド ID をオンにする

システム割り当てマネージド ID が有効になると、お客様の検索サービスのための ID が Azure によって作成されます。これは、同じテナントとサブスクリプション内の他の Azure サービスに対する認証に使用することができます。 その後、インデックス作成中にデータへのアクセスを許可する、ロールベースのアクセス制御 (RBAC) の割り当てにおいて、この ID を使用することができます。

![システム割り当てマネージド ID をオンにする](./media/search-managed-identities/turn-on-system-assigned-identity.png "システム割り当てマネージド ID をオンにする")

**[保存]** を選択すると、検索サービスに割り当てられたオブジェクト ID が表示されるようになります。

![オブジェクト ID](./media/search-managed-identities/system-assigned-identity-object-id.png "Object ID")
 
### <a name="2---add-a-role-assignment"></a>2 - ロールの割り当てを追加する

このステップでは、お客様の Azure Cognitive Search サービスに、Cosmos DB データベースからデータを読み取るためのアクセス許可を付与します。

1. Azure portal で、インデックスを作成するデータが含まれている Cosmos DB アカウントに移動します。
2. **[アクセス制御 (IAM)]** を選択します
3. **[追加]** 、 **[ロールの割り当ての追加]** を選択します

    ![ロールの割り当てを追加する](./media/search-managed-identities/add-role-assignment-cosmos-db.png "ロールの割り当ての追加")

4. **[Cosmos DB アカウントの閲覧者ロール]** を選択します
5. **[アクセスの割り当て先]** を **[Azure AD のユーザー、グループ、サービス プリンシパル]** のままにしておきます
6. 検索サービスを検索して選択し、 **[保存]** を選択します

    ![閲覧者とデータ アクセスのロールの割り当てを追加する](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "閲覧者とデータ アクセスのロールの割り当てを追加する")

### <a name="3---create-the-data-source"></a>3 - データ ソースを作成する

**データ ソース**は、インデックスを作成するデータ、資格情報、およびデータの変更を識別するためのポリシー (コレクション内の変更または削除されたドキュメントなど) を指定します。 データ ソースは、複数のインデクサーから使用できるように、独立したリソースとして定義します。

マネージド ID を使用してデータ ソースに対して認証する場合、**credentials** にはアカウント キーは含まれません。

[REST API](https://docs.microsoft.com/rest/api/searchservice/create-data-source) を使用して Cosmos DB データ ソース オブジェクトを作成する方法の例:

```
POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/cosmos-db-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/my-cosmos-db-account/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

要求の本文には、次のフィールドを含むデータ ソースの定義が含まれている必要があります。

| フィールド   | 説明 |
|---------|-------------|
| **name** | 必須。 データ ソース オブジェクトを表す名前を選択します。 |
|**type**| 必須。 `cosmosdb`である必要があります。 |
|**credentials** | 必須。 <br/><br/>マネージド ID を使用して接続する場合、**credentials** の形式は次のようになります。*Database=[database-name];ResourceId=[resource-id-string];(ApiKind=[api-kind];)*<br/> <br/>ResourceId の形式:*ResourceId=/subscriptions/**your subscription ID**/resourceGroups/**your resource group name**/providers/Microsoft.DocumentDB/databaseAccounts/**your cosmos db account name**/;*<br/><br/>SQL コレクションの場合は、接続文字列に ApiKind は必要ありません。<br/><br/>MongoDB コレクションの場合は、**ApiKind=MongoDb** を接続文字列に追加します。 <br/><br/>Gremlin グラフと Cassandra テーブルの場合には、[インデクサーの限定プレビュー](https://aka.ms/azure-cognitive-search/indexer-preview)にサインアップして、プレビュー機能に対するアクセス権と、資格情報の形式に関する情報を入手してください。<br/>|
| **container** | 次の要素が含まれます。 <br/>**name**:必須。 インデックスを作成するデータベース コレクションの ID を指定します。<br/>**query**: 省略可能。 任意の JSON ドキュメントを、Azure Cognitive Search がインデックスを作成できるフラット スキーマにフラット化するクエリを指定できます。<br/>MongoDB API、Gremlin API、Cassandra API では現在、クエリがサポートされていません。 |
| **dataChangeDetectionPolicy** | 推奨 |
|**dataDeletionDetectionPolicy** | 省略可能 |

Azure portal と [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) も、マネージド ID 接続文字列をサポートしています。 Azure portal では、このページ上部のリンクを使用してプレビューにサインアップする際に提供される、機能フラグが必要です。 

### <a name="4---create-the-index"></a>4 - インデックスを作成する

インデックスでは、検索に使用する、ドキュメント内のフィールド、属性、およびその他の構成要素を指定します。

検索可能な `booktitle` フィールドを使用してインデックスを作成する方法を次に示します。

```
POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

インデックスの作成の詳細については、[インデックスの作成](https://docs.microsoft.com/rest/api/searchservice/create-index)に関する記事をご覧ください。

### <a name="5---create-the-indexer"></a>5 - インデクサーを作成する

インデクサーはデータ ソースをターゲットの検索インデックスに接続し、データ更新を自動化するスケジュールを提供します。

インデックスとデータ ソースを作成したら、インデクサーを作成できます。

インデクサー定義の例:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

このインデクサーは 2 時間ごとに実行されます (スケジュールの間隔が "PT2H" に設定されています)。 インデクサーを 30 分ごとに実行するには、間隔を "PT30M" に設定します。 サポートされている最短の間隔は 5 分です。 スケジュールは省略可能です。省略した場合、インデクサーは作成時に一度だけ実行されます。 ただし、いつでもオンデマンドでインデクサーを実行できます。   

インデクサー作成 API の詳細については、「 [インデクサーの作成](https://docs.microsoft.com/rest/api/searchservice/create-indexer)」をご覧ください。

インデクサーのスケジュールの定義の詳細については、[Azure Cognitive Search のインデクサーのスケジュールを設定する方法](search-howto-schedule-indexers.md)に関する記事を参照してください。

## <a name="see-also"></a>関連項目

Cosmos DB インデクサーの詳細情報:
* [Azure Cosmos DB インデクサー](search-howto-index-cosmosdb.md)
