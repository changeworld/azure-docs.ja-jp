<properties 
	pageTitle="Azure Mobile Services および Sencha の使用" 
	description="このチュートリアルでは、Mobile Services と Sencha HTML5 モバイル アプリ フレームワークを使用した開発について説明します。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="hero-article" 
	ms.date="11/22/2014" 
	ms.author="sencha"/>

# <a name="getting-started"> </a>Azure Mobile Services および Sencha Touch の使用

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>このチュートリアルでは、Sencha Touch アプリケーションで Azure Mobile Services を活用する方法について説明します。管理ポータルで定義した Mobile Services を活用し、Sencha Touch を使用する簡単な To Do List アプリケーションを作成します。このチュートリアルでは、JavaScript について適切な知識を持ち、Sencha Touch フレームワークに精通する熟達した Web アプリケーション開発者を対象として中間的な説明を行うことを意図しています。 </p>
<p>右側のクリップを見ると、このチュートリアルと同じ手順をビデオで確認できます。このビデオでは、Arthur Kay が Azure Mobile Services バックエンドを使用して Sencha Touch アプリケーションをビルドする方法について説明します。</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=392574" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/partner-sencha-mobile-services-get-started/mobile-sencha-get-started-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=392574" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a> <span class="time">14:37</span></div>
</div>


完成したアプリケーションのスクリーンショットは次のようになります。

![][0]

## 要件

- <a href="http://wwww.sencha.com/products/touch/download" target="_blank">Sencha Touch</a> のダウンロードとインストール。

- <a href="http://www.sencha.com/products/sencha-cmd/download" target="_blank">Sencha Cmd Tool</a> のダウンロードとインストール。

- Java Runtime Environment (JRE) または Java Development Kit (Android アプリケーションを作成している場合)。

## <a name="create-new-service"> </a>新しい Mobile Services を作成する

