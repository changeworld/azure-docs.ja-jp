
1. 共有プロジェクト ファイル MainPage.cs を開き、次の using ステートメントを追加します。

        using Windows.UI.Popups;

2. MainPage クラスに、次のコード スニペットを追加します。
	
		// Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;

		// Define a method that performs the authentication process
		// using a Facebook sign-in. 
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            while (user == null)
            {
                string message;
                try
                {
					// Change 'MobileService' to the name of your MobileServiceClient instance.
					// Sign-in using Facebook authentication.
                    user = await App.MobileService
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    message = 
                        string.Format("You are now signed in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                    message = "You must log in. Login Required";
                }
                        
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    このユーザーは、Facebook ログインを使用して認証されます。Facebook 以外の ID プロバイダーを使用している場合は、上の **MobileServiceAuthenticationProvider** の値をプロバイダーに対応する値に変更してください。

3. 既存の **OnNavigatedTo** メソッドのオーバーライドで、**RefreshTodoItems** メソッドの呼び出しをコメントアウトするか、削除します。

	これを行うと、ユーザーが認証されるまでデータが読み込まれなくなります。

	>[AZURE.NOTE]Windows Phone ストア 8.1 アプリから正常に認証するためには、**OnNavigated** メソッドが呼び出された後、ページの **Loaded** イベントが発生した後に LoginAsync を呼び出す必要があります。このチュートリアルでは、アプリに **[サイン イン]** ボタンを追加してこれを行います。

4. MainPage クラスに、次のコード スニペットを追加します。

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            RefreshTodoItems();
        }
		
5. Windows ストア アプリ プロジェクトで、MainPage.xaml プロジェクト ファイルを開き、**[保存]** ボタンを定義する要素の直前に次の **Button** 要素を追加します。

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

6. Windows Phone ストア アプリの前の手順を繰り返しますが、今回は、**TextBlock** 要素の後の **TitlePanel** に**ボタン**を追加します。

5. 共有された App.xaml.cs プロジェクト ファイルを開き、次の using ステートメントが存在しない場合はこれを追加します。

        using Microsoft.WindowsAzure.MobileServices;  
 
6. App.xaml.cs プロジェクト ファイルに次のコードを追加します。

        protected override void OnActivated(IActivatedEventArgs args)
        {
			// Windows Phone 8.1 requires you to handle the respose from the WebAuthenticationBroker.
            #if WINDOWS_PHONE_APP
            if (args.Kind == ActivationKind.WebAuthenticationBrokerContinuation)
            {
				// Completes the sign-in process started by LoginAsync.
				// Change 'MobileService' to the name of your MobileServiceClient instance. 
                App.MobileService.LoginComplete(args as WebAuthenticationBrokerContinuationEventArgs);
            }
            #endif

            base.OnActivated(args);
        }

	**OnActivated** メソッドが既に存在する場合は、`#if...#endif` コード ブロックを追加します。

8. F5 キーを押して Windows ストア アプリを実行します。**[サイン イン]** ボタンをクリックして、選択した ID プロバイダーでアプリにサイン インします。 

   	ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。

9. Windows Phone ストア アプリ プロジェクトを右クリックし、**[スタートアップ プロジェクトに設定]** をクリックしてから、Windows Phone ストア アプリも正常に実行していることを確認する前の手順を繰り返します。  <!--HONumber=42-->
