<properties urlDisplayName="HTML Client" pageTitle="HTML クライアントの使用方法 - Azure Mobile Services" metaKeywords="Azure Mobile Services, Mobile Service HTML client, HTML client" description="Learn how to use an HTML client for Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="How to use an HTML/JavaScript client for Azure Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />


# Azure モバイル サービス向け HTML/JavaScript クライアントを使用する方法

<div class="dev-center-tutorial-selector sublanding">
  <a href="/ja-jp/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/ja-jp/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript" class="current">HTML/JavaScript</a><a href="/ja-jp/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/ja-jp/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>


このガイドでは、Azure のモバイル サービス向け HTML/JavaScript クライアントを使用して一般的なシナリオを実行する方法について説明します。紹介するシナリオは、データの照会、挿入、更新、および削除、ユーザーの認証、エラー処理などです。Mobile Services を初めて使用する場合は、まず Mobile Services の [Windows ストア JavaScript に関するクイック スタート]または [HTML に関するクイック スタート]を完了することを検討してください。このクイック スタート チュートリアルでは、アカウントを構成し、初めてのモバイル サービスを作成します。


## 目次

- [Mobile Services とは]
- [概念]
- [方法: Mobile Services クライアントを作成する]
- [方法: モバイル サービスのデータを照会する]
	- [返されるデータをフィルター処理する]
    - [返されるデータを並べ替える]
	- [ページにデータを返す]
	- [特定の列を選択する]
	- [ID でデータを検索する]
	- [OData クエリ操作を実行する]
- [方法: モバイル サービスにデータを挿入する]
- [方法: モバイル サービスのデータを変更する]
- [方法: モバイル サービスのデータを削除する]
- [方法: ユーザー インターフェイスにデータを表示する]
- [方法: ユーザーを認証する]
- [方法: エラーを処理する]
- [方法: promise を使用する]
- [方法: 要求ヘッダーをカスタマイズする]
- [方法: クロス オリジン リソース共有を使用する]
- [次のステップ]

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

##<a name="create-client"></a>方法:モバイル サービス クライアントを作成する



Web エディターで、HTML ファイルを開き、次のコードをページのスクリプト参照に追加します。

    <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

>[WACOM.NOTE]JavaScript/HTML で記述された Windows ストア アプリでは、プロジェクトに **WindowsAzure.MobileServices.WinJS** NuGet パッケージを追加する必要があります。

エディターで、JavaScript ファイルを開くか作成し、`MobileServiceClient` 変数を定義している次のコードを追加します。さらに、モバイル サービスからのアプリケーション URL とアプリケーション キーを `MobileServiceClient` コンストラクターに順に指定します。

	var MobileServiceClient = WindowsAzure.MobileServiceClient;
    var client = new MobileServiceClient('AppUrl', 'AppKey');

プレースホルダーの `AppUrl` と `AppKey` を、モバイル サービスのアプリケーション URL とアプリケーション キーでそれぞれ置き換える必要があります。モバイル サービスのアプリケーション URL とアプリケーション キーを取得する方法については、「[Windows ストア JavaScript でのデータの使用]」または「[HTML/JavaScript でのデータの使用]」を参照してください。

##<a name="querying"></a>方法:モバイル サービスのデータを照会する

SQL Database テーブルのデータにアクセスまたは変更するすべてのコードは、`MobileServiceTable` オブジェクトの関数を呼び出します。`MobileServiceClient` のインスタンスで `getTable()` 関数を呼び出して、テーブルへの参照を取得します。

    var todoItemTable = client.getTable('todoitem');


### <a name="filtering"></a>方法:返されるデータをフィルター処理する

次のコードは、クエリに where 句を含めることによってデータをフィルター処理する方法を示しています。このコードは、`todoItemTable` から、complete フィールドが `false` に等しいすべての項目を返します。`todoItemTable` は、既に作成したモバイル サービス テーブルへの参照です。where 関数は、行のフィルタリング述語をテーブルに対するクエリに適用します。この関数は、引数として JSON オブジェクトを受け取るかまたは行フィルターを定義する関数を受け取り、詳細に構成可能なクエリを返します。

	var query = todoItemTable.where({
	    complete: false
	}).read().done(function (results) {
	    alert(JSON.stringify(results));
	}, function (err) {
	    alert("Error: " + err);
	});

