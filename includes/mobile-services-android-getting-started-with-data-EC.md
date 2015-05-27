モバイル サービスの準備が整ったら、ローカル コレクションの代わりにモバイル サービスに項目を格納するようにアプリケーションを更新します。

1. [モバイル サービス Android SDK] を持っていない場合は、この段階でダウンロードし、圧縮ファイルを展開してください。

2. `.jar` ファイルを SDK の `mobileservices` フォルダーから GetStartedWithData プロジェクトの `libs` フォルダーにコピーします。

3. Eclipse の Package Explorer で、`libs` フォルダーを右クリックし、**[Refresh]** をクリックします。コピーされた jar ファイルが表示されます。

  	この操作により、モバイル サービス SDK の参照がワークスペースに追加されます。

4. AndroidManifest.xml ファイルを開き、次の行を追加します。その結果、アプリケーションが Azure 上の Mobile Services にアクセスできるようになります。

		<uses-permission android:name="android.permission.INTERNET" />

5. Package Explorer で、com.example.getstartedwithdata パッケージに含まれている TodoActivity.java ファイルを開き、次のコード行をコメント解除します。

		import java.net.MalformedURLException;
		import android.os.AsyncTask;
		import com.google.common.util.concurrent.FutureCallback;
		import com.google.common.util.concurrent.Futures;
		import com.google.common.util.concurrent.ListenableFuture;
		import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
		import com.microsoft.windowsazure.mobileservices.MobileServiceList;
		import com.microsoft.windowsazure.mobileservices.http.NextServiceFilterCallback;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilter;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilterRequest;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilterResponse;
		import com.microsoft.windowsazure.mobileservices.table.MobileServiceTable;

 
6. 次の行をコメント アウトします。

		import java.util.ArrayList;
		import java.util.List;

7. 次に、現在アプリケーションで使用されているメモリ内のリストを削除して、モバイル サービスで置き換えます。**ToDoActivity** クラスで、既存の **toDoItemList** リストを定義している次のコード行をコメント アウトします。

		public List<ToDoItem> toDoItemList = new ArrayList<ToDoItem>();

8. ファイルを保存すると、プロジェクトがビルド エラーを示します。`toDoItemList` 変数が使用されている残りの 3 か所を検索し、該当するセクションをコメント解除します。これで、メモリ内のリストが完全に削除されます。

9. 次に、モバイル サービスを追加します。次のコード行をコメント解除します。

		private MobileServiceClient mClient;
		private private MobileServiceTable<ToDoItem> mToDoTable;

10. ファイルの末尾にある *ProgressFilter* クラスを探し、コメント解除します。このクラスは、*MobileServiceClient* がネットワーク操作を実行しているときに "読み込み中" インジケーターを表示します。


11. 管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

12. **[ダッシュボード]** タブをクリックし、**サイトの URL** をメモに記録します。次に、**[キーの管理]** をクリックし、**アプリケーション キー**をメモに記録します。

   	![](./media/download-android-sample-code/mobile-dashboard-tab.png)

  	これらの値は、アプリケーション コードからモバイル サービスにアクセスするときに必要になります。

13. **onCreate** メソッドで、**MobileServiceClient** 変数を定義している次のコード行をコメント解除します。

		try {
		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL and key
			mClient = new MobileServiceClient(
					"MobileServiceUrl",
					"AppKey", 
					this).withFilter(new ProgressFilter());

			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getTable(ToDoItem.class);
		} catch (MalformedURLException e) {
			createAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
		}

  	これで、モバイル サービスへのアクセスに使用される *MobileServiceClient* の新しいインスタンスが作成されます。また、モバイル サービス内のデータ ストレージをプロキシ経由で接続するために使用される *MobileServiceTable* の新しいインスタンスも作成されます。

14. 前のコードの `MobileServiceUrl` と `AppKey` を、モバイル サービスの URL とアプリケーション キーでそれぞれ置き換えます。



15. 次に示す **checkItem** メソッドの行をコメント解除します。

	    new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.update(item).get();
	                runOnUiThread(new Runnable() {
	                    public void run() {
	                        if (item.isComplete()) {
	                            mAdapter.remove(item);
	                        }
	                        refreshItemsFromTable();
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();

   	これにより、項目の更新がモバイル サービスに送信され、チェックされた項目がアダプターから削除されます。
    
16. 次に示す **addItem** メソッドの行をコメント解除します。
	
		// Insert the new item
		new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.insert(item).get();
	                if (!item.isComplete()) {
	                    runOnUiThread(new Runnable() {
	                        public void run() {
	                            mAdapter.add(item);
	                        }
	                    });
	                }
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();
		

  	このコードでは、新しい項目を作成し、それをリモート モバイル サービスのテーブルに挿入します。

18. 次に示す **refreshItemsFromTable** メソッドの行をコメント解除します。

		// Get the items that weren't marked as completed and add them in the adapter
	    new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                final MobileServiceList<ToDoItem> result = mToDoTable.where().field("complete").eq(false).execute().get();
	                runOnUiThread(new Runnable() {
	                    @Override
	                    public void run() {
	                        mAdapter.clear();

	                        for (ToDoItem item : result) {
	                            mAdapter.add(item);
	                        }
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();

	このコードでは、モバイル サービスに対するクエリを実行して、完了マークが付けられていないすべての項目を取得します。項目は、バインド用にアダプターに追加されます。
		

<!-- URLs. -->
[モバイル サービス Android SDK]: http://aka.ms/Iajk6q
<!--HONumber=54-->