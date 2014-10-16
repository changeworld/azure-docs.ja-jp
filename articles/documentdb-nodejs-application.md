<properties title="Build a Node.js web application using DocumentDB" pageTitle="Build a Node.js web application using DocumentDB | Azure" description="Learn how to use Azure DocumentDB to store and access data from a Node.js application hosted on Azure." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, getting started"   services="documentdb" solutions="data-management" documentationCenter=""  authors="ryancraw" manager="jhubbard" editor="cgronlun" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="ryancraw"></tags>

# <a name="_Toc395783175">DocumentDB を使用した Node.js Web アプリケーションの作成</a>

<a name="_Toc395783175">

このチュートリアルでは、Azure DocumentDB サービスを使用して、Azure Websites で
ホストされる Node.js Express アプリケーションからデータを格納する方法やデータにアクセスする方法について説明します。

このチュートリアルは、Node.js を使用した経験がある読者を対象としています。

学習内容:

· Node.js Tools for Visual Studio の使い方

· documentdb npm モジュールを使って Azure DocumentDB サービスを操作する方法

· Web アプリケーションを Azure Websites にデプロイする方法

このチュートリアルでは、タスクを作成、取得、
完了する機能を備えた、単純な Web ベースのタスク管理アプリケーションを
作成します。対象となるタスクは、JSON ドキュメントとして Azure
DocumentDB に保存するものとします。

![Alt text][]

## <a name="_Toc395783176">前提条件</a>

この記事の手順を実行する前に、次のソフトウェアがインストール
されていることを確認してください。

[Node.js][] バージョン v0.10.29 以降

[Git][]

[Visual Studio 2013][] Update 3

[Node.js Tools for Visual Studio][]

**注:** このチュートリアルでは Visual Studio を使って Node.js プロジェクトを
ビルド、デバッグ、デプロイしていますが、
任意のエディターを使い、Node.js プロジェクトを普段実行するのと同じように、
任意のプラットフォームで Node.js を直接実行できます。その後、[Azure CLI][] ツールを使って
アプリケーションを Azure Websites にデプロイできます。

# <a name="_Toc395637761">DocumentDB データベース アカウントの作成</a>

DocumentDB データベース アカウントを Azure にプロビジョニングするには、[Azure の管理ポータル][]
を開いて、ホームページにある Azure ギャラリー タイルをクリックするか、または、画面の左下隅にある [+] をクリックします。

![Alt text][1]

これで Azure のギャラリーが開き、Azure に用意されているさまざまな
サービスを選択することができます。ギャラリーのカテゴリ一覧から
[データ、ストレージ、バックアップ] を選択します。

![Alt text][2]

その中から、Azure DocumentDB に該当するオプションを選択します。

![Alt text][3]

次に、画面の一番下にある [作成] を選択します。

![Alt text][4]

[新しい DocumentDB] ブレードが開き、新しいアカウントの名前、
リージョン、スケール、リソース グループなどの設定を指定することが
できます。

![Alt text][5]

アカウントに使用する値を指定した後、[作成] をクリックすると、
プロビジョニング プロセスが開始されて、データベース アカウントが作成されます。
プロビジョニング プロセスが完了すると、ポータルの通知領域に
その旨が表示され、スタート画面上のタイル (タイルを作成する設定になっている場合) が、処理の完了を示す表示に
変化します。

![Alt text][6]

プロビジョニングの完了後、スタート画面から DocumentDB
タイルをクリックすると、新しく作成した DocumentDB アカウントの
メイン ブレードが表示されます。

![Alt text][7]
![Alt text][8]

[キー] ボタンを使用してエンドポイントの URL とプライマリ キーを表示し、
それらの値をクリップボードにコピーして、いつでも呼び出せる場所に保存してください。
これから作成する Web アプリケーションの中でそれらの値を使用します。

## <a name="_Toc395783178">新しい Node.js アプリケーションの作成</a>

Visual Studio で、[ファイル] の [新しいプロジェクト] を選択し、[基本的な Microsoft Azure Express アプリケーション] を選択します

![Alt text][9]

基本的な Express アプリケーションが作成されます。依存関係を
インストールするかどうかをたずねるメッセージが表示された場合は、[はい] を選択します。新しい Express
アプリケーションに必要なすべての npm パッケージがインストールされます。

この操作が完了すると、ソリューション エクスプローラーは
次のようになります。