Query オブジェクトに呼び出し元の where を追加し、オブジェクトをパラメーターとして渡すことにより、complete 列に false 値が含まれる行のみを返すようにモバイル サービスに指示しています。また、次の要求 URI からわかるように、クエリ文字列自体にも変更を加えています。

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

ブラウザー開発者ツールや Fiddler などのメッセージ検査ソフトウェアを使用して、モバイル サービスに送信された要求の URI を表示できます。

通常、この要求は、サーバー側で次のような SQL クエリに変換されます。

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

where メソッドに渡されるオブジェクトは任意の数のパラメーターを持つことができます。これらのパラメーターは、クエリに対してすべて AND 句として解釈されます。たとえば、次のコードがあるとします。

	query.where({
	   complete: false,
	   assignee: "david",
	   difficulty: "medium"
	}).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

このコードは、ほぼ次のように変換できます (前と同じ要求)。

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND assignee = 'david'
	      AND difficulty = 'medium'

上記の where ステートメントと上記の SQL クエリでは、"david" に割り当てられた、難易度 (difficulty) が "medium" である未完了の項目が検索されます。

ただし、別の方法で同じクエリを作成することもできます。Query オブジェクトに対する .where 呼び出しは WHERE 句に AND 式を追加します。そこで、これを 3 行で記述することもできます。

	query.where({
	   complete: false
	});
	query.where({
	   assignee: "david"
	});
	query.where({
	   difficulty: "medium"
	});

または、次のように fluent API を使用することもできます。

	query.where({
	   complete: false
	})
	   .where({
	   assignee: "david"
	})
	   .where({
	   difficulty: "medium"
	});

この 2 つの方法は等価であり、区別しないで使用できます。これまでのすべての where 呼び出しは、いくつかのパラメーターを持つオブジェクトを受け取り、データベースからのデータに対して同等性を調べるために比較を実行しました。しかし、オブジェクトの代わりに関数を受け取る、クエリ メソッドの別のオーバーロードがあります。この関数では、非同等性やその他の関係演算のための演算子を使用して、より複雑な式を書くことができます。これらの関数では、this キーワードがサーバー オブジェクトにバインドされます。

関数の本文は、クエリ文字列パラメーターに渡される Open Data Protocol (OData) のブール式に変換されます。次に示すように、パラメーターを受け取らない関数を渡すこともできます。

    query.where(function () {
       return this.assignee == "david" && (this.difficulty == "medium" || this.difficulty == "low");
    }).read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });


パラメーターを持つ関数を渡した場合、where 句以降のすべての引数は指定された順に関数パラメーターに適用されます。関数スコープ外からのすべてのオブジェクトは、パラメーターとして渡す必要があります (関数は、外部変数をキャプチャできません)。次の 2 つの例では、引数 "david" が name パラメーターにバインドされています。最初の例では、引数 "medium" も level パラメーターにバインドされています。さらに、関数は、次に示すように、サポートされている式を含む単一の return ステートメントで構成されている必要があります。

	 query.where(function (name, level) {
	    return this.assignee == name && this.difficulty == level;
	 }, "david", "medium").read().done(function (results) {
	    alert(JSON.stringify(results));
	 }, function (err) {
	    alert("Error: " + err);
	 });

このように、規則に従っている限り、より複雑なフィルターをデータベース クエリに追加できます。

    query.where(function (name) {
       return this.assignee == name &&
          (this.difficulty == "medium" || this.difficulty == "low");
    }, "david").read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });

`where` は、`orderBy`、`take`、および `skip` を組み合わせることができます。詳細については、次のセクションを参照してください。

### <a name="sorting"></a>方法:返されるデータを並べ替える

次のコードでは、クエリに `orderBy` 関数または `orderByDescending` 関数を含めることによってデータを並べ替える方法を示します。次のコードは、`todoItemTable` から、`text` フィールドの値に基づいて昇順に並べ替えられた項目を返します。既定では、サーバーは最初の 50 個の要素のみを返します。

