
1. Android Studio の **Project Explorer** で ToDoActivity.java ファイルを開き、次の import ステートメントを追加します。
   
        import java.util.concurrent.ExecutionException;
        import java.util.concurrent.atomic.AtomicBoolean;
   
        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;
   
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;
2. **ToDoActivity** クラスに次のメソッドを追加します。 
   
        private void authenticate() {
            // Login using the Google provider.
   
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

    これで、認証プロセスを処理する新しいメソッドが作成されます。 ユーザーは、Google ログインを使用して認証されます。 認証されたユーザーの ID を示すダイアログが表示されます。 認証が成功しないと、次に進むことはできません。

    > [AZURE.NOTE] Google 以外の ID プロバイダーを使用している場合は、上の **login** メソッドに渡す値を、_MicrosoftAccount_、_Facebook_、_Twitter_、_windowsazureactivedirectory_ のいずれかに変更します。

1. **onCreate** メソッドで、`MobileServiceClient` オブジェクトをインスタンス化するコードの後に、次のコード行を追加します。
   
        authenticate();
   
    この呼び出しで、認証プロセスが開始されます。
2. **onCreate** メソッド内の `authenticate();` の後の残りのコードを新しい **createTable** メソッドに移動します。こうすると次のようになります。
   
        private void createTable() {
   
            // Get the table instance to use.
            mToDoTable = mClient.getTable(ToDoItem.class);
   
            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
   
            // Create an adapter to bind the items with the view.
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);
   
            // Load the items from Azure.
            refreshItemsFromTable();
        }
3. **[実行]** メニューの **[アプリの実行]** をクリックしてアプリを再開し、選択した ID プロバイダーでサインインします。 
   
       When you are successfully logged-in, the app should run without errors, and you should be able to query the backend service and make updates to data.



<!--HONumber=Nov16_HO3-->


