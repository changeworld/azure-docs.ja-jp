<properties urlDisplayName="Website with MongoDB" pageTitle="仮想マシン上の MongoDB を使用した Node.js Web サイト - Azure チュートリアルl" metaKeywords="Azure チュートリアル MongoDB、MongoDB 格納データ、アクセス データ MongoDB ノード、Azure ノード アプリ" description="MongoDB を使用して、Azure でホストされる Node アプリケーションのデータを格納する方法やデータにアクセスする方法を示すチュートリアル。" metaCanonical="http://www.windowsazure.com/ja-jp/develop/nodejs/tutorials/website-with-mongodb-mongolab/" services="web-sites,virtual-machines" documentationCenter="nodejs" title="Node.js Web Application with Storage on MongoDB (Virtual Machine)" authors="larryfr"  solutions="" writer="" manager="wpickett" editor=""  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />


# 仮想マシン上の MongoDB を使用した Azure での Node.js アプリケーションの作成

このチュートリアルでは、Azure Virtual Machines でホストされる [MongoDB] を使用してデータを格納し、Azure Website でホストされる [ノード] アプリケーションからデータにアクセスする方法を示します。[MongoDB] は、広く普及している高性能のオープン ソース NoSQL データベースです。

学習内容:

* VM Depot から Ubuntu または MongoDB を実行する仮想マシンを設定する方法
* ノード アプリケーションから MongoDB にアクセスする方法
* Azure 用のクロスプラットフォーム ツールを使用して Azure Website を作成する方法

このチュートリアルでは、タスクを作成、取得、完了する機能を備えた、単純な Web ベースのタスク管理アプリケーションを作成します。タスクは MongoDB に格納されます。

> [WACOM.NOTE] このチュートリアルでは、仮想マシンにインストールした MongoDB のインスタンスを使用します。Mongolab が提供するホスト型 MongoDB インスタンスを使用する場合は次を参照してください。 <a href="/ja-jp/develop/nodejs/tutorials/website-with-mongodb-mongolab/">MongoLab アドオンを使用して Azure で MongoDB 対応の Node.js アプリケーションを作成する</a>。
 
このチュートリアルのプロジェクト ファイルは **tasklist** という名前のディレクトリに保存され、作成されるアプリケーションは次のようになります。

![A web page displaying an empty tasklist][node-mongo-finished]

> [WACOM.NOTE] 以下に示す手順の多くでは、コマンド ラインを使用します。これらの手順では、__Windows PowerShell__ (Windows) や __Bash__ (Unix Shell) など、お使いのオペレーティング システムのコマンド ラインを使用してください。OS X システムでは、ターミナル アプリケーションを使用してコマンド ラインにアクセスできます。

##前提条件

Node.js を使用するこのチュートリアルの手順では、開発環境に最新バージョンの [Node.js][node] が必要です。

また、アプリケーションを Azure Website にデプロイする際に使用するため、開発環境のコマンド ラインから [Git] を使用できる必要があります。

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##仮想マシンの作成

<!--This tutorial assumes you have created a virtual machine in Azure. After creating the virtual machine you need to install MongoDB on the virtual machine:

* To create a Linux virtual machine and install MongoDB, see [Installing MongoDB on a Linux Virtual machine].

After you have created the virtual machine in Azure and installed MongoDB, be sure to remember the DNS name of the virtual machine ("testlinuxvm.cloudapp.net", for example) and the external port for MongoDB that you specified in the endpoint.  You will need this information later in the tutorial.-->

新しい VM を作成してから [MongoDB のインストール ガイド][installguides]に従ってその VM に MongoDB をインストールすることはできますが、この作業の大部分はコミュニティによって既に実行されており、VM Depot で利用できます。次の手順では、MongoDB が既にインストールされて構成されている、VM Depot のイメージを使用する方法を示します。 

> [WACOM.NOTE] このチュートリアルで使用するコミュニティ イメージは、MongoDB データを OS ディスクに格納します。これはチュートリアルの目的には十分ですが、MongoDB データをデータ ディスクに格納するとパフォーマンスが向上します。データ ディスクなどの新しい VM を作成して MongoDB データをデータ ディスクに格納する手順については、 [inux on Azure への MongoDB のインストール][mongodbonazure] を参照してください。

