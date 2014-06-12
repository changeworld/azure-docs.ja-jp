<properties linkid="develop-nodejs-tutorials-web-site-with-sql-database" urlDisplayName="SQL データベースを使用する Web サイト" pageTitle="SQL データベースを使用する Node.js Web サイト - Azure チュートリアル" metaKeywords="" description="SQL データベースにアクセスする Node.js Web サイトを作成し、Azure にデプロイする方法を説明します。" metaCanonical="" services="web-sites,sql-database" documentationCenter="Node.js" title="Azure SQL データベースを使用する Node.js Web アプリケーション" authors=""  solutions="" writer="" manager="" editor=""  />





# Azure SQL データベースを使用する Node.js Web アプリケーション

このチュートリアルでは、Azure データ管理で提供される SQL データベースを使用して、Azure でホストされる[ノード] アプリケーションのデータを格納する方法やデータにアクセスする方法を示します。このチュートリアルは、ノードおよび [Git] を使用した経験があることを前提としています。

学習内容: 

* Azure の管理ポータルを使用して Azure の Web サイトおよび SQL データベースを作成する方法

* npm (ノード パッケージ マネージャー) を使用してノード モジュールをインストールする方法

* node-sqlserver モジュールを使用して SQL データベースを操作する方法

* アプリ設定を使用してアプリケーションの実行時の値を指定する方法

このチュートリアルでは、タスクを作成、取得、完了する機能を備えた、単純な Web ベースのタスク管理アプリケーションを作成します。タスクは SQL データベースに格納されます。
 
このチュートリアルのプロジェクト ファイルは **tasklist** という名前のディレクトリに保存され、作成されるアプリケーションは次のようになります。

![空のタスク一覧が表示されている Web ページ][node-sql-finished]

<div class="dev-callout">
<b>メモ</b>
<p>このチュートリアルで使用する Microsoft Driver for Node.JS for SQL Server は現在、プレビュー リリースとして利用することができ、Microsoft Windows および Azure オペレーティング システムでのみ利用可能なランタイム コンポーネントに依存しています。</p>
</div>

<div class="dev-callout">
<strong>メモ</strong>
<p>このチュートリアルでは、<strong>tasklist</strong> フォルダーを参照します。パスのセマンティクスはオペレーティング システムによって異なるので、このフォルダーへの完全なパスは省略しています。このフォルダーは、ローカル ファイル システムのアクセスしやすい場所 (<strong>~/node/tasklist</strong> や <strong>c:\node\tasklist</strong></p> など) に作成してください。
</div>

<div class="dev-callout">
<strong>メモ</strong>
<p>以下に示す手順の多くでは、コマンド ラインを使用します。これらの手順では、<strong>cmd.exe</strong> (Windows) や <strong>Bash</strong> (Unix Shell) など、お使いのオペレーティング システムのコマンド ラインを使用してください。OS X システムでは、ターミナル アプリケーションを使用してコマンド ラインにアクセスできます。</p>
</div>

##前提条件

この記事の手順を実行する前に、次のソフトウェアがインストールされていることを確認してください。

* [node] Version 0.6.14 以上

* [Git]

* Microsoft SQL Server Native Client ライブラリ: [Microsoft SQL Server 2012 Feature Pack] の一部として入手できます。

* テキスト エディター

* Web ブラウザー

<!--div chunk="../../Shared/Chunks/create-account-and-websites-note.md" /-->

##データベースを使用する Web サイトの作成

Azure の Web サイトと SQL データベースを作成するには、次の手順に従います。

1. [Azure の管理ポータル][management-portal]にログインします。
2. ポータルの左下にある **[新規]** アイコンをクリックします。

	![新しい Azure の Web サイトの作成][new-website]

3. **[Web サイト]** をクリックし、**[カスタム作成]** をクリックします。

	![新しい Web サイトのカスタム作成][custom-create]

	**[URL]** ボックスに値を入力し、**[データベース]** ボックスの一覧の **[新しい SQL データベースを作成する]** を選択して、**[リージョン]** ボックスの一覧で Web サイトのデータ センターを選択します。ダイアログの下部にある矢印をクリックします。

	![Web サイトの詳細の入力][website-details-sqlazure]

