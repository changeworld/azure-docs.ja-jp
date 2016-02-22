<properties
	pageTitle="App Service Mobile Apps の管理されたクライアント ライブラリの操作 (Windows | Xamarin) | Microsoft Azure"
	description="Windows と Xamarin アプリで Azure App Service Mobile Apps 向け .NET クライアントを使用する方法について説明します。"
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="glenga"/>

# Azure Mobile Apps 用の管理されたクライアントの使用方法

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

##概要

このガイドでは、Windows および Xamarin アプリで Azure App Service Mobile Apps 用の管理されたクライアント ライブラリを使用する一般的なシナリオの実行方法を示します。Mobile Apps を初めて使用する場合は、まず、「[Mobile Apps のクイックスタート](app-service-mobile-windows-store-dotnet-get-started.md)」チュートリアルを完了することを検討してください。このガイドでは、クライアント側の管理された SDK に重点を置いています。Mobile Apps 用のサーバー側 SDK の詳細については、「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」または「[Azure Mobile Apps Node.js SDK の使用方法](app-service-mobile-node-backend-how-to-use-server-sdk.md)」を参照してください。

## リファレンス ドキュメント

クライアント SDK のリファレンス ドキュメントについては、「[Azure Mobile Apps .NET クライアント リファレンス](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.aspx)」を参照してください。

##<a name="setup"></a>セットアップと前提条件

テーブルを 1 つ含むモバイル アプリ バックエンド プロジェクトを既に作成して発行してあるものとします。このトピックで使用するコードでは、テーブルの名前は `TodoItem` であり、`Id`、`Text`、および `Complete` という列があります。これは、[クイックスタート チュートリアル](app-service-mobile-windows-store-dotnet-get-started.md)で作成したものと同じテーブルです。

これに対応する型指定されたクライアント側 C# 型を次に示します。


	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

[JsonPropertyAttribute](http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm) を使用してクライアントの型とテーブルの間の *PropertyName* のマッピングが定義されていることに注意してください。