1. [Azure 管理ポータル][azureportal] にログインし、 [Virtual Machines]、 [Images]、[VM Depot] の順に選択します。

	![screenshot of selecting VM Depot][selectdepo]

2. MongoDB が含まれているイメージを選択します。この例では、Ubuntu を選択して、Ubuntu Linux ディストリビューションに基づくイメージのみに一覧を絞り込みました。最終的には、MongoDB v2.2.3 on Hardened Ubuntu イメージを選択しました。

	![screenshot of selected mongodb v2.2.3 on hardened ubuntu image][selectedimage]

	> [WACOM.NOTE] イメージに関するすべての情報を表示するには、[項目表示] を必ず選択してください。イメージによっては、イメージを使用して VM を作成した後に追加の構成が必要になる場合があります。

	下部にある矢印をクリックして次の画面に進みます。

3. このイメージの VHD を保存するために使用するリージョンとストレージ アカウントを選択します。チェック マークをクリックして続行します。
	
	![screenshot of choose a storage account][selectstorage]

	> [WACOM.NOTE] これによって、指定したストレージ アカウントに VM Depot のイメージをコピーするコピー プロセスが開始されます。これには、長い時間 (15 分以上) がかかる場合があります。

4. イメージの状態が [登録を保留しています] に変わったら、[登録] を選択して、新しいイメージのフレンドリ名を入力します。チェック マークをクリックして続行します。

	![screenshot of registering an image][register]

5. イメージの状態が [利用可能] に変わったら、[+ 新規]、[Virtual Machine]、[ギャラリーから] の順に選択します。[イメージの選択] を求められたら、[マイ イメージ] を選択し、前の手順で作成したイメージを選択します。矢印をクリックして続行します。

	![screenshot of the image][myimage]

6. VM の名前、サイズ、ユーザー名を指定します。矢印をクリックして続行します。

	![screenshot of the vm name, user name, etc.][vmname]

	>[WACOM.NOTE] このチュートリアルでは、SSH を使用して VM にリモート接続する必要はありません。SSH での証明書の使用に慣れていない場合は、**[パスワードを使用する]** を選択してパスワードを指定します。
	>
	> Azure 上の Linux VM で SSH を使用する方法の詳細については、[How to use SSH with Linux on Azure （Azure 上の Linux における SSH の使用方法）][sshazure] を参照してください。

7. 新しい Cloud Service と既存の Cloud Service のどちらを使用するかを選択し、VM を作成するリージョンを選択します。矢印をクリックして続行します。

	![screenshot of the vm configuration][vmconfig]

8. VM に追加のエンドポイントを設定します。この VM 上の MongoDB にアクセスするため、次の情報を使用して新しいエンドポイントを追加します。

	* 名前 - MongoDB
	* プロトコル - TCP
	* パブリック ポート - 27017
	* プライベート ポート - 27017

	MongoDB Web ポータルを公開するには、次の情報を使用してエンドポイントをもう 1 つ追加します。

	* 名前 - MongoDBWeb
	* プロトコル - TCP
	* パブリック ポート - 28017
	* プライベート ポート - 28017
	
	最後に、チェック マークをオンにして仮想マシンを構成します。

	![screenshot of the endpoint configuration][vmendpoint]

9. 仮想マシンの状態が [実行中] に変わったら、Web ブラウザーを開いて http://&lt;YourVMDNSName&gt;.cloudapp.net:28017/ にアクセスし、MongoDB が実行されていることを確認できます。ページの下部に、サービスに関する情報を示す次のようなログが表示されます。

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

	> [WACOM.NOTE] このチュートリアルでは、「tasklist」フォルダーを参照します。パスのセマンティクスはオペレーティング システムによって異なるので、このフォルダーへの完全なパスは省略しています。このフォルダーは、ローカル ファイル システムのアクセスしやすい場所 (~/node/tasklist や c:\node\tasklist など) に作成してください。

