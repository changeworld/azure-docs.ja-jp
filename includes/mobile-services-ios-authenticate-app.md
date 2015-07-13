* **QSTodoListViewController.m** を開き、次のメソッドを追加します。Facebook を ID プロバイダーとして使用しない場合は、_facebook_ を _microsoftaccount_、_twitter_、_google_、_windowsazureactivedirectory_ のいずれかに変更します。

```
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
```

* `viewDidLoad` の `[self refresh]` を次のコードに置き換えます。

```
        [self loginAndGetData];
```

* **[実行]** をクリックしてアプリを起動したら、ログインします。ログインが成功すると、Todo リストを表示して更新できます。

<!---HONumber=July15_HO1-->