[AZURE.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

## TodoItems テーブルの作成

Mobile Services を作成したら、管理ポータルの簡単なクイック スタートに従って、
Mobile Services 用に新しいデータベース テーブルを作成できます。

1. 管理ポータルで、**[Mobile Services]** をクリックし、先ほど作成した Mobile Services をクリックします。

2. [クイック スタート] タブの **[プラットフォームの選択]** で **[HTML]** をクリックし、**[新しい HTML アプリを作成する]** を展開します。

    ![Mobile quickstart html](./media/partner-sencha-mobile-services-get-started/mobile-portal-quickstart-html.png)

    これにより、Mobile Services に接続された HTML アプリを作成し、ホストするための簡単な 3 つの手順が表示されます。

    ![Mobile quickstart html](./media/partner-sencha-mobile-services-get-started/mobile-quickstart-steps-html.png)

3. **[TodoItems テーブルを作成する]** をクリックして、アプリケーション データを格納するテーブルを作成します。

	> [AZURE.NOTE] 管理ポータルから HTML アプリをダウンロードしないでください。その代わりに、次のセクションで Sencha Touch アプリケーションを手動で作成します。


1. 管理ポータルの **appKey** と **appUrl** をメモします。これらは、このチュートリアルの他のセクションで使用します。

    ![app key](./media/partner-sencha-mobile-services-get-started/mobile-app-key-portal.png)

1. **[構成]** タブで、**[クロス オリジン リソース共有 (CORS)]** の **[ホスト名からの要求を許可する]** に `localhost` が既に表示されていることを確認します。表示されていない場合は、**[ホスト名]** フィールドに「`localhost`」と入力して、**[保存]** をクリックします。

    ![Setup CORS for localhost](./media/partner-sencha-mobile-services-get-started/mobile-services-set-cors-localhost.png)

## Touch アプリケーションの生成

Sencha Touch テンプレート アプリケーションを生成することは、Sencha Cmd によって簡単に実行可能であり、アプリケーションを非常に迅速に動作させることができる優れた方法です。

Touch フレームワークをインストールしたディレクトリから、次のコマンドを発行します。

	$ sencha generate app Basic /path/to/application

これによって、 'Basic' という名前のアプリケーションを含むテンプレート Touch アプリケーションが生成されます。アプリケーションを起動するには、ブラウザーで単純に directory /path/to/application を指定します。これによって、標準の Touch サンプル アプリケーションが表示されます。

## Azure 用 Sencha Touch Extensions のインストール

Azure 用の拡張機能は、手動または Sencha パッケージとしてインストールされます。いずれの方法を使用してもかまいません。

### 手動のインストール

大半の Touch アプリケーションでは、クラスの外部ライブラリを追加する場合、単純にパッケージをダウンロードし、アプリケーション ディレクトリ内でパッケージを展開して、ライブラリの場所について Touch ローダーを構成します。 

次の手順に従って、アプリケーションに Azure 拡張機能を手動で追加できます。

1. [ここ](https://market.sencha.com/extensions/sencha-touch-extensions-for-windows-azure)から Azure 拡張機能パッケージをダウンロードします(この領域にアクセスするために Sencha Forums ID を使用できます)。

2. ダウンロード ディレクトリから最終的に配置および展開する場所に Azure 拡張機能パッケージをコピーします。 

        $ cd /path/to/application
	    $ mv /download-location/azure.zip .
    	$ unzip azure.zip  

    これによって、パッケージ ソース、例、およびドキュメント全体を含む **azure** ディレクトリが作成されます。このソースは、**azure/src** ディレクトリに存在します。


### Sencha パッケージとしてのインストール

> [AZURE.NOTE] <code>sencha generate app</code> コマンドを使用してアプリケーションを生成している場合にのみ、この方法が使用できます。

Sencha Cmd によって生成されるすべてのアプリケーションは、ルートに "packages" フォルダーがあります。このフォルダーの場所は構成可能ですが、その場所にかかわらず packages フォルダーの役割は、アプリケーション (または Sencha ワークスペースを作成している場合は複数のアプリケーション) によって使用されるすべてのパッケージのストレージとして機能することです。

Ext.Azure は Sencha Cmd "パッケージ" であるため、ソース コードは Sencha Cmd を使用して簡単にアプリケーションにインストールし、組み込むことができます(詳細については、[Sencha Cmd パッケージ](http://docs.sencha.com/cmd/3.1.2/#!/guide/command_packages) をご覧ください)。

Sencha パッケージ リポジトリから Azure 拡張機能パッケージをダウンロードおよびインストールするには、**app.json** ファイルにパッケージ名を追加し、アプリケーションをビルドする必要があります。

1. app.json ファイルの requires セクションに Azure パッケージを追加します。

	    {
            "name": "Basic",
	        "requires": [
    	        "touch-azure"
        	]
    	}
    
2. パッケージを取得およびインストールするために **sencha cmd** を使用して、アプリケーションをリビルドします。

	    $ sencha app build

ここで、**sencha app build** および **sencha app refresh** は共に、アプリケーション内にパッケージを統合するために必要な手順を実行します。通常、パッケージ要件の変更後に、"開発モード" のサポートに必要なメタデータを最新に維持するために、**sencha app refresh** を実行する必要があります。

いずれのコマンドを実行しても、Sencha Cmd は "packages" フォルダーにパッケージをダウンロードして展開します。これによって、ワークスペースに "packages/touch-azure" フォルダーが表示されます。

## Azure を含めて構成する

**ファイル名**: app.js 

Azure 拡張機能がダウンロードされ、アプリケーション ディレクトリにインストールされたので、次の手順ではソース ファイルの検出元と要求先をアプリケーションに指示します。

1. Sencha ローダーでソース コードの場所を構成します。
 
        Ext.Loader.setConfig({
       	    enabled : true,
           	paths   : {
               	'Ext'       : 'touch/src',
               	'Ext.azure' : '/path-to/azure-for-touch/azure/src'
            }
        });


2. Azure クラス ファイルを要求します。

		Ext.application({

			requires: [ 'Ext.azure.Azure' ],

			// ...

		});


3. Azure の構成

	Azure パッケージは、アプリケーションの起動セクションで **Ext.Azure.init** メソッドを呼び出すことにより初期化されます。このメソッドでは、Mobile Services の資格情報と、使用したいその他の資格情報および機能を含む構成オブジェクトを渡します。

	構成オブジェクトは直接 init メソッドに渡すことができますが、**azure** という Sencha アプリケーション構成プロパティを作成し、そこに該当する情報すべてを格納することをお勧めします。次に、このプロパティ値を Ext. Azure.init メソッドに渡すことができます。

	Azure で Mobile Services を作成する場合 (「[Azure の概要](http://senchaazuredocs.azurewebsites.net/#!/guide/getting_started)」をご覧ください)、アプリケーション キーおよび URL がサービスに割り当てられます。この情報は、サービスに接続できるように Azure パッケージに提供する必要があります。

	この例では、アプリケーション キーと URL のみを提供する非常に簡単な Azure の構成と初期化を示しています。

	    Ext.application({
    	    name: 'Basic',

        	requires: [ 'Ext.azure.Azure' ],

	        azure: {
    	        appKey: 'myazureservice-access-key',
        	    appUrl: 'myazure-service.azure-mobile.net'
	        },

    	    launch: function() {

        	    // Call Azure initialization

            	Ext.Azure.init(this.config.azure);

 	       }
    	});

	Azure の構成オプションについては、Ext. Azure API のドキュメントをご覧ください。


ご利用ありがとうございます。これでアプリケーションは、Mobile Services にアクセスできます。

## ToDo アプリケーションのビルド

ここまで、Azure 拡張機能を含めるようにアプリケーションを構成し、Mobile Services 資格情報を指定したので、サービスに格納された ToDo リスト データを表示および編集するために Mobile Services を活用する Touch アプリケーションの作成に着手できます。

### Azure データ プロキシの構成

**ファイル名:** app/model/TodoItem.js

Touch アプリケーションは、データ プロキシを通じて Mobile Services と通信します。プロキシは、Mobile Services に対する要求の送信およびデータの受信の両方についてすべての処理を行います。Touch データ モデルおよびストアと組み合わせて使用することで、リモート データの処理およびアプリケーションへの提供にかかるすべての困難な処理が排除され、その処理が Touch 自体によって実行されます。

Sencha Touch モデルでは、アプリケーションで使用するデータ レコードの定義を提供します。このモデルでは、データ フィールドを定義するだけでなく、アプリケーションと Azure Mobile Services 間の通信を処理するプロキシに関する構成を提供できます。

次のコードでは、モデルのフィールド (およびその種類) を定義し、またプロキシの構成を提供しています。プロキシを構成する場合、種類 (この場合は 'azure')、Mobile Services テーブル名 (TodoItems)、その他のオプション パラメーターを渡す必要があります。この例では、プロキシのページングを有効にすることで、一覧の項目について前後にシームレスにページングできます。

Azure プロキシは、Azure API によって想定される適切な CRUD 操作と共に、すべての HTTP ヘッダーを自動的に設定します (存在する場合は、認証資格情報が含まれます)。

	Ext.define('Basic.model.TodoItem', {
    	extend : 'Ext.data.Model',

	    requires : [
    	    'Ext.azure.Proxy'
    	],

	    config : {
    	    idProperty : 'id',
        	useCache   : false,

	        fields     : [
    	        {
        	        name : 'id',
            	    type : 'int'
            	},
            	{
                	name : 'text',
                	type : 'string'
            	},
            	{
	                name : 'complete',
    	            type : 'boolean'
        	    }
	        ],

	        proxy : {
    	        type               : 'azure',
        	    tableName          : 'TodoItem',
            	enablePagingParams : true
        	}
    	}
	});


### ToDo 項目の格納 

**ファイル名**: app/store/TodoItems.js

Sencha Touch ストアでは、さまざまな方法でレコードを表示するために、Touch コンポーネントのソースとして使用できるデータ レコード (モデル) のコレクションを保存します。これには、グリッド、グラフ、リストなどを含めることができます。

ここでは、Azure Mobile Services から取得したすべてのストア ToDo リスト項目を保持するために使用するストアを定義します。ストア構成には、モデルの種類の名前が含まれます (Basic.model.TodoItem - 先に定義されています)。これによって、ストアに含まれるレコードの構造が定義されます。

また、ページ サイズ (8 レコード) を指定するなど、ストアに対するいくつかの追加構成オプションが用意されています。このストアのレコードの並べ替えは、Azure Mobile Services によってリモートに行われます (並べ替えは、ストア自体でローカルには行われません)。

	Ext.define('Basic.store.TodoItems', {
    	extend : 'Ext.data.Store',

	    requires : [
    	    'Basic.model.TodoItem'
	    ],

	    config : {
    	    model        : 'Basic.model.TodoItem',
        	pageSize     : 8,
	        remoteSort   : true,
    	    remoteFilter : true
    	}
	});


### ToDo 項目の表示および編集

**ファイル名**: app/view/DataItem.js

これで、各 ToDo 項目の構造を定義し、すべてのレコードを配置するためのストアを作成したので、アプリケーションのユーザーに対してこの情報をどのように表示するかについて考えます。通常は、**Views** を使用してユーザーに情報を表示します。任意の数の Touch コンポーネントの 1 つを個別にまたは他と組み合わせて表示できます。 

次の表示は、各レコードの表示方法を定義する ListItem と、各項目を削除するためのアクションを格納するいくつかのボタンから構成されています。

	Ext.define('Basic.view.DataItem', {
    	extend : 'Ext.dataview.component.ListItem',
    	xtype  : 'basic-dataitem',

	    requires : [
    	    'Ext.Button',
        	'Ext.layout.HBox',
        	'Ext.field.Checkbox'
    	],

	    config : {
    	    checkbox : {
        	    docked     : 'left',
            	xtype      : 'checkboxfield',
            	width      : 50,
            	labelWidth : 0
        	},

	        text : {
    	        flex : 1
        	},

	        button : {
    	        docked   : 'right',
        	    xtype    : 'button',
            	ui       : 'plain',
	            iconMask : true,
    	        iconCls  : 'delete',
        	    style    : 'color: red;'
        	},

	        dataMap : {
    	        getText : {
        	        setHtml : 'text'
            	},

	            getCheckbox : {
    	            setChecked : 'complete'
        	    }
        	},

	        layout : {
    	        type : 'hbox',
        	    align: 'stretch'
        	}
    	},

	    applyCheckbox : function(config) {
    	    return Ext.factory(config, Ext.field.Checkbox, this.getCheckbox());
    	},

	    updateCheckbox : function (cmp) {
    	    if (cmp) {
        	    this.add(cmp);
        	}
    	},

	    applyButton : function(config) {
    	    return Ext.factory(config, Ext.Button, this.getButton());
    	},

	    updateButton : function (cmp) {
    	    if (cmp) {
        	    this.add(cmp);
        	}
    	}

	});


### プライマリ ビューの作成

**ファイル名**: app/view/Main.js

ここまで、個々の ToDo リスト項目 (上) のレイアウトを定義したので、実際の項目のリスト、アプリケーションのタイトル、および新しいタスクを追加するためのボタンを定義する完全なユーザー インターフェイスでリストをラップします。 

	Ext.define('Basic.view.Main', {
    	extend : 'Ext.dataview.List',
    	xtype  : 'main',

	    requires : [
    	    'Ext.TitleBar',
        	'Ext.dataview.List',
        	'Ext.data.Store',
        	'Ext.plugin.PullRefresh',
        	'Ext.plugin.ListPaging',
        	'Basic.view.DataItem'
    	],

	    config : {
    	    store : 'TodoItems',

        	useSimpleItems : false,
        	defaultType    : 'basic-dataitem',

	        plugins : [
    	        {
        	        xclass          : 'Ext.plugin.PullRefresh',
            	    pullRefreshText : 'Pull down to refresh!'
            	},
            	{
                	xclass     : 'Ext.plugin.ListPaging',
                	autoPaging : true
            	}
        	],

	        scrollable : {
    	        direction     : 'vertical',
        	    directionLock : true
        	},

	        items : [
    	        {
        	        docked : 'top',
            	    xtype  : 'titlebar',
                	title  : 'Azure Mobile - Basic Data Example'
            	},
            	{
                	xtype  : 'toolbar',
                	docked : 'bottom',
                	items  : [
                    	{
                        	xtype       : 'textfield',
                        	placeHolder : 'Enter new task',
                        	flex        : 1
                    	},
                    	{
                        	xtype  : 'button',
                        	action : 'add',
                        	text   : 'Add'
                    	}
                	]
            	}
        	]
    	}
	});

### すべての共同作業

**ファイル名**: app/controller/Main.js

アプリケーションの最後の手順は、押されたボタン (削除、保存など) に応答し、こうしたすべての要求に対応するロジックを提供することです。Sencha Touch は、こうしたイベントをリッスンするコントローラーを活用し、それに応じて応答します。

	Ext.define('Basic.controller.Main', {
    	extend : 'Ext.app.Controller',

	    config : {
    	    refs : {
        	    todoField : 'main toolbar textfield',
            	main      : 'main'
        	},

	        control : {
    	        'button[action=add]'    : {
        	        tap : 'onAddItem'
            	},
            	'button[action=reload]' : {
                	tap : 'onReload'
            	},

	            main : {
    	            activate      : 'loadInitialData',
        	        itemdoubletap : 'onItemEdit'
            	},

	            'basic-dataitem checkboxfield' : {
    	            change : 'onItemCompleteTap'
        	    },

            	'basic-dataitem button' : {
                	tap : 'onItemDeleteTap'
            	}
        	}
    	},

	    loadInitialData : function () {
    	    Ext.getStore('TodoItems').load();
    	},

	    onItemDeleteTap : function (button, e, eOpts) {
    	    var store    = Ext.getStore('TodoItems'),
        	    dataItem = button.up('dataitem'),
            	rec      = dataItem.getRecord();

	        rec.erase({
    	        success: function (rec, operation) {
        	        store.remove(rec);
            	},
            	failure: function (rec, operation) {
                	Ext.Msg.alert(
                    	'Error',
                    	Ext.util.Format.format('There was an error deleting this task.<br/><br/>	Status Code: {0}<br/>Status Text: {1}', 
                    	operation.error.status, 
                    	operation.error.statusText)
                	);
            	}
        	});
    	},

	    onItemCompleteTap : function (checkbox, newVal, oldVal, eOpts) {
    	    var dataItem = checkbox.up('dataitem'),
        	    rec      = dataItem.getRecord(),
            	recVal   = rec.get('complete');

	        // this check is needed to prevent an issue where multiple creates get triggered from one create
        	if (newVal !== recVal) {
            	rec.set('complete', newVal);
            	rec.save({
                	success: function (rec, operation) {
                    	rec.commit();
                	},
                	failure: function (rec, operation) {
                    	// since there was a failure doing the update on the server then silently reject the change
	                    rec.reject(true);
    	                Ext.Msg.alert(
        	                'Error',
            	            Ext.util.Format.format('There was an error updating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}', 
            	            operation.error.status, 
            	            operation.error.statusText)
	                    );
    	            }
        	    });
        	}
    	},

	    onItemEdit : function (list, index, target, record, e, eOpts) {
    	    var rec = list.getSelection()[0];

        	Ext.Msg.prompt('Edit', 'Rename task',
            	function (buttonId, value) {
                	if (buttonId === 'ok') {
                    	rec.set('text', value);
                    	rec.save({
                        	success: function (rec, operation) {
                            	rec.commit();
                        	},
                        	failure: function (rec, operation) {
                            	// since there was a failure doing the update on the server then reject the change
                            	rec.reject();
                            	Ext.Msg.alert(
                                	'Error',
                                	Ext.util.Format.format('There was an error updating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}', 
                                	operation.error.status, 
                                	operation.error.statusText)
                            	);
                        	}
                    	});
                	}
            	},
            	null,
            	false,
            	record.get('text')
        	);
    	},

	    onReload : function () {
    	    Ext.getStore('TodoItems').load();
    	},

	    onAddItem : function () {
    	    var me = this,
        	    rec,
            	store = Ext.getStore('TodoItems'),
            	field = me.getTodoField(),
            	value = field.getValue();

	        if (value === '') {
    	        Ext.Msg.alert('Error', 'Please enter Task name', Ext.emptyFn);
        	}
        	else {
            	rec = Ext.create('Basic.model.TodoItem', {
                	complete : false,
                	text     : value
            	});
            	//store.insert(0, rec); //insert at the top
            	//store.sync();
            	rec.save({
                	success: function (rec, operation) {
                    	store.insert(0, rec); //insert at the top
                    	field.setValue('');
                	},
                	failure: function (rec, operation) {
                    	Ext.Msg.alert(
                        	'Error',
                        	Ext.util.Format.format('There was an error creating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}', 
                        	operation.error.status, 
                        	operation.error.statusText)
                    	);
                	}
            	});
        	}
    	}
	});

### すべてをまとめた配置

**ファイル名**: app.js

最後の手順は、メイン アプリケーション ファイルの編集を終了し、定義したモデル、ストア、ビュー、およびコントローラーに関する情報を提供することです。これらのリソースのソース ファイルは、アプリケーションに自動的に読み込まれます。最後に、プライマリ アプリケーション ビューである  'Basic.main.View' を作成および表示する起動メソッドを呼び出します。


	Ext.Loader.setConfig({
    	enabled : true,
    	paths   : {
        	'Ext'       : 'touch/src',
        	'Ext.azure' : 'packages/azure/src'
    	}
	});

	Ext.application({
    	name : 'Basic',

	    requires : [
    	    'Ext.MessageBox',
        	'Ext.azure.Azure'
    	],

	    views : [
    	    'Main'
    	],

	    controllers : [
    	    'Main'
    	],

	    stores : [
    	    'TodoItems'
    	],

	    azure : {
    	    appUrl : 'YOUR_APP_URL.azure-mobile.net',
        	appKey : 'YOUR_APP_KEY'
    	},

	    icon : {
    	    '57'  : 'resources/icons/Icon.png',
        	'72'  : 'resources/icons/Icon~ipad.png',
        	'114' : 'resources/icons/Icon@2x.png',
        	'144' : 'resources/icons/Icon~ipad@2x.png'
    	},

	    isIconPrecomposed : true,

	    startupImage : {
    	    '320x460'   : 'resources/startup/320x460.jpg',
        	'640x920'   : 'resources/startup/640x920.png',
        	'768x1004'  : 'resources/startup/768x1004.png',
        	'748x1024'  : 'resources/startup/748x1024.png',
        	'1536x2008' : 'resources/startup/1536x2008.png',
        	'1496x2048' : 'resources/startup/1496x2048.png'
    	},

	    launch : function () {
    	    // Destroy the #appLoadingIndicator element
        	Ext.fly('appLoadingIndicator').destroy();

	        // Initialize Azure
    	    Ext.Azure.init(this.config.azure);

	        // Initialize the main view
    	    Ext.Viewport.add(Ext.create('Basic.view.Main'));
	    },

	    onUpdated : function () {
    	    Ext.Msg.confirm(
        	    "Application Update",
            	"This application has just successfully been updated to the latest version. Reload now?",
	            function (buttonId) {
    	            if (buttonId === 'yes') {
        	            window.location.reload();
            	    }
            	}
        	);
   		}
	});

### Sencha Touch アプリケーションのホストおよび実行

このチュートリアルの最後に、ローカル コンピューターの新しいアプリケーションをホストして実行します。

  1. ターミナルで、展開されたアプリケーションの場所を参照します。

  2. Sencha Cmd を使用し、次のコマンドを実行します。

    * *sencha app refresh* :これにより、Sencha Cmd にすべてのアプリケーションの依存関係を特定し、
      必要なパッケージ (たとえば、[Azure 用 Sencha Touch Extensions](https://market.sencha.com/extensions/sencha-touch-extensions-for-windows-azure)) をダウンロードするよう指示します。

    * *sencha web start* :これによって、アプリケーションをテストするためにローカル Web サーバーが起動されます。

    ![sencha web start](./media/partner-sencha-mobile-services-get-started/sencha-web-start.png)

  3. アプリケーションを開始するためにターミナルに示された URL を Web ブラウザーで開きます (たとえば、http://localhost:1841 など)。

  4. アプリケーションで、意味のあるテキスト (たとえば、"チュートリアルの完了") を入力し、**[Add]** をクリックします。

    ![new todo item](./media/partner-sencha-mobile-services-get-started/new-todo-item.png)

    これで、Azure でホストされている新しい Mobile Services に POST 要求が送信されます。要求のデータは TodoItems テーブルに挿入されます。

  5. 管理ポータルに戻り、**[データ]** タブ、TodoItems テーブルの順にクリックします。

    ![Todo Items table](./media/partner-sencha-mobile-services-get-started/mobile-data-tab.png)

    これで、アプリケーションによってテーブルに挿入されたデータを参照できます。

    ![browse todo table](./media/partner-sencha-mobile-services-get-started/mobile-data-browse.png)

## 次のステップ
ファースト ステップ ガイドはこれで完了です。Sencha による Mobile Services で重要になるこれ以外の作業については、以下のトピックをご覧ください。

その他のスタイル、および Sencha Touch で可能なその他の機能については、全サンプル アプリケーションを[ダウンロード](https://github.com/arthurakay/sencha-touch-azure-example)してください。

さらに、Azure 用 Sencha Touch Extensions の詳細についてご覧ください。

  * サンプル アプリケーションの[チュートリアル](http://docs.sencha.com/touch-azure/1.0.0/#!/guide/data_filters)
  * [Sencha Forums](http://www.sencha.com/forum) でサポートを得てください
  * [Sencha のドキュメント](http://docs.sencha.com/)をご覧ください
  * Azure Mobile Services での Sencha の使用:[(ビデオ)](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-126-Using-Sencha-With-Windows-Azure-Mobile-Services)


## その他のリソース

  * [Sencha Touch のダウンロード](http://pages.sencha.com/touch-for-azure.html)
  * [Azure 用 Sencha Touch Extension](https://market.sencha.com/extensions/sencha-touch-extensions-for-windows-azure)
 

## まとめ

ここで概要を示した例は、Azure 用 Sencha Touch Extension パッケージで提供されており、examples ディレクトリに Basic Data の例として格納されています。詳細なコメントおよび説明と共に、この拡張機能の他の機能をデモするいくつかの例も提供されています。

Sencha Touch のさらなる概要については、全[ガイド](http://docs.sencha.com/touch/#!/guide) セットをご覧ください。

<!-- images -->
[0]: ./media/partner-sencha-mobile-services-get-started/finished-app.png


<!--HONumber=52-->