2. 次のコマンドを入力して、express コマンドをインストールします。

	npm install express-generator -g
 
	> [WACOM.NOTE] 一部のオペレーティング システムで "-g" パラメーターを使用する場合、___Error のエラーが発生する場合があります。EPERM, chmod '/usr/local/bin/express' というエラーが表示され、管理者としてアカウントを実行することを要求されることがあります。このような場合は、sudo コマンドを使用して、より高い権限レベルで npm を実行します。

    このコマンドの出力は次のように表示されます。

		express-generator@4.0.0 C:\Users\username\AppData\Roaming\npm\node_modules\express-generator
		├── mkdirp@0.3.5
		└── commander@1.3.2 (keypress@0.1.0)                                                                         
 
	> [WACOM.NOTE] express モジュールのインストール時に "-g" パラメーターを使用すると、モジュールはグローバルにインストールされます。これは、追加のパス情報を入力することなく express コマンドにアクセスして、Web サイトのスキャフォールディングを生成できるようにするためです。

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

3. **tasklist/bin/www** ファイルを **server.js** というファイル名で **tasklist** フォルダーにコピーします。Azure Web サイトは、Node.js アプリケーションのエントリ ポイントが **server.js** または **app.js** であることを想定します。**app.js** は既に存在しますがエントリ ポイントではないので、**server.js** を使用する必要があります。

4. **server.js** ファイルを変更して、次の行から "." 文字の 1 つを削除します。

		var app = require('../app');

	変更後の行は次のようになります。

		var app = require('./app');

	**server.js** (以前の **bin/www**) が現在は必須の **app.js** ファイルと同じフォルダーにあるので、このようにする必要があります。

###追加モジュールのインストール

**package.json** ファイルは、**express** コマンドで作成されるファイルの 1 つです。このファイルには、Express アプリケーションで必要な追加モジュールのリストが含まれます。このファイルは、後でこのアプリケーションを Azure の Web サイトにデプロイするときに、アプリケーションのサポートのために Azure にインストールする必要があるモジュールを判断するために使用されます。
	
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

    > [WACOM.NOTE] C++ bson パーサーのインストールに関するメッセージは無視してかまいません。

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

2. 次のコードを **tasklist.js** ファイルに追加します。これによって、mongoose モジュールと **task.js** で定義された task モデルが読み込まれます。TaskList 関数は、**connection** 値に基づいて MongoDB サーバーへの接続を作成するために使用されます。

		var mongoose = require('mongoose'),
	        task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
  		  mongoose.connect(connection);
		}

2. Continue adding to the **tasklist.js** ファイルへの内容の追加を続行し、**showTasks**、**addTask**、**completeTasks** の各メソッドを追加します。

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

2. **index.jade** ファイルの内容を、以下のコードに置き換えます。これにより、既存のタスクを表示するビューと、新しいタスクの追加とタスクの完了済みのマーク付けを実行するためのフォームを定義します。

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

ローカル コンピューターでアプリケーションをテストするには、次の手順を実行します。

1. コマンド ラインで、**tasklist** ディレクトリに移動します。

2. MongoDB をホストする仮想マシンをポイントするように開発環境で MONGODB_URI 環境変数を設定します。次の例では、[mymongodb] を仮想マシンの名前に置き換えます。

	Windows のシステムでは、次を使用して環境変数を設定します。

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

	OS X または Linux ベースのシステムについては、次を使用して環境変数を設定します。.

		set MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks
		export MONGODB_URI

	これによって、アプリケーションを前の手順で作成した [mymongodb.cloudapp.net] 仮想マシン上の MongoDB に接続して、「tasks」という名前の DB を使用するように指示されます。

2. 次のコマンドを使用して、ローカルでアプリケーションを起動します。

        node app.js

3. Web ブラウザーを開いて、http://localhost:3000 にアクセスします。次のような Web ページが表示されます。

    ![A webpage displaying an empty tasklist][node-mongo-finished]

4.  表示された **Item Name** と **Item Category**のフィールドを使用して情報を入力し、**[Add item]** をクリックします。

    ![An image of the add item field with populated values.][node-mongo-add-item]

5. ページが更新され、ToDo List テーブルにアイテムが表示されるようになります。

    ![An image of the new item in the list of tasks][node-mongo-list-items]

6.  タスクを完了するには、[Complete] 列のチェック ボックスをオンにし、**[Update tasks]** をクリックします。**[Update tasks]** をクリックしても視覚的には何も変化しませんが、MongoDB 内のドキュメントのエントリは完了済みとマークされています。