<div class="dev-callout"><strong>注</strong> <p>すべての要素が返されるのを防ぐために、サーバー側で設定されたページ サイズが既定で使用されます。これにより、大きなデータ セットの既定の要求がサービスに悪影響を与えないようにします。 </p> </div>
>
次のセクションで説明する take を呼び出すことにより、返される項目の数を増やすことができます。`todoItemTable` は、既に作成したモバイル サービス テーブルへの参照です。

	var ascendingSortedTable = todoItemTable.orderBy("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

	var descendingSortedTable = todoItemTable.orderByDescending("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

	var descendingSortedTable = todoItemTable.orderBy("text").orderByDescending("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

### <a name="paging"></a>方法:ページにデータを返す

次のコードでは、クエリで take 句と skip 句を使用して、返されたデータにページングを実装する方法を示しています。次のクエリを実行すると、テーブルの最初の上位 3 つの項目が返されます。

	var query = todoItemTable.take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

クエリの URI では、`take(3)` メソッドがクエリ オプション `$top=3` に変換されていることに注目してください。

次の変更されたクエリは、最初の 3 つの結果をスキップし、その後の 3 つを返します。ページ サイズが 3 つの項目である場合、これは実質的にデータの 2 番目の "ページ" になります。

	var query = todoItemTable.skip(3).take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

繰り返しになりますが、モバイル サービスに送信された要求の URI を表示できます。クエリの URI では、`skip(3)` メソッドがクエリ オプション `$skip=3` に変換されていることに注目してください。

これは、ハードコーディングされたページング値を take 関数および skip 関数に渡す、簡略化したシナリオです。実際のアプリケーションでは、ユーザーが前後のページに移動できるように、ページャー コントロールまたは同等の UI と共に上記と同様のクエリを使用することができます。

### <a name="selecting"></a>方法:特定の列を選択する

クエリに select 句を追加し、結果に含める一連のプロパティを指定できます。たとえば、次のコードは、`todoItemTable` のそれぞれの行から、`id`、`complete`、および `text` の各プロパティを返します。

	var query = todoItemTable.select("id", "complete", "text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	})

select 関数のパラメーターは、取得するテーブルの列の名です。


これまでに説明した関数はいずれも付加的なものであるため、この後も呼び出すようにし、クエリに活用することにします。次の例も参照してください。

    query.where({
       complete: false
    })
       .select('id', 'assignee')
       .orderBy('assignee')
       .take(10)
       .read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);

### <a name="lookingup"></a>方法:ID でデータを検索する

lookup 関数は、id 値のみを受け取り、データベースからその ID を持つオブジェクトを返します。整数型または文字列の id 列を持つデータベース テーブルが作成されます。既定では文字列の id 列になります。

	todoItemTable.lookup("37BBF396-11F0-4B39-85C8-B319C729AF6D").done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	})

##<a name="odata-query"></a>OData クエり操作を実行する

モバイル サービスでは、REST クエリの作成と実行に OData クエリ URI 慣例を使用します。組み込まれているクエリ関数ですべての OData クエリ (特にプロパティないのサブストリング検索などの複雑なフィルター操作) が作成できるわけではありません。複雑なクエリには、次のように有効な OData クエリ オプション文字列を `read` 関数に渡すことができます。

	function refreshTodoItems() {
	    todoItemTable.read("$filter=substringof('search_text',text)").then(function(items) {
	        var itemElements = $.map(items, createUiForTodoItem);
	        $("#todo-items").empty().append(itemElements);
	        $("#no-items").toggle(items.length === 0);
	    }, handleError);
	}

>[WACOM.NOTE]未処理の OData クエリ オプション文字列を `read` 関数に提供する場合、同じクエリ内でクエリ ビルダー メソッドは使用できません。この場合、クエリ全体を OData クエリ文字列として作成する必要があります。OData システム クエリ オプションの詳細については、「[OData system query options reference (OData システム クエリ オプション リファレンス)]」を参照してください。

<h2><a name="inserting"></a>方法:モバイル サービスにデータを挿入する</h2>

次のコードは、テーブルに新しい行を挿入する方法を示しています。クライアントは、モバイル サービスに POST 要求を送信して、データ行を挿入することを要求します。要求の本文には、挿入するデータが JSON オブジェクトとして含まれます。

	todoItemTable.insert({
	   text: "New Item",
	   complete: false
	})

このコードは、指定された JSON オブジェクトからのデータをテーブルに挿入します。挿入操作が完了したときに呼び出されるコールバック関数を指定することもできます。

	todoItemTable.insert({
	   text: "New Item",
	   complete: false
	}).done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	});


