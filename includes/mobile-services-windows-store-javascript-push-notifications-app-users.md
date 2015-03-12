
次に、登録が試行される前にユーザーが認証されていることを確認するために、プッシュ通知が登録される方法を変更する必要があります。クライアント アプリケーションの更新は、プッシュ通知を実装する方法によって異なります。

###Visual Studio 2013 Update 2 以降のバージョンでプッシュ通知ウィザードを使用する

このメソッドでは、ウィザードによってプロジェクトに新しい push.register.js ファイルと service.js ファイルが作成されています。

>[AZURE.NOTE]プッシュ通知の追加ウィザードは、現在 .NET バックエンド モバイル サービスについてのみサポートされています。

1. Visual Studio のソリューション エクスプローラーで、push.register.js プロジェクト ファイルを開き、**addEventListener** の呼び出しをコメント化または削除します。 

2. default.js プロジェクト ファイルで、既存の **login** 関数を次のコードに置き換えます。
 
		// Request authentication from Mobile Services using a Facebook login.
		var login = function () {
		    return new WinJS.Promise(function (complete) {
		        client.login("facebook").done(function (results) {
		            userId = results.userId;
		            // Request a push notification channel.
		            Windows.Networking.PushNotifications
		                .PushNotificationChannelManager
		                .createPushNotificationChannelForApplicationAsync()
		                .then(function (channel) {
		                    // Register for notifications using the new channel
		                    client.push.registerNative(channel.uri);
		                });
		            refreshTodoItems();
		            var message = "You are now logged in as: " + userId;
		            var dialog = new Windows.UI.Popups.MessageDialog(message);
		            dialog.showAsync().done(complete);
		        }, function (error) {
		            userId = null;
		            var dialog = new Windows.UI.Popups
		                .MessageDialog("An error occurred during login", "Login Required");
		            dialog.showAsync().done(complete);
		        });
		    });
		}  

###手動で有効にされたプッシュ通知		

このメソッドでは、チュートリアルから、直接 default.js プロジェクト ファイルに登録コードを追加しました。

1. Visual Studio のソリューション エクスプローラーで、default.js プロジェクト ファイルを開き、**onActivated** イベント ハンドラー内で、次に示すように **createPushNotificationChannelForApplicationAsync** 関数を呼び出すコード行を特定します。

		// Request a push notification channel.
		Windows.Networking.PushNotifications
		    .PushNotificationChannelManager
		    .createPushNotificationChannelForApplicationAsync()
		    .then(function (channel) {
		        // Register for notifications using the new channel
		        client.push.registerNative(channel.uri);
		    }); 
 
2. このコード行を、**refreshTodoItems** の呼び出しの直前にある **login** 関数内に移動します。結果、**login** 関数は次のようになります。
 
		// Request authentication from Mobile Services using a Facebook login.
		var login = function () {
		    return new WinJS.Promise(function (complete) {
		        client.login("facebook").done(function (results) {
		            userId = results.userId;
		            // Request a push notification channel.
		            Windows.Networking.PushNotifications
		                .PushNotificationChannelManager
		                .createPushNotificationChannelForApplicationAsync()
		                .then(function (channel) {
		                    // Register for notifications using the new channel
		                    client.push.registerNative(channel.uri);
		                });
		            refreshTodoItems();
		            var message = "You are now logged in as: " + userId;
		            var dialog = new Windows.UI.Popups.MessageDialog(message);
		            dialog.showAsync().done(complete);
		        }, function (error) {
		            userId = null;
		            var dialog = new Windows.UI.Popups
		                .MessageDialog("An error occurred during login", "Login Required");
		            dialog.showAsync().done(complete);
		        });
		    });
		}  
<!--HONumber=42-->
