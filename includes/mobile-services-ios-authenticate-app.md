1. **QSTodoListViewController.m** を開き、**viewDidLoad** メソッドで次の行を削除します。

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

    > [AZURE.NOTE] Facebook 以外の ID プロバイダーを使用している場合は、**loginWithProvider** に渡される値を変更します。サポートされる値は次のとおりです。_microsoftaccount_、_facebook_、_twitter_、_google_、_windowsazureactivedirectory_

3. **[実行]** をクリックしてアプリを開始し、選択した ID プロバイダーでログインします。ログインが成功すると、Todo リストを表示して更新できます。

<!--HONumber=49-->