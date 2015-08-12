<properties
	pageTitle="仮想マシン上の MongoDB を使用した Azure での Node.js Web アプリの作成"
	description="MongoDB を使用して、Azure にホストされた Node.js アプリケーションにデータを格納する方法。"
	tags="azure-portal"
	services="app-service\web, virtual-machines"
	documentationCenter="nodejs"
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/23/2015"
	ms.author="mwasson"/>


# 仮想マシン上の MongoDB を使用した Azure での Node.js Web アプリの作成

このチュートリアルでは、Azure 仮想マシンでホストされる [MongoDB] を使用してデータを格納し、[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) Web アプリでホストされる[ノード] アプリケーションからデータにアクセスする方法を示します。[MongoDB] は、高いパフォーマンスを特徴とし、広く普及しているオープン ソースの NoSQL データベースです。

学習内容:

* VM Depot から Ubuntu または MongoDB を実行する仮想マシンを設定する方法
* ノード アプリケーションから MongoDB にアクセスする方法
* Azure CLI を使用して Azure App Service で Web アプリを作成する方法

このチュートリアルでは、タスクを作成、取得、完了する機能を備えた、単純な Web ベースのタスク管理アプリケーションを作成します。タスクは MongoDB に格納されます。

> [AZURE.NOTE]このチュートリアルでは、仮想マシンにインストールした MongoDB のインスタンスを使用します。MongoLab が提供するホステッド MongoDB インスタンスを使用する場合は、「[Azure での MongoLab アドオンを使用して MongoDB 対応の Node.js Web アプリの作成](store-mongolab-web-sites-nodejs-store-data-mongodb)」を参照してください。

このチュートリアルのプロジェクト ファイルは **tasklist** という名前のディレクトリに保存され、作成されるアプリケーションは次のようになります。

![空のタスク一覧が表示されている Web ページ][node-mongo-finished]

> [AZURE.NOTE]以下に示す手順の多くでは、コマンド ラインを使用します。これらの手順では、**Windows PowerShell** (Windows) や **Bash** (Unix Shell) など、お使いのオペレーティング システムのコマンド ラインを使用してください。OS X システムでは、ターミナル アプリケーションを使用してコマンド ラインにアクセスできます。

##前提条件

Node.js を使用するこのチュートリアルの手順では、開発環境に最新バージョンの [Node.js][node] が必要です。

また、アプリケーションを Azure Website にデプロイする際に使用するため、開発環境のコマンド ラインから [Git] を使用できる必要があります。

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

##仮想マシンの作成

<!--
After you have created the virtual machine in Azure and installed MongoDB, be sure to remember the DNS name of the virtual machine ("testlinuxvm.cloudapp.net", for example) and the external port for MongoDB that you specified in the endpoint.  You will need this information later in the tutorial.-->

新しい VM を作成してから [MongoDB のインストール ガイド][installguides]に従ってその VM に MongoDB をインストールすることもできますが、Mongo DB とともにプレインストールされている VM が Azure Marketplace で利用できます。次の手順は、このような多くの VM テンプレートのいずれかを使用する方法を示します。

> [AZURE.NOTE]このチュートリアルで使用するコミュニティ イメージは、MongoDB データを OS ディスクに格納します。これはチュートリアルの目的には十分ですが、MongoDB データをデータ ディスクに格納するとパフォーマンスが向上します。データ ディスクなどの新しい VM を作成して MongoDB データをデータ ディスクに格納する手順については、「[Linux on Azure への MongoDB のインストール][mongodbonazure]」を参照してください。

1. [Azure ポータル][azureportal]にログインします。

3. **[New]** > **[Data + Storage]** > **[Marketplace]** の順にクリックします。

	![VM Depot の選択のスクリーンショット][selectdepo]

2. 上部にある検索ボックスに「mongodb」と入力し、**[MongoDB v2.2.3 on Hardened Ubuntu 12.04 LTS]** を選択します。**[作成]** をクリックして続行します。

	![選択した MongoDB v2.2.3 on Hardened Ubuntu イメージのスクリーンショット][selectedimage]

	下部にある矢印をクリックして次の画面に進みます。

7. VM の **[ホスト名]**、管理者の **[ユーザー名]** と **[パスワード]**、および **[リソース グループ]** を構成します。続いて **[オプションの構成]** をクリックします。

	![VM 構成のスクリーンショット][vmconfig]

