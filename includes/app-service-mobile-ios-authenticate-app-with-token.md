
前の例では、標準のサインインを示しました。標準のサインインでは、アプリが開始するたびに、クライアントは ID プロバイダーと App Service の両方にアクセスする必要があります。この方法は効率的ではないため、App Service に返された認証トークンをキャッシュしておき、プロバイダーに基づくサインインを使用する前にそれを使用することをお勧めします。

1. iOS クライアントで認証トークンを暗号化して保存する場合は、iOS Keychain の使用をお勧めします。このチュートリアルでは、[SSKeychain](https://github.com/soffes/sskeychain) （iOS Keychain の単純なラッパー） を使用します。SSKeychain ページの指示に従って、それをプロジェクトに追加します。プロジェクトの **[ビルド設定]** で、**[モジュールを有効にする]** 設定が有効になっていることを確認します (**[Apple LLVM - 言語 - モジュール]** セクション.)。

2. **QSTodoListViewController.m** を開き、次のコードを追加します。


		- (void) saveAuthInfo {
				[SSKeychain setPassword:self.todoService.client.currentUser.mobileServiceAuthenticationToken forService:@"AzureMobileServiceTutorial" account:self.todoService.client.currentUser.userId]
		}


		- (void)loadAuthInfo {
				NSString *userid = [[SSKeychain accountsForService:@"AzureMobileServiceTutorial"][0] valueForKey:@"acct"];
		    if (userid) {
		        NSLog(@"userid: %@", userid);
		        self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
		         self.todoService.client.currentUser.mobileServiceAuthenticationToken = [SSKeychain passwordForService:@"AzureMobileServiceTutorial" account:userid];

		    }
		}

3. **QSTodoListViewController.m** の **viewDidAppear** メソッドの最後の `[self refresh]` 行の直前に **saveAuthInfo** への呼び出しを追加します。この呼び出しでは、単にユーザー ID とトークンのプロパティを保存します。

				[self saveAuthInfo];

4. ユーザー ID とトークンを、アプリの起動時にも読み込むようにします。**QSTodoListViewController.m** 内の **viewDidLoad** メソッドで、loadAuthInfo への呼び出しを **self.todoService** の初期化直後に追加します。

				[self loadAuthInfo];

<!--HONumber=49-->