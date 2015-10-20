<properties
	pageTitle="NoSQL データベース - DocumentDB Node.js SDK の使用 | Microsoft Azure"
	description="DocumentDB アカウントへの接続、NoSQL データベースの作成、および Windows、Linux、または OS/X での Node.js を使用した DocumentDB へのクエリを実行する方法について説明します。"
	keywords="Create a database, nosql database, linux, OS/X, node.js, documentdb, azure, Microsoft azure"
	services="documentdb"
	documentationCenter="node.js"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="node"
	ms.topic="hero-article" 
	ms.date="10/12/2015"
	ms.author="anhoh"/>

#DocumentDB Node.js SDK の使用  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

DocumentDB Node.js SDK の使用へようこそ。 このチュートリアルに従うことで、DocumentDB リソースを作成し、クエリするコンソール アプリケーションを準備することができます。

ここで説明する操作は以下のとおりです。

- DocumentDB アカウントを作成して接続する
- アプリケーションをセットアップする
- データベースを作成する
- コレクションを作成する
- JSON ドキュメントを作成する
- コレクションをクエリする
- データベースを削除する

時間がなくても 心配はありません。 [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started) で完全なソリューションを入手できます。手順については「[完全なソリューションの取得](#GetSolution)」を参照してください。

その後で、このページの上部または下部にある投票ボタンを使用して、フィードバックをお寄せください。マイクロソフトから直接ご連絡を差し上げて問題がなければ、コメント欄に電子メール アドレスをご記入ください。

それでは始めましょう。

## 前提条件

以下のものがそろっていることを確認してください。

- アクティブな Azure アカウント。これがない場合は、[Azure の無料試用版](http://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
- [Node.js](https://nodejs.org/) バージョン v0.10.29 以降

## 手順 1: DocumentDB アカウントを作成する

DocumentDB アカウントを作成しましょう。使用するアカウントが既にある場合は、「[Node.js アプリケーションをセットアップする](#SetupNode)」に進んでかまいません。

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupNode"></a> 手順 2: Node.js アプリケーションをセットアップする

1. お使いのターミナルを開きます。
2. Node.js アプリケーションを保存するフォルダーまたはディレクトリを見つけます。
3. 次のコマンドを使用して、2 つの空の JavaScript ファイルを作成します。
	- Windows:    
	    * **fsutil file createnew app.js 0**
        * **fsutil file createnew config.js 0**
	- Linux/OS X: 
	    * **touch app.js**
        * **touch config.js**
4. npm で documentdb モジュールをインストールします。次のコマンドを使用します。
    * **npm install documentdb --save**

以上です。 これでセットアップは終了です。いくつかのコードの記述を開始しましょう。

##<a id="Config"></a> 手順 3: アプリの構成を設定する

普段使用しているテキスト エディターで *config.js* を開きます。

次に、空のオブジェクトを作成して *config* という名前を付け、プロパティの *config.endpoint* と *config.authKey* に実際の DocumentDB エンドポイントと承認キーを設定します。これらの構成はどちらも、[Azure プレビュー ポータル](https://portal.azure.com)にあります。

![DocumentDB アカウントを示す、アクティブなハブ、[DocumentDB アカウント] ブレードの [キー] ボタン、[キー] ブレードの URI の値、プライマリ キーの値、およびセカンダリ キーの値が強調表示されている Azure プレビュー ポータルのスクリーン ショット][keys]

    var config = {}

    config.endpoint = "https://YOUR_ENDPOINT_URI.documents.azure.com:443/";
    config.authKey = "oqTveZeWlbtnJQ2yMj23HOAViIr0ya****YOUR_AUTHORIZATION_KEY****ysadfbUV+wUdxwDAZlCfcVzWp0PQg==";

*データベース ID*、*コレクション ID*、および *JSON ドキュメント*を *config* オブジェクトに追加しましょう。*config.endpoint* と *config.authKey* のプロパティを設定した場所の下に、次のコードを追加します。データベースに保存するデータが既にある場合は、ドキュメント定義を追加するのではなく、DocumentDB の[データ移行ツール](documentdb-import-data.md)を使用できます。

    config.dbDefinition = {"id": "FamilyRegistry"};
    config.collDefinition = {"id": "FamilyCollection"};

    var documents = {
      "Andersen": {
        "id": "AndersenFamily",
        "LastName": "Andersen",
        "Parents": [
          {
            "FirstName": "Thomas"
          },
          {
            "FirstName": "Mary Kay"
          }
        ],
        "Children": [
          {
            "FirstName": "Henriette Thaulow",
            "Gender": "female",
            "Grade": 5,
            "Pets": [
              {
                "GivenName": "Fluffy"
              }
            ]
          }
        ],
        "Address": {
          "State": "WA",
          "County": "King",
          "City": "Seattle"
        }
      },
      "Wakefield":   {
          "id": "WakefieldFamily",
          "Parents": [
            {
              "FamilyName": "Wakefield",
              "FirstName": "Robin"
            },
            {
              "FamilyName": "Miller",
              "FirstName": "Ben"
            }
          ],
          "Children": [
            {
              "FamilyName": "Merriam",
              "FirstName": "Jesse",
              "Gender": "female",
              "Grade": 8,
              "Pets": [
                {
                  "GivenName": "Goofy"
                },
                {
                  "GivenName": "Shadow"
                }
              ]
            },
            {
              "FamilyName": "Miller",
              "FirstName": "Lisa",
              "Gender": "female",
              "Grade": 1
            }
          ],
          "Address": {
            "State": "NY",
            "County": "Manhattan",
            "City": "NY"
          },
          "IsRegistered": false
        }
    };

    config.docsDefinitions = documents;

データベース、コレクション、ドキュメント定義がそれぞれ、DocumentDB の *データベース ID*、*コレクション ID*、ドキュメントのデータになります。

最後に、*app.js* ファイル内で参照できるように、*config* オブジェクトをエクスポートします。

    module.exports = config;

##<a id="Connect"></a>手順 4: DocumentDB アカウントに接続する

テキスト エディターで、空の *app.js* ファイルを開きます。*documentdb* モジュールと新しく作成した *config* モジュールをインポートします。

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");

次に、以前に保存した *config.endpoint* と *config.authKey* を使用して、新しい DocumentClient を作成します。

    var client = new documentClient(config.endpoint, {"masterKey": config.authKey});

これで、DocumentDB アカウントに接続できました。続いては、DocumentDB リソースの使用方法について説明します。

## 手順 5: データベースを作成する
[データベース](documentdb-resources.md#databases)は、**DocumentClient** クラスの [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 関数を使用して作成できます。データベースは、コレクションに分割されたドキュメント ストレージの論理上のコンテナーです。app.js ファイルに新しいデータベースを作成するための関数を追加し、*config* オブジェクトで *ID* を指定します。最初に、同じ *FamilyRegistry* ID を持つデータベースが存在しないことが確認されます。存在する場合は、新しいデータベースを作成する代わりに、そのデータベースが返されます。

    var getOrCreateDatabase = function(callback) {
        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: config.dbDefinition.id
            }]
        };

        client.queryDatabases(querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if (results.length === 0) {
                client.createDatabase(config.dbDefinition, function(err, created) {
                    if(err) return callback(err);

                    callback(null, created);
                });
            }
            else {
                callback(null, results[0]);
            }
        });
    };

##<a id="CreateColl"></a>手順 6: コレクションを作成する  

> [AZURE.WARNING]**CreateDocumentCollectionAsync** は新しい S1 コレクションを作成します。これによって価格に影響があります。詳細については、[料金のページ](https://azure.microsoft.com/pricing/details/documentdb/)を参照してください。

[コレクション](documentdb-resources.md#collections)は、**DocumentClient** クラスの [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 関数を使用して作成できます。コレクションには、JSON ドキュメントのほか、関連する JavaScript アプリケーション ロジックが格納されます。新しく作成されたコレクションは、[S1 パフォーマンス レベル](documentdb-performance-levels.md)にマップされます。app.js ファイルに新しいコレクションを作成するための関数を追加し、*config* オブジェクトで *ID* を指定します。ここでも、同じ *FamilyCollection* ID を持つコレクションが存在しないことが確認されます。存在する場合は、新しいコレクションを作成する代わりに、そのコレクションが返されます。

    var getOrCreateCollection = function(callback) {

        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: config.collDefinition.id
            }]
        };

        var dbUri = "dbs/" + config.dbDefinition.id;

        client.queryCollections(dbUri, querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if (results.length === 0) {
                client.createCollection(dbUri, config.collDefinition, function(err, created) {
                    if(err) return callback(err);
                    callback(null, created);
                });
            }
            else {
                callback(null, results[0]);
            }
        });
    };

