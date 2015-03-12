<properties 
	pageTitle="テーブル ストレージを使用した Node.js Web サイト | Microsoft Azure" 
	description="Azure Table サービスを使用して、Azure Web サイトでホストされる Node アプリケーションのデータを格納する方法を示すチュートリアル。" 
	services="web-sites, storage" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="mwasson"/>





# Azure テーブル サービスを使用する Node.js Web アプリケーション

このチュートリアルでは、Azure データ管理で提供されるテーブル サービスを使用して、Azure でホストされる[ノード] アプリケーションのデータを格納する方法やデータにアクセスする方法を示します。このチュートリアルは、ノードおよび [Git] を使用した経験があることを前提としています。

学習内容:

* npm (ノード パッケージ マネージャー) を使用してノード モジュールをインストールする方法

* Azure テーブル サービスを使用する方法

* Mac および Linux 用 Azure コマンド ライン ツールを使用して Azure の Web サイトを作成する方法

このチュートリアルでは、タスクを作成、取得、完了する機能を備えた、単純な Web ベースのタスク管理アプリケーションを作成します。タスクはテーブル サービスに格納されます。

このチュートリアルのプロジェクト ファイルは **tasklist** という名前のディレクトリに保存され、作成されるアプリケーションは次のようになります。

![A web page displaying an empty tasklist][node-table-finished]

> [AZURE.NOTE] このチュートリアルでは、**tasklist** フォルダーを参照します。パスのセマンティクスはオペレーティング システムによって異なるので、このフォルダーへの完全なパスは省略しています。このフォルダーは、ローカル ファイル システムのアクセスしやすい場所 (**~/node/tasklist** や **c:\node\tasklist** など) に作成してください。

> [AZURE.NOTE] 以下に示す手順の多くでは、コマンド ラインを使用します。これらの手順では、**cmd.exe** (Windows) や **Bash** (Unix シェル) など、お使いのオペレーティング システムのコマンド ラインを使用してください。OS X システムでは、ターミナル アプリケーションを使用してコマンド ラインにアクセスできます。

##前提条件

この記事の手順を実行する前に、次のソフトウェアがインストールされていることを確認してください。

* [ノード] Version 0.10.24 以上

* [Git]

* テキスト エディター

* Web ブラウザー

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##ストレージ アカウントを作成する

ストレージ アカウントを作成するには、次の手順を実行しますこのアカウントは、このチュートリアルの以降の手順で使用されます。

1. Web ブラウザーを開き、[Azure ポータル]に移動します。ログインを求められた場合は、Azure サブスクリプションの情報を使ってログインします。

2. ポータルの下部にある **[+ 新規]** をクリックし、**[ストレージ アカウント]** を選択します。

	![+new][portal-new]

	![storage account][portal-storage-account]

3. **[簡易作成]** を選択し、このストレージ アカウントの URL とリージョン/アフィニティ グループを入力します。これはチュートリアルであり、グローバルにレプリケートする必要がないので、**[ジオ (主要地域) レプリケーションの有効化]** チェック ボックスをオフにします。最後に、[ストレージ アカウントの作成] をクリックします。

	![quick create][portal-quick-create-storage]

	入力した URL は、後の手順でアカウント名として参照されるのでメモしておきます。

4. ストレージ アカウントが作成されたら、ページの下部にある **[キーの管理]** をクリックします。これにより、このストレージ アカウントのプライマリ アクセス キーとセカンダリ アクセス キーが表示されます。プライマリ アクセス キーをコピーして保存した後、チェックマークをクリックします。

	![access keys][portal-storage-access-keys]

##モジュールのインストールとスキャフォールディングの生成

ここでは、新しい Node アプリケーションを作成し、npm を使用してモジュール パッケージを追加します。タスク一覧アプリケーションの場合は、[Express] モジュールと [Azure] モジュールを使用します。Express モジュールは node の Model View Controller フレームワークを提供し、Azure モジュールはテーブル サービスへの接続を提供します。

###express のインストールとスキャフォールディングの生成

1. コマンド ラインで、**tasklist** ディレクトリに移動します。**tasklist** ディレクトリがない場合は作成します。

