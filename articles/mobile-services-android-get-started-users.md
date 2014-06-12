<properties linkid="develop-mobile-tutorials-get-started-with-users-android" urlDisplayName="認証の使用" pageTitle="認証の使用 (Android) | モバイル デベロッパー センター" metaKeywords="" description="モバイル サービスを使用して、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを介して Android アプリのユーザーを認証する方法について説明します。" metaCanonical="" services="" documentationCenter="Mobile" title="モバイル サービスでの認証の使用" authors=""  solutions="" writer="" manager="" editor=""  />





# モバイル サービスでの認証の使用
<div class="dev-center-tutorial-selector sublanding">   
	<a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet" title="Windows ストア C#">Windows ストア C##</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-js" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-wp8" title="Windows Phone">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-ios" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-android" title="Android" class="current">Android</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-html" title="HTML">HTML</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>このトピックでは、アプリケーションから Azure のモバイル サービスのユーザーを認証する方法を示します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。</p>
</div>

<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="label">チュートリアルを見る</a> <a style="background-image: url('/media/devcenter/mobile/videos/mobile-android-get-started-authentication-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Android-Getting-Started-with-Authentication-in-Windows-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">ビデオを再生する</span></a><span class="time">10:42</span></div>
</div> 

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを認証に登録し、モバイル サービスを構成する]
2. [テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3. [アプリケーションに認証を追加する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用]」を完了している必要があります。

このチュートリアルを完了するには、Eclipse および Android 4.2 以降のバージョンが必要です。

<h2><a name="register"></a><span class="short-header">アプリの登録</span>アプリを認証に登録し、モバイル サービスを構成する</h2>

ユーザーを認証できるようにするには、アプリケーションを ID プロバイダーに登録する必要があります。その後、プロバイダーによって生成されたクライアント シークレットをモバイル サービスに登録する必要があります。

1. [Azure 管理ポータル]にログオンし、**[モバイル サービス]** をクリックして、目的のモバイル サービスをクリックします。

   	![][4]

2. **[ダッシュボード]** タブをクリックし、**[サイトの URL]** の値をメモしておきます。

   	![][5]

    アプリケーションを登録するときに、この値を ID プロバイダーに指定する必要が生じる場合があります。

3. 以下の一覧から、サポートされている ID プロバイダーを選択し、手順に従ってそのプロバイダーにアプリケーションを登録します。

 - <a href="/ja-jp/develop/mobile/how-to-guides/register-for-microsoft-authentication/" target="_blank">Microsoft アカウント</a>
 - <a href="/ja-jp/develop/mobile/how-to-guides/register-for-facebook-authentication/" target="_blank">Facebook ログイン</a>
 - <a href="/ja-jp/develop/mobile/how-to-guides/register-for-twitter-authentication/" target="_blank">Twitter ログイン</a>
 - <a href="/ja-jp/develop/mobile/how-to-guides/register-for-google-authentication/" target="_blank">Google ログイン</a>
 - <a href="/ja-jp/documentation/articles/mobile-services-how-to-register-active-directory-authentication/" target="_blank">Azure Active Directory</a>


    プロバイダーによって生成されるクライアント ID およびシークレット値をメモしておいてください。

    <div class="dev-callout"><b>セキュリティに関する注意</b>
	<p>プロバイダーによって生成されるシークレットは、重要なセキュリティ資格情報です。このシークレットは、他のユーザーと共有したり、アプリケーションと共に配布したりしないでください。</p>
    </div>

4. 管理ポータルに戻って **[識別]** タブをクリックし、アプリケーションの識別子と、ID プロバイダーから取得した共有シークレット値を入力して、**[保存]** をクリックします。

   	![][13]

これで、モバイル サービスとアプリケーションの両方が、選択した認証プロバイダーと連係するように構成されました。

<h2><a name="permissions"></a><span class="short-header">アクセス許可の制限</span>アクセス許可を認証されたユーザーだけに制限する</h2>

1. 管理ポータルで、**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。

   	![][14]

2. **[アクセス許可]** タブで、すべてのアクセス許可を **[認証されたユーザーのみ]** に設定し、**[保存]** をクリックします。これにより、**TodoItem** テーブルに対するすべての操作には、認証されたユーザーが必要になります。また、次のチュートリアルのスクリプトは、匿名ユーザーの可能性を考慮する必要がなくなるため、簡素化されます。

   	![][15]

3. Eclipse で、チュートリアル「[モバイル サービスの使用]」を実行したときに作成したプロジェクトを開きます。

4. **[実行]** メニューの **[実行]** をクリックして、アプリケーションを開始します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。

	 この問題は、非認証ユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしているのに、_TodoItem_ テーブルでは認証が要求されるために発生します。

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

<h2><a name="add-authentication"></a><span class="short-header">認証の追加</span>アプリに認証を追加する</h2>

1. Eclipse の Package Explorer で ToDoActivity.java ファイルを開き、次の import ステートメントを追加します。

		import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
		import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2. 次のメソッドを **ToDoActivity** クラスに追加します。
	
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

    <div class="dev-callout"><b>メモ</b>
	<p>Google 以外の ID プロバイダーを使用している場合は、上の <strong>login</strong> メソッドに渡す値を <em>MicrosoftAccount</em>、<em>Facebook</em>、<em>Twitter</em> のいずれかに変更します。</p>
    </div>

3. **onCreate** メソッドで、`MobileServiceClient` オブジェクトをインスタンス化するコードの後に、次のコード行を追加します。

		authenticate();

	この呼び出しで、認証プロセスが開始されます。

4. **onCreate** メソッド内の `authenticate();` の後の残りのコードを新しい **createTable** メソッドに移動し、次のようにします。

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

9. **[実行]** メニューの **[実行]** をクリックしてアプリケーションを再開し、選択した ID プロバイダーでサインインします。

   	ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。

## <a name="next-steps"></a>次のステップ

[スクリプトを使用したユーザーの認証]に関する次のチュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。

<!-- Anchors. -->
[アプリケーションを認証に登録し、モバイル サービスを構成する]: #register
[テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
[アプリケーションに認証を追加する]: #add-authentication
[次のステップ]:#next-steps

<!-- Images. -->




[4]: ./media/mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-android-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-android-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->

[アプリケーションの提出に関するページ: ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Live Connect を使用した Windows ストア アプリへのシングル サインオン]: /ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-android
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-android
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-android
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-android
[スクリプトを使用したユーザーの認証]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-android

[Azure の管理ポータル]: https://manage.windowsazure.com/

