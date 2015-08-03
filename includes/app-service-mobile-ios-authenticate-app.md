

1. **QSTodoListViewController.m** を開き、次のメソッドを追加します。


        - (void) loginAndGetData
        {
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                [self refresh];
            }];
        }


    > [AZURE.NOTE]Facebook 以外の ID プロバイダーを使用している場合は、**loginWithProvider** に渡される値を変更します。サポートされている値は、_microsoftaccount_、_facebook_、_twitter_、_google_、_windowsazureactivedirectory_ です。


2. 最後の `[self refresh]` を次の内容と交換して、`viewDidLoad` を変更します。

        [self loginAndGetData];

3. **[Run]** をクリックしてアプリを開始し、選択した ID プロバイダーでログインします。ログインが成功すると、Todo リストを表示して更新できます。

<!---HONumber=July15_HO4-->