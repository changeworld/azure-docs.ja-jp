<properties 
	pageTitle="iOS クライアント ライブラリの使用方法 - Azure Mobile Services" 
	description="Azure Mobile Services 向け iOS クライアント ライブラリを使用する方法について説明します。" 
	services="" 
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
	ms.date="10/10/2014" 
	ms.author="krisragh"/>




# モバイル サービス向け iOS クライアント ライブラリの使用方法
<div class="dev-center-tutorial-selector sublanding">
  <a href="/ja-jp/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/ja-jp/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/ja-jp/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS" class="current">iOS</a><a href="/ja-jp/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/ja-jp/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>

このガイドでは、Azure Mobile Services 向け iOS クライアントを使用して一般的なシナリオを実行する方法について説明します。サンプルは Objective-C で記述され、[モバイル サービス SDK] を必要とします。さらに、このチュートリアルには、[iOS SDK] も必要です。紹介するシナリオは、データの照会、挿入、更新、および削除、ユーザーの認証、エラー処理などです。Mobile Services を初めて使用する場合は、まず [Mobile Services のクイック スタート][Get started with Mobile Services]を完了することを検討してください。このクイック スタート チュートリアルでは、アカウントを構成し、初めてのモバイル サービスを作成します。

## 目次

- [Mobile Services とは][]
- [概念][]
- [セットアップと前提条件][]
- [方法:モバイル サービス クライアントを作成する][]
- [方法:テーブル参照を作成する][]
- [方法:モバイル サービスのデータを照会する][]
	- [返されるデータをフィルター処理する]
    - [MSQuery オブジェクトの使用][方法:MSQuery の使用]
	- [特定の列を選択する]
- [方法:モバイル サービスにデータを挿入する]
- [方法:モバイル サービスのデータを変更する]
- [方法:データをユーザー インターフェイスにバインドする]
- [方法:ユーザーを認証する]
- [方法:エラーを処理する]

<!--- [方法:単体テストを設計する]
- [方法:クライアントをカスタマイズする]
	- [要求ヘッダーをカスタマイズする]
	- [データ型のシリアル化をカスタマイズする]
- [次のステップ][]-->