7. ノード プロセスを停止するには、コマンド ラインで、**CTRL** キーを押しながら **C** キーを押します。-->

##Azure へのアプリケーションのデプロイ

ここで説明する手順では、Azure コマンド ライン ツールを使用して新しい Azure の Web サイトを作成し、Git を使用してアプリケーションをデプロイします。これらの手順を実行するには、Azure サブスクリプションが必要です。

> [WACOM.NOTE] これらの手順は、Azure ポータルを使用して実行することもできます。Azure ポータルを使用して Node.js アプリケーションをデプロイする手順については、次を参照してください。 <a href="/ja-jp/develop/nodejs/tutorials/create-a-website-(mac)/">Node.js アプリケーションの作成と Azure の Web サイトへのデプロイ</a>。

> [WACOM.NOTE] 初めて Azure の Web サイトを作成した場合は、Azure ポータルを使用してこのアプリケーションをデプロイする必要があります。

###Azure クロス プラットフォーム コマンド ライン インターフェイスのインストール

Azure クロス プラットフォーム コマンド ライン インターフェイス (xplat-cli) では、Azure サービスの管理操作を行うことができます。開発環境に xplat-cli をインストールおよび構成していない場合は、「[Install and configure the Azure Cross-Platform Command-Line Interface (Azure クロスプラットフォーム コマンド ライン インターフェイスのインストールと構成)][xplatcli]」で手順を確認してください。

###Azure Website の作成

1. コマンド ラインで、**tasklist** ディレクトリに移動します。

