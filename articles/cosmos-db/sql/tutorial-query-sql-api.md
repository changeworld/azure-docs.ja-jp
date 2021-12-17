---
title: チュートリアル:Azure Cosmos DB で SQL を使用してクエリを実行する方法
description: チュートリアル:Azure Cosmos DB に対する SQL クエリをクエリ プレイグラウンドを使用して実行する方法について説明します
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.custom: tutorial-develop, mvc
ms.topic: tutorial
ms.date: 08/26/2021
ms.reviewer: sngun
ms.openlocfilehash: 29edc7115e4f6d809401d165888c563c60b98b36
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117830"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>チュートリアル:SQL API を使って Azure Cosmos DB に対するクエリを実行する
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB [SQL API](../introduction.md) では SQL を使用したドキュメントのクエリがサポートされます。 この記事には、1 つのサンプル ドキュメントと 2 つのサンプル SQL クエリおよび結果が含まれます。

この記事に含まれるタスクは次のとおりです。 

> [!div class="checklist"]
> * SQL を使用してデータのクエリを実行する

## <a name="sample-document"></a>サンプル ドキュメント

この記事の SQL クエリは、次のサンプル ドキュメントを使用します。

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

## <a name="where-can-i-run-sql-queries"></a>SQL クエリを実行できるところ

Azure portal のデータ エクスプローラーと、[REST API および SDK](sql-api-sdk-dotnet.md) を使用してクエリを実行できます。

SQL クエリについて詳しくは、次を参照してください。
* [SQL クエリと SQL 構文](sql-query-getting-started.md)

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、Azure Cosmos DB アカウントとコレクションがあると仮定しています。 これらのリソースがない場合は、 [5 分でできるクイックスタート](create-cosmosdb-resources-portal.md)を完了してください。

## <a name="example-query-1"></a>サンプル クエリ 1

上の家族に関するサンプル ドキュメントに対して、次の SQL クエリは ID フィールドが `WakefieldFamily` と一致するドキュメントを返します。 `SELECT *` ステートメントであるため、クエリの出力は完全な JSON ドキュメントになります。

**クエリ**

```sql
    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"
```

**結果**

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

## <a name="example-query-2"></a>サンプル クエリ 2

次のクエリでは、ID が `WakefieldFamily` と一致する家族の子供の名前がすべて返されます。

**クエリ**

```sql
    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
```

**結果**

```
[
    {
        "givenName": "Jesse"
    },
    {
        "givenName": "Lisa"
    }
]
```


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の作業を行いました。

> [!div class="checklist"]
> * SQL を使用してクエリを実行する方法を学習しました。  

次のチュートリアルに進んで、データをグローバルに分散する方法について学習できます。

> [!div class="nextstepaction"]
> [データをグローバルに分散する](tutorial-global-distribution-sql-api.md)

Azure Cosmos DB への移行のための容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
* 既存のデータベース クラスター内の仮想コアとサーバーの数のみがわかっている場合は、[仮想コア数または仮想 CPU 数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md)に関するページを参照してください 
* 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](estimate-ru-with-capacity-planner.md)に関するページを参照してください