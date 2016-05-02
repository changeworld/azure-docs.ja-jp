<properties
	pageTitle="App Service Mobile Apps の管理されたクライアント ライブラリの操作 (Windows | Xamarin) | Microsoft Azure"
	description="Windows と Xamarin アプリで Azure App Service Mobile Apps 向け .NET クライアントを使用する方法について説明します。"
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/08/2016"
	ms.author="glenga"/>

# Azure Mobile Apps 用の管理されたクライアントの使用方法

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

##概要

このガイドでは、Windows および Xamarin アプリで Azure App Service Mobile Apps 用の管理されたクライアント ライブラリを使用する一般的なシナリオの実行方法を示します。Mobile Apps を初めて使用する場合は、まず、[Azure Mobile Apps のクイックスタート] チュートリアルを完了することを検討してください。このガイドでは、クライアント側の管理された SDK に重点を置いています。Mobile Apps 用のサーバー側 SDK の詳細については、[.NET Server SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) または [Node.js Server SDK](app-service-mobile-node-backend-how-to-use-server-sdk.md) 関するドキュメントを参照してください。

## リファレンス ドキュメント

クライアント SDK のリファレンス ドキュメントについては、「[Azure Mobile Apps .NET クライアント リファレンス]」を参照してください。GitHub リポジトリの「[Azure Samples (Azure サンプル)]」にも、いくつかのクライアント サンプルがあります。

##<a name="setup"></a>セットアップと前提条件

テーブルを少なくとも 1 つ含むモバイル アプリ バックエンド プロジェクトを既に作成して発行してあるものとします。このトピックで使用するコードでは、テーブルの名前は `TodoItem` であり、`Id`、`Text`、および `Complete` という列があります。これは、[Azure Mobile Apps クイックスタート]で作成したものと同じテーブルです。

これに対応する型指定されたクライアント側 C# 型を次に示します。

	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

[JsonPropertyAttribute] を使用してクライアントの型とテーブルの間の *PropertyName* のマッピングが定義されていることに注意してください。

Mobile Apps バックエンドに新しいテーブルを作成する方法については、「[方法: テーブル コントローラーを定義する](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller)」(.NET Server SDK) または「[方法: 動的スキーマを使用してテーブルを定義する](app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-dynamicschema)」(Node.js Server SDK) の情報を参照してください。Azure ポータルでクイックスタートを使用して Mobile App バックエンドを作成した場合は、[Azure ポータル]で **Easy tables** 設定を使用することもできます。

###<a name="symbolsource"></a>方法: Visual Studio でデバッグ シンボルを使用する

Microsoft.Azure.Mobile 名前空間用のシンボルは、[SymbolSource] で利用できます。SymbolSource と Visual Studio を統合するには、[SymbolSource の説明]を参照してください。

##<a name="create-client"></a>Mobile App クライアントの作成

次のコードは、Mobile App バックエンドへのアクセスに使用する [MobileServiceClient] オブジェクトを作成します。

	MobileServiceClient client = new MobileServiceClient("MOBILE_APP_URL");

上記のコードで、`MOBILE_APP_URL` を、[Azure ポータル]のモバイル アプリ バックエンド用のブレードで確認できるモバイル アプリ バックエンドの URL に置き換えます。クライアント インスタンスはシングルトンであることが通常であり、推奨されます。

## テーブルの操作

以下のセクションでは、レコードを検索し、取得する方法や、テーブル内のデータを変更する方法について詳しく説明します。次のトピックについて説明します。

