<properties linkid="develop-mobile-tutorials-get-started-with-users-xamarin-android" urlDisplayName="認証の使用 (Xamarin.Android)" pageTitle="認証の使用 (Xamarin.Android) - モバイル サービス" metaKeywords="Azure アプリケーションの登録, Azure 認証, アプリケーション 認証する, モバイル サービス 認証する, モバイル サービス Xamarin.Android" description="Xamarin.Android 向け Azure モバイル サービス アプリケーションで認証を使用する方法について説明します。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="モバイル サービスでの認証の使用" documentationCenter="Mobile" authors="" />
# モバイル サービスでの認証の使用
<div class="dev-center-tutorial-selector sublanding">   
	<a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-dotnet" title="Windows ストア C#">Windows ストア C#</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-js" title="Windows ストア JavaScript">Windows ストア JavaScript</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-wp8" title="Windows Phone">Windows Phone</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-ios" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-android" title="Android">Android</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-html" title="HTML">HTML</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-android" title="Xamarin.Android" class="current">Xamarin.Android</a>
</div>

<p>このトピックでは、Xamarin.Android アプリから Azure モバイル サービスのユーザーを認証する方法について説明します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。</p>

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを認証に登録し、モバイル サービスを構成する]
2. [テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3. [アプリケーションに認証を追加する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用]」を完了している必要があります。

このチュートリアルを完了するには、[Xamarin.Android] および Android SDK 4.2 以降のバージョンが必要です。

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

4. **[Run]** メニューの **[Run]** をクリックして、アプリケーションを開始します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。

	 この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしても、_TodoItem_ テーブルでは認証が要求されるために発生します。

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

<h2><a name="add-authentication"></a><span class="short-header">認証の追加</span>アプリに認証を追加する</h2>

1. **TodoActivity** クラスに次のメソッドを追加します。

	        private async Task Authenticate()
	        {
	            try
	            {
	                user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.MicrosoftAccount);
	                CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
	            }
	            catch (Exception ex)
	            {
	                CreateAndShowDialog(ex, "Authentication failed");
	            }
	        }

    これで、認証プロセスを処理する新しいメソッドが作成されます。ユーザーは、Microsoft アカウント ログインを使用して認証されます。認証されたユーザーの ID を示すダイアログが表示されます。認証が成功しないと、次に進むことはできません。

    <div class="dev-callout"><b>注</b>
	<p>Microsoft 以外の ID プロバイダーを使用している場合は、上の <strong>login</strong> メソッドに渡される値を <i>Facebook</i>、<i>Google</i>、<i>Twitter</i> のいずれかに変更します。</p>
    </div>

2. **OnCreate** メソッドで、`MobileServiceClient` オブジェクトをインスタンス化するコードの後に、次のコード行を追加します。

		await Authenticate();

	この呼び出しは、認証プロセスを開始し、認証プロセスを非同期に待機します。

3. **OnCreate** メソッド内の `await Authenticate();` の後にある残りのコードを、新しい **CreateTable** メソッドに移動します。次のようになります。

	        private async Task CreateTable()
	        {
	            // Get the Mobile Service Table instance to use
	            todoTable = client.GetTable<TodoItem>();

	            textNewTodo = FindViewById<EditText>(Resource.Id.textNewTodo);

	            // Create an adapter to bind the items with the view
	            adapter = new TodoItemAdapter(this, Resource.Layout.Row_List_To_Do);
	            var listViewTodo = FindViewById<ListView>(Resource.Id.listViewTodo);
	            listViewTodo.Adapter = adapter;

	            // Load the items from the Mobile Service
	            await RefreshItemsFromTableAsync();
	        }

4. 次に、手順 2. で追加した **Authenticate** 呼び出しの後の **OnCreate** 内で新しい **CreateTable** メソッドを呼び出します。

		await CreateTable();


5. **[Run]** メニューの **[Run]** をクリックしてアプリケーションを再開し、選択した ID プロバイダーでサインインします。

   	ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。

##完成したサンプルの入手
[完成したサンプル プロジェクト]をダウンロードします。**applicationURL** 変数と **applicationKey** 変数を独自の Azure 設定で更新してください。

## <a name="next-steps"></a>次のステップ

[スクリプトを使用したユーザーの認証]に関する次のチュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。

<!-- Anchors. -->
[アプリケーションを認証に登録し、モバイル サービスを構成する]: #register
[テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
[アプリケーションに認証を追加する]: #add-authentication
[次のステップ]:#next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-service-uri.png

[13]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->

[アプリケーションの提出に関するページ: ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[モバイル サービスの使用]: /ja-jp/develop/mobile/tutorials/get-started-xamarin-android
[データの使用]: /ja-jp/develop/mobile/tutorials/get-started-with-data-xamarin-android
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-xamarin-android
[プッシュ通知の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-push-xamarin-android
[スクリプトを使用したユーザーの承認]: /ja-jp/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android

[Azure 管理ポータル]: https://manage.windowsazure.com/

[完成したサンプル プロジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=331328

