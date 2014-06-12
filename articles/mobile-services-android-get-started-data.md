<properties linkid="develop-mobile-tutorials-get-started-with-data-android" urlDisplayName="データの使用 - Android" pageTitle="データの使用 (Android) | モバイル デベロッパー センター" metaKeywords="Azure android データ, Azure モバイル サービス データ, windows droid, windows android, microsoft droid, microsoft android" description="モバイル サービスを使用して Android アプリのデータを活用する方法について説明します。" metaCanonical="" services="" documentationCenter="Mobile" title="モバイル サービスでのデータの使用" authors="ricksal,glenga" solutions="" manager="" editor="" />



# モバイル サービスでのデータの使用
<div class="dev-center-tutorial-selector sublanding">    
	<a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-dotnet" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-js" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-wp8" title="Windows Phone">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-ios" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-android" title="Android" class="current">Android</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-html" title="HTML">HTML</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a> 
</div>	


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>このトピックでは、Azure のモバイル サービスを使用して Android アプリのデータを活用する方法について説明します。このチュートリアルでは、メモリにデータを格納するアプリケーションをダウンロードした後、新しいモバイル サービスを作成してそれをアプリケーションに統合します。その後、Azure 管理ポータルにログインして、アプリケーションの実行中にデータに加えられた変更を表示します。</p>

</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-data-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-With-Data-Connecting-your-app-to-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a><span class="time">15:32</span></div>
</div>

<div class="dev-callout"><b>メモ</b>
<p>このチュートリアルの目的は、モバイル サービスが Android アプリのデータを Azure に格納および取得できるようにするしくみを説明することにあります。したがって、このトピックでは、モバイル サービスのクイック スタートで完了している手順の多くについても説明します。モバイル サービスを初めて使用する場合は、最初にチュートリアル「<a href="/ja-jp/develop/mobile/tutorials/get-started-android">モバイル サービスの使用</a>」を完了することをお勧めします。</p>
</div>

このチュートリアルでは、次の基本的な手順について説明します。

1. [Android アプリ プロジェクトのダウンロード]
2. [モバイル サービスの作成]
3. [ストレージのデータ テーブルの追加]
4. [モバイル サービスを使用するためのアプリケーションの更新]
5. [モバイル サービスに対するアプリケーションのテスト]

<div class="dev-callout"><strong>注</strong><p> このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料の試用アカウントを数分で作成することができます。詳細については、<a href="http://www.windowsazure.com/ja-jp/pricing/free-trial/?WT.mc_id=AED8DE357" target="_blank">Azure の無料評価版のサイト</a>を参照してください。</p></div>

このチュートリアルを実行するには、[モバイル サービス Android SDK]、Eclipse 統合開発環境 (IDE) と Android Developer Tools (ADT) プラグインを含む <a href="https://go.microsoft.com/fwLink/p/?LinkID=280125" target="_blank">Android SDK</a>、および Android 4.2 以降のバージョンが必要です。

<div class="dev-callout"><b>メモ</b>
<p>このチュートリアルでは、Android SDK とモバイル サービス Android SDK の両方をインストールする手順について説明します。ダウンロードした GetStartedWithData プロジェクトを実行するには、Android 4.2 以降のバージョンが必要です。ただし、モバイル サービス SDK は、Android 2.2 以降で動作します。</p>
</div>



<h2><a name="download-app"></a><span class="short-header">プロジェクトのダウンロード</span>GetStartedWithData プロジェクトのダウンロード</h2>

###サンプル コードの取得

このチュートリアルは、Android アプリの [GetStartedWithData アプリ][GitHub]に基づいています。このアプリケーションの UI は、追加された項目がメモリにローカルに格納される点を除き、モバイル サービス Android のクイック スタートで生成したアプリケーションと同じです。ストレージにデータを保存するために必要なコードを追加します。


1. `GetStartedWithData` サンプル アプリをダウンロードし、コンピューター上でファイルを展開します。

2. Eclipse で、**[File]**、**[Import]** の順にクリックし、**[Android]** を展開します。**[Existing Android Code into Workspace]** をクリックし、**[Next]** をクリックします。

 	![][14]

3. **[Browse]** をクリックします。展開したプロジェクト ファイルの場所を参照し、**[OK]** をクリックします。TodoActivity プロジェクトのチェック ボックスがオンになっていることを確認します。プロジェクトを自分のワークスペースにコピーする場合は、**[Copy projects into workspace]** チェック ボックスをオンにします。最後に、**[Finish]** をクリックします。

 	![][15]

	これにより、プロジェクト ファイルが現在のワークスペースにインポートされます。

###Android SDK バージョンの検証

[WACOM.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version.md)]


###サンプル コードを確認して実行します。

1. Package Explorer で、**[GetStartedWithData]**、**[src]**、および **[.com.example.GetStartedWithData]** を展開し、ToDoActivity.java ファイルを確認します。

   	![][12]

   	`//TODO` コメントに、このアプリケーションをモバイル サービスで実行するために必要な手順が指定されている点に注目してください。

