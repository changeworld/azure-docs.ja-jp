---
title: MongoDB API を使用して Azure Cosmos DB アプリを構築する | Microsoft Docs
description: MongoDB 用の Azure Cosmos DB API を使用してオンライン データベースを作成するチュートリアル。
keywords: mongodb の例
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: ''
documentationcenter: ''
ms.assetid: fb38bc53-3561-487d-9e03-20f232319a87
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: anhoh
ms.openlocfilehash: 3d4b3bf36bdc93fdd1a65f5c8fdcfe2237d23aa9
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2018
---
# <a name="build-an-azure-cosmos-db-api-for-mongodb-app-using-nodejs"></a>Node.js を使用して Azure Cosmos DB: MongoDB 用 API を構築する
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [MongoDB 用 Node.js](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
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
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. お好きなターミナルを開き、**npm install mongodb --save** を実行してから、**node app.js** でアプリを実行します。

## <a name="next-steps"></a>次の手順
* Azure Cosmos DB: MongoDB 用 API アカウントで [MongoChef を使用する](mongodb-mongochef.md)方法を確認します。
