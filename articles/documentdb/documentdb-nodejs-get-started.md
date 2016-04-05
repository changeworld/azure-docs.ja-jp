<properties
	pageTitle="DocumentDB の NoSQL Node.js チュートリアル | Microsoft Azure"
	description="DocumentDB Node.js SDK を使用してノード データベースとコンソール アプリケーションを作成する NoSQL Node.js チュートリアル。DocumentDB は、JSON 用の NoSQL データベースです。"
    keywords="Node.js チュートリアル, ノード データベース"
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
	ms.date="03/30/2016"
	ms.author="anhoh"/>

# NoSQL Node.js チュートリアル: DocumentDB Node.js コンソール アプリケーション  

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.JS](documentdb-nodejs-get-started.md)

DocumentDB Node.js SDK の Node.js チュートリアルへようこそ。 このチュートリアルでは、Node データベースなどの DocumentDB リソースを作成してクエリするコンソール アプリケーションを作成します。

ここで説明する操作は以下のとおりです。

- DocumentDB アカウントを作成して接続する
- アプリケーションをセットアップする
- ノード データベースの作成
- コレクションを作成する
- JSON ドキュメントを作成する
- コレクションをクエリする
- ノード データベースの削除

時間がなくても 心配はありません。 [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started) で完全なソリューションを入手できます。手順については「[完全なソリューションの取得](#GetSolution)」を参照してください。

Node.js チュートリアルを完了した後で、このページの上部または下部にある投票ボタンを使用して、フィードバックをお寄せください。マイクロソフトから直接ご連絡を差し上げて問題がなければ、コメント欄に電子メール アドレスをご記入ください。

それでは始めましょう。

## Node.js チュートリアルの前提条件

以下のものがそろっていることを確認してください。

- アクティブな Azure アカウント。これがない場合は、[Azure の無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
- [Node.js](https://nodejs.org/) バージョン v0.10.29 以降

## 手順 1: DocumentDB アカウントを作成する

DocumentDB アカウントを作成しましょう。使用するアカウントが既にある場合は、「[Node.js アプリケーションをセットアップする](#SetupNode)」に進んでかまいません。

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupNode"></a> 手順 2: Node.js アプリケーションをセットアップする

1. お使いのターミナルを開きます。
2. Node.js アプリケーションを保存するフォルダーまたはディレクトリを見つけます。
3. 次のコマンドを使用して、2 つの空の JavaScript ファイルを作成します。
	- Windows:    
	    * ```fsutil file createnew app.js 0```
        * ```fsutil file createnew config.js 0```
	- Linux/OS X: 
	    * ```touch app.js```
        * ```touch config.js```
4. npm で documentdb モジュールをインストールします。次のコマンドを使用します。
    * ```npm install documentdb --save```

以上です。 これでセットアップは終了です。いくつかのコードの記述を開始しましょう。

##<a id="Config"></a> 手順 3: アプリの構成を設定する

普段使用しているテキスト エディターで ```config.js``` を開きます。

次に、空のオブジェクトを作成して ```config``` という名前を付け、プロパティの ```config.endpoint``` と ```config.authKey``` に実際の DocumentDB エンドポイントと承認キーを設定します。これらの構成はどちらも [Azure ポータル](https://portal.azure.com)にあります。

![Node.js チュートリアル - DocumentDB アカウントを示す Azure ポータルのスクリーン ショット。アクティブなハブが強調表示され、[DocumentDB アカウント] ブレードで [キー] ボタンが強調表示され、[キー] ブレードで URI 値、プライマリ キー値、およびセカンダリ キーの値が強調表示されている - ノード データベース][keys]

    var config = {}

    config.endpoint = "https://YOUR_ENDPOINT_URI.documents.azure.com:443/";
    config.authKey = "oqTveZeWlbtnJQ2yMj23HOAViIr0ya****YOUR_AUTHORIZATION_KEY****ysadfbUV+wUdxwDAZlCfcVzWp0PQg==";

```database id```、```collection id```、```JSON documents``` を ```config``` オブジェクトに追加してみましょう。```config.endpoint``` と ```config.authKey``` を設定した場所で、次のコードを追加します。データベースに保存するデータが既にある場合は、ドキュメント定義を追加するのではなく、DocumentDB の[データ移行ツール](documentdb-import-data.md)を使用できます。

    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl",
        "partitionKey": { "paths": ["/district"], "kind": "Hash" }
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "district": "WA5",
            "parents": [{
                "firstName": "Thomas"
            }, {
                    "firstName": "Mary Kay"
                }],
            "children": [{
                "firstName": "Henriette Thaulow",
                "gender": "female",
                "grade": 5,
                "pets": [{
                    "givenName": "Fluffy"
                }]
            }],
            "address": {
                "state": "WA",
                "county": "King",
                "city": "Seattle"
            }
        },
        "Wakefield": {
            "id": "Wakefield.7",
            "district": "NY23",
            "parents": [{
                "familyName": "Wakefield",
                "firstName": "Robin"
            }, {
                    "familyName": "Miller",
                    "firstName": "Ben"
                }],
            "children": [{
                "familyName": "Merriam",
                "firstName": "Jesse",
                "gender": "female",
                "grade": 8,
                "pets": [{
                    "givenName": "Goofy"
                }, {
                        "givenName": "Shadow"
                    }]
            }, {
                    "familyName": "Miller",
                    "firstName": "Lisa",
                    "gender": "female",
                    "grade": 1
                }],
            "address": {
                "state": "NY",
                "county": "Manhattan",
                "city": "NY"
            },
            "isRegistered": false
        }
    };


データベース、コレクション、ドキュメント定義がそれぞれ、DocumentDB の ```database id```、```collection id```、ドキュメントのデータになります。

最後に、```app.js``` ファイル内で参照できるように、```config``` オブジェクトをエクスポートします。

    module.exports = config;

##<a id="Connect"></a>手順 4: DocumentDB アカウントに接続する

テキスト エディターで、空の ```app.js``` ファイルを開きます。```documentdb``` モジュールと新しく作成した ```config``` モジュールをインポートします。

    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

次に、以前に保存した ```config.endpoint``` と ```config.authKey``` を使用して、新しい DocumentClient を作成します。

    var client = new documentClient(config.endpoint, { "masterKey": config.authKey });

これで、DocumentDB アカウントに接続できました。続いては、DocumentDB リソースの使用方法について説明します。

## 手順 5: ノード データベースを作成する
[データベース](documentdb-resources.md#databases)は、**DocumentClient** クラスの [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 関数を使用して作成できます。データベースは、コレクションに分割されたドキュメント ストレージの論理上のコンテナーです。app.js ファイルに新しいデータベースを作成するための関数を追加し、```config``` オブジェクトで ```id``` を指定します。最初に、同じ ```FamilyRegistry``` ID を持つデータベースが存在しないことが確認されます。存在する場合は、新しいデータベースを作成する代わりに、そのデータベースが返されます。

    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    /**
     * Get the database by ID, or create if it doesn't exist.
     * @param {string} database - The database to get or create
     */
    function getDatabase() {
        console.log(`Getting database:\n${config.database.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase(config.database, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }
##<a id="CreateColl"></a>手順 6: コレクションを作成する  

> [AZURE.WARNING] **CreateDocumentCollectionAsync** は新しいコレクションを作成します。これによって価格に影響があります。詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/documentdb/)を参照してください。

[コレクション](documentdb-resources.md#collections)は、**DocumentClient** クラスの [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 関数を使用して作成できます。コレクションは、JSON ドキュメントおよび関連する JavaScript アプリケーション ロジックを格納したコンテナーです。app.js ファイルに新しいコレクションを作成するための関数を追加し、```config``` オブジェクトで ```id``` を指定します。ここでも、同じ ID を持つ ```FamilyCollection``` が存在しないことが確認されます。存在する場合は、新しいコレクションを作成する代わりに、そのコレクションが返されます。

    /**
     * Get the collection by ID, or create if it doesn't exist.
     */
    function getCollection() {
        console.log(`Getting collection:\n${collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createCollection(databaseUrl, config.collection, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

##<a id="CreateDoc"></a>手順 7: ドキュメントを作成する
[ドキュメント](documentdb-resources.md#documents)は、**DocumentClient** クラスの [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) 関数を使用して作成できます。ドキュメントは、ユーザー定義の (ユーザーが自由に定義できる) JSON コンテンツです。これで、DocumentDB にドキュメントを挿入できます。

次は、```config``` オブジェクトに保存される JSON データが含まれたドキュメントを作成するための関数を app.js に追加します。ここでも、同じ ID を持つドキュメントが存在しないことが確認されます。

    /**
     * Get the document by ID, or create if it doesn't exist.
     * @param {function} callback - The callback function on completion
     */
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, { partitionKey: document.district }, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

ご利用ありがとうございます。 DocumentDB でデータベース、コレクション、およびドキュメントを作成するための関数ができました。

![Node.js チュートリアル - アカウント、データベース、コレクション、およびドキュメントの間の階層関係を示す図 - ノード データベース](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

##<a id="Query"></a>手順 8: DocumentDB リソースをクエリする

DocumentDB では、各コレクションに格納された JSON ドキュメントに対する[リッチ クエリ](documentdb-sql-query.md)をサポートしています。次のサンプル コードは、コレクションでドキュメントに対して実行できるクエリを示しています。以下の関数をこの ```app.js``` ファイルに追加します。以下のとおり、DocumentDB は SQL に似たクエリをサポートしています。複雑なクエリを構築する方法の詳細については、[Query Playground](https://www.documentdb.com/sql/demo) および[クエリに関するドキュメント](documentdb-sql-query.md)を参照してください。

    /**
     * Query the collection using SQL
     */
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.address.city FROM root r WHERE r.lastName = "Andersen"',
                { enableCrossPartitionQuery: true }
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        console.log(`Query returned ${queryResult}`);
                    }
                    resolve(results);
                }
            });
        });
    };


次の図は、作成したコレクションに対して DocumentDB SQL クエリ構文がどのように呼び出されるかを示しています。

![Node.js チュートリアル - クエリのスコープおよび意味を示す図 - ノード データベース](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

DocumentDB クエリのスコープは既に 1 つのコレクションに設定されているので、クエリでは [FROM](documentdb-sql-query.md/#from-clause) キーワードを省略できます。したがって、"FROM Families f" は "FROM root r" または他の任意の変数名に置き換えることができます。DocumentDB は、Families、root、または任意の変数名が、既定で現在のコレクションを参照しているものと推測します。

##<a id="DeleteDatabase"></a>手順 9: ノード データベースを削除する

作成したデータベースを削除すると、データベースとすべての子リソース (コレクション、ドキュメントなど) が削除されます。データベースを削除するには、次のコード スニペットを追加します。

    /**
     * Cleanup the database and collection on completion
     */
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

##<a id="Build"></a>手順 10: 1 つにまとめる

これで、アプリケーションに必要なすべての関数を設定できました。続いては、関数を呼び出しましょう。

```app.js``` 内のコードの一番下に、次のコード スニペットを追加します。

    /**
     * Exit the app with a prompt
     * @param {message} message - The message to display
     */
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
        .then(() => getCollection())
        .then(() => getFamilyDocument(config.documents.Andersen))
        .then(() => getFamilyDocument(config.documents.Wakefield))
        .then(() => queryCollection())
        .then(() => cleanup())
        .then(() => { exit(`Completed successfully`); })
        .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

##<a id="Run"></a>手順 11: Node.js アプリケーションを実行する

これで、Node.js アプリケーションを実行する準備が整いました。

ターミナルで、```app.js``` ファイルを見つけ、コマンド ```node app.js``` を実行します。

開始したアプリケーションの出力が表示されます。出力は、次のテキスト例のようなものになるはずです。

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
      Query returned Seattle

    Cleaning up by deleting database FamilyDB

    Completed successfully
    Press any key to exit

ご利用ありがとうございます。 以上で Node.js チュートリアルが完了し、初めての DocumentDB コンソール アプリケーションが完成しました。

##<a id="GetSolution"></a> 完全な Node.js チュートリアル ソリューションを入手する
この記事のすべてのサンプルを含む GetStarted ソリューションをビルドするには、以下が必要です。

-   [DocumentDB アカウント][documentdb-create-account]。
-   GitHub で入手可能な [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started) ソリューション。

npm で **documentdb** モジュールをインストールします。次のコマンドを使用します。
* ```npm install documentdb --save```

次に、```config.js``` ファイルで、「[手順 3: アプリの構成を設定する](#Config)」の説明に従って、config.endpoint と config.authKey の値を更新します。

## 次のステップ

-   さらに複雑な Node.js のサンプルが必要ですか。 「[DocumentDB を使用した Node.js Web アプリケーションの作成](documentdb-nodejs-application.md)」を参照してください。
-	[DocumentDB アカウントを監視する](documentdb-monitor-accounts.md)方法について学習します。
-	[クエリのプレイ グラウンド](https://www.documentdb.com/sql/demo)でサンプル データセットに対してクエリを実行します。
-	プログラミング モデルの詳細については、[DocumentDB のドキュメントに関するページ](../../services/documentdb/)の「開発」セクションを参照してください。

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

<!---HONumber=AcomDC_0330_2016-->