1.  Eclipse の Package Explorer で ToDoActivity.java ファイルを開き、次の import ステートメントを追加します。

        import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
        import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2.  **ToDoActivity** クラスに次のメソッドを追加します。

        private void authenticate() {

            // Login using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google,
                    new UserAuthenticationCallback() {

                        @Override
                        public void onCompleted(MobileServiceUser user,
                                Exception exception, ServiceFilterResponse response) {

                            if (exception == null) {
                                createAndShowDialog(String.format(
                                                "You are now logged in - %1$2s",
                                                user.getUserId()), "Success");
                                createTable();
                            } else {
                                createAndShowDialog("You must log in. Login Required", "Error");
                            }
                        }
                    });
        }

    これで、認証プロセスを処理する新しいメソッドが作成されます。ユーザーは、Google ログインを使用して認証されます。認証されたユーザーの ID を示すダイアログが表示されます。認証が成功しないと、次に進むことはできません。

    <div class="dev-callout"><b>注</b>
	<p>Google 以外の ID プロバイダーを使用している場合は、上の <strong>login</strong> メソッドに渡される値を<em>MicrosoftAccount</em>、<em>Facebook</em>、<em>Twitter</em>、<em>windowsazureactivedirectory</em> のいずれかにします。</p>
	</div>

3.  **onCreate** メソッドで、`MobileServiceClient` オブジェクトをインスタンス化するコードの後に、次のコード行を追加します。

        authenticate();

    この呼び出しで、認証プロセスが開始されます。

4.  **onCreate** メソッド内の `authenticate();` の後の残りのコードを新しい **createTable** メソッドに移動し、次のようにします。

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

5.  **[実行]** メニューの **[実行]** をクリックしてアプリケーションを再開し、選択した ID プロバイダーでサインインします。

    ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。