Mobile Services では、テーブル ID として一意のカスタム文字列値をサポートしています。このため、アプリケーションは、Mobile Services テーブルの ID 列に電子メール アドレスやユーザー名などのカスタム値を使用できます。たとえば、各レコードを電子メール アドレスで識別する場合は、次の JSON オブジェクトを使用できます。

			todoItemTable.insert({
			   id: "myemail@domain.com",
			   text: "New Item",
			   complete: false
			})

新しいレコードをテーブルに挿入するときに文字列 ID 値が指定されない場合は、モバイル サービスによって ID 用の一意の値が生成されます。

文字列 ID のサポートは、開発者にとって次のような利点があります。

+ データベースと往復することなく ID が生成されます。
+ 他のテーブルやデータベースのレコードをより簡単にマージできます。
+ ID 値をより適切にアプリケーションのロジックに統合できます。

サーバー スクリプトを使用して ID 値を設定することもできます。次のスクリプト例は、カスタム GUID を生成し、新しいレコードの ID に割り当てます。これは、レコードの ID として値を渡さなかった場合に Mobile Services によって生成される ID 値に似ています。

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


アプリケーションが ID の値を指定すると、モバイル サービスはそれをそのまま格納します。これには、前後の空白文字も含まれます。値から空白文字が除去されることはありません。

ID の値は一意である必要があり、次のセット内の文字を含まないようにする必要があります。

+ 制御文字:[0x0000-0x001F] および [0x007F-0x009F]。詳細については、[ASCII 制御コード C0 および C1 に関するページ]を参照してください。
+  印刷可能文字:**"**(0x0022)、**\+** (0x002B)、**/** (0x002F)、**?**(0x003F)、**\\** (0x005C)、**`** (0x0060)
+  ID "." および ".."

また、テーブルに整数 ID を使用することもできます。整数 ID を使用するには、mobile table create コマンドで --integerId オプションを使用してテーブルを作成する必要があります。このコマンドは、Azure のコマンド ライン インターフェイス (CLI) で使用されます。CLI の使い方の詳細については、「[Mobile Services テーブルの管理用コマンド]」を参照してください。


<h2><a name="modifying"></a>方法:モバイル サービスのデータを変更する</h2>

次のコードは、テーブルのデータを更新する方法を示しています。クライアントは、モバイル サービスに PATCH 要求を送信して、データ行を更新することを要求します。要求の本文には、更新する特定のフィールドが JSON オブジェクトとして含まれます。このコードは、テーブル `todoItemTable` 内の既存の項目を更新します。

			todoItemTable.update({
			   id: idToUpdate,
			   text: newText
			})

1 つ目のパラメーターは、テーブル内の更新するインスタンスを ID で指定します。

更新操作が完了したときに呼び出されるコールバック関数を指定することもできます。

			todoItemTable.update({
			   id: idToUpdate,
			   text: newText
			}).done(function (result) {
			   alert(JSON.stringify(result));
			}, function (err) {
			   alert("Error: " + err);
			});

<h2><a name="deleting"></a>方法:モバイル サービスのデータを削除する</h2>

次のコードは、テーブルからデータを削除する方法を示しています。クライアントは、モバイル サービスに DELETE 要求を送信して、データ行を削除することを要求します。このコードは、テーブル todoItemTable 内の既存の項目を削除します。

			todoItemTable.del({
			   id: idToDelete
			})

1 つ目のパラメーターは、テーブル内の削除するインスタンスを ID で指定します。

削除操作が完了したときに呼び出されるコールバック関数を指定することもできます。

			todoItemTable.del({
			   id: idToDelete
			}).done(function () {
			   /* Do something */
			}, function (err) {
			   alert("Error: " + err);
			});

