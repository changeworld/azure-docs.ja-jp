

1. プロジェクト ファイル QSTodoListViewController.m を開き、**viewDidLoad** メソッドで、データをテーブルに再び読み込む次のコードを削除します。

        [self refresh];

2.	**viewDidLoad** メソッドの直後に次のコードを追加します。  

        - (void)viewDidAppear:(BOOL)animated
        {
            MSClient *client = self.todoService.client;
            
            if (client.currentUser != nil) {
                return;
            }
            
            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];
        }

    > [AZURE.NOTE] Facebook 以外の ID プロバイダーを使用している場合は、上の **loginWithProvider** に渡される値を_microsoftaccount_、_facebook_、_twitter_、_google_、_windowsazureactivedirectory_ のいずれかに変更します。
		
3. **[実行]** ボタンを押してプロジェクトをビルドし、iPhone エミュレーターでアプリケーションを起動して、選択した ID プロバイダーでログオンします。

   	ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。
<!--HONumber=42-->
