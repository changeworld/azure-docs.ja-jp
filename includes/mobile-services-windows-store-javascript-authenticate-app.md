

1. default.js プロジェクト ファイルを開き、**app.OnActivated** メソッドのオーバーロード内で、**refreshTodoItems** メソッドの最後の呼び出しを次のコードで置き換えます。 
	
        var userId = null;

        // Request authentication from Mobile Services using a Facebook login.
        var login = function () {
            return new WinJS.Promise(function (complete) {
                client.login("facebook").done(function (results) {
                    userId = results.userId;
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

        var authenticate = function () {
            login().then(function () {
                if (userId === null) {

                    // Authentication failed, try again.
                    authenticate();
                }
            });
        }

        authenticate();

    これにより、現在のユーザーを格納するためのメンバー変数と認証プロセスを処理するためのメソッドが作成されます。ユーザーは、Facebook ログインを使用して認証されます。Facebook 以外の ID プロバイダーを使用している場合は、上の <strong>login</strong> メソッドに渡される値を_microsoftaccount_、_twitter_、_google_、_windowsazureactivedirectory_ のいずれかに変更します。

    >[AZURE.NOTE]Windows ストア アプリ パッケージ情報をモバイル サービスに登録している場合は、<em>useSingleSignOn</em> パラメーターに値 <strong>true</strong> を指定して <a href="http://go.microsoft.com/fwlink/p/?LinkId=322050" target="_blank">login</a> メソッドを呼び出す必要があります。この操作を行わない場合、login メソッドが呼び出されるたびに、ユーザーにログイン プロンプトが表示されます。

2. F5 キーを押してアプリケーションを実行し、選択した ID プロバイダーでアプリケーションにサインインします。 

   	ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。
<!--HONumber=42-->
