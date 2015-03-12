<properties 
	pageTitle="Mobile Services 向け .NET クライアント ライブラリの操作" 
	description="Azure Mobile Services 向け .NET クライアントを使用する方法について説明します。" 
	services="" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="glenga"/>





# Azure モバイル サービス向け .NET クライアントを使用する方法

<div class="dev-center-tutorial-selector sublanding">
  <a href="/ja-jp/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework" class="current">.NET Framework</a>
  	<a href="/ja-jp/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/ja-jp/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/ja-jp/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>


このガイドでは、Windows ストア アプリと Windows Phone アプリで Azure Mobile Services 用の .NET クライアントを使用する一般的なシナリオを実行する方法を示します。紹介するシナリオは、データの照会、挿入、更新、および削除、ユーザーの認証、エラー処理などです。モバイル サービスを初めて使用する場合は、まずモバイル サービスのクイック スタートに関するチュートリアル ([Windows ストアのクイック スタートのチュートリアル]/[Windows Phone のクイック スタートのチュートリアル]) と .NET でのデータの使用に関するチュートリアル ([Windows ストアのデータのチュートリアル]/[Windows Phone のデータのチュートリアル]) を完了することを検討してください。このクイック スタート チュートリアルでは、[モバイル サービス SDK] が必要です。ここでは、アカウントを構成し、初めてのモバイル サービスを作成します。


## 目次

- [Mobile Services とは]
- [概念]
- [方法:モバイル サービス クライアントを作成する]
- [方法:テーブル参照を作成する]
- [方法:モバイル サービスのデータを照会する]
	- [返されるデータをフィルター処理する]
    - [返されるデータを並べ替える]
	- [ページにデータを返す]
	- [特定の列を選択する]
	- [ID でデータを検索する]
- [方法:モバイル サービスにデータを挿入する]
- [方法:モバイル サービスのデータを変更する]
- [方法:モバイル サービスのデータを削除する]
- [方法:カスタム API の呼び出し]
- [方法:オプティミスティック同時実行制御を使用する]
- [方法:モバイル サービスでユーザー インターフェイスにデータをバインドする]
- [方法:ユーザーを認証する]
- [方法:エラーを処理する]
- [方法:型指定のないデータを処理する]
- [方法:単体テストを設計する]
- [方法:クライアントをカスタマイズする]
	- [要求ヘッダーをカスタマイズする]
	- [シリアル化をカスタマイズする]
- [次のステップ]

