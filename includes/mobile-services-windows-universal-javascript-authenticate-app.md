

1. default.js プロジェクト ファイルを開き、**app.OnActivated** メソッド オーバーロード内で、**refreshTodoItems** メソッドの最後の呼び出しを次のコードで置き換えます。 

        // Define a member variable for storing the signed-in user.
        var userId = null;

        // Request authentication from Mobile Services using a Facebook login.
        var authenticate = function () {
            return new WinJS.Promise(function (complete) {
                // Change 'client' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                client.login("facebook").done(function (results) {
                    userId = results.userId;
                    refreshTodoItems();
                    buttonLogin.disabled = true;
                    var message = "You are now signed in as: " + userId;
                    var dialog = new Windows.UI.Popups.MessageDialog(message);
                    dialog.showAsync().done(complete);
                }, function (error) {
                    userId = null;
                    var dialog = new Windows.UI.Popups
                    .MessageDialog(error);
                        //.MessageDialog("An error occurred during login", "Login Required");
                    dialog.showAsync().done(complete);
                });
            });
        };

        // Handle the sign in button click event.
        buttonLogin.addEventListener("click", function () {
            authenticate();
        });

	Facebook 以外の ID プロバイダーを使用している場合は、上の <strong>login</strong> メソッドに渡す値を _microsoftAccount_、_twitter_、_google_、_windowsazureactivedirectory_ のいずれかに変更します。

    >[AZURE.NOTE]Windows ストア アプリ パッケージ情報を Mobile Services に登録している場合は、<em>useSingleSignOn</em> パラメーターに値 <strong>true</strong> を指定して <a href="http://go.microsoft.com/fwlink/p/?LinkId=322050" target="_blank">login</a> メソッドを呼び出す必要があります。この操作を行わない場合、login メソッドが呼び出されるたびに、ユーザーにログイン プロンプトが表示されます。

2. Windows ストア アプリ プロジェクトで、default.html プロジェクト ファイルを開き、**[保存]** ボタンを定義する要素の直前に次の **Button** 要素を追加します。

      	<button id="buttonLogin" style="margin-left: 5px">Sign in</button>

3. F5 キーを押してアプリケーションを実行し、選択した ID プロバイダーでアプリケーションにサインインします。

   	ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。

<!---HONumber=August15_HO6-->