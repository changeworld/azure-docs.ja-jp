<properties linkid="develop-nodejs-tutorials-web-site-with-mongodb-mongolab" urlDisplayName="MongoDB を使用した Web サイト" pageTitle="MongoLab 上の MongoDB を使用した Node.js Web サイト - Azure" metaKeywords="" description="MongoLab がホスティングする MongoDB インスタンスに接続する Node.js Azure の Web サイトを作成する方法について説明します。" metaCanonical="" services="web-sites,virtual-machines" documentationCenter="Node.js" title="MongoLab アドオンを使用して Azure で MongoDB 対応の Node.js アプリケーションを作成する" authors=""  solutions="" writer="" manager="" editor=""  />





# MongoLab アドオンを使用して Azure で MongoDB 対応の Node.js アプリケーションを作成する

<p><em>執筆: Eric Sedor (MongoLab)</em></p>

こんにちは、冒険家のみなさん。サービスとしての MongoDB にようこそ。このチュートリアルでは、次のことについて説明します。

1. [データベースの準備][provision] - Azure ストアの [MongoLab](http://mongolab.com) アドオンは、Azure クラウドでホスティングされ MongoLab のクラウド データベース プラットフォームで管理された MongoDB データベースを提供します。
1. [アプリケーションの作成][create] - タスク一覧を管理する単純な Node.js アプリケーションです。
1. [アプリケーションの展開][deploy] - 構成フックをいくつか結び付けて、コードを楽々と配置できるようにします。
1. [データベースの管理][manage] - 最後に、データの検索や視覚化、修正が簡単にできる MongoLab の Web ベースのデータベース管理ポータルを紹介します。

このチュートリアルに関して質問がある場合は、遠慮なく [support@mongolab.com](mailto:support@mongolab.com) までメールをお送りください。

作業を続行する前に、次のソフトウェアがインストールされていることを確認してください。

* [Node.js] Version 0.8.14+

* [Git]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## クイック スタート
Azure ストアにある程度慣れている場合は、このセクションを使ってクイック スタートを目指してください。そうでない場合は、以下の「[データベースの準備][provision]」に進んでください。
 
1. Azure ストアを開きます。  
![ストア][button-store]
1. [MongoLab アドオン] をクリックします。  
![MongoLab][entry-mongolab]
1. アドオン一覧で MongoLab アドオンをクリックし、**[接続文字列]** をクリックします。  
![ConnectionInfoButton][button-connectioninfo]  
1. MONGOLAB_URI をクリップボードにコピーします。  
![ConnectionInfoScreen][screen-connectioninfo]  
**この URI は、データベース ユーザー名とパスワードを含んでいます。機密情報として扱い、他人と共有しないでください。**
1. Azure Web アプリケーションの [構成] メニューの [接続文字列] ボックスに値を追加します。  
![WebSiteConnectionStrings][focus-website-connectinfo]
1. **[名前]** に「MONGOLAB\_URI」と入力します。
1. **[値]** に、先にコピーした接続文字列を貼り付けます。
1. [種類] ボックスの一覧の **[カスタム]** をクリックします (既定値の **SQLAzure** の代わりに)。
1. `npm install mongoose` を実行して、Mongoose (MongoDB ノード ドライバー) を取得します。
1. 環境変数から MongoLab 接続 URI を取得して接続するフックをコードに記述します。

        var mongoose = require('mongoose');  
 		...
 		var connectionString = process.env.CUSTOMCONNSTR_MONGOLAB_URI
 		...
 		mongoose.connect(connectionString);

注: Azure では、宣言された元の接続文字列名に **CUSTOMCONNSTR\_** というプレフィックスが追加されます。そのため、コードでは **MONGOLAB\_URI** ではなく **CUSTOMCONNSTR\_MONGOLAB\_URI.** になっています。

では完全なチュートリアルに進みましょう...

<h2><a name="provision"></a>データベースの準備</h2>

[WACOM.INCLUDE [howto-provision-mongolab](../includes/howto-provision-mongolab.md)]

<h2><a name="create"></a>アプリケーションの作成</h2>

ここでは、開発環境をセットアップし、Node.js、Express、および MongoDB を使用して、基本的なタスク一覧 Web アプリケーションのコードを作成します。[Express] は node の View Controller フレームワークを提供し、[Mongoose] は node で MongoDB と通信するためのドライバーです。

### セットアップ

#### スキャフォールディングの生成とモジュールのインストール

1. コマンド ラインで、**tasklist** ディレクトリを作成し、このディレクトリに移動します。これがプロジェクト ディレクトリになります。
2. 次のコマンドを入力して、express をインストールします。

		npm install express -g
 
	`-g` はグローバル モードを示します。ディレクトリ パスを指定せずに <strong>express</strong> モジュールを利用できるようにするために使用します。<strong>Error: EPERM, chmod '/usr/local/bin/express'</strong> が表示された場合は、<strong>sudo</strong> を使用して、より高い権限レベルで npm を実行します。

    このコマンドの出力は次のように表示されます。

		express@3.3.4 C:\Users\larryfr\AppData\Roaming\npm\node_modules\express
		├── methods@0.0.1
		├── fresh@0.1.0
		├── cookie-signature@1.0.1
		├── range-parser@0.0.4
		├── buffer-crc32@0.2.1
		├── cookie@0.1.0
		├── debug@0.7.2
		├── mkdirp@0.3.5
		├── commander@1.2.0 (keypress@0.1.0)
		├── send@0.1.3 (mime@1.2.9)
		└── connect@2.8.4 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, formidable@1.0.14)
 