<h2><a name="binding"></a>方法:ユーザー インターフェイスにデータを表示する</h2>

このセクションでは、返されたデータ オブジェクトを UI 要素を使用して表示する方法について説明します。次のサンプル コードを実行すると、`todoItemTable` 内の項目を照会し、それを単純なリストに表示できます。ここでは、どのような種類の選択、フィルター処理、または並べ替えも行われません。

			var query = todoItemTable;

			query.read().then(function (todoItems) {
			   // The space specified by 'placeToInsert' is an unordered list element <ul> ... </ul>
			   var listOfItems = document.getElementById('placeToInsert');
			   for (var i = 0; i < todoItems.length; i++) {
			      var li = document.createElement('li');
			      var div = document.createElement('div');
			      div.innerText = todoItems[i].text;
			      li.appendChild(div);
			      listOfItems.appendChild(li);
			   }
			}).read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

Windows ストア アプリでは、クエリの結果を使用して、[ListView] オブジェクトのデータ ソースとしてバインドできる [WinJS.Binding.List] オブジェクトを作成できます。詳細については、「[データ バインディング (JavaScript と HTML を使った Windows ストア アプリ)]」を参照してください。

<h2><a name="caching"></a>方法:ユーザーを認証する</h2>

Mobile Services は、Facebook、Google、Microsoft アカウント、Twitter などのさまざまな外部 ID プロバイダーを使用したアプリケーション ユーザーの認証と承認をサポートします。テーブルのアクセス許可を設定することにより、特定の操作へのアクセスを認証されたユーザーのみに制限できます。さらに、認証されたユーザーの ID を使用することにより、サーバー スクリプトで承認ルールを実装することもできます。詳細については、チュートリアル「[モバイル サービスでの認証の使用]」を参照してください。

_server フローおよび _client フローという 2 つの認証フローがサポートされます。サーバー フローには、プロバイダーの Web 認証のインターフェイスを利用する、最も簡単な認証方法が用意されています。クライアント フローでは、プロバイダー固有およびデバイス固有の SDK を利用することから、シングル サインオンなどのデバイス固有の機能との統合がさらに進みます。

<h3>サーバー フロー</h3>
モバイル サービスによって Windows ストア アプリまたは HTML5 アプリの認証プロセスが管理されるようにするには、アプリケーションを ID プロバイダーに登録する必要があります。その後、モバイル サービス内で、プロバイダーから提供されたアプリケーション ID とシークレットを構成する必要があります。詳細については、チュートリアル「モバイル サービスでの認証の使用」([Windows ストア][Get started with authentication Windows Store]/[HTML][Get started with authentication]) を参照してください。

ID プロバイダーを登録したら、[MobileServiceAuthenticationProvider] にプロバイダーの値を指定して [LoginAsync メソッド] を呼び出します。たとえば、Facebook にログインするには、次のコードを使用します。

		client.login("facebook").done(function (results) {
		     alert("You are now logged in as: " + results.userId);
		}, function (err) {
		     alert("Error: " + err);
		});

Facebook 以外の ID プロバイダーを使用している場合は、上の `login` メソッドに渡される値を`microsoftaccount`、`facebook`、`twitter`、`google`、または `windowsazureactivedirectory` に変更します。

この場合、モバイル サービスは、選択されたプロバイダーのログイン ページを表示し、ID プロバイダーでのログインが成功した後でモバイル サービス認証トークンを生成することで、OAuth 2.0 認証フローを管理します。[login] 関数は、完了すると、**userId** フィールドのユーザー ID と **authenticationToken** フィールドの Mobile Services 認証トークンの両方を公開する JSON オブジェクト (**user**) を返します。このトークンはキャッシュして、期限が切れるまで再利用することができます。詳細については、「[認証トークンをキャッシュする]」を参照してください。