4. **[名前]** ボックスにデータベースの名前を入力し、**[エディション]** [(WEB または BUSINESS)][sql-database-editions] を選択して、データベースの **[最大サイズ]** を選択します。**[照合順序]** を選択し、**[新しい SQL データベース サーバー]** を選択します。ダイアログの下部にある矢印をクリックします。

	![SQL データベースの設定の指定][database-settings]

5. 管理者の名前とパスワードを入力 (およびパスワードを確認) し、新しい SQL データベース サーバーを作成するリージョンを選択して、**[Azure サービスにサーバーへのアクセスを許可します]** チェック ボックスをオンにします。

	![新しい SQL データベース サーバーの作成][create-server]

	Web サイトが作成されると、"**Web サイト '[SITENAME]' の作成が正常に完了しました**" というテキストが表示されます。これで、Git 発行を有効にする準備ができました。

6. Web サイトの一覧に表示されている Web サイトの名前をクリックして、Web サイトのクイック スタート ダッシュボードを開きます。

	![Web サイトのダッシュボードを開く][go-to-dashboard]


7. クイック スタート ページの下部で、**[Git 発行の設定]** をクリックします。

	![Git 発行の設定][setup-git-publishing]

8. Git 発行を有効にするには、ユーザー名とパスワードを指定する必要があります。作成するユーザー名とパスワードはメモしておいてください (Git リポジトリを設定したことがある場合は、この手順をスキップできます)。

	![発行資格情報の作成][portal-git-username-password]

	リポジトリの設定にかかる時間はわずかです。


9. リポジトリの準備ができると、アプリケーション ファイルをリポジトリにプッシュするための手順が表示されます。これらの手順は後で必要になるため、メモしておいてください。

	![Git の手順][git-instructions]

##SQL データベース接続情報を取得する

Azure の Web サイトで実行されている SQL データベース インスタンスに接続するには、接続情報が必要になります。SQL データベースの接続情報を取得するには、次の手順に従います。

1. Azure の管理ポータルで、**[リンク済みリソース]** をクリックし、目的のデータベース名をクリックします。

	![リンク済みリソース][linked-resources]

2. **[接続文字列の表示]** をクリックします。

	![接続文字列][connection-string]
	
3. 表示されたダイアログの **[ODBC]** セクションから、後で必要になる接続文字列をメモします。

##タスク テーブルの設計

tasklist アプリケーションのアイテムを格納するために使用するデータベース テーブルを作成するには、次の手順を実行します。

1. Azure の管理ポータルで、お使いの SQL データベースを選択し、ページの下部の **[管理]** をクリックします。現在の IP がファイアウォール ルールに含まれていないことを示すメッセージが表示された場合は、**[OK]** をクリックして IP アドレスを追加します。

	![[管理] ボタン][sql-azure-manage]

2. 先ほどデータベース サーバーを作成するときに選択したログイン名とパスワードを使用してログインします。

	![データベース管理のログイン][sql-azure-login]

3. ページの左下にある **[設計]** を選択し、**[新しいテーブル]** を選択します。

	![新しいテーブル][sql-new-table]

4. **[テーブル名]** として「tasks」と入力し、**[ID]** 列の **[ID?]** チェック ボックスをオンにします。

	![テーブル名を tasks に設定し、ID をオンに設定][table-name-identity]

5. **Column1** を **name** に変更し、**Column2** を **category** に変更します。**[列の追加]** をクリックして 2 つの新しい列を追加します。最初の新しい列は **created** という名前で、型は **date** です。2 番目の新しい列は **completed** という名前で、型は **bit** です。どちらの列も **[必須?]** をオンにする必要があります。

	![完成したテーブルの設計][completed-table]

6. **[保存]** をクリックしてテーブルに対する変更内容を保存します。これで SQL データベース管理ページを閉じてかまいません。

##モジュールのインストールとスキャフォールディングの生成

