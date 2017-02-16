---
title: "DocumentDB の MongoDB 対応の例 | Microsoft Docs"
description: "DocumentDB を MongoDB のプロトコルに対応させる例を紹介します。"
keywords: "mongodb の例"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: fb38bc53-3561-487d-9e03-20f232319a87
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 8d4c3aaefee502e79dd1ea3e074937bb9a1d4593
ms.openlocfilehash: 703373c2c0090a2c6ffef3433aee3a05f0f41475


---
# <a name="documentdb-protocol-support-for-mongodb-examples"></a>DocumentDB における MongoDB のプロトコル対応の例
以降の例を使用するには、次の作業が必要となります。

* [作成](documentdb-create-mongodb-account.md) する
* MongoDB のプロトコル対応の DocumentDB アカウントに使用する [接続文字列](documentdb-connect-mongodb-account.md) 情報を取得する

## <a name="get-started-with-a-sample-nodejs-getting-started-app"></a>Node.js 使用開始サンプル アプリケーションの概要

1. *app.js* ファイルを作成し、次のコードをコピーして貼り付けます。

         var MongoClient = require('mongodb').MongoClient;
         var assert = require('assert');
         var ObjectId = require('mongodb').ObjectID;
         var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10250/?ssl=true';

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

2. アカウント設定に従って、*app.js* ファイル内の次の変数を変更します (接続文字列を確認する方法は[こちら](documentdb-connect-mongodb-account.md)を参照)。
   
         var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10250/?ssl=true';
     
3. お好きなターミナルを開き、**npm install mongodb --save** を実行してから、**node app.js** でアプリを実行します。

## <a name="get-started-with-a-sample-aspnet-mvc-task-list-application"></a>サンプル ASP.NET MVC タスク リスト アプリケーションの概要
ここでは MongoDB のプロトコル対応の DocumentDB アカウントに接続する MongoDB アプリケーションを最小限の変更で手間なくセットアップ (ローカルに作成、または Azure Web アプリとして発行) するために、「 [仮想マシンで実行される MongoDB に接続する Web アプリを Azure に作成する](../app-service-web/web-sites-dotnet-store-data-mongodb-vm.md) 」のチュートリアルに沿って説明します。  

1. チュートリアルのコードに 1 か所変更を加えます。  Dal.cs のコードを次の内容に置き換えてください。
   
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using MyTaskListApp.Models;
        using MongoDB.Driver;
        using MongoDB.Bson;
        using System.Configuration;
        using System.Security.Authentication;
   
        namespace MyTaskListApp
        {
            public class Dal : IDisposable
            {
                //private MongoServer mongoServer = null;
                private bool disposed = false;
   
                // To do: update the connection string with the DNS name
                // or IP address of your server. 
                //For example, "mongodb://testlinux.cloudapp.net
                private string connectionString = "mongodb://localhost:27017";
                private string userName = "<your user name>";
                private string host = "<your host>";
                private string password = "<your password>";
   
                // This sample uses a database named "Tasks" and a 
                //collection named "TasksList".  The database and collection 
                //will be automatically created if they don't already exist.
                private string dbName = "Tasks";
                private string collectionName = "TasksList";
   
                // Default constructor.        
                public Dal()
                {
                }
   
                // Gets all Task items from the MongoDB server.        
                public List<MyTask> GetAllTasks()
                {
                    try
                    {
                        var collection = GetTasksCollection();
                        return collection.Find(new BsonDocument()).ToList();
                    }
                    catch (MongoConnectionException)
                    {
                        return new List<MyTask>();
                    }
                }
   
                // Creates a Task and inserts it into the collection in MongoDB.
                public void CreateTask(MyTask task)
                {
                    var collection = GetTasksCollectionForEdit();
                    try
                    {
                        collection.InsertOne(task);
                    }
                    catch (MongoCommandException ex)
                    {
                        string msg = ex.Message;
                    }
                }
   
                private IMongoCollection<MyTask> GetTasksCollection()
                {
                    MongoClientSettings settings = new MongoClientSettings();
                    settings.Server = new MongoServerAddress(host, 10250);
                    settings.UseSsl = true;
                    settings.SslSettings = new SslSettings();
                    settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                    MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                    MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                    settings.Credentials = new List<MongoCredential>()
                    {
                        new MongoCredential("SCRAM-SHA-1", identity, evidence)
                    };
   
                    MongoClient client = new MongoClient(settings);
                    var database = client.GetDatabase(dbName);
                    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                    return todoTaskCollection;
                }
   
                private IMongoCollection<MyTask> GetTasksCollectionForEdit()
                {
                    MongoClientSettings settings = new MongoClientSettings();
                    settings.Server = new MongoServerAddress(host, 10250);
                    settings.UseSsl = true;
                    settings.SslSettings = new SslSettings();
                    settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;
   
                    MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
                    MongoIdentityEvidence evidence = new PasswordEvidence(password);
   
                    settings.Credentials = new List<MongoCredential>()
                    {
                        new MongoCredential("SCRAM-SHA-1", identity, evidence)
                    };
                    MongoClient client = new MongoClient(settings);
                    var database = client.GetDatabase(dbName);
                    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
                    return todoTaskCollection;
                }
   
                # region IDisposable
   
                public void Dispose()
                {
                    this.Dispose(true);
                    GC.SuppressFinalize(this);
                }
   
                protected virtual void Dispose(bool disposing)
                {
                    if (!this.disposed)
                    {
                        if (disposing)
                        {
                        }
                    }
   
                    this.disposed = true;
                }
   
                # endregion
            }
        }
2. Dal.cs ファイル内の次の変数を実際のアカウントの設定に従って変更します。
   
         private string userName = "<your user name>";
         private string host = "<your host>";
         private string password = "<your password>";
3. アプリを使用します。

## <a name="next-steps"></a>次のステップ
* MongoDB のプロトコル対応の DocumentDB アカウントで [MongoChef を使用](documentdb-mongodb-mongochef.md) する方法を確認します。



<!--HONumber=Nov16_HO4-->


