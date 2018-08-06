---
title: 'Azure Cosmos DB: MongoDB API を使用してクエリを実行する方法 | Microsoft Docs'
description: Azure Cosmos DB の MongoDB API を使用してクエリを実行する方法を学習します
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: e941eb27e9c55f9a4b0e7af8d4cd8efa0220616b
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "39264462"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-mongodb-api"></a>チュートリアル: MongoDB API を使用して Azure Cosmos DB に対してクエリを実行する

Azure Cosmos DB の [MongoDB API](mongodb-introduction.md) では、[MongoDB シェル クエリ](https://docs.mongodb.com/manual/tutorial/query-documents/)がサポートされます。 

この記事に含まれるタスクは次のとおりです。 

> [!div class="checklist"]
> * MongoDB を使用してデータのクエリを実行する

まずは、このドキュメントの例を使用したり、[MongoDB シェルでの Azure Cosmos DB に対するクエリの実行](https://azure.microsoft.com/resources/videos/query-azure-cosmos-db-data-by-using-the-mongodb-shell/)に関するビデオを見ることから始めます。

## <a name="sample-document"></a>サンプル ドキュメント

この記事のクエリは、次のサンプル ドキュメントを使用します。

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
## <a id="examplequery1"></a> サンプル クエリ 1 

上記の家族に関するサンプル ドキュメントに対して、次のクエリは ID フィールドが `WakefieldFamily` と一致するドキュメントを返します。

**クエリ**
    
    db.families.find({ id: “WakefieldFamily”})

**結果**

    {
    "_id": "ObjectId(\"58f65e1198f3a12c7090e68c\")",
    "id": "WakefieldFamily",
    "parents": [
      {
        "familyName": "Wakefield",
        "givenName": "Robin"
      },
      {
        "familyName": "Miller",
        "givenName": "Ben"
      }
    ],
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ],
    "address": {
      "state": "NY",
      "county": "Manhattan",
      "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
    }

## <a id="examplequery2"></a>サンプル クエリ 2 

次のクエリでは、家族のすべての子が返されます。 

**クエリ**
    
    db.families.find( { id: “WakefieldFamily” }, { children: true } )

**結果**

    {
    "_id": "ObjectId("58f65e1198f3a12c7090e68c")",
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ]
    }


## <a id="examplequery3"></a>サンプル クエリ 3 

次のクエリでは、登録されているすべての家族が返されます。 

**クエリ**
    
    db.families.find( { "isRegistered" : true })
**結果** ドキュメントは返されません。 

## <a id="examplequery4"></a>サンプル クエリ 4

次のクエリでは、登録されていないすべての家族が返されます。 

**クエリ**
    
    db.families.find( { "isRegistered" : false })
**結果**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery5"></a>サンプル クエリ 5

次のクエリでは、登録されていない、州が NY のすべての家族が返されます。 

**クエリ**
    
     db.families.find( { "isRegistered" : false, "address.state" : "NY" })

**結果**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}


## <a id="examplequery6"></a>サンプル クエリ 6

次のクエリでは、子が 8 年生のすべての家族が返されます。

**クエリ**
  
     db.families.find( { children : { $elemMatch: { grade : 8 }} } )

**結果**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery7"></a>サンプル クエリ 7

次のクエリでは、子の配列サイズが 3 のすべての家族が返されます。

**クエリ**
  
      db.Family.find( {children: { $size:3} } )

**結果**

子が 2 人より多い家族はないため、返される結果はありません。 パラメーターが 2 の場合のみ、このクエリは成功し、ドキュメント全体を返します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の手順を行いました。

> [!div class="checklist"]
> * MongoDB を使用してクエリを実行する方法を学習しました。 

次のチュートリアルに進んで、データをグローバルに分散する方法について学習できます。

> [!div class="nextstepaction"]
> [データをグローバルに分散する](tutorial-global-distribution-sql-api.md)