[AZURE.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

<h2><a name="setup"></a>セットアップと前提条件</h2>

前提条件として、モバイル サービスとテーブルを作成してあるとします。詳細については、「[テーブルの作成](http://go.microsoft.com/fwlink/?LinkId=298592)」を参照してください。このトピックで使用するコードでは、テーブルの名前は `TodoItem` であり、 `Id`、 `Text`、および `Complete` という列があります。

これに対応する型指定されたクライアント側 .NET 型を次に示します。


	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

動的スキーマを有効にすると、挿入または更新の要求に含まれるオブジェクトに基づいて、Azure Mobile Services で自動的に新しい列が生成されます。詳細については、[動的スキーマ](http://go.microsoft.com/fwlink/?LinkId=296271) を参照してください。

<h2><a name="create-client"></a>方法:モバイル サービス クライアントを作成する</h2>

次のコードは、Mobile Services にアクセスするために使用される `MobileServiceClient` オブジェクトを生成します。


	MobileServiceClient client = new MobileServiceClient(
		"AppUrl",
		"AppKey"
	);

前のコードの  `AppUrl` と  `AppKey` を、モバイル サービスの URL とアプリケーション キーで順に置き換えます。どちらも Azure 管理ポータルで確認できます。モバイル サービスを選択し、[ダッシュボード] をクリックしてください。

<h2><a name="instantiating"></a>方法:テーブル参照を作成する</h2>

Mobile Services のテーブル データにアクセスするコードとそのデータを変更するコードは、必ず `MobileServiceTable` オブジェクトで関数を呼び出します。 `MobileServiceClient` のインスタンスで [GetTable](http://msdn.microsoft.com/library/windowsazure/jj554275.aspx) 関数を呼び出して、テーブルへの参照を取得します。

    IMobileServiceTable<TodoItem> todoTable =
		client.GetTable<TodoItem>();

これは型指定されたシリアル化のモデルです。後で示す<a href="#untyped">型指定のないシリアル化のモデル</a>に関する説明を参照してください。

<h2><a name="querying"></a>方法:モバイル サービスのデータを照会する</h2>

このセクションでは、モバイル サービスにクエリを発行する方法について説明します。サブセクションでは、並べ替え、フィルター処理、ページングなど、さまざまな処理について説明します。

>[AZURE.NOTE] すべての行が返されるのを防ぐために、サーバー側で設定されたページ サイズが既定で使用されます。これにより、大きなデータ セットの既定の要求がサービスに悪影響を与えないようにします。50 を超える行を返すには、「[ページにデータを返す]」で説明されている `Take` メソッドを使用します。  

### <a name="filtering"></a>方法:返されるデータをフィルター処理する

次のコードは、クエリに `Where` 句を含めることによってデータをフィルター処理する方法を示しています。このコードは、 `Complete` プロパティが `false` に等しい `todoTable` からすべての項目を返します。 `Where` 関数は、行のフィルタリング述語をテーブルに対するクエリに適用します。

	// This query filters out completed TodoItems and
	// items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

ブラウザー開発者ツールや [Fiddler] などのメッセージ検査ソフトウェアを使用して、Mobile Services に送信された要求の URI を表示できます。次の要求 URI を確認すると、クエリ文字列自体に変更を加えていることがわかります。

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
通常、この要求は、サーバー側で次のような SQL クエリに変換されます。

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

 `Where` メソッドに渡される関数には、任意の数の条件を設定できます。たとえば、次のコードがあるとします。

	// This query filters out completed TodoItems where Text isn't null
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false
		   && todoItem.Text != null)
	   .ToListAsync();

このコードは、ほぼ次のように変換できます (前と同じ要求)。

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND ISNULL(text, 0) = 0

前の `where` ステートメントは、null 以外の `Text` を持ち、 `Complete` のステータスが false に設定されている項目を検索します。

代わりに、次のように複数の行で記述することもできます。

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

この 2 つの方法は等価であり、区別しないで使用できます。複数の述語を 1 つのクエリに連結する前のオプションが、よりコンパクトでありお勧めです。

 `where` 句は、Mobile Services の OData サブセットに変換される操作をサポートします。これには、関係演算子 (==、!=、<、<=、>、>=)、算術演算子 (+、-、/、*、%)、数の精度 (Math.Floor、Math.Ceiling)、文字列関数 (Length、Substring、Replace、IndexOf、StartsWith、EndsWith)、日付プロパティ (Year、Month、Day、Hour、Minute、Second)、オブジェクトのアクセス プロパティ、これらすべてを組み合わせた式が含まれます。

### <a name="sorting"></a>方法:返されるデータを並べ替える

次のコードは、クエリに `OrderBy` または `OrderByDescending` 関数を含めることによってデータを並べ替える方法を示しています。次のコードは、 `todoTable` から、 `Text` フィールドの値に基づいて昇順に並べ替えられた項目を返します。

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

### <a name="paging"></a>方法:ページにデータを返す

既定では、サーバーは最初の 50 行のみを返します。[Take] メソッドを呼び出すことによって、返される行の数を増やすことができます。[Skip] メソッドと共に `Take` を使用して、クエリによって返されるデータセット全体のうち特定の "ページ" を要求します。次のクエリを実行すると、テーブルの最初の上位 3 つの項目が返されます。

	// Define a filtered query that returns the top 3 ite
	ms.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

次の変更されたクエリは、最初の 3 つの結果をスキップし、その後の 3 つを返します。ページ サイズが 3 つの項目である場合、これは実質的にデータの 2 番目の "ページ" になります。

	// Define a filtered query that skips the top 3 items and returns the next 3 ite
	ms.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Skip(3)
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

また、[IncludeTotalCount] メソッドを使用すると、指定された take paging/limit 句を無視して、返される<i>すべての</i>レコードの合計数を取得することができます。

	query = query.IncludeTotalCount();

これは、ハードコーディングされたページング値を `Take` メソッドおよび `Skip` メソッドに渡す、簡略化したシナリオです。実際のアプリケーションでは、ユーザーが前後のページに移動できるように、ページャー コントロールまたは同等の UI と共に上記と同様のクエリを使用することができます。

### <a name="selecting"></a>方法:特定の列を選択する

クエリに `Select` 句を追加し、結果に含める一連のプロパティを指定できます。たとえば、次のコードでは、1 つのフィールドだけを選択する方法と、複数のフィールドを選択し、フォーマットする方法も示しています。

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();

	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}", todoItem.Text.PadRight(30), todoItem.Complete ? "Now complete!" : "Incomplete!"));
	List<string> items = await query.ToListAsync();

これまでに説明した関数はいずれも付加的なものであるため、この後も呼び出すようにし、クエリに活用することにします。次の例も参照してください。

	MobileServiceTableQuery<TodoItem> query = todoTable
					.Where(todoItem => todoItem.Complete == false)
					.Select(todoItem => todoItem.Text)
					.Skip(3).
					.Take(3);
	List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>方法:ID でデータを検索する

 `LookupAsync` 関数を使用すると、データベースから特定の ID を持つオブジェクトを検索することができます。

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

<a name="inserting"></a>方法:モバイル サービスにデータを挿入する

> [AZURE.NOTE] 型に対して挿入、検索、削除、更新の各操作を実行する場合は、**ID** と呼ばれるメンバーを作成する必要があります。そのため、**TodoItem** クラスの例には、名前 **ID** のメンバーがあります。更新および削除の操作では、有効な ID 値を常に指定する必要があります。

次のコードは、テーブルに新しい行を挿入する方法を示しています。パラメーターには、挿入するデータが .NET オブジェクトとして含まれます。

	await todoTable.InsertAsync(todoItem);

 `todoTable.InsertAsync` 呼び出しに渡された `todoItem` に一意のカスタム ID 値が含まれていない場合、クライアントに返される  `todoItem` オブジェクトで設定されているサーバーによって、ID のための値が生成されます。

Mobile Services では、テーブル ID として一意のカスタム文字列値をサポートしています。このため、アプリケーションは、Mobile Services テーブルの ID 列に電子メール アドレスやユーザー名などのカスタム値を使用できます。新しいレコードをテーブルに挿入するときに文字列 ID 値が指定されない場合は、モバイル サービスによって ID 用の一意の値が生成されます。

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

 `id` の値は一意である必要があり、次のセット内の文字を含まないようにする必要があります。

+ 制御文字:[0x0000-0x001F] および [0x007F-0x009F]。詳細については、[ASCII 制御コード C0 および C1 に関するページ]を参照してください。
+  印刷可能文字:**"**(0x0022)、**\+** (0x002B)、**/** (0x002F)、**?**(0x003F)、**\\** (0x005C)、**`** (0x0060)
+  ID "." および ".."


また、テーブルに整数 ID を使用することもできます。整数 ID を使用するには、 `mobile table create` コマンドで --integerId オプションを使用してテーブルを作成する必要があります。このコマンドは、Azure のコマンド ライン インターフェイス (CLI) で使用されます。CLI の使い方の詳細については、「[モバイル サービス テーブルの管理用コマンド]」を参照してください。


型指定のないデータを挿入するために、次に示すように Json.NET を活用することもできます。

	JObject jo = new JObject();
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);

メール アドレスを一意の文字列 ID として使用する例を次に示します。

	JObject jo = new JObject();
	jo.Add("id", "myemail@emaildomain.com");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);