8. VM に追加のエンドポイントを設定します。この VM 上の MongoDB にアクセスするため、次の情報を使用して新しいエンドポイントを追加します。

	* 名前 – MongoDB
	* プロトコル – TCP
	* パブリック ポート – 27017
	* プライベート ポート – 27017

	MongoDB Web ポータルを公開するには、次の情報を使用してエンドポイントをもう 1 つ追加します。

	* 名前 – MongoDBWeb
	* プロトコル – TCP
	* パブリック ポート – 28017
	* プライベート ポート – 28017

	![エンドポイント構成のスクリーンショット][vmendpoint]

9. **[OK]** を 2 回クリックして **[作成]** をクリックすると、VM が作成されます。

	仮想マシンが作成されると、スタート画面に表示され、VM のブレードをクリックして開くことができます。Web ブラウザーを **http://&lt;YourVMDNSName&gt;.cloudapp.net:28017/** に開いて MongoDB が実行されていることを確認できます。ページの下部に、サービスに関する情報を示す次のようなログが表示されます。

		Fri Mar  7 18:57:16 [initandlisten] MongoDB starting : pid=1019 port=27017 dbpath=/var/lib/mongodb 64-bit host=localhost.localdomain
           18:57:16 [initandlisten] db version v2.2.3, pdfile version 4.5
           18:57:16 [initandlisten] git version: f570771a5d8a3846eb7586eaffcf4c2f4a96bf08
           18:57:16 [initandlisten] build info: Linux ip-10-2-29-40 2.6.21.7-2.ec2.v1.2.fc8xen #1 SMP Fri Nov 20 17:48:28 EST 2009 x86_64 BOOST_LIB_VERSION=1_49
           18:57:16 [initandlisten] options: { config: "/etc/mongodb.conf", dbpath: "/var/lib/mongodb", logappend: "true", logpath: "/var/log/mongodb/mongodb.log" }
           18:57:16 [initandlisten] journal dir=/var/lib/mongodb/journal
           18:57:16 [initandlisten] recover : no journal files present, no recovery needed
           18:57:17 [initandlisten] waiting for connections on port 27017

	ログでエラーが表示される場合は、[MongoDB のドキュメント][mongodocs]でトラブルシューティング手順を確認してください。


##モジュールのインストールとスキャフォールディングの生成

ここでは、開発環境で新しい Node アプリケーションを作成し、npm を使用してモジュール パッケージを追加します。タスク一覧アプリケーションの場合は、[Express] モジュールと [Mongoose] モジュールを使用します。Express モジュールは node の Model View Controller フレームワークを提供し、Mongoose は MongoDB と通信するためのドライバーです。

###express のインストールとスキャフォールディングの生成

1. コマンド ラインで、**tasklist** ディレクトリに移動します。**tasklist** ディレクトリがない場合は作成します。

> [AZURE.NOTE]このチュートリアルでは、**tasklist** フォルダーを参照します。パスのセマンティクスはオペレーティング システムによって異なるので、このフォルダーへの完全なパスは省略しています。このフォルダーは、ローカル ファイル システムのアクセスしやすい場所 (**~/node/tasklist** や **c:\node\tasklist** など) に作成してください。

2. 次のコマンドを入力して、express コマンドをインストールします。

	npm install express-generator -g

> [AZURE.NOTE]一部のオペレーティング システムで "-g" パラメーターを使用すると、"___Error: EPERM, chmod '/usr/local/bin/express'___" というエラーが表示され、管理者としてアカウントを実行することを要求される場合があります。このような場合は、`sudo` コマンドを使用して、より高い権限レベルで npm を実行します。

    このコマンドの出力は次のように表示されます。

		express-generator@4.0.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)

	> [AZURE.NOTE]express モジュールのインストール時に "-g" パラメーターを使用すると、モジュールはグローバルにインストールされます。これは、追加のパス情報を入力することなく ___express___ コマンドにアクセスして、Web アプリのスキャフォールディングを生成できるようにするためです。

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

3. **tasklist/bin/www** ファイルを **server.js** というファイル名で **tasklist** フォルダーにコピーします。App Service Web Apps は、Node.js アプリケーションのエントリ ポイントが **server.js** または **app.js** であることを想定します。**app.js** は既に存在しますがエントリ ポイントではないので、**server.js** を使用する必要があります。

4. **server.js** ファイルを変更して、次の行から "." 文字の 1 つを削除します。

		var app = require('../app');

	変更後の行は次のようになります。

		var app = require('./app');

	**server.js** (以前の **bin/www**) が現在は必須の **app.js** ファイルと同じフォルダーにあるので、このようにする必要があります。

