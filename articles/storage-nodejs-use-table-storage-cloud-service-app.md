<properties linkid="dev-nodejs-basic-web-app-with-storage" urlDisplayName="ストレージを使用する Web アプリケーション" pageTitle="テーブル ストレージを使用する Web アプリケーション (Node.js) | Microsoft Azure" metaKeywords="Azure Node.js Hello World チュートリアル, Azure Node.js Hello World, Azure Node.js 概要チュートリアル, Azure Node.js チュートリアル, Azure Node.js Express チュートリアル" description="Express を使用する Web アプリケーションのチュートリアルを基に、Azure ストレージ ストレージ サービスと Azure モジュールを追加したチュートリアル。" metaCanonical="" services="cloud-services,storage" documentationCenter="Node.js" title="ストレージを使用する Node.js Web アプリケーション" authors="" solutions="" manager="" editor="" />





# ストレージを使用する Node.js Web アプリケーション

このチュートリアルでは、Node.js 用の Azure クライアント ライブラリを
使用して、[Express を使用する Node.js Web アプリケーション]のチュートリアルで
作成したアプリケーションを拡張し、データ管理サービスを使用できるようにします。アプリケーションを
拡張して、Azure に展開できる Web ベースのタスク一覧アプリケーションを
作成します。このタスク一覧では、ユーザーが
タスクの取得、新しいタスクの追加、タスクの完了済みのマーク付けを実行できます。

タスク項目は Azure ストレージに格納されます。Azure 
ストレージは、フォールト トレランスと可用性に優れた非構造化データ 
ストレージです。Azure ストレージには、データを格納してアクセス
できるデータ構造がいくつか用意されています。Azure SDK for Node.js に
含まれる API または REST API を通じて、そのストレージ サービスを
活用できます。詳細については、「[Azure のデータの格納とアクセス]」を参照してください。

このチュートリアルは、[Node.js Web アプリケーション]および [Express を使用する Node.js][Node.js Web Application using Express] のチュートリアルを完了していることを前提としています。

学習内容:

-   Jade テンプレート エンジンを操作する方法
-   Azure データ管理サービスを操作する方法

完成したアプリケーションのスクリーンショットは次のようになります。

![Internet Explorer で表示された完成した Web ページ](./media/storage-nodejs-use-table-storage-cloud-service-app/getting-started-1.png)

## Web.Config のストレージ資格情報の設定

Azure ストレージにアクセスするには、ストレージ資格情報を
渡す必要があります。そのためには、web.config アプリケーション設定を使用します。
これらの設定は環境変数としてノードに渡され、その後 
Azure SDK によって読み取られます。

<div class="dev-callout">
<strong>注</strong>
<p>ストレージの資格情報は、アプリケーションを Azure に展開する
ときにのみ使用されます。エミュレーターで実行している場合、アプリケーションは
ストレージ エミュレーターを使用します。</p>
</div>

ストレージ アカウントの資格情報を取得して web.config 設定に追加するには、
次の手順を実行します。

1.  Azure PowerShell をまだ開いていない場合は起動します。**[スタート]** メニューで **[すべてのプログラム]、[Azure]** の順に展開し、**[Azure PowerShell]** を右クリックして、**[管理者として実行]** を選択します。

2.  アプリケーションが含まれているフォルダーに移動します。たとえば、C:\\node\\tasklist\\WebRole1 です。

3.  Azure Powershell ウィンドウで次のコマンドレットを入力して、ストレージ アカウント情報を取得します。

        PS C:\node\tasklist\WebRole1> Get-AzureStorageAccounts

	これにより、ホステッド サービスに関連付けられたストレージ アカウントとアカウント キーのリストが取得されます。

	<div class="dev-callout">
	<strong>注</strong>
	<p>Azure SDK では、サービスを展開するときにストレージ アカウントが作成されるので、以前のガイドでアプリケーションを展開したときから、ストレージ アカウントは既に存在しています。</p>
	</div>

4.  web.cloud.config ファイルを開きます。このファイルには、アプリケーションを Azure に展開するときに使用される環境設定が含まれます。

        PS C:\node\tasklist\WebRole1> notepad web.cloud.config

5.  次のブロックを **configuration** 要素の下に挿入し、{STORAGE ACCOUNT} と {STORAGE ACCESS KEY} を、展開に使用するストレージ アカウントのアカウント名とプライマリ キーに置き換えます。

        <appSettings>
          <add key="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}"/>
          <add key="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}"/>
        </appSettings>

	![web.cloud.config ファイルの内容](./media/storage-nodejs-use-table-storage-cloud-service-app/node37.png)

