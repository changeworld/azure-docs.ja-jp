
前の例では、標準のサインインを示しました。標準のサインインでは、アプリが開始するたびに、クライアントは ID プロバイダーとモバイル サービスの両方にアクセスする必要があります。このメソッドは、拡張性がなく、効率的ではありません。

Azure Mobile Services から返される承認トークンをキャッシュし、最初にその承認トークンの使用を試してから、プロバイダー ベースのサインインを使用するほうが効果的です。

1. iOS クライアントで認証トークンを暗号化し、格納する場合は、iOS Keychain の使用をお勧めします。このチュートリアルでは、[SSKeychain](https://github.com/soffes/sskeychain) （iOS Keychain の単純なラッパー） を使用します。SSKeychain ページの指示に従って、それをプロジェクトに追加します。プロジェクトの [**ビルド設定**] で、[**モジュールを有効にする**] 設定が有効になっていることを確認します ([**Apple LLVM - 言語 - モジュール**] セクション)。

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

3. `loginAndGetData` メソッド内で `loginWithProvider:controller:animated:completion:` 呼び出しの完了ブロック変更します。それには、`saveAuthInfo` の呼び出しを `[self refresh]` 行の直前に追加します。この呼び出しでは、単にユーザー ID とトークンのプロパティを保存します。

				[self saveAuthInfo];

4. アプリの起動時にユーザー ID とトークンも読み込むようにします。`self.todoService` が初期化されたら、**QSTodoListViewController.m** 内の `viewDidLoad` メソッドに loadAuthInfo の呼び出しを追加します。

				[self loadAuthInfo];
<!--HONumber=54-->