###追加モジュールのインストール

**package.json** ファイルは、**express** コマンドで作成されるファイルの 1 つです。このファイルには、Express アプリケーションで必要な追加モジュールのリストが含まれます。このファイルは、後でこのアプリケーションを App Service Web Apps に展開するときに、アプリケーションのサポートのために Azure にインストールする必要があるモジュールを判断するために使用されます。

1. **tasklist** フォルダーで、**package.json** ファイルに記述されたモジュールを次のコマンドを使用してインストールします。

        npm install

    このコマンドの出力は次のように表示されます。

		debug@0.7.4 node_modules\debug

		cookie-parser@1.0.1 node_modules\cookie-parser
		├── cookie-signature@1.0.3
		└── cookie@0.1.0

		morgan@1.0.0 node_modules\morgan
		└── bytes@0.2.1

		body-parser@1.0.2 node_modules\body-parser
		├── qs@0.6.6
		├── raw-body@1.1.4 (bytes@0.3.0)
		└── type-is@1.1.0 (mime@1.2.11)

		express@4.0.0 node_modules\express
		├── methods@0.1.0
		├── parseurl@1.0.1
		├── merge-descriptors@0.0.2
		├── utils-merge@1.0.0
		├── escape-html@1.0.1
		├── cookie-signature@1.0.3
		├── fresh@0.2.2
		├── range-parser@1.0.0
		├── buffer-crc32@0.2.1
		├── qs@0.6.6
		├── cookie@0.1.0
		├── path-to-regexp@0.1.2
		├── send@0.2.0 (mime@1.2.11)
		├── type-is@1.0.0 (mime@1.2.11)
		├── accepts@1.0.0 (negotiator@0.3.0, mime@1.2.11)
		└── serve-static@1.0.1 (send@0.1.4)

		jade@1.3.1 node_modules\jade
		├── character-parser@1.2.0
		├── commander@2.1.0
		├── mkdirp@0.3.5
		├── monocle@1.1.51 (readdirp@0.2.5)
		├── constantinople@2.0.0 (uglify-js@2.4.13)
		├── with@3.0.0 (uglify-js@2.4.13)
		└── transformers@2.1.0 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)

	これによって、Express アプリケーションで使用される既定のモジュールがすべてインストールされます。

2. 次のコマンドを入力して、Mongoose モジュールをローカルにインストールし、これらのモジュールのエントリを **package.json** ファイルに保存します。

		npm install mongoose --save

	このコマンドの出力は次のように表示されます。

		mongoose@3.8.8 node_modules\mongoose
		├── regexp-clone@0.0.1
		├── sliced@0.0.5
		├── muri@0.3.1
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── mpromise@0.4.3
		├── ms@0.1.0
		├── mquery@0.5.3
		└── mongodb@1.3.23 (kerberos@0.0.3, bson@0.2.5)

    > [AZURE.NOTE]C++ bson パーサーのインストールに関するメッセージは無視してかまいません。

##node アプリケーションでの MongoDB の使用

ここでは、**express** コマンドで作成された基本的なアプリケーションを、タスクのモデルを格納する **task.js** ファイルを追加することによって拡張します。また、既存の **app.js** を変更し、このモデルを使用する新しい **tasklist.js** コントローラー ファイルを作成します。

### モデルの作成

1. **tasklist** ディレクトリ内に、**models** という名前の新しいディレクトリを作成します。

2. **models** ディレクトリ内に、**task.js** という名前の新しいファイルを作成します。このファイルには、アプリケーションで作成されるタスクのモデルが格納されます。

3. **task.js** ファイルの先頭に、必要なライブラリを参照する次のコードを追加します。

        var mongoose = require('mongoose'),
	        Schema = mongoose.Schema;