2. 以下を入力して、express コマンドをインストールします。

		npm install express-generator@4.2.0 -g

    > [AZURE.NOTE] 一部のオペレーティング システムで "-g" パラメーターを使用する場合、"**Error:EPERM, chmod '/usr/local/bin/express'**" というエラーが表示され、管理者としてアカウントを実行することを要求されることがあります。このような場合は、**sudo** コマンドを使用して、より高い権限レベルで npm を実行します。

    このコマンドの出力は次のように表示されます。

		express-generator@4.2.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)

	> [AZURE.NOTE] express モジュールのインストール時に "-g" パラメーターを使用すると、モジュールはグローバルにインストールされます。これは、追加のパス情報を入力することなく **express** コマンドにアクセスして、Web サイトのスキャフォールディングを生成できるようにするためです。**

4. このアプリケーションで使用するスキャフォールディングを作成するには、**express** コマンドを使用します。

        express

	このコマンドの出力は次のように表示されます。

		   create : .
		   create : ./package.json
		   create : ./app.js
		   create : ./public
		   create : ./public/images
		   create : ./routes
		   create : ./routes/index.js
		   create : ./routes/users.js
		   create : ./public/stylesheets
		   create : ./public/stylesheets/style.css
		   create : ./views
		   create : ./views/index.jade
		   create : ./views/layout.jade
		   create : ./views/error.jade
		   create : ./public/javascripts
		   create : ./bin
		   create : ./bin/www
		
		   install dependencies:
		     $ cd . && npm install
		
		   run the app:
		     $ DEBUG=my-application ./bin/www

	このコマンドが完了すると、**tasklist** ディレクトリ内に複数の新しいディレクトリやファイルが作成されています。

###追加モジュールのインストール

**package.json** ファイルは、**express** コマンドで作成されるファイルの 1 つです。このファイルには、Express アプリケーションで必要な追加モジュールのリストが含まれます。このファイルは、後でこのアプリケーションを Azure の Web サイトにデプロイするときに、アプリケーションのサポートのために Azure にインストールする必要があるモジュールを判断するために使用されます。

1. コマンド ラインで **tasklist** フォルダーに移動し、次のコマンドを入力して **package.json** ファイルに記述されたモジュールをインストールします。

        npm install

    このコマンドの出力は次のように表示されます。

		debug@0.7.4 node_modules\debug
		
		static-favicon@1.0.2 node_modules\static-favicon
		
		morgan@1.0.1 node_modules\morgan
		└── bytes@0.3.0
		
		cookie-parser@1.0.1 node_modules\cookie-parser
		├── cookie-signature@1.0.3
		└── cookie@0.1.0
		
		body-parser@1.0.2 node_modules\body-parser
		├── qs@0.6.6
		├── raw-body@1.1.7 (string_decoder@0.10.25-1, bytes@1.0.0)
		└── type-is@1.1.0 (mime@1.2.11)
		
		express@4.2.0 node_modules\express
		├── parseurl@1.0.1
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── cookie@0.1.2
		├── escape-html@1.0.1
		├── cookie-signature@1.0.3
		├── debug@0.8.1
		├── fresh@0.2.2
		├── qs@0.6.6
		├── range-parser@1.0.0
		├── methods@1.0.0
		├── buffer-crc32@0.2.1
		├── serve-static@1.1.0
		├── path-to-regexp@0.1.2
		├── send@0.3.0 (debug@0.8.0, mime@1.2.11)
		├── type-is@1.1.0 (mime@1.2.11)
		└── accepts@1.0.1 (negotiator@0.4.7, mime@1.2.11)
		
		jade@1.3.1 node_modules\jade
		├── commander@2.1.0
		├── character-parser@1.2.0
		├── mkdirp@0.3.5
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── constantinople@2.0.1 (uglify-js@2.4.15)
		├── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		└── with@3.0.0 (uglify-js@2.4.15)


	これによって、Express で必要な既定のモジュールがすべてインストールされます。