6.  ファイルを保存して、メモ帳を閉じます。

## モジュールのインストール

Azure データ管理サービスを使用するには、ノード用の Azure モジュールを
インストールする必要があります。node-uuid モジュールもインストールする必要が
あります。これは汎用一意識別子 (UUID) を生成するために使用されます。これらの
モジュールをインストールするには、次のコマンドを入力します。

    PS C:\node\tasklist\WebRole1> npm install node-uuid azure

コマンドが完了すると、モジュールが 
**node\_modules** フォルダーに追加されます。アプリケーションでこれらのモジュールを使用する
には、次の手順を実行します。

1.  server.js ファイルを開きます。

        PS C:\node\tasklist\WebRole1> notepad server.js

2.  以下のコードを、express.createServer() で終わっている行の後に追加して、node-uuid、home、および azure の各モジュールをインクルードします。まだ home モジュールはありませんが、すぐ後で作成します。

	![server.js コードの app = modules.exports の行を強調表示](./media/storage-nodejs-use-table-storage-cloud-service-app/node38.png)

        var uuid = require('node-uuid');
        var Home = require('./home');
        var azure = require('azure');

3.  ストレージ アカウントとアクセス キー情報を渡してストレージ テーブル クライアントを作成するコードを追加します。

	<div class="dev-callout">
	<strong>注</strong>
	<p>エミュレーターで実行している場合、web.config でストレージ アカウント情報が提供されている場合でも、SDK は自動的にエミュレーターを使用します。</p>
	</div>

        var client = azure.createTableService();

4.  次に、Azure ストレージで tasks という名前のテーブルを作成します。以下のロジックでは、このテーブルが存在しない場合は新しく作成し、テーブルに既定のデータを入力します。

        //table creation
        client.createTableIfNotExists('tasks', function(error){
            if(error){
                throw error;
            }

            var item = {
                name: 'Add readonly task list',
                category: 'Site work',
                date: '12/01/2011',
                RowKey: uuid(),
                PartitionKey: 'partition1',
                completed: false
            };

            client.insertEntity('tasks', item, function(){});

        });

5.  route セクションの既存のコードを次のコードに置き換えます。このコードは、home コントローラー インスタンスを作成し、**/** または **/home** に対するすべての要求をこのインスタンスにルーティングします。

	![server.js ファイルの routes セクションを強調表示.](./media/storage-nodejs-use-table-storage-cloud-service-app/node39.png)

        var home = new Home(client);
        app.get('/', home.showItems.bind(home));
        app.get('/home', home.showItems.bind(home));

	要求をインラインで処理する代わりに、コマンドを Home オブジェクトにデリゲートしています。**bind** コマンドは、これらの参照が home コントローラー内でローカルに正しく解決されるようにするために必要です。

## home コントローラーの作成

ここで、タスク一覧サイトに対するすべての要求を処理する、home コントローラーを
作成する必要があります。コントローラーを作成するには、次の手順を
実行します。

1.  メモ帳で新しい home.js ファイルを作成します。このファイルには、タスク一覧の
    ロジックを処理するコントローラー コードを含めます。

        PS C:\node\tasklist\WebRole1> notepad home.js

2.  内容を次のコードに置き換えて、ファイルを保存します。以下のコードでは、
    javascript モジュール パターンを使用しています。それによって Home 
    関数がエクスポートされます。Home プロトタイプには、実際の要求を処理する関数が
    含まれています。

        var azure=require('azure');
        module.exports = Home;

        function Home (client) {
            this.client = client;
        };

        Home.prototype = {
            showItems: function (req, res) {
                var self = this;
                this.getItems(false, function (resp, tasklist) {
                    if (!tasklist) {
                        tasklist = [];
                    }			
                    self.showResults(res, tasklist);
                });
            },

            getItems: function (allItems, callback) {
                var query = azure.TableQuery
                    .select()
                    .from('tasks');
        	
                if (!allItems) {
                    query = query.where('completed eq ?', 'false');
                }
                this.client.queryEntities(query, callback);
             },

             showResults: function (res, tasklist) {
                res.render('home', { 
                    title: 'Todo list', 
                    layout: false, 
                    tasklist: tasklist });
             },
        };

	これで、home コントローラーには 3 つの関数が追加されました。

	-   *showItems* は要求を処理します。
	-   *getItems* は、テーブル クライアントを使用して、開かれている
        タスク項目をタスク テーブルから取得します。クエリでは追加のフィルターを適用できます。
        たとえば、前のクエリ フィルターを適用した場合、completed が 
        false であるタスクのみが表示されます。
	-   *showResults* は、Express のレンダリング関数を呼び出し、
        次のセクションで作成する home ビューを使用してページを
        レンダリングします。