<div class="dev-callout"><b>Windows ストア アプリ</b>
<p>Windows ストア アプリケーションのユーザーの認証に Microsoft アカウント ログイン プロバイダーを使用する場合は、アプリケーション パッケージもモバイル サービスに登録する必要があります。Windows ストア アプリケーションのパッケージ情報をモバイル サービスに登録すると、クライアントはシングル サインオン サービス エクスペリエンスを実現するために Microsoft アカウント ログイン資格情報を再利用できます。この操作を行わない場合、login メソッドが呼び出されるたびに、Microsoft アカウント ログイン ユーザーにログイン プロンプトが表示されます。Windows ストア アプリケーション パッケージの登録方法の詳細については、「 <a href="/ja-jp/develop/mobile/how-to-guides/register-windows-store-app-package/" target="_blank">Windows ストア アプリ パッケージを Microsoft 認証に登録する</a>」を参照してください。パッケージ情報が Mobile Services に登録された後、 <a href="http://go.microsoft.com/fwlink/p/?LinkId=322050" target="_blank">資格情報を再利用するには、</a> useSingleSignOn パラメーターに値 <strong>true</strong> を指定して <em>login メソッド</em> を呼び出します。</p>
</div>

<h3>クライアント フロー</h3>
アプリケーションは個別に ID プロバイダーにアクセスして、返されたトークンを認証のためにモバイル サービスに提供することもできます。このクライアント フローでは、ユーザーにシングル サインイン エクスペリエンスを提供したり、ID プロバイダーから追加のユーザー データを取得したりすることができます。

次の例では、Windows ストア アプリケーションのシングル サインオンを Microsoft アカウントによってサポートしている Live SDK を使用します。

		WL.login({ scope: "wl.basic"}).then(function (result) {
		      client.login(
		            "microsoftaccount",
		            {"authenticationToken": result.session.authentication_token})
		      .done(function(results){
		            alert("You are now logged in as: " + results.userId);
		      },
		      function(error){
		            alert("Error: " + err);
		      });
		});

この簡略化された例では、トークンを Live Connect から取得します。このトークンは、[login] 関数を呼び出すことによって、Mobile Services に渡されます。シングル サインイン エクスペリエンスを提供するために Microsoft アカウントを使用する方法のより完全な例については、「[Live Connect シングル サインオンによる Windows ストア アプリの認証]」を参照してください。

クライアント認証に Facebook または Google API を使用している場合は、例が少し異なります。

		client.login(
		     "facebook",
		     {"access_token": token})
		.done(function (results) {
		     alert("You are now logged in as: " + results.userId);
		}, function (err) {
		     alert("Error: " + err);
		});

この例では、それぞれのプロバイダー SDK で提供されるトークンが変数 token に格納されるとします。
現時点では、クライアント認証に Twitter を使用することはできません。

<h3>認証トークンをキャッシュする</h3>
場合によっては、最初のユーザー認証の後の login メソッドの呼び出しを避けることができます。そのためには、[sessionStorage] または [localStorage] を使用して、ユーザーが初めてログインするときに使用した現在のユーザー ID をキャッシュし、それ以降はユーザー ID がキャッシュに保存されているかどうかをチェックします。キャッシュが空の場合や呼び出しが失敗した場合 (現在のログイン セッションが期限切れになったことを示します) は、ログオン プロセスを実行する必要があります。

        // After logging in
        sessionStorage.loggedInUser = JSON.stringify(client.currentUser);

        // Log in
        if (sessionStorage.loggedInUser) {
           client.currentUser = JSON.parse(sessionStorage.loggedInUser);
        } else {
           // Regular login flow
       }

         // Log out
        client.logout();
        sessionStorage.loggedInUser = null;


<h2><a name="errors"></a>方法:エラーを処理する</h2>

モバイル サービスには、エラーの検出、検証、回避のためのさまざまな方法があります。

たとえば、サーバー スクリプトは、モバイル サービスに登録され、挿入や更新が行われるデータでの広範な操作 (検証やデータの修正を含む) の実行に使用できます。次のようにデータを検証および変更するサーバー スクリプトを定義し、登録するとします。

			function insert(item, user, request) {
			   if (item.text.length > 10) {
				  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
			   } else {
			      request.execute();
			   }
			}

サーバー側スクリプトは、モバイル サービスに送信された文字列データの長さを検証し、長すぎる文字列 (この場合は 10 文字を超える) を拒否します。