[AZURE.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

##<a name="Setup"></a>セットアップと前提条件

このガイドでは、前提条件として、モバイル サービスとテーブルを作成してあるとします。詳細については、「[テーブルの作成]」を参照するか、「[Mobile Services の使用]」チュートリアルで作成した  `ToDoItem` テーブルを再利用してください。このトピックの例では、次の列を持つ `ToDoItem` という名前のテーブルを使用しています。

+ `id`
+ `text`
+ `complete`
+ `duration`


iOS アプリケーションを初めて作成する場合は、 `WindowsAzureMobileServices.framework` をアプリケーションの [**[Link Binary With Libraries]**](https://developer.apple.com/library/ios/recipes/xcode_help-project_editor/Articles/AddingaLibrarytoaTarget.html) 設定に追加してください。このステップで、[Add Other] をクリックし、ダウンロードした Microsoft Azure Mobile Services SDK の場所に移動して、その場所を選択します。

さらに、適切なファイルまたはアプリケーションの .pch ファイルに次の参照を追加する必要があります。

	#import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

<h2><a name="create-client"></a>方法:モバイル サービス クライアントを作成する</h2>

次のコードでは、モバイル サービスにアクセスするために使用されるモバイル サービス クライアント オブジェクトを作成します。

	MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"]

前のコードの  `MobileServiceUrl` と  `AppKey` を、モバイル サービスの URL とアプリケーション キーでそれぞれ置き換えます。モバイル サービスのためのこれらの設定を確認するには、Azure 管理ポータルでモバイル サービスを選択し、**[ダッシュボード]** をクリックします。

また、次に示すように、サービスの URL である **NSURL** オブジェクトからクライアントを作成することもできます。

	MSClient *client = [MSClient clientWithApplicationURL:[NSURL URLWithString:@"MobileServiceUrl"] applicationKey:@"AppKey"];

<h2><a name="table-reference"></a>方法:テーブル参照を作成する</h2>

モバイル サービスからデータにアクセスするには、項目を照会、更新、または削除するテーブルへの参照を事前に取得する必要があります。次の例では、 `ToDoItem` はテーブル名です。

	MSTable *table = [client tableWithName:@"ToDoItem"];


<h2><a name="querying"></a>方法:モバイル サービスのデータを照会する</h2>

MSTable オブジェクトを作成した後は、クエリを作成します。次の単純なクエリは、ToDoItem テーブル内のすべての項目を取得します。

	[table readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
		if(error) {
			NSLog(@"ERROR %@", error);
		} else {
			for(NSDictionary *item in items) {
				NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
			}
		}
	}];

ここでは、タスクのテキストをログに書き込んでいるだけです。

次のパラメーターは、コールバック内で指定されます。

+ _items_:クエリに一致したレコードの **NSArray**。
+ _totalCount_:クエリのすべてのページの項目の合計数 (現在のページで返された項目の数ではありません)。要求で合計数を明示的に要求しない限り、この値は -1 に設定されます。詳細については、「[ページにデータを返す]」を参照してください。
+ _error_:発生したエラー。エラーが発生していない場合は  `nil`.

### <a name="filtering"></a>方法:返されるデータをフィルター処理する

結果をフィルター処理するには、いくつかのオプションがあります。

最も一般的なのは、NSPredicate を使用して結果をフィルター処理する方法です。

	[table readWithPredicate:(NSPredicate *)predicate completion:(MSReadQueryBlock)completion];

次の述語は、ToDoItem テーブル内の未完了の項目のみを返します。

	NSPredicate *predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
	[table readWithPredicate:predicate completion:^(NSArray *items, NSInteger totalCount, NSError *error) {
		if(error) {
			NSLog(@"ERROR %@", error);
		} else {
			for(NSDictionary *item in items) {
				NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
			}
		}
	}];

1 つのレコードを取得するには、ID を使用できます。

	[table readWithId:[@"37BBF396-11F0-4B39-85C8-B319C729AF6D"] completion:^(NSDictionary *item, NSError *error) {
		if(error) {
			NSLog(@"ERROR %@", error);
		} else {
				NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
		}
	}];

この場合、コールバック パラメーターがわずかに異なることに注意してください。結果の配列と省略可能な数を取得する代わりに、1 個のレコードを取得します。

### <a name="query-object"></a>MSQuery オブジェクトの使用

**MSQuery** オブジェクトは、結果の並べ替え順序を変更したり、取得するデータ レコードの数を制限したりするなど、単に行をフィルター処理するだけにとどまらない複雑なクエリを必要とする場合に使用します。次の 2 つの例は、MSQuery オブジェクト インスタンスを作成する方法を示しています。

    MSQuery *query = [table query];

    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];

MSQuery オブジェクトを使用すると、クエリの動作を次のように制御できます。

* 結果が返される順序を指定する。
* 返されるフィールドを制限する。
* 返されるレコードの数を制限する。
* 応答に合計数を含めるかどうかを指定する。
* 要求にカスタムのクエリ文字列パラメーターを指定する。

加えて、1 つ以上の関数を適用してクエリを定義できます。定義したクエリは、**readWithCompletion** 関数を呼び出すことによって実行します。

#### <a name="sorting"></a>返されるデータを並べ替える

並べ替えに使用するフィールドを指定するには、次の関数を使用します。

	-(void) orderByAscending:(NSString *)field
	-(void) orderByDescending:(NSString *)field

このクエリは、期間 (duration) に基づいて結果を並べ替えた後、タスクが完了 (complete) したかどうかに基づいてさらに結果を並べ替えます。

	[query orderByAscending:@"duration"];
	[query orderByAscending:@"complete"];
	[query readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
		//code to parse results here
	}];

#### <a name="paging"></a>ページにデータを返す

モバイル サービスでは、1 つの応答で返されるレコードの量が制限されます。ユーザーに表示されるレコードの数を制御するには、ページング システムを実装する必要があります。ページングを行うには、**MSQuery** オブジェクトの次の 3 つのプロパティを使用します。

+	`BOOL includeTotalCount`
+	`NSInteger fetchLimit`
+	`NSInteger fetchOffset`


