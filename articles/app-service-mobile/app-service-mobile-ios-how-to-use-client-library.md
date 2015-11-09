<properties
	pageTitle="Azure Mobile Apps 向け iOS SDK の使用方法"
	description="Azure Mobile Apps 向け iOS SDK の使用方法"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="09/28/2015"
	ms.author="krisragh"/>

# Azure Mobile Apps 向け iOS クライアント ライブラリの使用方法

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-client-library.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

このガイドでは、最新の [Azure Mobile Apps iOS SDK](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409) を使用して一般的なシナリオを実行する方法について説明します。Azure Mobile Apps を初めて使用する場合は、まず、「[iOS アプリの作成]」を参照して、バックエンドの作成、テーブルの作成、構築済みの iOS Xcode プロジェクトのダウンロードを行ってください。このガイドでは、クライアント側の iOS SDK に重点を置いています。バックエンドの .NET サーバー側 SDK の詳細については、「[Azure モバイル アプリ用 .NET バックエンド サーバー SDK の操作](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」をご覧ください。

##<a name="Setup"></a>セットアップと前提条件

このガイドでは、バックエンドとテーブルを作成済みであることを前提としています。このガイドでは、テーブルのスキーマが、これらのチュートリアルのテーブルの場合と同じであることを前提とします。また、コードで `WindowsAzureMobileServices.framework` を参照し、`WindowsAzureMobileServices/WindowsAzureMobileServices.h` をインポートしていることも前提となります。

##<a name="create-client"></a>方法: クライアントを作成する

プロジェクトで Azure Mobile Apps バックエンドにアクセスするには、`MSClient` を作成します。`AppUrl` をアプリの URL に置き換えます。`gatewayURLString` と `applicationKey` は空のままにしておいてかまいません。認証用のゲートウェイを設定する場合は、ゲートウェイの URL で `gatewayURLString` を設定します。

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl" gatewayURLString:@"" applicationKey:@""];
```

##<a name="table-reference"></a>方法: テーブル参照を作成する

データへのアクセスやデータの更新を行うには、バックエンド テーブルへの参照を作成します。`TodoItem` をテーブルの名前に置き換えます。

```
	MSTable *table = [client tableWithName:@"TodoItem"];
```

##<a name="querying"></a>方法: データをクエリする

データベース クエリを作成するには、`MSTable` オブジェクトのクエリを実行します。次のクエリは、`TodoItem` 内のすべての項目を取得し、各項目のテキストをログに記録します。

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) { // error is nil if no error occured
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) { // items is NSArray of records that match query
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

##<a name="filtering"></a>方法: 返されるデータをフィルター処理する

結果をフィルター処理するには、多くのオプションを使用できます。

述語を使用してフィルター処理するには、`NSPredicate` と `readWithPredicate` を使用します。次のコードは、返されたデータをフィルター処理して、不完全な Todo 項目のみを見つけます。

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table and update the items property with the results from the service
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

##<a name="query-object"></a>方法: MSQuery を使用する

複雑なクエリ (並べ替えとページングを含む) を実行するには、`MSQuery` オブジェクトを直接作成するか、述語を使用して作成します。

```
    MSQuery *query = [table query];
    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

`MSQuery` を使用すれば、次のような複数のクエリ動作を制御できます。`MSQuery` クエリを実行するには、以下の例に示すように `readWithCompletion` を呼び出します。* 結果の順序を指定する * 返すフィールドを制限する * 返すレコード数を制限する * 応答の合計数を指定する * 要求内にカスタム クエリ文字列パラメーターを指定する * 追加の関数を適用する


## <a name="sorting"></a>方法: MSQuery でデータを並べ替える

結果の並べ替えの例を見てみましょう。`text` フィールドの昇順で並べ替えてから、`completion` の降順で並べ替えるには、次のように `MSQuery` を呼び出します。

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```


## <a name="selecting"></a><a name="parameters"></a>方法: MSQuery でフィールドを制限し、クエリ文字列パラメーターを展開する

クエリで返されるフィールドを制限するには、**selectFields** プロパティにフィールド名を指定します。次の例では、text フィールドと completed フィールドのみが返されます。

```
	query.selectFields = @[@"text", @"completed"];
```

サーバー要求に追加のクエリ文字列パラメーターを含めるには (たとえば、カスタム サーバー側のスクリプトで使用されているため)、`query.parameters` 次のように設定します。

```
	query.parameters = @{
		@"myKey1" : @"value1",
		@"myKey2" : @"value2",
	};
```

##<a name="inserting"></a>方法: データを挿入する

新しいテーブル行を挿入するには、新しい `NSDictionary` を作成して、`table insert` を呼び出します。[動的スキーマ]が無効になっている場合、Mobile Services は `NSDictionary` に基づいて新しい列を自動的に生成します。

`id` が指定されていない場合、バックエンドは新しい一意の ID を自動的に生成します。電子メール アドレス、ユーザー名、または独自のカスタム値を ID として使用するには、独自の `id` を指定します。独自の ID を指定すると、結合およびビジネス指向のデータベース ロジックを容易にすることができます。

```
	NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
	[self.table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
						NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
		}
	}];
```

##<a name="modifying"></a>方法: データを変更する

既存の行を更新するには、次のように項目を変更して `update` を呼び出します。

```
	NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
	[newItem setValue:@"Updated text" forKey:@"text"];
	[self.table update:newItem completion:^(NSDictionary *item, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

あるいは、次のように行 ID と更新フィールドを指定します。

```
	[self.table update:@{@"id":@"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete":@YES} completion:^(NSDictionary *item, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

更新操作を行う場合は、少なくとも `id` 属性を設定する必要があります。

##<a name="deleting"></a>方法: データを削除する

項目を削除するには、次のように、該当する項目を指定して `delete` を呼び出します。

```
	[self.table delete:item completion:^(id itemId, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

あるいは、次のように行 ID を指定して削除します。

```
	[self.table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
		// Handle error or perform additional logic as needed
	}];   
```

削除操作を行う場合は、少なくとも `id` 属性を設定する必要があります。


##<a name="errors"></a>方法: エラーを処理する

モバイル サービスを呼び出したとき、完了ブロックには `NSError *error` パラメーターが含まれます。エラーが発生すると、このパラメーターは null 以外の値になります。コードでは、このパラメーターを確認し、必要に応じてエラーを処理する必要があります。

エラーに関連するより多くのデータを取得するには、ファイル [`<WindowsAzureMobileServices/MSError.h>`](https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h) で、`MSErrorResponseKey`、`MSErrorRequestKey`、および `MSErrorServerItemKey` の定数を定義します。さらに、ファイルで、エラー コードごとに定数を定義します。これらの定数の使用方法の例については、`MSErrorServerItemKey` と `MSErrorPreconditionFailed` の使用を取り上げた「[競合ハンドラー]」を参照してください。

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[iOS アプリの作成]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[動的スキーマ]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[競合ハンドラー]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!---HONumber=Nov15_HO1-->