2. 新しい Azure Website を作成するには、次のコマンドを使用します。"myuniquesitename" は作成する Web サイトの一意のサイト名に置き換えます。この値は、完成した Web サイトの URL の一部として使用されます。

		azure site create myuniquesitename --git
		
	サイトが配置されるデータセンターを指定するよう求められます。現在の場所に地理的に近いデータセンターを選択します。
	
	`--git` パラメーターを指定すると、 **tasklist** フォルダー内にローカルに Git リポジトリが作成されます (存在しない場合)。また、'azure' という名前の [Git リモート]も作成されます。これは、Azure にアプリケーションを発行するために使用されます。[iisnode.yml] ファイルが作成されます。このファイルには、ノード アプリケーションをホストするために、Azure によって使用される設定が格納されます。最後に、.gitignore ファイルも作成されます。このファイルは、node-modules フォルダーが .git に発行されないように除外します。
	
	> [WACOM.NOTE] 既に Git リポジトリが含まれているディレクトリからこのコマンドを実行した場合、ディレクトリは再初期化されません。
	
	> [WACOM.NOTE] "--git" パラメーターを省略した場合でも、ディレクトリには Git リポジトリが含まれ、"azure" リモートが作成されます。
	
	このコマンドが完了すると、次のような出力が表示されます。**Created website at** で始まる行には、Web サイトの URL が含まれています。

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

	> [WACOM.NOTE> これがサブスクリプションで最初の Azure Website である場合、ポータルを使用して Web サイトを作成するように指示するメッセージが表示されます。詳細については、次を参照してください。 <a href="/ja-jp/develop/nodejs/tutorials/create-a-website-(mac)/">Node.js アプリケーションの作成と Azure Web サイトへのデプロイ</a>。

###MONGODB_URI 環境変数の設定

このアプリケーションでは、MONGODB_URI 環境変数で MongoDB インスタンスの接続文字列を利用できると想定しています。Web サイトのこの値を設定するには、次のコマンドを実行します。

	azure site config add MONGODB_URI=mongodb://mymongodb.cloudapp.net/tasks

これで、Web サイトの新しいアプリケーション設定が作成されます。この設定は、Web サイトによって読み取られる MONGODB_URI 環境変数を設定する際に使用されます。値 "mymongodb.cloudapp.net" を、MongoDB をインストールした仮想マシンの名前に置き換えます。

###アプリケーションの発行

1. [ターミナル] ウィンドウで、**tasklist** ディレクトリに移動します (現在のディレクトリがこのディレクトリではない場合)。

2. 次のコマンドを使用して、ローカル Git リポジトリにファイルを追加し、コミットします。

		git add .
		git commit -m "adding files"

3. 最新の Git リポジトリの変更内容を Azure の Web サイトに発行する場合、ターゲット分岐は、Web サイトのコンテンツ用に使用されるので、**master** であることを指定する必要があります。

		git push azure master
	
	You will see output similar to the following. As the deployment takes place Azure will download all npm modules. 

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
 
4. プッシュ操作が完了したら、`azure site browse` コマンドを使用して Web サイトに移動し、アプリケーションを表示します。

##次のステップ

この記事の手順では、MongoDB を使用して情報を格納する方法を説明しましたが、Azure テーブル サービスを使用することもできます。詳細については、「[Node.js Web Application with the Azure Table Service (Azure テーブル サービスを使用する Node.js Web アプリケーション)]」を参照してください。

MongoLab が提供するホスト型 MongoDB インスタンスを使用する方法については、[Create a Node.js Application on Azure with MongoDB using the MongoLab Add-On (MongoLab アドオンを使用して Azure で MongoDB 対応の Node.js アプリケーションを作成する)] を参照してください。(/ja-jp/develop/nodejs/tutorials/website-with-mongodb-mongolab/)。

MongoDB をセキュリティ保護する方法については、「[MongoDB Security (MongoDB セキュリティ)][mongosecurity]」を参照してください。

##その他のリソース

[Mac および Linux 用 Azure コマンド ライン ツール]    
[Node.js アプリケーションの作成と Azure の Web サイトへのデプロイ]    
[Git を使用した Azure の Web サイトへの発行]    

[mongosecurity]: http://docs.mongodb.org/manual/security/
[node]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[for free]: /ja-jp/pricing/free-trial
[Git remote]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Mac および Linux 用 Azure コマンド ライン ツール]: /ja-jp/develop/nodejs/how-to-guides/command-line-tools/
[Azure デベロッパー センター]: /ja-jp/develop/nodejs/
[Node.js アプリケーションの作成と Azure の Web サイトへのデプロイ]: /ja-jp/develop/nodejs/tutorials/create-a-website-(mac)/
[Git を使用した Azure の Web サイトへの発行]: /ja-jp/develop/nodejs/common-tasks/publishing-with-git/
[Linux 仮想マシンへの MongoDB のインストール]: /ja-jp/manage/linux/common-tasks/mongodb-on-a-linux-vm/
[Node.js Web Application with the Azure Table Service (Azure テーブル サービスを使用する Node.js Web アプリケーション)]: /ja-jp/develop/nodejs/tutorials/web-site-with-storage/
[node-mongo-finished]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_empty.png
[node-mongo-express-results]: ./media/store-mongodb-web-sites-nodejs-use-mac/express_output.png
[node-mongo-add-item]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_add_item.png
[node-mongo-list-items]: ./media/store-mongodb-web-sites-nodejs-use-mac/todo_list_items.png
[download-publishing-settings]: ./media/store-mongodb-web-sites-nodejs-use-mac/azure-account-download-cli.png
[installguides]: http://docs.mongodb.org/manual/installation/
[azureportal]: https://manage.windowsazure.com/
[mongodocs]: http://docs.mongodb.org/manual/
[xplatcli]: /ja-jp/documentation/articles/xplat-cli/

[selectdepo]: ./media/web-sites-nodejs-store-data-mongodb/browsedepot.png
[selectedimage]: ./media/web-sites-nodejs-store-data-mongodb/selectimage.png
[selectstorage]: ./media/web-sites-nodejs-store-data-mongodb/storageaccount.png
[register]: ./media/web-sites-nodejs-store-data-mongodb/register.png
[myimage]: ./media/web-sites-nodejs-store-data-mongodb/myimages.png
[vmname]: ./media/web-sites-nodejs-store-data-mongodb/vmname.png
[vmconfig]: ./media/web-sites-nodejs-store-data-mongodb/vmconfig.png
[vmendpoint]: ./media/web-sites-nodejs-store-data-mongodb/endpoints.png
[sshazure]: http://www.windowsazure.com/ja-jp/documentation/articles/linux-use-ssh-key/
[mongodbonazure]: http://docs.mongodb.org/ecosystem/tutorial/install-mongodb-on-linux-in-azure/ 

<!--HONumber=35.2-->