4. 次に、モデルを定義およびエクスポートするコードを追加します。このモデルは、MongoDB データベースの操作を実行するために使用されます。

        var TaskSchema = new Schema({
	        itemName      : String,
	        itemCategory  : String,
	        itemCompleted : { type: Boolean, default: false },
	        itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5. **task.js** ファイルを保存して閉じます。

###コントローラーの作成

1. **tasklist/routes** ディレクトリに **tasklist.js** という名前の新しいファイルを作成し、テキスト エディターで開きます。

2. 次のコードを **tasklist.js** に追加します。これによって、mongoose モジュールと **task.js** で定義された task モデルが読み込まれます。TaskList 関数は、**connection** 値に基づいて MongoDB サーバーへの接続を作成するために使用されます。

		var mongoose = require('mongoose'),
	        task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
  		  mongoose.connect(connection);
		}

2. **tasklist.js** ファイルへの内容の追加を続行し、**showTasks**、**addTask**、および **completeTasks** の各メソッドを追加します。

		TaskList.prototype = {
  		  showTasks: function(req, res) {
      	    task.find({itemCompleted: false}, function foundTasks(err, items) {
      		  res.render('index',{title: 'My ToDo List ', tasks: items})
    		});
  		  },

  		  addTask: function(req,res) {
    		var item = req.body.item;
    		newTask = new task();
    		newTask.itemName = item.name;
    		newTask.itemCategory = item.category;
    		newTask.save(function savedTask(err){
      		  if(err) {
      		    throw err;
      		  }
    	    });
    	  	res.redirect('/');
  		  },


  		  completeTask: function(req,res) {
    		var completedTasks = req.body;
    		for(taskId in completedTasks) {
      		  if(completedTasks[taskId]=='true') {
        		var conditions = { _id: taskId };
        		var updates = { itemCompleted: completedTasks[taskId] };
        		task.update(conditions, updates, function updatedTask(err) {
          		  if(err) {
          		    throw err;
          		  }
        		});
      		  }
    		}
    		res.redirect('/');
  		  }
		}

3. **tasklist.js** ファイルを保存します。

### app.js の変更

1. **tasklist** ディレクトリ内の **app.js** ファイルを、テキスト エディターで開きます。このファイルは、先ほど **express** コマンドを実行することによって作成されたものです。

2. **app.js** ファイルの先頭に次のコードを追加します。これによって、MongoDB サーバーの接続文字列を使用して **TaskList** が初期化されます。

        var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.MONGODB_URI);

	2 行目に注目してください。後で構成する環境変数にアクセスしています。この変数には、mongo インスタンスの接続情報が格納されます。開発目的で実行中のローカル mongo インスタンスがある場合は、この値を process.env.MONGODB_URI ではなく、一時的に "localhost" に設定することもできます。

3. 次の行を探します。

		app.use('/', routes);
		app.use('/users', users);

	上の 2 つの行を次のように置き換えます。

    	app.get('/', taskList.showTasks.bind(taskList));
    	app.post('/addtask', taskList.addTask.bind(taskList));
    	app.post('/completetask', taskList.completeTask.bind(taskList));

	これにより、**tasklist.js** に定義されている関数がルートとして追加されます。

4. **app.js** ファイルを保存します。

###index ビューの変更

1. **views** ディレクトリに移動し、テキスト エディターで **index.jade** ファイルを開きます。

2. **index.jade** ファイルの内容を次のコードに置き換えます。これにより、既存のタスクを表示するビューと、新しいタスクの追加とタスクの完了済みのマーク付けを実行するためのフォームを定義します。

		h1= title
		form(action="/completetask", method="post")
		  table(border="1")
		    tr
		      td Name
		      td Category
		      td Date
		      td Complete
		    each task in tasks
		      tr
		        td #{task.itemName}
		        td #{task.itemCategory}
		        - var day   = task.itemDate.getDate();
		        - var month = task.itemDate.getMonth() + 1;
		        - var year  = task.itemDate.getFullYear();
		        td #{month + "/" + day + "/" + year}
		        td
		          input(type="checkbox", name="#{task._id}", value="#{!task.itemCompleted}", checked=task.itemCompleted)
		  input(type="submit", value="Update tasks")
		hr
		form(action="/addtask", method="post")
		  table(border="1")
		    tr
		      td Item Name:
		      td
		        input(name="item[name]", type="textbox")
		    tr
		      td Item Category:
		      td
		        input(name="item[category]", type="textbox")
		  input(type="submit", value="Add item")

3. **index.jade** ファイルを保存して閉じます。

<!-- ##Run your application locally

To test the application on your local machine, perform the following steps:

1. From the command-line, change directories to the **tasklist** directory.

2. Set the MONGODB_URI environment variable on your development environment to point to the virtual machine hosting MongoDB. In the examples below, replace __mymongodb__ with your virtual machine name.

	On a Windows system, use the following to set the environment variable.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

	On an OS X or Linux-based system, use the following to set the environment variable.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks
		export MONGODB_URI

	This will instruct the application to connect to MongoDB on the __mymongodb.cloudapp.net__ virtual machine created earlier, and to use a DB named 'tasks'.

2. Use the following command to launch the application locally:

        node app.js