![Alt text][10]

この画面から、Express、Jade、Stylus がインストールされていることがわかります。

Visual Studio で F5 キーを押すと、プロジェクトがビルドされた後、
Node.js が起動され、"Hello World" の Express 版が
ブラウザーに表示されます。

![Alt text][11]

## <a name="_Toc395783179">追加モジュールのインストール</a>

**package.json** ファイルは、プロジェクトのルートに作成される
ファイルの 1 つです。このファイルには、Express アプリケーションで必要な
追加モジュールのリストが含まれます。このファイルは、
後でこのアプリケーションを Azure の Web サイトにデプロイするときに、
アプリケーションのサポートのために Azure にインストールする必要があるモジュールを判断するために使用されます。

![Alt text][12]

このチュートリアルでは、追加で 2 つのパッケージをインストールする必要があります。

ソリューション エクスプローラーで、[npm] を右クリックし、
[npm パッケージのインストール] を選択します。

![Alt text][13]

[新しい npm パッケージのインストール] ダイアログで、「**nconf**」と
入力してモジュールを検索します。このモジュールは、アプリケーションが構成ファイルから
データベース構成値を読み取るために使用されます。

![Alt text][14]

最後に、**documentdb** を検索して、同じように Azure DocumentDB
モジュールをインストールします。これは、すべての DocumentDB 操作を行うモジュール
です。

これらの 2 つの追加モジュールと依存関係をインストールすると、
ソリューション エクスプローラーの **npm**
モジュールの下にこれらのモジュールが表示されます。

![Alt text][15]

アプリケーションの **package.json** ファイルを見ると、モジュールが
追加されていることがわかります。このファイルは、
後でアプリケーションを実行するときにダウンロードしてインストールする
必要があるパッケージを Azure に通知するために使用されます。

次の例と同じになるように、必要に応じて package.json ファイルを編集します。

![Alt text][16]

これにより、アプリケーションがこれらの追加モジュールに依存していることが
Node (および後で Azure) に通知されます。

# <a name="_Toc395783180">ノード アプリケーションでの DocumentDB サービスの使用</a>

最初の設定と構成を行った後は、いよいよ Azure DocumentDB を使ってコードを作成する作業に入ります。 最初に、作成した Express アプリケーションのルートにある **app.js** を編集します。 ファイル内で次の行を見つけます。

    app.get('/', routes.index);
    app.get('/users', user.list);

これらの行を次の 2 行に置き換えます。

    app.get('/', routes.index);
    app.post('/', routes.createOrUpdateItem);

ここでは、次に作成するフォームの既定の GET メソッドと POST メソッドで行う操作をアプリケーションに指示しています。

次に、**routes** フォルダーにある **index.js** ファイルを見つけます。エディターでファイルを開き、ファイル内のコードをすべて削除します。

ファイルの先頭に次のコードを追加します。

    // import the modules we will use
    var DocumentDBClient = require('documentdb').DocumentClient;
    var nconf = require('nconf');

    // tell nconf which config file to use
    nconf.env();
    nconf.file({ file: 'config.json' });

このコードでは、使用するモジュール (**documentdb**、**nconf**) を定義しています。

**nconf** は、データベース接続文字列などの構成値をコード内にインラインで配置する代わりに外部ファイルから読み込めるようにするモジュールです。nconf は、既定で **config.json** から構成を取得します。

次に、プロジェクトのルート (app.js と同じ場所) に **config.json** という名前の空のテキスト ファイルを作成します。

この新しい **config.json** ファイルを開き、DocumentDB エンドポイントに合わせて次の値を入力します。HOST と MASTER\_KEY の値を適切に設定してください。

    {
        "HOST"       : "<insert your DocDB endpoint here>",
        "AUTH_KEY"   : "<insert either primary or secondary key here>",
        "DATABASE"   : "ToDoList",
        "COLLECTION" : "Items"
    }

**index.js** に戻り、最後の行の後に次の行を追加します。このコードでは、構成ファイルを読み取ってその値をページ レベルの変数に格納することを指定しています。

    var host = nconf.get("HOST");
    var authKey = nconf.get("AUTH_KEY");
    var databaseId = nconf.get("DATABASE");
    var collectionId = nconf.get("COLLECTION");