<h2><a name="modifying"></a>方法:モバイル サービスのデータを変更する</h2>

次のコードは、同じ ID を持つ既存のインスタンスを新しい情報で更新する方法を示しています。パラメーターには、更新するデータが .NET オブジェクトとして含まれます。

	await todoTable.UpdateAsync(todoItem);


型指定のないデータを挿入するために、次のような Json.NET を活用することもできます。更新を行う場合は、更新対象のインスタンスをモバイル サービスが識別できるように、ID を指定する必要があることに注意してしてください。ID は、 `InsertAsync` の呼び出しの結果から取得できます。

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

"Id" 値を設定せずに項目を更新しようとすると、サービスが更新対象を識別できないため、Mobile Services SDK は `ArgumentException` をスローします。


<h2><a name="deleting"></a>方法:モバイル サービスのデータを削除する</h2>

次のコードは、既存のインスタンスを削除する方法を示しています。インスタンスは、 `todoItem` に設定した "Id" フィールドで識別されます。

	await todoTable.DeleteAsync(todoItem);

型指定のないデータを削除するために、次のような Json.NET を活用することもできます。削除要求を行う場合は、削除対象のインスタンスをモバイル サービスが識別できるように、ID を指定する必要があることに注意してしてください。削除要求は ID のみを必要とします。他のプロパティはサービスに渡されません。そのいずれかが渡された場合、サービスに無視されます。 `DeleteAsync` の呼び出しの結果も通常は `null` です。渡す ID は、 `InsertAsync` の呼び出しの結果から取得できます。

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