### home ビューの変更

Jade テンプレート エンジンでは、HTML よりも簡潔なマークアップ構文を使用します。
Jade は Express を操作するための既定のエンジンです。タスク一覧の
項目の表示をサポートするビューを作成するには、次の手順を実行
します。

1.  Windows PowerShell コマンド ウィンドウで、次のコマンドを使用して、
    home.jade ファイルを編集します。

        PS C:\node\tasklist\WebRole1\views> notepad home.jade

2.  home.jade ファイルの内容を次のコードに置き換えて、ファイルを
    保存します。以下のフォームには、タスク項目の読み取りと更新の
    機能が含まれます (ただし、現時点で home コントローラーがサポート
    しているのは読み取りだけです。これは後で変更します)。このフォームには、
    タスク一覧内の各項目の詳細が含まれます。

        html
        head
            title Index
        body
            h1 My ToDo List

            form
                table(border="1")
                    tr
                        td Name
                        td Category
                        td Date
                        td Complete

                        each item in tasklist
                            tr
                                td #{item.name}
                                td #{item.category} 
                                td #{item.date} 
                                td 
                                    input(type="checkbox", name="completed", value="#{item.RowKey}") 

## コンピューティング エミュレーターでのアプリケーションの実行

1.  Windows PowerShell ウィンドウで次のコマンドレットを入力して、
    コンピューティング エミュレーターでサービスを起動し、サービスの呼び出し元の 
    Web ページを表示します。

        PS C:\node\tasklist\WebRole1> Start-AzureEmulator -launch

	ブラウザーに次のページが表示され、Azure ストレージから取得されたタスク項目が表示されます。

	![Internet Explorer でテーブル内に項目を 1 つ含む "My Tasklist" ページを表示.](./media/storage-nodejs-use-table-storage-cloud-service-app/node40.png)

## タスクの新規作成機能の追加

このセクションでは、新しいタスク項目の追加をサポートするようにアプリケーションを
更新します。

### server.js への新しいルートの追加

server.js ファイルで、**/home** の最後の route エントリの後に
次の行を追加し、ファイルを保存します。

![server.js ファイルの home のルートを含む行を強調表示.](./media/storage-nodejs-use-table-storage-cloud-service-app/node41.png)

        app.post('/home/newitem', home.newItem.bind(home));

	ルート セクションは次のようになります。

       // Routes

       var home = new Home(client);
       app.get('/', home.showItems.bind(home));
       app.get('/home', home.showItems.bind(home));
       app.post('/home/newitem', home.newItem.bind(home));

### node-uuid モジュールの追加

node-uuid モジュールを使用して一意識別子を作成するには、
home.js ファイルの先頭で、モジュールがエクスポートされている最初の行の
後に、次の行を追加します。

![home.js ファイルの module.exports = Home の行を強調表示.](./media/storage-nodejs-use-table-storage-cloud-service-app/node42.png)

       var uuid = require('node-uuid');

### home コントローラーへの項目の新規作成関数の追加

項目の新規作成機能を実装するには、**newItem** 関数を作成します。
home.js ファイルで、最後の関数の後に次のコードを貼り付け、
ファイルを保存します。

![showResults 関数を強調表示](./media/storage-nodejs-use-table-storage-cloud-service-app/node43.png)

       newItem: function (req, res) {
           var self = this;
           var createItem = function (resp, tasklist) {
               if (!tasklist) {
                   tasklist = [];
               }

               var count = tasklist.length;

               var item = req.body.item;
               item.RowKey = uuid();
               item.PartitionKey = 'partition1';
               item.completed = false;

               self.client.insertEntity('tasks', item, function (error) {
                   if(error){  
                       throw error;
                   }
                   self.showItems(req, res);
               });
           };

           this.getItems(true, createItem);
       },

**newItem** 関数は、次のタスクを実行します。

-   ポストされた項目を本体から抽出します。
-   新しい項目の **RowKey** 値と **PartitionKey** 値を設定します。
    これらの値は、項目を Azure テーブルに挿入するうえで
    必要です。UUID は **RowKey** 値に対して生成されます。
-   **insertEntity** 関数を呼び出して、項目を 
    tasks テーブルに挿入します。
-   **getItems** 関数を呼び出して、ページをレンダリングします。