モバイル サービスはデータを検証してエラー応答をサーバー側に送信するため、検証からのエラー応答を処理できるように HTML アプリケーションを更新することができます。

		todoItemTable.insert({
		   text: itemText,
		   complete: false
		})
		   .then(function (results) {
		   alert(JSON.stringify(results));
		}, function (error) {
		   alert(JSON.parse(error.request.responseText).error);
		});


これをさらに詳しく見ると、データ アクセスを実行するたびに 2 つ目の引数としてエラー ハンドラーを渡します。

			function handleError(message) {
			   if (window.console && window.console.error) {
			      window.console.error(message);
			   }
			}

			client.getTable("tablename").read().then(function (data) { /* do something */ }, handleError);

<h2><a name="promises"></a>方法:promise を使用する</h2>

promise は、まだ計算されていない値の操作を完了するスケジュールを設定するためのメカニズムを提供します。promise は、非同期 API を使用して相互作用を管理するための抽象化です。

done promise は、指定された関数が正常に完了した時点またはエラーが発生した時点で、すぐに実行されます。then promise とは異なり、関数内で処理されないエラーをスローすることが保証されます。この関数は、ハンドラーの実行が完了した後、then の場合であれば返されたすべてのエラーをエラー状態の promise としてスローします。詳細については、[Promise.done メソッドに関するページ]を参照してください。

			promise.done(onComplete, onError);

次に例を示します。

			var query = todoItemTable;
			query.read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

then promise は done promise と同じです。ただし、done promise では、then promise とは異なり、関数内で処理されないエラーがスローされることが保証されます。then にエラー ハンドラーを提供しなかった場合、操作でエラーが発生すると、例外はスローされず、代わりにエラー状態の promise が返されます。詳細については、[Promise.then メソッドに関するページ]を参照してください。

			promise.then(onComplete, onError).done( /* Your success and error handlers */ );

次に例を示します。

			var query = todoItemTable;
			query.read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

promise はいくつかの異なる方法で使用することができます。前の then 関数から返される promise で then または done を呼び出すことにより、promise 操作を連結できます。then は操作の中間ステージ (たとえば、.then().then()) に使用し、done は操作の最終ステージ (たとえば、.then().then().done()) に使用します。then は promise を返すため、複数の then 関数を連結できます。done メソッドは undefined を返すため、このメソッドを複数個連結することはできません。[then と done の違いについてはこのページを参照してください]。

 			todoItemTable.insert({
 			   text: "foo"
 			}).then(function (inserted) {
 			   inserted.newField = 123;
 			   return todoItemTable.update(inserted);
 			}).done(function (insertedAndUpdated) {
 			   alert(JSON.stringify(insertedAndUpdated));
 			})

<h2><a name="customizing"></a>方法:クライアント要求ヘッダーをカスタマイズする</h2>

カスタムの要求ヘッダーを送信できます。そのためには、withFilter 関数を使用し、フィルター内で送信される要求の任意のプロパティの読み取りと書き込みを行います。このようなカスタム HTTP ヘッダーを追加するケースとしては、サーバー側スクリプトで必要とされる場合や、カスタム HTTP ヘッダーによってサーバー側スクリプトを拡張できる場合があります。

			var client = new WindowsAzure.MobileServiceClient('https://your-app-url', 'your-key')
			   .withFilter(function (request, next, callback) {
			   request.headers.MyCustomHttpHeader = "Some value";
			   next(request, callback);
			});

フィルターを使用する機会は要求ヘッダーをカスタマイズする機会よりもたくさんあります。フィルターを使用すると、要求の検査または変更、応答の検査または変更、ネットワーキング呼び出しのバイパス、複数の呼び出しの送信などの操作を実行できます。

<h2><a name="hostnames"></a>方法:クロス オリジン リソース共有を使用する</h2>

モバイル サービスとの対話やモバイル サービスへの要求の送信を許可する Web サイトを制御するには、[構成] タブを使用して、ホストする Web サイトのホスト名をクロス オリジン リソース共有 (CORS) ホワイトリストに追加してください。必要に応じて、ワイルドカードを使用できます。既定では、新しいモバイル サービスは localhost からのアクセスのみを許可するようブラウザーに指示します。クロス オリジン リソース共有 (CORS) により、外部ホスト名のブラウザーで実行されている JavaScript コードがモバイル サービスと対話できるようになります。この構成は、WinJS アプリケーションには必要ありません。