"Id" フィールドを設定せずに項目を削除しようとすると、サービスが削除対象のインスタンスを識別できないため、サービスから `MobileServiceInvalidOperationException` が返されます。同様に、"Id" フィールドを設定せずに型指定されていない項目を削除しようとした場合も、サービスから `MobileServiceInvalidOperationException` が返されます。

##<a name="#custom-api"></a>方法:カスタム API の呼び出し

カスタム API を使用してカスタム エンドポイントを定義することにより、insert、update、delete、read のいずれの操作にも関連しないサーバー機能を公開することができます。カスタム API を使用することによって、HTTP メッセージ ヘッダーの読み取りや設定、JSON 以外のメッセージ本文形式の定義など、メッセージングをより柔軟に制御することができます。Mobile Services でカスタム API を作成する方法を含む完全な例については、「[クライアントからのカスタム API 呼び出し]」を参照してください。

クライアントで [InvokeApiAsync] メソッド オーバーロードの 1 つを呼び出して、カスタム API を呼び出します。たとえば、次のコード行は Mobile Services で **completeAll** API に POST 要求を送信します。

    var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll",
        System.Net.Http.HttpMethod.Post, null);

これは型指定メソッドの呼び出しのため、返される **MarkAllResult** の型を定義する必要があります。型指定および型指定のないメソッドの両方がサポートされます。型指定でペイロードを送信せず、クエリ パラメーターがなく、また要求ヘッダーを変更しないため、これは簡単な例になります。より現実的な例と [InvokeApiAsync] についてのより完全な説明については、[Azure Mobile Services クライアント SDK のカスタム API] に関するトピックを参照してください。


##<a name="optimisticconcurrency"></a>方法:オプティミスティック同時実行制御を使用する

シナリオによっては、複数のクライアントが同じ項目に対して同時に変更を書き込む場合があります。競合を検知しない場合、それを意図していなくても、最後に行われた書き込みによってそれ以前の更新がすべて上書きされます。オプティミスティック同時実行制御では、それぞれのトランザクションがコミットでき、そのためリソース ロックが一切使用されないことを前提としています。オプティミスティック同時実行制御ではトランザクションをコミットする前に、他のトランザクションがそのデータを変更していないことを確認します。データが変更されている場合、トランザクションのコミットはロール バックされます。

Mobile Services はオプティミスティック同時実行制御をサポートしており、Mobile Services によって作成された各テーブルに定義されている `__version` システム プロパティ列を使用して各項目の変更を追跡します。レコードが更新されるたびに、Mobile Services はそのレコードの `__version` プロパティを新しい値に設定します。各更新要求の際に、要求に含まれているレコードの `__version` プロパティが、サーバー上のレコードの同じプロパティと比較されます。要求によって渡されたバージョンがサーバーと一致しない場合は、Mobile Services .NET クライアント ライブラリから  `MobileServicePreconditionFailedException<T>` がスローされます。この例外に含まれている型は、サーバー上のレコードのバージョンを含む、サーバーのレコードです。アプリケーションはこの情報を使用して、サーバーからの正しい `__version` 値で更新要求をもう一度実行して変更をコミットするかどうかを判断できます。  

オプティミスティック同時実行制御を有効にするために、アプリケーションはテーブル クラスに `__version` システム プロパティ用の列を定義しています。次の定義は、その一例です。

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

		// *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "__version")]
        public byte[] Version { set; get; }
    }


型指定のないテーブルを使用しているアプリケーションは、テーブルの `SystemProperties` に次のように `Version` フラグを設定することによって、オプティミスティック同時実行制御を有効にします。

	//Enable optimistic concurrency by retrieving __version
	todoTable.SystemProperties |= MobileServiceSystemProperties.Version;