次の例では、単純な関数によって、サーバーから 20 のレコードを要求し、それを以前に読み込まれたレコードのローカル コレクションに追加しています。

	- (bool) loadResults() {
		MSQuery *query = [table query];

		query.includeTotalCount = YES;
		query.fetchLimit = 20;
		query.fetchOffset = self.loadedIte
	ms.count;

		[query readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
			if(!error) {
				// Add the items to our local copy
				[self.loadedItems addObjectsFromArray:items];

				// Set a flag to keep track if there are any additional records we need to load
				self.moreResults = (self.loadedIte
	ms.count < totalCount);
			}
		}];
	}

#### <a name="selecting"></a>返されるフィールドの制限

クエリによって返されるフィールドを制限するには、単に **selectFields** プロパティに目的のフィールドの名前を指定します。次の例では、text フィールドと completed フィールドのみが返されます。

	query.selectFields = @[@"text", @"completed"];

#### <a name="parameters"></a>追加のクエリ文字列パラメーターの指定

クライアント ライブラリでは、サーバーに対する要求に追加のクエリ文字列パラメーターを含めることができます。これらのパラメーターは、サーバー側のスクリプトで必要とされる場合があります。次の例では、2 つのクエリ文字列パラメーターを要求に追加しています。

	query.parameters = @{
		@"myKey1" : @"value1",
		@"myKey2" : @"value2",
	};

これらのパラメーターは、`myKey1=value1&myKey2=value2` のように、クエリ URI に追加されます。
詳細については、「[方法: カスタム パラメーターにアクセスする]」を参照してください。

<h2><a name="inserting"></a>方法:モバイル サービスにデータを挿入する</h2>

新しい行をテーブルに挿入するには、新しい [NSDictionary オブジェクト]を作成して insert 関数に渡します。次のコードでは、新しい Todo 項目をテーブルに挿入しています。

	NSDictionary *newItem = @{@"text": @"my new item", @"complete" : @NO};
	[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
	}];

Mobile Services では、テーブル ID として一意のカスタム文字列値をサポートしています。このため、アプリケーションは、Mobile Services テーブルの ID 列に電子メール アドレスやユーザー名などのカスタム値を使用できます。たとえば、各レコードを電子メール アドレスで識別する場合は、次の JSON オブジェクトを使用できます。

	NSDictionary *newItem = @{@"id": @"myemail@emaildomain.com", @"text": @"my new item", @"complete" : @NO};
	[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
	}];

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

 `id` の値は一意である必要があり、次のセット内の文字を含まないようにする必要があります。

+ 制御文字:[0x0000-0x001F] および [0x007F-0x009F]。詳細については、[ASCII 制御コード C0 および C1 に関するページ]を参照してください。
+  印刷可能文字:**"**(0x0022)、**\+** (0x002B)、**/** (0x002F)、**?**(0x003F)、**\\** (0x005C)、**`** (0x0060)
+  ID "." および ".."

また、テーブルに整数 ID を使用することもできます。整数 ID を使用するには、 `mobile table create` コマンドで --integerId オプションを使用してテーブルを作成する必要があります。このコマンドは、Azure のコマンド ライン インターフェイス (CLI) で使用されます。CLI の使い方の詳細については、「[モバイル サービス テーブルの管理用コマンド]」を参照してください。

動的スキーマが有効な場合、挿入または更新の要求に含まれるオブジェクトのフィールドに基づいて、モバイル サービスによって自動的に新しい列が生成されます。詳細については、「[動的スキーマ]」を参照してください。

<h2><a name="modifying"></a>方法:モバイル サービスのデータを変更する</h2>

既存のオブジェクトを更新するには、前のクエリから返された項目を変更し、**update** 関数を呼び出します。

	NSMutableDictionary *item = [self.results.item objectAtIndex:0];
	[item setObject:@YES forKey:@"complete"];
	[table update:item completion:^(NSDictionary *item, NSError *error) {
		//handle errors or any additional logic as needed
	}];

次の例に示すように、更新の場合に指定する必要があるのは、更新するフィールドと行 ID のみです。

	[table update:@{@"id" : @"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete": @YES} completion:^(NSDictionary *item, NSError *error) {
		//handle errors or any additional logic as needed
	}];