<h2><a name="nextsteps"></a>次のステップ</h2>

使用方法の概念リファレンス トピックを完了した後は、モバイル サービスで重要なタスクを実行する方法について詳しく確認してください。

* [Mobile Services の使用]
  <br/>モバイル サービスを使用する方法の基本について説明します。

* [データの使用]
  <br/>Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用]
  <br/>ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]
  <br/>モバイル サービスでサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

* [ページングを使用したモバイル サービス クエリの改善]
  <br/>クエリ内でページングを使用して、単一の要求で渡されるデータの量を制御する方法について説明します。

* [スクリプトを使用したユーザーの承認]
  <br/>認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理する方法について説明します。

<!-- Anchors. -->
[Mobile Services とは]: #what-is
[概念]: #concepts
[方法: Mobile Services クライアントを作成する]: #create-client
[方法: モバイル サービスのデータを照会する]: #querying
[返されるデータをフィルター処理する]: #filtering
[返されるデータを並べ替える]: #sorting
[ページにデータを返す]: #paging
[特定の列を選択する]: #selecting
[ID でデータを検索する]: #lookingup
[方法: ユーザー インターフェイスにデータを表示する]: #binding
[方法: モバイル サービスにデータを挿入する]: #inserting
[方法: モバイル サービスのデータを変更する]: #modifying
[方法: モバイル サービスのデータを削除する]: #deleting
[方法: ユーザーを認証する]: #caching
[方法: エラーを処理する]: #errors
[方法: promise を使用する]: #promises
[方法: 要求ヘッダーをカスタマイズする]: #customizing
[方法: クロス オリジン リソース共有を使用する]: #hostnames
[次のステップ]: #nextsteps
[OData クエリ操作を実行する]: #odata-query



<!-- URLs. -->
[Mobile Services の使用]: /ja-jp/develop/mobile/tutorials/get-started-html
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[データの使用]: http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/get-started-with-data-html/
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-html
[認証と Windows ストアの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-js
[Promise.then メソッドに関するページ]: http://msdn.microsoft.com/ja-jp/library/windows/apps/br229728.aspx
[Promise.done メソッドに関するページ]: http://msdn.microsoft.com/ja-jp/library/windows/apps/hh701079.aspx
[then と done の違いについてはこのページを参照してください]: http://msdn.microsoft.com/ja-jp/library/windows/apps/hh700334.aspx
[promise でエラーを処理する方法]: http://msdn.microsoft.com/ja-jp/library/windows/apps/hh700337.aspx

[sessionStorage]: http://msdn.microsoft.com/ja-jp/library/cc197062(v=vs.85).aspx
[localStorage]: http://msdn.microsoft.com/ja-jp/library/cc197062(v=vs.85).aspx

[ListView]: http://msdn.microsoft.com/ja-jp/library/windows/apps/br211837.aspx
[データ バインディング (JavaScript と HTML を使った Windows ストア アプリ)]: http://msdn.microsoft.com/ja-jp/library/windows/apps/hh758311.aspx
[Windows ストア JavaScript のクイック スタート]: http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/get-started
[HTML のクイック スタート]: http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/get-started-html
[データの使用 (Windows ストア JavaScript)]: http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/get-started-with-data-js
[データの使用 (HTML/JavaScript)]: http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/get-started-with-data-html/
[このシナリオを設定する方法の例については、このページを参照してください]: http://www.windowsazure.com/ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-js/
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-html
[サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-html
[ページングを使用したモバイル サービス クエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-html
[スクリプトを使用したユーザーの承認]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-html
[login]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj554236.aspx
[シングル サインオンによるアプリの認証]: /ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[ASCII 制御コード C0 および C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services テーブルの管理用コマンド]: http://www.windowsazure.com/ja-jp/manage/linux/other-resources/command-line-tools/#Mobile_Tables
[OData system query options reference (OData システム クエリ オプション リファレンス)]: http://go.microsoft.com/fwlink/p/?LinkId=444502

<!--HONumber=35_1-->