6. **[Run]** メニューの **[Run]** をクリックし、**[Android Application]** をクリックしてプロジェクトを開始します。

	<div class="dev-callout"><strong>メモ</strong> <p>このプロジェクトは、Android フォンまたは Android エミュレーターを使用して実行できます。Android フォンで実行するには、機器に固有の USB ドライバーをダウンロードする必要があります。</p><p>プロジェクトを Android エミュレーターで実行するには、Android Virtual Device (AVD) を 1 つ以上定義する必要があります。これらのデバイスを作成および管理するには、AVD Manager を使用します。</p></div>

7. アプリケーションで、わかりやすいテキスト (たとえば、「_チュートリアルの完了_」) を入力し、**[Add]** をクリックします。

   	![][13]

   	保存したテキストがメモリ内のコレクションに格納され、下のリストに表示されます。

<h2><a name="create-service"></a><span class="short-header">モバイル サービスの作成</span>管理ポータルでの新しいモバイル サービスの作成</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a><span class="short-header">新しいテーブルの追加</span>モバイル サービスへの新しいテーブルの追加</h2>

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

<h2><a name="update-app"></a><span class="short-header">アプリケーションの更新</span>モバイル サービスをデータ アクセスに使用するためのアプリケーションの更新</h2>

モバイル サービスの準備が整ったら、ローカル コレクションの代わりにモバイル サービスに項目を格納するようにアプリケーションを更新します。

1. [モバイル サービス Android SDK] を持っていない場合は、この段階でダウンロードし、圧縮ファイルを展開してください。

2. SDK の `mobileservices` フォルダーに含まれている `.jar` ファイルを GetStartedWithData プロジェクトの `libs` フォルダーにコピーします。

3. Eclipse の Package Explorer で、`libs` フォルダーを右クリックし、**[Refresh]** をクリックします。すると、コピーした jar ファイルが表示されます。

  	この操作により、モバイル サービス SDK の参照がワークスペースに追加されます。

4. AndroidManifest.xml ファイルを開き、次の行を追加します。その結果、アプリケーションが Azure 上のモバイル サービスにアクセスできるようになります。

		<uses-permission android:name="android.permission.INTERNET" />

5. Package Explorer で、com.example.getstartedwithdata パッケージに含まれている TodoActivity.java ファイルを開き、次のコード行をコメント解除します。

		import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
		import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
		import com.microsoft.windowsazure.mobileservices.NextServiceFilterCallback;
		import com.microsoft.windowsazure.mobileservices.ServiceFilter;
		import com.microsoft.windowsazure.mobileservices.ServiceFilterRequest;
		import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
		import com.microsoft.windowsazure.mobileservices.ServiceFilterResponseCallback;
		import com.microsoft.windowsazure.mobileservices.TableOperationCallback;
		import com.microsoft.windowsazure.mobileservices.TableQueryCallback;

		import java.net.MalformedURLException;
 
6. 次に、現在アプリケーションで使用されているメモリ内のリストを削除して、モバイル サービスで置き換えます。**ToDoActivity** クラスで、既存の **toDoItemList** リストを定義している次のコード行をコメント アウトします。

		public List<ToDoItem> toDoItemList = new ArrayList<ToDoItem>();

7. 前の手順を完了すると、プロジェクトのビルド エラーが示されます。`toDoItemList` 変数が使用されている残りの 3 か所を検索し、該当するセクションをコメント アウトします。さらに、`import java.util.ArrayList` を削除します。これで、メモリ内のリストが完全に削除されます。

8. 次に、モバイル サービスを追加します。次のコード行をコメント解除します。

		private MobileServiceClient mClient;
		private private MobileServiceTable<ToDoItem> mToDoTable;

9. ファイルの末尾にある ProgressFilter クラスを探し、コメント解除します。このクラスは、MobileServiceClient がネットワーク操作を実行しているときに "読み込み中" インジケーターを表示します。


10. 管理ポータルで、**[モバイル サービス]** をクリックし、先ほど作成したモバイル サービスをクリックします。

11. **[ダッシュボード]** タブをクリックし、**サイトの URL** をメモに記録します。次に、**[キーの管理]** をクリックし、**アプリケーション キー**をメモに記録します。

   	![][8]

  	これらの値は、アプリケーション コードからモバイル サービスにアクセスするときに必要になります。

12. **onCreate** メソッドで、**MobileServiceClient** 変数を定義している次のコード行をコメント解除します。

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

  	これで、モバイル サービスにアクセスするために使用される MobileServiceClient の新しいインスタンスが作成されます。また、モバイル サービス内のデータ ストレージをプロキシ経由で接続するために使用される MobileServiceTable の新しいインスタンスも作成されます。

13. 上記のコードの `MobileServiceUrl` と `AppKey` を、モバイル サービスの URL とアプリケーション キーでそれぞれ置き換えます。



14. 次に示す **checkItem** メソッドの行をコメント解除します。

		mToDoTable.update(item, new TableOperationCallback<ToDoItem>() {	
			public void onCompleted(ToDoItem entity, Exception exception,
					ServiceFilterResponse response) {
				if(exception == null){
					if (entity.isComplete()) {
						mAdapter.remove(entity);
					}
				} else {
					createAndShowDialog(exception, "Error");	
				}
			}
		});

   	これにより、項目の更新がモバイル サービスに送信され、チェックされた項目がアダプターから削除されます。
    