3. このアプリケーションで使用するスキャフォールディングを作成するには、**express** コマンドを使用します。

    express

	このコマンドの出力は次のように表示されます。

		create : .
   		create : ./package.json
   		create : ./app.js
   		create : ./public
   		create : ./public/javascripts
   		create : ./public/images
   		create : ./public/stylesheets
   		create : ./public/stylesheets/style.css
   		create : ./routes
   		create : ./routes/index.js
   		create : ./views
   		create : ./views/layout.jade
   		create : ./views/index.jade
		
   		dont forget to install dependencies:
   		$ cd . && npm install

	このコマンドが完了すると、**tasklist** ディレクトリ内に複数の新しいディレクトリやファイルが作成されています。
	
4. **package.json** ファイルに記述されたモジュールをインストールするには、次のコマンドを入力します。

        npm install

    このコマンドの出力は次のように表示されます。

		express@3.3.4 node_modules\express
		├── methods@0.0.1
		├── fresh@0.1.0
		├── range-parser@0.0.4
		├── cookie-signature@1.0.1
		├── buffer-crc32@0.2.1
		├── cookie@0.1.0
		├── debug@0.7.2
		├── mkdirp@0.3.5
		├── commander@1.2.0 (keypress@0.1.0)
		├── send@0.1.3 (mime@1.2.9)
		└── connect@2.8.4 (uid2@0.0.2, pause@0.0.1, qs@0.6.5, bytes@0.2.0, formidable@1.0.14)

		jade@0.33.0 node_modules\jade
		├── character-parser@1.0.2
		├── mkdirp@0.3.5
		├── commander@1.2.0 (keypress@0.1.0)
		├── with@1.1.0 (uglify-js@2.3.6)
		├── constantinople@1.0.1 (uglify-js@2.3.6)
		├── transformers@2.0.1 (promise@2.0.0, css@1.0.8, uglify-js@2.2.5)
		└── monocle@0.1.48 (readdirp@0.2.5)

	**package.json** ファイルは、**express** コマンドで作成されるファイルの 1 つです。このファイルには、Express アプリケーションで必要な追加モジュールのリストが含まれます。このファイルは、後でこのアプリケーションを Azure の Web サイトに展開するときに、アプリケーションのサポートのために Azure にインストールする必要があるモジュールを判断するために使用されます。

5. 次のコマンドを入力して、Mongoose モジュールをローカルにインストールし、これらのモジュールのエントリを **package.json** ファイルに保存します。

		npm install mongoose --save

	このコマンドの出力は次のように表示されます。

		mongoose@3.6.15 node_modules\mongoose
		├── regexp-clone@0.0.1
		├── sliced@0.0.3
		├── muri@0.3.1
		├── hooks@0.2.1
		├── mpath@0.1.1
		├── ms@0.1.0
		├── mpromise@0.2.1 (sliced@0.0.4)
		└── mongodb@1.3.11 (bson@0.1.9, kerberos@0.0.3)

    C++ bson パーサーのインストールに関するメッセージは無視してかまいません。
	
### コード

これで環境とスキャフォールディングの準備ができたので、**express** コマンドで作成された基本的なアプリケーションを、タスクのモデルを格納する **task.js** ファイルを追加することによって拡張します。また、既存の **app.js** を変更し、このモデルを使用する新しい **tasklist.js** コントローラー ファイルを作成します。