ここでは、新しい Node アプリケーションを作成し、npm を使用してモジュール パッケージを追加します。タスク一覧アプリケーションの場合は、[express] モジュールと [node-sqlserver] モジュールを使用します。Express モジュールは node の Model View Controller フレームワークを提供し、node-sqlserver モジュールは Azure SQL データベースへの接続を提供します。

###express のインストールとスキャフォールディングの生成

1. コマンド ラインで、**tasklist** ディレクトリに移動します。**tasklist** ディレクトリがない場合は作成します。

2. 次のコマンドを入力して、express をインストールします。

		npm install express -g

    <div class="dev-callout">
	<strong>注</strong>
	<p>一部のオペレーティング システムで "-g" パラメーターを使用する場合、"<strong>Error: EPERM, chmod '/usr/local/bin/express'</strong>" というエラーが表示され、管理者としてアカウントを実行することを要求されることがあります。このような場合は、<strong>sudo</strong> コマンドを使用して、より高い権限レベルで npm を実行します。</p>
	</div>

    このコマンドの出力は次のように表示されます。

		express@2.5.9 /usr/local/lib/node_modules/express
		├── mime@1.2.4
		├── mkdirp@0.3.0
		├── qs@0.4.2
		└── connect@1.8.7

	<div class="dev-callout">
	<strong>注</strong>
	<p>express モジュールのインストール時に "-g" パラメーターを使用すると、モジュールはグローバルにインストールされます。これは、追加のパス情報を入力することなく <strong>express</strong> コマンドにアクセスして、Web サイトのスキャフォールディングを生成できるようにするためです。</p>
	</div>

4. このアプリケーションで使用するスキャフォールディングを作成するには、**express** コマンドを使用します。

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

###追加モジュールのインストール

1. コマンド ラインで **tasklist** フォルダーに移動し、次のコマンドを入力して **package.json** ファイルに記述されたモジュールをインストールします。

        npm install

    このコマンドの出力は次のように表示されます。

		express@2.5.8 ./node_modules/express
		├── mime@1.2.4
		├── qs@0.4.2
		├── mkdirp@0.3.0
		└── connect@1.8.7
		jade@0.26.0 ./node_modules/jade
		├── commander@0.5.2
		└── mkdirp@0.3.0

	これによって、Express で必要な既定のモジュールがすべてインストールされます。

2. 次に、次のコマンドを使用して、nconf モジュールを追加します。このモジュールは、構成ファイルからデータベース接続文字列を読み取るアプリケーションによって使用されます。

	npm install nconf -save

2. 次に、Microsoft Driver for Node.JS for SQL Server のバイナリ バージョンを[ダウンロード センター]からダウンロードします。

3. アーカイブを **tasklist\\node\_modules** ディレクトリに展開します。

4. **tasklist\\node\_modules** ディレクトリ内の **msnodesql-install.cmd** ファイルを、テキスト エディターで開きます。これにより、**node\_modules** の下に **msnodesql** サブディレクトリが作成され、ドライバー ファイルがこの新しいディレクトリ構造に移動されます。

5. **msnodesql-install.cmd** ファイルは不要になったため削除します。

##ノード アプリケーションでの SQL データベースの使用

ここでは、**express** コマンドで作成された基本的なアプリケーションを、既存の **app.js** を変更することによって拡張し、先ほど作成したデータベースを使用するための新しい **index.js** ファイルを作成します。

###コントローラーの変更

1. **tasklist/routes** ディレクトリ内の **index.js** ファイルを、テキスト エディターで開きます。

2. **index.js** ファイルで、既存のコードを次のコードに置き換えます。これによって、msnodesql モジュールと nconf モジュールが読み込まれ、nconf を使用して **SQL\_CONN** という名前の環境変数または **config.json** ファイル内の **SQL\_CONN** 値から接続文字列が読み込まれます。

		var sql = require('msnodesql')
		    , nconf = require('nconf');

		nconf.env()
	         .file({ file: 'config.json' });
		var conn = nconf.get("SQL_CONN");

