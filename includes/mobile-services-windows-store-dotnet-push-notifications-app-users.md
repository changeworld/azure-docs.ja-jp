
次に、登録が試行される前にユーザーが認証されていることを確認するために、プッシュ通知が登録される方法を変更する必要があります。クライアント アプリケーションの更新は、プッシュ通知を実装する方法によって異なります。

###Visual Studio 2013 Update 2 以降のバージョンでプッシュ通知の追加ウィザードを使用する

このメソッドでは、ウィザードによってプロジェクトに新しい push.register.cs ファイルが作成されています。

>[AZURE.NOTE]プッシュ通知の追加ウィザードは、現在 .NET バックエンド モバイル サービスについてのみサポートされています。

1. Visual Studio のソリューション エクスプローラーで、app.xaml.cs プロジェクト ファイルを開き、**OnLaunched** イベント ハンドラーで、**UploadChannel** メソッドの呼び出しをコメント化または削除します。 

2. push.register.cs プロジェクト ファイルを開き、**UploadChannel** メソッドを次のコードと置き換えます。

		public async static void UploadChannel()
		{
		    var channel = 
		        await Windows.Networking.PushNotifications.PushNotificationChannelManager
		        .CreatePushNotificationChannelForApplicationAsync();
		
		    try
		    {
		        // Create a native push notification registration.
		        await App.MobileService.GetPush().RegisterNativeAsync(channel.Uri);		        
		
		    }
		    catch (Exception exception)
		    {
		        HandleRegisterException(exception);
		    }
		}

	これにより、認証されたユーザー資格情報を持つ同じクライアント インスタンスによって登録が確実に行われます。そうしなければ、登録は失敗し、認証エラー (401) を返します。

3. MainPage.xaml.cs プロジェクト ファイルを開き、**OnNavigatedTo** メソッドのオーバーライドを次のコードに置き換えます。

	    protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();            
            todolistPush.UploadChannel();
            RefreshTodoItems();
        }

	このコードでは、生成されたプッシュ クラス名 (`todolistPush`) をウィザードによって生成されるクラス名 (形式は通常、<code><em>mobile_service</em>Push</code>) に置き換える必要があります。

###手動で有効にされたプッシュ通知		

このメソッドでは、チュートリアルから、直接 app.xaml.cs プロジェクト ファイルに登録コードを追加しました。

1. Visual Studio のソリューション エクスプローラーで、app.xaml.cs プロジェクト ファイルを開き、**OnLaunched** イベント ハンドラーで、**InitNotificationsAsync** メソッドの呼び出しをコメント化または削除します。 
 
2. **InitNotificationsAsync** メソッドへのアクセス制限を  `private` から  `public` に変更し、 `static` 修飾子を追加します。 

3. MainPage.xaml.cs プロジェクト ファイルを開き、**OnNavigatedTo** メソッドのオーバーライドを次のコードに置き換えます。

	    protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();            
            App.InitNotificationsAsync();
            RefreshTodoItems();
        }
<!--HONumber=42-->
