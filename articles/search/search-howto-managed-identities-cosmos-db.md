---
title: マネージド ID を使用して、Cosmos DB アカウントへの接続を設定する
titleSuffix: Azure Cognitive Search
description: マネージド ID を使用して Cosmos DB アカウントへのインデクサー接続を設定する方法を学ぶ
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/02/2021
ms.openlocfilehash: 6fbd0e851055271d8c5fcf42a970f085ede015c2
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894338"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity"></a>マネージド ID を使用して Cosmos DB データベースへのインデクサー接続を設定する

このページでは、データ ソースのオブジェクト接続文字列で資格情報を指定する代わりに、マネージド ID を使用して Azure Cosmos DB データベースへのインデクサー接続を設定する方法を説明します。

システム割り当てマネージド ID またはユーザー割り当てマネージド ID (プレビュー) を使用できます。

この機能についてさらに学ぶ前に、インデクサーとは何かについて、およびデータ ソースに対してインデクサーを設定する方法について理解しておくことをお勧めします。 以下のリンクで詳しい情報を確認できます。

* [インデクサーの概要](search-indexer-overview.md)
* [Azure Cosmos DB インデクサー](search-howto-index-cosmosdb.md)

## <a name="1---set-up-a-managed-identity"></a>1 - マネージド ID を設定する

次のいずれかのオプションを使用して[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) を設定します。

### <a name="option-1---turn-on-system-assigned-managed-identity"></a>オプション 1 - システム割り当てマネージド ID を有効にする

システム割り当てマネージド ID が有効になると、Azure で検索サービスのための ID が作成されます。これは、同じテナントとサブスクリプション内の他の Azure サービスに対する認証に使用することができます。 この ID はその後、インデックス作成中にデータへのアクセスを許可する Azure ロールベースのアクセス制御 (Azure RBAC) の割り当てで、使用することができます。

![システム割り当てマネージド ID をオンにする](./media/search-managed-identities/turn-on-system-assigned-identity.png "システム割り当てマネージド ID をオンにする")

**[保存]** を選択した後に、検索サービスに割り当てられたオブジェクト ID が表示されます。

![オブジェクト ID](./media/search-managed-identities/system-assigned-identity-object-id.png "Object ID")
 
### <a name="option-2---assign-a-user-assigned-managed-identity-to-the-search-service-preview"></a>オプション 2 - ユーザー割り当てのマネージド ID を検索サービスに割り当てる (プレビュー)

ユーザー割り当てマネージド ID をまだ作成していない場合は、作成する必要があります。 ユーザー割り当てマネージド ID は Azure のリソースです。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. **[+ リソースの作成]** を選択します。
1. [サービスとマーケットプレース] の検索バーで、[ユーザー割り当てマネージド ID] を検索し、 **[作成]** を選択します。
1. ID にわかりやすい名前を付けます。

次に、ユーザー割り当てマネージド ID を検索サービスに割り当てます。 これは、[2021-04-01-preview 管理 API](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) を使用して行うことができます。

ID プロパティは、型と 1 つ以上の完全修飾ユーザー割り当て ID を受け取ります。