##<a id="CreateDoc"></a>手順 7: ドキュメントを作成する
[ドキュメント](documentdb-resources.md#documents)は、**DocumentClient** クラスの [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 関数を使用して作成できます。ドキュメントは、ユーザー定義の (ユーザーが自由に定義できる) JSON コンテンツです。これで、DocumentDB にドキュメントを挿入できます。

次は、*config* オブジェクトに保存される JSON データが含まれたドキュメントを作成するための関数を app.js に追加します。ここでも、同じ ID を持つドキュメントが存在しないことが確認されます。

    var getOrCreateDocument = function(document, callback) {
        var querySpec = {
            query: 'SELECT * FROM root r WHERE r.id=@id',
            parameters: [{
                name: '@id',
                value: document.id
            }]
        };

        var collectionUri = "dbs/" + config.dbDefinition.id + "/colls/" + config.collDefinition.id;

        client.queryDocuments(collectionUri, querySpec).toArray(function(err, results) {
            if(err) return callback(err);

            if(results.length === 0) {
                client.createDocument(collectionUri, document, function(err, created) {
                    if(err) return callback(err);

                    callback(null, created);
                });
            } else {
                callback(null, results[0]);
            }
        });
    };

ご利用ありがとうございます。 DocumentDB でデータベース、コレクション、およびドキュメントを作成するための関数ができました。

![アカウント、データベース、コレクション、およびドキュメントの間の階層関係を示す図](./media/documentdb-get-started/account-database.png)

##<a id="Query"></a>手順 8: DocumentDB リソースをクエリする

DocumentDB では、各コレクションに格納された JSON ドキュメントに対する[リッチ クエリ](documentdb-sql-query.md)をサポートしています。次のサンプル コードは、コレクションでドキュメントに対して実行できるクエリを示しています。次の関数を *app.js* ファイルに追加します。以下のとおり、DocumentDB は SQL に似たクエリをサポートしています。複雑なクエリを構築する方法の詳細については、[Query Playground](https://www.documentdb.com/sql/demo) および[クエリに関するドキュメント](documentdb-sql-query.md)を参照してください。

    var queryCollection = function(documentId, callback) {
      var querySpec = {
          query: 'SELECT * FROM root r WHERE r.id=@id',
          parameters: [{
              name: '@id',
              value: documentId
          }]
      };

      var collectionUri = "dbs/" + config.dbDefinition.id + "/colls/" + config.collDefinition.id;

      client.queryDocuments(collectionUri, querySpec).toArray(function(err, results) {
          if(err) return callback(err);

          callback(null, results);
      });
    };

次の図は、作成したコレクションに対して DocumentDB SQL クエリ構文がどのように呼び出されるかを示しています。

![クエリのスコープおよび意味を示す図](./media/documentdb-get-started/collection-documents.png)

DocumentDB クエリのスコープは既に 1 つのコレクションに設定されているので、クエリでは [FROM](documentdb-sql-query.md/#from-clause) キーワードを省略できます。したがって、"FROM Families f" は "FROM root r" または他の任意の変数名に置き換えることができます。DocumentDB は、Families、root、または任意の変数名が、既定で現在のコレクションを参照しているものと推測します。

##<a id="DeleteDatabase"></a>手順 9: データベースを削除する

作成したデータベースを削除すると、データベースとすべての子リソース (コレクション、ドキュメントなど) が削除されます。データベースを削除するには、次のコード スニペットを追加します。

    // WARNING: this function will delete your database and all its children resources: collections and documents
    function cleanup(callback) {
        client.deleteDatabase("dbs/" + config.dbDefinition.id, function(err) {
            if(err) return callback(err);

            callback(null);
        });
    }

##<a id="Build"></a>手順 10: 1 つにまとめる

これで、アプリケーションに必要なすべての関数を設定できました。続いては、関数を呼び出しましょう。

関数呼び出しの順序は、* *getOrCreateDatabase* * *getOrCreateCollection* * *getOrCreateDocument* * *getOrCreateDocument* * *queryCollection* * *cleanup* です。

*app.js* 内のコードの一番下に、次のコード スニペットを追加します。

    getOrCreateDatabase(function(err, db) {
        if(err) return console.log(err);
        console.log('Read or created db:\n' + db.id + '\n');

        getOrCreateCollection(function(err, coll) {
            if(err) return console.log(err);
            console.log('Read or created collection:\n' + coll.id + '\n');

            getOrCreateDocument(config.docsDefinitions.Andersen, function(err, doc) {
                if(err) return console.log(err);
                console.log('Read or created document:\n' + doc.id + '\n');

                getOrCreateDocument(config.docsDefinitions.Wakefield, function(err, doc) {
                    if(err) return console.log(err);
                    console.log('Read or created document:\n' + doc.id + '\n');

                    queryCollection("AndersenFamily", function(err, results) {
                        if(err) return console.log(err);
                        console.log('Query results:\n' + JSON.stringify(results, null, '\t') + '\n');

                        cleanup(function(err) {
                            if(err) return console.log(err);
                            console.log('Done.');
                        });
                    });
                });
            });
        });
    });

##<a id="Run"></a>手順 11: アプリケーションを実行する

これで、アプリケーションを実行する準備が整いました。

ターミナルで、*app.js* ファイルを見つけ、コマンド **node app.js** を実行します。

開始したアプリケーションの出力が表示されます。出力は、次のテキスト例のようなものになるはずです。

    Read or created db:
    FamilyRegistry

    Read or created collection:
    FamilyCollection

    Read or created document:
    AndersenFamily

    Read or created document:
    WakefieldFamily

    Query results:
    [
            {
                    "id": "AndersenFamily",
                    "LastName": "Andersen",
                    "Parents": [
                            {
                                    "FirstName": "Thomas"
                            },
                            {
                                    "FirstName": "Mary Kay"
                            }
                    ],
                    "Children": [
                            {
                                    "FirstName": "Henriette Thaulow",
                                    "Gender": "female",
                                    "Grade": 5,
                                    "Pets": [
                                            {
                                                    "GivenName": "Fluffy"
                                            }
                                    ]
                            }
                    ],
                    "Address": {
                            "State": "WA",
                            "County": "King",
                            "City": "Seattle"
                    },
                    "_rid": "tOErANjwoQcBAAAAAAAAAA==",
                    "_ts": 1444327141,
                    "_self": "dbs/tOErAA==/colls/tOErANjwoQc=/docs/tOErANjwoQcBAAAAAAAAAA==/",
                    "_etag": ""00001200-0000-0000-0000-5616aee50000"",
                    "_attachments": "attachments/"
            }
    ]

    Done.

ご利用ありがとうございます。 最初の DocumentDB アプリの作成が終わりました。

##<a id="GetSolution"></a>完全なソリューションを取得する
この記事のすべてのサンプルを含む GetStarted ソリューションをビルドするには、以下が必要です。

-   [DocumentDB アカウント][documentdb-create-account]。
-   GitHub で入手可能な [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) ソリューション。

npm で **documentdb** モジュールをインストールします。コマンド * **npm install documentdb --save** を実行します。

次に、*config.js* ファイルで、「[手順 3: アプリの構成を設定する](#Config)」の説明に従って、config.endpoint と config.authKey の値を更新します。

## 次のステップ

-   さらに複雑な Node.js のサンプルが必要ですか。 「[DocumentDB を使用した Node.js Web アプリケーションの作成](documentdb-nodejs-application.md)」を参照してください。
-	[DocumentDB アカウントを監視する](documentdb-monitor-accounts.md)方法について学習します。
-	[クエリのプレイ グラウンド](https://www.documentdb.com/sql/demo)でサンプル データセットに対してクエリを実行します。
-	プログラミング モデルの詳細については、[DocumentDB のドキュメントに関するページ](../../services/documentdb/)の「開発」セクションを参照してください。

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-get-started/keys.png

<!---HONumber=Oct15_HO3-->