#### モデルの作成

1. **tasklist** ディレクトリ内に、**models** という名前の新しいディレクトリを作成します。

2. **models** ディレクトリ内に、**task.js** という名前の新しいファイルを作成します。このファイルには、アプリケーションで作成されるタスクのモデルが格納されます。

3. 次のコードを **task.js** ファイルに追加します。

        var mongoose = require('mongoose')
	      , Schema = mongoose.Schema;

        var TaskSchema = new Schema({
	        itemName      : String
	      , itemCategory  : String
	      , itemCompleted : { type: Boolean, default: false }
	      , itemDate      : { type: Date, default: Date.now }
        });

        module.exports = mongoose.model('TaskModel', TaskSchema);

5. **task.js** ファイルを保存して閉じます。

#### コントローラーの作成

1. **tasklist/routes** ディレクトリに **tasklist.js** という名前の新しいファイルを作成し、テキスト エディターで開きます。

2. 次のコードを **tasklist.js** に追加します。これによって、mongoose モジュールと **task.js** で定義された task モデルが読み込まれます。TaskList 関数は、**connection** 値に基づいて MongoDB サーバーへの接続を作成するために使用されます。また、**showTasks**、**addTask**、**completeTasks** の各メソッドを提供します。

		var mongoose = require('mongoose')
	      , task = require('../models/task.js');

		module.exports = TaskList;

		function TaskList(connection) {
  		  mongoose.connect(connection);
		}

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

#### index ビューの変更

1. **views** ディレクトリに移動し、テキスト エディターで **index.jade** ファイルを開きます。

2. **index.jade** ファイルの内容を次のコードに置き換えます。これにより、既存のタスクを表示するビューと、新しいタスクの追加とタスクの完了済みのマーク付けを実行するためのフォームを定義します。

		h1 #{title}
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

#### app.js の置き換え

1. **tasklist** ディレクトリ内の **app.js** ファイルを、テキスト エディターで開きます。このファイルは、先ほど **express** コマンドを実行することによって作成されたものです。
1. **app.js** ファイルの先頭に次のコードを追加します。これによって、MongoDB サーバーの接続文字列を使用して **TaskList** が初期化されます。

		var TaskList = require('./routes/tasklist');
		var taskList = new TaskList(process.env.CUSTOMCONNSTR_MONGOLAB_URI);

 	2 行目に注目してください。後で構成する環境変数にアクセスしています。この変数には、mongo インスタンスの接続情報が格納されます。開発目的で実行中のローカル mongo インスタンスがある場合は、この値を `process.env.CUSTOMCONNSTR_MONGOLAB_URI` ではなく、一時的に "localhost" に設定することもできます。

2. `app.get` で始まる行を探し、次の行に置き換えます。

		app.get('/', taskList.showTasks.bind(taskList));
		app.post('/addtask', taskList.addTask.bind(taskList));
		app.post('/completetask', taskList.completeTask.bind(taskList));

	これにより、**tasklist.js** に定義されている関数がルートとして追加されます。

4. **app.js** ファイルを保存します。

<h2><a name="deploy"></a>アプリケーションの展開</h2>

アプリケーションの開発が済んだため、今度はアプリケーションをホスティングする Azure の Web サイトを作成し、その Web サイトを構成して、コードを展開します。このセクションで中心になるのは、MongoDB 接続文字列 (URI) の使用法です。Web サイトではこの URI を設定した環境変数を構成して、コードから URI を分離します。データベースに接続する資格情報を含むため、URI は機密情報として扱う必要があります。

ここで説明する手順では、Azure コマンド ライン ツールを使用して新しい Azure の Web サイトを作成し、Git を使用してアプリケーションを展開します。これらの手順を実行するには、Azure サブスクリプションが必要です。

### Mac および Linux 用 Azure コマンド ライン ツールのインストール

コマンド ライン ツールをインストールするには、次のコマンドを使用します。
	
	npm install azure-cli -g

<a href="/ja-jp/develop/nodejs/">Azure デベロッパー センター</a>から既に <strong>Azure SDK for Node.js</strong> をインストールしている場合は、コマンド ライン ツールもインストールされています。詳細については、「<a href="/ja-jp/develop/nodejs/how-to-guides/command-line-tools/">Mac および Linux 用 Azure コマンド ライン ツール</a>」を参照してください。

