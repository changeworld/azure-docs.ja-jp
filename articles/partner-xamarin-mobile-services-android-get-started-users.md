<properties urlDisplayName="Get Started with Authentication (Xamarin.Android)" pageTitle="認証の使用 (Xamarin.Android) - Mobile Services の使用" metaKeywords="アプリケーションを登録する Azure, Azure 認証, アプリケーション認証, モバイル サービスの認証, Mobile Services Xamarin.Android" description="Xamarin.Android 向け Azure Mobile Services アプリケーションで認証を使用する方法について説明します。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" title="Get started with authentication in Mobile Services" services="mobile-services" documentationCenter="Mobile" manager="dwrede" authors="donnam"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam" />

# Mobile Services アプリへの認証の追加

[WACOM.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

<p>このトピックでは、Xamarin.Android アプリから Azure モバイル サービスのユーザーを認証する方法について説明します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。</p>

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを認証に登録し、モバイル サービスを構成する]
2. [テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3. [アプリケーションに認証を追加する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用]」を完了している必要があります。 

このチュートリアルを完了するには、Xamarin.Android および Android SDK 4.2 以降のバージョンが必要です。 

<h2><a name="register"></a>アプリケーションを認証に登録し、モバイル サービスを構成する</h2>

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<h2><a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する</h2>


[WACOM.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 


3. Eclipse で、チュートリアル「[モバイル サービスの使用]」を実行したときに作成したプロジェクトを開きます。 

4. **[実行]** メニューの **[実行]** をクリックして、アプリケーションを開始します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。 

	 この問題は、非認証ユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしているのに、_TodoItem_ テーブルでは認証が要求されるために発生します。

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

<h2><a name="add-authentication"></a>アプリケーションに認証を追加する</h2>

1. **ToDoActivity** クラスに次のメソッドを追加します。

			private MobileServiceUser user;

2. **TodoActivity** クラスに次のメソッドを追加します。 

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
	<p>Microsoft 以外の ID プロバイダーを使用している場合は、上の <strong>login</strong> メソッドに渡される値を、<i>Facebook</i>、<i>Google</i>、<i>Twitter</i>、<i>WindowsAzureActiveDirectory</i>.</p> のいずれかにします。
    </div>

3. **OnCreate** メソッドで、`MobileServiceClient` オブジェクトをインスタンス化するコードの後に、次のコード行を追加します。

		await Authenticate();

	この呼び出しは、認証プロセスを開始し、認証プロセスを非同期に待機します。

4. **OnCreate** メソッド内の `await Authenticate();` の後にある残りのコードを、新しい **CreateTable** メソッドに移動します。次のようになります。

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

5. 次に、手順 2. で追加した **Authenticate** 呼び出しの後の **OnCreate** 内で新しい **CreateTable** メソッドを呼び出します。

		await CreateTable();


6. **[実行]** メニューの **[実行]** をクリックしてアプリケーションを開始し、選択した ID プロバイダーでサインインします。 

   	ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。

## 完成したサンプルの入手
[完成したサンプル プロジェクト]をダウンロードします。**applicationURL** 変数と **applicationKey** 変数を独自の Azure 設定で更新してください。

## <a name="next-steps"></a>次のステップ

次の[スクリプトを使用したユーザーの認証に関するチュートリアル]では、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。 

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