2. 次に、次のコマンドを入力して、[azure]、[node-uuid]、[nconf]、および [async] モジュールをローカルにインストールし、これらのモジュールのエントリを **package.json** ファイルに保存します。

		npm install azure-storage node-uuid async nconf --save

	このコマンドの出力は次のように表示されます。

		async@0.9.0 node_modules\async

		node-uuid@1.4.1 node_modules\node-uuid
		
		nconf@0.6.9 node_modules\nconf
		├── ini@1.2.1
		├── async@0.2.9
		└── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.10)
		
		azure-storage@0.3.0 node_modules\azure-storage
		├── extend@1.2.1
		├── xmlbuilder@0.4.3
		├── mime@1.2.11
		├── validator@3.1.0
		├── underscore@1.4.4
		├── xml2js@0.2.7 (sax@0.5.2)
		└── request@2.27.0 (forever-agent@0.5.2, aws-sign@0.3.0, json-stringify-safe@5.0.0, tunnel-agent@0.3.0, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, form-data@0.1.4, hawk@1.0.0, http-signature@0.10.0)

##ノード アプリケーションでのテーブル サービスの使用

ここでは、**express** コマンドで作成された基本的なアプリケーションを、タスクのモデルを格納する **task.js** ファイルを追加することによって拡張します。また、既存の **app.js** を変更し、このモデルを使用する新しい **tasklist.js** ファイルを作成します。

### モデルの作成

1. **tasklist** ディレクトリ内に、**models** という名前の新しいディレクトリを作成します。

2. **models** ディレクトリ内に、**task.js** という名前の新しいファイルを作成します。このファイルには、アプリケーションで作成されるタスクのモデルが格納されます。

3. **task.js** ファイルの先頭に、必要なライブラリを参照する次のコードを追加します。

        var azure = require('azure-storage');
  		var uuid = require('node-uuid');
		var entityGen = azure.TableUtilities.entityGenerator;

4. 次に、Task オブジェクトを定義およびエクスポートするコードを追加します。このオブジェクトは、テーブルへの接続を処理します。

  		module.exports = Task;

		function Task(storageClient, tableName, partitionKey) {
		  this.storageClient = storageClient;
		  this.tableName = tableName;
		  this.partitionKey = partitionKey;
		  this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
		    if(error) {
		      throw error;
		    }
		  });
		};

5. 次に、Task オブジェクトの追加のメソッドを定義する次のコードを追加します。このメソッドによって、テーブルに格納されたデータを操作できます。

		Task.prototype = {
		  find: function(query, callback) {
		    self = this;
		    self.storageClient.queryEntities(this.tableName, query, null, function entitiesQueried(error, result) {
		      if(error) {
		        callback(error);
		      } else {
		        callback(null, result.entries);
		      }
		    });
		  },

		  addItem: function(item, callback) {
		    self = this;
		    // use entityGenerator to set types
			// NOTE: RowKey must be a string type, even though
            // it contains a GUID in this example.
		    var itemDescriptor = {
		      PartitionKey: entityGen.String(self.partitionKey),
		      RowKey: entityGen.String(uuid()),
		      name: entityGen.String(item.name),
		      category: entityGen.String(item.category),
		      completed: entityGen.Boolean(false)
		    };
		    self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
		      if(error){  
		        callback(error);
		      }
		      callback(null);
		    });
		  },

		  updateItem: function(rKey, callback) {
		    self = this;
		    self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
		      if(error) {
		        callback(error);
		      }
		      entity.completed._ = true;
		      self.storageClient.updateEntity(self.tableName, entity, function entityUpdated(error) {
		        if(error) {
		          callback(error);
		        }
		        callback(null);
		      });
		    });
		  }
		}

6. **task.js** ファイルを保存して閉じます。

###コントローラーの作成

1. **tasklist/routes** ディレクトリに **tasklist.js** という名前の新しいファイルを作成し、テキスト エディターで開きます。

2. 次のコードを **tasklist.js** に追加します。これによって、**tasklist.js** で使用される azure モジュールと async モジュールが読み込まれます。また、**TaskList** 関数が定義されます。先ほど定義した **Task** オブジェクトのインスタンスがこの関数に渡されます。

		var azure = require('azure-storage');
		var async = require('async');

		module.exports = TaskList;

		function TaskList(task) {
		  this.task = task;
		}