次のコードは、書き込み競合が検出された場合にそれを解決する方法を示しています。解決をコミットするには、 `UpdateAsync()` 呼び出しに正しい `__version` 値が含まれている必要があります。

	private async void UpdateToDoItem(TodoItem item)
	{
    	MobileServicePreconditionFailedException<TodoItem> exception = null;

	    try
    	{
	        //update at the remote table
    	    await todoTable.UpdateAsync(item);
    	}
    	catch (MobileServicePreconditionFailedException<TodoItem> writeException)
	    {
        	exception = writeException;
	    }

    	if (exception != null)
    	{
			// Conflict detected, the item has changed since the last query
        	// Resolve the conflict between the local and server item
	        await ResolveConflict(item, exception.Item);
    	}
	}


	private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
	{
    	//Ask user to choose the resoltion between versions
	    MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
        	                                        serverItem.Text, localItem.Text),
                                                	"CONFLICT DETECTED - Select a resolution:");

	    UICommand localBtn = new UICommand("Commit Local Text");
    	UICommand ServerBtn = new UICommand("Leave Server Text");
    	msgDialog.Commands.Add(localBtn);
	    msgDialog.Commands.Add(ServerBtn);

    	localBtn.Invoked = async (IUICommand command) =>
	    {
        	// To resolve the conflict, update the version of the
	        // item being committed. Otherwise, you will keep
        	// catching a MobileServicePreConditionFailedException.
	        localItem.Version = serverItem.Version;

    	    // Updating recursively here just in case another
        	// change happened while the user was making a decision
	        UpdateToDoItem(localItem);
    	};

	    ServerBtn.Invoked = async (IUICommand command) =>
    	{
	        RefreshTodoItems();
    	};

	    await msgDialog.ShowAsync();
	}


モバイル サービスのオプティミスティック同時実行制御を使用する、より完全な例については、[オプティミスティック同時実行制御のチュートリアルに関するページ]を参照してください。


<h2><a name="binding"></a>方法:モバイル サービスでユーザー インターフェイスにデータをバインドする</h2>

このセクションでは、返されたデータ オブジェクトを UI 要素を使用して表示する方法について説明します。 `todoTable` で未完了の項目を照会し、ごくシンプルな一覧で表示するために、次のコード例を実行して、クエリで一覧のソースをバインドします。 `MobileServiceCollection` を使用すると、Mobile Services 対応のバインディング コレクションが作成されます。

	// This query filters out completed TodoIte
	ms.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

Windows ランタイムの一部のコントロールは、[ISupportIncrementalLoading](http://msdn.microsoft.com/library/windows/apps/Hh701916) と呼ばれるインターフェイスをサポートします。このインターフェイスにより、コントロールはユーザーによるスクロールの際に追加のデータを要求することができます。このインターフェイスには、 `MobileServiceIncrementalLoadingCollection` による Windows ストア アプリ用の組み込みのサポートがあり、コントロールからの呼び出しが自動的に処理されます。Windows ストア アプリで `MobileServiceIncrementalLoadingCollection` を使用するには、次を実行します。

			MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
		items =  todoTable.Where(todoItem => todoItem.Complete == false)
					.ToIncrementalLoadingCollection();

		ListBox lb = new ListBox();
		lb.ItemsSource = items;


Windows Phone で新しいコレクションを使用するには、 `IMobileServiceTableQuery<T>` や `IMobileServiceTable<T>` で `ToCollection` 拡張メソッドを使用します。実際にデータを読み込むには、 `LoadMoreItemsAsync()` を呼び出します。

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await ite
	ms.LoadMoreItemsAsync();

 `ToCollectionAsync` または `ToCollection` を呼び出して作成されたコレクションを使用する場合は、UI コントロールにバインドできるコレクションを取得します。このコレクションはページングに対応しています。つまり、コントロールが "項目をさらに読み込む" ようにコレクションに依頼し、コレクションがそれを実行します。その時点で、ユーザー コードは関係せず、コントロールはフローを開始します。ただし、コレクションはネットワークからデータを読み込むため、読み込みが失敗することもあると想定されます。このようなエラーを処理するために、 `MobileServiceIncrementalLoadingCollection` で `OnException` メソッドをオーバーライドし、コントロールが実行する `LoadMoreItemsAsync` の呼び出しの結果として生じる例外を処理する必要があります。

最後に、テーブルには多くのフィールドが存在するものの、コントロールにはその一部のみを表示する必要があるとします。UI に表示する特定の列を選ぶ際には、前のセクション「<a href="#selecting">特定の列を選択する</a>」のガイダンスを参考にしてください。

<h2><a name="authentication"></a>方法:ユーザーを認証する</h2>

Mobile Services は、Facebook、Google、Microsoft アカウント、Twitter、Azure Active Directory などのさまざまな外部 ID プロバイダーを使用したアプリケーション ユーザーの認証と承認をサポートします。テーブルのアクセス許可を設定することにより、特定の操作へのアクセスを認証されたユーザーのみに制限できます。さらに、認証されたユーザーの ID を使用することにより、サーバー スクリプトで承認ルールを実装することもできます。詳細については、認証に関するチュートリアル ([Windows ストア][Windows ストアの認証]/[Windows Phone][Windows Phone の認証]) を参照してください。

_サーバー フロー_と_クライアント フロー_という 2 つの認証フローがサポートされます。サーバー フローには、プロバイダーの Web 認証のインターフェイスを利用する、最も簡単な認証方法が用意されています。クライアント フローでは、プロバイダー固有とデバイス固有の SDK を利用することから、デバイス固有の機能との統合がさらに進みます。

<h3>サーバー フロー</h3>
Windows ストアまたは Windows Phone アプリでの認証プロセスを Mobile Services で管理するためには、
アプリケーションを ID プロバイダーに登録する必要があります。その後、モバイル サービス内で、プロバイダーから提供されたアプリケーション ID とシークレットを構成する必要があります。詳細については、認証に関するチュートリアル ([Windows ストア][Windows ストアの認証]/[Windows Phone][Windows Phone の認証]) を参照してください。

ID プロバイダーを登録したら、[MobileServiceAuthenticationProvider] にプロバイダーの値を指定して [LoginAsync メソッド]を呼び出します。たとえば、次のコードは、Facebook を使用してサーバー フローのログインを開始します。

	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
				message =
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}