* **type** は ID の型です。 有効な値は "SystemAssigned"、"UserAssigned"、または "SystemAssigned, UserAssigned" (両方を使用する場合) です。 値 "None" を指定すると、検索サービスから以前に割り当てられた ID がクリアされます。
* **userAssignedIdentities** には、ユーザー割り当てマネージド ID の詳細が含まれます。
    * ユーザー割り当てマネージド ID の形式: 
        * /subscriptions/**サブスクリプション ID**/resourcegroups/**リソース グループ名**/providers/Microsoft.ManagedIdentity/userAssignedIdentities/**マネージド ID の名前**

ユーザー割り当てマネージド ID を検索サービスに割り当てる方法の例:

```http
PUT https://management.azure.com/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Search/searchServices/[search service name]?api-version=2021-04-01-preview
Content-Type: application/json

{
  "location": "[region]",
  "sku": {
    "name": "[sku]"
  },
  "properties": {
    "replicaCount": [replica count],
    "partitionCount": [partition count],
    "hostingMode": "default"
  },
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[name of managed identity]": {}
    }
  }
} 
```
 
## <a name="2---add-a-role-assignment"></a>2 - ロールの割り当てを追加する

このステップでは、Azure Cognitive Search サービスまたはユーザー割り当てマネージド ID に、Cosmos DB データベースからデータを読み取るためのアクセス許可を付与します。

1. Azure portal で、インデックスを作成するデータが含まれている Cosmos DB アカウントに移動します。
2. **[アクセス制御 (IAM)]** を選択します
3. **[追加]** 、 **[ロールの割り当ての追加]** の順に選択します

    ![ロールの割り当てを追加する](./media/search-managed-identities/add-role-assignment-cosmos-db.png "ロールの割り当ての追加")

4. **[Cosmos DB アカウントの閲覧者ロール]** を選択します
5. **[アクセスの割り当て先]** を **[Azure AD のユーザー、グループ、サービス プリンシパル]** のままにしておきます
6. システム割り当てマネージド ID を使用している場合、検索サービスを検索して選択します。 ユーザー割り当てマネージド ID を使用している場合、ユーザー割り当てマネージド ID の名前を検索して選択します。 **[保存]** を選択します。

    システム割り当てマネージド ID を使用した Cosmos DB の例:

    ![閲覧者とデータ アクセスのロールの割り当てを追加する](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "閲覧者とデータ アクセスのロールの割り当てを追加する")

## <a name="3---create-the-data-source"></a>3 - データ ソースを作成する

データ ソースを作成し、システム割り当てマネージド ID またはユーザー割り当てマネージド ID (プレビュー) のいずれかを指定します。 以下の手順では管理 REST API を使用しなくなったことに注意してください。

### <a name="option-1---create-the-data-source-with-a-system-assigned-managed-identity"></a>オプション 1 - システム割り当てマネージド ID を使用してデータ ソースを作成する

[REST API](/rest/api/searchservice/create-data-source)、Azure portal、および [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) では、システム割り当てマネージド ID の使用がサポートされています。 次に、[REST API](/rest/api/searchservice/create-data-source) とマネージド ID 接続文字列を使用して Cosmos DB のデータにインデックスを付けるためのデータ ソースを作成する方法例を示します。 マネージド ID 接続文字列の形式は、REST API、.NET SDK、および Azure portal において同じです。

マネージド ID を使用して認証する場合、**資格情報** にはアカウント キーは含まれません。

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.DocumentDB/databaseAccounts/[Cosmos DB account name]/;"
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
|**credentials** | 必須。 <br/><br/>マネージド ID を使用して接続する場合、**credentials** の形式は次のようになります。*Database=[database-name];ResourceId=[resource-id-string];(ApiKind=[api-kind];)*<br/> <br/>ResourceId の形式:*ResourceId=/subscriptions/**your subscription ID**/resourceGroups/**your resource group name**/providers/Microsoft.DocumentDB/databaseAccounts/**your cosmos db account name**/;*<br/><br/>SQL コレクションの場合は、接続文字列に ApiKind は必要ありません。<br/><br/>MongoDB コレクションの場合は、**ApiKind=MongoDb** を接続文字列に追加します。 <br/><br/>Gremlin グラフの場合には、[インデクサーの限定プレビュー](https://aka.ms/azure-cognitive-search/indexer-preview)にサインアップして、プレビュー機能に対するアクセス権と、資格情報の形式に関する情報を入手してください。<br/>|
| **container** | 次の要素が含まれます。 <br/>**name**:必須。 インデックスを作成するデータベース コレクションの ID を指定します。<br/>**query**: 省略可能。 任意の JSON ドキュメントを、Azure Cognitive Search がインデックスを作成できるフラット スキーマにフラット化するクエリを指定できます。<br/>MongoDB API および Gremlin API では、クエリがサポートされていません。 |
| **dataChangeDetectionPolicy** | 推奨 |
|**dataDeletionDetectionPolicy** | 省略可能 |

### <a name="option-2---create-the-data-source-with-a-user-assigned-managed-identity"></a>オプション 2 - ユーザー割り当てマネージド ID を使用してデータ ソースを作成する

2021-04-30-preview REST API では、ユーザー割り当てマネージド ID がサポートされています。 以下の例は、[REST API](/rest/api/searchservice/create-data-source)、マネージド ID 接続文字列、ユーザー割り当てマネージド ID を使用し、ストレージ アカウントのデータにインデックスを付けてデータ ソースを作成する方法を示しています。

```http
POST https://[service name].search.windows.net/datasources?api-version=2021-04-30-preview
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.DocumentDB/databaseAccounts/[Cosmos DB account name]/;"
    },
    "container": { 
        "name": "myCollection", "query": null 
    },
    "identity" : { 
        "@odata.type": "#Microsoft.Azure.Search.DataUserAssignedIdentity",
        "userAssignedIdentity" : "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]" 
    },
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
|**credentials** | 必須。 <br/><br/>マネージド ID を使用して接続する場合、**credentials** の形式は次のようになります。*Database=[database-name];ResourceId=[resource-id-string];(ApiKind=[api-kind];)*<br/> <br/>ResourceId の形式:*ResourceId=/subscriptions/**your subscription ID**/resourceGroups/**your resource group name**/providers/Microsoft.DocumentDB/databaseAccounts/**your cosmos db account name**/;*<br/><br/>SQL コレクションの場合は、接続文字列に ApiKind は必要ありません。<br/><br/>MongoDB コレクションの場合は、**ApiKind=MongoDb** を接続文字列に追加します。 <br/><br/>Gremlin グラフの場合には、[インデクサーの限定プレビュー](https://aka.ms/azure-cognitive-search/indexer-preview)にサインアップして、プレビュー機能に対するアクセス権と、資格情報の形式に関する情報を入手してください。<br/>|
| **container** | 次の要素が含まれます。 <br/>**name**:必須。 インデックスを作成するデータベース コレクションの ID を指定します。<br/>**query**: 省略可能。 任意の JSON ドキュメントを、Azure Cognitive Search がインデックスを作成できるフラット スキーマにフラット化するクエリを指定できます。<br/>MongoDB API および Gremlin API では、クエリがサポートされていません。 |
| **identity** | ユーザー割り当てマネージド ID のコレクションが含まれます。 データ ソースを作成するとき、ユーザー割り当てマネージド ID を 1 つだけ指定する必要があります。 次の要素が含まれます。 <br/>**userAssignedIdentities** には、ユーザー割り当てマネージド ID の詳細が含まれます。<br/><br/>ユーザー割り当てマネージド ID の形式: /subscriptions/**サブスクリプション ID**/resourcegroups/**リソース グループ名**/providers/Microsoft.ManagedIdentity/userAssignedIdentities/**マネージド ID の名前**|
| **dataChangeDetectionPolicy** | 推奨 |
|**dataDeletionDetectionPolicy** | 省略可能 |

## <a name="4---create-the-index"></a>4 - インデックスを作成する

インデックスでは、検索に使用する、ドキュメント内のフィールド、属性、およびその他の構成要素を指定します。

検索可能な `booktitle` フィールドを使用してインデックスを作成する方法を次に示します。

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
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

インデックスの作成の詳細については、[インデックスの作成](/rest/api/searchservice/create-index)に関する記事をご覧ください。

## <a name="5---create-the-indexer"></a>5 - インデクサーを作成する

インデクサーはデータ ソースをターゲットの検索インデックスに接続し、データ更新を自動化するスケジュールを提供します。

インデックスとデータ ソースを作成したら、インデクサーを作成できます。

インデクサー定義の例:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

このインデクサーは 2 時間ごとに実行されます (スケジュールの間隔が "PT2H" に設定されています)。 インデクサーを 30 分ごとに実行するには、間隔を "PT30M" に設定します。 サポートされている最短の間隔は 5 分です。 スケジュールは省略可能です。省略した場合、インデクサーは作成時に一度だけ実行されます。 ただし、いつでもオンデマンドでインデクサーを実行できます。   

インデクサー作成 API の詳細については、「 [インデクサーの作成](/rest/api/searchservice/create-indexer)」をご覧ください。

インデクサーのスケジュールの定義の詳細については、[Azure Cognitive Search のインデクサーのスケジュールを設定する方法](search-howto-schedule-indexers.md)に関する記事を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

Cosmos DB からのデータにインデックスを付けることができない場合は、以下の点について検討してください。

1. Cosmos DB のアカウント キーを最近ローテーションした場合は、マネージド ID の接続文字列が機能するまでに最大で 15 分間待つ必要があります。

1. Cosmos DB アカウントで、特定のネットワークへのアクセスが制限されているかどうかを確認します。 そうされている場合は、[Azure ネットワーク セキュリティ機能を使用したデータ ソースへのインデクサーのアクセス](search-indexer-securing-resources.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

* [Azure Cosmos DB インデクサー](search-howto-index-cosmosdb.md)