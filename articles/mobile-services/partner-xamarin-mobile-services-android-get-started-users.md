<properties 
	pageTitle="認証の使用 (Xamarin.Android) - Mobile Services" 
	description="Xamarin.Android 向け Azure Mobile Services アプリケーションで認証を使用する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="xamarin" 
	manager="dwrede" 
	authors="lindydonna" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/01/2015" 
	ms.author="donnam"/>

# Mobile Services アプリへの認証の追加

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

<p>このトピックでは、Xamarin.Android アプリから Azure モバイル サービスのユーザーを認証する方法について説明します。このチュートリアルでは、モバイル サービスでサポートされている ID プロバイダーを使用して、クイック スタート プロジェクトに認証を追加します。モバイル サービスによって正常に認証および承認されると、ユーザー ID 値が表示されます。</p>

このチュートリアルでは、アプリケーションでの認証を有効にするための、次の基本的な手順について説明します。

1. [アプリケーションを認証に登録し、Mobile Services を構成する]
2. [テーブルのアクセス許可を、認証されたユーザーだけに制限する]
3. [アプリケーションに認証を追加する]

このチュートリアルは、モバイル サービスのクイック スタートに基づいています。先にチュートリアル「[モバイル サービスの使用]」を完了している必要があります。

このチュートリアルを完了するには、Xamarin.Android および Android SDK 4.2 以降のバージョンが必要です。

##<a name="register"></a>アプリケーションを認証に登録し、Mobile Services を構成する

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)] 

##<a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する


[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)] 


3. Eclipse で、チュートリアル「[モバイル サービスの使用]」を実行したときに作成したプロジェクトを開きます。 

4. **[実行]** メニューの **[実行]** をクリックして、アプリケーションを開始します。アプリケーションの開始後に、状態コード 401 (許可されていません) のハンドルされない例外が発生することを確認します。

	 この問題は、認証されないユーザーとしてアプリケーションがモバイル サービスにアクセスしようとしても、_TodoItem_ テーブルでは認証が要求されるために発生します。

次に、モバイル サービスのリソースを要求する前にユーザーを認証するようにアプリケーションを更新します。

##<a name="add-authentication"></a>アプリケーションに認証を追加する

1. **TodoActivity** クラスに次のプロパティを追加します。

		private MobileServiceUser user;

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

    > [AZURE.NOTE]Microsoft 以外の ID プロバイダーを使用している場合は、上の **login** メソッドに渡す値を、_Facebook_、_Google_、_Twitter_、_WindowsAzureActiveDirectory_ のいずれかに変更します。

3. **onCreate** メソッドで、`MobileServiceClient` オブジェクトをインスタンス化するコードの後に、次のコード行を追加します。

		await Authenticate();

	この呼び出しは、認証プロセスを開始し、認証プロセスを非同期に待機します。

4. **OnCreate** メソッド内の `await Authenticate();` の後の残りのコードを新しい **CreateTable** メソッドに移動します。こうすると次のようになります。

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


6. **[実行]** メニューの **[実行]** をクリックしてアプリケーションを再開し、選択した ID プロバイダーでサインインします。

   	ログインに成功すると、アプリケーションはエラーなしで実行されます。また、モバイル サービスを照会してデータを更新できるようになります。

## 完成したサンプルの入手
[完成したサンプル プロジェクト]をダウンロードします。**applicationURL** 変数と **applicationKey** 変数を独自の Azure 設定で更新してください。

## <a name="next-steps"></a>次のステップ

[スクリプトを使用したユーザーの認証]に関する次のチュートリアルでは、認証されたユーザーに基づいてモバイル サービスによって提供されるユーザー ID 値を受け取り、それを使用して、モバイル サービスから返されたデータをフィルター処理します。

<!-- Anchors. -->
[アプリケーションを認証に登録し、Mobile Services を構成する]: #register
[テーブルのアクセス許可を、認証されたユーザーだけに制限する]: #permissions
[アプリケーションに認証を追加する]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->
[4]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-services-selection.png
[5]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-service-uri.png

[13]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-identity-tab.png
[14]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-data-tables.png
[15]: ./media/partner-xamarin-mobile-services-android-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[スクリプトを使用したユーザーの認証]: mobile-services-javascript-backend-service-side-authorization.md
[Azure Management Portal]: https://manage.windowsazure.com/
[完成したサンプル プロジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=331328

<!--HONumber=54--> 