Facebook 以外の ID プロバイダーを使用している場合は、上の [MobileServiceAuthenticationProvider] の値をプロバイダーに対応する値に変更してください。

この場合、Mobile Services は、選択されたプロバイダーのログイン ページを表示し、ID プロバイダーでのログインが成功した後で Mobile Services 認証トークンを生成することで、OAuth 2.0 認証フローを管理します。[LoginAsync メソッド]は [MobileServiceUser] を返します。これによって、認証されたユーザーの [userId] と、JSON Web トークン (JWT) としての [MobileServiceAuthenticationToken] が提供されます。このトークンはキャッシュして、期限が切れるまで再利用することができます。詳細については、「[認証トークンをキャッシュする]」を参照してください。

> [AZURE.NOTE] **Windows ストア アプリ**
Windows ストア アプリケーションのユーザーの認証に Microsoft アカウント ログイン プロバイダーを使用する場合は、アプリケーション パッケージもモバイル サービスに登録する必要があります。Windows ストア アプリケーションのパッケージ情報をモバイル サービスに登録すると、クライアントはシングル サインオン サービス エクスペリエンスを実現するために Microsoft アカウント ログイン資格情報を再利用できます。この操作を行わない場合、login メソッドが呼び出されるたびに、Microsoft アカウント ログイン ユーザーにログイン プロンプトが表示されます。Windows ストア アプリケーション パッケージの登録方法の詳細については、「[Windows ストア アプリケーション パッケージを Microsoft 認証に登録する](/ja-jp/develop/mobile/how-to-guides/register-windows-store-app-package/%20target="_blank")」を参照してください。パッケージ情報が Mobile Services に登録された後、資格情報を再利用するには、_useSingleSignOn_ パラメーターに値 **true** を指定して [LoginAsync](http://go.microsoft.com/fwlink/p/?LinkId=311594%20target="_blank") メソッドを呼び出します。

<h3>クライアント フロー</h3>

アプリケーションは個別に ID プロバイダーにアクセスして、返されたトークンを認証のためにモバイル サービスに提供することもできます。このクライアント フローでは、ユーザーにシングル サインイン エクスペリエンスを提供したり、ID プロバイダーから追加のユーザー データを取得したりすることができます。

最も単純な形式では、この Facebook や Google のスニペットに示すようにクライアント フローを使用できます。

	var token = new JObject();
	// Replace access_token_value with actual value of your access token obtained
	// using the Facebook or Google SDK.
	token.Add("access_token", "access_token_value");

	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				// Change MobileServiceAuthenticationProvider.Facebook
				// to MobileServiceAuthenticationProvider.Google if using Google auth.
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
				message =
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}

Microsoft アカウントを使用する場合は、次の方法でログインします。

	// Replace authentication_token_value with actual value of your Microsoft authentication token obtained through the Live SDK
	user = await client
		.LoginWithMicrosoftAccountAsync(authentication_token_value);

Microsoft アカウントを使用してシングル サインイン エクスペリエンスを提供する方法の例については、シングル サインインによるアプリケーションの認証に関するチュートリアル ([Windows ストア](/ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)/[Windows Phone](/ja-jp/develop/mobile/tutorials/single-sign-on-wp8/)) を参照してください。