15. 次に示す **addItem** メソッドの行をコメント解除します。
	
		mToDoTable.insert(item, new TableOperationCallback<ToDoItem>() {
			
			public void onCompleted(ToDoItem entity, Exception exception,
					ServiceFilterResponse response) {
				if(exception == null){
					if (!entity.isComplete()) {
						mAdapter.add(entity);
					}
				} else {
					createAndShowDialog(exception, "Error");
				}				
			}
		});
		

  	このコードでは、新しい項目を作成し、それをリモート モバイル サービスのテーブルに挿入します。

16. 次に示す **refreshItemsFromTable** メソッドの行をコメント解除します。

		mToDoTable.where().field("complete").eq(false)
		.execute(new TableQueryCallback<ToDoItem>() {
		     public void onCompleted(List<ToDoItem> result, 
		    		 int count, Exception exception, 
		    		 ServiceFilterResponse response) {
						
						if(exception == null){
							mAdapter.clear();
	
							for (ToDoItem item : result) {
								mAdapter.add(item);
							}
						} else {
							createAndShowDialog(exception, "Error");
						}
					}
				}); 

	このコードでは、モバイル サービスに対するクエリを実行して、完了マークが付けられていないすべての項目を取得します。項目は、バインド用にアダプターに追加されます。
		


<h2><a name="test-app"></a><span class="short-header">アプリケーションのテスト</span>新しいモバイル サービスに対するアプリケーションのテスト</h2>

バック エンド ストレージのモバイル サービスを使用するようにアプリケーションを更新した後は、Android エミュレーターまたは Android フォンを使用して、モバイル サービスに対してアプリケーションをテストします。

1. **[Run]** メニューの **[Run]** をクリックして、プロジェクトを開始します。

	これにより、モバイル サービスから項目を取得するクエリを、クライアント ライブラリを使用して送信するアプリケーションが Android SDK でビルドされ、実行されます。

5. 前回と同様に、わかりやすいテキストを入力し、**[Add]** をクリックします。

   	これにより、新しい項目が挿入としてモバイル サービスに送信されます。

3. [管理ポータル]で、**[モバイル サービス]** をクリックし、対象のモバイル サービスをクリックします。

4. **[データ]** タブをクリックし、**[参照]** をクリックします。

   	![][9]
  
   	**TodoItem** テーブルに、モバイル サービスによって生成されたいくつかの値を持つデータが含まれ、アプリケーションの TodoItem クラスに対応してその列が自動的にテーブルに追加されていることに注目してください。

これで、Android 向けの**データの使用**に関するチュートリアルは終了です。

## <a name="next-steps"> </a>次のステップ

このチュートリアルでは、Android アプリでモバイル サービスのデータを操作できるようにするための基本について説明しました。

次は、このチュートリアルで作成した GetStartedWithData アプリケーションに基づく次のいずれかのチュートリアルを行うことをお勧めします。

* [サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]
  <br/>モバイル サービスでサーバー スクリプトを使用して、アプリケーションから送信されたデータを検証および変更する方法について説明します。

* [ページングを使用したモバイル サービス クエリの改善]
  <br/>クエリ内でページングを使用して、単一の要求で渡されるデータの量を制御する方法について説明します。

データ シリーズを完了した後は、次に示す、Android に関する他のチュートリアルを行うことをお勧めします。

* [認証の使用]
	<br/>アプリケーションのユーザーを認証する方法について説明します。

* [プッシュ通知の使用]
  <br/>モバイル サービスを使用してアプリケーションにごく基本的なプッシュ通知を送信する方法について説明します。

<!-- Anchors. -->
[Android アプリ プロジェクトのダウンロード]: #download-app
[モバイル サービスの作成]: #create-service
[ストレージのデータ テーブルの追加]: #add-table
[モバイル サービスを使用するためのアプリケーションの更新]: #update-app
[モバイル サービスに対するアプリケーションのテスト]: #test-app
[次のステップ]:#next-steps

<!-- Images. -->
[8]: ./media/mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/mobile-services-android-get-started-data/mobile-todoitem-data-browse.png


[12]: ./media/mobile-services-android-get-started-data/mobile-eclipse-project.png
[13]: ./media/mobile-services-android-get-started-data/mobile-quickstart-startup-android.png
[14]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-workspace.png
[15]: ./media/mobile-services-android-get-started-data/mobile-services-import-android-project.png


<!-- URLs. -->
[サーバー スクリプトを使用したモバイル サービスのデータの検証および変更]: /ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[ページングを使用したモバイル サービス クエリの改善]: /ja-jp/develop/mobile/tutorials/add-paging-to-data-android
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-android
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-android
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-android
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-android

[Azure 管理ポータル]: https://manage.windowsazure.com/
[管理ポータル]: https://manage.windowsazure.com/
[モバイル サービス Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
[GitHub]:  http://go.microsoft.com/fwlink/p/?LinkID=282122
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

