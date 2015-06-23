<properties 
	pageTitle="JavaScript バックエンド モバイル サービスを操作する" 
	description="Azure Mobile Services でサーバー スクリプトを定義、登録、使用する方法の例を提供します。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="ricksal"/>


# JavaScript バックエンド モバイル サービスを操作する

<div class="dev-center-tutorial-subselector"><a href="/documentation/articles/mobile-services-dotnet-backend-how-to-use/" title=".NET バックエンド">.NET バックエンド</a> | <a href="/documentation/articles/mobile-services-how-to-use-server-scripts/"  title="JavaScript バックエンド" class="current">JavaScript バックエンド</a></div>
この記事では、Azure モバイル サービスで JavaScript バックエンドを使用する方法についての詳細な情報と例を提供します。

## <a name="intro"></a>はじめに

JavaScript バックエンド モバイル サービスを使用すると、サーバーに格納されて実行される JavaScript コードとして、カスタムのビジネス ロジックを定義できます。このサーバー スクリプト コードは、次のサーバー機能のいずれかに割り当てられます。

+ [特定のテーブルの挿入、読み取り、更新、または削除操作][Table operations]。
+ [スケジュールされたジョブ][Job Scheduler]。
+ [カスタム API で定義された HTTP メソッド][Custom API anchor]。 

サーバー スクリプトのメイン関数のシグネチャは、スクリプトが使用されるコンテキストによって異なります。スクリプト間で共有される共通スクリプト コードを nodes.js モジュールとして定義することもできます。詳細については、「[ソース管理と共有コード][Source control, shared code, and helper functions]」を参照してください。

個々のサーバー スクリプト オブジェクトおよび関数の詳細については、「[Mobile Services server script reference (モバイル サービスのサーバー スクリプト リファレンス)]」を参照してください。


## <a name="table-scripts"></a>テーブル操作

テーブル操作スクリプトは、テーブルに対する操作 (insert、read、update、または delete (*del*)) に登録されたサーバー スクリプトです。このセクションでは、JavaScript バックエンドでテーブルを操作する方法について説明します。次のセクションが含まれています。