Azure コマンド ライン ツールは、主に Mac および Linux ユーザー向けに作成されていますが、Node.js に基づいているため、Node を実行できる任意のシステムで動作します。

### 発行の設定をインポートする

Azure でコマンド ライン ツールを使用する前に、自分のサブスクリプションに関する情報が含まれているファイルをダウンロードする必要があります。このファイルをダウンロードしてインポートするには、次のステップを実行します。

1. コマンド ラインで、次のコマンドを入力してブラウザーを起動し、ダウンロード ページに移動します。ログインを求められた場合は、サブスクリプションに関連付けられたアカウントを使ってログインします。

		azure account download
	
	![ダウンロード ページ][download-publishing-settings]
	
	ファイルのダウンロードが自動的に開始されます。ダウンロードが開始されない場合は、ページの先頭にあるリンクをクリックして、手動でファイルをダウンロードできます。

3. ファイルのダウンロードが完了したら、次のコマンドを使用して設定をインポートします。

		azure account import <path-to-file>
		
	そのためには、前の手順でダウンロードした発行設定ファイルのパスとファイル名を指定します。コマンドが完了すると、次のような出力が表示されます。
	
		info:   Executing command account import
		info:   Found subscription: subscriptionname
		info:   Setting default subscription to: subscriptionname
		warn:   The '/Users/user1/.azure/publishSettings.xml' file contains sensitive information.
		warn:   Remember to delete it now that it has been imported.
		info:   Account publish settings imported successfully
		info:   account import command OK


4. インポートが完了したら、不要になった発行設定ファイルを削除してください。このファイルには、Azure サブスクリプションの機密情報が含まれているからです。

### 新しい Web サイトの作成とコードのプッシュ

Azure での Web サイトの作成は簡単です。初めて Azure の Web サイトを作成する場合は、ポータルを使用する必要があります。既に 1 つ以上の Web サイトを作成している場合は、ステップ 7. に進んでください。

1. Azure ポータルで **[新規]** をクリックします。  
![新規][button-new]
1. **[コンピューティング]、[Web サイト]、[簡易作成]** の順に選択します。
![CreateSite][screen-mongolab-newwebsite]
1. URL のプレフィックスを入力します。好みの名前を選択します。ただし、重複した名前は使用できません ('mymongoapp' は使用できない可能性があります)。
1. **[Web サイトの作成]** をクリックします。
1. Web サイトの作成が完了したら、Web サイト一覧で、作成した Web サイトの名前をクリックします。Web サイトのダッシュボードが表示されます。  
![WebSiteDashboard][screen-mongolab-websitedashboard]
1. **[概要]** の **[Git 発行の設定]** をクリックし、設定する git ユーザー名とパスワードを入力します。このパスワードは、Web サイトにプッシュするときに使用します (ステップ 9.)。
1. 前の手順で Web サイトを作成した場合、次のコマンドでプロセスを完了します。ただし、既に 1 つ以上の Azure の Web サイトがある場合、前の手順をスキップして、次の同じコマンドを使用して新しい Web サイトを作成できます。**tasklist** プロジェクト ディレクトリから、次のコマンドを実行します。

		azure site create myuniquesitename --git  
	'myuniquesitename' は作成する Web サイトの一意のサイト名に置き換えます。このコマンドで Web サイトが作成された場合、サイトが配置されるデータセンターを指定するよう求められます。一意の名前を指定し、使用する MongoLab データベースに地理的に近いデータセンターを選択します。
	
	`--git` パラメーターを指定すると、次のものが作成されます。
	A. **tasklist** フォルダー内のローカル Git リポジトリ (存在しない場合)。
	A. 'azure' という名前の [Git リモート]。アプリケーションを Azure に発行するために使用されます。
	A. [iisnode.yml] ファイル。このファイルには、ノード アプリケーションをホストするために、Azure によって使用される設定が格納されます。
	A. .gitignore ファイル。node-modules フォルダーが .git に発行されることを防止します。
	  
	このコマンドが完了すると、次のような出力が表示されます。**Created web site at** で始まる行には、Web サイトの URL が含まれています。

		info:   Executing command site create
		info:   Using location southcentraluswebspace
		info:   Executing `git init`
		info:   Creating default web.config file
		info:   Creating a new web site
		info:   Created web site at  mongodbtasklist.azurewebsites.net
		info:   Initializing repository
		info:   Repository initialized
		info:   Executing `git remote add azure http://gitusername@myuniquesitename.azurewebsites.net/mongodbtasklist.git`
		info:   site create command OK

