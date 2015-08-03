
1. Eclipse の Package Explorer で ToDoActivity.java ファイルを開き、次の import ステートメントを追加します。

		import java.util.concurrent.ExecutionException;
		import java.util.concurrent.atomic.AtomicBoolean;

		import android.content.Context;
		import android.content.SharedPreferences;
		import android.content.SharedPreferences.Editor;

		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. **ToDoActivity** クラスに次のメソッドを追加します。
	
	private void authenticate() { // Google プロバイダーを使用してログイン。
	    
		ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);

    	Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
    		@Override
    		public void onFailure(Throwable exc) {
    			createAndShowDialog((Exception) exc, "Error");
    		}   		
    		@Override
    		public void onSuccess(MobileServiceUser user) {
    			createAndShowDialog(String.format(
                        "You are now logged in - %1$2s",
                        user.getUserId()), "Success");
    			createTable();	
    		}
    	});   	
	}


	これで、認証プロセスを処理する新しいメソッドが作成されます。ユーザーは、Google ログインを使用して認証されます。認証されたユーザーの ID を示すダイアログが表示されます。認証が成功しないと、次に進むことはできません。

    > [AZURE.NOTE]Google 以外の ID プロバイダーを使用している場合は、上の **login** メソッドに渡す値を_ MicrosoftAccount_、_Facebook_、_Twitter_、_windowsazureactivedirectory_ のいずれかに変更します。

3. **onCreate** メソッドで、`MobileServiceClient` オブジェクトをインスタンス化するコードの後に、次のコード行を追加します。

		authenticate();

	この呼び出しで、認証プロセスが開始されます。

4. **onCreate** メソッド内の`authenticate();` の後の残りのコードを新しい **createTable** メソッドに移動し、次のようにします。

		private void createTable() {
	
			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getTable(ToDoItem.class);
	
			mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
	
			// Create an adapter to bind the items with the view
			mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
			ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
			listViewToDo.setAdapter(mAdapter);
	
			// Load the items from the Mobile Service
			refreshItemsFromTable();
		}

9. **[実行]** メニューの **[実行]** をクリックしてアプリケーションを開始し、選択した ID プロバイダーでサインインします。

   	ログインに成功すると、アプリケーションはエラーなしで実行されます。また、Mobile Services を照会してデータを更新できるようになります。

<!---HONumber=July15_HO4-->