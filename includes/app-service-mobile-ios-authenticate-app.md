**Objective-C**:

1. Mac の Xcode で *QSTodoListViewController.m* を開き、次のメソッドを追加します。Google を ID プロバイダーとして使用しない場合は、*google* を *microsoftaccount*、*twitter*、*facebook*、*windowsazureactivedirectory* のいずれかに変更します。Facebook を使用する場合、[アプリで Facebook ドメインをホワイトリストに追加する必要があります](https://developers.facebook.com/docs/ios/ios9#whitelist)。
   
            - (void) loginAndGetData
            {
                MSClient *client = self.todoService.client;
                if (client.currentUser != nil) {
                    return;
                }
   
                [client loginWithProvider:@"google" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
                    [self refresh];
                }];
            }
2. *QSTodoListViewController.m* の `viewDidLoad` で `[self refresh]` を次のように置き換えます。
   
            [self loginAndGetData];
3. *[実行]* をクリックしてアプリを起動したら、ログインします。ログインが成功すると、Todo リストを表示して更新できます。

**Swift**:

1. Mac の Xcode で *ToDoTableViewController.swift* を開き、次のメソッドを追加します。Google を ID プロバイダーとして使用しない場合は、*google* を *microsoftaccount*、*twitter*、*facebook*、*windowsazureactivedirectory* のいずれかに変更します。Facebook を使用する場合、[アプリで Facebook ドメインをホワイトリストに追加する必要があります](https://developers.facebook.com/docs/ios/ios9#whitelist)。
   
            func loginAndGetData() {
   
                guard let client = self.table?.client where client.currentUser == nil else {
                    return
                }
   
                client.loginWithProvider("google", controller: self, animated: true) { (user, error) in
                    self.refreshControl?.beginRefreshing()
                    self.onRefresh(self.refreshControl)
                }
            }
2. *ToDoTableViewController.swift* の `viewDidLoad()` の最後にある `self.refreshControl?.beginRefreshing()` と `self.onRefresh(self.refreshControl)` の行を削除します。その場所に `loginAndGetData()` の呼び出しを追加します。
   
            loginAndGetData()
3. *[実行]* をクリックしてアプリを起動したら、ログインします。ログインが成功すると、Todo リストを表示して更新できます。

<!---HONumber=AcomDC_0218_2016-->