3. Open a web browser and navigate to http://localhost:3000. This should display a web page similar to the following:

    ![A webpage displaying an empty tasklist][node-mongo-finished]

4. Use the provided fields for **Item Name** and **Item Category** to enter information, and then click **Add item**.

    ![An image of the add item field with populated values.][node-mongo-add-item]

5. The page should update to display the item in the ToDo List table.

    ![An image of the new item in the list of tasks][node-mongo-list-items]

6. To complete a task, simply check the checkbox in the Complete column, and then click **Update tasks**. While there is no visual change after clicking **Update tasks**, the document entry in MongoDB has now been marked as completed.

7. To stop the node process, go to the command-line and press the **CTRL** and **C** keys. -->

##Azure へのアプリケーションのデプロイ

ここで説明する手順では、Azure コマンド ライン ツールを使用して新しい Azure App Service の Web アプリを作成し、Git を使用してアプリケーションを展開します。これらの手順を実行するには、Azure サブスクリプションが必要です。

> [AZURE.NOTE]これらの手順は、Azure ポータルを使用して実行することもできます。Azure ポータルを使用して Node.js アプリケーションを作成およびデプロイする手順については、「[Azure App Service での Node.js Web アプリの作成と デプロイ](web-sites-nodejs-develop-deploy-mac.md)」を参照してください。

> [AZURE.NOTE]初めて App Service Web アプリを作成した場合は、Azure ポータルを使用してこのアプリケーションを展開する必要があります。

###Azure コマンド ライン インターフェイス (Azure CLI) のインストール

Azure CLI では、Azure サービスの管理操作を実行できます。開発環境で Azure CLI のインストールと構成をまだ行っていない場合、手順については、[Azure CLI のインストールと構成](../xplat-cli-install.md)に関するページをご覧ください。

###App Service Web アプリの作成

1. コマンド ラインで、**tasklist** ディレクトリに移動します。

