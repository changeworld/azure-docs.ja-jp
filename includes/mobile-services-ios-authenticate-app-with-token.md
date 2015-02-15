
前の例では、標準のサインインを示しました。標準のサインインでは、アプリケーションが開始するたびに、クライアントは ID プロバイダーとモバイル サービスの両方にアクセスする必要があります。この方法は非効率であるだけでなく、多くの顧客が同時にアプリケーションを開始すると、使用率に関連した問題が発生する場合があります。よって、Mobile Services から返される承認トークンをキャッシュし、最初にその承認トークンの使用を試してから、プロバイダー ベースのサインインを使用するほうが効果的です。 


>[AZURE.NOTE] クライアントによって管理される認証とサービスによって管理される認証のどちらを使用する場合でも、Mobile Services が発行したトークンをキャッシュすることができます。このチュートリアルでは、サービスによって管理される認証を使用します。

1. iOS クライアントで認証トークンを暗号化し、格納する場合は、Keychain の使用をお勧めします。これを行うには、クラス KeychainWrapper を作成し、[KeychainWrapper.m](https://github.com/WindowsAzure-Samples/iOS-LensRocket/blob/master/source/client/LensRocket/Misc/KeychainWrapper.m) と [KeychainWrapper.h](https://github.com/WindowsAzure-Samples/iOS-LensRocket/blob/master/source/client/LensRocket/Misc/KeychainWrapper.h) を [LensRocket sample](https://github.com/WindowsAzure-Samples/iOS-LensRocket) からコピーします。この KeychainWrapper を使用する理由は、Apple のドキュメントで定義されている KeychainWrapper では Automatic Reference Counting (ARC) について詳細に説明されていないためです。


2. プロジェクト ファイル **QSTodoListViewController.m** を開き、次のコードを追加します。

		
		- (void) saveAuthInfo{
		    [KeychainWrapper createKeychainValue:self.todoService.client.currentUser.userId
				 forIdentifier:@"userid"];
		    [KeychainWrapper createKeychainValue:self.todoService.client.currentUser.mobileServiceAuthenticationToken
				 forIdentifier:@"token"];
		}
		
		
		- (void)loadAuthInfo {
		    NSString *userid = [KeychainWrapper keychainStringFromMatchingIdentifier:@"userid"];
		    if (userid) {
		        NSLog(@"userid: %@", userid);
		        self.todoService.client.currentUser = [[MSUser alloc] initWithUserId:userid];
		        self.todoService.client.currentUser.mobileServiceAuthenticationToken = [KeychainWrapper keychainStringFromMatchingIdentifier:@"token"];
		    }
		}
		


3. **QSTodoListViewController.m** 内の **viewDidAppear** メソッドの最後に、saveAuthInfo の呼び出しを追加します。この呼び出しを使用すれば、ユーザー ID とトークンのプロパティの格納が簡単になります。  



		- (void)viewDidAppear:(BOOL)animated
		{
		    MSClient *client = self.todoService.client;
		
		    if (client.currentUser != nil) {
		        return;
		    }
		
		    [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
		
		        [self saveAuthInfo];
		        [self refresh];
		    }];
		}

  
4. ユーザーのトークンと ID をキャッシュする方法を説明したので、次に、アプリの起動時にそれをロードする方法について説明します。**self.todoService** が初期化されたら、**QSTodoListViewController.m** 内の **viewDidLoad** メソッドに loadAuthInfo の呼び出しを追加します。 
		
		- (void)viewDidLoad
		{
		    [super viewDidLoad];
		    
		    // Create the todoService - this creates the Mobile Service client inside the wrapped service
		    self.todoService = [QSTodoService defaultService];

			[self loadAuthInfo];
		    
		    // Set the busy method
		    UIActivityIndicatorView *indicator = self.activityIndicator;
		    self.todoService.busyUpdate = ^(BOOL busy)
		    {
		        if (busy)
		        {
		            [indicator startAnimating];
		        } else
		        {
		            [indicator stopAnimating];
		        }
		    };
		    
		    // have refresh control reload all data from server
		    [self.refreshControl addTarget:self
		                            action:@selector(onRefresh:)
		                  forControlEvents:UIControlEventValueChanged];
		
		    // load the data
		    [self refresh];
		}

5. アプリが Mobile Service に要求を送った場合、ユーザーが認証されるので、401 応答 (認証エラー) を受信します。これは、渡したユーザー トークンが期限切れになっていることを意味します。提供しているメソッドで Mobile Service と対話するすべてのメソッドについては、それぞれの完了ハンドラーで、401 応答が発生しているかどうかを調べることが可能であり、または 1 つの場所 (MSFilter の handleRequest メソッド) で処理を行うことができます。このシナリオへの対処方法については、[このブログ記事](http://www.thejoyofcode.com/Handling_expired_tokens_in_your_application_Day_11_.aspx)を参照してください。


<!--HONumber=42-->