### home ビューへの項目の新規作成フォームの追加

項目の追加機能を備えた新しいフォームを追加して、ビューを更新
します。home.jade ファイルで、ファイルの末尾に次のコードを貼り付け、
ファイルを保存します。

<div class="dev-callout">
<strong>注</strong>
<p>Jade ではスペースに意味があるため、以下の
スペースはどれも削除しないでください。</p>
</div>

        hr
        form(action="/home/newitem", method="post")
            table(border="1")    
                tr
                    td Item Name: 
                    td 
                        input(name="item[name]", type="textbox")
                tr
                    td Item Category: 
                    td 
                        input(name="item[category]", type="textbox")
                tr
                    td Item Date: 
                    td 
                        input(name="item[date]", type="textbox")
            input(type="submit", value="Add item")

### エミュレーターでのアプリケーションの実行

1.  Azure エミュレーターは既に実行されているので、更新された
    アプリケーションを表示できます。

        PS C:\node\tasklist\WebRole1> start http://localhost:81/home

	ブラウザーが開き、次のページが表示されます。

	![My Task List というタイトルの Web ページと、タスクおよび新しいタスクを追加するためのフィールドを含むテーブル.](./media/storage-nodejs-use-table-storage-cloud-service-app/node44.png)

2.  **[Item Name]** に「New task functionality」、**[Item Category]** に「Site work」、**[Item Date]** に「12/02/2011」と入力します。次に、**[Add item]** をクリックします。

	Azure ストレージの tasks テーブルに項目が追加され、以下のスクリーンショットのように表示されます。

	![一覧にタスクを追加した後の、My Task List というタイトルの Web ページとタスクが格納されたテーブル](./media/storage-nodejs-use-table-storage-cloud-service-app/node45.png)

##Azure へのアプリケーションの再発行

アプリケーションが完成したので、既存のホステッド サービスへの
展開を更新して、アプリケーションを Azure に発行します。

1.  Windows PowerShell ウィンドウで、次のコマンドレットを呼び出して
    ホステッド サービスを Azure に再展開します。ストレージの設定と
    場所は以前に保存されているので、再入力する必要はありません。

        PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch

	デプロイが完了すると、次のような応答が表示されます。

	![デプロイ中に表示されるステータス メッセージ](./media/storage-nodejs-use-table-storage-cloud-service-app/node35.png)

	前と同様に **–launch** オプションを指定してあるので、発行が完了するとブラウザーが開かれ、Azure で実行されているアプリケーションが表示されます。

	![My Task List ページを表示しているブラウザー ウィンドウ。URL から、ページが Azure でホストされていることがわかります](./media/storage-nodejs-use-table-storage-cloud-service-app/node47.png)

## アプリケーションの停止と削除

アプリケーションの展開後に、コストがかかることを避けたり、無料評価版の
有効期間中に他のアプリケーションを作成して展開したりするために、
アプリケーションを無効にしたい場合があります。

Azure では、消費されたサーバー時間の 1 時間単位の料金が Web ロール インスタンスに課金されます。
いったんアプリケーションを展開すると、インスタンスが実行されていない場合や
停止状態の場合でもサーバー時間が消費されます。

次の手順では、アプリケーションの停止と削除の方法を示します。

1.  Windows PowerShell ウィンドウで次のコマンドレットを実行し、
    前のセクションで作成したサービスの展開を停止します。

        PS C:\node\tasklist\WebRole1> Stop-AzureService

	サービスの停止には、数分間かかる場合があります。サービスが停止すると、停止したことを知らせるメッセージが表示されます。

	![サービスが停止したことを示すステータス メッセージ](./media/storage-nodejs-use-table-storage-cloud-service-app/node48.png)

3.  サービスを削除するには、次のコマンドレットを呼び出します。

        PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist

	確認を求めるメッセージが表示されたら、「**Y**」と入力して、サービスを削除します。

	サービスの削除には、数分間かかる場合があります。サービスが削除されると、削除されたことを知らせるメッセージが表示されます。

	![サービスが削除されたことを示すステータス メッセージ](./media/storage-nodejs-use-table-storage-cloud-service-app/node49.png)

  [Express を使用する Node.js Web アプリケーション]: http://www.windowsazure.com/ja-jp/develop/nodejs/tutorials/web-app-with-express/
  [Azure のデータの格納とアクセス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433040.aspx
  [Node.js Web アプリケーション]: http://www.windowsazure.com/ja-jp/develop/nodejs/tutorials/getting-started/
 