上記の行を追加したら、さらに次のコードを **index.js** に追加します。

    // create some global variables which we will use later to hold instances of the DocumentDBClient, Database and Collection

    // create an instance of the DocumentDB client
    var client = new DocumentDBClient(host, { masterKey: authKey });

    exports.index = function (req, res) {       
        // before we can query for Items in the document store, we need to ensure we 
        // have a database with a collection then use the collection to read the documents
        readOrCreateDatabase(function (database) {
            readOrCreateCollection(database, function (collection) {
                listItems(collection, function (items) {
                    res.render('index', { title: 'My ToDo List', tasks: items });
                });    
            });
        });
    };

    exports.createOrUpdateItem = function (req, res) {
        //first have to set the database & collection context so that we have the self links   
        readOrCreateDatabase(function (database) {
            readOrCreateCollection(database, function (collection) {
                        
                //if we find an item on the form, we'll create it in the database
                var item = req.body.item;
                if (item) {
                    createItem(collection, item, function () {
                    res.redirect('/');
                });
                
                //else let's look for items marked as completed, 
                //and update that item in the database
                } else {
                    var completed = req.body.completed;
                    
                    //check if completed is actually an Array, if not make it one. 
                    //this happens when you select only one item            
                    if (!completed.forEach)
                        completed = [completed];
                    
                    //use a recursive function to loop through items in 
                    //array calling updateItem each time through                                    
                    function updater(i) {
                        if (i < completed.length) {
                            updateItem(collection, completed[i], function () {
                                updater(i + 1);
                            });
                        } else {
                            res.redirect('/');
                        }
                    }
                    
                    //kick off the recursion
                    updater(0);
                }
            });
        });
    }

ここには、前にルートを定義したときに **app.js** 内で使用することをアプリケーションに指示した 2 つの関数が定義されています。インデックス ビューで GET が受け取られると、**exports.index** 関数が実行されます。同様に、インデックス ビューで POST が受け取られると、**exports.createOfUpdateItem** 関数が実行されます。

これらの 2 つの関数には、今作成しているアプリケーションのすべての動作が含まれていますが、単にコードを読みやすくする目的で他の関数への呼び出しも含まれています。続けて次のコードを **index.js** ファイルに追加します。このコードには、上記の 2 つの関数によって使用されるすべてのメソッドと、すべての DocumentDB 呼び出しが含まれています。それぞれの関数については、後で大まかに説明します。

    // update item
    var updateItem = function (collection, itemId, callback) {
        //first fetch the document based on the id
        getItem(collection, itemId, function (doc) {
            //now replace the document with the updated one
            doc.completed = true;
            client.replaceDocument(doc._self, doc, function (err, replacedDoc) {
                if (err) {
                    throw (err);
                }
                
                callback();
            });
        });
    }

    // get item
    var getItem = function (collection, itemId, callback) {      
        client.queryDocuments(collection._self, 'SELECT * FROM root r WHERE r.id="' + itemId + '"').toArray(function (err, results) {
            if (err) {
                throw (err);
            }

            callback(results[0]);
        });
    }

    // create new item
    var createItem = function (collection, documentDefinition, callback) {
        documentDefinition.completed = false;
        client.createDocument(collection._self, documentDefinition, function (err, doc) {
            if (err) {
                throw (err);
            }
            
            callback();
        });
    }

    // query the provided collection for all non-complete items
    var listItems = function (collection, callback) {
        client.queryDocuments(collection._self, 'SELECT * FROM root r WHERE r.completed=false').toArray(function (err, docs) {
            if (err) {
                throw (err);
            }
            
            callback(docs);
        });
    }

    // if the database does not exist, then create it, else return the database object
    var readOrCreateDatabase = function (callback) {
        client.queryDatabases('SELECT * FROM root r WHERE r.id="' + databaseId + '"').toArray(function (err, results) {
            if (err) {
                // some error occured, rethrow up
                throw (err);
            }
            if (!err && results.length === 0) {
                // no error occured, but there were no results returned 
                // indicating no database exists matching the query            
                client.createDatabase({ id: databaseId }, function (err, createdDatabase) {
                    callback(createdDatabase);
                });
            } else {
                // we found a database
                callback(results[0]);
            }
        });
    };

    // if the collection does not exist for the database provided, create it, else return the collection object
    var readOrCreateCollection = function (database, callback) {
        client.queryCollections(database._self, 'SELECT * FROM root r WHERE r.id="' + collectionId + '"').toArray(function (err, results) {
            if (err) {
                // some error occured, rethrow up
                throw (err);
            }           
            if (!err && results.length === 0) {
                // no error occured, but there were no results returned 
                //indicating no collection exists in the provided database matching the query
                client.createCollection(database._self, { id: collectionId }, function (err, createdCollection) {
                    callback(createdCollection);
                });
            } else {
                // we found a collection
                callback(results[0]);
            }
        });
    };