Mobile Apps バックエンドに新しいテーブルを作成する方法については、「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller)」または「[動的スキーマを使用するテーブルの定義](app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations)」(Node.js バックエンド) を参照してください。Node.js バックエンドでは、[Azure ポータル](https://portal.azure.com/)の **[テーブルの簡単操作]** 設定を使用することもできます。

##<a name="create-client"></a>方法: モバイル アプリ クライアントを作成する

次のコードは、モバイル アプリ バックエンドにアクセスするために使用される `MobileServiceClient` オブジェクトを作成します。

	MobileServiceClient client = new MobileServiceClient("MOBILE_APP_URL");

上記のコードで、`MOBILE_APP_URL` を、[Azure ポータル](https://portal.azure.com/)のモバイル アプリ バックエンド用のブレードで確認できるモバイル アプリ バックエンドの URL に置き換えます。

##<a name="instantiating"></a>方法: テーブル参照を作成する

バックエンド テーブルのデータにアクセスする、またはデータを変更するすべてのコードは、`MobileServiceTable` オブジェクトに対して関数を呼び出します。次のように、`MobileServiceClient` のインスタンスで [GetTable](https://msdn.microsoft.com/library/azure/jj554275.aspx) メソッドを呼び出して、テーブルへの参照を取得します。

    IMobileServiceTable<TodoItem> todoTable =
		client.GetTable<TodoItem>();

これは、型指定されたシリアル化モデルです。型指定されていないシリアル化モデルもサポートされます。次のコードは、型指定されていないテーブルへの参照を作成します。

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

型指定されていないクエリでは、基になる OData クエリ文字列を指定する必要があります。

##<a name="querying"></a>方法: Mobile App のデータを照会する

このセクションでは、モバイル アプリ バックエンドにクエリを発行する方法について説明します。これには次の機能が含まれます。

- [返されるデータをフィルター処理する]
- [返されるデータを並べ替える]
- [ページにデータを返す]
- [特定の列を選択する]
- [ID でデータを検索する]

>[AZURE.NOTE] すべての行が返されるのを防ぐために、サーバー側で設定されたページ サイズが適用されます。これにより、大きなデータ セットの既定の要求がサービスに悪影響を与えないようにします。50 以上の行を返すには、「[ページにデータを返す]」で説明されている `Take` メソッドを使用します。

### <a name="filtering"></a>方法: 返されるデータをフィルター処理する

次のコードは、クエリに `Where` 句を含めることによってデータをフィルター処理する方法を示しています。このコードは、`Complete` プロパティが `false` に等しい `todoTable` からすべての項目を返します。`Where` 関数は、行のフィルタリング述語をテーブルに対するクエリに適用します。

	// This query filters out completed TodoItems and
	// items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

ブラウザー開発者ツールや [Fiddler] などのメッセージ検査ソフトウェアを使用して、バックエンドに送信された要求の URI を表示できます。次の要求 URI を確認すると、クエリ文字列自体に変更を加えていることがわかります。

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

通常、この要求は、Azure 側で次のような SQL クエリに変換されます。

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

前の `where` ステートメントは、null 以外の `Text` を持ち、`Complete` のステータスが false に設定されている項目を検索します。

代わりに、次のように複数の行で記述することもできます。

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

この 2 つの方法は等価であり、区別しないで使用できます。複数の述語を 1 つのクエリに連結する前のオプションが、よりコンパクトでありお勧めです。

`where` 句は、OData サブセットに変換される操作をサポートします。これには、関係演算子 (==、! =、<、<=、>、>=)、算術演算子 (+、-、/、*、%)、数の精度 (Math.Floor、Math.Ceiling)、文字列関数 (Length、Substring、Replace、IndexOf、StartsWith、EndsWith)、日付プロパティ (Year、Month、Day、Hour、Minute、Second)、オブジェクトのアクセス プロパティ、これらすべてを組み合わせた式が含まれます。

### <a name="sorting"></a>方法: 返されるデータを並べ替える

次のコードは、クエリに `OrderBy` または `OrderByDescending` 関数を含めることによってデータを並べ替える方法を示しています。次のコードは、`todoTable` から、`Text` フィールドの値に基づいて昇順に並べ替えられた項目を返します。

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

### <a name="paging"></a>方法: ページにデータを返す

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

>[AZURE.NOTE]モバイル アプリ バックエンドの 50 行の制限をオーバーライドするには、[EnableQueryAttribute](https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx) をパブリック GET メソッドに適用してページング動作を指定することも必要です。次のコードをメソッドに適用すると、返される最大行数が 1000 行に設定されます。

    [EnableQuery(MaxTop=1000)]


### <a name="selecting"></a>方法: 特定の列を選択する

クエリに `Select` 句を追加し、結果に含める一連のプロパティを指定できます。たとえば、次のコードでは、1 つのフィールドだけを選択する方法と、複数のフィールドを選択し、フォーマットする方法も示しています。

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

`LookupAsync` 関数を使うと、データベースから特定の ID を持つオブジェクトを検索することができます。

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="lookingup"></a>方法: 型指定されていないクエリを実行する

型指定されていないテーブル オブジェクトを使用してクエリを実行するときは、次の例のように、OData クエリ文字列を明示的に指定する必要があります。

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

プロパティ バッグのように使用できる JSON 値が返されます。JToken と Json.NET の詳細については、[Json.NET](http://json.codeplex.com/) を参照してください。

##<a name="inserting"></a>方法: モバイル アプリ バックエンドにデータを挿入する

クライアントのすべての型には、**Id** という名前のメンバーが含まれる必要があります。その既定値は文字列です。この **Id** は、オフラインで CRUD 操作を実行するために必要です。次のコードは、テーブルに新しい行を挿入する方法を示しています。パラメーターには、挿入するデータが .NET オブジェクトとして含まれます。

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

+ データベースと往復することなく ID が生成されます。
+ 他のテーブルやデータベースのレコードをより簡単にマージできます。
+ ID 値をより適切にアプリケーションのロジックに統合できます。

挿入されたレコードで文字列 ID 値が設定されない場合は、モバイル アプリ バックエンドによって ID 用の一意の値が生成されます。`Guid.NewGuid()` メソッドを使用すれば、クライアント上またはバックエンドで、独自の ID 値を生成することができます。

##<a name="modifying"></a>方法: モバイル アプリ バックエンドのデータを変更する

次のコードは、同じ ID を持つ既存のインスタンスを新しい情報で更新する方法を示しています。パラメーターには、更新するデータが .NET オブジェクトとして含まれます。

	await todoTable.UpdateAsync(todoItem);

型指定されていないデータを挿入するには、次のような Json.NET を利用できます。 JObject jo = new JObject(); jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D"); jo.Add("Text", "Hello World"); jo.Add("Complete", false); var inserted = await table.UpdateAsync(jo);

更新を行うときは ID を指定する必要があることに注意してください。バックエンドはそれによって更新するインスタンスを識別します。ID は、`InsertAsync` の呼び出しの結果から取得できます。"Id" の値を指定しないで項目を更新しようとすると、`ArgumentException` が発生します。


##<a name="deleting"></a>方法: モバイル アプリ バックエンドのデータを削除する

次のコードは、既存のインスタンスを削除する方法を示しています。インスタンスは、`todoItem` に設定した "Id" フィールドで識別されます。

	await todoTable.DeleteAsync(todoItem);

型指定されていないデータを削除するには、次のような Json.NET を利用できます。

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

削除要求を行うときは、ID を指定する必要があります。それ以外のプロパティは、サービスに渡されないか、またはサービスで無視されます。通常、`DeleteAsync` の呼び出しの結果は `null` です。渡す ID は、`InsertAsync` の呼び出しの結果から取得できます。"Id" フィールドを設定しないで項目を削除しようとすると、`MobileServiceInvalidOperationException` がバックエンドから返されます。

##<a name="#custom-api"></a>方法: カスタム API の呼び出し

カスタム API を使用してカスタム エンドポイントを定義することにより、insert、update、delete、read のいずれの操作にも関連しないサーバー機能を公開することができます。カスタム API を使用することによって、HTTP メッセージ ヘッダーの読み取りや設定、JSON 以外のメッセージ本文形式の定義など、メッセージングをより柔軟に制御することができます。

クライアントで [InvokeApiAsync] メソッド オーバーロードの 1 つを呼び出して、カスタム API を呼び出します。たとえば、次のコード行は、バックエンドの **completeAll** API に POST 要求を送信します。

    var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll",
        System.Net.Http.HttpMethod.Post, null);

これは型指定メソッドの呼び出しのため、返される **MarkAllResult** の型を定義する必要があります。型指定および型指定のないメソッドの両方がサポートされます。型指定でペイロードを送信せず、クエリ パラメーターがなく、また要求ヘッダーを変更しないため、これは簡単な例になります。より現実的な例と [InvokeApiAsync] についてのより完全な説明については、「[Azure モバイル サービス クライアント SDK のカスタム API]」を参照してください。

##方法: プッシュ通知に登録する

Mobile Apps クライアントでは、Azure Notification Hubs によるプッシュ通知に登録できます。登録する場合、プラットフォーム固有のプッシュ通知サービス (PNS) からハンドルを取得します。この値は、登録を作成するときに、任意のタグと一緒に指定します。次のコードは、Windows Notification Service (WNS) によるプッシュ通知用の Windows アプリを登録します。

		private async void InitNotificationsAsync()
		{
		    // Request a push notification channel.
		    var channel =
		        await PushNotificationChannelManager
		            .CreatePushNotificationChannelForApplicationAsync();

		    // Register for notifications using the new channel and a tag collection.
			var tags = new List<string>{ "mytag1", "mytag2"};
		    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, tags);
		}

この例では、登録で 2 つのタグが含められます。テンプレート登録に登録する方法を含む Windows アプリの詳細については、「[アプリにプッシュ通知を追加する](app-service-mobile-windows-store-dotnet-get-started-push.md)」を参照してください。

Xamarin アプリではいくつかの追加コードが必要になります。このコードを使用して、iOS または Android アプリで実行されているアプリを Apple Push Notification サービス (APNS) と Google Cloud Messaging (GCM) にそれぞれ登録することができます。詳細については、「**アプリにプッシュ通知を追加する**」を参照してください ([Xamarin.iOS](partner-xamarin-mobile-services-ios-get-started-push.md#add-push) | [Xamarin.Android](partner-xamarin-mobile-services-android-get-started-push.md#add-push))。

## 方法: プッシュ テンプレートを登録してクロス プラットフォーム通知を送信する

テンプレートを登録するには、**MobileService.GetPush().RegisterAsync()** メソッドを使用してクライアント アプリにテンプレートを渡すだけです。

        MobileService.GetPush().RegisterAsync(channel.Uri, newTemplates());

テンプレートは JObject 型となり、次のような JSON 形式で複数のテンプレートを含めることが可能です。

        public JObject newTemplates()
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

これらの登録済みテンプレートを使用して通知を送信するには、[Notification Hubs API](https://msdn.microsoft.com/library/azure/dn495101.aspx) を使用します。

##<a name="optimisticconcurrency"></a>方法: オプティミスティック同時実行制御を使用する

シナリオによっては、複数のクライアントが同じ項目に対して同時に変更を書き込む場合があります。競合を検知しない場合、それを意図していなくても、最後に行われた書き込みによってそれ以前の更新がすべて上書きされます。*オプティミスティック同時実行制御*では、それぞれのトランザクションがコミットでき、そのためリソース ロックが一切使用されないことを前提としています。オプティミスティック同時実行制御ではトランザクションをコミットする前に、他のトランザクションがそのデータを変更していないことを確認します。データが変更されている場合、トランザクションのコミットはロール バックされます。

Mobile Apps はオプティミスティック同時実行制御をサポートしており、モバイル アプリ バックエンドで各テーブルに定義されている `__version` システム プロパティ列を使用して各項目の変更を追跡します。レコードが更新されるたびに、Mobile Apps はそのレコードの `__version` プロパティを新しい値に設定します。各更新要求の際に、要求に含まれているレコードの `__version` プロパティが、サーバー上のレコードの同じプロパティと比較されます。要求で渡されたバージョンがバックエンドと一致しない場合、クライアント ライブラリは `MobileServicePreconditionFailedException<T>` を生成します。例外に含まれている型は、レコードのサーバー側のバージョンを含んでいるバックエンドのレコードです。アプリケーションはこの情報を使用して、バックエンドからの正しい `__version` 値で更新要求をもう一度実行して変更をコミットするかどうかを判断できます。

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


次のコードは、書き込み競合が検出された場合にそれを解決する方法を示しています。解決をコミットするには、`UpdateAsync()` 呼び出しに正しい `__version` 値が含まれている必要があります。

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
	    MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: "{0}" \nLocal Text: "{1}"\n",
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

詳細については、「[Azure Mobile Apps でのオフライン データ同期](app-service-mobile-offline-data-sync.md)」を参照してください。


##<a name="binding"></a>方法: Mobile Apps のデータを Windows ユーザー インターフェイスにバインドする

このセクションでは、Windows アプリで UI 要素を使用して、返されたデータ オブジェクトを表示する方法について説明します。`todoTable` で未完了の項目を照会し、ごくシンプルな一覧で表示するには、次のコード例を実行して、クエリで一覧のソースをバインドします。`MobileServiceCollection` を使用すると、Mobile Apps 対応のバインディング コレクションが作成されます。

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

マネージ ランタイムの一部のコントロールでは、[ISupportIncrementalLoading](http://msdn.microsoft.com/library/windows/apps/Hh701916) というインターフェイスがサポートされます。このインターフェイスにより、コントロールはユーザーによるスクロールの際に追加のデータを要求することができます。このインターフェイスには、`MobileServiceIncrementalLoadingCollection` によるユニバーサル Windows 8.1 アプリ用の組み込みのサポートが用意されており、コントロールからの呼び出しが自動的に処理されます。Windows アプリで `MobileServiceIncrementalLoadingCollection` を使用するには、次を実行します。

			MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
		items =  todoTable.Where(todoItem => todoItem.Complete == false)
					.ToIncrementalLoadingCollection();

		ListBox lb = new ListBox();
		lb.ItemsSource = items;


Windows Phone 8 と "Silverlight" アプリで新しいコレクションを使用するには、`IMobileServiceTableQuery<T>` や `IMobileServiceTable<T>` で `ToCollection` 拡張メソッドを使用します。実際にデータを読み込むには、`LoadMoreItemsAsync()` を呼び出します。

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

`ToCollectionAsync` または `ToCollection` を呼び出して作成されたコレクションを使用する場合は、UI コントロールにバインドできるコレクションを取得します。このコレクションはページングに対応しています。つまり、コントロールが "項目をさらに読み込む" ようにコレクションに依頼し、コレクションがそれを実行します。その時点で、ユーザー コードは関係せず、コントロールはフローを開始します。ただし、コレクションはネットワークからデータを読み込むため、読み込みが失敗することもあると想定されます。このようなエラーを処理するには、`MobileServiceIncrementalLoadingCollection` で `OnException` メソッドをオーバーライドし、コントロールが実行する `LoadMoreItemsAsync` の呼び出しの結果として生じる例外を処理する必要があります。

最後に、テーブルには多くのフィールドが存在するものの、コントロールにはその一部のみを表示する必要があるとします。UI に表示する特定の列を選ぶ際には、前のセクション「[特定の列を選択する](#selecting)」のガイダンスを参考にしてください。

## <a name="adal"></a>方法: Active Directory 認証ライブラリを使用してユーザーを認証する

Active Directory 認証ライブラリ (ADAL) を使用して、Azure Active Directory を使用しているアプリケーションにユーザーをサインインさせることができます。これにより、よりネイティブな UX が実現し、さらにカスタマイズすることが可能になるため、多くの場合、`loginAsync()` メソッドを使用する方法よりも推奨されます。

1. 「[Azure Active Directory ログインを使用するように App Service アプリケーションを構成する方法](app-service-mobile-how-to-configure-active-directory-authentication.md)」のチュートリアルに従って、AAD のサインイン用にモバイル アプリ バックエンドを構成します。ネイティブ クライアント アプリケーションを登録する省略可能な手順を確実に実行します。

2. Visual Studio または Xamarin Studio で、プロジェクトを開き、`Microsoft.IdentityModel.CLients.ActiveDirectory` NuGet パッケージへの参照を追加します。検索時に、プレリリース版を含めます。

3. ご使用のプラットフォームに応じて、以下のコードをアプリケーションに追加します。それぞれで、次の置換を行います。

* **INSERT-AUTHORITY-HERE** を、アプリケーションをプロビジョニングしたテナントの名前に置き換えます。形式は https://login.windows.net/contoso.onmicrosoft.com である必要があります。この値は、[Azure クラシック ポータル] の Azure Active Directory の [ドメイン] タブからコピーできます。

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

## <a name="package-sid"></a>方法: Windows ストアのパッケージ SID を取得する

Windows アプリでは、プッシュ通知と特定の認証モードを有効にするにはパッケージ SID が必要です。この値を取得するには:

1. Visual Studio ソリューション エクスプローラーで、Windows Store アプリ プロジェクトを右クリックし、**[ストア]**、**[アプリケーションをストアと関連付ける]** の順にクリックします。
2. ウィザードで **[次へ]** をクリックし、Microsoft アカウントでサインインし、**[新しいアプリケーション名の予約]** にアプリの名前を入力し、**[予約]** をクリックします。
3. アプリの登録が正常に作成されたら、新しいアプリ名を選択し、**[次へ]** をクリックし、**[関連付け]** をクリックします。この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。
4. Microsoft アカウントを使用して [Windows デベロッパー センター](https://dev.windows.com/ja-JP/overview)にログインします。**[マイ アプリ]** で、先ほど作成したアプリ登録をクリックします。
5. **[アプリ管理]**、**[アプリ ID]** の順にクリックし、下にスクロールして **[パッケージ SID]** を探します。

多くの場合、パッケージ SID は URI として使用されます。その場合はスキームとして _ms-app://_ を使用する必要があります。この値をプレフィックスとして連結して形成されたパッケージ SID のバージョンをメモしておきます。

<!--- We want to just point to the authentication topic when it's done
##<a name="authentication"></a>How to: Authenticate users

Mobile Apps supports authenticating and authorizing app users using a variety of external identity providers: Facebook, Google, Microsoft Account, Twitter, and Azure Active Directory. You can set permissions on tables to restrict access for specific operations to only authenticated users. You can also use the identity of authenticated users to implement authorization rules in server scripts. For more information, see the tutorial [Add authentication to your app].

Two authentication flows are supported: a _server flow_ and a _client flow_. The server flow provides the simplest authentication experience, as it relies on the provider's web authentication interface. The client flow allows for deeper integration with device-specific capabilities as it relies on provider-specific device-specific SDKs.

###Server flow
To have App Service manage the authentication process in your Windows apps,
you must register your app with your identity provider. Then in your mobile App backed, you need to configure the application ID and secret provided by your provider. For more information, see the tutorial [Add authentication to your app].

Once you have registered your identity provider, simply call the [LoginAsync method] with the [MobileServiceAuthenticationProvider] value of your provider. For example, the following code initiates a server flow sign-in by using Facebook.

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

If you are using an identity provider other than Facebook, change the value of [MobileServiceAuthenticationProvider] above to the value for your provider.

In this case, App Service manages the OAuth 2.0 authentication flow by displaying the sign-in page of the selected provider and generating an App Service authentication token after successful sign-on with the identity provider. The [LoginAsync method] returns a [MobileServiceUser], which provides both the [userId] of the authenticated user and the [MobileServiceAuthenticationToken], as a JSON web token (JWT). This token can be cached and re-used until it expires. For more information, see [Caching the authentication token].

###Client flow

Your app can also independently contact the identity provider and then provide the returned token to App Service for authentication. This client flow enables you to provide a single sign-in experience for users or to retrieve additional user data from the identity provider.

####Single sign-in using a token from Facebook or Google

In the most simplified form, you can use the client flow as shown in this snippet for Facebook or Google.

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


####Single sign-in using Microsoft Account with the Live SDK

To be able to authenticate users, you must register your app at the Microsoft account Developer Center. You must then connect this registration with your Mobile App backend. Complete the steps in [Register your app to use a Microsoft account login](mobile-services-how-to-register-microsoft-authentication.md) to create a Microsoft account registration and connect it to your Mobile App backend. If you have both Windows Store and Windows Phone 8/Silverlight versions of your app, register the Windows Store version first.

The following code authenticates using Live SDK and uses the returned token to sign-in to your Mobile App backend.

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
			// The wl.basic scope is requested.
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


###<a name="caching"></a>Caching the authentication token
In some cases, the call to the login method can be avoided after the first time the user authenticates. You can use [PasswordVault] for Windows Store apps to cache the current user identity the first time they log in and every subsequent time you check whether you already have the user identity in our cache. When the cache is empty, you still need to send the user through the login process.

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


For Windows Phone apps, you may encrypt and cache data using the [ProtectedData] class and store sensitive information in isolated storage.

-->

##<a name="errors"></a>方法: エラーを処理する

次の例では、バックエンドによって返される例外を処理する方法を示します。

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


##<a name="unit-testing"></a>方法: 単体テストを設計する

`MobileServiceClient.GetTable` とクエリによって返される値はインターフェイスです。そのためテスト目的で簡単にモックを作成することができるので、テスト ロジックを実装する `MyMockTable : IMobileServiceTable<TodoItem>` を作成できます。

##<a name="customizing"></a>方法: クライアントをカスタマイズする

このセクションでは、要求ヘッダーをカスタマイズし、応答内の JSON オブジェクトのシリアル化をカスタマイズする方法について説明します。

### <a name="headers"></a>方法: 要求ヘッダーをカスタマイズする

特定のアプリケーション シナリオをサポートするには、モバイル アプリ バックエンドとの通信をカスタマイズすることが必要な場合があります。たとえば、すべての送信要求にカスタム ヘッダーを追加したり、応答のステータス コードを変更したりすることが必要な場合がります。これを行うには、次の例のように、カスタムの [DelegatingHandler] を指定します。

    public async Task CallClientWithHandler()
    {
        MobileServiceClient client = new MobileServiceClient(
            "AppUrl", "", "",
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
            // Add a custom header to the request.
            request.Headers.Add("x-my-header", "my value");
            var response = await base.SendAsync(request, cancellationToken);
            // Set a differnt response status code.
            response.StatusCode = HttpStatusCode.ServiceUnavailable;
            return response;
        }
    }

このコードは、要求内に新しい **x-my-header** ヘッダーを追加し、利用不可に対する応答コードを任意に設定します。実際のシナリオでは、アプリケーションで必要とされるカスタム ロジックに基づいて応答ステータス コードを設定します。

### <a name="serialization"></a>方法: シリアル化をカスタマイズする

Mobile Apps クライアント ライブラリは、Json.NET を使用して、クライアントで JSON 応答を .NET オブジェクトに変換します。メッセージでの .NET 型と JSON との間のこのシリアル化の動作を構成することができます。[MobileServiceClient] クラスは [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm) 型の `SerializerSettings` プロパティを公開します。

このプロパティを使用すれば、次のように、多くの Json.NET プロパティの中からいずれかを設定できます。

	var settings = new JsonSerializerSettings();
	settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
	client.SerializerSettings = settings;

シリアル化中、このプロパティによってすべてのプロパティが小文字に変換されます。

<!-- Anchors. -->
[返されるデータをフィルター処理する]: #filtering
[返されるデータを並べ替える]: #sorting
[ページにデータを返す]: #paging
[特定の列を選択する]: #selecting
[ID でデータを検索する]: #lookingup

<!-- Images. -->



<!-- URLs. -->
[Add authentication to your app]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[LoginAsync method]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md/#Commands_to_manage_mobile_services
[Optimistic Concurrency Tutorial]: mobile-services-windows-store-dotnet-handle-database-conflicts.md
[MobileServiceClient]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
[IncludeTotalCount]: http://msdn.microsoft.com/library/windowsazure/dn250560.aspx
[Skip]: http://msdn.microsoft.com/library/windowsazure/dn250573.aspx
[Take]: http://msdn.microsoft.com/library/windowsazure/dn250574.aspx
[Fiddler]: http://www.telerik.com/fiddler
[Azure モバイル サービス クライアント SDK のカスタム API]: http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx
[InvokeApiAsync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx

<!---HONumber=AcomDC_0211_2016-->