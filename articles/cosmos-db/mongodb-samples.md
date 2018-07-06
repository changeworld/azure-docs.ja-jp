---
title: MongoDB API を使用して Azure Cosmos DB アプリを構築する | Microsoft Docs
description: MongoDB 用の Azure Cosmos DB API を使用してオンライン データベースを作成するチュートリアル。
keywords: mongodb の例
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: sample
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: 188b192cf9b86a2d28a578bbcec0d6b19a8cc5d0
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084904"
---
# <a name="build-an-azure-cosmos-db-api-for-mongodb-app-using-nodejs"></a>Node.js を使用して Azure Cosmos DB: MongoDB 用 API を構築する
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [MongoDB 用 Node.js](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

次の例は、Node.js を使用して Azure Cosmos DB: MongoDB 用 API コンソール アプリを構築する方法を示しています。

この例を使用するための前提条件は、以下のとおりです。

* Azure Cosmos DB: MongoDB 用 API アカウントを[作成](create-mongodb-dotnet.md#create-account)していること
* MongoDB [接続文字列](connect-mongodb-account.md)情報を取得していること

## <a name="create-the-app"></a>アプリケーションの作成

1. *app.js* ファイルを作成し、次のコードをコピーして貼り付けます。

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **省略可能**: **MongoDB Node.js 2.2 ドライバー**を使用する場合、次のコード スニペットを置き換えてください。

    元のコード:

    ```nodejs
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    以下のコードに置き換える必要があります。

    ```nodejs
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```
    
2. アカウント設定に従って、*app.js* ファイル内の次の変数を変更します (接続文字列を確認する方法は[こちら](connect-mongodb-account.md)を参照)。

    > [!IMPORTANT]
    > **MongoDB Node.js 3.0 ドライバー**では、Cosmos DB パスワード内の特殊文字をエンコードする必要があります。 '=' 文字を必ず %3D としてエンコードします。
    >
    > 例: パスワード *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==* は、*jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D* にエンコードされます。
    >
    > **MongoDB Node.js 2.2 ドライバー**では、Cosmos DB パスワード内の特殊文字をエンコードする必要はありません。
    >
    >
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. お好きなターミナルを開き、**npm install mongodb --save** を実行してから、**node app.js** でアプリを実行します。

## <a name="next-steps"></a>次の手順
* Azure Cosmos DB: MongoDB 用 API アカウントで [MongoChef を使用する](mongodb-mongochef.md)方法を確認します。