**updateItem** – フォームから渡された項目 ID に基づいてデータベース内のドキュメントを更新します。このメソッドは、この ID を使用して DocumentDB に対する ***readDocument*** メソッドを実行して、格納されている特定のドキュメントを読み取ります。次に、ドキュメントの "completed" 属性を true に変更してドキュメントの操作が完了したことを示した後、データベース内のドキュメントの置換操作に進みます。

**getItem –** ***queryDocuments*** と項目の id プロパティを使用して、データベースから 1 つの項目を取得します。

**createItem** – ***createDocument*** メソッドを使用して、フォームに入力された項目名と項目カテゴリに基づいて、データベース内に新しいドキュメントを作成します。さらに、completed フラグを false に設定して、この項目の操作がまだ完了していないことを示します。

**listItems** – ***queryDocuments*** を使用して、コレクション内の未完了 (completed = false) のすべてのドキュメントを検索します。このメソッドは、ANSI-SQL に基づく DocumentDB クエリ文法を使用して、この一般的でありながら強力なクエリ機能を実現しています。

**readOrCreateDatabase** – ***queryDatabases*** を使用して、この名前のデータベースが既に存在するかどうかを調べます。この名前のデータベースが見つからない場合は、***createDatabase*** を使用して、(構成ファイルで) 指定された識別子を持つ新しいデータベースを (構成ファイルで) 指定されたエンドポイント上に作成します。

**readOrCreateCollection** – readOrCreateDatabase と同様に、このメソッドは、最初に指定された識別子を持つコレクションがあるかどうかを調べます。コレクションが見つかった場合はそれを返し、見つからない場合はコレクションを作成します。

このサンプル アプリケーションで DocumentDB とやり取りするために必要なコードはこれですべてです。

次に、ユーザーがアプリケーションとやり取りするためのユーザー インターフェイスを作成します。作成した Express アプリケーションでは、ビュー エンジンとして **Jade** を使用しています。Jade の詳細については、<http://jade-lang.com/> を参照してください。

**views** フォルダーにある **layout.jade** ファイルを開き、その内容を次のコードで置き換えます。

    doctype html
    html
      head
        title= title
        meta(http-equiv='X-UA-Compatible', content='IE=10')
        link(rel='stylesheet', href='/stylesheets/style.css')
        link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
      body.app
        nav.navbar.navbar-fixed-top
          div.navbar-inner
            container
                a.brand(href='/') My Tasks
        block content

このコードでは、アプリケーションの特定の HTML を描画し、コンテンツ ページのレイアウトを指定するための、"**content**" という名前の "ブロック (**block**)" を作成することを **Jade** エンジンに指示しています。

次に、**index.jade** ファイル (index.js に関連付けられているビュー) を開き、その内容を次のコードで置き換えます。

    extends layout

    block content
      h1= title
      br

      form(action="/", method="post")
        table.table.table-striped.table-bordered
          tr
            td Name
            td Category
            td Complete
          each task in tasks
            tr
                td #{task.name}
                td #{task.category}
                td 
                    input(type="checkbox", name="completed", value="#{task.id}", checked=(task.completed == true))
        button.btn(type="submit") Update tasks

      hr

      form.well(action="/", method="post")
        label Item Name:  
        input(name="item[name]", type="textbox")
        label Item Category: 
        input(name="item[category]", type="textbox")
        br
        button.btn(type="submit") Add item

これはレイアウトを拡張するためのコードで、前述の layout.jade
ファイル内の "block content" プレースホルダーの内容を定義します。

このレイアウトでは、2 つの HTML フォームを作成しています。1 つ目のフォームには、
データを格納するテーブルと、項目を更新するためのボタンが配置されます。
2 番目のフォームには、いくつかの入力フィールドと、新しい項目を作成するためのボタンが配置されます。