2. Continue adding to the **tasklist.js** ファイルへの内容の追加を続行し、**showTasks**、**addTask**、**completeTasks** の各メソッドを追加します。

		TaskList.prototype = {
		  showTasks: function(req, res) {
		    self = this;
		    var query = new azure.TableQuery()
		      .where('completed eq ?', false);
		    self.task.find(query, function itemsFound(error, items) {
		      res.render('index',{title: 'My ToDo List ', tasks: items});
		    });
		  },

		  addTask: function(req,res) {
		    var self = this      
		    var item = req.body.item;
		    self.task.addItem(item, function itemAdded(error) {
		      if(error) {
		        throw error;
		      }
		      res.redirect('/');
		    });
		  },

		  completeTask: function(req,res) {
		    var self = this;
		    var completedTasks = Object.keys(req.body);
		    async.forEach(completedTasks, function taskIterator(completedTask, callback) {
		      self.task.updateItem(completedTask, function itemsUpdated(error) {
		        if(error){
		          callback(error);
		        } else {
		          callback(null);
		        }
		      });
		    }, function goHome(error){
		      if(error) {
		        throw error;
		      } else {
		       res.redirect('/');
		      }
		    });
		  }
		}

3. **tasklist.js** ファイルを保存します。

### app.js の変更

1. **tasklist** ディレクトリ内の **app.js** ファイルを、テキスト エディターで開きます。このファイルは、先ほど **express** コマンドを実行したことによって作成されたものです。

2. ファイルの先頭に次のコードを追加します。このコードは、azure モジュールを読み込み、テーブル名と partitionKey を設定し、この例で使用されるストレージ資格情報を設定します。

		var azure = require('azure-storage');
		var nconf = require('nconf');
		nconf.env()
		     .file({ file: 'config.json'});
		var tableName = nconf.get("TABLE_NAME");
		var partitionKey = nconf.get("PARTITION_KEY");
		var accountName = nconf.get("STORAGE_NAME");
		var accountKey = nconf.get("STORAGE_KEY");

	> [AZURE.NOTE] nconf は、環境変数または後で作成する **config.json** ファイルから構成値を読み込みます。

3. app.js ファイル内で、次の行が表示されるまで下へスクロールします。

		app.use('/', routes);
		app.use('/users', users);

	これらの行を下のコードに置き換えます。これにより、ストレージ アカウントへの接続を使って、<strong>Task</strong> のインスタンスが初期化されます。これは <strong>TaskList</strong> に渡され、TaskList ではこれを使用してテーブル サービスを操作します。

		var TaskList = require('./routes/tasklist');
		var Task = require('./models/task');
		var task = new Task(azure.createTableService(accountName, accountKey), tableName, partitionKey);
		var taskList = new TaskList(task);

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));
	
4. **app.js** ファイルを保存します。

###index ビューの変更

1. **views** ディレクトリに移動し、テキスト エディターで **index.jade** ファイルを開きます。

2. **index.jade** ファイルの内容を、以下のコードに置き換えます。これにより、既存のタスクを表示するビューと、新しいタスクの追加とタスクの完了済みのマーク付けを実行するためのフォームを定義します。

		extends layout

		block content
		  h1= title
		  br
		
		  form(action="/completetask", method="post")
		    table.table.table-striped.table-bordered
		      tr
		        td Name
		        td Category
		        td Date
		        td Complete
		      if (typeof tasks === "undefined")
		        tr
		          td 
		      else
		        each task in tasks
		          tr
		            td #{task.name._}
		            td #{task.category._}
		            - var day   = task.Timestamp._.getDate();
		            - var month = task.Timestamp._.getMonth() + 1;
		            - var year  = task.Timestamp._.getFullYear();
		            td #{month + "/" + day + "/" + year}
		            td
		              input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
		    button.btn(type="submit") Update tasks
		  hr
		  form.well(action="/addtask", method="post")
		    label Item Name: 
		    input(name="item[name]", type="textbox")
		    label Item Category: 
		    input(name="item[category]", type="textbox")
		    br
		    button.btn(type="submit") Add item

3. **index.jade** ファイルを保存して閉じます。

###グローバル レイアウトの変更