<h3><a name="caching"></a>認証トークンをキャッシュする</h3>
場合によっては、最初のユーザー認証の後の login メソッドの呼び出しを避けることができます。そのためには、Windows ストア アプリ用の [PasswordVault] を使用して、ユーザーが初めてログインするときに使用した現在のユーザー ID をキャッシュし、それ以降はユーザー ID がキャッシュに保存されているかどうかをチェックします。キャッシュが空の場合は、ユーザーにログイン プロセスを実行してもらう必要があります。

	// After logging in
	PasswordVault vault = new PasswordVault();
	vault.Add(new PasswordCredential("Facebook", user.UserId, user.MobileServiceAuthenticationToken));

	// Log in
	var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
	if (creds != null)
	{
		user = new MobileServiceUser(creds.UserName);
		user.MobileServiceAuthenticationToken = vault.Retrieve("Facebook", creds.UserName).Password;
	}
	else
	{
		// Regular login flow
		user = new MobileServiceuser( await client
			.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
		var token = new JObject();
		// Replace access_token_value with actual value of your access token
		token.Add("access_token", "access_token_value");
	}

	 // Log out
	client.Logout();
	vault.Remove(vault.Retrieve("Facebook", user.UserId));


Windows Phone アプリの場合は、[ProtectedData] クラスを使用してデータの暗号化とキャッシュを行い、分離ストレージに機密情報を保存することもできます。

<h2><a name="errors"></a>方法:エラーを処理する</h2>

モバイル サービスには、エラーの検出、検証、回避のためのさまざまな方法があります。

たとえば、サーバー スクリプトは、モバイル サービスに登録され、挿入や更新が行われるデータでの広範な操作 (検証やデータの修正を含む) の実行に使用できます。次のようにデータを検証および変更するサーバー スクリプトを定義し、登録するとします。

	function insert(item, user, request)
	{
	   if (item.text.length > 10) {
		  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
	   } else {
		  request.execute();
	   }
	}

サーバー側スクリプトは、モバイル サービスに送信された文字列データの長さを検証し、長すぎる文字列 (この場合は 10 文字を超える) を拒否します。

Mobile Services はデータを検証してエラー応答をサーバー側に送信するため、検証からのエラー応答を処理できるように .NET アプリケーションを更新することができます。

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and Mobile Services has assigned an Id, the item is added to the CollectionView
		try
		{
			await todoTable.InsertAsync(todoItem);
			ite
	ms.Add(todoItem);
		}
		catch (MobileServiceInvalidOperationException e)
		{
			// Handle error
		}
	}

<h2><a name="untyped"></a>方法:型指定のないデータを処理する</h2>

.NET クライアントは厳密に型指定されたシナリオ向けに設計されます。ただし、大まかに型指定されたエクスペリエンスの方が便利であることもあります。たとえば、オープン スキーマでオブジェクトを処理する場合です。このシナリオには次のように対応できます。クエリでは、LINQ を使用せずに、ワイヤ形式を使用します。

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

