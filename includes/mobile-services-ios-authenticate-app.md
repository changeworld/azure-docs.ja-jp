

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

    <div class="dev-callout"><b>注</b>
	<p>Facebook 以外の ID プロバイダーを使用している場合は、上の <strong>loginWithProvider</strong> メソッドに渡される値を<em>microsoftaccount</em>、<em>facebook</em>、<em>twitter</em>、<em>google</em>、<em>windowsazureactivedirectory</em> のいずれかに変更します。</p>
    </div>
		
3. **[実行]** を押してプロジェクトをビルドし、iPhone エミュレーターでアプリケーションを起動して、選択した ID プロバイダーでログオンします。 

   	ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。