+ [テーブル操作の概要][Basic table operations]
+ [方法: テーブル操作に登録する]
+ [方法: 既定の応答をオーバーライドする]
+ [方法: execute success をオーバーライドする]
+ [方法: 既定のエラー処理をオーバーライドする]
+ [方法: 一意の ID 値を生成する](#generate-guids)
+ [方法: カスタム パラメーターを追加する]
+ [方法: テーブルのユーザーを処理する][How to: Work with users]

### <a name="basic-table-ops"></a>テーブル操作の概要

スクリプトの名前は、登録されている操作の種類と一致する必要があります。1 つのテーブル操作に対して登録できるスクリプトは 1 つだけです。REST 要求によって特定の操作が呼び出されるたびに、スクリプトが実行されます。たとえば、テーブルに項目を挿入する POST 要求を受け取ると、そのたびに特定のスクリプトが実行されます。Mobile Services では、スクリプトの実行間で状態は保持されません。スクリプトが実行されるたびに新しいグローバル コンテキストが作成されるため、スクリプトで定義されている状態変数がすべて再初期化されます。要求間で状態を保存する必要がある場合は、モバイル サービス上でテーブルを作成し、そのテーブルに対して状態を読み書きします。詳細については、「[方法: スクリプトからテーブルにアクセスする]」を参照してください。

操作が実行されたときにカスタマイズされたビジネス ロジックを強制的に実行する必要がある場合は、テーブル操作スクリプトを作成します。たとえば、次のスクリプトでは、`text` フィールドの文字列の長さが 10 文字を超える挿入操作を拒否します。

	function insert(item, user, request) {
	    if (item.text.length > 10) {
	        request.respond(statusCodes.BAD_REQUEST, 
				'Text length must be less than 10 characters');
	    } else {
	        request.execute();
	    }
	}

テーブル スクリプト関数は、常に 3 つの引数を受け取ります。

- 最初の引数は、テーブル操作によって異なります。 

	- 挿入と更新では、**item** オブジェクトです。これは、操作の影響を受ける行の JSON 表現です。そのため、*item.Owner* のように、名前で列の値にアクセスできます。*Owner* は JSON 表現でのいずれかの名前です。
	- 削除では、削除するレコードの ID です。 
	- 読み取りでは、返される行セットを指定する [query オブジェクト]です。

- 2 番目の引数は、常に要求を送信したユーザーを表す [user オブジェクト][User object]です。

- 3 番目の引数は常に、[request オブジェクト][Request object]です。このオブジェクトを使用すると、要求された操作の実行およびクライアントに送信される応答を制御できます。

テーブル操作の正規のメイン関数署名は次のとおりです。

+ [挿入][insert function]: `function insert (item, user, request) { ... }`
+ [更新][update function]: `function update (item, user, request) { ... }`
+ [削除][delete function]: `function del (id, user, request) { ... }`
+ [読み取り][read function]: `function read (query, user, request) { ... }`

>[AZURE.NOTE]delete は JavaScript の予約語であるため、削除操作に登録する関数には _del_ という名前を付ける必要があります。

サーバー スクリプトには必ずメインの関数が含まれ、必要に応じて省略可能なヘルパー関数が使用されます。サーバー スクリプトが特定のテーブル用に作成されていても、同じデータベース内の他のテーブルも参照することができます。スクリプト間で共有できる一般的な関数をモジュールとして定義することもできます。詳細については、「[ソース管理と共有コード][Source control, shared code, and helper functions]」を参照してください。

### <a name="register-table-scripts"></a>方法: テーブル スクリプトを登録する

次のいずれかの方法で、テーブル操作に登録されるサーバー スクリプトを定義できます。

+ [Azure 管理ポータル][Management Portal]で。テーブル操作用のスクリプトには、特定のテーブルの **[スクリプト]** タブでアクセスします。`TodoItem` テーブルの挿入スクリプトに登録されている既定のコードを次に示します。このコードを独自のカスタム ビジネス ロジックでオーバーライドできます。

	![1][1]
	
	この方法については、「[サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]」を参照してください。

+ ソース管理を使用して。ソース管理を有効にしている場合は、単に git リポジトリの .\\service\\table サブフォルダーに <em>`<table>`</em>.<em>`<operation>`</em>.js という名前のファイルを作成します。ここで、<em>`<table>`</em> はテーブルの名前であり、<em>`<operation>`</em> は登録するテーブル操作です。詳細については、「[ソース管理と共有コード][Source control, shared code, and helper functions]」を参照してください。

+ コマンド プロンプトから Azure コマンド ライン ツールを使用して。詳細については、「[コマンド ライン ツールの使用]」を参照してください。


テーブル操作スクリプトでは、[request] オブジェクトの次の関数のうちの少なくとも 1 つを呼び出して、クライアントが応答を確実に受信するようにする必要があります。
 
+ **execute 関数**: 操作を要求されたとおりに完了し、標準の応答を返します。
 
+ **respond 関数**: カスタム応答を返します。

> [AZURE.IMPORTANT]スクリプトに、**execute** も **respond** も呼び出されないコード パスが含まれている場合、操作が応答しなくなることがあります。

次のスクリプトでは、**execute** 関数を呼び出して、クライアントによって要求されたデータ操作を完了します。

	function insert(item, user, request) { 
	    request.execute(); 
	}

この例では、データベースに項目を挿入し、適切なステータス コードをユーザーに返します。

**execute** 関数が呼び出されると、スクリプト関数に最初の引数として渡された `item`、[query][query object]、または `id` 値を使用して、操作を実行します。挿入、更新、またはクエリ操作では、**execute** を呼び出す前に、item や query を変更できます。

	function insert(item, user, request) { 
	    item.scriptComment =
			'this was added by a script and will be saved to the database'; 
	    request.execute(); 
	} 
 
	function update(item, user, request) { 
	    item.scriptComment = 
			'this was added by a script and will be saved to the database'; 
	    request.execute(); 
	} 

	function read(query, user, request) { 
		// Only return records for the current user 	    
		query.where({ userid: user.userId}); 
	    request.execute(); 
	}
 
>[AZURE.NOTE]削除スクリプトでは、指定した userId 変数の値を変更しても、削除されるレコードには影響しません。

その他の例については、「[データの読み取りと書き込み]」、「[要求の変更]」、および「[データの検証]」を参照してください。


### <a name="override-response"></a>方法: 既定の応答をオーバーライドする

スクリプトを使用して、既定の応答動作をオーバーライドできる検証ロジックを実装することもできます。検証が失敗したら、**execute** 関数の代わりに **respond** 関数を呼び出して、応答をクライアントに書き込みます。

	function insert(item, user, request) {
	    if (item.userId !== user.userId) {
	        request.respond(statusCodes.FORBIDDEN, 
	        'You may only insert records with your userId.');
	    } else {
	        request.execute();
	    }
	}

この例では、挿入する項目の `userId` プロパティが、認証されたクライアントに関連して渡された [user オブジェクト]の `userId` と一致しない場合に、要求が拒否されます。その場合、データベース操作 (*insert*) は実行されず、403 HTTP ステータス コードとカスタム エラー メッセージを含む応答がクライアントに返されます。その他の例については、「[応答の変更]」を参照してください。

### <a name="override-success"></a>方法: execute success をオーバーライドする

テーブル操作の場合、既定では、**execute** 関数は応答を自動的に書き込みます。ただし、execute 関数に、成功および失敗時の動作をオーバーライドする 2 つのオプション パラメーターを渡すことができます。

execute を呼び出すときに **success** ハンドラーを渡すことで、クエリの結果を変更した後でそれを応答に書き込むことができます。次の例は、`execute({ success: function(results) { ... })` を呼び出すことで、データをデータベースから読み取ってから応答を書き込むまでの間に、追加の作業を実行します。

	function read(query, user, request) {
	    request.execute({
	        success: function(results) {
	            results.forEach(function(r) {
	                r.scriptComment = 
	                'this was added by a script after querying the database';
	            });
	            request.respond();
	        }
	    });
	}

**execute** 関数に **success** ハンドラーを渡す場合は、スクリプトが完了して応答を書き込むことができることをランタイムに通知するために、**success** ハンドラーの一部として **respond** 関数も呼び出す必要があります。引数を渡さずに **respond** を呼び出すと、既定の応答がモバイル サービスによって生成されます。

>[AZURE.NOTE]最初に **execute** 関数を呼び出した後でのみ、引数を指定せずに **respond** 関数を呼び出すと、既定の応答を呼び出すことができます。
 
### <a name="override-error"></a>方法: 既定のエラー処理をオーバーライドする

**execute** 関数は、データベースへの接続が失われた場合、オブジェクトが無効である場合、クエリが間違っている場合に失敗することがあります。エラーが発生すると、サーバー スクリプトは既定でエラーをログに記録し、エラー結果を応答に書き込みます。モバイル サービスには既定のエラー処理が用意されているため、サービスで発生する可能性のあるそうしたエラーを処理する必要はありません。

特定の修正操作を実行する場合や、グローバル console オブジェクトを使用して詳細な情報をログに書き込む場合は、明示的なエラー処理を実装して、既定のエラー処理をオーバーライドすることができます。それには、**execute** 関数に **error** ハンドラーを渡します。

	function update(item, user, request) { 
	  request.execute({ 
	    error: function(err) { 
	      // Do some custom logging, then call respond. 
	      request.respond(); 
	    } 
	  }); 
	}
 

error ハンドラーを渡した場合は、**respond** が呼び出されたときに、モバイル サービスからクライアントにエラー結果が返されます。

必要であれば、**success** ハンドラーと **error** ハンドラーも渡すことができます。

### <a name="generate-guids"></a>方法: 一意の ID 値を生成する

Mobile Services は、テーブルの **ID** 列で一意のカスタム文字列値をサポートしています。このため、アプリケーションは、ID にメール アドレスやユーザー名などのカスタム値を使用できます。

文字列 ID には、次のような利点があります。

+ データベースと往復することなく ID が生成されます。
+ 他のテーブルやデータベースのレコードをより簡単にマージできます。
+ ID 値をより適切にアプリケーションのロジックに統合できます。

挿入されたレコードで文字列 ID 値が設定されない場合は、Mobile Services によって ID 用の一意の値が生成されます。一意の ID 値はサーバー スクリプトで生成できます。次のスクリプト例は、カスタム GUID を生成し、新しいレコードの ID に割り当てます。これは、レコードの ID として値を渡さなかった場合に、モバイル サービスによって生成される ID 値に似ています。

	// Example of generating an id. This is not required since Mobile Services
	// will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


アプリケーションが ID の値を指定すると、Mobile Services はそれをそのまま格納します。これには、前後の空白文字も含まれます。値から空白文字が除去されることはありません。

`id` の値は一意である必要があり、次のセット内の文字を含まないようにする必要があります。

+ 制御文字: [0x0000-0x001F] および [0x007F-0x009F]。詳細については、[ASCII 制御コード C0 および C1 に関するページ](http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set)を参照してください。
+  印刷可能文字: **"** (0x0022)、**+** (0x002B)、**/** (0x002F)、**?** (0x003F)、**\\** (0x005C)、**`** (0x0060)
+  ID "." および ".."

また、テーブルに整数 ID を使用することもできます。整数 ID を使用するには、`mobile table create` コマンドで `--integerId` オプションを使用してテーブルを作成する必要があります。このコマンドは、Azure のコマンド ライン インターフェイス (CLI) で使用されます。CLI の使い方の詳細については、「[モバイル サービス テーブルの管理用コマンド](../virtual-machines-command-line-tools.md#Mobile_Tables)」を参照してください。


### <a name="access-headers"></a>方法: カスタム パラメーターへのアクセスに関するページ

モバイル サービスに要求を送信する際に、要求の URI に 1 つ以上のカスタム パラメーターを含めることで、特定の要求の処理方法をテーブル操作スクリプトに指定できます。その後、処理パスを判断するためにパラメーターを調べるようにスクリプトを変更します。

たとえば、POST 要求の次の URI では、同じテキスト値を持つ新しい *TodoItem* の挿入を許可しないようにサービスに指示します。

		https://todolist.azure-mobile.net/tables/TodoItem?duplicateText=false

これらのカスタム クエリ パラメーターには、**request オブジェクト**の [parameters] プロパティから JSON 値としてアクセスします。**request** オブジェクトは、モバイル サービスからテーブル操作に登録されている関数に提供されます。次の挿入操作のサーバー スクリプトでは、挿入操作を実行する前に、`duplicateText` パラメーターの値を確認します。

		function insert(item, user, request) {
		    var todoItemTable = tables.getTable('TodoItem');
		    // Check the supplied custom parameter to see if
		    // we should allow duplicate text items to be inserted.		   
		    if (request.parameters.duplicateText === 'false') {
		        // Find all existing items with the same text
		        // and that are not marked 'complete'. 
		        todoItemTable.where({
		            text: item.text,
		            complete: false
		        }).read({
		            success: insertItemIfNotComplete
		        });
		    } else {
		        request.execute();
		    }

		    function insertItemIfNotComplete(existingItems) {
		        if (existingItems.length > 0) {
		            request.respond(statusCodes.CONFLICT, 
                        "Duplicate items are not allowed.");
		        } else {
		            // Insert the item as normal. 
		            request.execute();
		        }
		    }
		}

**insertItemIfNotComplete** では、重複するテキストが存在しない場合は、**request オブジェクト**の [execute] 関数を呼び出して、項目を挿入します。それ以外の場合は、**respond** 関数を呼び出して、重複についてクライアントに通知します。

前のコードでの **success** 関数への呼び出しの構文に注意してください。

 		        }).read({
		            success: insertItemIfNotComplete
		        });

JavaScript では、これは次のようなより長い構文の短縮版です。

		success: function(results) 
		{ 
			insertItemIfNotComplete(results); 
		}


### <a name="work-with-users"></a>方法: ユーザーを処理する