* [テーブル参照を作成する](#instantiating)
* [データのクエリを実行する](#querying)
* [返されるデータをフィルター処理する](#filtering)
* [返されるデータを並べ替える](#sorting)
* [ページにデータを返す](#paging)
* [特定の列を選択する](#selecting)
* [ID でレコードを検索する](#lookingup)
* [型指定のないクエリを処理する](#untypedqueries)
* [データを挿入する](#inserting)
* [データを更新する](#updating)
* [データを削除する](#deleting)
* [競合の解決とオプティミスティック同時実行制御](#optimisticconcurrency)
* [Windows ユーザー インターフェイスへのバインド](#binding)

###<a name="instantiating"></a>方法: テーブル参照を作成する

バックエンド テーブルのデータにアクセスする、またはデータを変更するすべてのコードは、`MobileServiceTable` オブジェクトに対して関数を呼び出します。次のように、`MobileServiceClient` のインスタンスで [GetTable] メソッドを呼び出して、テーブルへの参照を取得します。

    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();

これは、型指定されたシリアル化モデルです。型指定されていないシリアル化モデルもサポートされます。次のコードは、[型指定されていないテーブルへの参照を作成します]。

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

型指定されていないクエリでは、基になる OData クエリ文字列を指定する必要があります。

###<a name="querying"></a>方法: Mobile App のデータを照会する

このセクションでは、モバイル アプリ バックエンドにクエリを発行する方法について説明します。これには次の機能が含まれます。

- [返されるデータをフィルター処理する]
- [返されるデータを並べ替える]
- [ページにデータを返す]
- [特定の列を選択する]
- [ID でデータを検索する]

>[AZURE.NOTE] すべての行が返されるのを防ぐために、サーバー側で設定されたページ サイズが適用されます。これにより、大きなデータ セットの既定の要求がサービスに悪影響を与えないようにします。50 以上の行を返すには、「[ページにデータを返す]」で説明されている `Take` メソッドを使用します。

###<a name="filtering"></a>方法: 返されるデータをフィルター処理する

次のコードは、クエリに `Where` 句を含めることによってデータをフィルター処理する方法を示しています。このコードは、`Complete` プロパティが `false` に等しい `todoTable` からすべての項目を返します。[Where] 関数は、行のフィルタリング述語をテーブルに対するクエリに適用します。

	// This query filters out completed TodoItems and items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

ブラウザー開発者ツールや [Fiddler] などのメッセージ検査ソフトウェアを使用して、バックエンドに送信された要求の URI を表示できます。次の要求 URI を確認すると、クエリ文字列が変更されていることがわかります。

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

この OData 要求は、Server SDK によって次のように SQL クエリに変換されます。

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

`Where` メソッドに渡される関数には、任意の数の条件を設定できます。たとえば、次のコードがあるとします。

	// This query filters out completed TodoItems where Text isn't null
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
	   .ToListAsync();

Server SDK によって次のように SQL クエリに変換されます。

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND ISNULL(text, 0) = 0

前の `WHERE` ステートメントは、null 以外の `Text` を持ち、`Complete` のステータスが false に設定されている項目を検索します。

このクエリは複数の句に分割することもできます。

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

この 2 つの方法は等価であり、区別しないで使用できます。複数の述語を 1 つのクエリに連結する前のオプションが、よりコンパクトでありお勧めです。

`Where` 句は、OData サブセットに変換される操作をサポートします。これには、関係演算子 (==、! =、<、<=、>、>=)、算術演算子 (+、-、/、*、%)、数の精度 (Math.Floor、Math.Ceiling)、文字列関数 (Length、Substring、Replace、IndexOf、StartsWith、EndsWith)、日付プロパティ (Year、Month、Day、Hour、Minute、Second)、オブジェクトのアクセス プロパティ、これらすべてを組み合わせた式が含まれます。Server SDK のサポート対象を考慮する際に、[OData バージョン 3 のドキュメント]を検討することができます。

###<a name="sorting"></a>方法: 返されるデータを並べ替える

次のコードは、クエリに [OrderBy] または [OrderByDescending] 関数を含めることによってデータを並べ替える方法を示しています。次のコードは、`todoTable` から、`Text` フィールドの値に基づいて昇順に並べ替えられた項目を返します。

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

###<a name="paging"></a>方法: ページにデータを返す

既定では、バックエンドは最初の 50 行のみを返します。[Take] メソッドを呼び出すことによって、返される行の数を増やすことができます。[Skip] メソッドと共に `Take` を使用して、クエリによって返されるデータセット全体のうち特定の "ページ" を要求します。次のクエリを実行すると、テーブルの最初の上位 3 つの項目が返されます。

	// Define a filtered query that returns the top 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

次の変更されたクエリは、最初の 3 つの結果をスキップし、その後の 3 つを返します。ページ サイズが 3 つの項目である場合、これは実質的にデータの 2 番目の "ページ" になります。

	// Define a filtered query that skips the top 3 items and returns the next 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Skip(3)
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

また、[IncludeTotalCount] メソッドを使用すると、指定された take paging/limit 句を無視して、返される<i>すべての</i>レコードの合計数を取得することができます。

	query = query.IncludeTotalCount();

これは、ハードコーディングされたページング値を `Take` メソッドおよび `Skip` メソッドに渡す、簡略化したシナリオです。実際のアプリケーションでは、ユーザーが前後のページに移動できるように、ページャー コントロールまたは同等の UI と共に上記と同様のクエリを使用することができます。

>[AZURE.NOTE]モバイル アプリ バックエンドの 50 行の制限をオーバーライドするには、[EnableQueryAttribute] をパブリック GET メソッドに適用してページング動作を指定することも必要です。次のコードをメソッドに適用すると、返される最大行数が 1000 行に設定されます。
>
>    [EnableQuery(MaxTop=1000)]

### <a name="selecting"></a>方法: 特定の列を選択する

クエリに [Select] 句を追加し、結果に含める一連のプロパティを指定できます。たとえば、次のコードでは、1 つのフィールドだけを選択する方法と、複数のフィールドを選択し、フォーマットする方法も示しています。

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();

	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}",
						todoItem.Text.PadRight(30), todoItem.Complete ?
						"Now complete!" : "Incomplete!"));
	List<string> items = await query.ToListAsync();

これまでに説明した関数はいずれも付加的なものであるため、この後も呼び出すようにし、クエリに活用することにします。次の例も参照してください。

	MobileServiceTableQuery<TodoItem> query = todoTable
					.Where(todoItem => todoItem.Complete == false)
					.Select(todoItem => todoItem.Text)
					.Skip(3).
					.Take(3);
	List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>方法: ID でデータを検索する

[LookupAsync] 関数は、特定の ID でデータベースのオブジェクトを検索するために使用します。

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="untypedqueries"></a>方法: 型指定されていないクエリを実行する

型指定されていないテーブル オブジェクトを使用してクエリを実行するときは、次の例のように [ReadAsync] を呼び出して、OData クエリ文字列を明示的に指定する必要があります。

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

プロパティ バッグのように使用できる JSON 値が返されます。JToken と Newtonsoft Json.NET の詳細については、[Json.NET] のサイトを参照してください。

### <a name="inserting"></a>方法: モバイル アプリ バックエンドにデータを挿入する

クライアントのすべての型には、**Id** という名前のメンバーが含まれる必要があります。その既定値は文字列です。この **Id** は、オフラインで CRUD 操作を実行するために必要です。次のコードは、[InsertAsync] メソッドを使用してテーブルに新しい行を挿入する方法を示しています。パラメーターには、挿入するデータが .NET オブジェクトとして含まれます。

	await todoTable.InsertAsync(todoItem);

`todoTable.InsertAsync` 呼び出しに渡された `todoItem` に一意のカスタム ID 値が含まれていない場合は、ID の値がサーバーによって生成され、クライアントに返される `todoItem` オブジェクトに設定されます。

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

###ID 値の操作

Mobile Apps は、テーブルの **ID** 列で一意のカスタム文字列値をサポートしています。このため、アプリケーションは、ID にメール アドレスやユーザー名などのカスタム値を使用できます。

文字列 ID には、次のような利点があります。

* データベースと往復することなく ID が生成されます。
* 他のテーブルやデータベースのレコードをより簡単にマージできます。
* ID 値をより適切にアプリケーションのロジックに統合できます。

挿入されたレコードで文字列 ID 値が設定されない場合は、モバイル アプリ バックエンドによって ID 用の一意の値が生成されます。[Guid.NewGuid] メソッドを使用すれば、クライアント上またはバックエンドで、独自の ID 値を生成することができます。

    JObject jo = new JObject();
    jo.Add("id", Guid.NewGuid().ToString("N"));

###<a name="modifying"></a>方法: モバイル アプリ バックエンドのデータを変更する

次のコードは、[UpdateAsync] メソッドを使用して、同じ ID を持つ既存のレコードを新しい情報で更新する方法を示しています。パラメーターには、更新するデータが .NET オブジェクトとして含まれます。

	await todoTable.UpdateAsync(todoItem);

型指定されていないデータを挿入するには、次のような [Json.NET] を利用できます。

	JObject jo = new JObject();
	jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

更新を行うときは `id` フィールドを指定する必要があります。バックエンドはそれによって更新するインスタンスを識別します。`id` フィールドは、`InsertAsync` の呼び出しの結果から取得できます。`id` の値を指定せずに項目を更新しようとすると、`ArgumentException` が発生します。

###<a name="deleting"></a>方法: モバイル アプリ バックエンドのデータを削除する

次のコードは、[DeleteAsync] メソッドを使用して既存のインスタンスを削除する方法を示しています。インスタンスは、`todoItem` に設定した `id` フィールドで識別されます。

	await todoTable.DeleteAsync(todoItem);

型指定されていないデータを削除するには、次のような Json.NET を利用できます。

	JObject jo = new JObject();
	jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

削除要求を行うときは、ID を指定する必要があります。それ以外のプロパティは、サービスに渡されないか、またはサービスで無視されます。通常、`DeleteAsync` の呼び出しの結果は `null` です。渡す ID は、`InsertAsync` の呼び出しの結果から取得できます。`id` フィールドに値を指定せず項目を削除しようとすると、`MobileServiceInvalidOperationException` がスローされます。

###<a name="optimisticconcurrency"></a>方法: 競合解決にオプティミスティック同時実行制御を使用する

複数のクライアントが同じ項目に対して同時に変更を書き込む場合があります。競合を検知しない場合、それを意図していなくても、最後に行われた書き込みによってそれ以前の更新がすべて上書きされます。*オプティミスティック同時実行制御*では、それぞれのトランザクションがコミットでき、そのためリソース ロックが一切使用されないことを前提としています。オプティミスティック同時実行制御ではトランザクションをコミットする前に、他のトランザクションがそのデータを変更していないことを確認します。データが変更されている場合、トランザクションのコミットはロール バックされます。

Mobile Apps はオプティミスティック同時実行制御をサポートしており、モバイル アプリ バックエンドで各テーブルに定義されている `version` システム プロパティ列を使用して各項目の変更を追跡します。レコードが更新されるたびに、Mobile Apps はそのレコードの `version` プロパティを新しい値に設定します。各更新要求の際に、要求に含まれているレコードの `\version` プロパティが、サーバー上のレコードの同じプロパティと比較されます。要求で渡されたバージョンがバックエンドと一致しない場合、クライアント ライブラリは `MobileServicePreconditionFailedException<T>` 例外を生成します。例外に含まれている型は、レコードのサーバー側のバージョンを含んでいるバックエンドのレコードです。アプリケーションはこの情報を使用して、バックエンドからの正しい `version` 値で更新要求をもう一度実行して変更をコミットするかどうかを判断できます。

オプティミスティック同時実行制御を有効にするために、たとえば次のように、テーブル クラスに `version` システム プロパティ用の列を定義します。

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

		// *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "version")]
        public byte[] Version { set; get; }
    }


型指定のないテーブルを使用しているアプリケーションは、テーブルの `SystemProperties` に次のように `Version` フラグを設定することによって、オプティミスティック同時実行制御を有効にします。

	//Enable optimistic concurrency by retrieving version
	todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

オプティミスティック同時実行制御を有効にすることに加えて、[UpdateAsync] の呼び出し時にコード内で起きた `MobileServicePreconditionFailedException<T>` 例外をキャッチすることも必要です。更新されるレコードに正確な `version` を適用して競合を解決し、解決済みのレコードで [UpdateAsync] を呼び出します。次のコードは、書き込み競合が検出された場合にそれを解決する方法を示しています。

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
	    MessageDialog msgDialog = new MessageDialog(
            String.Format("Server Text: "{0}" \nLocal Text: "{1}"\n",
            serverItem.Text, localItem.Text),
            "CONFLICT DETECTED - Select a resolution:");

	    UICommand localBtn = new UICommand("Commit Local Text");
    	UICommand ServerBtn = new UICommand("Leave Server Text");
    	msgDialog.Commands.Add(localBtn);
	    msgDialog.Commands.Add(ServerBtn);

    	localBtn.Invoked = async (IUICommand command) =>
	    {
        	// To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
        	// catching a MobileServicePreConditionFailedException.
	        localItem.Version = serverItem.Version;

    	    // Updating recursively here just in case another change happened while the user was making a decision
	        UpdateToDoItem(localItem);
    	};

	    ServerBtn.Invoked = async (IUICommand command) =>
    	{
	        RefreshTodoItems();
    	};

	    await msgDialog.ShowAsync();
	}

詳細については、「[Azure Mobile Apps でのオフライン データ同期]」トピックを参照してください。

###<a name="binding"></a>方法: Mobile Apps のデータを Windows ユーザー インターフェイスにバインドする

このセクションでは、Windows アプリで UI 要素を使用して、返されたデータ オブジェクトを表示する方法について説明します。次のコード例を実行すると、`todoTable` 内の未完了の項目に対するクエリでリストのソースにバインドし、ごくシンプルな一覧として表示できます。[MobileServiceCollection] を使用すると、Mobile Apps 対応のバインディング コレクションが作成されます。

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

マネージ ランタイムの一部のコントロールでは、[ISupportIncrementalLoading] というインターフェイスがサポートされます。このインターフェイスにより、コントロールはユーザーによるスクロールの際に追加のデータを要求することができます。このインターフェイスには、[MobileServiceIncrementalLoadingCollection] によるユニバーサル Windows アプリ用の組み込みサポートがあり、コントロールからの呼び出しが自動で処理されます。Windows アプリで `MobileServiceIncrementalLoadingCollection` を使用するには、次を実行します。

    MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
    items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Windows Phone 8 と "Silverlight" アプリで新しいコレクションを使用するには、`IMobileServiceTableQuery<T>` や `IMobileServiceTable<T>` で `ToCollection` 拡張メソッドを使用します。実際にデータを読み込むには、`LoadMoreItemsAsync()` を呼び出します。

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

`ToCollectionAsync` または `ToCollection` を呼び出して作成されたコレクションを使用する場合は、UI コントロールにバインドできるコレクションを取得します。このコレクションはページングに対応しています。つまり、コントロールが "項目をさらに読み込む" ようにコレクションに依頼し、コレクションがそれを実行します。その時点で、ユーザー コードは関係せず、コントロールはフローを開始します。ただし、コレクションはネットワークからデータを読み込むため、読み込みが失敗することもあると想定されます。このようなエラーを処理するには、`MobileServiceIncrementalLoadingCollection` で `OnException` メソッドをオーバーライドし、コントロールが実行する `LoadMoreItemsAsync` の呼び出しの結果として生じる例外を処理する必要があります。

最後に、テーブルには多くのフィールドが存在するものの、コントロールにはその一部のみを表示する必要があるとします。UI に表示する特定の列を選ぶ際には、前のセクション「[特定の列を選択する](#selecting)」のガイダンスを参考にしてください。

##<a name="#customapi"></a>カスタム API の使用

カスタム API を使用してカスタム エンドポイントを定義することにより、insert、update、delete、read のいずれの操作にも関連しないサーバー機能を公開することができます。カスタム API を使用することによって、HTTP メッセージ ヘッダーの読み取りや設定、JSON 以外のメッセージ本文形式の定義など、メッセージングをより柔軟に制御することができます。

クライアントで [InvokeApiAsync] メソッド オーバーロードの 1 つを呼び出して、カスタム API を呼び出します。たとえば、次のコード行は、バックエンドの **completeAll** API に POST 要求を送信します。

    var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);

これは型指定メソッドの呼び出しのため、返される **MarkAllResult** の型を定義する必要があります。型指定および型指定のないメソッドの両方がサポートされます。


##<a name="authentication"></a>ユーザーの認証

Mobile Apps は、Facebook、Google、Microsoft アカウント、Twitter、Azure Active Directory などのさまざまな外部 ID プロバイダーを使用したアプリケーション ユーザーの認証と承認をサポートします。テーブルのアクセス許可を設定することにより、特定の操作へのアクセスを認証されたユーザーのみに制限できます。さらに、認証されたユーザーの ID を使用することにより、サーバー スクリプトで承認ルールを実装することもできます。詳細については、チュートリアル「[アプリへの認証の追加]」を参照してください。

_サーバー フロー_と_クライアント フロー_という 2 つの認証フローがサポートされます。サーバー フローには、プロバイダーの Web 認証のインターフェイスを利用する、最も簡単な認証方法が用意されています。クライアント フローでは、プロバイダー固有とデバイス固有の SDK を利用することから、デバイス固有の機能との統合がさらに進みます。

いずれの場合でも、アプリケーションを ID プロバイダーに登録する必要があります。ID プロバイダーは、クライアント ID とクライアント シークレットを提示します。その後、ID プロバイダーが提示したクライアント ID とクライアント シークレットで、Azure App Service 認証/承認を構成する必要があります。詳細については、「[Windows アプリに認証を追加する]」の詳しい説明に従ってください。

###<a name="serverflow"></a>サーバー フロー
ID プロバイダーを登録した後は、プロバイダーの [MobileServiceAuthenticationProvider] 値で MobileServiceClient.[LoginAsync メソッド] を呼び出します。たとえば、次のコードは、Facebook を使用してサーバー フローのサインインを開始します。

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

サーバー フローでは、Azure App Service は、選択されたプロバイダーのサインイン ページを表示し、ID プロバイダーでのサインオンが成功した後に App Service 認証トークンを生成することで、OAuth 2.0 認証フローを管理します。[LoginAsync メソッド] は [MobileServiceUser] を返します。これによって、認証されたユーザーの [UserId] と、JSON Web トークン (JWT) としての [MobileServiceAuthenticationToken] が提供されます。このトークンはキャッシュして、期限が切れるまで再利用することができます。詳細については、「[認証トークンをキャッシュする](#caching)」を参照してください。

###<a name="client-flow"></a>クライアント フロー

アプリケーションは個別に ID プロバイダーにアクセスして、返されたトークンを認証のために App Service に提供することもできます。このクライアント フローでは、ユーザーにシングル サインイン エクスペリエンスを提供したり、ID プロバイダーから追加のユーザー データを取得したりすることができます。

####Facebook や Google から取得したトークンを利用したシングル サインイン

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
				user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
				message = string.Format("You are now logged in - {0}", user.UserId);
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

####Microsoft アカウントと Live SDK を利用したシングル サインイン

ユーザーを認証できるようにするには、Microsoft アカウント デベロッパー センターでアプリケーションを登録する必要があります。この登録を Mobile App バックエンドと接続する必要があります。[Microsoft アカウント ログインを使用するためのアプリケーションの登録]の手順を完了して Microsoft アカウント登録を作成し、それを Mobile App バックエンドに接続します。Windows ストア と Windows Phone 8/Silverlight の両方のバージョンのアプリがある場合は、Windows ストア バージョンを最初に登録します。

次のコードでは Live SDK を使用して認証し、返されたトークンを利用して Mobile App バックエンドにサインインします。

	private LiveConnectSession session;
 	//private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the Mobile App backend.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the service URL.
        LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
        //// Create the authentication client for Windows Phone using the client ID of the registration.
        //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

        while (session == null)
        {
            // Request the authentication token from the Live authentication service.
			// The wl.basic scope should always be requested.  Other scopes can be added
            LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
            if (result.Status == LiveConnectSessionStatus.Connected)
            {
                session = result.Session;

                // Get information about the logged-in user.
                LiveConnectClient client = new LiveConnectClient(session);
                LiveOperationResult meResult = await client.GetAsync("me");

                // Use the Microsoft account auth token to sign in to App Service.
                MobileServiceUser loginResult = await App.MobileService
                    .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                // Display a personalized sign-in greeting.
                string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                var dialog = new MessageDialog(message, title);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
            else
            {
                session = null;
                var dialog = new MessageDialog("You must log in.", "Login Required");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
    }

[Windows Live SDK] の詳細については、ドキュメントを参照してください。

###<a name="caching"></a>認証トークンをキャッシュする
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

-->

### <a name="adal"></a>Active Directory 認証ライブラリを使用してユーザーを認証する

Active Directory 認証ライブラリ (ADAL) を使用して、Azure Active Directory を使用しているアプリケーションにユーザーをサインインさせることができます。これにより、よりネイティブな UX が実現し、さらにカスタマイズすることが可能になるため、多くの場合、`loginAsync()` メソッドを使用する方法よりも推奨されます。

1. 「[Azure Active Directory ログインを使用するように App Service アプリケーションを構成する方法]」のチュートリアルに従って、AAD のサインイン用にモバイル アプリ バックエンドを構成します。ネイティブ クライアント アプリケーションを登録する省略可能な手順を確実に実行します。

2. Visual Studio または Xamarin Studio で、プロジェクトを開き、`Microsoft.IdentityModel.CLients.ActiveDirectory` NuGet パッケージへの参照を追加します。検索時に、プレリリース版を含めます。

3. ご使用のプラットフォームに応じて、以下のコードをアプリケーションに追加します。それぞれで、次の置換を行います。

* **INSERT-AUTHORITY-HERE** を、アプリケーションをプロビジョニングしたテナントの名前に置き換えます。形式は https://login.windows.net/contoso.onmicrosoft.com である必要があります。この値は、[[Azure クラシック ポータル]] の Azure Active Directory の [ドメイン] タブからコピーできます。

* **INSERT-RESOURCE-ID-HERE** を、モバイル アプリ バックエンドのクライアント ID に置き換えます。これは、ポータルの **[Azure Active Directory の設定]** の **[詳細]** タブで入手できます。

* **INSERT-CLIENT-ID-HERE** を、ネイティブ クライアント アプリケーションからコピーしたクライアント ID に置き換えます。

* **INSERT-REDIRECT-URI-HERE** を、HTTPS スキームを使用してサイトの _/.auth/login/done_ エンドポイントに置き換えます。この値は、_https://contoso.azurewebsites.net/.auth/login/done_ と同様です。

各プラットフォームに必要なコードは次のとおりです。

**Windows:**

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {
        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        while (user == null)
        {
            string message;
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                message = string.Format("You are now logged in - {0}", user.UserId);
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

**Xamarin.iOS**

    private MobileServiceUser user;
    private async Task AuthenticateAsync(UIViewController view)
    {
        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }

**Xamarin.Android**

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {
        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(ex.Message);
            builder.SetTitle("You must log in. Login Required");
            builder.Create().Show();
        }
    }
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }

##<a name="pushnotifications">プッシュ通知

以下のトピックでは、プッシュ通知について説明します。

* [プッシュ通知に登録する](#register-for-push)
* [Windows ストアのパッケージ SID を取得する](#package-sid)
* [クロスプラットフォームのテンプレートで登録する](#register-xplat)

###<a name="register-for-push"></a>方法: プッシュ通知に登録する

Mobile Apps クライアントでは、Azure Notification Hubs によるプッシュ通知に登録できます。登録する場合、プラットフォーム固有のプッシュ通知サービス (PNS) からハンドルを取得します。この値は、登録を作成するときに、任意のタグと一緒に指定します。次のコードは、Windows Notification Service (WNS) によるプッシュ通知用の Windows アプリを登録します。

    private async void InitNotificationsAsync()
    {
        // Request a push notification channel.
        var channel =await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // Register for notifications using the new channel.
        await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
    }

WNS に対するプッシュを行う場合は、Windows ストア パッケージ SID (下記) を取得する必要があります。この例では、登録で 2 つのタグが含められます。テンプレート登録に登録する方法を含む Windows アプリの詳細については、「[アプリにプッシュ通知を追加する]」を参照してください。

クライアントからのタグ要求はサポートされていません。タグ要求は、通告なく登録から削除されます。タグと共にデバイスを登録したい場合は、Notification Hubs API を使用するカスタム API を作成し、登録を代行させます。`RegisterNativeAsync()` メソッドの代わりに、[カスタム API](#customapi) を呼び出します。

###<a name="package-sid"></a>方法: Windows ストアのパッケージ SID を取得する

Windows ストア アプリでプッシュ通知を有効にするには、パッケージ SID が必要です。パッケージ SID は、他のことにも使われます (Windows シングル サインインなど)。パッケージ SID を受け取るには、Windows ストアでアプリケーションを登録する必要があります。

この値を取得するには:

1. Visual Studio ソリューション エクスプローラーで、Windows Store アプリ プロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。
2. ウィザードで **[次へ]** をクリックし、Microsoft アカウントでサインインし、**[新しいアプリケーション名の予約]** にアプリの名前を入力し、**[予約]** をクリックします。
3. アプリの登録が正常に作成されたら、新しいアプリ名を選択し、**[次へ]** をクリックし、**[関連付け]** をクリックします。この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。
4. Microsoft アカウントを使用して [Windows デベロッパー センター]にログインします。**[マイ アプリ]** で、先ほど作成したアプリ登録をクリックします。
5. **[アプリ管理]**、**[アプリ ID]** の順にクリックし、下にスクロールして **[パッケージ SID]** を探します。

多くの場合、パッケージ SID は URI として使用されます。その場合はスキームとして _ms-app://_ を使用する必要があります。この値をプレフィックスとして連結して形成されたパッケージ SID のバージョンをメモしておきます。

Xamarin アプリではいくつかの追加コードが必要になります。このコードを使用して、iOS または Android アプリで実行されているアプリを Apple Push Notification サービス (APNS) と Google Cloud Messaging (GCM) にそれぞれ登録することができます。詳細については、ご使用のプラットフォームに関するトピックを参照してください。

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push)

###<a name="register-xplat"></a>方法: プッシュ テンプレートを登録してクロス プラットフォーム通知を送信する

テンプレートを登録するには、次のように、テンプレートと共に `RegisterAsync()` メソッドを使用します。

        JObject templates = myTemplates();
        MobileService.GetPush().RegisterAsync(channel.Uri, templates);

テンプレートは JObject 型となり、次のような JSON 形式で複数のテンプレートを含めることが可能です。

        public JObject myTemplates()
        {
            // single template for Windows Notification Service toast
            var template = "<toast><visual><binding template="ToastText01"><text id="1">$(message)</text></binding></visual></toast>";

            var templates = new JObject
            {
                ["generic-message"] = new JObject
                {
                    ["body"] = template,
                    ["headers"] = new JObject
                    {
                        ["X-WNS-Type"] = "wns/toast"
                    },
                    ["tags"] = new JArray()
                },
                ["more-templates"] = new JObject {...}
            };
            return templates;
        }

**RegisterAsync()** メソッドは次のセカンダリ タイルも受け入れます。

        MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);

セキュリティのためにタグはすべて取り除かれるので注意してください。インストールまたはインストール内のテンプレートにタグを追加する方法については、「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作]」を参照してください。

これらの登録済みテンプレートを使用して通知を送信するには、[Notification Hubs API] を参照します。

##<a name="misc"></a>その他のトピック

###<a name="errors"></a>方法: エラーを処理する

バックエンドでエラーが発生すると、クライアント SDK が `MobileServiceInvalidOperationException` を出します。次の例では、バックエンドによって返される例外を処理する方法を示します。

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and App Service has assigned an Id, the item is added to the CollectionView
		try
		{
			await todoTable.InsertAsync(todoItem);
			items.Add(todoItem);
		}
		catch (MobileServiceInvalidOperationException e)
		{
			// Handle error
		}
	}

エラー状況を処理する他の例は、[Mobile Apps ファイル サンプル]で確認することができます - [LoggingHandler] の例では、ログ記録代理ハンドラー (下記参照) を使用して、バックエンドに対する要求を記録します。この方法では、Fiddler に頼るよりも容易に、Xamarin アプリケーションをデバッグできます。

###<a name="headers"></a>方法: 要求ヘッダーをカスタマイズする

特定のアプリケーション シナリオをサポートするには、モバイル アプリ バックエンドとの通信をカスタマイズすることが必要な場合があります。たとえば、すべての送信要求にカスタム ヘッダーを追加したり、応答のステータス コードを変更したりすることが必要な場合がります。これを行うには、次の例のように、カスタムの [DelegatingHandler] を指定します。

    public async Task CallClientWithHandler()
    {
        HttpResponseMessage[]
        MobileServiceClient client = new MobileServiceClient("AppUrl",
            new MyHandler()
            );
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await todoTable.InsertAsync(newItem);
    }

    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage>
            SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Change the request-side here based on the HttpRequestMessage
            request.Headers.Add("x-my-header", "my value");

            // Do the request
            var response = await base.SendAsync(request, cancellationToken);

            // Change the response-side here based on the HttpResponseMessage

            // Return the modified response
            return response;
        }
    }


<!-- Anchors. -->
[返されるデータをフィルター処理する]: #filtering
[返されるデータを並べ替える]: #sorting
[ページにデータを返す]: #paging
[特定の列を選択する]: #selecting
[ID でデータを検索する]: #lookingup

<!-- Images. -->

<!-- Internal URLs. -->
[Azure Mobile Apps のクイックスタート]: app-service-mobile-windows-store-dotnet-get-started.md
[Azure Mobile Apps クイックスタート]: app-service-mobile-windows-store-dotnet-get-started.md
[Windows アプリに認証を追加する]: app-service-mobile-windows-store-dotnet-get-started-users.md
[アプリへの認証の追加]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Work with .NET backend SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[How to use the Node.js backend SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[How to: Define a table controller]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[Define Tables using a Dynamic Schema]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Azure Mobile Apps でのオフライン データ同期]: app-service-mobile-offline-data-sync.md
[アプリにプッシュ通知を追加する]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Microsoft アカウント ログインを使用するためのアプリケーションの登録]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Azure Active Directory ログインを使用するように App Service アプリケーションを構成する方法]: app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[Azure Mobile Apps .NET クライアント リファレンス]: https://msdn.microsoft.com/ja-JP/library/azure/mt419521(v=azure.10).aspx
[MobileServiceClient]: https://msdn.microsoft.com/ja-JP/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx
[MobileServiceCollection]: https://msdn.microsoft.com/ja-JP/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/ja-JP/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/ja-JP/library/azure/jj554275(v=azure.10).aspx
[型指定されていないテーブルへの参照を作成します]: https://msdn.microsoft.com/ja-JP/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/ja-JP/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/ja-JP/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/ja-JP/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/ja-JP/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/ja-JP/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/ja-JP/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/ja-JP/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/ja-JP/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/ja-JP/library/azure/mt691741(v=azure.10).aspx
[Take]: https://msdn.microsoft.com/ja-JP/library/azure/dn250574(v=azure.10).aspx
[Select]: https://msdn.microsoft.com/ja-JP/library/azure/dn250569(v=azure.10).aspx
[Skip]: https://msdn.microsoft.com/ja-JP/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/ja-JP/library/azure/dn250536.(v=azure.10)aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Where]: https://msdn.microsoft.com/ja-JP/library/azure/dn250579(v=azure.10).aspx
[Azure ポータル]: https://portal.azure.com/
[[Azure クラシック ポータル]]: https://manage.windowsazure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/ja-JP/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows デベロッパー センター]: https://dev.windows.com/ja-JP/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[Windows Live SDK]: https://msdn.microsoft.com/ja-JP/library/bb404787.aspx
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Notification Hubs API]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Mobile Apps ファイル サンプル]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63
[Azure Samples (Azure サンプル)]: https://github.com/Azure-Samples

<!-- External URLs -->
[JsonPropertyAttribute]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[OData バージョン 3 のドキュメント]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[SymbolSource]: http://www.symbolsource.org/
[SymbolSource の説明]: http://www.symbolsource.org/Public/Wiki/Using

<!---HONumber=AcomDC_0420_2016-->