1. mainpage.xaml.cs プロジェクト ファイルを開き、次のコード スニペットを MainPage クラスに追加します。
	
        private MobileServiceUser user;
        private async System.Threading.Tasks.Task Authenticate()
        {
            while (user == null)
            {
                string message;
                try
                {
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message =
                        string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }

                MessageBox.Show(message);
            }
        }

    これにより、現在のユーザーを格納するためのメンバー変数と認証プロセスを処理するためのメソッドが作成されます。ユーザーは、Facebook ログインを使用して認証されます。

    >[AZURE.NOTE]Facebook 以外の ID プロバイダーを使用している場合は、上の <strong>MobileServiceAuthenticationProvider</strong> の値をプロバイダーに対応する値に変更してください。</p>
    </div>

2. 既存の **OnNavigatedTo** メソッド オーバーライドを削除またはコメントアウトして、ページの **Loaded** イベントを処理する次のメソッドに置き換えます。 

        async void MainPage_Loaded(object sender, RoutedEventArgs e)
        {
            await Authenticate();
            RefreshTodoItems();
        }

   	このメソッドでは、新しい **Authenticate** メソッドが呼び出されます。 

3. MainPage コンストラクターを次のコードに置き換えます。

        // Constructor
        public MainPage()
        {
            InitializeComponent();
            this.Loaded += MainPage_Loaded;
        }

   	このコンストラクターでは、Loaded イベントのハンドラーも登録されます。
		
4. F5 キーを押してアプリケーションを実行し、選択した ID プロバイダーでアプリケーションにサインインします。 

   	ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。
<!--HONumber=42-->