プロパティ バッグのように使用できる JSON 値が返されます。JToken と Json.NET の詳細については、[Json.NET](http://json.codeplex.com/) を参照してください。

<h2><a name="unit-testing"></a>方法:単体テストを設計する</h2>

 `MobileServiceClient.GetTable` とクエリによって返される値はインターフェイスです。そのためテスト目的で簡単にモックを作成することができるので、テスト ロジックを実装する `MyMockTable : IMobileServiceTable<TodoItem>` を作成できます。

<h2><a name="customizing"></a>方法:クライアントをカスタマイズする</h2>

### <a name="headers"></a>方法:要求ヘッダーをカスタマイズする

場合によっては、すべての送信要求にカスタム ヘッダーをアタッチしたり、応答のステータス コードを変更したりする必要があります。これは、次のような DelegatingHandler を構成することによって実現できます。

	public async Task CallClientWithHandler()
	{
		MobileServiceClient client = new MobileServiceClient(
			"AppUrl",
			"AppKey" ,
			new MyHandler()
			);
		IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
		var newItem = new TodoItem { Text = "Hello world", Complete = false };
		await table.InsertAsync(newItem);
	}

	public class MyHandler : DelegatingHandler
	{
		protected override async Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
		{
			request.Headers.Add("x-my-header", "my value");
			var response = awaitbase.SendAsync(request, cancellationToken);
			response.StatusCode = HttpStatusCode.ServiceUnavailable;
			return response;
		}
	}


### <a name="serialization"></a>方法:シリアル化をカスタマイズする

[MobileServiceClient](http://msdn.microsoft.com/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) クラスは、[JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm) 型の  `SerializerSettings` プロパティを公開します。

このプロパティを使用すると、Json.NET のプロパティ (多数あり) を設定できます。たとえば、すべてのプロパティを小文字に変換する場合は次のようになります。

	var settings = new JsonSerializerSettings();
	settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
	client.SerializerSettings = settings;

<h2><a name="nextsteps"></a>次のステップ</h2>

使用方法の概念リファレンス トピックを完了した後は、モバイル サービスで重要なタスクを実行する方法について詳しく確認してください。

* [モバイル サービスの使用]
  <br/>モバイル サービスを使用する方法の基本について説明します。

* [データの使用]
  <br/>Mobile Services を使用してデータの格納およびクエリを実行する方法について説明します。

* [認証の使用]
  <br/>ID プロバイダーを使用してアプリケーションのユーザーを認証する方法について説明します。

* [スクリプトを使用したデータの検証および変更]
  <br/>モバイル サービスでサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

* [ページングを使用したクエリの改善]
  <br/>クエリ内でページングを使用して、単一の要求で渡されるデータの量を制御する方法について説明します。

* [スクリプトを使用したユーザーの承認]
  <br/>認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理する方法について説明します。

<!-- Anchors. -->
[Mobile Services とは]: #what-is
[概念]: #concepts
[方法:モバイル サービス クライアントを作成する]: #create-client
[方法:テーブル参照を作成する]: #instantiating
[方法:モバイル サービスのデータを照会する]: #querying
[返されるデータをフィルター処理する]: #filtering
[返されるデータを並べ替える]: #sorting
[ページにデータを返す]: #paging
[特定の列を選択する]: #selecting
[ID でデータを検索する]: #lookingup
[方法:モバイル サービスでユーザー インターフェイスにデータをバインドする]: #binding
[方法:モバイル サービスにデータを挿入する]: #inserting
[方法:モバイル サービスのデータを変更する]: #modifying
[方法:モバイル サービスのデータを削除する]: #deleting
[方法:オプティミスティック同時実行制御を使用する]: #optimisticconcurrency
[方法:ユーザーを認証する]: #authentication
[方法:エラーを処理する]: #errors
[方法:単体テストを設計する]: #unit-testing
[方法:モバイル サービスのデータを照会する]: #querying
[方法:クライアントをカスタマイズする]: #customizing
[方法:型指定のないデータを処理する]: #untyped
[要求ヘッダーをカスタマイズする]: #headers
[シリアル化をカスタマイズする]: #serialization
[次のステップ]: #nextsteps
[認証トークンをキャッシュする]: #caching
[方法:カスタム API の呼び出し]: #custom-api

<!-- Images. -->



<!-- URLs. -->
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started
[モバイル サービス SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Windows ストアのクイック スタート チュートリアル]: http://azure.microsoft.com/develop/mobile/tutorials/get-started/
[Windows Phone のクイック スタート チュートリアル]: http://azure.microsoft.com/develop/mobile/tutorials/get-started-wp8/
[Windows ストアのデータ チュートリアル]: http://azure.microsoft.com/develop/mobile/tutorials/get-started-with-data-dotnet/
[Windows Phone のデータ チュートリアル]: http://azure.microsoft.com/develop/mobile/tutorials/get-started-with-data-wp8/
[Windows ストアの認証]: http://azure.microsoft.com/develop/mobile/tutorials/get-started-with-users-dotnet/
[Windows Phone の認証]: http://azure.microsoft.com/develop/mobile/tutorials/get-started-with-users-wp8/
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[モバイル サービス SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[モバイル サービス SDK]: http://nuget.org/packages/WindowsAzure.MobileServices/
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet/
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet
[スクリプトを使用したデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[ページングを使用したクエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-dotnet
[スクリプトを使用したユーザーの承認]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[LoginAsync メソッド]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
[ASCII 制御コード C0 および C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services テーブルの管理用コマンド]: http://azure.microsoft.com/manage/linux/other-resources/command-line-tools/#Mobile_Tables
[オプティミスティック同時実行制御チュートリアル]: http://azure.microsoft.com/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/

[IncludeTotalCount]: http://msdn.microsoft.com/library/windowsazure/dn250560.aspx
[Skip]: http://msdn.microsoft.com/library/windowsazure/dn250573.aspx
[Take]: http://msdn.microsoft.com/library/windowsazure/dn250574.aspx
[Fiddler]: http://www.telerik.com/fiddler
[Azure Mobile Services クライアント SDK のカスタム API]: http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx
[ クライアントからのカスタム API 呼び出し]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api/
[InvokeApiAsync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx


<!--HONumber=42-->