これらのボタンのどちらかがクリックされると (フォームの POST が行われると)、
**index.js** ファイルに作成した **createOrUpdateItem**
関数が呼び出されます。このページが直接読み込まれた場合 (GET が
実行されると)、**index** 関数が実行されます。

アプリケーションが動作するために必要なコードはこれですべてです。

</h1>
# <a name="_Toc395783181">ローカルでのアプリケーションの実行</a>

ローカル コンピューターでアプリケーションをテストするには、
Visual Studio で F5 キーを押します。アプリがビルドされた後、
Node.js が起動され、次のようなページがブラウザーに表示されます。

![Alt text][17]

1. 表示された [Item]、[Item Name]、および [Item Category] の
フィールドを使用して情報を入力し、**[Add item]** をクリックします。

2. ページが更新され、ToDo List に新しく作成された項目が表示されます。

![Alt text][18]

3. タスクを完了するには、[Complete] 列のチェック ボックスをオンにし、
**[Update tasks]** をクリックします。

</h1>
# <a name="_Toc395783182">Azure Websites へのアプリケーションのデプロイ</a>

Node.js Tools for Visual Studio がインストールされていると、
数ステップで簡単に Azure Websites にアプリケーションをデプロイできます。

プロジェクトを右クリックし、[発行] を選択します。

![Alt text][19]

次に、発行ウィザードに従って、Azure Websites の必要な構成を
指定します。ウィザードでは、既存の Web サイトを更新するか、
または新しい Web サイトを作成するかを選択できます。

必要な構成を指定したら、[発行] を選択します。

![Alt text][20]

次に、発行ウィザードに従って、Azure Websites の必要な構成を
指定します。ウィザードでは、既存の Web サイトを更新するか、
または新しい Web サイトを作成するかを選択できます。

必要な構成を指定したら、[発行] を選択します。

Visual Studio によって Azure サブスクリプションに接続され、
この Node.js アプリケーションが発行されます。

数秒すると、Web アプリケーションの発行が完了し、
ブラウザーが起動されます。作成したアプリケーションが Azure で実行されているようすが
確認できます。

# <a name="_Toc395783183"></a> <a name="_Toc395637775">まとめ</a>

ご利用ありがとうございます。ここでは初めての方を対象に、Azure DocumentDB
を使用した Node.js Express Web アプリケーションを作成し、Azure Websites に発行する方法を説明しました。

アプリケーションの完全なソース コードは[こちら][]からダウンロードできます。ぜひご参考ください。

</h1>

  [Alt text]: ./media/documentdb-nodejs-application/image1.png
  [Node.js]: http://nodejs.org/
  [Git]: http://git-scm.com/
  [Visual Studio 2013]: http://msdn.microsoft.com/en-us/vstudio/cc136611.aspx
  [Node.js Tools for Visual Studio]: https://nodejstools.codeplex.com/
  [Azure CLI]: http://azure.microsoft.com/ja-jp/documentation/articles/xplat-cli/
  [Azure の管理ポータル]: http://portal.azure.com
  [1]: ./media/documentdb-nodejs-application/image2.png
  [2]: ./media/documentdb-nodejs-application/image3.png
  [3]: ./media/documentdb-nodejs-application/image4.png
  [4]: ./media/documentdb-nodejs-application/image5.png
  [5]: ./media/documentdb-nodejs-application/image6.png
  [6]: ./media/documentdb-nodejs-application/image7.png
  [7]: ./media/documentdb-nodejs-application/image8.png
  [8]: ./media/documentdb-nodejs-application/image9.png
  [9]: ./media/documentdb-nodejs-application/image10.png
  [10]: ./media/documentdb-nodejs-application/image11.png
  [11]: ./media/documentdb-nodejs-application/image12.png
  [12]: ./media/documentdb-nodejs-application/image13.png
  [13]: ./media/documentdb-nodejs-application/image14.png
  [14]: ./media/documentdb-nodejs-application/image15.png
  [15]: ./media/documentdb-nodejs-application/image16.png
  [16]: ./media/documentdb-nodejs-application/image17.png
  [17]: ./media/documentdb-nodejs-application/image18.png
  [18]: ./media/documentdb-nodejs-application/image19.png
  [19]: ./media/documentdb-nodejs-application/image20.png
  [20]: ./media/documentdb-nodejs-application/image21.png
  [こちら]: http://go.microsoft.com/fwlink/?LinkID=509839&clcid=0x409