2. **index.js** ファイルへの内容の追加を続行し、**index** および **updateItem** メソッドを追加します。**index** メソッドは、データベースから完了していないタスクをすべて返し、**updateItem** メソッドは、選択されたタスクを完了済みとしてマークします。

		exports.index = function(req, res) {
		    var select = "select * from tasks where completed = 0";
		    sql.query(conn, select, function(err, items) {
		        if(err)
		            throw err;
		        res.render('index', { title: 'My ToDo List ', tasks: items });
		    });
		};

		exports.updateItem = function(req, res) {
		    var item = req.body.item;
		    if(item) {
		        var insert = "insert into tasks (name, category, created, completed) values (?, ?, GETDATE(), 0)";
		        sql.query(conn, insert, [item.name, item.category], function(err) {
		            if(err)
		                throw err;
		            res.redirect('/');
		        });
		    } else {
		        var completed = req.body.completed;
		        if(!completed.forEach)
		            completed = [completed];
		        var update = "update tasks set completed = 1 where id in (" + completed.join(",") + ")";
		        sql.query(conn, update, function(err) {
		            if(err)
		                throw err;
		            res.redirect('/');
		        });
		    }
		}

3. **index.js** ファイルを保存します。

###変更app.js

1. **tasklist** ディレクトリ内の **app.js** ファイルを、テキスト エディターで開きます。このファイルは、先ほど **express** コマンドを実行することによって作成されたものです。

2. app.js ファイル内で、次のコードが表示されるまで下へスクロールします。

		app.configure('development', function(){
  		app.use(express.errorHandler());
		});

3. ここで、次のコードを挿入します。


        app.get('/', routes.index);
		app.post('/', routes.updateItem);


 これによって、先ほど **index.js** ファイルに追加した **updateItem** メソッドへの新しいルートが追加されます。

       		
3. **app.js** ファイルを保存します。

###index ビューの変更

1. **views** ディレクトリに移動し、テキスト エディターで **index.jade** ファイルを開きます。

2. **index.jade** ファイルの内容を次のコードに置き換えます。これにより、既存のタスクを表示するビューと、新しいタスクの追加とタスクの完了済みのマーク付けを実行するためのフォームを定義します。

		h1= title
		br

		form(action="/", method="post")
		  table(class="table table-striped table-bordered")
		    thead
		      tr
		        td Name
		        td Category
		        td Date
		        td Complete
		    tbody
		    each task in tasks
		      tr
		        td #{task.name}
		        td #{task.category}
		        td #{task.created}
		        td
		          input(type="checkbox", name="completed", value="#{task.ID}", checked=task.completed == 1)
		  button(type="submit", class="btn") Update tasks
		hr

		form(action="/", method="post", class="well")
		  label Item Name:
		  input(name="item[name]", type="textbox")
		  label Item Category:
		  input(name="item[category]", type="textbox")
		  br
		  button(type="submit", class="btn") Add Item

3. **index.jade** ファイルを保存して閉じます。

###グローバル レイアウトの変更

