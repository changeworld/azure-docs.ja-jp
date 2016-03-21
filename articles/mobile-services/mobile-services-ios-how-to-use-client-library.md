<properties
	pageTitle="Azure Mobile Services 向け iOS クライアント ライブラリの使用方法"
	description="Mobile Services 向け iOS クライアント ライブラリの使用方法"
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="01/12/2016"
	ms.author="krisragh"/>

# Azure Mobile Services 向け iOS クライアント ライブラリの使用方法

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

このガイドでは、Azure Mobile Services の [iOS SDK] を使用して一般的なシナリオを実行する方法について説明します。Mobile Services を初めて使用する場合は、まず、「[Mobile Services のクイック スタート]」を実行してアカウントを構成し、テーブルを作成し、モバイル サービスを作成してください。

> [AZURE.NOTE]このガイドは、最新では [iOS Mobile Services SDK](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409) を使用します。プロジェクトで古いバージョンの SDK が使用されている場合は、まず Xcode のフレームワークをアップグレードします。

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="Setup"></a>セットアップと前提条件

このガイドでは、前提条件として、モバイル サービスとテーブルを作成してあるとします。詳細については、「[テーブルの作成]」を参照するか、「[Mobile Services のクイック スタート]」で作成した `TodoItem` テーブルを再利用してください。このガイドでは、テーブルのスキーマが、これらのチュートリアルのテーブルの場合と同じであることを前提とします。このガイドではまた、Xcode が `WindowsAzureMobileServices.framework` を参照し、`WindowsAzureMobileServices/WindowsAzureMobileServices.h` をインポートすることを前提とします。

##<a name="create-client"></a>方法: Mobile Services クライアントを作成する

プロジェクトで Azure Mobile Service にアクセスするには、`MSClient` クライアント オブジェクトを作成します。`AppUrl` をモバイル サービスの URL に置き換え、`AppKey` をアプリケーション キー ダッシュ ボード値に置き換えます。

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl" applicationKey:@"AppKey"];
```

##<a name="table-reference"></a>方法: テーブル参照を作成する

Azure Mobile Service のデータへのアクセスまたはデータの更新を行うには、テーブルへの参照を作成します。`TodoItem` をテーブルの名前に置き換えます。

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

## <a name="paging"></a>方法: MSQuery でページにデータを返す

モバイル サービスでは、1 つの応答で返されるレコードの量が制限されます。ユーザーに表示されるレコードの数を制御するには、ページング システムを実装する必要があります。ページングを行うには、**MSQuery** オブジェクトの次の 3 つのプロパティを使用します。

```
+	`BOOL includeTotalCount`
+	`NSInteger fetchLimit`
+	`NSInteger fetchOffset`
```

次の例では、単純な関数によって、サーバーから 5 のレコードを要求し、それを以前に読み込まれたレコードのローカル コレクションに追加しています。

```
// Create and initialize these properties
@property (nonatomic, strong)   NSMutableArray *loadedItems; // Init via [[NSMutableArray alloc] init]
@property (nonatomic)   				BOOL moreResults;
```

```
-(void)loadResults
{
    MSQuery *query = [self.table query];

    query.includeTotalCount = YES;
    query.fetchLimit = 5;
    query.fetchOffset = self.loadedItems.count;


    [query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(!error) {
            // Add the items to our local copy
            [self.loadedItems addObjectsFromArray:result.items];

            // Set a flag to keep track if there are any additional records we need to load
            self.moreResults = (self.loadedItems.count <= result.totalCount);
        }
    }];
}

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

##<a name="#custom-api"></a>方法: カスタム API の呼び出し

カスタム API を使用してカスタム エンドポイントを定義することにより、insert、update、delete、read のいずれの操作にも関連しないサーバー機能を公開することができます。カスタム API を使用することによって、HTTP メッセージ ヘッダーの読み取りや設定、JSON 以外のメッセージ本文形式の定義など、メッセージングをより柔軟に制御することができます。モバイル サービスでカスタム API を作成する方法の例については、「[カスタム API を定義する方法](mobile-services-dotnet-backend-define-custom-api.md)」を参照してください。

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](../../includes/mobile-services-ios-call-custom-api.md)]


##<a name="authentication"></a>方法: ユーザーを認証する

Azure Mobile Services は、さまざまな ID プロバイダーをサポートします。基本的なチュートリアルについては、「[認証]」を参照してください。

Azure Mobile Services は、次の 2 つの認証ワークフローをサポートします。

- **サーバーによって管理されるログイン**: Azure Mobile Services がアプリケーションに代わってログオン プロセスを管理します。Azure Mobile Services は、プロバイダー固有のログイン ページを表示し、選択したプロバイダーで認証されます。

- **クライアントによって管理されるログイン**: アプリケーションは ID プロバイダーにトークンを要求し、認証のためにこのトークンを Azure Mobile Services に示します。

認証が成功すると、ユーザー ID の値と認証トークンを含むユーザー オブジェクトが返されます。このユーザー ID を使用してユーザーを承認するには、「[サービス側の承認]」を参照してください。認証されたユーザーのみにテーブルへのアクセスを制限するには、「[権限]」を参照してください。

### サーバーによって管理されるログイン

ここでは、[Mobile Services クイック スタート] プロジェクトに、サーバーによって管理されるログインを追加する方法を示します。他のプロジェクトに同様のコードを使用することもできます。詳細およびエンド ツー エンド例の動作を確認するには、「[認証]」を参照してください。

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

### クライアントによって管理されるログイン (シングル サインオン)

ログイン プロセスが Mobile Services クライアントの外部で実行される場合もあります。このような操作は、シングル サインオン サービス機能を有効にする場合や、アプリケーションで ID プロバイダーにアクセスする場合に使用します。このような場合は、サポートされている ID プロバイダーから個別に取得したトークンを指定することによって、Mobile Services にログインできます。

次の例では、[Live Connect SDK] を使用して、iOS アプリケーションのシングル サインオンを有効にしています。このコードでは、コントローラーに `liveClient` という名前の **LiveConnectClient** インスタンスが作成されていて、ユーザーがログインしている状況を想定しています。

```
	[client loginWithProvider:@"microsoftaccount"
		token:@{@"authenticationToken" : self.liveClient.session.authenticationToken}
		completion:^(MSUser *user, NSError *error) {
				// Handle success and errors
	}];
```

##<a name="caching-tokens"></a>方法: 認証トークンをキャッシュする

[Mobile Services のクイック スタート] プロジェクトで、トークンをキャッシュする方法を見てみましょう。任意のプロジェクトに同様の手順を適用することもできます。[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

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
[Mobile Services のクイック スタート]: mobile-services-ios-get-started.md
[Mobile Services クイック スタート]: mobile-services-ios-get-started.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[認証]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[権限]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[サービス側の承認]: mobile-services-javascript-backend-service-side-authorization.md
[動的スキーマ]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[テーブルの作成]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[競合ハンドラー]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!---HONumber=AcomDC_0121_2016-->