テーブルから項目を削除するには、次に示すように、項目を単に delete メソッドに渡します。

	[table delete:item completion:^(id itemId, NSError *error) {
		//handle errors or any additional logic as needed
	}];

また、次の例に示すように、ID を直接使用してレコードを削除することもできます。

	[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
		//handle errors or any additional logic as needed
	}];

更新操作や削除操作を行う場合は、少なくとも  `id` 属性を設定する必要があります。

<h2><a name="authentication"></a>方法:ユーザーを認証する</h2>

モバイル サービスでは、次の ID プロバイダーを使用して、ユーザーを認証できます。

- Facebook
- Google
- Microsoft アカウント
- Twitter
- Azure Active Directory

ID プロバイダーの構成の詳細については、「[認証の使用]」を参照してください。

モバイル サービスでは、次の 2 つの認証ワークフローがサポートされます。

- サーバーによって管理されるログインの場合、モバイル サービスがアプリケーションに代わってログオン プロセスを管理します。プロバイダー固有のログイン ページがクライアント ライブラリによって表示されます。モバイル サービスは、選択されたプロバイダーとの認証作業を行います。

- クライアントによって管理されるログインの場合、アプリケーションは ID プロバイダーにトークンを要求し、認証のためにこのトークンをモバイル サービスに示す必要があります。

認証が成功すると、割り当てられたユーザー ID 値と認証トークンを含むユーザー オブジェクトが返されます。このユーザー ID をサーバー スクリプトで使用して、要求を検証または変更することができます。詳細については、「[スクリプトを使用したユーザーの認証]」を参照してください。トークン自体は、後続のログインで使用できるように、安全にキャッシュできます。

さらに、テーブルのアクセス許可を設定することにより、特定の操作へのアクセスを認証されたユーザーのみに制限できます。詳細については、「[権限]」を参照してください。

### サーバーによって管理されるログイン

Microsoft アカウントを使用してログインする方法の例を次に示します。このコードは、コントローラーの ViewDidLoad 内で呼び出すことも、UIButton から手動でトリガーすることもできます。このコードは、ID プロバイダーにログインするための標準 UI を表示します。

	[client loginWithProvider:@"MicrosoftAccount" controller:self animated:YES
		completion:^(MSUser *user, NSError *error) {
			NSString *msg;
			if(error) {
				msg = [@"An error occured: " stringByAppendingString:error.description];
			} else {
				msg = [@"You are logged in as " stringByAppendingString:user.userId];
			}

			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login"
								  message:msg
								  delegate:nil
								  cancelButtonTitle:@"OK"
								  otherButtonTitles: nil];
			[alert show];
	}];

注: Microsoft アカウント用以外の ID プロバイダーを使用している場合は、上のログイン メソッドに渡される値を  `facebook`、 `twitter`、 `google`、 `windowsazureactivedirectory` のいずれかに変更します。

次に示すように、MSLoginController への参照を取得し、これを表示することもできます。

	-(MSLoginController *)loginViewControllerWithProvider:(NSString *)provider completion:(MSClientLoginBlock)completion;

### クライアントによって管理されるログイン (シングル サインオン)

ログイン プロセスがモバイル サービス クライアントの外部で実行される場合もあります。このような操作は、シングル サインオン サービス機能を有効にする場合や、アプリケーションでユーザー情報を取得するために直接 ID プロバイダーにアクセスする必要がある場合に使用します。これらのケースでは、サポートされて ID プロバイダーから個別に取得したトークンを提供することによって、モバイル サービスにログインできます。

次の例では、[Live Connect SDK] を使用して、iOS アプリケーションのシングル サインオンを有効にしています。

	[client loginWithProvider:@"microsoftaccount"
		token:@{@"authenticationToken" : self.liveClient.session.authenticationToken}
		completion:^(MSUser *user, NSError *error) {
			self.loggedInUser = user;
			NSString *msg = [@"You are logged in as " stringByAppendingString:user.userId];
			UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login"
				message:msg
				delegate:nil
				cancelButtonTitle:@"OK"
				otherButtonTitles: nil];
			[alert show];
	}];

このコードでは、コントローラーに  `liveClient` という名前の **LiveConnectClient** インスタンスが以前に作成されていて、ユーザーがログインしている状況を想定しています。