**views** ディレクトリ内の **layout.jade** ファイルは、他の .**.jade** ファイルのグローバル テンプレートとして使用されます。この手順では、[Twitter Bootstrap](https://github.com/twbs/bootstrap) を使用するようにこのファイルを変更します。Twitter Bootstrap は、見栄えのよい Web サイトを簡単にデザインできるツールキットです。

1. [Twitter Bootstrap](http://getbootstrap.com/) のファイルをダウンロードして展開します。**bootstrap\\css** フォルダーから **bootstrap.min.css** ファイルを tasklist アプリケーションの **public\\stylesheets** ディレクトリにコピーします。

2. **views** フォルダーから、テキスト エディターで **layout.jade** を開き、内容を次の内容に置き換えます。

		!!!html
		html
		  head
		    title= title
		    meta(http-equiv='X-UA-Compatible', content='IE=10')
		    link(rel='stylesheet', href='/stylesheets/style.css')
		    link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
		  body(class='app')
		    div(class='navbar navbar-fixed-top')
		      .navbar-inner
		        .container
		          a(class='brand', href='/') My Tasks
		    .container!= body

3. **layout.jade** ファイルを保存します。

###構成ファイルの作成

**config.json** ファイルには、SQL データベースに接続するために使用される接続文字列が含まれており、実行時に **index.js** ファイルによって読み取られます。このファイルを作成するには、次の手順を実行します。

1. **tasklist** ディレクトリに **config.json** という名前の新しいファイルを作成し、テキスト エディターで開きます。

2. **config.json** ファイルの内容は、次のように設定する必要があります。

		{
		  "SQL_CONN" : "connection_string"
		}

	**connection_string** を、先ほど返された ODBC 接続文字列の値に置き換えます。

3. ファイルを保存します。

##ローカルでのアプリケーションの実行

ローカル コンピューターでアプリケーションをテストするには、次の手順を実行します。

1. コマンド ラインで、**tasklist** ディレクトリに移動します。

2. 次のコマンドを使用して、ローカルでアプリケーションを起動します。

        node app.js

3. Web ブラウザーを開いて、http://127.0.0.1:3000 にアクセスします。次のような Web ページが表示されます。

    ![空のタスク一覧が表示されている Web ページ][node-sql-empty]

4. 表示された **[Item Name]** と **[Item Category]** のフィールドを使用して情報を入力し、**[Add item]** をクリックします。

5. ページが更新され、ToDo List にアイテムが表示されるようになります。

    ![タスク一覧の新しいアイテムの画像][node-sql-list-items]

6. タスクを完了するには、[Complete] 列のチェック ボックスをオンにし、**[Update tasks]** をクリックします。

7. ノード プロセスを停止するには、コマンド ラインで、**Ctrl** キーを押しながら **C** キーを押します。

##Azure へのアプリケーションの展開

ここでは、Web サイトを作成した後のデプロイの手順を使用して、アプリケーションを Azure に発行します。

###アプリケーションの発行

1. コマンド ラインで、**tasklist** ディレクトリに移動します (現在のディレクトリがこのディレクトリではない場合)。

2. 次のコマンドを使用して、アプリケーションのローカルの git リポジトリを初期化し、リポジトリにアプリケーション ファイルを追加し、最後にファイルを Azure にプッシュします。

		git init
		git add .
		git commit -m "adding files"
		git remote add azure [URL for remote repository]
		git push azure master
	
	デプロイの最後に、次のようなステートメントが表示されます。
	
		To https://username@tabletasklist.azurewebsites.net/TableTasklist.git
 		 * [new branch]      master -> master

4. プッシュ操作が完了したら、**http://[site name].azurewebsites.net/** に移動してアプリケーションを表示します。

###環境変数への切り替え

前の手順で、**SQL_CONN** 環境変数の接続文字列を検索するか、**config.json** ファイルから接続文字列の値を読み込むコードを実装しました。次の手順では、実際のアプリケーションが環境変数を使ってアクセスする、Web サイト構成に含まれるキー/値のペアを作成します。

1. Azure の管理ポータルで、**[Web サイト]** をクリックし、対象となる Web サイトを選択します。

	![Web サイトのダッシュボードを開く][go-to-dashboard]

2. **[構成]** をクリックし、ページの **[アプリ設定]** セクションを探します。

	![構成リンク][web-configure]

3. **[アプリ設定]** で、**[キー]** フィールドに「**SQL_CONN**」と入力し、**[値]** フィールドに ODBC 接続文字列を入力します。最後に、チェック マークをクリックします。

	![アプリケーション設定][app-settings]

4. ページの下部にある **[保存]** アイコンをクリックし、この変更を実行時環境にコミットします。

	![アプリケーション設定の保存][app-settings-save]

5. コマンド ラインで、**tasklist** ディレクトリに移動し、次のコマンドを入力して **config.json** ファイルを削除します。

		git rm config.json
		git commit -m "Removing config file"

6. 次のコマンドを実行して、変更内容を Azure にデプロイします。

		git push azure master

変更内容を Azure にデプロイした後も、Web アプリケーションは **[アプリ設定]** のエントリから接続文字列を読み取って動作し続けます。これを確認するために、**[アプリ設定]** の **SQL_CONN** のエントリを無効な値に変更します。この値を保存すると、無効な接続文字列のために Web サイトは動作しなくなります。

##次のステップ

* [MongoDB を使用する Node.js Web アプリケーション]

* [テーブル ストレージを使用する node.js Web アプリケーション]

##その他のリソース

[Mac および Linux 用 Azure コマンド ライン ツール]    
[Node.js アプリケーションの作成と Azure の Web サイトへの展開]: /ja-jp/develop/nodejs/tutorials/create-a-website-(mac)/
[Git を使用した Azure の Web サイトへの発行]: /ja-jp/develop/nodejs/common-tasks/publishing-with-git/
[Azure デベロッパー センター]: /ja-jp/develop/nodejs/
[テーブル ストレージを使用する node.js Web アプリケーション]: /ja-jp/develop/nodejs/tutorials/web-site-with-storage/

[ノード]: http://nodejs.org
[Git]: http://git-scm.com
[express]: http://expressjs.com
[無料評価版]: http://windowsazure.com
[Git リモート]: http://git-scm.com/docs/git-remote

[MongoDB を使用する Node.js Web アプリケーション]: ../store-mongolab-web-sites-nodejs-store-data-mongodb/
[Mac および Linux 用 Azure コマンド ライン ツール]: /ja-jp/develop/nodejs/how-to-guides/command-line-tools/
[Node.js アプリケーションの作成と Azure の Web サイトへの展開]: ./web-site-with-mongodb-Mac
[Git を使用した Azure の Web サイトへの発行]: ../CommonTasks/publishing-with-git
[Azure ポータル: ]: http://windowsazure.com
[management-portal]: https://manage.windowsazure.com/
[node-sqlserver]: https://github.com/WindowsAzure/node-sqlserver
[Microsoft SQL Server 2012 Feature Pack]: http://www.microsoft.com/ja-jp/download/details.aspx?id=29065
[sql-database-editions]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee621788.aspx
[ダウンロード センター]: http://www.microsoft.com/ja-jp/download/details.aspx?id=29995
[Twitter Bootstrap]: http://twitter.github.com/bootstrap/

[app-settings-save]: ./media/sql-database-nodejs-use-web-site/savebutton.png
[web-configure]: ./media/sql-database-nodejs-use-web-site/sql-task-configure.png
[app-settings]: ./media/sql-database-nodejs-use-web-site/appsettings.png
[connection-string]: ./media/sql-database-nodejs-use-web-site/connection_string.jpg
[website-details-sqlazure]: ./media/sql-database-nodejs-use-web-site/website_details_sqlazure.jpg
[database-settings]: ./media/sql-database-nodejs-use-web-site/database_settings.jpg
[create-server]: ./media/sql-database-nodejs-use-web-site/create_server.jpg
[go-to-dashboard]: ./media/sql-database-nodejs-use-web-site/go_to_dashboard.png
[setup-git-publishing]: ./media/sql-database-nodejs-use-web-site/setup_git_publishing.png
[portal-git-username-password]: ./media/sql-database-nodejs-use-web-site/git-deployment-credentials.png


[git-instructions]: ./media/sql-database-nodejs-use-web-site/git-instructions.png
[linked-resources]: ./media/sql-database-nodejs-use-web-site/linked_resources.jpg
[new-website]: ./media/sql-database-nodejs-use-web-site/new_website.jpg
[custom-create]: ./media/sql-database-nodejs-use-web-site/custom_create.png

[node-sql-finished]: ./media/sql-database-nodejs-use-web-site/sql_todo_final.png
[node-sql-empty]: ./media/sql-database-nodejs-use-web-site/sql_todo_empty.png
[node-sql-list-items]: ./media/sql-database-nodejs-use-web-site/sql_todo_list.png





[sql-azure-manage]: ./media/sql-database-nodejs-use-web-site/sql-manage.png
[sql-azure-login]: ./media/sql-database-nodejs-use-web-site/sqlazurelogin.png
[sql-new-table]: ./media/sql-database-nodejs-use-web-site/new-table.png
[table-name-identity]: ./media/sql-database-nodejs-use-web-site/table-name-identity.png
[completed-table]: ./media/sql-database-nodejs-use-web-site/table-columns.png