2. 新しい App Service Web アプリを作成するには、次のコマンドを使用します。"myuniqueappname" は作成する Web アプリの一意のサイト名に置き換えます。この値は、完成した Web アプリの URL の一部として使用されます。

		azure site create myuniqueappname --git

	Web アプリケーションが格納されるデータ センターを尋ねられます。現在の場所に地理的に近いデータセンターを選択します。

	`--git` パラメーターを指定すると、**tasklist** フォルダー内にローカルに Git リポジトリが作成されます (存在しない場合)。また、'azure' という名前の [Git リモート]も作成されます。これは、Azure にアプリケーションを発行するために使用されます。[iisnode.yml] ファイルが作成されます。このファイルには、ノード アプリケーションをホストするために、Azure によって使用される設定が格納されます。最後に、.gitignore ファイルも作成されます。このファイルは、node-modules フォルダーが .git に発行されないように除外します。

	> [AZURE.NOTE]既に Git リポジトリが含まれているディレクトリからこのコマンドを実行した場合、ディレクトリは再初期化されません。

	> [AZURE.NOTE]"--git" パラメーターを省略した場合でも、ディレクトリには Git リポジトリが含まれ、"azure" リモートが作成されます。

	このコマンドが完了すると、次のような出力が表示されます。**Created website at** で始まる行には、App Service Web アプリの URL が含まれています。

		info:   Executing command site create
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default web.config file
		info:   Creating a new web site
		info:   Created web site at  mongodbtasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure http://username@mongodbtasklist.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

	> [AZURE.NOTE> これがサブスクリプションで最初の App Service Web アプリである場合、ポータルを使用して Web アプリを作成するように指示するメッセージが表示されます。詳細は、「[Azure App Service での Node.js Web アプリの構築とデプロイ](web-sites-nodejs-develop-deploy-mac.md)」を参照してください。

###MONGODB_URI 環境変数の設定

このアプリケーションでは、MONGODB_URI 環境変数で MongoDB インスタンスの接続文字列を利用できると想定しています。Web アプリのこの値を設定するには、次のコマンドを実行します。

	azure site config add MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

これで、Web アプリの新しいアプリケーション設定が作成されます。この設定は、Web アプリによって読み取られる MONGODB_URI 環境変数を設定する際に使用されます。値 "mymongodb.cloudapp.net" を、MongoDB をインストールした仮想マシンの名前に置き換えます。

###アプリケーションの発行

1. ターミナル ウィンドウで、**tasklist** ディレクトリに移動します (現在のディレクトリがこのディレクトリではない場合)。

2. 次のコマンドを使用して、ローカル Git リポジトリにファイルを追加し、コミットします。

		git add .
		git commit -m "adding files"

3. 最新の Git リポジトリの変更内容を App Service Web アプリにプッシュする場合、ターゲット分岐は、Web サイトのコンテンツ用に使用されるので、**master** であることを指定する必要があります。

		git push azure master

	次のような出力が表示されます。展開時に、Azure によってすべての npm モジュールがダウンロードされます。

		Counting objects: 17, done.
		Delta compression using up to 8 threads.
		Compressing objects: 100% (13/13), done.
		Writing objects: 100% (17/17), 3.21 KiB, done.
		Total 17 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id 'ef276f3042'.
		remote: Preparing files for deployment.
		remote: Running NPM.
		...
		remote: Deploying Web.config to enable Node.js activation.
		remote: Deployment successful.
		To https://username@mongodbtasklist.azurewebsites.net/MongoDBTasklist.git
 		 * [new branch]      master -> master

4. プッシュ操作が完了したら、`azure site browse` コマンドで Web アプリに移動してアプリケーションを表示します。

##次のステップ

この記事の手順では、MongoDB を使用して情報を格納する方法を説明しましたが、Azure テーブル サービスを使用することもできます。詳細については、「[Azure テーブル サービスを使用する Node.js Web アプリ]」を参照してください。

MongoLab が提供するホステッド MongoDB インスタンスを使用する方法については、「[Azure での MongoLab アドオンを使用した MongoDB 対応の Node.js Web アプリの作成](store-mongolab-web-sites-nodejs-store-data-mongodb.md)」を参照してください。

MongoDB をセキュリティ保護する方法については、「[MongoDB Security (MongoDB セキュリティ)][mongosecurity]」を参照してください。

##その他のリソース

[Mac および Linux 用 Azure コマンド ライン ツール ][Build and deploy a Node.js web app in Azure App Service] [Azure App Service の GIT を使用する連続的な展開]

## 変更内容
* Web サイトから App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。
* 古いポータルから新しいポータルへの変更ガイドについては、「[プレビュー ポータル内の移動に関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)」を参照してください。

[mongosecurity]: http://docs.mongodb.org/manual/security/
[node]: http://nodejs.org
[ノード]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[for free]: /pricing/free-trial
[Git リモート]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Azure command-line tool for Mac and Linux]: ../virtual-machines-command-line-tools.md
[Azure Developer Center]: /develop/nodejs/
[Build and deploy a Node.js web app in Azure App Service]: web-sites-nodejs-develop-deploy-mac.md
[Azure App Service の GIT を使用する連続的な展開]: web-sites-publish-source-control.md
[Azure テーブル サービスを使用する Node.js Web アプリ]: storage-nodejs-use-table-storage-web-site.md
[node-mongo-finished]: ./media/web-sites-nodejs-store-data-mongodb/todo_list_empty.png
[node-mongo-express-results]: ./media/store-mongodb-web-sites-nodejs-use-mac/express_output.png
[node-mongo-add-item]: ./media/web-sites-nodejs-store-data-mongodb/todo_add_item.png
[node-mongo-list-items]: ./media/web-sites-nodejs-store-data-mongodb/todo_list_items.png
[download-publishing-settings]: ./media/store-mongodb-web-sites-nodejs-use-mac/azure-account-download-cli.png
[installguides]: http://docs.mongodb.org/manual/installation/
[azureportal]: https://portal.azure.com
[mongodocs]: http://docs.mongodb.org/manual/
[Azure CLI]: ../xplat-cli.md

[selectdepo]: ./media/web-sites-nodejs-store-data-mongodb/browsedepot.png
[selectedimage]: ./media/web-sites-nodejs-store-data-mongodb/selectimage.png
[selectstorage]: ./media/web-sites-nodejs-store-data-mongodb/storageaccount.png
[register]: ./media/web-sites-nodejs-store-data-mongodb/register.png
[myimage]: ./media/web-sites-nodejs-store-data-mongodb/myimages.png
[vmname]: ./media/web-sites-nodejs-store-data-mongodb/vmname.png
[vmconfig]: ./media/web-sites-nodejs-store-data-mongodb/vmconfig.png
[vmendpoint]: ./media/web-sites-nodejs-store-data-mongodb/endpoints.png
[mongodbonazure]: http://docs.mongodb.org/ecosystem/tutorial/install-mongodb-on-linux-in-azure/
 

<!-----HONumber=July15_HO4-->