###<a name="caching-tokens"></a>方法:認証トークンをキャッシュする

ユーザーがアプリケーションを使用するたびに認証が求められるのを回避するには、ログイン後に現在のユーザー ID をキャッシュします。次にこの情報を使用して、ユーザーを直接作成してログイン プロセスをバイパスします。そのためには、ユーザー ID と認証トークンをローカルに保存する必要があります。次の例では、トークンが [KeyChain] に安全にキャッシュされます。

	- (NSMutableDictionary *) createKeyChainQueryWithClient:(MSClient *)client andIsSearch:(bool)isSearch
	{
		NSMutableDictionary *query = [[NSMutableDictionary alloc] init];
		[query setObject:(__bridge id)kSecClassInternetPassword forKey:(__bridge id)(kSecClass)];
		[query setObject:client.applicationURL.absoluteString forKey:(__bridge id)(kSecAttrServer)];

		if(isSearch) {
			// Use the proper search constants, return only the attributes of the first match.
			[query setObject:(__bridge id)kSecMatchLimitOne forKey:(__bridge id)kSecMatchLimit];
			[query setObject:(id)kCFBooleanTrue forKey:(__bridge id)kSecReturnAttributes];
			[query setObject:(id)kCFBooleanTrue forKey:(__bridge id)kSecReturnData];
		}

		return query;
	}

	- (IBAction)loginUser:(id)sender {
		NSMutableDictionary *query = [self createKeyChainQueryWithClient:self.todoService.client andIsSearch:YES];
		CFDictionaryRef cfresult;

		OSStatus status = SecItemCopyMatching((__bridge CFDictionaryRef)query, (CFTypeRef *)&cfresult);
		if (status == noErr) {
			NSDictionary * result = (__bridge_transfer NSDictionary*) cfresult;

			//create an MSUser object
			MSUser *user = [[MSUser alloc] initWithUserId:[result objectForKey:(__bridge id)(kSecAttrAccount)]];
			NSData *data = [result objectForKey:(__bridge id)(kSecValueData)];
			user.mobileServiceAuthenticationToken = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
			[self.todoService.client setCurrentUser:user];

		} else if (status == errSecItemNotFound) {
			//we need to log the user in
			[self.todoService.client loginWithProvider:@"MicrosoftAccount" controller:self animated:YES
				completion:^(MSUser *user, NSError *error) {
					NSString *msg = [@"You are logged in as " stringByAppendingString:user.userId];
					UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login"
											message:msg delegate:nil
											cancelButtonTitle:@"OK"
											otherButtonTitles: nil];
					[alert show];

					//save the user id and token to the KeyChain
					NSMutableDictionary *newItem = [self createKeyChainQueryWithClient:self.todoService.client
															andIsSearch:NO];
					[newItem setObject:user.userId forKey:(__bridge id)kSecAttrAccount];
					[newItem setObject:[user.mobileServiceAuthenticationToken dataUsingEncoding:NSUTF8StringEncoding]
                                                    forKey:(__bridge id)kSecValueData];

					OSStatus status = SecItemAdd((__bridge CFDictionaryRef)newItem, NULL);
					if(status != errSecSuccess) {
						//handle error as needed
						NSAssert(NO, @"Error caching password.");
					}
			}];
		}

> [AZURE.NOTE] トークンは慎重な扱いを要するデータであり、デバイスの紛失や盗難に備えて暗号化して保存する必要があります。

キャッシュされたトークンを使用しているとき、ユーザーはトークンの期限が切れるまで再度ログインする必要はありません。トークンの有効期限が切れているときにユーザーがログインしようとすると、"401 許可されていません" 応答が返されます。この段階で、ユーザーは、再度ログインして新しいトークンを取得する必要があります。この新しいトークンはキャッシュできます。フィルターを使用すると、アプリケーションがモバイル サービスを呼び出すすべての場所に有効期限切れのトークンを処理するコードを書かずに済みます。フィルターを使用して、モバイル サービスの呼び出しとモバイル サービスからの応答を取得することができます。フィルターのコードでは、401 の応答の有無をテストし、トークンが有効期限切れの場合はログイン プロセスをトリガーし、401を生成した要求を再試行します。詳細については、[期限切れのトークンの処理]に関するページを参照してください。