**views** ディレクトリ内の **layout.jade** ファイルは、他の **.jade** ファイルのグローバル テンプレートとして使用されます。この手順では、[Twitter Bootstrap](https://github.com/twbs/bootstrap) を使用するようにこのファイルを変更します。Twitter Bootstrap は、見栄えのよい Web サイトを簡単にデザインできるツールキットです。

1. [Twitter Bootstrap](http://getbootstrap.com/) のファイルをダウンロードして展開します。**bootstrap\\dist\\css** フォルダーから **bootstrap.min.css** ファイルを tasklist アプリケーションの **public\\stylesheets** ディレクトリにコピーします。

2. **views** フォルダーから、テキスト エディターで **layout.jade** を開き、内容を次の内容に置き換えます。

		doctype html
		html
		  head
		    title= title
		    link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
		    link(rel='stylesheet', href='/stylesheets/style.css')
		  body.app
		    nav.navbar.navbar-default
		      div.navbar-header
		        a.navbar-brand(href='/') My Tasks
		    block content

3. **layout.jade** ファイルを保存します。

###構成ファイルの作成

**config.json** ファイルには、SQL データベースに接続するために使用される接続文字列が含まれており、実行時にアプリケーションによって読み取られます。このファイルを作成するには、次の手順を実行します。

1. **tasklist** ディレクトリに **config.json** という名前の新しいファイルを作成し、テキスト エディターで開きます。

2. **config.json** ファイルの内容は、次のように設定する必要があります。

		{
			"STORAGE_NAME": "storage account name",
			"STORAGE_KEY": "storage access key",
			"PARTITION_KEY": "mytasks",
			"TABLE_NAME": "tasks"
		}

	**storage account name** を、先ほど作成したストレージ アカウントの名前に置き換えます。**storage access key** を、ストレージ アカウントのプライマリ アクセス キーに置き換えます。

3. ファイルを保存します。

##ローカルでのアプリケーションの実行

ローカル コンピューターでアプリケーションをテストするには、次の手順を実行します。

1. コマンド ラインで、**tasklist** ディレクトリに移動します。

2. 次のコマンドを使用して、ローカルでアプリケーションを起動します。

        npm start

3. Web ブラウザーを開いて、http://127.0.0.1:3000 にアクセスします。次のような Web ページが表示されます。

    ![A webpage displaying an empty tasklist][node-table-finished]

4. 表示された **Item Name** と **Item Category**のフィールドを使用して情報を入力し、**[Add item]** をクリックします。

5. ページが更新され、ToDo List テーブルにアイテムが表示されるようになります。

    ![An image of the new item in the list of tasks][node-table-list-items]

6. タスクを完了するには、[Complete] 列のチェック ボックスをオンにし、**[Update tasks]** をクリックします。

7. ノード プロセスを停止するには、コマンド ラインで、**CTRL** キーを押しながら **C** キーを押します。

##Azure へのアプリケーションのデプロイ

ここで説明する手順では、Azure コマンド ライン ツールを使用して新しい Azure の Web サイトを作成し、Git を使用してアプリケーションをデプロイします。これらの手順を実行するには、Azure サブスクリプションが必要です。

> [AZURE.NOTE] これらの手順は、Azure ポータルを使用して実行することもできます。Azure ポータルを使用して Node.js アプリケーションをデプロイする手順については、「[Node.js Web サイトの構築と Azure へのデプロイ]」を参照してください。

> [AZURE.NOTE] 初めて Azure の Web サイトを作成した場合は、Azure ポータルを使用してこのアプリケーションをデプロイする必要があります。

###Azure サブスクリプションの作成

Azure サブスクリプションをまだ取得していない場合でも、[無料]でサインアップできます。サインアップした後で、次の手順に従ってこのチュートリアルを続行します。

[AZURE.INCLUDE [antares-iaas-signup](../includes/antares-iaas-signup.md)]

###Mac および Linux 用 Azure コマンド ライン ツールのインストール

コマンド ライン ツールをインストールするには、次のコマンドを使用します。
	
	npm install azure-cli -g

> [AZURE.NOTE] 詳細については、「[Azure クロスプラットフォーム コマンド ライン インターフェイスのインストールと構成](/ja-jp/documentation/articles/xplat-cli/)」を参照してください;

> [AZURE.NOTE] コマンド ライン ツールは、主に Mac および Linux ユーザー向けに作成されていますが、Node.js に基づいているため、Node を実行できる任意のシステムで動作します。

###発行の設定をインポートする

Azure でコマンド ライン ツールを使用する前に、自分のサブスクリプションに関する情報が含まれているファイルをダウンロードする必要があります。このファイルをダウンロードしてインポートするには、次のステップを実行します。

1. コマンド ラインで、**tasklist** ディレクトリに移動します。

2. 次のコマンドを入力してブラウザーを起動し、ダウンロード ページに移動します。ログインを求められた場合は、サブスクリプションに関連付けられたアカウントを使ってログインします。

		azure account download
	
	![The download page][download-publishing-settings]
	
	ファイルのダウンロードが自動的に開始されます。ダウンロードが開始されない場合は、ページの先頭にあるリンクをクリックして、手動でファイルをダウンロードできます。

3. ファイルのダウンロードが完了したら、次のコマンドを使用して設定をインポートします。

		azure account import <path-to-file>
		
	そのためには、前の手順でダウンロードした発行設定ファイルのパスとファイル名を指定します。コマンドが完了すると、次のような出力が表示されます。
	
		info:   Executing command account import
		info:   Setting service endpoint to: management.core.windows.net
		info:   Setting service port to: 443
		info:   Found subscription: YourSubscription
		info:   Setting default subscription to: YourSubscription
		warn:   The 'C:\users\username\downloads\YourSubscription-6-7-2012-credentials.publishsettings' file contains sensitive information.
		warn:   Remember to delete it now that it has been imported.
		info:   Account publish settings imported successfully
		info:   account import command OK

4. インポートが完了したら、不要になった発行設定ファイルを削除してください。このファイルには、Azure サブスクリプションの機密情報が含まれているためです。

###Azure Web サイトの作成

1. コマンド ラインで、**tasklist** ディレクトリに移動します。

2. 新しい Azure の Web サイトを作成するには、次のコマンドを使用します。

		azure site create --git
		
	Web サイト名と Web サイトが配置されるデータ センターを指定するよう求められます。一意の名前を指定し、現在の場所に地理的に近いデータ センターを選択します。
	
	`--git` パラメーターにより、この Web サイトの Azure に Git リポジトリが作成されます。存在しない場合は、現在のディレクトリで Git リポジトリが初期化されます。また、 'azure' という名前の [Git リモート]も作成されます。これは、Azure にアプリケーションを発行するために使用されます。最後に、**web.config** ファイルが作成されます。このファイルには、ノード アプリケーションをホストするために、Azure によって使用される設定が格納されます。
	
	> [AZURE.NOTE] 既に Git リポジトリが含まれているディレクトリからこのコマンドを実行した場合、ディレクトリは再初期化されません。
	
	> [AZURE.NOTE] `--git` パラメーターを省略した場合でも、ディレクトリには Git リポジトリが含まれ、'azure' リモートが作成されます。
	
	このコマンドが完了すると、次のような出力が表示されます。**Website created at** で始まる行には、Web サイトの URL が含まれています。
	
		info:   Executing command site create
		help:   Need a site name
		Name:   TableTasklist
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default .gitignore file
		info:   Creating a new web site
		info:   Created web site at  tabletasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure https://username@tabletasklist.azurewebsites.net/TableTasklist.git`
		info:   site create command OK

	> [AZURE.NOTE] これがサブスクリプションで最初の Azure Web サイトである場合、ポータルを使用して Web サイトを作成するように指示するメッセージが表示されます。詳細については、「[Node.js Web サイトの構築と Azure へのデプロイ]」を参照してください。

###アプリケーションの発行

1. [ターミナル] ウィンドウで、**tasklist** ディレクトリに移動します (現在のディレクトリがこのディレクトリではない場合)。

2. 次のコマンドを使用して、ローカル Git リポジトリにファイルを追加し、コミットします。

		git add .
		git commit -m "adding files"

3. 最新の Git リポジトリの変更内容を Azure の Web サイトに発行する場合、ターゲット分岐は、Web サイトのコンテンツ用に使用されるので、**master** であることを指定する必要があります。

		git push azure master
	
	デプロイの最後に、次のようなステートメントが表示されます。
	
		To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
 		 * [new branch]      master -> master

4. プッシュ操作が完了したら、先ほど  `azure create site` コマンドから返された Web サイトの URL に移動してアプリケーションを表示します。

###環境変数への切り替え

前の手順で、環境変数を検索するか、**config.json** ファイルから値を読み込むコードを実装しました。次の手順では、実際のアプリケーションが環境変数を使ってアクセスする、Web サイト構成に含まれるキー/値のペアを作成します。

1. 管理ポータルで、**[Web サイト]** をクリックし、対象となる Web サイトを選択します。

	![Open website dashboard][go-to-dashboard]

2. **[構成]** をクリックし、ページの **[アプリケーション設定]** セクションを探します。 

	![configure link][web-configure]

3. **[アプリケーション設定]** セクションで、**[キー]** に「**STORAGE_NAME**」と入力し、**[値]** に自分のストレージ アカウントの名前を入力します。チェックマークをクリックして、次のフィールドに移動します。次のキーと値について、この手順を繰り返します。

	* **STORAGE_KEY** - ストレージ アカウントのアクセス キー
	
	* **PARTITION_KEY** - 「mytasks」

	* **TABLE_NAME** - 「tasks」

	![app settings][app-settings]

4. ページの下部にある **[保存]** アイコンをクリックし、この変更を実行時環境にコミットします。

	![app settings save][app-settings-save]

5. コマンド ラインで、**tasklist** ディレクトリに移動し、次のコマンドを入力して **config.json** ファイルを削除します。

		git rm config.json
		git commit -m "Removing config file"

6. 次のコマンドを実行して、変更内容を Azure にデプロイします。

		git push azure master

変更内容を Azure に展開した後も、Web アプリケーションは **[アプリ設定]** のエントリから接続文字列を読み取って動作し続けます。これを確認するために、**[アプリケーション設定]** の **STORAGE_KEY** のエントリを無効な値に変更します。この値を保存すると、無効なストレージ アクセス キー設定のために Web サイトは動作しなくなります。

##次のステップ

この記事の手順では、テーブル サービスを使用して情報を格納する方法を説明しましたが、MongoDB を使用することもできます。詳細については、「[MongoDB を使用する Node.js Web アプリケーション]」を参照してください。

##その他のリソース

[Mac および Linux 用 Azure コマンド ライン ツール]    
[Create and deploy a Node.js application to Azure Web Sites]: /ja-jp/documentation/articles/web-sites-nodejs-develop-deploy-mac/
[Publishing to Azure Web Sites with Git]: /ja-jp/documentation/articles/web-sites-publish-source-control/
[Azure Developer Center]: /ja-jp/develop/nodejs/


[ノード]: http://nodejs.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[無料]: http://windowsazure.com
[Git リモート]: http://git-scm.com/docs/git-remote

[MongoDB を使用する Node.js Web アプリケーション]: /ja-jp/documentation/articles/web-sites-nodejs-store-data-mongodb/
[Mac および Linux 用 Azure コマンド ライン ツール]: /ja-jp/documentation/articles/xplat-cli/

[Publishing to Azure Web Sites with Git]: /ja-jp/documentation/articles/web-sites-publish-source-control/
[azure]: https://github.com/Azure/azure-sdk-for-node


[Azure Portal]: http://windowsazure.com


[node-table-finished]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_empty.png
[node-table-list-items]: ./media/storage-nodejs-use-table-storage-web-site/table_todo_list.png
[download-publishing-settings]: ./media/storage-nodejs-use-table-storage-web-site/azure-account-download-cli.png
[portal-new]: ./media/storage-nodejs-use-table-storage-web-site/plus-new.png
[portal-storage-account]: ./media/storage-nodejs-use-table-storage-web-site/new-storage.png
[portal-quick-create-storage]: ./media/storage-nodejs-use-table-storage-web-site/quick-storage.png
[portal-storage-access-keys]: ./media/storage-nodejs-use-table-storage-web-site/manage-access-keys.png

[go-to-dashboard]: ./media/storage-nodejs-use-table-storage-web-site/go_to_dashboard.png
[web-configure]: ./media/storage-nodejs-use-table-storage-web-site/sql-task-configure.png
[app-settings-save]: ./media/storage-nodejs-use-table-storage-web-site/savebutton.png
[app-settings]: ./media/storage-nodejs-use-table-storage-web-site/storage-tasks-appsettings.png

[Node.js Web サイトの構築と Azure へのデプロイ]: /ja-jp/documentation/articles/web-sites-nodejs-develop-deploy-mac/
<!--HONumber=42-->