1. 次のコマンドを使用して、自分のローカル Git リポジトリにファイルを追加し、コミットします。

		git add .
		git commit -m "adding files"

1. コードをプッシュします。

		git push azure master  
	最新の Git リポジトリの変更内容を Azure の Web サイトに発行する場合、ターゲット分岐は、Web サイトのコンテンツ用に使用されるので、**master** であることを指定する必要があります。パスワードの入力を求められた場合は、前の Web サイトで git の発行を設定するときに作成したパスワードを入力します。
	
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
 
あともう少しで終了です。

### 環境の構成
コード内の process.env.CUSTOMCONNSTR\_MONGOLAB\_URI を覚えていますか。この環境変数に、MongoLab データベースのプロビジョニング中に、Azure に対して指定された値を入力できます。

#### MongoLab 接続文字列を取得します。

[WACOM.INCLUDE [howto-get-connectioninfo-mongolab](../includes/howto-get-connectioninfo-mongolab.md)]

#### Web サイトの環境変数に接続文字列を追加します。

[WACOM.INCLUDE [howto-save-connectioninfo-mongolab](../includes/howto-save-connectioninfo-mongolab.md)]

## 成功です。

プロジェクト ディレクトリから `azure site browse` を実行して自動的にブラウザーを開くか、ブラウザーを開いて、手動で Web サイト URL (myuniquesite.azurewebsites.net) に移動します。

![空のタスク一覧が表示されている Web ページ][node-mongo-finished]

<h2><a name="manage"></a>データベースの管理</h2>

[WACOM.INCLUDE [howto-access-mongolab-ui](../includes/howto-access-mongolab-ui.md)]

ご利用ありがとうございます。これで、MongoLab がホスティングする MongoDB データベースに支えられた Node.js アプリケーションが公開されました。MongoLab データベースを利用していて、データベースに関する疑問または不明点がある場合や、MongoDB や node ドライバーそのものに関するヘルプが必要な場合は、[support@mongolab.com](mailto:support@mongolab.com) にお問い合わせください。それではお元気で。



[screen-mongolab-websitedashboard]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-websitedashboard.png
[screen-mongolab-newwebsite]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/screen-mongolab-newwebsite.png
[button-new]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-new.png
[button-store]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-store.png
[entry-mongolab]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/entry-mongolab.png 
[button-connectioninfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/button-connectioninfo.png
[screen-connectioninfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/dialog-mongolab_connectioninfo.png
[focus-website-connectinfo]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
[provision]: #provision
[create]: #create
[deploy]: #deploy
[manage]: #manage
[Node.js]: http://nodejs.org
[MongoDB]: http://www.mongodb.org
[Git]: http://git-scm.com
[Express]: http://expressjs.com
[Mongoose]: http://mongoosejs.com
[無料評価版]: /ja-jp/pricing/free-trial
[Git リモート]: http://git-scm.com/docs/git-remote
[azure-sdk-for-node]: https://github.com/WindowsAzure/azure-sdk-for-node
[iisnode.yml]: https://github.com/WindowsAzure/iisnode/blob/master/src/samples/configuration/iisnode.yml
[Mac および Linux 用 Azure コマンド ライン ツール]: /ja-jp/develop/nodejs/how-to-guides/command-line-tools/
[Azure デベロッパー センター]: /ja-jp/develop/nodejs/
[Node.js アプリケーションの作成と Azure の Web サイトへの展開]: /ja-jp/develop/nodejs/tutorials/create-a-website-(mac)/
[Git を使用した Azure の Web サイトへの発行]: /ja-jp/develop/nodejs/common-tasks/publishing-with-git/
[MongoLab]: http://mongolab.com
[MongoDB (仮想マシン) のストレージを使用する Node.js Web アプリケーション]: /ja-jp/develop/nodejs/tutorials/website-with-mongodb-(mac)/
[node-mongo-finished]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/todo_list_noframe.png
[node-mongo-express-results]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/express_output.png
[download-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azure-account-download-cli.png
[import-publishing-settings]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/azureimport.png
[mongolab-create]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-create.png
[mongolab-view]: ./media/store-mongolab-web-sites-nodejs-store-data-mongodb/mongolab-view.png