Azure Mobile Services では、ID プロバイダーを使用して、ユーザーを認証できます。詳細については、「[認証の使用]」を参照してください。認証されたユーザーがテーブル操作を呼び出したとき、モバイル サービスでは [user オブジェクト]を使用して、登録されたスクリプト関数にユーザーに関する情報を渡します。**userId** プロパティを使用すると、ユーザー固有の情報を保存および取得できます。次の例では、認証済みのユーザーの userId に基づいて、item の owner プロパティを設定します。

	function insert(item, user, request) {
	    item.owner = user.userId;
	    request.execute();
	}

次の例では、認証されたユーザーの **userId** に基づいて、query にフィルターを追加します。このフィルターでは、結果が現在のユーザーに属する項目のみに制限されます。

	function read(query, user, request) {
	    query.where({
	        owner: user.userId
	    });
	    request.execute();
	}

## <a name="custom-api"></a>カスタム API

このセクションでは、カスタム API エンドポイントを作成して操作する方法について説明します。次のセクションが含まれています。
	
+ [カスタム API の概要](#custom-api-overview)
+ [方法: カスタム API を定義する]
+ [方法: HTTP メソッドを実装する]
+ [方法: データを XML として送受信する]
+ [方法: カスタム API でユーザーとヘッダーを操作する]
+ [方法: カスタム API で複数のルートを定義する]

### <a name="custom-api-overview"></a>カスタム API の概要

カスタム API は、GET、POST、PUT、PATCH、DELETE という 1 つ以上の標準 HTTP メソッドによってアクセスされる、モバイル サービスのエンドポイントです。カスタム API によってサポートされている各 HTTP メソッドに個別の関数 export を定義し、すべてを 1 つのスクリプト ファイルに含めることができます。特定のメソッドを使用するカスタム API への要求が受信されると、登録されたスクリプトが呼び出されます。詳細については、「[カスタム API]」を参照してください。

カスタム API 関数がモバイル サービス ランタイムによって呼び出される場合は、[request オブジェクト][request object]と [response オブジェクト][response object]の両方が提供されます。これらのオブジェクトは、[express.js ライブラリ]の機能を公開します。その機能をスクリプトで利用できます。次の **hello** という名前のカスタム API は、非常に単純な例で、POST 要求に対して "_Hello, world!_" を返します。

		exports.post = function(request, response) {
		    response.send(200, "{ message: 'Hello, world!' }");
		} 

**response オブジェクト**の [send] 関数は、指定された応答をクライアントに返します。このコードは、次の URL への POST 要求が送信されたときに呼び出されます。

		https://todolist.azure-mobile.net/api/hello  

グローバル状態は実行間で維持されます。

### <a name="define-custom-api"></a>方法: カスタム API を定義する

次のいずれかの方法で、カスタム API エンドポイントの HTTP メソッドに登録されるサーバー スクリプトを定義できます。

+ [Azure 管理ポータル][Management Portal]で。カスタム API スクリプトは、**[API]** タブで作成および変更できます。サーバー スクリプト コードは、特定のカスタム API の **[スクリプト]** タブにあります。次の図は、`CompleteAll` というカスタム API エンドポイントへの POST 要求によって呼び出されるスクリプトを示しています。 

	![2][2]
	
	カスタム API メソッドに対するアクセス許可は、[アクセス許可] タブで割り当てます。このカスタム API がどのように作成されたかを確認するには、「[クライアントからのカスタム API 呼び出し]」を参照してください。

+ ソース管理を使用して。ソース管理を有効にしている場合は、単に git リポジトリの .\\service\\api サブフォルダーに <em>`<custom_api>`</em>.js という名前のファイルを作成します。ここで、<em>`<custom_api>`</em> は登録するカスタム API の名前です。このスクリプト ファイルには、カスタム API によって公開されている各 HTTP メソッドの _exported_ 関数が含まれています。アクセス許可は、付属 .json ファイルで定義されます。詳細については、「[ソース管理と共有コード][Source control, shared code, and helper functions]」を参照してください。

+ コマンド プロンプトから Azure コマンド ライン ツールを使用して。詳細については、「[コマンド ライン ツールの使用]」を参照してください。

### <a name="handle-methods"></a>方法: HTTP メソッドを実装する

カスタム API は、1 つ以上の HTTP メソッド (GET、POST、PUT、PATCH、および DELETE) を処理できます。カスタム API によって処理される HTTP メソッドごとに、エクスポートされる関数が定義されます。1 つのカスタム API コード ファイルは、次の関数の 1 つまたはすべてをエクスポートできます。

		exports.get = function(request, response) { ... };
		exports.post = function(request, response) { ... };
		exports.patch = function(request, response) { ... };
		exports.put = function(request, response) { ... };
		exports.delete = function(request, response) { ... };

カスタム API エンドポイントは、サーバー スクリプトで実装されていない HTTP メソッドを使用して呼び出すことはできず、405 (許可されていないメソッド) エラー応答が返されます。各サポート HTTP メソッドに個別のアクセス許可レベルを割り当てることができます。

### <a name="api-return-xml"></a>方法: データを XML として送受信する

クライアントがデータを格納および取得するときに、モバイル サービスは JavaScript Object Notation (JSON) を使用してメッセージ本体のデータを表現します。ただし、そうするのではなく、XML ペイロードを使用したいシナリオもあります。たとえば、Windows ストア アプリには、サービスに XML の発行を要求する、組み込みの定期的な通知機能があります。詳細については、「[Define a custom API that supports periodic notifications (定期的な通知をサポートするカスタム API の定義)]」を参照してください。

次の **OrderPizza** カスタム API 関数は、応答ペイロードとして単純な XML ドキュメントを返します。

		exports.get = function(request, response) {
		  response.set('content-type', 'application/xml');
		  var xml = '<?xml version="1.0"?><PizzaOrderForm><PizzaOrderForm/>';
		  response.send(200, xml);
		};

このカスタム API 関数は、次のエンドポイントへの HTTP GET 要求によって呼び出されます。

		https://todolist.azure-mobile.net/api/orderpizza

### <a name="get-api-user"></a>方法: カスタム API でユーザーとヘッダーを操作する

Azure Mobile Services では、ID プロバイダーを使用して、ユーザーを認証できます。詳細については、「[認証の使用]」を参照してください。認証されたユーザーがカスタム API を要求すると、モバイル サービスは [user オブジェクト]を使用して、カスタム API コードにユーザーに関する情報を提供します。[user オブジェクト]には、[request オブジェクト]の user プロパティからアクセスします。**userId** プロパティを使用すると、ユーザー固有の情報を保存および取得できます。

次の **OrderPizza** カスタム API 関数では、認証済みのユーザーの userId に基づいて、item の owner プロパティを設定します。

		exports.post = function(request, response) {
			var userTable = request.service.tables.getTable('user');
			userTable.lookup(request.user.userId, {
				success: function(userRecord) {
					callPizzaAPI(userRecord, request.body, function(orderResult) {
						response.send(201, orderResult);
					});
				}
			});
		
		};

このカスタム API 関数は、次のエンドポイントへの HTTP POST 要求によって呼び出されます。

		https://<service>.azure-mobile.net/api/orderpizza

次のコードに示すように、[request オブジェクト]から特定の HTTP ヘッダーにアクセスすることもできます。

		exports.get = function(request, response) {    
    		var header = request.header('my-custom-header');
    		response.send(200, "You sent: " + header);
		};

この単純な例は、`my-custom-header` という名前のカスタム ヘッダーを読み取り、応答内で値を返します。

### <a name="api-routes"></a>方法: カスタム API で複数のルートを定義する

モバイル サービスでは、カスタム API 内で複数のパス (ルート) を定義できます。たとえば、**calculator** カスタム API での次の URL への HTTP GET 要求は、それぞれ **add** 関数または **subtract** 関数を呼び出します。

+ `https://<service>.azure-mobile.net/api/calculator/add`
+ `https://<service>.azure-mobile.net/api/calculator/sub`

複数のルートは、**register** 関数をエクスポートすることによって定義されます。この関数は、ルートをカスタム API エンドポイントに登録するために使用される **api** オブジェクト ([express.js 内の express オブジェクト]に似たオブジェクト) を渡されます。次の例は、**calculator** カスタム API の **add** メソッドと **sub** メソッドを実装しています。

		exports.register = function (api) {
		    api.get('add', add);
		    api.get('sub', subtract);
		}
		
		function add(req, res) {
		    var result = parseInt(req.query.a) + parseInt(req.query.b);
		    res.send(200, { result: result });
		}
		
		function subtract(req, res) {
		    var result = parseInt(req.query.a) - parseInt(req.query.b);
		    res.send(200, { result: result });
		}

**register** 関数に渡された **api** オブジェクトは、各 HTTP メソッド (**get**、**post**、**put**、**patch**、**delete**) の関数を公開します。これらの関数は、特定の HTTP メソッド用に定義された関数にルートを登録します。各関数は 2 つのパラメーターを受け取ります。最初のパラメーターはルート名で、2 つ目のパラメーターはルートに登録される関数です。

前のカスタム API の例での 2 つのルートは、次のような HTTP GET 要求で呼び出すことができます (応答と共に示します)。

+ `https://<service>.azure-mobile.net/api/calculator/add?a=1&b=2`

		{"result":3}

+ `https://<service>.azure-mobile.net/api/calculator/sub?a=3&b=5`

		{"result":-2}

## <a name="scheduler-scripts"></a>ジョブ スケジューラ

モバイル サービスを使用すると、決まったスケジュールでのジョブまたは管理ポータルからのオンデマンドとして実行できるサーバー スクリプトを定義できます。スケジュールされたジョブは、テーブル データのクリーンアップやバッチ処理のような定期的なタスクを実行する場合に便利です。詳細については、「[ジョブのスケジュール]」を参照してください。

スケジュールされたジョブに登録されているスクリプトには、スケジュールされたジョブと同じ名前のメイン関数があります。スケジュールされたスクリプトは HTTP 要求によって呼び出されないため、サーバー ランタイムによって渡すことができるコンテキストがなく、関数はパラメーターを受け取りません。他の種類のスクリプトと同様に、サブルーティン関数を使用したり、共有モジュールを要求したりすることができます。詳細については、「[ソース管理、共有コード、およびヘルパー関数]」を参照してください。

### <a name="scheduler-scripts"></a>方法: スケジュールされたジョブ スクリプトを定義する

サーバー スクリプトは、モバイル サービス スケジューラで定義されたジョブに割り当てることができます。これらのスクリプトは、ジョブに属し、ジョブ スケジュールに従って実行されます([管理ポータル]を使用してジョブをオンデマンドで実行することもできます)。 スケジュールされたジョブを定義するスクリプトでは、モバイル サービスからデータが渡されないため、パラメーターはありません。このようなスクリプトは通常の JavaScript 関数として実行され、モバイル サービスと直接データをやり取りすることはありません。

スケジュールされたジョブは、次のいずれかの方法で定義できます。

+ [Azure 管理ポータル][Management Portal]のスケジューラの **[スクリプト]** タブで。

	![3][3]

	これを行う方法の詳細については、[モバイル サービスでのバックエンド ジョブの計画に関するページ]を参照してください。

+ コマンド プロンプトから Azure コマンド ライン ツールを使用して。詳細については、「[コマンド ライン ツールの使用]」を参照してください。

>[AZURE.NOTE]ソース管理を有効にしている場合は、git リポジトリの .\\service\\scheduler サブフォルダーにあるスケジュールされたジョブのスクリプト ファイルを直接編集できます。詳細については、「[方法: ソース管理を使用してコードを共有する]」を参照してください。

## <a name="shared-code"></a>ソース管理、共有コード、およびヘルパー関数

このセクションでは、ソース管理を活用して、カスタムの node.js モジュール、共有コードや他のコードの再利用計画を追加するする方法について説明します。次のセクションが含まれています。

+ [共有コードの活用の概要](#leverage-source-control)
+ [方法: Node.js モジュールを読み込む]
+ [方法: ヘルパー関数を使用する]
+ [方法: ソース管理を使用してコードを共有する]
+ [方法: アプリケーションの設定を操作する] 

### <a name="leverage-source-control"></a>共有コードの活用の概要

モバイル サービスはサーバーの Node.js を使用するため、スクリプトは既に組み込みの Node.js モジュールへのアクセス許可を持っています。ソース管理を使用して独自のモジュールを定義したり、他の Node.js モジュールをサービスに追加したりすることもできます。

以下に、グローバルな **require** 関数を使用することによってスクリプトで利用できるいくつかのより便利なモジュールを示します。

+ **azure**: Node.js に対して Azure SDK の機能を公開します。詳細については、「[Azure SDK for Node.js]」をご覧ください。 
+ **crypto**: OpenSSL の暗号化機能を提供します。詳細については、[Node.js に関するドキュメント][crypto API]を参照してください。
+ **path**: ファイル パスを操作するためのユーティリティが含まれています。詳細については、[Node.js に関するドキュメント][path API]を参照してください。
+ **querystring**: クエリ文字列を操作するためのユーティリティが含まれています。詳細については、[Node.js に関するドキュメント][querystring API]を参照してください。
+ **request**: Twitter や Facebook などの外部 REST サービスに HTTP 要求を送信します。詳細については、「[HTTP 要求の送信]」を参照してください。
+ **sendgrid**: Azure の Sendgrid 電子メール サービスを使用して、電子メールを送信します。詳細については、「[Send email from Mobile Services with SendGrid (SendGrid を使用したモバイル サービスからの電子メールの送信)]」を参照してください。
+ **url**: URL を解析および解決するためのユーティリティが含まれています。詳細については、[Node.js に関するドキュメント][url API]を参照してください。
+ **util**: 文字列の書式設定やオブジェクトの種類の確認など、さまざまなユーティリティが含まれています。詳細については、[Node.js に関するドキュメント][util API]を参照してください。 
+ **zlib**: gzip や deflate などの圧縮機能を公開します。詳細については、[Node.js に関するドキュメント][zlib API]を参照してください。 

### <a name="modules-helper-functions"></a>方法: モジュールを利用する

モバイル サービスでは、スクリプトで **require** グローバル関数を使用して読み込むことができる一連のモジュールを公開しています。たとえば、スクリプトから **request** で HTTP 要求を行うよう要求できます。

	function update(item, user, request) { 
	    var httpRequest = require('request'); 
	    httpRequest('http://www.google.com', function(err, response, body) { 
	    	... 
	    }); 
	} 


### <a name="shared-code-source-control"></a>方法: ソース管理を使用してコードを共有する

ソース管理で Node.js のパッケージ マネージャー (npm) を使用して、モバイル サービスで利用できるモジュールを制御することができます。この作業を実行する 2 つの方法があります。

+ npm によって発行およびインストールされるモジュールでは、package.json ファイルを使用して、モバイル サービスからインストールしようとするパッケージを宣言します。この方法で、サービスは常に、必要なパッケージの最新バージョンにアクセスできます。package.json ファイルは、`.\service` ディレクトリ内に存在しています。詳細については、「[Azure Mobile Services 内での package.json のサポート]」を参照してください。

+ プライベート モジュールまたはカスタム モジュールの場合は、npm を使用して、ソース管理の `.\service\node_modules` ディレクトリにモジュールを手動でインストールすることができます。モジュールを手動でアップロードする方法の例については、「[サーバー スクリプトで共有コードと Node.js モジュールを活用する]」を参照してください。

	>[AZURE.NOTE]ディレクトリ階層の中に `node_modules` ディレクトリが既に存在している場合、リポジトリに新しい `node_modules` が作成されるのではなく、その既存のディレクトリの中に `\node-uuid` サブディレクトリが作成されます。この場合、既存の `node_modules` ディレクトリを削除してください。

モバイル サービスに対応する package.json ファイルまたはカスタム モジュールをリポジトリにコミットした後、**require** を使用してそのモジュールを名前によって参照します。

>[AZURE.NOTE]package.json 内で指定するモジュール、またはモバイル サービスにアップロードするモジュールは、サーバー スクリプト コード内でのみ使用されます。これらのモジュールは、モバイル サービス ランタイムによって使用されることはありません。

### <a name="helper-functions"></a>方法: ヘルパー関数を使用する

モジュールを要求する方法に加えて、個々のサーバー スクリプトにヘルパー関数を含めることもできます。これらはメイン関数から分離された関数であり、スクリプト内のコードを分割するために使用できます。

次の例では、テーブル スクリプトが挿入操作に登録されます。この挿入操作に、ヘルパー関数 **handleUnapprovedItem** が含まれています。


	function insert(item, user, request) {
	    if (!item.approved) {
	        handleUnapprovedItem(item, user, request);
	    } else {
	        request.execute();
	    }
	}
	
	function handleUnapprovedItem(item, user, request) {
	    // Do something with the supplied item, user, or request objects.
	}
 
スクリプトでは、メインの関数の後にヘルパー関数を宣言する必要があります。スクリプトでは、すべての変数を定義する必要があります。宣言されていない変数があると、エラーが発生します。

ヘルパー関数は、1 回だけ定義して、複数のサーバー スクリプト間で共有することもできます。スクリプト間で関数を共有するには、関数をエクスポートし、スクリプト ファイルを `.\service\shared` ディレクトリに保存する必要があります。次に示すのは、ファイル `.\services\shared\helpers.js` 内の共有関数をエクスポートするためのテンプレートです。

		exports.handleUnapprovedItem = function (tables, user, callback) {
		    
		    // Do something with the supplied tables or user objects and 
			// return a value to the callback function.
		};
 
その後は、テーブル操作スクリプトで次のようにして関数を使用することができます。

		function insert(item, user, request) {
		    var helper = require('../shared/helper');
		    helper.handleUnapprovedItem(tables, user, function(result) {
		        	
					// Do something based on the result.
		            request.execute();
		        }
		    }
		}

この例では、[tables オブジェクト]と [user オブジェクト]の両方を共有関数に渡す必要があります。これは、共有スクリプトがグローバルな [tables オブジェクト]にアクセスできず、[user オブジェクト]は要求のコンテキストだけに存在するためです。

スクリプト ファイルは、[ソース管理][How to: Share code by using source control]を使用するか、[コマンド ライン ツール][Using the command line tool]を使用して、共有ディレクトリにアップロードされます。

### <a name="app-settings"></a>方法: アプリケーションの設定を操作する

モバイル サービスを使用すると、値をアプリケーション設定として安全に格納できます。アプリケーション設定には、実行時にサーバー スクリプトからアクセスできます。モバイル サービスのアプリケーション設定にデータを追加すると、名前/値ペアが暗号化されて格納され、サーバー スクリプトでそれらにアクセスできます。スクリプト ファイル内でそれらをハード コーディングする必要はありません。詳細については、「[アプリ設定]」を参照してください。

次のカスタム API の例は、指定された [service オブジェクト]を使用してアプリケーション設定値を取得します。

		exports.get = function(request, response) {
		
			// Get the MY_CUSTOM_SETTING value from app settings.
		    var customSetting = 
		        request.service.config.appSettings.my_custom_setting;
				
			// Do something and then send a response.

		}

次のコードは構成モジュールを使用して、アプリケーション設定に格納されている Twitter アクセス トークン値を取得します。この値が、スケジュールされたジョブのスクリプトで使用されます。

		// Get the service configuration module.
		var config = require('mobileservice-config');

		// Get the stored Twitter consumer key and secret. 
		var consumerKey = config.twitterConsumerKey,
		    consumerSecret = config.twitterConsumerSecret
		// Get the Twitter access token from app settings.    
		var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
		    accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;

このコードは、ポータルの **[ID]** タブに格納されている Twitter コンシューマー キー値も取得します。**config オブジェクト**はテーブル操作とスケジュールされたジョブ スクリプトでは使用できないため、アプリケーション設定にアクセスするには構成モジュールを要求する必要があります。完全な例については、「[モバイル サービスでのバックエンド ジョブの計画]」を参照してください。

<h2><a name="command-prompt"></a>コマンド ライン ツールの使用</h2>

Mobile Services では、Azure コマンド ライン ツールを使用してサーバー スクリプトを作成、変更、および削除できます。スクリプトをアップロードする前に、次のディレクトリ構造を使用していることを確認してください。

![4][4]

このディレクトリ構造は、ソース管理を使用する場合の git リポジトリと同じです。

コマンド ライン ツールでスクリプト ファイルをアップロードする場合は、まず、`.\services` ディレクトリに移動する必要があります。次のコマンドは `table` サブディレクトリから `todoitem.insert.js` という名前のスクリプトをアップロードします。

		~$azure mobile script upload todolist table/todoitem.insert.js
		info:    Executing command mobile script upload
		info:    mobile script upload command OK

次のコマンドは、モバイル サービスで管理されているすべてのスクリプト ファイルについての情報を返します。

		~$ azure mobile script list todolist
		info:    Executing command mobile script list
		+ Retrieving script information
		info:    Table scripts
		data:    Name                       Size
		data:    -------------------------  ----
		data:    table/channels.insert      1980
		data:    table/TodoItem.insert      5504
		data:    table/TodoItem.read        64
		info:    Shared scripts
		data:    Name              Size
		data:    ----------------  ----
		data:    shared/helper.js  62
		data:    shared/uuid.js    7452
		info:    Scheduled job scripts
		data:    Job name    Script name           Status    Interval     Last run  Next run
		data:    ----------  --------------------  --------  -----------  --------  --------
		data:    getUpdates  scheduler/getUpdates  disabled  15 [minute]  N/A       N/A
		info:    Custom API scripts
		data:    Name                    Get          Put          Post         Patch        Delete
		data:    ----------------------  -----------  -----------  -----------  -----------  -----------
		data:    completeall             application  application  application  application  application
		data:    register_notifications  application  application  user         application  application
		info:    mobile script list command OK

詳細については、「[Azure Mobile Services の管理用コマンド]」を参照してください。

## <a name="working-with-tables"></a>テーブルの操作

このセクションでは、SQL データベース テーブルのデータを直接操作する方法について説明します。次のセクションが含まれています。

+ [テーブルの操作の概要](#overview-tables)
+ [方法: スクリプトからテーブルにアクセスする]
+ [方法: 一括挿入を実行する]
+ [方法: JSON 型をデータベース型にマッピングする]
+ [テーブルにアクセスするための Transact-SQL の使用]

### <a name="overview-tables"></a>テーブルの操作の概要

モバイル サービスの多くのシナリオでは、サーバー スクリプトがデータベース内のテーブルにアクセスする必要があります。たとえば、モバイル サービスはスクリプトの実行間で状態を保持しないため、スクリプトの実行間で保持する必要があるデータはテーブルに格納しておかなければなりません。アクセス許可テーブルのエントリを調べたり、監査データを単にログに書き込むだけでなくテーブルに保存したりする場合もあります (ログではデータの保存期間に限定があり、プログラムでアクセスすることができません)。

モバイル サービスには、テーブルにアクセスするための 2 つの方法があります。[table オブジェクト] プロキシを使用する方法と、[mssql オブジェクト]を使用して Transact-SQL クエリを作成する方法です。[table オブジェクト]を使用するとサーバー スクリプト コードからテーブル データに簡単にアクセスできるようになりますが、[mssql オブジェクト]はより複雑なデータ操作をサポートしており、最高レベルの柔軟性を提供します。

### <a name="access-tables"></a>方法: スクリプトからテーブルにアクセスする

スクリプトからテーブルにアクセスする最も簡単な方法は、[tables オブジェクト]を使用する方法です。**getTable** 関数では、要求されたテーブルにアクセスするためのプロキシである [table オブジェクト] インスタンスが返されます。その後、プロキシで関数を呼び出すことで、データにアクセスして変更できます。

テーブル操作とスケジュールされたジョブに登録されたスクリプトは、グローバル オブジェクトとして [tables オブジェクト]にアクセスできます。次のコード行では、グローバル *tables オブジェクト*から [TodoItems] テーブルのプロキシを取得します。

		var todoItemsTable = tables.getTable('TodoItems');

カスタム API スクリプトは、指定された [request オブジェクト]の <strong>service</strong> プロパティから [tables オブジェクト]にアクセスできます。次のコード行では、要求から [tables オブジェクト]を取得します。

		var todoItemsTable = request.service.tables.getTable('TodoItem');

> [AZURE.NOTE]共有関数は、**tables** オブジェクトに直接アクセスできません。共有関数内で tables オブジェクトを関数に渡す必要があります。

[table オブジェクト]を一度取得すると、1 つ以上のテーブル操作関数 (挿入、更新、削除、または読み取り) を呼び出すことができます。次の例では、permissions テーブルからユーザーのアクセス許可を読み取ります。

	function insert(item, user, request) {
		var permissionsTable = tables.getTable('permissions');
	
		permissionsTable
			.where({ userId: user.userId, permission: 'submit order'})
			.read({ success: checkPermissions });
			
		function checkPermissions(results) {
			if(results.length > 0) {
				// Permission record was found. Continue normal execution.
				request.execute();
			} else {
				console.log('User %s attempted to submit an order without permissions.', user.userId);
				request.respond(statusCodes.FORBIDDEN, 'You do not have permission to submit orders.');
			}
		}
	}

次の例では、**audit** テーブルに監査情報を書き込みます。

	function update(item, user, request) {
		request.execute({ success: insertAuditEntry });
		
		function insertAuditEntry() {
			var auditTable = tables.getTable('audit');
			var audit = {
				record: 'checkins',
				recordId: item.id,
				timestamp: new Date(),
				values: JSON.stringify(item)
			};
			auditTable.insert(audit, {
				success: function() {
					// Write to the response now that all data operations are complete
					request.respond();
				}
			});
		}
	}

コード サンプル内にある最後の例は、「[方法: カスタム パラメーターにアクセスする][How to: Add custom parameters]」に掲載されています。

### <a name="bulk-inserts"></a>方法: 一括挿入を実行する

厳密な **for** ループまたは **while** ループを使用して多数の項目 (1,000 個など) をテーブルに挿入すると、SQL 接続数の上限に達して一部の挿入に失敗する場合があります。要求が完了しないか、HTTP 500 内部サーバー エラーが返されます。この問題を回避するには、一度に 10 個程度の項目をバッチとして挿入します。1 つのバッチが挿入されるまで待ってから、次のバッチを挿入するようにします。

次のスクリプトを使用すると、レコードのバッチのサイズを設定して、並列挿入することができます。レコードの数は大きくしないことをお勧めします。非同期挿入バッチが完了すると、**insertItems** 関数が自身を再帰的に呼び出します。末尾の for ループでは一度に 1 個のレコードを挿入し、成功時には **insertComplete** を呼び出し、失敗時には **errorHandler** を呼び出します。**insertComplete** によって、次のバッチで **insertItems** を再帰的に呼び出すか、ジョブを完了してスクリプトを終了するかを制御しています。

		var todoTable = tables.getTable('TodoItem');
		var recordsToInsert = 1000;
		var batchSize = 10; 
		var totalCount = 0;
		var errorCount = 0; 
		
		function insertItems() {        
		    var batchCompletedCount = 0;  
		
		    var insertComplete = function() { 
		        batchCompletedCount++; 
		        totalCount++; 
		        if(batchCompletedCount === batchSize || totalCount === recordsToInsert) {                        
		            if(totalCount < recordsToInsert) {
		                // kick off the next batch 
		                insertItems(); 
		            } else { 
		                // or we are done, report the status of the job 
		                // to the log and don't do any more processing 
		                console.log("Insert complete. %d Records processed. There were %d errors.", totalCount, errorCount); 
		            } 
		        } 
		    }; 
		
		    var errorHandler = function(err) { 
		        errorCount++; 
		        console.warn("Ignoring insert failure as part of batch.", err); 
		        insertComplete(); 
		    };
		
		    for(var i = 0; i < batchSize; i++) { 
		        var item = { text: "This is item number: " + totalCount + i }; 
		        todoTable.insert(item, { 
		            success: insertComplete, 
		            error: errorHandler 
		        }); 
		    } 
		} 
		
		insertItems(); 


コード サンプルの全体と詳細な説明については、この[ブログの投稿](http://blogs.msdn.com/b/jpsanders/archive/2013/03/20/server-script-to-insert-table-items-in-windows-azure-mobile-services.aspx)を参照してください。このコードを使用する際には、使用する状況に合わせて修正したり、徹底的にテストしたりできます。

### <a name="JSON-types"></a>方法: JSON 型をデータベース型にマッピングする

クライアントとモバイル サービス データベース テーブルでは、データ型のコレクションが異なります。データ型を簡単にマッピングできる場合もあれば、難しい場合もあります。モバイル サービスでは、マッピングによってさまざまな型変換を実行します。

- クライアントの言語固有の型は JSON にシリアル化されます。
- JSON の表現は JavaScript に変換された後でサーバー スクリプトに使用されます。
- JavaScript のデータ型は、[tables オブジェクト]を使用して保存されるときに SQL データベースの型に変換されます。

クライアント スキーマから JSON への変換方法は、プラットフォームによって異なります。Windows ストア クライアントと Windows Phone クライアントでは、JSON.NET が使用されます。Android クライアントでは gson ライブラリが使用されます。iOS クライアントでは NSJSONSerialization クラスが使用されます。このようなライブラリでは既定のシリアル化動作が使用されます。ただし、日付のオブジェクトを、ISO 8601 を使用してエンコードされた日付が含まれている JSON 文字列に変換する場合は例外です。

[insert 関数]、[update 関数]、[read 関数]、または [delete 関数]を使用したサーバー スクリプトを作成する場合、データの JavaScript 表現にアクセスすることができます。モバイル サービスでは Node.js の逆シリアル化関数 ([JSON.parse](http://es5.github.io/#x15.12)) を使用して、ネットワーク上で JSON を JavaScript オブジェクトに変換します。ただし、ISO 8601 文字列から **Date** オブジェクトを取得する際には、変換を実行します。

[tables オブジェクト]または [mssql オブジェクト]を使用するとき、または単純にテーブル スクリプトを実行するときに、逆シリアル化された JavaScript オブジェクトが SQL データベースに挿入されます。このプロセスでは、オブジェクトのプロパティが T-SQL 型にマッピングされます。

<table border="1">
<tr>
<td>JavaScript のプロパティ</td>
<td>T-SQL 型</td>
</tr><tr>
<td>Number</td>
<td>Float(53)</td>
</tr><tr>
<td>Boolean</td>
<td>Bit</td>
</tr><tr>
<td>Date</td>
<td>DateTimeOffset(3)</td>
</tr>
<tr>
<td>String</td>
<td>Nvarchar(max)</td>
</tr>
<tr>
<td>Buffer</td>
<td>サポートされていません</td>
</tr><tr>
<td>オブジェクト</td>
<td>サポートされていません</td>
</tr><tr>
<td>Array</td>
<td>サポートされていません</td>
</tr><tr>
<td>Stream</td>
<td>サポートされていません</td>
</tr>
</table>

### <a name="TSQL"></a>テーブルにアクセスするための Transact-SQL の使用

サーバー スクリプトからテーブル データを操作する最も簡単な方法は、[table オブジェクト] プロキシを使用する方法です。ただし、[table オブジェクト]ではサポートされていない、より高度なシナリオがあります。たとえば、結合クエリやその他の複雑なクエリや、ストアド プロシージャの呼び出しです。このような場合は、[mssql オブジェクト]を使用して、リレーショナル テーブルに対して Transact-SQL ステートメントを直接実行する必要があります。このオブジェクトには、以下の関数が用意されています。

- **query**: TSQL 文字列で指定されたクエリを実行します。結果は **options** オブジェクトに対する **success** コールバックに返されます。*params* パラメーターがある場合、クエリにパラメーターを含めることができます。
- **queryRaw**: *query* と同様ですが、クエリから返される結果セットが `未加工` 形式である点が異なります (以下の例を参照)。
- **open**: Mobile Services データベースへの接続を取得する場合に使用します。この connection オブジェクトを使用して、トランザクションなどのデータベース操作を呼び出すことができます。

以下の方法は、下のものほど、クエリ処理に対して低レベルの制御を行うことができます。

+ [方法: 静的クエリを実行する]
+ [方法: 動的クエリを実行する]
+ [方法: リレーショナル テーブルを結合する]
+ [方法: *未加工*の結果を返すクエリを実行する]
+ [方法: データベース接続へのアクセスを取得する]	

#### <a name="static-query"></a>方法: 静的クエリを実行する

次のクエリにはパラメーターがなく、`statusupdate` テーブルから 3 つのレコードを返します。行セットは標準の JSON 形式です。

		mssql.query('select top 3 * from statusupdates', {
		    success: function(results) {
		        console.log(results);
		    },
            error: function(err) {
                console.log("error is: " + err);
			}
		});


#### <a name="dynamic-query"></a>方法: 動的なパラメーター化されたクエリを実行する

次の例は、permissions テーブルから各ユーザーのアクセス許可を読み取ることによってカスタム承認を実装しています。プレースホルダー (?) は、クエリが実行されるときに、指定されたパラメーターに置き換えられます。

		    var sql = "SELECT _id FROM permissions WHERE userId = ? AND permission = 'submit order'";
		    mssql.query(sql, [user.userId], {
		        success: function(results) {
		            if (results.length > 0) {
		                // Permission record was found. Continue normal execution. 
		                request.execute();
		            } else {
		                console.log('User %s attempted to submit an order without permissions.', user.userId);
		                request.respond(statusCodes.FORBIDDEN, 'You do not have permission to submit orders.');
		            }
		        },
            	error: function(err) {
                	console.log("error is: " + err);
				}	
		    });


#### <a name="joins"></a>方法: リレーショナル テーブルを結合する

**mssql オブジェクト**の [query] メソッドを使用して、結合を実装する TSQL コードを渡すことで、2 個のテーブルを結合できます。**ToDoItem** テーブルにいくつかの項目があり、各項目には **priority** プロパティがあって、テーブルの列に対応しているとします。項目は次のようになります。

		{ text: 'Take out the trash', complete: false, priority: 1}

また、**Priority** という名前の追加のテーブルがあり、優先度の**番号**とテキストの**説明**を含む行があるとします。たとえば、優先度番号 1 の説明は "Critical" で、オブジェクトは次のようになります。

		{ number: 1, description: 'Critical'}

ここで、項目の**優先度**番号を、そのテキスト説明に置き換えることができます。そうするには、2 つのテーブルのリレーショナル結合を使用します。

		mssql.query('SELECT t.text, t.complete, p.description FROM ToDoItem as t INNER JOIN Priority as p ON t.priority = p.number', {
			success: function(results) {
				console.log(results);
			},
            error: function(err) {
                console.log("error is: " + err);
		});
	
スクリプトは 2 つのテーブルを結合し、結果をログに書き込みます。結果のオブジェクトは、次のようになります。

		{ text: 'Take out the trash', complete: false, description: 'Critical'}


#### <a name="raw"></a>方法: *未加工*の結果を返すクエリを実行する

この例は、前の例と同様にクエリを実行しますが、結果セットを "未加工" の形式で返します。そのため、行ごと、列ごとに解析する必要があります。このようなシナリオとして考えられるのは、モバイル サービスでサポートされていないデータ型にアクセスする必要がある場合です。次のコードは、単に出力をコンソール ログに書き込むので、未加工形式を調べることができます。

		mssql.queryRaw('SELECT * FROM ToDoItem', {
		    success: function(results) {
		        console.log(results);
		    },
            error: function(err) {
                console.log("error is: " + err);
			}
		});

このクエリを実行したときの出力を次に示します。テーブルの各列についてのメタデータと、行および列の表現が含まれます。

		{ meta: 
		   [ { name: 'id',
		       size: 19,
		       nullable: false,
		       type: 'number',
		       sqlType: 'bigint identity' },
		     { name: 'text',
		       size: 0,
		       nullable: true,
		       type: 'text',
		       sqlType: 'nvarchar' },
		     { name: 'complete',
		       size: 1,
		       nullable: true,
		       type: 'boolean',
		       sqlType: 'bit' },
		     { name: 'priority',
		       size: 53,
		       nullable: true,
		       type: 'number',
		       sqlType: 'float' } ],
		  rows: 
		   [ [ 1, 'good idea for the future', null, 3 ],
		     [ 2, 'this is important but not so much', null, 2 ],
		     [ 3, 'fix this bug now', null, 0 ],
		     [ 4, 'we need to fix this one real soon now', null, 1 ],
		   ] }

#### <a name="connection"></a>方法: データベース接続へのアクセスを取得する

**open** メソッドを使用して、データベース接続にアクセスできます。このようにする理由の 1 つとして考えられるのは、データベース トランザクションを使用する必要がある場合です。

**open** の実行が成功すると、データベース接続がパラメーターとして **success** 関数に渡されます。**connection** オブジェクトに対して呼び出すことができる関数は、*close*、*queryRaw*、*query*、*beginTransaction*、*commit*、および *rollback* です。

		    mssql.open({
		        success: function(connection) {
		            connection.query(//query to execute);
		        },
	            error: function(err) {
	                console.log("error is: " + err);
				}
		    });

## <a name="debugging"></a>デバッグおよびトラブルシューティング

サーバー スクリプトをデバッグおよびトラブルシューティングするための主な方法は、サービス ログへの書き込みです。既定では、モバイル サービスは、サービス スクリプトの実行中に発生したエラーをサービス ログに書き込みます。また、スクリプトでログに書き込むこともできます。ログへの書き込みは、スクリプトをデバッグし、適切に動作しているかどうかを検証するための優れた方法です。

### <a name="write-to-logs"></a>方法: 出力をモバイル サービス ログに書き込む

ログに書き込むには、グローバル [console オブジェクト]を使用します。情報レベルの警告をログに記録するには、**log** 関数または **info** 関数を使用します。**warning** 関数および **error** 関数では、それぞれのレベルをログに記録します。これらは、ログ内で強調されます。

> [AZURE.NOTE]モバイル サービスのログを表示するには、[管理ポータル](https://manage.windowsazure.com/)にログオンし、モバイル サービスを選択して、**[ログ]** タブをクリックします。

また、[console オブジェクト]のログ関数で、パラメーターを使用してメッセージを書式設定することもできます。次の例では、メッセージ文字列のパラメーターとして JSON オブジェクトを指定します。

	function insert(item, user, request) {
	    console.log("Inserting item '%j' for user '%j'.", item, user);  
	    request.execute();
	}

文字列 `%j` は JSON オブジェクトのプレースホルダーとして使用されており、パラメーターを順番に指定していることに注意してください。

ログが過大になることを避けるには、運用環境で使用する必要がない console.log() への呼び出しを削除または無効にする必要があります。

<!-- Anchors. -->
[Introduction]: #intro
[Table operations]: #table-scripts
[Basic table operations]: #basic-table-ops
[方法: テーブル操作に登録する]: #register-table-scripts
[How to: Define table scripts]: #execute-operation
[方法: 既定の応答をオーバーライドする]: #override-response
[How to: Modify an operation]: #modify-operation
[How to: Override success and error]: #override-success-error
[方法: execute success をオーバーライドする]: #override-success
[方法: 既定のエラー処理をオーバーライドする]: #override-error
[方法: スクリプトからテーブルにアクセスする]: #access-tables
[How to: Add custom parameters]: #access-headers
[方法: カスタム パラメーターを追加する]: #access-headers
[How to: Work with users]: #work-with-users
[How to: Define scheduled job scripts]: #scheduler-scripts
[How to: Refine access to tables]: #authorize-tables
[テーブルにアクセスするための Transact-SQL の使用]: #TSQL
[方法: 静的クエリを実行する]: #static-query
[方法: 動的クエリを実行する]: #dynamic-query
[方法: *未加工*の結果を返すクエリを実行する]: #raw
[方法: データベース接続へのアクセスを取得する]: #connection
[方法: リレーショナル テーブルを結合する]: #joins
[方法: 一括挿入を実行する]: #bulk-inserts
[方法: JSON 型をデータベース型にマッピングする]: #JSON-types
[方法: Node.js モジュールを読み込む]: #modules-helper-functions
[How to: Write output to the mobile service logs]: #write-to-logs
[Source control, shared code, and helper functions]: #shared-code
[ソース管理、共有コード、およびヘルパー関数]: #shared-code
[Using the command line tool]: #command-prompt
[コマンド ライン ツールの使用]: #command-prompt
[Working with tables]: #working-with-tables
[Custom API anchor]: #custom-api
[方法: カスタム API を定義する]: #define-custom-api
[How to: Share code by using source control]: #shared-code-source-control
[方法: ソース管理を使用してコードを共有する]: #shared-code-source-control
[方法: ヘルパー関数を使用する]: #helper-functions
[Debugging and troubleshooting]: #debugging
[方法: HTTP メソッドを実装する]: #handle-methods
[方法: カスタム API でユーザーとヘッダーを操作する]: #get-api-user
[How to: Access custom API request headers]: #get-api-headers
[Job Scheduler]: #scheduler-scripts
[方法: カスタム API で複数のルートを定義する]: #api-routes
[方法: データを XML として送受信する]: #api-return-xml
[方法: アプリケーションの設定を操作する]: #app-settings

[1]: ./media/mobile-services-how-to-use-server-scripts/1-mobile-insert-script-users.png
[2]: ./media/mobile-services-how-to-use-server-scripts/2-mobile-custom-api-script.png
[3]: ./media/mobile-services-how-to-use-server-scripts/3-mobile-schedule-job-script.png
[4]: ./media/mobile-services-how-to-use-server-scripts/4-mobile-source-local-cli.png

<!-- URLs. -->
[Mobile Services server script reference (モバイル サービスのサーバー スクリプト リファレンス)]: http://msdn.microsoft.com/library/windowsazure/jj554226.aspx
[モバイル サービスでのバックエンド ジョブの計画]: /develop/mobile/tutorials/schedule-backend-tasks/
[モバイル サービスでのバックエンド ジョブの計画に関するページ]: /develop/mobile/tutorials/schedule-backend-tasks/
[request object]: http://msdn.microsoft.com/library/windowsazure/jj554218.aspx
[execute]: http://msdn.microsoft.com/library/windowsazure/jj554218.aspx
[parameters]: http://msdn.microsoft.com/library/windowsazure/jj554218.aspx
[request]: http://msdn.microsoft.com/library/windowsazure/jj554218.aspx
[request オブジェクト]: http://msdn.microsoft.com/library/windowsazure/jj554218.aspx
[response object]: http://msdn.microsoft.com/library/windowsazure/dn303373.aspx
[send]: http://msdn.microsoft.com/library/windowsazure/dn303373.aspx
[User object]: http://msdn.microsoft.com/library/windowsazure/jj554220.aspx
[user オブジェクト]: http://msdn.microsoft.com/library/windowsazure/jj554220.aspx
[push object]: http://msdn.microsoft.com/library/windowsazure/jj554217.aspx
[insert function]: http://msdn.microsoft.com/library/windowsazure/jj554229.aspx
[insert 関数]: http://msdn.microsoft.com/library/windowsazure/jj554229.aspx
[update function]: http://msdn.microsoft.com/library/windowsazure/jj554214.aspx
[delete function]: http://msdn.microsoft.com/library/windowsazure/jj554215.aspx
[read function]: http://msdn.microsoft.com/library/windowsazure/jj554224.aspx
[update 関数]: http://msdn.microsoft.com/library/windowsazure/jj554214.aspx
[delete 関数]: http://msdn.microsoft.com/library/windowsazure/jj554215.aspx
[read 関数]: http://msdn.microsoft.com/library/windowsazure/jj554224.aspx
[query object]: http://msdn.microsoft.com/library/windowsazure/jj613353.aspx
[query オブジェクト]: http://msdn.microsoft.com/library/windowsazure/jj613353.aspx
[apns object]: http://msdn.microsoft.com/library/windowsazure/jj839711.aspx
[mpns object]: http://msdn.microsoft.com/library/windowsazure/jj871025.aspx
[wns object]: http://msdn.microsoft.com/library/windowsazure/jj860484.aspx
[table オブジェクト]: http://msdn.microsoft.com/library/windowsazure/jj554210.aspx
[TodoItems]: http://msdn.microsoft.com/library/windowsazure/jj614364.aspx
[tables オブジェクト]: http://msdn.microsoft.com/library/windowsazure/jj614364.aspx
[mssql オブジェクト]: http://msdn.microsoft.com/library/windowsazure/jj554212.aspx
[query]: http://msdn.microsoft.com/library/windowsazure/jj554212.aspx
[console オブジェクト]: http://msdn.microsoft.com/library/windowsazure/jj554209.aspx
[データの読み取りと書き込み]: http://msdn.microsoft.com/library/windowsazure/jj631640.aspx
[データの検証]: http://msdn.microsoft.com/library/windowsazure/jj631638.aspx
[要求の変更]: http://msdn.microsoft.com/library/windowsazure/jj631635.aspx
[応答の変更]: http://msdn.microsoft.com/library/windowsazure/jj631631.aspx
[Management Portal]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[ジョブのスケジュール]: http://msdn.microsoft.com/library/windowsazure/jj860528.aspx
[サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/
[Azure Mobile Services の管理用コマンド]: ../virtual-machines-command-line-tools.md#Mobile_Scripts
[Windows Store Push]: /develop/mobile/tutorials/get-started-with-push-dotnet/
[Windows Phone Push]: /develop/mobile/tutorials/get-started-with-push-wp8/
[iOS Push]: /develop/mobile/tutorials/get-started-with-push-ios/
[Android Push]: /develop/mobile/tutorials/get-started-with-push-android/
[Azure SDK for Node.js]: http://go.microsoft.com/fwlink/p/?LinkId=275539
[HTTP 要求の送信]: http://msdn.microsoft.com/library/windowsazure/jj631641.aspx
[Send email from Mobile Services with SendGrid (SendGrid を使用したモバイル サービスからの電子メールの送信)]: /develop/mobile/tutorials/send-email-with-sendgrid/
[認証の使用]: http://go.microsoft.com/fwlink/p/?LinkId=287177
[crypto API]: http://go.microsoft.com/fwlink/p/?LinkId=288802
[path API]: http://go.microsoft.com/fwlink/p/?LinkId=288803
[querystring API]: http://go.microsoft.com/fwlink/p/?LinkId=288804
[url API]: http://go.microsoft.com/fwlink/p/?LinkId=288805
[util API]: http://go.microsoft.com/fwlink/p/?LinkId=288806
[zlib API]: http://go.microsoft.com/fwlink/p/?LinkId=288807
[カスタム API]: http://msdn.microsoft.com/library/windowsazure/dn280974.aspx
[クライアントからのカスタム API 呼び出し]: /develop/mobile/tutorials/call-custom-api-dotnet/#define-custom-api
[express.js ライブラリ]: http://go.microsoft.com/fwlink/p/?LinkId=309046
[Define a custom API that supports periodic notifications (定期的な通知をサポートするカスタム API の定義)]: /develop/mobile/tutorials/create-pull-notifications-dotnet/
[express.js 内の express オブジェクト]: http://expressjs.com/api.html#express
[Store server scripts in source control]: /develop/mobile/tutorials/store-scripts-in-source-control/
[サーバー スクリプトで共有コードと Node.js モジュールを活用する]: /develop/mobile/tutorials/store-scripts-in-source-control/#use-npm
[service オブジェクト]: http://msdn.microsoft.com/library/windowsazure/dn303371.aspx
[アプリ設定]: http://msdn.microsoft.com/library/dn529070.aspx
[config module]: http://msdn.microsoft.com/library/dn508125.aspx
[Azure Mobile Services 内での package.json のサポート]: http://go.microsoft.com/fwlink/p/?LinkId=391036

<!--HONumber=54--> 