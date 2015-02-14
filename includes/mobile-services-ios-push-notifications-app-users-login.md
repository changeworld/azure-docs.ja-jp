
次に、登録が試行される前にユーザーが認証されていることを確認するために、プッシュ通知が登録される方法を変更する必要があります。 

1. **QSAppDelegate.m** で、**didFinishLaunchingWithOptions** の実装をまとめて削除します。

		
		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
		(NSDictionary *)launchOptions
		{
		    // Register for remote notifications
		    [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
		    UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
		    return YES;
		}

2. プロジェクト ファイル **QSTodoListViewController.m** を開き、**viewDidLoad** メソッド内で、前に削除したコードを付けたします。

	
		- (void)viewDidAppear:(BOOL)animated
		{
		    MSClient *client = self.todoService.client;
		
		    if (client.currentUser != nil) {
		        return;
		    }
		
		    [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
		        [self refresh];
		    }];
		
		    // Register for remote notifications
		    [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
		    UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
		}
<!--HONumber=42-->
