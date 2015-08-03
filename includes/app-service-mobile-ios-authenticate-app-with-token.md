
前の例では、標準のサインインを示しました。標準のサインインでは、アプリが開始するたびに、クライアントは ID プロバイダーと App Service の両方にアクセスする必要があります。この方法は効率的ではないため、App Service に返された認証トークンをキャッシュしておき、プロバイダーに基づくサインインを使用する前にそれを使用することをお勧めします。

1. iOS クライアントで認証トークンを暗号化し、格納する場合は、iOS Keychain の使用をお勧めします。このチュートリアルでは、[SSKeychain](https://github.com/soffes/sskeychain) （iOS Keychain の単純なラッパー） を使用します。SSKeychain ページの指示に従って、それをプロジェクトに追加します。プロジェクトの **[Build Settings]** で、**[Enable Modules]** 設定が有効になっていることを確認します (**[Apple LLVM - Languages - Modules]** セクション)。

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

3. `loginAndGetData` メソッド内で `loginWithProvider:controller:animated:completion:` 呼び出しの完了ブロックを変更します。それには、`saveAuthInfo` の呼び出しを `[self refresh]` 行の直前に追加します。この呼び出しでは、単にユーザー ID とトークンのプロパティを保存します。

				[self saveAuthInfo];

4. アプリの起動時にユーザー ID とトークンも読み込むようにします。`self.todoService` が初期化されたら、**QSTodoListViewController.m** 内の `viewDidLoad` メソッドに loadAuthInfo の呼び出しを追加します。

				[self loadAuthInfo];

<!---HONumber=July15_HO4-->