<h2><a name="errors"></a>方法:エラーを処理する</h2>

モバイル サービスを呼び出したとき、完了ブロックには  `NSError *error` パラメーターが含まれます。エラーが発生すると、このパラメーターに null 以外の値が返されます。コードでは、このパラメーターを確認し、必要に応じてエラーを処理する必要があります。

発生したエラーに関しては、コードに MSError.h ファイルを含めることによって詳細情報を取得することができます。

    #import <WindowsAzureMobileServices/MSError.h>

このファイルでは、次の定数を定義します。これらの定数を使用して、[error userInfo] からの追加データにアクセスできます。

+ **MSErrorResponseKey**: エラーに関連付けられた HTTP 応答データ
* **MSErrorRequestKey**: エラーに関連付けられた HTTP 要求データ

また、それぞれのエラー コードに対して定数が定義されます。これらのコードの説明は、MSError.h ファイルで確認できます。

検証と処理の例については、「[サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]」を参照してください。このトピックでは、サーバー スクリプトを使用してサーバー側の検証が実装されています。無効なデータが送信されるとエラー応答が返され、この応答がクライアントによって処理されます。

<!--
<h2><a name="#unit-testing"></a>方法:単体テストを設計する</h2>

_(省略可能) このセクションでは、クライアント ライブラリを使用しているときに単体テストを作成する方法について説明します (Yavor からの情報)。_

<h2><a name="#customizing"></a>方法:クライアントをカスタマイズする</h2>

_(省略可能) このセクションでは、クライアント動作のカスタマイズを送信する方法について説明します。_

###<a name="custom-headers"></a>方法:要求ヘッダーをカスタマイズする

_(省略可能) このセクションでは、カスタム要求ヘッダーを送信する方法について説明します。_

詳細については、サーバー側でのヘッダーの処理に関する新しいトピックを参照してください。

###<a name="custom-serialization"></a>方法:シリアル化をカスタマイズする

_(省略可能) このセクションでは、属性を使用してデータ型のシリアル化の方法をカスタマイズする方法について説明します。_

詳細については、サーバー側でのヘッダーの処理に関する新しいトピックを参照してください。

## <a name="next-steps"></a>次のステップ
-->

<!-- Anchors. -->

[Mobile Services とは]: #what-is
[概念]: #concepts
[セットアップと前提条件]: #Setup
[方法:モバイル サービス クライアントを作成する]: #create-client
[方法:テーブル参照を作成する]: #table-reference
[方法:モバイル サービスのデータを照会する]: #querying
[返されるデータをフィルター処理する]: #filtering
[返されるデータを並べ替える]: #sorting
[ページにデータを返す]: #paging
[特定の列を選択する]: #selecting
[方法:データをユーザー インターフェイスにバインドする]: #binding
[方法:モバイル サービスにデータを挿入する]: #inserting
[方法:モバイル サービスのデータを変更する]: #modifying
[方法:ユーザーを認証する]: #authentication
[認証トークンをキャッシュする]: #caching-tokens
[方法:イメージおよびサイズの大きいファイルをアップロードする]: #blobs
[方法:エラーを処理する]: #errors
[方法:単体テストを設計する]: #unit-testing
[方法:クライアントをカスタマイズする]: #customizing
[要求ヘッダーをカスタマイズする]: #custom-headers
[データ型のシリアル化をカスタマイズする]: #custom-serialization
[次のステップ]: #next-steps
[方法:MSQuery の使用]: #query-object

<!-- Images. -->

<!-- URLs. -->
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-ios
[サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-ios
[モバイル サービス SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[期限切れのトークンの処理]に関するページ: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[権限]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[スクリプトを使用したユーザーの認証]に関するページ: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-ios
[動的スキーマ]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[方法: カスタム パラメーターへのアクセス]に関するページ: /ja-jp/develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[テーブルの作成]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary オブジェクト]に関するページ: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII 制御コード C0 および C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services テーブルの管理用コマンド]: http://azure.microsoft.com/manage/linux/other-resources/command-line-tools/#Mobile_Tables


<!--HONumber=42-->
